# pod-babashka-aws

## API

The namespaces and functions in this pod reflect those in the official
[aws-api](https://github.com/cognitect-labs/aws-api) library.

Available namespaces and functions:

- `pod.babashka.aws`: `client`, `doc`, `invoke`

## Example

``` clojure
#!/usr/bin/env bb

(require '[babashka.pods :as pods])

(pods/load-pod 'org.babashka/aws "0.0.1")

(require '[pod.babashka.aws :as aws])

(def region "eu-central-1")

(def s3-client
  (aws/client {:api :s3 :region region}))

(aws/doc s3-client :ListBuckets)

(aws/invoke s3-client {:op :ListBuckets})

(aws/invoke s3-client
            {:op :CreateBucket
             :request {:Bucket "pod-babashka-aws"
                       :CreateBucketConfiguration {:LocationConstraint region}}})

(require '[clojure.java.io :as io])

(aws/invoke s3-client
            {:op :PutObject
             :request {:Bucket "pod-babashka-aws"
                       :Key "logo.png"
                       :Body (io/input-stream
                              (io/file "resources" "babashka.png"))}})
```

See [test/script.clj](test/script.clj) for an example script.

## Differences with aws-cli

- Credentials: currently only `~/.aws/credentials` and environment variables are supported.
- This pod doesn't require adding dependencies for each AWS service.
- Multi-part uploads haven't been verified to work yet. Might be fixed in a later version.
- Async might be added in later version.

## Build

Run `script/compile`. This requires `GRAALVM_HOME` to be set.

## Test

To test the pod code with JVM clojure, run `clojure -M test/script.clj`.

To test the native image with bb, run `bb test/script.clj`.

## Thanks

Thanks to Valtteri Harmainen for feedback on and testing the initial version.

## License

Copyright © 2020 Michiel Borkent, Jeroen van Dijk and Rahul De.

Distributed under the Apache License 2.0. See LICENSE.
