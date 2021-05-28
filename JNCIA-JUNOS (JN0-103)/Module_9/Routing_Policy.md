# Routing Policy

## Objectives

- Describe the framework for routing policies
- Explain the evaluation of routing policies
- Identify situations where you might use routing policies
- Create and apply a routing policy

## Routing Policy

Use routing policy to accept, reject, or modify attributes for routes:

- Choose routes to be received from neighbors running dynamic routing protocols
- Choose routes to be sent to neighbors running dynamic routing protocols
- Modify attributes on routes as they enter or leave the routing table
- Use routing policy to control the flow of routing information into the forwarding table

## Types of Routing Policies

### Import Policies

These policies control how the software imports routes into the routing table. The policies are applied before placing them in the routing table. This can change the routes that are available in the routing table and also affect the local route selection process.

### Export Policies

These policies control how the software sends routes from the routing table to either dynamic routing protocols or the forwarding table. The policies are applied as it exports the routes. Only *active routes* are available for export from the routing table. Thus, although it can choose which active routes it can export, it cannot export inactive routes.

Junos applies export policies applies as it exports the routes from the routing table so the attributes don't affect the local routing table.

## Default Routing Policy

Every protocol has its own import and export policy.

| Protocol | Import Policy | Export Policy |
--- | --- | --- |
| BGP | Accept all BGP routes and import into `inet.0` | Advertise all active BGP routes|
| OSPF | Accept all OSPF routes into `inet.0` | Reject everything (protocol floods by default) |
| IS-IS | Accept all IS-IS routes into `inet.0` | Reject everything (protocol floods by default) |
| RIP | Accept all RIP routes from configured neighbors into `inet.0` | Reject everything |

## Building Blocks of Routing Policy

Routing policies contain ordered blocks called *terms*. Terms are the building blocks of routing policies. Terms are like if-else statements which are evaluated sequentially until it reaches a terminating action or end of policy.

Policies are created with a user-defined name and consist of zero or more terms.

### Terms

Terms are also given user-defined names as a meaningful identifier but are evaluated in the order which they are configured and the names have no effect on the evaluations. Terms consist of:

- `from` statements which describe the match conditions. *The from statement optional. If you omit it, all routes will match the term*
- `then` statements describe the actions to take if the `from` conditions are matched.

You can use the `insert` command in configuration mode to modify the order in which terms appear.

### Common Match Criteria

The *from* statements describe match conditions. Some of the common match criteria are:

- Prefix (route-filter or prefix-list)
- Protocol (Static, BGP, OSPF, IS-IS, etc)
- Routing Protocol attributes:
    - OSPF area ID, AS path, BGP community, Next hop etc

### Multiple from Statements - an AND or an OR

When a term is defined with multiple from statements, there are two possible combinations:

1. Multiple from statements of the same type or matching the same attribute. Junos evaluates situations like this as an **OR** statement. That is, if one of the criteria matches, the `then` action(s) are performed Example:

```text
[edit policy-options policy-statement TEST]
user@router# show | display set relative
set term 1 from protocol bgp
set term 1 from protocol static
```

In this example, if the route is BGP **OR** Static, it matches the term.

2. Multiple from statements of different types or matching different attributes. Junos evaluates this as an **AND** statement. Example:

```text
[edit policy-options policy-statement TEST]
user@router# show | display set relative
set term 2 from protocol direct
set term 2 from interface ge-0/0/0.0
```

With this, if the route is direct **AND** the outbound interface is ge-0/0/0.0, it matches the term.

### Then actions

When the term is matched based on the `from` statement, Junos executes all the actions in the *then* statement of the term. However, the policy stops evaluating if the `then` action is a *Terminating Action*. The Termination actions such as

- accept
- reject

This is called a First Match Policy.

## Prefix Lists

These are named lists of prefixes. They are configured under the `[edit policy-options]` hierarchy. It can be used in multiple places. It can be referenced in multiple terms in a single policy or in different policies.

Prefix lists can be used in both routing policies and firewall filters. However, they can't be used for stateful firewall rules. Prefix list can be used in two ways in the `from` statement of the routing policy:

