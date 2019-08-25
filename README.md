# SYNOPSIS

🏗 Prebuilt version of `node-canvas` (2.6.0) for use with AWS Lambda (Amazon Linux AMI support).

## USAGE

Install this node module in the root of your Lamba function's directory.

```sh
git clone https://github.com/joemccann/node-canvas-prebuilt-for-lambda.git
```

In the root of your Lambda function's directory, you need to replace the local version of `node-canvas` with the Amazon Linux AMI supported version.

First, create the directory in the rare case it doesn't exist.

```sh
mkdir -p /path/to/your/lambda/node_modules/canvas/build/Release/
```

Copy over the local version of `node-canvas`

```sh
cp -r /path/to/your/lambda/node_modules/node-canvas-prebuilt-for-lambda/ /path/to/your/lambda/node_modules/canvas/
```

The prebuilt binary is now in place.

> NOTE: If you see some missing dependencies upon deploying, just `npm install` them.

## DEPLOYMENT

No matter how you deploy your Lambda function, whether with AWS CLI or some other tool, you will need to include the `node_modules/canvas` in the zip file.

For example, if your lambda function is in the file `index.js` you will need to zip your `index.js` and `node_modules/` into the zip file itself. This will bloat your Lambda but _c'est la vie_.

```sh
zip -r function.zip index.js node_modules/
```

Now, deploy it the first time or update the code depending on if you are updating to a new versio of the lambda itself.

To create for the first time:

```sh
aws lambda create-function --function-name my-function \
--zip-file fileb://function.zip --handler index.handler --runtime nodejs10.x \
--role arn:aws:iam::1337:role/Lambdas
```

To update the Lambda going forward:

```sh
aws lambda update-function-code --function-name my-function \
--zip-file fileb://function.zip
```

> NOTE: You will need to create a Lambda CLI approved role to create and update Lambda functions. If you haven't already, go here to create the role: [Create Lambda CLI role](https://docs.aws.amazon.com/lambda/latest/dg/with-userapp.html#with-userapp-walkthrough-custom-events-create-iam-role)

Test it by invoking the function:

```sh
aws lambda invoke --function-name my-function output | cat output
```

You should see something like:

`"<img src="\"data:image/png;base64,asdfadsfasdfadsfadsfads......\"" />"`

## SUPPORT

To do this yourself, `npm i canvas@next` on your Amazon Linux AMI EC2 server, then zip that entire `node_modules` directoy into a file named `node-canvas.zip`, download, and unzip here:

```sh
unzip -r node-canvas.zip
```

> NOTE: This requires an additional download due to a [bug](https://github.com/Automattic/node-canvas/issues/1448) in Lambda's version of Linux.

```sh
wget https://github.com/jwerre/node-canvas-lambda/raw/master/canvas-lib64-layer.zip
```

These files should be unzipped in the `node_modules/canvas/build/Release/` folder.

```sh
unzip canvas-lib64-layer.zip
mv lib/* node_modules/canvas/build/Release/
```

Read more here: [node-canvas-layer](https://github.com/jwerre/node-canvas-lambda)

> This repo only supports Node.js version `10.15`, the most recent version of Node.js that is officially supported by AWS Lambda.

## LICENSE

MIT
