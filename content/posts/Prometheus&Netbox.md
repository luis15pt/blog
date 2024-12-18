---
title: "Did You See That Ludicrous Integration Last Night? Prometheus and Netbox"
date: 2024-12-18
author: "Maurice Moss"
tags: ["monitoring", "prometheus", "netbox", "infrastructure"]
---

Hello there! Maurice Moss speaking. Today I'm quite excited to share something rather brilliant that I've been working on in between fixing printers and helping Roy turn things off and on again. It's about making Prometheus and Netbox work together, which is almost as exciting as that time I mastered the entire Wood Sitting Down genre!

## What You'll Need (Rather Important, Really)

* Netbox version 3.5.0 or newer (don't try it with older versions, trust me - it's like trying to run Windows Vista on a ZX Spectrum)
* Prometheus version 2.28.0+ (the plus is important, like remembering to wear your emergency socks)

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

To validate your export template, simply navigate to `/api/virtualization/virtual-machines/?export=prometheus`. If it works, you should see a nice JSON output. If it doesn't work, well... have you tried turning it off and on again?

## Conclusion

And there you have it! A perfectly integrated system that's almost as satisfying as solving the Grand Unified Theory of Mathematics... which I did do, by the way, but I lost the paper I wrote it on. Probably Roy used it to clean up a coffee spill.

Remember, with great power comes great responsibility, and with great monitoring comes... well, great monitoring, I suppose. I should work on my analogies.

If you need me, I'll be in my office, reading about different kinds of moss. Not related to my name, just a coincidence really.

---

P.S. If anyone needs help with their computer, remember: 0118 999 881 999 119 725... 3.