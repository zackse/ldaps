# ldaps

simple wrapper around ldapsearch(1) for easy command-line AD/LDAP queries

description
-----------

This script wraps the OpenLDAP [ldapsearch(1)](http://www.openldap.org/software/man.cgi?query=ldapsearch&apropos=0&sektion=0&manpath=OpenLDAP+2.0-Release&format=html) utility, allowing simple command-line AD/LDAP queries without having to remember the various arguments/options to `ldapsearch`.

usage
-----

```bash
ldaps <filter> [ attribute1, ... ]
```

This will search your configured LDAP server (see **installation** below) using the given filter and will return the specified attributes. If no attributes are specified, all attributes are returned.

To handle the common case of searching by Active Directory username, specifying a single word will be treated as a search against the `sAMAccountName` field. For example, the following command would search using the LDAP filter `(sAMAccountName=zackse)` and return the `displayName` attribute.

```bash
ldaps zackse displayName
```

requirements
------------

* OpenLDAP clients
  * Debian/Ubuntu: `ldap-utils`
  * Red Hat/CentOS/Fedora: `openldap-clients`
  * OS X: installed by default
* Perl

tab-completion
--------------

If you install the bash completion script (see **installation** below), you can tab-complete usernames and attributes. For example, this would provide completions for usernames starting with `zack`:

```bash
ldaps zack<TAB>
```

If you are searching on a username, you can tab-complete attribute names:

```bash
$ ldaps zackse sam<TAB>
samaccountname  samaccounttype
```

examples
--------

Find a mobile number:

```bash
ldaps zackse mobile
```

Create a shell function to display members in the specified group:

```bash
members() {
    ldaps "$1" | grep ^member: | cut -c9-
}
```

Now you can issue `members engineering_team` to get a list of people in that
group.

Search for Jack Tors:

```bash
ldaps 'cn=jack tors'
```

installation
------------

Copy ldaps into a directory in your PATH. For example:

```bash
cp ldaps ~/bin/
chmod 755 ~/bin/ldaps
```

Create a config file:

```bash
mkdir -p ~/.config/ldaps
cp sample_rc ~/.config/ldaps/rc
chmod 600 ~/.config/ldaps/rc
$EDITOR ~/.config/ldaps/rc
...

```

Optional: add tab completion!

```bash
cp ldaps_completion /etc/bash_completion.d/
source /etc/bash_completion.d/ldaps_completion
```

a note on security
------------------

This script requires credentials for an account with read access to your AD/LDAP server. If you do not want to save the credentials in a config file (`~/.config/ldaps/rc`), you can set them in your environment. This isn't necessarily more secure, but it is an alternate approach if you don't want the credentials in persistent storage.

For example, if you prefer to omit the password from the config file, you could set it in your environment and make it available to `ldaps`:

```bash
read -s LDAP_BIND_PW
(type in your password here, it will not be displayed while you are typing)

export LDAP_BIND_PW
```
