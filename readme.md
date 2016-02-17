# Yaral 

[![Build Status](https://img.shields.io/travis/WatchBeam/yaral.svg?style=flat-square)](https://travis-ci.org/WatchBeam/yaral)

Yaral is Yet Another RAte Limit plugin for Hapi. But, unlike others, it does several nice things!
 - Integrates with you server's Catbox cache
 - Allows you to limit with custom attributes, not just the user's IP.
 - Allows you to limit certain responses, add limiting globally, and adjust the limiting endpoint-by-endpoint

### Concepts

 * The entire server can be limited under one rule, and additionally routes can provide their own limiting rules that are _appended_ to the global rule.
 * Each rule has a list of status codes that it can limit. This allows you to, for example, limit invalid response codes at a lower rate than successful response codes. Responses "bubble up" to the first rule that can handle them. If no rules handle that code, it will not be limited.
 * Limit rules are specified using a maximum number of requests per unit time, similar to the way the Twitter API works.

### Configuration

The following options are available when you register Yaral:
 - `buckets` is an array of interval config for [Limitus](https://github.com/MCProHosting/limitus#limitusrulename-rule) intervals. Each item should have:
    - An identifying `name`
    - An `interval` that allows a `max` number of requests.
    - An `id` function that takes a Hapi request object and returns a string, number or object that identifies the requester.
    - A list of `codes` that specify response codes that count towards this bucket's limit. Responses not in this range will not be limited. Defaults to `['2xx', '3xx']`. *Tip:* to limit all responses, use `['xxx']`.
 - `default` is a bucket `name` or array of names of the bucket applied to all routes. Defaults to `[]`. Buckets are matched first to last.
 - `cache` is the cache name (as configured in the Hapi server) used to store rate limiting data. Defaults to the server's default cache.
 - `enabled` is a boolean whether to enable rate limiting. Useful to disable limiting in tests and development. Default to `true`.
 - `includeHeaders` specifies whether rate limit headers should be included in the response.
 - `limitus` is a Limitus instance to use for this rate limiting. Defaults to `new Limitus()`.

You can also configure options on a per-route basis in `config.plugins.yaral`:
 - `buckets` specifies the bucket `name` or array of of the rate limit buckets to use in addition to the configured `default` rules. Buckets are matched first to last.
 - `enabled` is a boolean which allows you to override a true `enabled` global configuration. This can be used to exclude routes from global rate limits. Defaults to `true`.

Alternately, for routes, you can specify a single string or array as a shorthand for buckets.
