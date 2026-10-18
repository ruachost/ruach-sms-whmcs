# Ruach SMS for WHMCS

Version 1.3.0 · Ruach Networks · https://ruachnet.com/whmcs-sms

SMS notifications for WHMCS, sent over Ruach Networks' direct carrier routes to mobile numbers in any country. The module is free; you need a Ruach SMS account to send messages.

Full documentation: https://docs.ruachnet.com/whmcs-sms · Support: support@ruachnet.com

## What it sends

| Event | When it is sent |
|---|---|
| Invoice created | A new unpaid invoice is published (including renewal invoices) |
| Payment received | An invoice is paid (sent immediately) |
| Invoice overdue | WHMCS sends the 1st, 2nd or 3rd overdue notice |
| Service expiry reminder | X days before the due date, **only if the renewal invoice is unpaid** |
| Domain expiry reminder | X days before the due date, **only if the renewal invoice is unpaid** |
| Service suspended | A product/service is suspended |
| Service reactivated | A product/service is unsuspended (sent immediately) |

## Requirements

- WHMCS 8.0 or later; PHP 7.4 or later with the cURL extension
- The WHMCS cron running every 5–15 minutes
- A Ruach SMS account (https://ruachnet.com) with an API key, a Client ID and an approved Sender ID

## Installation

1. Upload `modules/addons/ruach_sms` to `<your WHMCS>/modules/addons/`.
2. Go to **System Settings → Addon Modules**, find **Ruach SMS for WHMCS** and click **Activate**.
3. Click **Configure**:
   - enter your **API Key**, **Client ID** and **Sender ID (transactional)**, plus a **Sender ID (promotional)** if your destinations route promotional traffic separately
   - set **Countries** (default `NG`), **Expiry reminder days** (default `7,3,1`) and the sending hours
   - optionally set a **Low credit alert** amount
   - tick the admin roles that may use the module under **Access Control**
   - leave **Dry run** on, then save
4. Open **Addons → Ruach SMS for WHMCS → Send test** and send yourself an SMS.
5. Leave dry run on for a day. The **Message log** shows every message the module would have sent and to whom.
6. When the log looks right, turn **Dry run** off.

## How it behaves

- **Sending hours:** invoice, overdue, reminder and suspension messages wait for your sending hours (server time). Payment and reactivation messages go out immediately.
- **Transactional and promotional:** each template is Transactional or Promotional (editable). Set a promotional Sender ID to send promotional templates from it, or leave it blank to use one Sender ID for everything. Promotional messages can have their own sending hours and can be limited to clients opted in to marketing. The log shows the category and Sender ID of every message.
- **No duplicates:** each notification has a unique key, so hooks firing twice or overlapping cron runs never send the same SMS twice.
- **Retries:** failed sends are retried up to 3 times within 2 days. Queued messages older than 2 days expire rather than arrive late.
- **Delivery reports:** each sent message is checked for up to 3 days and shown as Delivered, Undelivered or Awaiting report.
- **Logging:** every send, and every message that isn't delivered, is written to the WHMCS Activity Log and the client's log.
- **Global delivery:** clients in any country can be texted. Use **Countries** to limit sending to the countries you choose, or leave it blank for all.
- **Phone numbers:** formats such as `+234.803…`, `0803…`, `803…`, `+234 0803…` and `00234…` are converted to international format. Numbers that can't be resolved are logged as `skipped`.
- **Message length:** amounts use currency codes (`NGN 25,000.00`), not symbols. A symbol such as ₦ forces Unicode and halves the characters per SMS. The template editor counts characters and SMS parts as you type.
- **Opt-outs:** clients on the opt-out list receive nothing. Closed clients are always skipped.
- **Credit balance:** shown on the Message log page, with an optional low-credit email to admins (at most once a day).
- **Deactivating** keeps your templates, log and opt-outs, unless **Delete data on deactivate** is on.

## Upgrading

Upload the new `modules/addons/ruach_sms` folder over the old one, then open **System Settings → Addon Modules**. WHMCS runs the upgrade automatically; your settings, templates and log are kept.

## Data protection

The module sends your clients' names, mobile numbers and billing details to Ruach to deliver messages. Ruach processes this data as your processor under its Data Processing Agreement: https://ruachnet.com/dpa

## Licence

Free to use with a Ruach SMS account. See `modules/addons/ruach_sms/LICENSE.txt`.

## Changelog

**1.3.0**: Transactional and promotional Sender IDs, per-template category, promotional sending hours and marketing opt-in. Delivery times shown in server time. The Message log filter stays applied after Check, Retry and Refresh.

**1.2.0**: First public release. Adds the "Delete data on deactivate" option.

**1.1.1**: Invoice SMS also sent for invoices published without the email; credit balance shown to 2 decimal places.

**1.1.0**: Delivery reports, Activity Log entries for every send, Delivered/Undelivered filters.

**1.0.0**: Initial release.
