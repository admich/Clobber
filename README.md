# Explanation
Clobber is an alternative to so-called "object prevalence", and in
particular to [cl-prevalence](https://cl-prevalence.common-lisp.dev/).

Clobber is both simpler, more flexible, and more robust than systems
based on object prevalence.

## Simplicity
It is simpler because we do not take any snapshots at all.  Other
systems typically use a combination of transaction logs and snapshots.
Clobber uses only a transaction log which is always read into an empty
system.

## Flexibility
It is more flexible because the system itself does not define the
format of a transaction.  Client code can save transactions as Lisp
lists, as instances of standard-object, or anything else that can be
serialized.  It is also more flexible because transactions can contain
any object that can be serialized, including model objects.  With this
method, client code does not need to manipulate "manually created
pointers" such as social security numbers, account numbers, membership
numbers, etc. whenever it needs to execute a transaction.  Instead it
can use the model objects themselves such as people, accounts,
automobiles and whatnot.

## Robustness
It is more robust because serialization of instances of (subclasses
of) `standard-object` is not accomplished based on slots.  Slots are
considered implementation details.  In other object prevalence systems, whenever
the model evolves, the serialization might no longer be valid.  In
contrast, Clobber serializes instances of `standard-object` as a list of
pairs, each one consisting of an initarg and a value.  These pairs can
be handled by client code in any way it sees fit.  They can be handled
by an `:initarg`, by `initialize-instance`, or they can be ignored.  The
downside of the Clobber method is that client code must specify these
pairs in the form of an initarg and the name of an accessor function
to be called to obtain the value used for the initarg.  This
inconvenience is however relatively minor, especially considering the
additional robustness it buys in terms of less sensitivity to changes
in the model classes. 

## Design
At the heart of Clobber is a mechanism for serializing objects that
preserves object identity, much like the reader macros #= and ##,
except that Clobber detects sharing within the entire transaction log,
not only within a single transaction.  This mechanism is what makes it
possible for client code to put any old object in a transaction, while
making sure that sharing is preserved.

## Examples
Two examples are included - see files [demo.lisp](demo.lisp) and [demo2.lisp](demo2.lisp)

To run `demo.lisp` -

```lisp
(ql:quickload :clobber)
(in-package :clobber-demo)
(start "/path/to/new/file")
(do-things-1)
;; inspect the file to see the transaction log
```

## License
Clobber is in the public domain in countries where it is possible to
place works in the public domain explicitly.  In other countries, we
will distribute Clobber according to a license that lets the user do
whatever he or she pleases with the code. 

## Contact
Send comments to robert.strandh@gmail.com

A manual might be written one day.

# How to use Clobber
1. Use `clobber:define-save-info` to tell Clobber how to persist your application objects.

2. Use `clobber:open-transaction-log` to create a transaction log, usually in a special variable.

3. Call `clobber:log-transaction` whenever you update your application state. You may wish to define a helper function or macro for this.

4. Close the transaction log using `clobber:close-transaction-log`.

# Reference
1. `define-save-info`
2. `open-transaction-log`
3. `close-transaction-log`
4. `log-transaction`
