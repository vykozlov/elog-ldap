elog+ldap
====

Modification of the ELOG software in order to use LDAP authentication.

Original version of ELOG: 2.9.2-2455

Modified files: auth.c and Makefile

###Makefile:
####Add USE_LDAP parameter
```
if USE_LDAP=1 adds:
  CFLAGS += -DHAVE_LDAP
  LIBS += -lldap
```

###auth.c:

####Add on top of the file:
```
#ifdef HAVE_LDAP
#include <ldap.h>

LDAP *ldap_ld;
char ldap_login_attr[64];
char ldap_userbase[256];
char ldap_bindDN[512];
#endif  /* */
```

####Add following ldap functions:

- ldap_init(LOGBOOK *lbs, char *error_str, int error_size):
to check if elogd.cfg has been configured properly to use LDAP. Initiates connection with LDAP server

- auth_verify_password_ldap(LOGBOOK *lbs, const char *user, const char *password, char *error_str, int error_size):
to check if the user is found in the LDAP database with the right credentials

- ldap_adduser_file(LOGBOOK *lbs, const char *user, const char *password, char *error_str, int error_size):
in case "LDAP register" is configured (>0), and user is found in the LDAP database, add this user automatically to the local file if was not there, relevant info (Name, Surname, Email address) is retrieved from LDAP server

- ldap_clear():
clear some ldap parameters.


###elogd.cfg:

The following configure options are added for LDAP:

// Authentication methods are extended to have LDAP

Authentication = LDAP

// address of the LDAP server + port

LDAP server = ldap://example.org:389

// base to search for users

LDAP userbase = ou=People,dc=example,dc=org

// login attribute to form the DN (distinguished name), e.g. uid=user,ou=People,dc=example,dc=org

LDAP login attribute = uid

// flag to allow adding user info automatically from LDAP to the local file

LDAP register = 1

### Password change
is **not** possible from ELOG, i.e. a user cannot change his/her password from the ELOG form such that LDAP entry is updated.
