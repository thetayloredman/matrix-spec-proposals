# MSCXXXX: Per-room Profile Overrides

[MSC4133][] introduced arbitrary key-value pairs on user profiles, and [MSC4144][] generalized
that behavior to be applicable on individual message events (a per-message profile).

Presently, room membership only specifies events for a displayname and profile picture, but many
MSCs rely on additional key-value pairs [not currently permitted on room membership events][MSC4133_deets].
This MSC clarifies resolution order and extends arbitrary key-value pairs to individual users'
[`m.room.member`][memb_state] events, allowing for per-room scoped arbitrary profile keys usable by
proposals such as [MSC4247][], [MSC4426][], [MSC4440][], and [MSC4462][].

## Proposal

`m.room.member` events now MAY contain any additional field permitted on [global profiles][MSC4133],
as a [common namespaced identifier][].

When a client renders a user's profile, it SHOULD search for a profile in the following places, in order:
1. IF [MSC4144] is accepted or the client implements it, the `m.per_message_profile` key on the message
  (if the profile is being viewed in a room)
2. Fields from the user's `m.room.member` state event in the room (if the profile is being viewed in a room)
3. Fields from the user's global profile.

If a profile is found in any of these location, clients SHOULD take the entire profile from the first
one found. Clients SHOULD NOT fall through to the global profile on a per-key basis, as it is expected
that desired keys are [propagated to rooms][MSC4466].

Homeservers MUST reject profile keys that should otherwise exist on membership events, such as the
`membership` key (or any key introduced into the spec in the future).

## Potential issues

### Conflicts with other keys

Users could potentially define a `membership` key on their profile and propagate it to rooms. The same
could be true for any future introduced key on `m.room.member` events: validation is key to prevent this.

See [the below listed alternative][#use-of-a-field-under-membership-event]

### State bloat

Adding more contents to room state becomes quite expensive, but this is necessary to make per-room
profile overrides possible.

## Alternatives

### Use of a field under membership event

This would eliminate the issue listed under "Conflicts with other keys," but would conflict with
existing behavior for user display names and profile pictures.

## Security considerations

None known.

## Dependencies

This MSC does not directly depend on any un-merged MSCs, as stage 1 in the resolution process is not
true unless the client supports [MSC4144][]. This MSC supplements other MSCs regarding global account
key-value extensions to apply to rooms.

## Unstable prefix

N/A.

[MSC4133]: https://github.com/matrix-org/matrix-spec-proposals/pull/4133
[MSC4133_deets]: https://github.com/matrix-org/matrix-spec-proposals/blob/main/proposals/4133-extended-profiles.md#implementation-details
[MSC4144]: https://github.com/matrix-org/matrix-spec-proposals/pull/4144
[memb_state]: https://spec.matrix.org/v1.18/client-server-api/#mroommember
[MSC4247]: https://github.com/matrix-org/matrix-spec-proposals/pull/4247
[MSC4426]: https://github.com/matrix-org/matrix-spec-proposals/pull/4426
[MSC4440]: https://github.com/matrix-org/matrix-spec-proposals/pull/4440
[MSC4462]: https://github.com/matrix-org/matrix-spec-proposals/pull/4462
[MSC4466]: https://github.com/matrix-org/matrix-spec-proposals/pull/4466
[common namespaced identifier]: https://spec.matrix.org/v1.18/appendices/#common-namespaced-identifier-grammar
