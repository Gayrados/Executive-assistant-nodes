# Executive Calendar Workflow Build Manifest

## Import order
1. `WF-01 Meeting Request Intake.json`
2. `WF-02 Missing Info + Agenda Check.json`
3. `WF-03 Necessity - Delegate - Async Decision.json`
4. `WF-04 Priority + Urgency Classification.json`
5. `WF-05 Availability + Conflict + Time Zone Check.json`
6. `WF-06 Buffer - Travel - Focus - Personal-Time Rules.json`
7. `WF-07 Calendar Invite Creation + Color Coding.json`
8. `WF-08 Confirmation + Boss Notification.json`
9. `WF-09 Reschedule - Cancel - Urgent Override.json`
10. `WF-10 Daily Review - Weekly Review - Follow-Up.json`
11. `Executive Calendar Command Center` parent workflow JSON from Batch 1

## Workflow files

### 1. File name
`Executive Calendar Command Center`

### Workflow name
`Executive Calendar Command Center`

### Purpose
Parent orchestration workflow. Receives or simulates request, normalizes it, calls sub-workflows in sequence, logs status, stops on missing information, approval requirements, or validation failures, and routes special/change/review paths.

### Required credentials placeholders
- `GOOGLE_CALENDAR_CREDENTIAL_PLACEHOLDER`
- `GMAIL_CREDENTIAL_PLACEHOLDER`
- `SLACK_CREDENTIAL_PLACEHOLDER`
- `AIRTABLE_CREDENTIAL_PLACEHOLDER`

### Required environment variables
- `EXECUTIVE_TIMEZONE`
- `DEFAULT_CALENDAR_ID`
- `EXECUTIVE_NOTIFICATION_CHANNEL`
- `REQUESTER_FALLBACK_EMAIL`
- `FOLLOW_UP_TRACKER_TABLE_ID`

### Required input fields
- `request_id`
- `request_type`
- `request_source`
- `meeting_purpose`
- `desired_outcome`
- `meeting_type`
- `meeting_format`
- `preferred_datetime`
- `preferred_time_window`
- `deadline`
- `duration_minutes`
- `time_zone`
- `agenda`
- `prep_materials`
- `location`
- `video_link`
- `dial_in_details`
- `confidentiality_level`
- `boss_required`
- `required_attendees_json`
- `optional_attendees_json`
- `hold_owner`
- `hold_expiry`

### Output fields
- `status`
- `request_status`
- `next_step`
- `human_action_required`
- `special_route`
- `status_log_parent_*`

### Dependencies
- WF-01 through WF-10 imported and addressable by `Execute Workflow`

---

### 2. File name
`WF-01 Meeting Request Intake.json`

### Workflow name
`WF-01 Meeting Request Intake`

### Purpose
Normalize and classify raw intake. Covers meeting request received, request intake started, purpose presence, desired outcome presence, routing classification.

### Required credentials placeholders
None

### Required environment variables
None strictly required

### Required input fields
- `request_id`
- `request_type`
- `request_source`
- `meeting_purpose`
- `desired_outcome`
- `meeting_type`
- `meeting_format`
- `preferred_datetime`
- `preferred_time_window`
- `deadline`
- `duration_minutes`
- `time_zone`
- `agenda`
- `prep_materials`
- `location`
- `video_link`
- `dial_in_details`
- `confidentiality_level`
- `boss_required`
- `required_attendees_json`
- `optional_attendees_json`
- `hold_owner`
- `hold_expiry`

### Output fields
- `normalized_request_type`
- `purpose_status`
- `desired_outcome_status`
- `special_route`
- `status`
- `request_status`

### Dependencies
- none

---

### 3. File name
`WF-02 Missing Info + Agenda Check.json`

### Workflow name
`WF-02 Missing Info + Agenda Check`

### Purpose
Detect missing required fields, apply agenda rules, decide hold eligibility, create tentative hold if needed, send missing-information request.

### Required credentials placeholders
- `GOOGLE_CALENDAR_CREDENTIAL_PLACEHOLDER`
- `GMAIL_CREDENTIAL_PLACEHOLDER`

### Required environment variables
- `DEFAULT_CALENDAR_ID`
- `REQUESTER_FALLBACK_EMAIL`

