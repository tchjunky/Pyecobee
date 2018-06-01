Pyecobee: A Python implementation of the `ecobee API <https://www.ecobee.com/home/developer/api/introduction/index.shtml>`_
===========================================================================================================================

Introduction
============
Pyecobee is a simple, elegant, and object oriented implementation of the ecobee API in Python. It is compatible with Python 2.6/2.7/3.3/3.4/3.5/3.6

**Warning:** Pyecobee has been tested with an ecobee Smart Si. Though the following methods have not been tested I
believe they should work find. Please create an `issue <https://github.com/sfanous/Pyecobee/issues>`_ or even better
create a `pull request <https://github.com/sfanous/Pyecobee/pull/new/master>`_ if you encounter an issue with any of
them.

- control_plug: I don't own an ecobee smart plug, so couldn't test this function
- reset_preferences: I didn't want to wipe my thermostat's settings
- set_occupied: Can only be used by an EMS thermostat
- update_sensor: Requires an ecobee3 or ecobee4 thermostat
- All Hierarchy requests: Accessible to EMS and Utility accounts only
    - list_sets
    - list_users
    - add_set
    - remove_set
    - rename_set
    - move_set
    - add_user
    - remove_user
    - unregister_user
    - update_user
    - register_thermostat
    - unregister_thermostat
    - move_thermostat
    - assign_thermostat
- All Utility requests: Accessible to Utility accounts only
    - list_demand_response
    - issue_demand_response
    - cancel_demand_response
    - issue_demand_management
- All Runtime Report Job requests: Accessible to Utility accounts only
    - create_runtime_report_job
    - list_runtime_report_job_status
    - cancel_runtime_report_job

**Disclaimer:** Any ecobee API Keys, Authorization/Access/Refresh Tokens used in the following examples are fake.

JSON Vs Objects
===============
Whereas JSON notation is used for the serialization/deserialization of request/response objects sent to and from the
ecobee API, Pyecobee's interface is based on core Python data types and user defined objects instead. Pyecobee
handles the serialization of Python objects into JSON request objects and deserialization of JSON response objects
into Python objects thus completely alleviating the developer's need to create/parse JSON objects.

Pyecobee response from an authorize request
-------------------------------------------

.. code-block:: python

    authorize_response = ecobee_service.authorize()
    pin = authorize_response.ecobee_pin
    code = authorize_response.code
    scope = authorize_response.scope
    expires_in = authorize_response.expires_in
    interval = authorize_response.interval

Installation
============
To install Pyecobee:

.. code-block:: bash

    $ pip install pyecobee

Enjoy.


Documentation
=============
Pyecobee comes with extensive documentation. Use dir and help to explore all the details.
.. code-block:: python

    >>> from pyecobee import *
    >>> dir(EcobeeService)
    >>> help(EcobeeService)

General usage
=============
The **EcobeeService** class provides the ecobee API implementation.

To use Pyecobee follow these steps

- Import the modules
- Instantiate an EcobeeService object
- Complete the authorization sequence if required (authorize + request_tokens)
- Refresh tokens if required (refresh_tokens)
- Invoke the needed ecobee API requests/functions

All Pyecobee user defined objects overload __repr__, __str__, and implement a pretty_format method.

.. code-block:: python

    >>> repr(authorize_response)
    AuthorizeResponse(ecobee_pin='bv29', code='uiNQok9Uhy5iScG4gncCAilcFUMK0zWT', scope='smartWrite', expires_in=9, interval=30)

    >>> str(authorize_response)
    AuthorizeResponse(ecobeePin=bv29, code=uiNQok9Uhy5iScG4gncCAilcFUMK0zWT, scope=smartWrite, expires_in=9, interval=30)

    >>> authorize_response.pretty_format()
    AuthorizeResponse(
      ecobeePin=bv29,
      code=uiNQok9Uhy5iScG4gncCAilcFUMK0zWT,
      scope=smartWrite,
      expires_in=9,
      interval=30
    )

