---
title: Warehouse Syncs
redirect_from: 
  - 'Paxdivitiae.com'
  -___Skip to main content
We use cookies (and other similar technologies) to collect data in order to improve our site. You have the option to opt-in or opt-out of certain cookie tracking technologies.

To do so, click here.

✕



Search the Segment documentation

Home

Getting Started
What is Segment?
How Segment Works
Getting Started Guide
A Basic Segment Installation
Planning a Full Installation
A Full Segment Installation
Sending Data to Destinations
Testing and Debugging
What's Next

Use Cases

Guides

Connections

Unify

Engage

Privacy

Monitor

Protocols

Segment App

API

Unified Profiles
Partners
Glossary

Config API
Help
/
Guides
/
Filtering your Segment Data
Filtering your Segment Data
On this page

Filtering with the Integrations Object
Destination filters
Per-Source schema integrations filters
Schema event filters
Protocols Tracking Plan blocking and property omission
Destination Insert Function
Warehouse Selective Sync
Privacy Portal filtering
There are many ways you can use Segment to filter event and object based data to control which destinations it reaches. This document lists the most commonly used ways you can filter data in Segment, and explains when you’d use each.

Use Segment to filter event and object based data

Filtering with the Integrations Object
The Integrations object is the only filtering method that cannot be edited using the Segment web app. As such, it is both the most reliable, and the most complicated filtering option to change. The integrations object is available to all customers regardless of Segment plan.

Use this option when you absolutely, for sure, 100% know that you always, or never want this data in a specific destination or set of destinations. You can also build logic in your app or site to conditionally enable or disable destinations by rewriting this object, however this is not recommended as it is time consuming to change, especially for mobile apps.

The Integrations object filters track, page, group, identify, and screen events from both client and cloud based sources, and routes or prevents them from getting to the listed destinations.

You can use the integrations JSON object as part of your Segment payloads to control how Segment routes your data to specific destinations. An example payload is below:

{
  "anonymousId": "507f191e810c19729de860ea",
  "context": {
    "locale": "en-US",
    "page": {
      "title": "Analytics Academy",
      "url": "https://segment.com/academy/"
    }
  },
  "integrations": {
    "All": true,
    "Mixpanel": false,
    "Salesforce": false,
    "My Destination Function (My Workspace)": true
  }
}
By default, the integrations object is set to 'All': true. You do not need to include this flag in the object to use this behavior, but if you’ll be using the integrations object frequently to control destination filtering, you might want to do this to make it explicit for later readers. Change this to 'All': false to prevent any downstream destinations from receiving data, not including data warehouses. If you set 'Segment.io': false in the integrations object, Analytics.js 2.0 drops the event before it reaches your Source Debugger. You can also add destinations to the object by key, and provide a true or false value to allow or disallow data to flow to them on an individual basis. The Destination Info box at the top of each destination page lets you know how to refer to each destination in the Integrations object.

If you are using multiple instances of a destination, any settings you set in the integrations object are applied to all instances of the destination. You cannot specify an instance of a destination to apply Integrations object settings to. 

Note that destination flags are case sensitive and match the destination’s name in the docs (for example, “AdLearn Open Platform”, “awe.sm”, or “MailChimp”).

The syntax to filter data to a data warehouse is different. Refer to the Warehouse FAQs for more details.

Destination filters
Destination filters allow you to control the data flowing into each specific destination, by examining event payloads, and conditionally preventing data from being sent to destinations. You can filter out entire events, or just specific fields in the properties, in the traits, or in the context of your events. Destination filters support cloud-based (server-side), actions-based, and mobile and web device-mode destinations. Destination filters aren’t available for, and don’t prevent data from reaching your warehouse(s) or S3 destinations.

Destination filters are only available in workspaces that are on a Business Tier plan.

Keep these limitations in mind when using destination filters.

Configuring a destination filter

To set up destination filters from the Segment web app for the destination from which you want to exclude data:

