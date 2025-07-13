```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "TenantReadWriteOnlyTenantItems",
      "Effect": "Allow",
      "Action": [
        "dynamodb:GetItem",
        "dynamodb:PutItem",
        "dynamodb:UpdateItem",
        "dynamodb:DeleteItem",
        "dynamodb:Query",
        "dynamodb:BatchGetItem",
        "dynamodb:BatchWriteItem"
      ],
      "Resource": [
        "arn:aws:dynamodb:af-south-1:123456789012:table/MaruTenantItems"
      ],
      "Condition": {
        "ForAllValues:StringEquals": {
          "dynamodb:LeadingKeys": [
            "${aws:PrincipalTag/TENANT_ID}"
          ]
        }
      }
    }
  ]
}
```