Import the modules
------------------
.. code-block:: python

    from pyecobee import *

Instantiate an EcobeeService object
-----------------------------------

.. code-block:: python

    ecobee_service = EcobeeService(thermostat_name='My Thermostat',
                                   application_key='jiNXJ2Q6dyeAPXxy4HsFGUp1nK94C9VF')

Authorization & Token Requests
------------------------------
Authorize
^^^^^^^^^

.. code-block:: python

    authorize_response = ecobee_service.authorize()
    logger.info(authorize_response.pretty_format())
    logger.info('Authorization Token => {0}'.format(ecobee_service.authorization_token))

A successful invocation of authorize() returns an EcobeeAuthorizeResponse instance



Request Tokens
^^^^^^^^^^^^^^

.. code-block:: python

    token_response = ecobee_service.request_tokens()
    logger.info(token_response.pretty_format())
    logger.info(
            'Access Token => {0}\n'
            'Access Token Expires On => {1}\n'
            'Refresh Token => {2}\n'
            'Refresh Token Expires On => {3}'.format(ecobee_service.access_token,
                                                     ecobee_service.access_token_expires_on,
                                                     ecobee_service.refresh_token,
                                                     ecobee_service.refresh_token_expires_on))

A successful invocation of request_tokens() returns an EcobeeTokenResponse instance


Refresh Tokens
^^^^^^^^^^^^^^

.. code-block:: python

    token_response = ecobee_service.refresh_tokens()
    logger.info(token_response.pretty_format())
    logger.info(
            'Access Token => {0}\n'
            'Access Token Expires On => {1}\n'
            'Refresh Token => {2}\n'
            'Refresh Token Expires On => {3}'.format(ecobee_service.access_token,
                                                     ecobee_service.access_token_expires_on,
                                                     ecobee_service.refresh_token,
                                                     ecobee_service.refresh_token_expires_on))

A successful invocation of refresh_tokens() returns an EcobeeTokenResponse instance


Thermostat Requests
--------------------
Request Thermostat Summary
^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: python

    thermostat_summary_response = ecobee_service.request_thermostats_summary(selection=Selection(
            selection_type=SelectionType.REGISTERED.value,
            selection_match='',
            include_equipment_status=True))
    logger.info(thermostat_summary_response.pretty_format())

A successful invocation of request_thermostats_summary() returns an EcobeeThermostatsSummaryResponse instance



Request Thermostats
^^^^^^^^^^^^^^^^^^^

.. code-block:: python

    # Only set the include options you need to True. I've set most of them to True for illustrative purposes only.
    selection = Selection(selection_type=SelectionType.REGISTERED.value, selection_match='', include_alerts=True,
                          include_device=True, include_electricity=True, include_equipment_status=True,
                          include_events=True, include_extended_runtime=True, include_house_details=True,
                          include_location=True, include_management=True, include_notification_settings=True,
                          include_oem_cfg=False, include_privacy=False, include_program=True, include_reminders=True,
                          include_runtime=True, include_security_settings=False, include_sensors=True,
                          include_settings=True, include_technician=True, include_utility=True, include_version=True,
                          include_weather=True)
    thermostat_response = ecobee_service.request_thermostats(selection)
    logger.info(thermostat_response.pretty_format())
    assert thermostat_response.status.code == 0, 'Failure while executing request_thermostats:\n{0}'.format(
        thermostat_response.pretty_format())

A successful invocation of request_thermostats() returns an EcobeeThermostatResponse instance



Update Thermostat
^^^^^^^^^^^^^^^^^

.. code-block:: python

    update_thermostat_response = ecobee_service.update_thermostats(
            selection=Selection(
                selection_type=SelectionType.REGISTERED.value,
                selection_match=''),
            thermostat=Thermostat(
                settings=Settings(
                    hvac_mode='off')),
            functions=[
                Function(
                    type='deleteVacation',
                    params={'name': 'My vacation'})])
    logger.info(update_thermostat_response.pretty_format())
    assert update_thermostat_response.status.code == 0, 'Failure while executing update_thermostats:\n{0}'.format(
        update_thermostat_response.pretty_format())