### Required input fields
- `meeting_purpose`
- `desired_outcome`
- `meeting_format`
- `preferred_datetime`
- `preferred_time_window`
- `duration_minutes`
- `time_zone`
- `boss_required`
- `required_attendees_json`
- `agenda`
- `location`
- `video_link`
- `hold_owner`
- `hold_expiry`

### Output fields
- `missing_information_list`
- `agenda_status`
- `hold_status`
- `status`
- `request_status`

### Dependencies
- Google Calendar
- Gmail

---

### 4. File name
`WF-03 Necessity - Delegate - Async Decision.json`

### Workflow name
`WF-03 Necessity / Delegate / Async Decision`

### Purpose
Evaluate executive necessity, delegation, async conversion, decline, shorten/combine, or proceed.

### Required credentials placeholders
- `GMAIL_CREDENTIAL_PLACEHOLDER`

### Required environment variables
- `REQUESTER_FALLBACK_EMAIL`

### Required input fields
- `boss_required`
- `boss_attendance_status`
- `delegate_option`
- `delegate_candidate`
- `async_possible`
- `necessity_decision`
- `recommended_duration`

### Output fields
- `boss_attendance_status`
- `scheduling_outcome`
- `status`
- `request_status`

### Dependencies
- Gmail

---

### 5. File name
`WF-04 Priority + Urgency Classification.json`

### Workflow name
`WF-04 Priority + Urgency Classification`

### Purpose
Assign priority, urgency, audience class, and confidentiality/privacy class.

### Required credentials placeholders
None

### Required environment variables
None

### Required input fields
- `meeting_purpose`
- `meeting_type`
- `priority_signal`
- `urgency_signal`
- `urgency_validated`
- `required_attendees_json`
- `confidentiality_signal`

### Output fields
- `priority_level`
- `priority_reason`
- `urgency_level`
- `urgency_reason`
- `audience_class`
- `confidentiality_level`
- `approval_required`
- `status`

### Dependencies
- none

---

### 6. File name
`WF-05 Availability + Conflict + Time Zone Check.json`

### Workflow name
`WF-05 Availability + Conflict + Time Zone Check`

### Purpose
Check executive availability, attendee conflicts, time zone, duration, and hard/soft conflicts.

### Required credentials placeholders
- `GOOGLE_CALENDAR_CREDENTIAL_PLACEHOLDER`

### Required environment variables
- `DEFAULT_CALENDAR_ID`
- `EXECUTIVE_TIMEZONE`

### Required input fields
- `preferred_datetime`
- `duration_minutes`
- `time_zone`
- `required_attendee_issue`
- `optional_attendee_issue`
- `recurring_commitment_issue`

### Output fields
- `executive_hard_conflict`
- `timezone_issue`
- `approved_duration_minutes`
- `hard_conflict`
- `soft_conflict`
- `status`

### Dependencies
- Google Calendar

---

### 7. File name
`WF-06 Buffer - Travel - Focus - Personal-Time Rules.json`

### Workflow name
`WF-06 Buffer / Travel / Focus / Personal-Time Rules`

### Purpose
Apply protected-time, travel, buffer, and prep rules.

### Required credentials placeholders
None

### Required environment variables
None

### Required input fields
- `priority_level`
- `meeting_type`
- `meeting_format`
- `location`
- `focus_conflict`
- `personal_conflict`
- `break_conflict`
- `personal_override_approved`

### Output fields
- `focus_rule_result`
- `personal_rule_result`
- `break_rule_result`
- `travel_block_needed`
- `travel_minutes_before`
- `travel_minutes_after`
- `buffer_minutes_before`
- `buffer_minutes_after`
- `prep_minutes`
- `prep_block_needed`
- `status`

### Dependencies
- none

---

### 8. File name
`WF-07 Calendar Invite Creation + Color Coding.json`

### Workflow name
`WF-07 Calendar Invite Creation + Color Coding`

### Purpose
Create the main event, support blocks, reminder/privacy/recurrence controls, color/category, and validation result.

### Required credentials placeholders
- `GOOGLE_CALENDAR_CREDENTIAL_PLACEHOLDER`

### Required environment variables
- `DEFAULT_CALENDAR_ID`

### Required input fields
- `meeting_purpose`
- `event_title`
- `preferred_datetime`
- `duration_minutes`
- `approved_duration_minutes`
- `agenda`
- `desired_outcome`
- `prep_materials`
- `location`
- `meeting_type`
- `meeting_format`
- `audience_class`
- `urgency_level`
- `priority_level`
- `time_zone`
- `required_attendees_json`
- `travel_block_needed`
- `travel_minutes_before`
- `travel_minutes_after`
- `prep_block_needed`
- `prep_minutes`
- `confidentiality_level`
- `recurrence_required`