(For web device-mode destinations only) Enable device mode destination filters for your Analytics.js source. To do this, go to your Javascript source and navigate to Settings > Analytics.js and turn the toggle on for Destination Filters.
NOTE: Destination filters for web device-mode only supports the Analytics.js 2.0 source.
Navigate to Connections > Destinations and select the destination you want to set up filters for.
Go to the Filters tab and click + New Filter to create a destination filter. See the Destination Filters documentation for more details.
You can set up destination filters using the options presented in the Segment web app, or using Segment’s Filter Query Logic (FQL). If you use FQL, your query syntax is limited to 5KB per query.

Per-Source schema integrations filters
Integration filters allow you to quickly change which destinations receive specific Track, Identify, or Group events. Access this tool in any Source that is receiving data by navigating to the Schema tab. Schema integration filters are available to workspaces that are on a Business Tier plan only.

You can apply Integrations filters to specific events regardless of whether the source is connected to a Tracking Plan. To update which destination an event can be sent to, click the Integrations dropdown menu to see a list of the destinations each call is sent to. You can turn those destinations on or off from within the dropdown menu.

The Integrations dropdown menu displays a list of destinations each call is sent to

The events filtered out of individual destinations using this method still arrive in your data warehouse(s). Warehouses do not appear in the integration filters dropdown, and you cannot prevent data from flowing to Warehouses using this feature - to do that use Warehouse Selective Sync.

Integration filters are all-or-nothing for each event. If you require more detailed control over which events are sent to specific destinations, you can use Destination Filters to inspect the event payload, and conditionally drop the data or forward it to the destination.

Integration filters won’t override an existing value in the integrations object. If the integration object already has a value for the integration, the per source schema integration filters will not override this. For example, if you’re sending events to Appsflyer with the appsflyerId passed into the integration object:

integrations: {
  Appsflyer: {
    appsflyerId: 'xxxxxx'
  }
}
For the same event you have Appsflyer turned off using the per source schema integrations filter, this filter won’t override the above object with a false value, and events still send downstream. In this scenario, you can use destination filters to drop the event before it sends downstream.

Schema event filters
You can use Schema Event Filters to discard and permanently remove Page, Screen and Track events from event-based sources, preventing them from reaching any destinations or warehouses, as well as omit identify traits and group properties. Use this if you know that you’ll never want to access this data again. This functionality is similar to filtering with the Integrations object, however it can be changed from within the Segment app without touching any code.

When you enable these filters, Segment stops forwarding the data to all of your Cloud- and device-mode destinations, including warehouses, and your data is no longer stored in Segment’s warehouses for later replay.

Use this when you need to disable an event immediately, but may need more time to remove it from your code, or when you want to temporarily disable an event for testing. In addition to blocking track calls, you can block all page and screen calls, as well as omit identify traits and group properties.

If the Source is not connected to a tracking plan, you’ll find event filter toggles next to the Integration filters in the source’s schema tab. When an event is set to block, the entire event is blocked. This means no destinations receive it, including data warehouses.

When you block an event using Schema filters, it won’t be considered in the MTU count unless blocked event forwarding is enabled.

Event filter toggles

When an event is blocked, the name of the event or property appears on your Schema page with a counter which shows how many times it has been blocked. By default, data from blocked events and properties is not recoverable. You can always re-enable the event to continue sending it to downstream destinations.

In most cases, blocking an event immediately stops that event from sending to destinations. In rare cases, it can take up to 6 hours for an event to completely stop arriving in all Destinations.

This feature is only available if the Source is not connected to a Tracking Plan, and is only available in workspaces that are on a Business Tier plan.

Protocols Tracking Plan blocking and property omission
If you’re using Protocols, and you’re confident that your tracking plan includes exactly the events and properties you want to record, you can tell Segment to block unplanned events or malformed JSON. When you do this, Segment discards any data coming from the Source that doesn’t conform to the tracking plan.

By default, the blocked events are permanently discarded: they do not flow to Destinations, and cannot be Replayed (similar to Schema Controls). However, you can opt to send data in violation of the tracking plan to a new Segment Source so you can monitor it. (This source can affect your MTU count.)

If you have Protocols in your workspace, and have a tracking plan associated with the Source, you’ll see additional options in the Schema Configuration section of the Source’s Settings page. From this page you can choose how to handle data violations across different types of calls and properties, whether that be blocking events entirely or omitting violating properties.

