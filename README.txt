//$Id$

NCSU Roles module v7.x-1.2
==========================
Updated to Drupal 7 by njyoung at ncsu.edu
Drupal 6 module by brabec at ncsu.edu

DESCRIPTION
-----------
This module automatically populates Drupal User Roles using various 
NCSU group sources. 

PREREQUISITES
-------------
You should be running wraplogin for your primary user authentication. 
This module assumes that WRAP users will be named in the database as 
"unityid.ncsu.edu", which is the standard used by the wraplogin module. 
All of the group memberships that are used are based on Unity accounts.

INSTALLATION
------------
Place the entire ncsuroles module folder into your third-party modules
directory, typically at sites/all/modules.

Enable the module in Drupal by going to 
    <site-root>admin/config/people/ncsuroles

Set up ncsuroles.module by going to 
    Configure --> People --> Roles at NCSU

CONFIGURATION
-------------
Create a new role using People >> Permissions >> Roles
    - select the name for your new role and click "Add Role"

Once created, click on the "edit role" link next to the new role.

On the edit form:
    - check the Enable NCSU Role source box
    - select the Source Type (see below)
    - enter the Group Key (see below) for the source group
    - click "Save role"

If your key checks out, the role will be added to the database. 
The next time the cron update runs, your role will be populated
with any of the member users who already have Drupal login accounts.

If a user signs in to WRAP at a later date, the module will add their
roles at that time, rather than waiting for the next cron run.

GROUP TYPES
-----------

AFS PTS Groups
    - this type can include any group defined in our PTS
    - the default AFS cell is "unity"
    - the format of the key is "owner:group@cell"
    - for example, group "cccadm" in the default cell is "cccadm@unity"
    - a personal group named "test" owned by me would be "brabec:test@unity"

Hesiod Groups
    - this type can include any of the groups reported by the 'hes'
      command on a unix machine
    - the group key is just the hesiod group name
    - for example, "hes brabec grplist" returns:
        ncsu_staff:324:ncsu:108:cccadm:110:cc-staff:320
      so we could use "ncsu_staff", "ncsu", "cccadm", or "cc-staff"

LDAP Groups
    - these are fake groups that are populated using student and employee 
      information from ldap.ncsu.edu datasets
    - the following groups are provided (case sensitive):
        - primary_role_E or primary_role_S = employee or student
        - emp_status_X = where X is their emp_status code as seen in ldap
        - employee     = given if X is one of the "active" codes [ALPQR]
        - ouc_123456   = when the user is an employee in OUC=123456
        - ouc4_1234    = when the user is an employee of the OUC prefix 1234
        - ouc2_12      = when the user is an employee of the OUC prefix 12
        - class_code_XX  = where XX is their class_code (e.g. FR, SO, GR...)
        - student        = given if they have XX defined at all
        - curr_code_XXX  = where XXX is the curriculum code of their major
    - for example, this user is an employee of OIT-ISO, and is a 
      member of these groups:
          'primary_role_E',
          'ouc_512001',
          'ouc4_5120',
          'ouc2_51',
          'employee',
          'emp_status_A'

SysNews Groups
    - this type can include any of the SysNews SysTools permissions groups
    - the group key is the "groupid" in SysTools, which is usually
      a short acronym ID.
    - all of the OIT organizational groups are available from SysNews:
      e.g. "oit", "oit-iso", "oit-iso-shs", etc.

UPDATES
-------
The cron update runs every time that cron.php is called for your installation.
However, this module has a built-in refresh period that prevents cron from 
polling all the groups too often. The default polling period is every 
24 hours, but that can be reduced to every 4 or 8 hours on the 
module configuration page.
