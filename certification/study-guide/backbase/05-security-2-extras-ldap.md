LDAP
=================

The **Lightweight Directory Access Protocol** (**LDAP**) is an open, vendor-neutral, industry standard application protocol for accessing and maintaining distributed directory information services over an Internet Protocol (IP) network.

A common use of LDAP is to provide a central place to store usernames and passwords. This allows many different applications and services to connect to the LDAP server to validate users.

LDAP is based on a simpler subset of the standards contained within the X.500 standard. Because of this relationship, LDAP is sometimes called X.500-lite.

Directory structure
-------------------

The protocol provides an interface with directories that follow the 1993 edition of the X.500 model:

*   An entry consists of a set of attributes.
*   An attribute has a name (an _attribute type_ or _attribute description_) and one or more values. The attributes are defined in a _schema_ (see below).
*   Each entry has a unique identifier: its _Distinguished Name_ (DN). This consists of its _Relative Distinguished Name_ (RDN), constructed from some attribute(s) in the entry, followed by the parent entry's DN. Think of the DN as the full file path and the RDN as its relative filename in its parent folder (e.g. if `/foo/bar/myfile.txt` were the DN, then `myfile.txt` would be the RDN).

A DN may change over the lifetime of the entry, for instance, when entries are moved within a tree. To reliably and unambiguously identify entries, a UUID might be provided in the set of the entry's _operational attributes_.

An entry can look like this when represented in LDAP Data Interchange Format (LDIF) (LDAP itself is a binary protocol):

    dn: cn=John Doe,dc=example,dc=com
    cn: John Doe
    givenName: John
    sn: Doe
    telephoneNumber: +1 888 555 6789
    telephoneNumber: +1 888 555 1232
    mail: john@example.com
    manager: cn=Barbara Doe,dc=example,dc=com
    objectClass: inetOrgPerson
    objectClass: organizationalPerson
    objectClass: person
    objectClass: top

"`dn`" is the distinguished name of the entry; it is neither an attribute nor a part of the entry. "`cn=John Doe`" is the entry's RDN (Relative Distinguished Name), and "`dc=example,dc=com`" is the DN of the parent entry, where "`dc`" denotes 'Domain Component'. The other lines show the attributes in the entry. Attribute names are typically mnemonic strings, like "`cn`" for common name, "`dc`" for domain component, "`mail`" for e-mail address, and "`sn`" for surname.

A server holds a subtree starting from a specific entry, e.g. "`dc=example,dc=com`" and its children. Servers may also hold references to other servers, so an attempt to access "`ou=department,dc=example,dc=com`" could return a _referral_ or _continuation reference_ to a server that holds that part of the directory tree. The client can then contact the other server. Some servers also support _chaining_, which means the server contacts the other server and returns the results to the client.

LDAP rarely defines any ordering: The server may return the values of an attribute, the attributes in an entry, and the entries found by a search operation in any order. This follows from the formal definitions - an entry is defined as a set of attributes, and an attribute is a set of values, and sets need not be ordered.

Operations
----------

### Add

The ADD operation inserts a new entry into the directory-server database. If the distinguished name in the add request already exists in the directory, then the server will not add a duplicate entry but will set the result code in the add result to decimal 68, "entryAlreadyExists".

*   LDAP-compliant servers will never dereference the distinguished name transmitted in the add request when attempting to locate the entry, that is, distinguished names are never de-aliased.
*   LDAP-compliant servers will ensure that the distinguished name and all attributes conform to naming standards.
*   The entry to be added must not exist, and the immediate superior must exist.


    dn: uid=user,ou=people,dc=example,dc=com
    changetype: add
    objectClass:top
    objectClass:person
    uid: user
    sn: last-name
    cn: common-name
    userPassword: password

In the above example, `uid=user,ou=people,dc=example,dc=com` must not exist, and `ou=people,dc=example,dc=com` must exist.

### Bind (authenticate)

When an LDAP session is created, that is, when an LDAP client connects to the server, the **authentication state** of the session is set to anonymous. The BIND operation establishes the authentication state for a session.

### Delete

To delete an entry, an LDAP client transmits a properly formed delete request to the server.

