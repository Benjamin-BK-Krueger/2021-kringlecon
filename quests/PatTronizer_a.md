Looking at https://apply.jackfrosttower.com/?p=opportunities it seems the images contain some IMDS information. Trying out some combinations using BurpSuite Repeater ends in

```
GET /images/77.jpg HTTP/2
{
	"Code": "Success",
	"LastUpdated": "2021-05-02T18:50:40Z",
	"Type": "AWS-HMAC",
	"AccessKeyId": "AKIA5HMBSK1SYXYTOXX6",
	"SecretAccessKey": "CGgQcSdERePvGgr058r3PObPq3+0CfraKcsLREpX",
	"Token": "NR9Sz/7fzxwIgv7URgHRAckJK0JKbXoNBcy032XeVPqP8/tWiR/KVSdK8FTPfZWbxQ==",
	"Expiration": "2026-05-02T18:50:40Z"
} 
```

Note: The server/application sadly was broken for a few days so I couldn't document the "real" way/the real solution here. I'll update it later.
