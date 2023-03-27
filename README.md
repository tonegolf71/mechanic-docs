---
description: I'm glad you're here. :) –Isaac
---

# Introduction

Mechanic is a Shopify development and automation platform. Get started by...

* ... [looking in the task library](resources/task-library/) for an off-the-shelf solution
* ... [learning about "going custom"](custom.md) for something that fits you perfectly
* ... [asking in our community Slack workspace](resources/slack.md) if you need something else

{% hint style="info" %}
Find Mechanic on the Shopify app store: [apps.shopify.com/mechanic](https://apps.shopify.com/mechanic)
{% endhint %}

## Can Mechanic help me?

Maybe! Mechanic is a Shopify development and automation platform, which comes with a rich library of pre-written automation tasks – and our users write their own custom tasks every day. Let's find out if Mechanic might work for you.

1. Are you working on something Shopify-related? (Mechanic is only available for Shopify.)
2. Is what you're looking for already available from [Mechanic's task library](https://tasks.mechanic.dev/)? (We have hundreds of common scenarios already handled with pre-written, open-source, modifiable, off-the-shelf tasks.)
3. As far as your problem concerns Shopify data, is what you want to do supported by the [Shopify Admin API](https://shopify.dev/docs/api/admin-graphql)? (Mechanic's toolkit goes beyond Shopify's APIs, but a Mechanic task can only interact with Shopify data in ways that Shopify supports.)
4. Are you open to [going custom](custom.md)? (Whether you need a developer or already have that covered, the path to creating a custom Mechanic task is well-established.)

That list wasn't exactly a formal flowchart, but we hope it's helpful as you're evaluating Mechanic for your purposes. At its best, Mechanic is a platform and toolkit that you go to, and return to, when you hit the limits of the Shopify admin. And it's a community that collectively has learned how solve many, many kinds of problems. (Join our [community Slack workspace](resources/slack.md)!)

{% hint style="info" %}
Got a question you need answered now? [Join our Slack workspace.](https://join.slack.com/t/usemechanic/shared\_invite/zt-cq84nrs7-ggYbYTbf\~CrCjTg8nmHP2A) 💬
{% endhint %}

## How does Mechanic work?

### Tasks, events, and actions

A developer writes [**tasks**](core/tasks/) – Mechanic's term for a piece of automation. These tasks can respond to many different [**events**](core/events/), like a Shopify webhook, a manual trigger, a regular interval (e.g. hourly, daily), or an incoming email.

When a task responds to an incoming event, it can choose to generate an [**action**](core/actions/) – an operation that has an effect.

* The [Shopify](core/actions/integrations/shopify.md) action makes changes to a Shopify store, like tagging, publishing, creating or deleting resources. It provides direct and complete access to Shopify's admin API, with support for both REST and GraphQL.
* The [Email](core/actions/email.md) action is for sending email. It supports custom templates, and attachments.
* The [FTP](core/actions/ftp.md) action is for uploading files to an FTP or SFTP server. These files may be generated by the task, or can be fetched from external locations.
* The [HTTP](core/actions/http.md) action performs any request, to any HTTP endpoint. This facilitates integration with third-party APIs.
* The [Files](core/actions/files.md) action generates a variety of file formats, including PDF, CSV, ZIP, and anything retrieved from a public URL. Files generated this way receive a temporary URL of their own, and can be fed into other tasks for further processing.

For a complete list of supported actions, see [Actions](core/actions/).

### Liquid

Mechanic makes heavy use of [**Liquid**](platform/liquid/basics/) – a template language created by Shopify. Its primary use is in [**task code**](core/tasks/code/). In the same way that a Liquid theme receives browser requests and renders HTML, a Mechanic task receives events, and renders actions (by defining them with JSON).

In Mechanic, our Liquid implementation includes additional support for constructing [arrays](platform/liquid/basics/types.md#array) and [hashes](platform/liquid/basics/types.md#hash), and includes many useful [filters](platform/liquid/filters.md), making data processing more efficient.

### Run queues

Mechanic performs work using queues of [**runs**](core/runs/), with no limit on how large each queue can become. If there is a sudden surge of incoming events for a Shopify store, the store's dedicated Mechanic queue could become delayed. This is an important difference between Mechanic and many other systems: in a high-traffic period, Mechanic will never refuse incoming events for a store; instead, it will process each one as soon as possible, by putting them into a run queue. The rate at which Mechanic processes work varies, depending on [concurrency](core/runs/concurrency.md) and the [Shopify API rate limit](core/shopify/api-rate-limit.md).
