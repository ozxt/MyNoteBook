# module：email



# module：smtplib

*class* `smtplib.SMTP`(*host=''*, *port=0*, *local_hostname=None*, [*timeout*, ]*source_address=None*）
	SMTP实例封装了一个SMTP连接。

`SMTP.sendmail`(*from_addr*, *to_addrs*, *msg*, *mail_options=()*, *rcpt_options=()*)
	
`SMTP.send_message`(*msg*, *from_addr=None*, *to_addrs=None*, *mail_options=()*, *rcpt_options=()*)
	send_message里面还是调用sendmail来发送邮件，只是它的msg参数是`email.message.Message`，sendmail是`string`
*class* `smtplib.SMTP_SSL`(*host=''*, *port=0*, *local_hostname=None*, *keyfile=None*, *certfile=None*, [*timeout*, ]*context=None*, *source_address=None*)
	SMTP_SSL实例操作同SMTP一样，只是带SSL连接。