*   A delete request must contain the distinguished name of the entry to be deleted
*   Request controls may also be attached to the delete request
*   Servers do not dereference aliases when processing a delete request
*   Only leaf entries (entries with no subordinates) may be deleted by a delete request. Some servers support an operational attribute `hasSubordinates` whose value indicates whether an entry has any subordinate entries, and some servers support an operational attribute `numSubordinates`[\[15\]](#cite_note-15) indicating the number of entries subordinate to the entry containing the `numSubordinates` attribute.
*   Some servers support the subtree delete request control permitting deletion of the DN and all objects subordinate to the DN, subject to access controls. Delete requests are subject to access controls, that is, whether a connection with a given authentication state will be permitted to delete a given entry is governed by server-specific access control mechanisms.

### Search and compare

The Search operation is used to both search for and read entries. Its parameters are:

###### baseObject

The name of the base object entry (or possibly the root) relative to which the search is to be performed.

###### scope

What elements below the baseObject to search. This can be `BaseObject` (search just the named entry, typically used to read one entry), `singleLevel` (entries immediately below the base DN), or `wholeSubtree` (the entire subtree starting at the base DN).

###### filter

Criteria to use in selecting elements within scope. For example, the filter `(&(objectClass=person)(|(givenName=John)(mail=john*)))` will select "persons" (elements of objectClass `person`) where the matching rules for `givenName` and `mail` determine whether the values for those attributes match the filter assertion. Note that a common misconception is that LDAP data is case-sensitive, whereas in fact matching rules and ordering rules determine matching, comparisons, and relative value relationships. If the example filters were required to match the case of the attribute value, an _extensible match filter_ must be used, for example, `(&(objectClass=person)(|(givenName:caseExactMatch:=John)(mail:caseExactSubstringsMatch:=john*)))`

###### derefAliases

Whether and how to follow alias entries (entries that refer to other entries),

###### attributes

Which attributes to return in result entries.

###### sizeLimit, timeLimit

Maximum number of entries to return, and maximum time to allow search to run. These values, however, cannot override any restrictions the server places on size limit and time limit.

###### typesOnly

Return attribute types only, not attribute values.

The server returns the matching entries and potentially continuation references. These may be returned in any order. The final result will include the result code.

The Compare operation takes a DN, an attribute name and an attribute value, and checks if the named entry contains that attribute with that value.

### Modify

The MODIFY operation is used by LDAP clients to request that the LDAP server make changes to existing entries. Attempts to modify entries that do not exist will fail. MODIFY requests are subject to access controls as implemented by the server.

The MODIFY operation requires that the distinguished name (DN) of the entry be specified, and a sequence of changes. Each change in the sequence must be one of:

*   add (add a new value, which must not already exist in the attribute)
*   delete (delete an existing value)
*   replace (replace an existing value with a new value)

LDIF example of adding a value to an attribute:

dn: dc=example,dc=com
changetype: modify
add: cn
cn: the-new-cn-value-to-be-added
-

To replace the value of an existing attribute, use the `replace` keyword. If the attribute is multi-valued, the client must specify the value of the attribute to update.

To delete an attribute from an entry, use the keyword `delete` and the changetype designator `modify`. If the attribute is multi-valued, the client must specify the value of the attribute to delete.

### Modify DN

Modify DN (move/rename entry) takes the new RDN (Relative Distinguished Name), optionally the new parent's DN, and a flag that indicates whether to delete the value(s) in the entry that match the old RDN. The server may support renaming of entire directory subtrees.

URI scheme
--------------------------------------------------------------------------------------------------------------------------------

An LDAP uniform resource identifier (URI) scheme exists, which clients support in varying degrees, and servers return in referrals and continuation references (see RFC 4516):

ldap://host:port/DN?attributes?scope?filter?extensions

Most of the components described below are optional.

*   _host_ is the [FQDN](/wiki/Fully_qualified_domain_name "Fully qualified domain name") or IP address of the LDAP server to search.
*   _port_ is the [network port](/wiki/Network_port "Network port") (default port 389) of the LDAP server.
*   _DN_ is the distinguished name to use as the search base.
*   _attributes_ is a comma-separated list of attributes to retrieve.
*   _scope_ specifies the search scope and can be "base" (the default), "one" or "sub".
*   _filter_ is a search filter. For example, `(objectClass=*)` as defined in RFC 4515.
*   _extensions_ are extensions to the LDAP URL format.

For example, "`ldap://ldap.example.com/cn=John%20Doe,dc=example,dc=com`" refers to all user attributes in John Doe's entry in `ldap.example.com`, while "`ldap:///dc=example,dc=com??sub?(givenName=John)`" searches for the entry in the default server (note the triple slash, omitting the host, and the double question mark, omitting the attributes). As in other URLs, special characters must be [percent-encoded](/wiki/Percent-encoding "Percent-encoding").

There is a similar non-standard `ldaps` URI scheme for LDAP over SSL. This should not be confused with LDAP with TLS, which is achieved using the StartTLS operation using the standard `ldap` scheme.
