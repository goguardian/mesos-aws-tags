mesos-aws-tags
==============

Script that fetches the running EC2 instance's tags and generates corresponding
files in `/etc/mesos-slave/attributes/`. This is the behaviour as expected by
[`mesos-init-wrapper`][1].

This script also comes with a provided `mesos-slave.conf` which should supersede
the existing `/etc/init/mesos-slave.conf`. The upgraded init configuration will
run `mesos-aws-tags` as a `pre-start` script.

Prequisites
-----------

  1. An installed version of `mesos` that uses the `mesos-init-wrapper`
     script. We are using the deb from the [official mesosphere repository][3].
  2. `ec2-api-tools` package
  3. [`ec2-metadata`][2] tool

IaM Permissions
---------------

This script implicitly requires IaM permissions to read instance metadata. A sample IaM 
configuration (typically applied to the EC2 instance IaM role) is below:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Stmt1430864375000",
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeTags"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
```

Installation
------------

To install:

  1. Install the prerequisites above
  2. Place `mesos-aws-tags` in /usr/bin/
  3. Replace in /etc/init/mesos-slave.conf with the provided `mesos-slave.conf`

Other Notes
-----------

*Illegal Attribute Key Names*: The script will convert illegal key characters
into underscores. For example, the tag `aws:autoscaling:groupName` will be
converted into `aws_autoscaling_groupName`.

*Checkpointing*: Note that when restarting a mesos-slave with new attributes, 
you may not resume from a checkpoint that had different attributes. To ignore 
this checkpoint and start from scratch, try: 
`rm -f /tmp/mesos/meta/slaves/latest`

License
-------

The MIT License (MIT)

Copyright (c) <year> <copyright holders>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.

[1]: http://stackoverflow.com/questions/30064060/mesos-attributes-source-from-ec2-tags
[2]: https://aws.amazon.com/items/1825?externalID=1825
[3]: https://mesosphere.com/blog/2014/07/17/mesosphere-package-repositories/
