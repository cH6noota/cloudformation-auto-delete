# 時限式 CloudFormation
セットした時間経過後、自動的にスタックの削除を行います。

## 利用準備
スクリプト格納用のS3バケットの作成
```
aws s3 mb バケット名
```
スクリプトをS3バケットへ格納
```
aws s3 cp delete.yml s3://バケット名
```

スタック削除用IAMロールの作成
```
aws cloudformation deploy --template-file main_output.yml --stack-name 任意のスタック名
```

## 利用方法
テンプレートへ以下の内容を記述する
```
# 削除するまでの時間（分）
Parameters:
  Timer:
    Type: Number
    MinValue: 3
    Default: 3

Resources:
  # スクリプトの呼び出し
  Fn::Transform:
    Name: AWS::Include
    Parameters:
      Location: s3://バケット名/delete.yml
```
## 例
`example.yml`
```
Description: Time Delte Item
Parameters:
  BucketName:
    Type: String
  Timer:
    Type: Number
    MinValue: 3
    Default: 3

Resources:
  Fn::Transform:
    Name: AWS::Include
    Parameters:
      Location: s3://バケット名/delete.yml
  MyBucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName:
        Ref: BucketName
```

