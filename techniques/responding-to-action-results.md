# Responding to action results

In writing a Mechanic task, it may be necessary to do more than a single round of work. The task may need to act based on the content of a previous action, possibly rendering additional actions to follow.

To achieve enhanced flows of this sort, subscribe to the **mechanic/actions/perform** event topic. When a task includes this subscription, Mechanic will generate an event with that topic for every action that the task completes. This event will only ever be responded to by the task that generated it; it will never be sent to other tasks.

{% hint style="info" %}
The mechanic/actions/perform subscription is commonly used with the [Shopify action](../core/actions/integrations/shopify.md) (with GraphQL mutations) and the [HTTP action](../core/actions/http.md) (for reading data from third-party APIs – see [Working with external APIs](working-with-external-apis.md)).

The [Echo action](../core/actions/echo.md) does not generate mechanic/actions/perform events.
{% endhint %}

## Inspecting the context

Events with the topic mechanic/actions/perform are, by nature, always [child events](../core/events/parent-and-child-events.md). As such, their `event` variable contains a reference to the event's parent. This means a task may use `event.parent.data` to access all the data that informed the construction of the action in question. (Note that only the previous 5 generations of parents are available.)

A mechanic/actions/perform event also provides task runs with an `action` variable (containing the same information available in `event.data`). This variable contains the original [action object](../core/tasks/code/action-objects.md) (including its type, options, and meta information), and data from the run in which the action was performed.

Common sources of information for followup task runs:

* `action.options` — the original options defined for the action
* `action.run.ok` — `true` or `false`, indicating the action's success
* `action.run.result` — the value (if any) generated by the action run
* `action.run.error` — the error (if any) raised during the action run
* `action.meta` — the [meta information](../core/tasks/code/action-objects.md#meta) given in the action's definition

{% hint style="warning" %}
It's important to specifically account for the mechanic/actions/perform topic when writing a task script, minding the fact that improper composition could result in an infinite loop.

Mechanic will step in and forcibly fail subsequent task runs that contain results identical to their predecessors.
{% endhint %}

## Example

This example cycles between three different modes of operation, depending on the [meta information](../core/tasks/code/action-objects.md#meta) recorded in the action definition. And, by checking on `action.run.ok` at the very beginning, the task is also able to "break" into failure-handling mode, in which an email is sent to an administrator.

{% tabs %}
{% tab title="Subscriptions" %}
```
mechanic/user/trigger
mechanic/actions/perform
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="Code" %}
```javascript
{% raw %}
{% if action.run.ok == false %}
  {% action "email" %}
    {
      "to": "admin@example.com",
      "subject": "Action failure",
      "body": {{ "Alert!<br><br>" | append: action.run.error | json }}
    }
  {% endaction %}
{% elsif event.topic contains "trigger" %}
  {% action %}
    {
      "type": "cache",
      "options": ["set", "foo", "bar"],
      "meta": {
        "mode": "first"
      }
    }
  {% endaction %}
{% elsif action.meta.mode == "first" %}
  {% capture mutation %}
    mutation {
      tagsAdd(id: "gid://shopify/Customer/1234567890", tags: ["processed"]) {
        userErrors { field, message }
      }
    }
  {% endcapture %}

  {% action %}
    {
      "type": "shopify",
      "options": {{ mutation | json }},
      "meta": {
        "mode": "second"
      }
    }
  {% endaction %}
{% elsif action.meta.mode == "second" %}
  {% action "echo", "done" %}
{% endif %}
{% endraw %}
```
{% endtab %}
{% endtabs %}
