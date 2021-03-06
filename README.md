# s3-batch-upload

Super fast batched S3 folder uploads from CLI or API.

## Installation

```sh
yarn add s3-batch-upload
```

```sh
npm i -S s3-batch-upload
```


## Basic Usage

### CLI

```sh
# with config
s3-batch-upload -c ./config/configS3.json -b bucket-name -p ./files -r remote/path/in/bucket

# with env vars
AWS_ACCESS_KEY_ID=AKIA...Q AWS_SECRET_ACCESS_KEY=jY...uJ s3-batch-upload -b bucket-name -p ./files -r remote/path/in/bucket -g "*.jpg -C 200 -d"
```

```
Usage: cli.js <command> [options]

Commands:
  cli.js upload  Upload files to s3                                                                            [default]

Required:
  -b, --bucket       The bucket to upload to.                                                        [string] [required]
  -p, --local-path   The path to the folder to upload.                                               [string] [required]
  -r, --remote-path  The remote path in the bucket to upload the files to.                           [string] [required]

Options:
  -d, --dry-run        Do a dry run, don't do any upload.                                     [boolean] [default: false]
  -C, --concurrency    The amount of simultaneous uploads, increase on faster internet connection.
                                                                                                 [number] [default: 100]
  -g, --glob           A glob on filename level to filter the files to upload                  [string] [default: "*.*"]
  --go, --glob-options  Options to pass to the glob module                                [string] [default: "undefined]
  -a, --cache-control  Cache control for uploaded files, can be string for single value or list of glob settings
                                                                                                  [string] [default: ""]
  -acl, --access-control-level  Sets the access control level for uploaded files
                                                                                                  [string] [default: "undefined"]
  -c, --config         The AWS config json path to load S3 credentials with loadFromPath.                       [string]
  -h, --help           Show help                                                                               [boolean]

Examples:
  cli.js -b bucket-name -p ./files  -r /data                    Upload files from a local folder to a s3 bucket path
  cli.js ... -a "max-age=300"                                  Set cache-control for all files
  cli.js ... -a '{ "**/*.json": "max-age=300", "**/*.*":       Upload files from a local folder to a s3 bucket path
  "max-age=3600" }'
  cli.js ... -g "*" --go.nodir true --go.dot true               Upload files from a local folder (including files with or without extension and .dot files)
  cli.js -d ...                                                 Dry run upload

for more information about AWS authentication, please visit
https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/setting-credentials-node.html

```

### API
```js
import Uploader from 's3-batch-upload';

const files = await new Uploader({
  config: './config/configS3.json', // can also use environment variables
  bucket: 'bucket-name',
  localPath: './files',
  remotePath: 'remote/path/in/bucket',
  glob: '*.jpg', // default is '*.*'
  globOptions: { nodir: true, dot: true }, // optional, additional options to pass to "glob" module
  concurrency: '200', // default is 100
  dryRun: true, // default is false
  cacheControl: 'max-age=300', // can be a string, for all uploade resources
  cacheControl: { // or an object with globs as keys to match the input path
    '**/settings.json': 'max-age=60', // 1 mins for settings, specific matches should go first
    '**/*.json': 'max-age=300', // 5 mins for other jsons
    '**/*.*': 'max-age=3600', // 1 hour for everthing else
  },
  accessControlLevel: 'bucket-owner-full-control' // optional, not passed if undefined. - available options - "private"|"public-read"|"public-read-write"|"authenticated-read"|"aws-exec-read"|"bucket-owner-read"|"bucket-owner-full-control"
}).upload();

// the files array contains a list of uploaded keys, which you can use to build up the S3 urls.
// e.g. "remote/path/in/bucket/demo.jpg"
```

### S3 Authentication

As seem above, you can either use environment variables, or a config file.

When using a config file, add it to the `.gitignore`, because you don't want your credentials
in your repo. Use the following template for the config file as stated in the [AWS Docs](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/loading-node-credentials-json-file.html):

```json
{
  "accessKeyId": "<YOUR_ACCESS_KEY_ID>",
  "secretAccessKey": "<YOUR_SECRET_ACCESS_KEY>",
  "region": "us-east-1"
}
```

When using environment variables, check the [AWS docs](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/loading-node-credentials-environment.html).

## Documentation

View the [generated documentation](http://mediamonks.github.io/s3-batch-upload/).


## Building

In order to build s3-batch-upload, ensure that you have [Git](http://git-scm.com/downloads)
and [Node.js](http://nodejs.org/) installed.

Clone a copy of the repo:
```sh
git clone https://github.com/mediamonks/s3-batch-upload.git
```

Change to the s3-batch-upload directory:
```sh
cd s3-batch-upload
```

Install dev dependencies:
```sh
yarn
```

Use one of the following main scripts:
```sh
yarn build            # build this project
yarn dev              # run compilers in watch mode, both for babel and typescript
yarn test             # run the unit tests incl coverage
yarn test:dev         # run the unit tests in watch mode
yarn lint             # run eslint and tslint on this project
yarn doc              # generate typedoc documentation
```

When installing this module, it adds a pre-commit hook, that runs lint and prettier commands
before committing, so you can be sure that everything checks out.


## Contribute

View [CONTRIBUTING.md](./CONTRIBUTING.md)


## Changelog

View [CHANGELOG.md](./CHANGELOG.md)


## Authors

View [AUTHORS.md](./AUTHORS.md)


## LICENSE

[MIT](./LICENSE) © MediaMonks