A successful invocation of update_thermostats() returns an EcobeeStatusResponse instance



Report Requests
---------------
Meter Report
^^^^^^^^^^^^

.. code-block:: python

    eastern = timezone('US/Eastern')
    meter_reports_response = ecobee_service.request_meter_reports(
            selection=Selection(
                selection_type=SelectionType.THERMOSTATS.value,
                selection_match='123456789012'),
            start_date_time=eastern.localize(datetime(2013, 4, 4, 0, 0, 0), is_dst=True),
            end_date_time=eastern.localize(datetime(2013, 4, 4, 23, 59, 0), is_dst=True))
    logger.info(meter_report_response.pretty_format())
    assert meter_report_response.status.code == 0, 'Failure while executing request_meter_reports:\n{0}'.format(
        meter_report_response.pretty_format())

A successful invocation of request_meter_reports() returns an EcobeeMeterReportsResponse instance



Runtime Report
^^^^^^^^^^^^^^

.. code-block:: python

    eastern = timezone('US/Eastern')
    runtime_report_response = ecobee_service.request_runtime_reports(
            selection=Selection(
                selection_type=SelectionType.THERMOSTATS.value,
                selection_match='123456789012'),
            start_date_time=eastern.localize(datetime(2010, 1, 1, 0, 0, 0), is_dst=False),
            end_date_time=eastern.localize(datetime(2010, 1, 2, 0, 0, 0), is_dst=False),
            columns='auxHeat1,auxHeat2,auxHeat3,compCool1,compCool2,compHeat1,compHeat2,dehumidifier,dmOffset,'
                    'economizer,fan,humidifier,hvacMode,outdoorHumidity,outdoorTemp,sky,ventilator,wind,zoneAveTemp,'
                    'zoneCalendarEvent,zoneClimate,zoneCoolTemp,zoneHeatTemp,zoneHumidity,zoneHumidityHigh,'
                    'zoneHumidityLow,zoneHvacMode,zoneOccupancy')
    logger.info(runtime_report_response.pretty_format())
    assert runtime_report_response.status.code == 0, 'Failure while executing request_runtime_reports:\n{0}'.format(
        runtime_report_response.pretty_format())

A successful invocation of request_runtime_reports() returns an EcobeeRuntimeReportsResponse instance



Group Requests
--------------
Request Groups
^^^^^^^^^^^^^^

.. code-block:: python

    group_response = ecobee_service.request_groups(
            selection=Selection(
                selection_type=SelectionType.REGISTERED.value))
    logger.info(group_response.pretty_format())
    assert group_response.status.code == 0, 'Failure while executing request_groups:\n{0}'.format(
        group_response.pretty_format())

A successful invocation of request_groups() returns an EcobeeGroupsResponse instance



Update Groups
^^^^^^^^^^^^^

.. code-block:: python

    # Create Groups
    group_response = ecobee_service.update_groups(
            selection=Selection(
                selection_type=SelectionType.REGISTERED.value),
            groups=[
                Group(
                    group_ref='3d03a26fd80001',
                    group_name='ground_floor',
                    synchronize_alerts=True,
                    synchronize_vacation=True,
                    thermostats=[
                        '123456789101']),
                Group(
                    group_ref='3bb5a91b180001',
                    group_name='first_floor',
                    synchronize_reset=True,
                    synchronize_vacation=True,
                    thermostats=[
                        '123456789102'])])
    logger.info(group_response.pretty_format())
    assert group_response.status.code == 0, 'Failure while executing update_groups:\n{0}'.format(
        group_response.pretty_format())

    # Update a Group
    group_response = ecobee_service.update_groups(
            selection=Selection(
                selection_type=SelectionType.REGISTERED.value),
            groups=[
                Group(
                    group_ref='3d03a26fd80001',
                    synchronize_system_mode=True)])
    logger.info(group_response.pretty_format())
    assert group_response.status.code == 0, 'Failure while executing update_groups:\n{0}'.format(
        group_response.pretty_format())

    # Delete a group (Set the thermostats parameter of the group to an empty list)
    group_response = ecobee_service.update_groups(
            selection=Selection(
                selection_type=SelectionType.REGISTERED.value),
            groups=[
                Group(
                    group_ref='3d03a26fd80001',
                    thermostats=[])])
    logger.info(group_response.pretty_format())
    assert group_response.status.code == 0, 'Failure while executing update_groups:\n{0}'.format(
        group_response.pretty_format())

