# MCP Connected but No Publishing Tools? A Diagnostic Checklist

An MCP server can connect successfully while the agent still has no publishing tool available. Check the active client's tool configuration first, then account authentication and destination requirements. A connected indicator alone cannot tell you why publishing is unavailable.

Record the last operation that succeeded and the first one that failed. Those two results help you choose what to investigate before changing credentials or retrying a post.

Groniz connects AI clients to social publishing through its MCP server, with provider capabilities varying by destination. The [setup hub](https://groniz.com/blog/ai-agent-social-media-publishing-setup-a-client-by-client-guide) explains the full path. This checklist helps locate a failure along it.

## Match the symptom to a check

Use this worksheet before changing configuration. Client controls differ, so use the checks to identify what to inspect in your installation.

| Observation | Likely layer to inspect | Next check | Evidence to retain |
| --- | --- | --- | --- |
| No connection | Client configuration or transport | Confirm endpoint and active configuration | Sanitized error and configuration location |
| Connected, no Groniz tools visible | Discovery, client filters, or policy | Inspect server tool availability and client inclusion rules | Visible tool names and active restrictions |
| Discovery tools visible, scheduling absent | Tool selection or write availability | Check the configured tool set and operator controls | Whether scheduling is excluded or unavailable |
| Tool call returns authentication error | Credential route | Verify auth in this client and runtime | Route, source category, sanitized result |
| Accounts load, intended destination absent | Account selection or connection | Compare returned integration IDs with the intended account | Returned account labels and IDs |
| Destination found, post input rejected | Provider schema or payload | Retrieve current requirements | Rejected field and relevant schema requirement |
| Write times out | Delivery result uncertain | Reconcile remote records before retrying | Attempt time, reviewed payload, any returned ID |

The suggested layer is a starting point; use the next result to narrow the cause. For example, when a visible scheduling tool returns an authentication error, inspect its credential route. If the tool is absent, check its availability first.

## Confirm the configuration belongs to this session

Check the active agent and its runtime before editing a file. A configuration used by another agent, a terminal process, or a previous session may explain why a change has no effect.

Use the guide for your actual setup: [Windsurf Cascade](https://groniz.com/blog/how-to-connect-windsurf-cascade-to-social-media-with-mcp), [Hermes Agent](https://groniz.com/blog/how-to-connect-hermes-agent-to-social-media-with-mcp), or [Paperclip](https://groniz.com/blog/how-to-set-up-paperclip-for-reviewed-social-publishing). The configuration and runtime checks differ by client. For NanoClaw, start with the [CLI readiness checklist](https://groniz.com/blog/nanoclaw-social-publishing-a-cli-readiness-checklist); this guide does not establish a remote MCP connection for it.

Keep a short note containing the client name, active agent, configuration location, and credential source category. Record variable names when useful, but omit secret values. Include those details in a troubleshooting handoff so the next operator knows which session to inspect.

## Inspect discovery before changing credentials

List the Groniz tools available to the active agent. Check applicable inclusion filters, disabled tools, runtime scope, and administrator restrictions using that client's supported controls.

If only account discovery was enabled intentionally, the absence of a write tool may be the expected result. Have an authorized operator enable the required tool, then review the payload before using it.

Check the MCP server's tool catalog separately from provider settings. In a CLI `integrations:settings` response, `output.settings` contains the integration schema and the sibling field `output.tools` lists dynamic provider helper tools. Neither field is the client's MCP tool catalog. An empty `output.tools` list does not mean publishing is unsupported.

The [MCP publishing guide](https://groniz.com/blog/social-media-mcp-servers-how-ai-agents-actually-publish-posts) provides background on the client, server, and destination roles.

## Test account access with a read-only task

Once discovery tools are visible, ask the agent to list connected integrations. Select the destination by its returned integration ID, then retrieve its current settings schema.

If account discovery fails, investigate the authentication used by this connection. A successful CLI session elsewhere does not prove that MCP received a valid credential. Preserve the error without copying authorization headers into the report.

If discovery succeeds but the destination is missing, confirm which account the connection reached and whether the intended social account is connected. If the destination is present, compare the proposed post with its required settings, length limits, and media requirements. Keep the account lookup result separate from any payload validation error.

## Review the post after the repair

After repairing the failure, rebuild the complete review packet: final body, integration ID, uploaded media references, required provider settings, and exact ISO timestamp with timezone. Review the packet before submission, even after tool and authentication checks pass.

For an attempted write, distinguish a clear rejection from an unknown outcome. A timeout after submission can leave you uncertain whether a post was created. Inspect the remote queue or available post records before repeating the call. Preserve any post ID already returned.

Close the investigation with a concrete result, such as "scheduling tool now visible; account discovery succeeds; payload awaits review." If delivery is accepted, record it as queued and verify publication separately.

If the remaining issue is an unconnected destination, [open Groniz Connectors](https://groniz.com/console/connectors), complete that connection, and repeat account discovery from the active client.
