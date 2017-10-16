TabData
=======
`this.props.tabData`

An object which is populated with the details of the currently opened ticket.

```js
import React from 'react';
import { sdkConnect } from 'deskpro-sdk-react';

class TicketInfo extends React.Component {
    render() {
        const { tabData } = this.props;
        
        return (
            <ul>
                <li>ID: {tabData.id}</li>
                <li>Created: {tabData.date_created}</li>
                <li>Subject: {tabData.subject}</li>
            </ul>
        );
    }
}

export default sdkConnect(TicketInfo);
```

The object contains the following properties:

```js
{
    id:                             number,
    is_hold:                        bool,
    access_code:                    string,
    access_code_email_body_token:   string,
    access_code_email_header_token: string,
    agent:                          object,
    attachments:                    array,
    auth:                           string,
    category:                       object,
    count_agent_replies:            number,
    count_user_replies:             number,
    current_user_waiting:           number,
    current_user_waiting_work:      number,
    custom_data:                    array,
    date_agent_waiting:             string,
    date_agent_waiting_ts:          number,
    date_agent_waiting_ts_ms:       number,
    date_archived:                  string,
    date_archived_ts:               number,
    date_archived_ts_ms:            number,
    date_created:                   string,
    date_created_ts:                number,
    date_created_ts_ms:             number,
    date_feedback_rating_ts:        number,
    date_feedback_rating_ts_ms:     number,
    date_first_agent_assign:        string,
    date_first_agent_assign_ts:     number,
    date_first_agent_assign_ts_ms:  number,
    date_first_agent_reply:         string,
    date_first_agent_reply_ts:      number,
    date_first_agent_reply_ts_ms:   number,
    date_last_agent_reply:          string,
    date_last_agent_reply_ts:       number,
    date_last_agent_reply_ts_ms:    number,
    date_last_user_reply:           string,
    date_last_user_reply_ts:        number,
    date_last_user_reply_ts_ms:     number,
    date_locked:                    string,
    date_locked_ts:                 number,
    date_locked_ts_ms:              number,
    date_on_hold:                   string,
    date_on_hold_ts:                number,
    date_on_hold_ts_ms:             number,
    date_resolved:                  string,
    date_resolved_ts:               number,
    date_resolved_ts_ms:            number,
    date_status:                    string,
    date_status_ts:                 number,
    date_status_ts_ms:              number,
    date_user_waiting:              string,
    date_user_waiting_ts:           number,
    date_user_waiting_ts_ms:        number,
    department:                     object,
    has_attachments:                bool,
    hidden_status:                  string,
    labels:                         array,
    language:                       object,
    organization:                   object,
    original_subject:               string,
    parent_ticket:                  string,
    participants:                   array,
    person:                         object,
    person_email:                   object,
    priority:                       string,
    product:                        object,
    ref:                            string,
    sent_to_address:                string,
    status:                         string,
    subject:                        string,
    ticket_hash:                    string,
    ticket_slas:                    object,
    total_to_first_reply:           number,
    total_to_first_reply_work:      number,
    total_to_resolution:            number,
    total_to_resolution_work:       number,
    total_user_waiting:             number,
    total_user_waiting_real:        number,
    total_user_waiting_work:        number,
    urgency:                        number,
    waiting_times:                  array,
    workflow:                       object,
    worst_sla_status:               string
}
```