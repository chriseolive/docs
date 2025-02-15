Troubleshooting Captive Portal
==============================

The best source of captive portal troubleshooting information can be found in
the |book_link|.

Issues with Apple iOS Clients
-----------------------------

If problems are encountered while accessing Captive Portal from an iPad
device, it may be due to an issue with Safari on the iPad which may be
be solved by disabling Autofill for names and passwords.

See also: https://forum.netgate.com/topic/23829/ipad-and-captive-portal-simply-hangs-on-login

Captive portal not redirecting
------------------------------

If clients are not being redirected to the portal page when attempting to browse
on an interface with captive portal enabled, it's most always one of the
following causes:

:DNS resolution not functioning: Clients on the captive portal interface must
   either be using the DNS resolver or forwarder on pfSense software, on the IP address
   of the interface where the client resides (which is the default
   configuration), or if using another IP address for DNS, it must be in an
   allowed IP address entry. If DNS fails, the browser never issues the HTTP
   request, hence it cannot be intercepted and redirected.
:Firewall rules on the captive portal interface do not allow the initial HTTP request:
   If the user is trying to browse to google.com, but HTTP connections are not
   allowed to google.com, the HTTP request will be blocked and hence cannot be
   redirected. Under **Firewall > Rules**, on the interface where captive portal
   is enabled, the traffic to be redirected must be allowed to pass. This is
   most commonly HTTP to any destination.
:The client has an HTTPS home page: The request must be to an HTTP
   site in order for the portal to redirect the client. If HTTPS is enabled for
   the portal, this may still work but it depends upon the client browser or
   operating system automatic portal detection to work.

Captive Portal Rule Generation
------------------------------

To see the ipfw rules, which includes rules for Captive Portal in
general as well as zone specific tables, run::

  # ipfw show

IPFW tables
-----------

Show all tables::

  # ipfw table all list

The **<name>_auth_up** table holds authenticated/allowed clients for a zone.
This table allow traffic from clients to enter the interface. For
example, a zone called "myzone" would contain this table::

  # ipfw table myzone_auth_up list

The **<name>_auth_down** table holds authenticated/allowed clients for a zone.
This table allow traffic to clients to exit the interface. For example, a
zone called "myzone" would contain this table::

  # ipfw table myzone_auth_down list

See **captiveportal.inc** for information on other tables, these include tables
for host/MAC bypass entries and other necessary controls.

Pipe/flowset Errors
-------------------

If an error such as the following appears::

  need a pipe/flowset/sche number Warning: Cannot modify header information -
  headers already sent by (output started at /etc/inc/captiveportal.inc:1928) in
  /etc/inc/captiveportal.inc on line 1686

The pipe allocation file, **/var/db/captiveportaldn.rules**, may have become
corrupt or otherwise broken. Remove that file and restart Captive Portal.

.. seealso:: For assistance in solving problems, post on the `Captive Portal
   category of Netgate Forum`_.

.. _Captive Portal category of Netgate Forum: https://forum.netgate.com/category/3/captive-portal