Schema Configuration section of a source's Settings page

Destination Insert Function
A customizable way to filter or alter data going from a source to a cloud-mode destination is to use Insert Functions). This feature gives you the ability to receive data from your Segment source, write custom code to alter or block it, and then pass that altered payload to a downstream cloud-mode destination.

Warehouse Selective Sync
Warehouse Selective Sync allows you to stop sending specific data to specific warehouses. You can use this to stop syncing specific events or properties that aren’t relevant, and could be slowing down your warehouse syncs. See the Warehouse Selective Sync documentation to learn more.

This feature is only available to Business Tier customers, and you must be a Workspace Owner to change Selective Sync settings.

Privacy Portal filtering
The Privacy Portal is available to all Segment customers, because Segment believes that data privacy is a right, and that anyone collecting data should have tools to help ensure their users’ privacy. More enhancements are available to BT customers who may need tools for managing complex implementations.

The Privacy Portal tools allow you to inspect your incoming calls and their payloads, detect potential Personally Identifiable Information (PII) in properties using matchers, classify the information by different categories of risk, and use those categories to determine which Destinations may or may not receive the data. Learn more about these features in the Privacy Portal documentation.

Add a new matcher with the Privacy Portal tools

This page was last modified: 02 Feb 2024

Need support?
Questions? Problems? Need more info? Contact Segment Support for assistance!

Visit our Support page
Help improve these docs!
 Edit this page
 Request docs change
Was this page helpful?
 Yes
 No
Get started with Segment
Segment is the easiest way to integrate your websites & mobile apps data to over 300 analytics and growth tools.
Your work e-mail
Request Demo
or
Create free account
© 2025 Segment.io, Inc.

Privacy
Terms
Website Data Collection Preferences ''
---

Instead of constantly streaming data to the warehouse destination, Segment loads data to the warehouse in bulk at regular intervals. Before the data loads, Segment inserts and updates events and objects, and automatically adjusts the schema to make sure the data in the warehouse is inline with the data in Segment.

{% include content/how-a-sync-works.md %}

