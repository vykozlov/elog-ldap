elog+ldap
====

Modification of the ELOG software in order to use LDAP authentication.
Original verions of ELOG: 2.9.2-2455
Modified file: auth.c and Makefile

Makefile:
====
Add USE_LDAP parameter
```
if USE_LDAP=1 adds:
  CFLAGS += -DHAVE_LDAP
  LIBS += -lldap
```

auth.c:
====
Add in top of the file:
```
#ifdef HAVE_LDAP
#include <ldap.h>

LDAP *ldap_ld;
char ldap_login_attr[64];
char ldap_userbase[256];
char ldap_bindDN[512];
#endif  /* */
```

Add following ldap functions:

- ldap_init(LOGBOOK *lbs, char *error_str, int error_size):
to check if elogd.cfg has been configured properly to use LDAP. Initiate connection to LDAP server

- auth_verify_password_ldap(LOGBOOK *lbs, const char *user, const char *password, char *error_str, int error_size):
to check if the user is found in the LDAP database with the right credentials

- ldap_adduser_file(LOGBOOK *lbs, const char *user, const char *password, char *error_str, int error_size):
in case "LDAP register" is configured (>0), and user is found in the LDAP database, add this user to the local file if not there, relevant info (Name, Surname, Email address) is retrivied from LDAP server

- ldap_clear():
clear some ldap parameters.


elogd.cfg:
====
The following configure options are added for LDAP:

// Authentication methods are extended to have LDAP

Authentication = LDAP

// address of the LDAP server + port

LDAP server = ldap://example.org:389

// base to search for users

LDAP userbase = ou=People,dc=example,dc=org

// login attribute to form the DN (distiguished name), e.g. uid=user,ou=People,dc=example,dc=org

LDAP login attribute = uid

// flag to allow automatically adding user info from LDAP to the local file

LDAP register = 1
