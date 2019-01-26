.. _git_revise:

=============
git-revise(1)
=============

.. program:: git revise:

SYNOPSIS
========

*git revise* [<options>] <target>

DESCRIPTION
===========

:program:`git revise` is a :manpage:`git(1)` subcommand to efficiently
update, split, and rearrange commits.

By default, :program:`git revise` will apply staged changes to <target>,
updating ``HEAD`` to point at the revised history. It also supports splitting
commits, rewording commit messages.

Unlike :manpage:`git-rebase(1)`, :program:`git revise` avoids modifying
working directory and index state, performing all merges in-memory, and only
writing them when necessary. This allows it to be significantly faster on
large codebases, and avoid invalidating builds.

OPTIONS
=======

General options
---------------

.. option:: -a, --all

   Stage changes to tracked files before revising.

.. option:: --no-index

   Ignore staged changes in the index.

.. option:: --reauthor

   Reset target commit's author to the current user.

.. option:: --ref <gitref>

   Working branch to update; defaults to ``HEAD``.

Main modes of operation
-----------------------

.. option:: -i, --interactive

   Rather than applying staged changes to <target>, edit a to-do list of
   actions to perform on commits after <target>. See :ref:`interactive-mode`.

.. option:: -c, --cut

   Interactively select hunks from <target>. The chosen hunks are split into
   a second commit immediately after the target.

   After splitting is complete, both commit's messages are edited.

   See the "Interactive Mode" section of :manpage:`git-add(1)` to learn how
   to operate this mode.

.. option:: -e, --edit

   After applying staged changes, edit <target>'s commit message.

.. option:: -m <msg>, --message <msg>

   Use the given <msg> as the new commit message for <target>. If multiple
   :option:`-m` options are given, their values are concatenated as separate
   paragraphs.

.. option:: --version

   Print version information and exit.


CONFLICT RESOLUTION
===================

When a conflict is encountered, :command:`git revise` will attempt to resolve
it automatically using standard git mechanisms. If automatic resolution
fails, the user will be prompted to resolve them manually.

There is currently no support for using :manpage:`git-mergetool(1)` to
resolve conflicts.

No attempt is made to detect renames of files or directories. :command:`git
revise` may produce suboptimal results across renames. Use the interactive
mode of :manpage:`git-rebase(1)` when rename tracking is important.


NOTES
=====

A successful :command:`git revise` will add a single entry to the reflog,
allowing it to be undone with ``git reset @{1}``. Unsuccessful :command:`git
revise` commands will leave your repository largely unmodified.

No marge commits may occur between the target commit and ``HEAD``, as
rewriting them is not supported.

See :manpage:`git-rebase(1)` for more information on the implications of
modifying history on a repository that you share.


.. _interactive-mode:

INTERACTIVE MODE
================

:command:`git revise` supports an interactive mode inspired by the
interactive mode of :manpage:`git-rebase(1)`.

This mode is started with the last commit you want to retain "as-is":

.. code-block:: bash

    git revise -i <after-this-commit>

An editor will be fired up with the commits in your current branch after the
given commit. If the index has any staged but uncommitted changes, a ``<git
index>`` entry will also be present.

.. code-block:: none

    pick 8338dfa88912 Oneline summary of first commit
    pick 735609912343 Summary of second commit
    index 672841329981 <git index>

These commits may be re-ordered to change the order they appear in history.
In addition, the ``pick`` and ``index`` commands may be replaced to modify
their behaviour.

.. describe:: index

   Do not commit these changes, instead leaving them staged in the index.
   Index lines must come last in the file.

   .. note:
      Commits deleted from the to-do list are treated as an index action. To
      completely discard changes, additionally use :manpage:`git-reset(1)` to
      discard the changes to the index.

.. describe:: pick

   Use the given commit as-is in history. When applied to the generated
   ``index`` entry, the commit will have the message ``<git index>``.


.. describe:: fixup

   Add the commit's changes into the previous commit, discarding it's commit
   message.

.. describe:: squash

   Like fuse, but also open an editor to merge the commit's messages.

.. describe:: reword

   Open an eitor to modify the commit message.

.. describe:: cut

   Interactively select hunks from the commit. The chosen hunks are split
   into a second commit immediately after it.

   After splitting is complete, both commit's messages are edited.

   See the "Interactive Mode" section of :manpage:`git-add(1)` to learn how
   to operate this mode.


REPORTING BUGS
==============

Please report issues and feature requests to the issue tracker at
https://github.com/mystor/git-revise/issues.

Code, documentation and other contributions are also welcomed.


SEE ALSO
========

:manpage:`git(1)`
:manpage:`git-rebase(1)`
:manpage:`git-add(1)`