1. When referenced in a `prefix-list <name>` statement, routes match only if they exactly match one of the prefixes in the list and then the `then` action is performed.
2. When referenced in a `prefix-list-filter` statement, you can specify a match type of *exact, longer or orlonger* to be applied to the listed prefixes. You can also specify an optional action to be taken if the filter matches. This is executed immediately after the match occurs and the `then` statement is ignored.

## Route Filters

Route filters are lists of prefixes configured within a single routing policy or policy term and match individual routes within a policy. They have various match types that are supported such as *exact* and *orlonger*.

Route filters are **not reusable** and they are term specific.

Route filters also have an optional action that are executed immediately after the match occurs and the `then` statement is ignored.

## Match Types

These determine how the route filters are evaluated against incoming routes:

- *exact*: only routes that match the given prefix exactly will match the filter statement. For instance, this `from route-filter 192.168.0.0/16 exact` statement, only the 192.168.0.0/16 will match this. (=)
- *orlonger*: routes within the specified prefix - greater than or equal to the prefix length will match the filter statement. So for this statement `from route-filter 192.168.0.0/16 orlonger`, any route in this ip block with prefix-length /16 to /32 will match. *Greater than or equal to*
- *longer*: same as the *orlonger* match type, the only difference is that the prefix itself won't match the statement when the *longer* statement is used. This statement `from route-filter 192.168.0.0/16 longer` will only match routes in the block whose prefix-lengths are greater than /16 that is /17 to /32. *Greater than*
- *upto*: this behaves like the *orlonger* match type but provides an upper limit to the acceptable prefix length. `from route-filter 192.168.0.0/16 upto /24` means that - /16 >= prefix-length =< /24. Hence routes >= /25 will not match.
- *prefix-length-range*: this match type provides both a lower and an upper limit to the acceptable prefix length. `from route-filter 192.168.0.0/16 prefix-length-range /20-/24` means that all routes that are subsets of *192.168.0.0/16* and have prefix lengths /20 and /24 (inclusive) will match.

### Matching Summary

**Step 1: Matching**  
When Junos OS searches for a match between the configured prefix and mask in a route-filter and a route's prefix and mask, it searches for matches starting with the most specific route filter entry and ends with the least specific entry.

Junos checks for the *longest* match first. The second *from* statement could be the longest match.

**Step 2: Evaluation**  
When the software finds a match, it evaluates the route against the route filter match type. If a match exists, it matches the route filter. If the prefix doesn't match the route filter, the comparison fails even if the prefix might match a less specific entry in the route filter.

After matching on the longest match, it checks the match type - exact, orlonger etc. If it matches the match type, the associated action is taken. Else, it moves to the next term until it gets to the default policy of the protocol.

You cant test the effectiveness of your policy including route filters and prefix lists, using the `test policy` command. The `test policy` command always uses accept as the default policy, so unless you explicitly reject all routes that you do not want to match you might see more routes matching than you want.

## Common Actions

Common actions in routing policy include:

1. Terminating actions:
    - accept
    - reject

`default-action-accept` and `default-action-reject` do not cause the policy evaluation to stop but they overrule the default policy's accept or reject determination.

2. Flow Control:
    - next term
    - next policy

3. Modifying attributes:
    - community (add, delete and set)
    - preference

## Implementing Routing Policy

### Defining Routing Policy

Define the routing policy under the `[edit policy-options]` hierarchy level. Create your terms.

### Applying Routing Policy

Apply the import and export policies at multiple levels of the hierarchy, depending on the routing protocol. Junos always apply the most specific import or export policy. Hence, import or export policies applied at higher levels will apply to the lower levels if there are no policies existing at that level.

## Routing Policy Flow

Junos list terms sequentially from top to bottom and evaluates them in that order.

- It checks each term for its match criteria.
- When a match occurs, Junos performs the associated `then` action in the term.
- If there's no match in the term, it checks the next term.
- This repeats itself for all terms.
- If there's no match in the last term, Junos checks the next applied policy and then eventually the default policy for the protocol.

When a match occurs within a term, Junos takes the corresponding action. If the action is a terminating action, Junos stops processing the terms and the applied policies stops - else processing continues.

Policies can be cascaded to form a chain of policy processing. Each policy will be evaulated until Junos reaches a terminating action.