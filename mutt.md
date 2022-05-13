T: 给符合 pattern 的邮件加上 tag

MUTT(1)                               User Manuals                              MUTT(1)

NAME
       mutt - The Mutt Mail User Agent

SYNOPSIS
       mutt  [-nRyzZ] [-e command] [-F rcfile] [-m type] [-f mailbox]

       mutt  [-Enx] [-e command] [-F rcfile] [-H draft] [-i include]
             [-b bcc-addr] [-c cc-addr] [-s subject]
             [-a file ... --] to-addr ...

       mutt  [-nx] [-e command] [-F rcfile] [-b bcc-addr] [-c cc-addr]
             [-s subject] [-a file ... --] to-addr ... < message

       mutt  [-n] [-e command] [-F rcfile] -p

       mutt  [-n] [-e command] [-F rcfile] -A alias

       mutt  [-n] [-e command] [-F rcfile] -Q variable

       mutt  -v[v]

       mutt  -D

DESCRIPTION
       Mutt  is  a  small  but very powerful text based program for reading and sending
       electronic mail under unix operating systems, including support for color termi‐
       nals, MIME, OpenPGP, and a threaded sorting mode.

       Note:  This  manual  page  gives a brief overview of the mutt executable command
       line options.  A copy of the full manual is located in  /usr/share/doc/mutt,  in
       text,  HTML,  and/or PDF format.  Please refer to the manual to learn how to use
       and configure Mutt.

OPTIONS
       -A alias
              Print an expanded version of the given alias and exit.

       -a file ...
              Attach a file using MIME.  Separating file  and  to-addr  arguments  with
              “--” is mandatory.  For example:

                  mutt -a image.jpg -- to-addr
                  mutt -a img.jpg *.png -- to-addr-1 to-addr-2

              The -a option must be placed at the end of command line options.

       -b bcc-addr
              Specify a blind carbon copy (BCC) address.

       -c cc-addr
              Specify a carbon copy (CC) address.

       -d level
              If  Mutt  was compiled with +DEBUG log debugging output to ~/.muttdebug0.
              Level can range from -5 to 5 and affects verbosity. A value of zero  dis‐
              ables debugging. A value less than zero disables automatic log file rota‐
              tion; the log level is then its absolute value. A value of 2 (-2) is rec‐
              ommended for most diagnosis.

       -D     Print the value of all configuration options to stdout.

       -E     Edit  the draft file specified by -H or include file specified by -i dur‐
              ing message composition.

       -e command
              Specify a configuration command to be run after processing of initializa‐
              tion files.

       -f mailbox
              Specify a mailbox to load.

       -F rcfile
              Use rcfile instead of the user configuration file.

       -h     Display a short option summary and exit.

       -H draft
              Specify a draft file which contains header and body to use to send a mes‐
              sage.  If draft is “-”, then data is read from stdin.  The draft file  is
              expected  to contain just an RFC822 email — headers and a body.  Although
              it is not an mbox file, if an mbox "From " line is present,  it  will  be
              silently  discarded.   Draft  files are processed the same in interactive
              and batch mode; they are not passed through untouched.  For example,  en‐
              crypted draft files will be decrypted.

       -i include
              Specify  an  include file to be inserted into the body of a message.  Ig‐
              nored if -H is set.  If include is “-”, then data is read from stdin.

       -m type
              Specify a default mailbox type for newly created folders.  Can be one  of
              the  following:  mbox,  MMDF,  MH or Maildir.  See also $mbox_type in the
              manual.

       -n     Do not read the system-wide Muttrc configuration file.

       -p     Resume a postponed message.  Exit immediately if there are  no  postponed
              messages.

       -Q variable
              Query a configuration variable.  The query is performed after all config‐
              uration files have been parsed, and any commands  given  on  the  command
              line have been executed.

       -R     Open a mailbox in read-only mode.

       -s subject
              Specify  the  subject  of  the message.  Must be enclosed in quotes if it
              contains spaces.

       -v     Display the Mutt version number and compile-time definitions.

       -vv    Display license and copyright information.

       -x     Emulate the mailx(1) compose mode.

       -y     Start Mutt with a listing of all mailboxes  specified  by  the  mailboxes
              configuration command.

       -z     Exit  immediately with code 1 if mailbox specified by -f does not contain
              any messages.

       -Z     Open the first mailbox specified by the mailboxes  configuration  command
              which contains new mail.  Exit immediately with code 1 if there is no new
              mail in any of them.

       --     Treat remaining arguments as to-addr even if they start with a dash.  See
              also -a above.  To-addr can be a local or network mail address as well as
              mailto: URL.

