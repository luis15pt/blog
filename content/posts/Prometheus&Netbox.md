---
title: "Prometheus and Netbox"
date: 2025-02-01
author: "Luis Sarabando"
tags: ["monitoring", "prometheus", "netbox", "infrastructure"]
---

Hello there! Maurice Moss speaking. Today I'm quite excited to share something rather brilliant that I've been working on in between fixing printers and helping Roy turn things off and on again. It's about making Prometheus and Netbox work together, which is almost as exciting as that time I mastered the entire Wood Sitting Down genre!

## What You'll Need (Rather Important, Really)

* Netbox version 3.5.0 or newer (don't try it with older versions, trust me - it's like trying to run Windows Vista on a ZX Spectrum)
* Prometheus version 2.28.0+ (the plus is important, like remembering to wear your emergency socks)
* Node exporter or similar running on your target machines (I'll explain why in a moment, like I had to explain to Jen why computers need fans)

## Why Would You Want This? (A Very Good Question!)

Now, let me explain why this integration is absolutely brilliant. You see, normally with Prometheus, you'd have to manually declare all your targets in the configuration - writing them down like Roy writes down his lunch orders. But with this Netbox integration, it becomes dynamic! 

Think of it like this: whenever you add a new server to your infrastructure, you document it in Netbox (as any sensible person would), and BOOM! - Prometheus automatically starts monitoring it. No need to restart Prometheus, no need to update configurations, it just works! It's like magic, except it's actually carefully structured API calls and service discovery.

But here's the catch (there's always a catch, like that time I thought I found a free iPhone but it was actually just a very convincing drawing): you need to have the appropriate exporter (like node_exporter) already running on your target machines. This integration doesn't magically make your machines exportable - it just helps Prometheus find them automatically. It's perfect if you already have a monitoring setup but are tired of manually updating your Prometheus configuration every time you add a new machine.

## Creating an Export Template (The Fun Part!)

Right then! First things first, we need to create what's called an 'Export Template' in Netbox. Navigate to the Customization menu - it's quite straightforward, unlike trying to explain to Jen what IT actually stands for.

For this example, we'll be using 'Virtualization > Virtual Machine' for content types. Here's a template I've crafted (and thoroughly tested while enjoying a nice cup of milk):

```jinja2
[
    {% for vm in queryset %}{% if vm.status and vm.primary_ip %}
    {
        "targets": ["{{ vm.primary_ip.address.ip }}:9100"],
        "labels": {
            "host": "{{ vm.name }}",
            "role": "{{ vm.role }}"
            }
    }{% if not loop.last %},{% endif %}{% endif %}{% endfor %}
]
```

Now, this might look like a bunch of random characters - similar to when Roy tries to type with his elbows - but I assure you it's quite meaningful! Make sure to set the MIME type to application/json under rendering options. And whatever you do, DON'T check 'As attachment' - that would be like putting Smarties in a Smarties tube... actually, that would be fine, bad example.

## Configuring Prometheus (The Really Technical Bit)

Here's how we configure Prometheus to talk to Netbox. It's quite simple really, like making a cup of tea... actually, that's quite complicated when you think about it. Anyway, here's the configuration:

```yaml
  - job_name: "http_sd_netbox"
    http_sd_configs:
      - url: http://your-netbox-instance/api/virtualization/virtual-machines/?export=prometheus
        refresh_interval: 15s
        authorization:
          type: "Token"
          credentials: "<your-token-here>"
```

Replace `your-netbox-instance` with your actual Netbox URL, and `<your-token-here>` with your actual token. Though I probably didn't need to tell you that - you're not Jen, after all!

## A More Complex Example

If you're feeling adventurous (and why wouldn't you be?), here's a more detailed template that includes additional fields. It's like the deluxe version of my regular template, sort of like my special emergency glasses compared to my regular ones:

```jinja2
[
    {% for device in queryset %}{% if device.status and device.primary_ip %}
    {
        "targets": ["{{ device.primary_ip.address.ip }}:9100"],
        "labels": {
            "host": "{{ device.name }}",
            "status": "{{ device.status }}",
            "role": "{{ device.device_role.name }}",
            "site": "{{ device.site.name }}",
            "tenant": "{{ device.tenant.name }}",
            "tags": "{{ device.tags.names() | join(", ") }}"
            }
    }{% if not loop.last %},{% endif %}{% endif %}{% endfor %}
]
```

## Testing Your Configuration

To validate your export template, simply navigate to `http://your-netbox-instance/api/virtualization/virtual-machines/?export=prometheus` (make sure to include the full URL with 'http://' - it's like including the area code when making a phone call, absolutely crucial). 

Now, here's something absolutely vital - as vital as remembering to defragment your hard drive or keeping your emergency mousepads dry: You *must* use the `/api/` path in your URL. For example:

✅ CORRECT: `http://your-netbox-instance/api/dcim/devices/?export=prometheus`

❌ WRONG: `http://your-netbox-instance/dcim/devices/?export=prometheus`

Missing that `/api/` is like trying to send an email without the @ symbol - technically possible to type, but absolutely useless in practice. I learned this the hard way after spending three hours debugging, only to realize I'd forgotten to put on my glasses.

If it works, you should see a nice JSON output. If it doesn't work, well... have you tried turning it off and on again?

## Conclusion

And there you have it! A perfectly integrated system that's almost as satisfying as solving the Grand Unified Theory of Mathematics... which I did do, by the way, but I lost the paper I wrote it on. Probably Roy used it to clean up a coffee spill.

Remember, with great power comes great responsibility, and with great monitoring comes... well, great monitoring, I suppose. I should work on my analogies.

If you need me, I'll be in my office, reading about different kinds of moss. Not related to my name, just a coincidence really.

---

P.S. If anyone needs help with their computer, remember: 0118 999 881 999 119 725... 3.

Written by Luis Sarabando, channeling his inner Maurice Moss from The IT Crowd with a little help of Claude.ai