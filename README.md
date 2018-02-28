
pdmaint
=======

A command line utility for scheduling and managing maintenance windows in PagerDuty.

Install
============
Run `pip install -r requirements.txt`

Requirements
============

pdmaint uses the python ``pygerduty`` library to interact with the PagerDuty API.
This library can be installed from https://github.com/dropbox/pygerduty or via ``pip install pygerduty``.
Make sure the version of pygerduty you have installed supports v2 of the PagerDuty API (version 0.36 or later).

pdmaint also makes use of the ``pytz`` library for timezone data.
This library can be installed from http://pytz.sourceforge.net/ or via ``pip install pytz``.

Documentation
=============

To get started with pdmaint, copy the file pdmaint-sample to ~/.pdmaint and
edit the [pagerduty] section.  Include a v2 API key for your PagerDuty account,
and an e-mail address associated with your PagerDuty account.

Also make sure to set your time zone properly.  It should match the time zone
specified in your Pager Duty account.  For a list of time zone strings simply
run the tzlist command.

Once the [pagerduty] section of your ~/.pdmaint file is set up then invoke
pdmaint with the "services" command and you should see output that
summarizes your defined services.  It should look something like this:

    $ pdmaint services
    Service name                             Service ID
    ---------------------------------------------------
    Production Alerts                        TV4ATV1
    Staging Alerts                           TQDX1FV
    Test services                            TT42DPX

You can now schedule maintenance for any of these services using the pdmaint
command line:

    $ pdmaint schedule -d 30 -n "30 minute test" -i TT42DPX
    ID: TX3JWSD : 30 minute test
    created by: Bruce Pennypacker
    start time: Thu Oct  9 13:18:47 2014 -0400
    end time  : Thu Oct  9 13:48:46 2014 -0400
    services:
        Test services

And to end the maintenance window on PagerDuty:

    $ pdmaint delete TX3JWSD

To schedule multiple services for maintenance just specify multiple service
IDs separated by commas:

    $ pdmaint schedule -d 60 -n "maintenance work" -i TV4ATV1,TQDX1FV,TT42DPX
    ID: TP3DXQ1 : 30 minute test
    created by: Bruce Pennypacker
    start time: Thu Oct  9 13:23:11 2014 -0400
    end time  : Thu Oct  9 13:53:09 2014 -0400
    services:
        Production Alerts
        Staging Alerts

If you find yourself reguarly scheduling similar maintenance windows then
you can define templates in your ~/.pdmaint configuration file.  Sections in
the configuration file whose name ends with _schedule define templates
that let you quickly and easily schedule common maintenance windows.  For
example, the folloing block defines a 2 hour maintenance window for
performing releases to two services and a 30 minute window for testing another.

    [release_schedule]
    duration=120
    note=Release maintenance window
    ids=TV4ATV1,TQDX1FV

    [test_schedule]
    duration=30
    note=Testing
    ids=TT42DPX


With the above entry in your ~/.pdmaint you can now do the following:

Get a list of defined schedules:

    $ pdmaint schedules
    release
    test

Schedule a release maintenance window:

    $ pdmaint schedule release
    ID: TJP53AF : maintenance window
    created by: Bruce Pennypacker
    start time: Thu Oct  9 13:43:54 2014 -0400
    end time  : Thu Oct  9 14:13:53 2014 -0400
    services:
        Production Alerts
        Staging Alerts

If desired you can override a defined maintenance window.  Suppose you know
that a release will take an hour longer than usual:

    $ pdmaint schedule release -d 180
    ID: TJQ51A5 : maintenance window
    created by: Bruce Pennypacker
    start time: Thu Oct  9 13:47:33 2014 -0400
    end time  : Thu Oct  9 16:47:32 2014 -0400
    services:
        Production Alerts
        Staging Alerts

Upgrading From PagerDuty v1 API Keys to v2 API Keys
============

PagerDuty [has announced](https://v2.developer.pagerduty.com/docs/v1-rest-api-decommissioning-faq) that
version 1 of their API will be decommissioned on October 19, 2018. At that time legacy version 1 API keys
will no longer work with PagerDuty.

pdmaint now supports version 2 of PagerDuty API keys, so if you haven't done so already then you will
need to log into your PagerDuty Account, click on ``Configuration`` -> ``API Access``, and generate a
new v2 API key to use with pdmaint.

Once you have generated the new v2 API key, simply edit ~/.pdmaint and change the ``api_key`` value
in that file. You can also delete the ``account`` entry in the ~/.pdmaint file as it is no longer
needed.

That's all you need to do! Easy enough!