ENVIRONMENT
       EDITOR, VISUAL
              Specifies the editor to use when composing messages.  If both EDITOR  and
              VISUAL  are  set,  VISUAL takes precedence.  If neither EDITOR nor VISUAL
              are set, the default is vi(1).

       EGDSOCKET, RANDFILE
              Paths used to initialize the random engine for SSL library.

       EMAIL  The user's e-mail address.

       HOME   Full path of the user's home directory.

       MAIL   Full path of the user's spool mailbox.

       MAILDIR
              Full path of the user's spool mailbox if MAIL is  unset.   Commonly  used
              when the spool mailbox is a maildir(5) folder.

       MAILCAPS
              Path to search for mailcap files.

       MM_NOASK
              If this variable is set, mailcap are always used without prompting first.

       PGPPATH
              Directory in which the user's PGP public keyring can be found.  When used
              with the original PGP program, mutt and mutt_pgpring(1) rely on this  be‐
              ing set.

       REPLYTO
              Default Reply-To address.

       TMPDIR Directory  in which temporary files are created.  If unset, /tmp is used.
              See also $tmpdir configuration variable.

       LC_ALL, LC_CTYPE, LANG
              Used to determine charset and locale to use.

       TEXTDOMAINDIR
              Directory containing translation files.  If set, this path overwrite  the
              Mutt installation directory.  Used for testing translation changes.

FILES
       ~/.muttrc
       ~/.mutt/muttrc
       $XDG_CONFIG_HOME/mutt/muttrc
              User configuration files.

       /etc/Muttrc or /usr/share/mutt/Muttrc
              System-wide configuration file.

       /tmp/muttXXXXXX
              Temporary files created by Mutt.

       ~/.muttdebug0
              File containing debugging output.  Log files are automatically rotated by
              mutt changing the number at the end.  See -d option above.

       ~/.mailcap
              User definition for handling non-text MIME types.

       /etc/mailcap
              System definition for handling non-text MIME types.

       ~/.mime.types
              User's personal mapping between MIME types and file extensions.

       /etc/mime.types
              System mapping between MIME types and file extensions.

       /usr/bin/mutt_dotlock
              The privileged dotlocking program.

       /usr/share/doc/mutt/manual.txt
              The Mutt manual.

BUGS
       None.  Mutts have fleas, not bugs.

FLEAS
       Suspend/resume while editing a file with an external editor does not work  under
       SunOS  4.x if you use the curses lib in /usr/5lib.  It does work with the S-Lang
       library, however.

       Resizing the screen while using an external pager causes Mutt to go  haywire  on
       some systems.

       Suspend/resume does not work under Ultrix.

       The  help  line for the index menu is not updated if you change the bindings for
       one of the functions listed while Mutt is running.

       For a more up-to-date list of bugs, errm, fleas, please visit the mutt project's
       bug tracking system under https://gitlab.com/muttmua/mutt/issues.

NO WARRANTIES
       This  program is distributed in the hope that it will be useful, but WITHOUT ANY
       WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR  A
       PARTICULAR PURPOSE.  See the GNU General Public License for more details.

SEE ALSO
       mutt_dotlock(1),    mutt_pgpring(1),    pgpewrap(1),    sendmail(1),   smail(1),
       smime_keys(1), curses(3), ncurses(3), mailcap(5), maildir(5), mbox(5),  mmdf(5),
       muttrc(5)

       Mutt Home Page: http://www.mutt.org/

       The Mutt manual

       RFC5322  — Internet Message Format: https://tools.ietf.org/rfcmarkup/5322 (obso‐
       letes RFC2822 and RFC822)

AUTHOR
       Michael Elkins, and others.  Use <mutt-dev@mutt.org> to contact the developers.

Unix                                 July 24, 2020                              MUTT(1)