A successful invocation of request_groups() returns an EcobeeGroupsResponse instance





Thermostat Functions
--------------------
A successful invocation of any function returns an EcobeeStatusResponse instance



Send Message
^^^^^^^^^^^^

.. code-block:: python

    update_thermostat_response = ecobee_service.send_message('Hello World')
    logger.info(update_thermostat_response.pretty_format())
    assert thermostat_response.status.code == 0, 'Failure while executing request_thermostats:\n{0}'.format(
        thermostat_response.pretty_format())

Acknowledge
^^^^^^^^^^^

.. code-block:: python

    selection = Selection(selection_type=SelectionType.REGISTERED.value, selection_match='', include_alerts=True)
    thermostat_response = ecobee_service.request_thermostats(selection)
    thermostat = thermostat_response.thermostat_list[0]
    alerts = [alert for alert in thermostat.alerts if alert.text == message]

    update_thermostat_response = ecobee_service.acknowledge(thermostat_identifier=thermostat.identifier,
                                                            ack_ref=alerts[0].acknowledge_ref,
                                                            ack_type=AckType.ACCEPT)
    logger.info(update_thermostat_response.pretty_format())
    assert update_thermostat_response.status.code == 0, 'Failure while executing acknowledge:\n{0}'.format(
        update_thermostat_response.pretty_format())

Set Hold
^^^^^^^^

.. code-block:: python

    # Simplest form
    update_thermostat_response = ecobee_service.set_hold(hold_climate_ref='away', hold_type=HoldType.NEXT_TRANSITION)
    logger.info(update_thermostat_response.pretty_format())
    assert update_thermostat_response.status.code == 0, 'Failure while executing set_hold:\n{0}'.format(
        update_thermostat_response.pretty_format())

    # Using specific start/end date and time
    eastern = timezone('US/Eastern')
    update_thermostat_response = ecobee_service.set_hold(hold_climate_ref='away',
                                                         start_date_time=eastern.localize(datetime(
                                                             2017, 5, 10, 13, 0, 0),
                                                             is_dst=True),
                                                         end_date_time=eastern.localize(datetime(
                                                             2017, 5, 10, 14, 0, 0),
                                                             is_dst=True),
                                                         hold_type=HoldType.DATE_TIME)
    logger.info(update_thermostat_response.pretty_format())
    assert update_thermostat_response.status.code == 0, 'Failure while executing set_hold:\n{0}'.format(
        update_thermostat_response.pretty_format())

    # Using duration
    eastern = timezone('US/Eastern')
    update_thermostat_response = ecobee_service.set_hold(hold_climate_ref='away',
                                                         start_date_time=eastern.localize(datetime(
                                                             2017, 5, 10, 13, 0, 0),
                                                             is_dst=True),
                                                         hold_type=HoldType.HOLD_HOURS,
                                                         hold_hours=1)
    logger.info(update_thermostat_response.pretty_format())
    assert update_thermostat_response.status.code == 0, 'Failure while executing set_hold:\n{0}'.format(
        update_thermostat_response.pretty_format())

    # Specifically the cooling temperature to use and hold indefinitely
    update_thermostat_response = ecobee_service.set_hold(cool_hold_temp=65,  hold_type=HoldType.INDEFINITE)
    logger.info(update_thermostat_response.pretty_format())
    assert update_thermostat_response.status.code == 0, 'Failure while executing set_hold:\n{0}'.format(
        update_thermostat_response.pretty_format())

    # Specifically the heating temperature to use and hold indefinitely
    update_thermostat_response = ecobee_service.set_hold(heat_hold_temp=72,  hold_type=HoldType.INDEFINITE)
    logger.info(update_thermostat_response.pretty_format())
    assert update_thermostat_response.status.code == 0, 'Failure while executing set_hold:\n{0}'.format(
        update_thermostat_response.pretty_format())