Warehouses sync with all data coming from your source. However, Business plan members can manage the data that is sent to their warehouses using [Selective Sync](#warehouse-selective-sync).

## Sync Frequency

Your plan determines how frequently data is synced to your warehouse. 

| Plan      | Frequency                                                                                                      |
| --------- | -------------------------------------------------------------------------------------------------------------- |
| Free      | Once a day (every 86,400 seconds)                                                                              |
| Team      | Twice a day (every 43,200 seconds)                                                                             |
| Business* | Up to 24 times a day. Generally, these syncs are fixed to the top of the hour (:00), but these times can vary. |

*If you're a Business plan member and would like to adjust your sync frequency, you can do so using the Selective Sync feature. To enable Selective Sync, please go to **Warehouse** > **Settings** > **Sync Schedule**. 

> info "Why can't I sync more than 24 times per day?"
> Segment does not set syncs to happen more than once per hour (24 times per day). The warehouse product is not designed for real-time data, so more frequent syncs would not necessarily be helpful.

## Sync History
You can use the Sync History page to see the status and history of data updates in your warehouse. The Sync History page is available for every source connected to each warehouse. This page helps you answer questions like, “Has the data from a specific source been updated recently?” “Did a sync completely fail, or only partially fail?” and “Why wasn't this sync successful?”

The Sync History includes the following information:

* **Sync Status**: The possible statuses are:
   * *Success*: The sync run completed without any notices and all rows synced, OR no rows synced because no data was found.
   * *Partial*: The sync run completed with some notices and some rows synced.
   * *Failure*: The sync run completed with some notices and no rows synced.
* **Start Time**: The time at which the sync began. This is shown in your local timezone.
* **Duration**: The length of time the sync took.
* **Synced Rows**: Number of rows successfully synced from the sync run.
* **Notices**: A list of errors or warnings found, which could indicate problems with the sync run. Click a notice message to show details about the result, and any errors or warnings for each collection included in the sync run.

> info ""
> If a sync run shows a partial success or failure, the next sync attempts to sync any data that was not successfully synced in the prior run.

### View the Sync History

To view the Sync History:
1. Go to **Connections > Destinations** and choose the warehouse destination you want to view the sync history for.
2. Click the source you want to view the sync history for.
3. *(Optional)* Click on any of the rows in the Sync History table to see additional details related to that sync. You can view:
   * The **Results** of your sync which shows the number of rows synced for each collection.
   * The **Sync Duration** which shows the **Preparation** and **Loading** times of your sync.

## Warehouse Selective Sync

Warehouse Selective Sync allows you to manage the data that you send to your warehouses. You can use this feature to stop syncing specific events (also known as collections) or properties that aren't relevant, and may slow down your warehouse syncs.

> info ""
> This feature is only available to Business Tier customers. <br><br>You must be a Workspace Owner to change Selective Sync settings.

With Selective Sync, you can customize which collections and properties from a source are sent to each warehouse. This helps you control the data that is sent to each warehouse, allowing you to sync different sets of data from the same source to different warehouses.

> info ""
> This feature only affects [warehouses](/docs/connections/storage/warehouses/), and doesn't prevent data from going to any other [destinations](/docs/connections/destinations/).

When you disable a source, collection or property, Segment no longer syncs data from that source. Segment won't delete any historical data from your warehouse. When you re-enable a source, Segment syncs all events since the last sync. This doesn't apply when a collection or property is re-enabled. Only new data generated after re-enabling a collection or property will sync to your warehouse.

> warning ""
> For each warehouse only the first 5,000 collections per source and 5,000 properties per collection are visible in the Selective Sync user interface. [Learn more about the limits](#selective-sync-user-interface-limits).

> warning ""
> Disabling the `received_at` column will cause your syncs to fail, as all tables use `received_at` as the sort key.

### When to use Selective Sync

By default, all sources and their collections and properties are sent, and no data is prevented from reaching warehouses.

When you disable sources, collections, or properties using Selective Sync, Segment stops sending new data for these sources, collections, or properties to your warehouse. It doesn't delete any existing data in the warehouse.

If you choose to re-enable a source to begin syncing again, Segment loads all data that arrived since the last sync into the warehouse, but doesn't backfill data that was omitted while these were disabled. When a collection or property is re-enabled, data only syncs going forward. It will not be loaded from the last sync.

### Enable Selective Sync

To use Selective Sync:
1. Go to **Connections > Destinations** and select the warehouse you want to enable Selective Sync for.
2. Click the **Settings** tab and click **Selective Sync** in the left menu.
3. Select which sources, collections, and properties to sync. All that is not selected won't be synced to your warehouse.
4. Click **Save Changes**.

### Change sync settings to a single warehouse from multiple sources

To change the sync settings to a single warehouse from multiple sources, follow the same steps as [above](#enable-selective-sync).

This may be valuable if you're looking to make changes in bulk, such as when setting up a new warehouse.


### Change sync settings on a specific Warehouse to Source connection

To manage data from one specific source to an individual warehouse:
1. Go to **Connections > Destinations** and select the warehouse you want to change the sync settings for.
2. On the **Warehouse Overview** page, click the **Schema** you want to change the sync settings for.
3. On the **Settings** tab of the **Sync History** page for that source, select the data you want synced to your warehouse, or deselect the data you don't want synced.

This may be valuable when you're making smaller changes, for example, disabling all properties from one unnecessary collection.

> info ""
> All changes made through Selective Sync only impact an individual warehouse. They don't impact multiple warehouses at once. To make changes to multiple warehouses, you need to enable/disable data for each individual warehouse.

### Selective Sync User Interface Limits

Regardless of schema size, for each warehouse only the first 5,000 collections per source and 5,000 properties per collection can be managed using the Selective Sync user interface. After you hit any of these limits, all future data is still tracked and sent to your warehouse. New collections created after hitting this limit is not displayed in the Selective Sync table.

You will see a warning in the Selective Sync user interface when the warehouse schema has reached 80% of the limit for collections and/or properties. An error message will appear when you've reached the limit.

Contact [Support](https://app.segment.com/help/contact/) to edit Selective Sync settings for any collections and/or properties which exceed the limit.

> warning ""
> Only Workspace Owners can change Selective Sync settings.
