# Quick Start

The quickest way to get started is to deploy the Partitions example repo.  Keep in mind these are common tools.  There's a good chance you have most if not all already installed.
If not the links below will take you to common locations to download said tools.



## What you'll need

- [git command tool](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
- [node/npm](https://nodejs.org/en/)
- [aws command line access](https://aws.amazon.com/cli/)

## Setup

Once you have your tools prepared pull down the Partitions example repo in a directory of your choice.
```bash
git clone https://github.com/SkylarWapato/partitions-example.git
```

Then, navigate to the repo.
```bash
cd partitions-example
```

After that, install your dependencies.
```bash
npm install
```

Finally, deploy your schema to aws lambda.
```bash
npm run deploy
```

The console will output your Graphql api endpoint upon completion.

As you probably noticed, within the schema.graphql file is a basic example schema.  Feel free to replace these definitions with type definitions of your choice.
For a more in-depth look at how to [build your schema](schema.md)

<!-- ## Examples

### Basic Query -->