### Output fields
- `event_status`
- `color_code`
- `category_label`
- `reminder_settings`
- `visibility_setting`
- `recurrence_rule`
- `status`
- `request_status`

### Dependencies
- Google Calendar

---

### 9. File name
`WF-08 Confirmation + Boss Notification.json`

### Workflow name
`WF-08 Confirmation + Boss Notification`

### Purpose
Send external confirmation if needed and notify the executive based on significance.

### Required credentials placeholders
- `GMAIL_CREDENTIAL_PLACEHOLDER`
- `SLACK_CREDENTIAL_PLACEHOLDER`

### Required environment variables
- `EXECUTIVE_NOTIFICATION_CHANNEL`

### Required input fields
- `meeting_purpose`
- `event_title`
- `priority_level`
- `priority_reason`
- `audience_class`
- `preferred_datetime`
- `location`
- `prep_block_needed`
- `request_status`

### Output fields
- `confirmation_status`
- `boss_notification_status`
- `status`
- `request_status`

### Dependencies
- Gmail
- Slack

---

### 10. File name
`WF-09 Reschedule - Cancel - Urgent Override.json`

### Workflow name
`WF-09 Reschedule / Cancel / Urgent Override`

### Purpose
Handle reschedules, cancellations, urgent overrides, and general change management.

### Required credentials placeholders
- `GOOGLE_CALENDAR_CREDENTIAL_PLACEHOLDER`
- `GMAIL_CREDENTIAL_PLACEHOLDER`
- `SLACK_CREDENTIAL_PLACEHOLDER`

### Required environment variables
- `DEFAULT_CALENDAR_ID`
- `EXECUTIVE_NOTIFICATION_CHANNEL`

### Required input fields
- `change_type`
- `request_type`
- `event_id`
- `priority_level`
- `reschedule_approved`
- `new_start`
- `new_end`
- `urgency_validated`
- `override_reason`
- `displaced_item`

### Output fields
- `status`
- `request_status`

### Dependencies
- Google Calendar
- Gmail
- Slack

---

### 11. File name
`WF-10 Daily Review - Weekly Review - Follow-Up.json`

### Workflow name
`WF-10 Daily Review / Weekly Review / Follow-Up`

### Purpose
Run daily review, weekly planning, recurring review, prep signal, and follow-up tracking.

### Required credentials placeholders
- `GOOGLE_CALENDAR_CREDENTIAL_PLACEHOLDER`
- `AIRTABLE_CREDENTIAL_PLACEHOLDER`

### Required environment variables
- `DEFAULT_CALENDAR_ID`
- `FOLLOW_UP_TRACKER_TABLE_ID`

### Required input fields
- `wf10_mode`
- `review_window_start`
- `review_window_end`
- `next_week_start`
- `next_week_end`
- `related_meeting_id`
- `action_item`
- `action_owner`
- `action_due_date`
- `recurrence_review_date`

### Output fields
- `daily_review_status`
- `weekly_review_status`
- `follow_up_status`
- `recurring_review_status`
- `prep_packet_status`
- `final_audit_signal`
- `status`
- `request_status`

### Dependencies
- Google Calendar
- Airtable

## Manual approval steps represented outside automation
- Personal-time override approval
- High-priority reschedule approval
- Executive exception requests that override standard rules
- Final judgment on politically sensitive decline/delegate cases
- Urgent override acceptance when displacement is material

## Final audit representation map
- Intake: WF-01
- Missing information handling: WF-02
- Agenda handling: WF-02
- Boss required check: WF-03
- Delegation: WF-03
- Async/email option: WF-03
- Necessity check: WF-03
- Priority and urgency: WF-04
- Internal/external and privacy: WF-04
- Availability, time zone, duration, conflicts: WF-05
- Focus, personal, lunch, travel, buffer, prep: WF-06
- Invite creation, reminders, privacy, recurrence, color, validation: WF-07
- Confirmation and boss notification: WF-08
- Reschedule, cancellation, urgent override, change management: WF-09
- Recurring review, daily review, weekly review, follow-up, prep signal: WF-10
- Parent routing, stop rules, logging: Parent workflow