Resume Program
^^^^^^^^^^^^^^

.. code-block:: python

    update_thermostat_response = ecobee_service.resume_program(resume_all=False)
    logger.info(update_thermostat_response.pretty_format())
    assert update_thermostat_response.status.code == 0, 'Failure while executing resume_program:\n{0}'.format(
        update_thermostat_response.pretty_format())

Create Vacation
^^^^^^^^^^^^^^^

.. code-block:: python

    eastern = timezone('US/Eastern')
    update_thermostat_response = ecobee_service.create_vacation(name='Christmas Vacation!',
                                                                cool_hold_temp=104,
                                                                heat_hold_temp=59,
                                                                start_date_time=eastern.localize(datetime(
                                                                    2017, 12, 23, 10, 0, 0),
                                                                    is_dst=True),
                                                                end_date_time=eastern.localize(datetime(
                                                                    2017, 12, 28, 17, 0, 0),
                                                                    is_dst=True),
                                                                fan_mode=FanMode.AUTO,
                                                                fan_min_on_time=0)
    logger.info(update_thermostat_response.pretty_format())
    assert update_thermostat_response.status.code == 0, 'Failure while executing create_vacation:\n{0}'.format(
        update_thermostat_response.pretty_format())

Delete Vacation
^^^^^^^^^^^^^^^

.. code-block:: python

    update_thermostat_response = ecobee_service.delete_vacation(name='Christmas Vacation!')
    logger.info(update_thermostat_response.pretty_format())
    assert update_thermostat_response.status.code == 0, 'Failure while executing delete_vacation:\n{0}'.format(
        update_thermostat_response.pretty_format())

Reset Preferences
^^^^^^^^^^^^^^^^^

.. code-block:: python

    # Danger zone!!!
    update_thermostat_response = ecobee_service.reset_preferences()
    logger.info(update_thermostat_response.pretty_format())
    assert update_thermostat_response.status.code == 0, 'Failure while executing reset_preferences:\n{0}'.format(
        update_thermostat_response.pretty_format())

Persistence
===========
The ecobee API specifies that all tokens issued must be stored by the application. These tokens represent the credentials of the user and must be kept secure. A simple way is to use the Python shelve module as follows

