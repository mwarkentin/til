You can create an AWS SES domain identity and configure the validation records in Cloudflare using Terraform:

```
resource "aws_ses_domain_identity" "foo_dot_com" {
  domain = "foo.com"
}

resource "aws_ses_domain_dkim" "foo_dot_com" {
  domain = aws_ses_domain_identity.foo_dot_com.domain
}

resource "cloudflare_record" "aws_ses_foo_dot_com_dkim_record" {
  for_each = toset(aws_ses_domain_dkim.foo_dot_com.dkim_tokens)

  zone_id = local.cloudflare_zone_id
  name    = "${each.key}._domainkey"
  value   = "${each.key}.dkim.amazonses.com"
  type    = "CNAME"
  ttl     = 600
}
```
