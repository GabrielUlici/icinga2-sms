# icinga2-sms

Sending Icinga 2 notifications via SMS with Twilio!

## Preparing

Create a twilio account (https://www.twilio.com/console)

Then you need to add a "Messaging Services" (https://www.twilio.com/console/sms/services) to your account. And buy a phone number (https://www.twilio.com/console/sms/services/SERVICESID/numbers).

Doc about Messaging Services: https://www.twilio.com/docs/sms/send-messages#messaging-services
## Examples

The phone numbers have to be international format (https://support.twilio.com/hc/en-us/articles/223183008-Formatting-International-Phone-Numbers) and for a contact there is the possibility to add multiple numbers as a comma separated list.

'+XXXX'

'+XXXX, ‭+XXXX‬, +XXXX‬'

### Testing a notification

```ini
sudo -u nagios ./host-by-sms.sh \
  -d 'LONGDATE' \
  -l 'HOSTALIAS' \
  -n 'HOSTDISPLAYNAME' \
  -o 'HOSTOUTPUT' \
  -r '+XXXX' \
  -s 'HOSTSTATE' \
  -t 'NOTIFICATIONTYPE'
```

```ini
Output SMS : [PROBLEM] Host host-display-name is WARNING!
```

```ini
sudo -u nagios ./service-by-sms.sh \
  -d 'LONGDATE' \
  -e 'SERVICENAME' \
  -l 'HOSTALIAS' \
  -n 'HOSTDISPLAYNAME' \
  -o 'HOSTOUTPUT' \
  -r '+XXXX, ‭+XXXX‬, +XXXX‬' \
  -s 'SERVICESTATE' \
  -t 'NOTIFICATIONTYPE' \
  -u 'SERVICEDISPLAYNAME'`
```

```ini
Output SMS :  [RECOVERY] processes on host-display-name is OK!
```

### Icinga2 objects
#### Example Command Definitions

```ini
object NotificationCommand "Host Alarm By SMS" {
    import "plugin-notification-command"
    command = [ "/etc/icinga2/scripts/host-by-sms.sh" ]
    arguments += {
        "-d" = {
            required = true
            value = "$icinga.long_date_time$"
        }
        "-l" = {
            required = true
            value = "$host.name$"
        }
        "-n" = {
            required = true
            value = "$host.display_name$"
        }
        "-o" = {
            required = true
            value = "$host.output$"
        }
        "-r" = {
            required = true
            value = "$user.pager$"
        }
        "-s" = {
            required = true
            value = "$host.state$"
        }
        "-t" = {
            required = true
            value = "$notification.type$"
        }
        "-v" = "$notification_logtosyslog$"
    }
}
```

```ini
object NotificationCommand "Service Alarm By SMS" {
    import "plugin-notification-command"
    command = [ "/etc/icinga2/scripts/service-by-sms.sh" ]
    arguments += {
        "-d" = {
            required = true
            value = "$icinga.long_date_time$"
        }
        "-e" = {
            required = true
            value = "$service.name$"
        }

        "-l" = {
            required = true
            value = "$host.name$"
        }
        "-n" = {
            required = true
            value = "$host.display_name$"
        }
        "-o" = {
            required = true
            value = "$service.output$"
        }
        "-r" = {
            required = true
            value = "$user.pager$"
        }
        "-s" = {
            required = true
            value = "$service.state$"
        }
        "-t" = "$notification.type$"
        "-u" = {
            required = true
            value = "$service.display_name$"
        }
        "-v" = {
            required = false
            value = "$notification_logtosyslog$"
        }
    }
}
```