.. code-block:: python

    import shelve
    from datetime import datetime

    import pytz
    from six.moves import input

    from pyecobee import *


    def persist_to_shelf(file_name, ecobee_service):
        pyecobee_db = shelve.open(file_name, protocol=2)
        pyecobee_db[ecobee_service.thermostat_name] = ecobee_service
        pyecobee_db.close()


    def refresh_tokens(ecobee_service):
        token_response = ecobee_service.refresh_tokens()
        logger.debug('TokenResponse returned from ecobee_service.refresh_tokens():\n{0}'.format(
            token_response.pretty_format()))

        persist_to_shelf('pyecobee_db', ecobee_service)


    def request_tokens(ecobee_service):
        token_response = ecobee_service.request_tokens()
        logger.debug('TokenResponse returned from ecobee_service.request_tokens():\n{0}'.format(
            token_response.pretty_format()))

        persist_to_shelf('pyecobee_db', ecobee_service)


    def authorize(ecobee_service):
        authorize_response = ecobee_service.authorize()
        logger.debug('AutorizeResponse returned from ecobee_service.authorize():\n{0}'.format(
            authorize_response.pretty_format()))

        persist_to_shelf('pyecobee_db', ecobee_service)

        logger.info('Please goto ecobee.com, login to the web portal and click on the settings tab. Ensure the My '
                    'Apps widget is enabled. If it is not click on the My Apps option in the menu on the left. In the '
                    'My Apps widget paste "{0}" and in the textbox labelled "Enter your 4 digit pin to '
                    'install your third party app" and then click "Install App". The next screen will display any '
                    'permissions the app requires and will ask you to click "Authorize" to add the application.\n\n'
                    'After completing this step please hit "Enter" to continue.'.format(
            authorize_response.ecobee_pin))
        input()


    if __name__ == '__main__':
        thermostat_name = 'My Thermostat'
        try:
            pyecobee_db = shelve.open('pyecobee_db', protocol=2)
            ecobee_service = pyecobee_db[thermostat_name]
        except KeyError:
            application_key = input('Please enter the API key of your ecobee App: ')
            ecobee_service = EcobeeService(thermostat_name=thermostat_name, application_key=application_key)
        finally:
            pyecobee_db.close()

        if not ecobee_service.authorization_token:
            authorize(ecobee_service)

        if not ecobee_service.access_token:
            request_tokens(ecobee_service)

        now_utc = datetime.now(pytz.utc)
        if now_utc > ecobee_service.refresh_token_expires_on:
            authorize(ecobee_service)
            request_tokens(ecobee_service)
        elif now_utc > ecobee_service.access_token_expires_on:
            token_response = ecobee_service.refresh_tokens()

        # Now make your requests :)


Tokens Refresh
==============
All access tokens must be refreshed periodically. Access tokens expire 3600 seconds (1 hour) from the time they were
refreshed. There are two patterns to refresh the access token.

Pro-active
----------
- Get the current date/time in UTC
- Compare the current date/time to the date/time on which the access and refresh token are due to expire
- Re-authorize app if the current date/time is later than the refresh token expiry date/time
- Refresh tokens if the current date/time is later than the access token expiry date/time

.. code-block:: python

        now_utc = datetime.now(pytz.utc)
        if now_utc > ecobee_service.refresh_token_expires_on:
            authorize(ecobee_service)
            request_tokens(ecobee_service)
        elif now_utc > ecobee_service.access_token_expires_on:
            token_response = ecobee_service.refresh_tokens()

Reactive
--------
The ecobee API returns status code 14 to indicate that a request was attempted using an expired access token. All
non-successful ecobee API responses are wrapped into the EcobeeApiException. The following code snippet demonstrates
how to refresh an expired access token

.. code-block:: python

        try:
            thermostat_summary_response = ecobee_service.request_thermostats_summary(selection=Selection(
            selection_type=SelectionType.REGISTERED.value,
            selection_match='',
            include_equipment_status=True))
        except EcobeeApiException as e:
            if e.status_code == 14:
                token_response = ecobee_service.refresh_tokens()

Date & Time Handling
====================
Some of the ecobee API requests expect the date and time to be in thermostat time, while others expect the date and time to be in UTC time.

Any EcobeeService method that accepts a datetime object as an argument expects the argument to be passed in thermostat time. The datetime object passed **must be a timezone aware** object.

.. code-block:: python

    import pytz
    from datetime import datetime

    from pytz import timezone

    eastern = timezone('US/Eastern')
    start_date_time=eastern.localize(datetime(2017, 5, 1, 10, 0, 0), is_dst=True) # 2017/05/01 10:00:00 -0400

The method will then either use the passed in datetime object as is, or convert it to its UTC time equivalent depending on the requirements of the ecobee API request being executed.

Exception Handling
==================
Your code should be prepared to handle the following Exceptions

- **EcobeeApiException**: Raised if a request results in an ecobee API error response
- **EcobeeAuthorizationException**: Raised if a request results in a standard or extended OAuth error response
- **EcobeeRequestsException**: Raised if a request results in an exception being raised by the underlying requests module
- **EcobeeHttpException**: Raised if a request results in any other HTTP error
