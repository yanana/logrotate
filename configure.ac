AC_INIT([logrotate],[3.11.0])

dnl foreign: Do not require AUTHORS, ChangeLog, NEWS, and README to exist
dnl serial-tests: Do not hide standard output of our sequential test-suite
dnl dist-xz: Produce tar.xz version of the release archive
AM_INIT_AUTOMAKE([foreign serial-tests dist-xz])

AC_DEFINE(_GNU_SOURCE)

AC_PROG_CC
AC_PROG_CC_STDC
AC_STRUCT_ST_BLKSIZE
AC_STRUCT_ST_BLOCKS

AC_CANONICAL_HOST

dnl Use 64-bit file offsets on 32-bit systems (defines C macros if necessary)
AC_SYS_LARGEFILE

AC_CHECK_LIB([popt],[poptParseArgvString],,
  AC_MSG_ERROR([libpopt required but not found]))

dnl Needed for out-of-source builds
mkdir -p test

AC_ARG_WITH([selinux],
  [AS_HELP_STRING([--with-selinux],
    [support handling SELinux contexts (yes,no,check) @<:@default=check@:>@])],
  [],
  [with_selinux=check])
AS_CASE(["$with_selinux"],
  [yes], [AC_CHECK_LIB([selinux],[getfscreatecon_raw])],
  [no], [],
  [AC_CHECK_LIB([selinux],[getfscreatecon_raw])])
AS_IF([test "$ac_cv_lib_selinux_getfscreatecon_raw" = yes],
  echo "1" > ./test/test.SELINUX;, echo "0" > ./test/test.SELINUX;)

AC_ARG_WITH([acl],
  [AS_HELP_STRING([--with-acl],
    [support handling ACL (yes,no,check) @<:@default=check@:>@])],
  [],
  [with_acl=check])
AS_CASE(["$with_acl"],
  [yes], [AC_CHECK_LIB([acl],[acl_get_file])],
  [no], [],
  [AC_CHECK_LIB([acl],[acl_get_file])])
AS_IF([test "$ac_cv_lib_acl_acl_get_file" = yes],
  echo "1" > ./test/test.ACL;, echo "0" > ./test/test.ACL;)

DEFAULT_MAIL_COMMAND="/bin/mail"
COMPRESS_COMMAND="/bin/gzip"
UNCOMPRESS_COMMAND="/bin/gunzip"
COMPRESS_EXT=".gz"
STATE_FILE_PATH="/var/lib/logrotate.status"

case "$host" in
  *-hpux*)
          DEFAULT_MAIL_COMMAND="/usr/bin/mailx"
          COMPRESS_COMMAND="/usr/contrib/bin/gzip"
          UNCOMPRESS_COMMAND="/usr/contrib/bin/gunzip"
          ;;
  *-solaris*|*-sunos*)
          DEFAULT_MAIL_COMMAND="/usr/bin/mailx"
          ;;
  *-netbsd*)
          DEFAULT_MAIL_COMMAND="/usr/bin/mail -s"
          COMPRESS_COMMAND="/usr/bin/gzip"
          UNCOMPRESS_COMMAND="/usr/bin/gunzip"
          ;;
  *-darwin*)
          COMPRESS_COMMAND="/usr/bin/gzip"
          UNCOMPRESS_COMMAND="/usr/bin/gunzip"
          ;;
esac

AC_ARG_WITH([state-file-path],
  AC_HELP_STRING([--with-state-file-path=PATH],
                 [path to state file (/var/lib/logrotate.status by default)]),
  [
    case "$withval" in
      yes|no)
        AC_MSG_ERROR([--with-state-file-path=PATH requires a path to be specified])
        ;;
      *)
        STATE_FILE_PATH="$withval"
        ;;
    esac
  ])
AC_DEFINE_UNQUOTED([STATEFILE], "$STATE_FILE_PATH")
AC_SUBST(STATE_FILE_PATH)

AC_ARG_WITH([default-mail-command],
  AC_HELP_STRING([--with-default-mail-command=COMMAND],
                 [default mail command (e.g. /bin/mail -s)]),
  [
    case "$withval" in
      yes|no)
        AC_MSG_ERROR([--with-default-mail-command=COMMAND requires a command to be specified])
        ;;
      *)
        DEFAULT_MAIL_COMMAND="$withval"
        ;;
    esac
  ])
AC_DEFINE_UNQUOTED([DEFAULT_MAIL_COMMAND], "$DEFAULT_MAIL_COMMAND")
AC_SUBST(DEFAULT_MAIL_COMMAND)

AC_ARG_WITH([compress-command],
  AC_HELP_STRING([--with-compress-command=COMMAND],
                 [compress command (default: /bin/gzip)]),
  [
    case "$withval" in
      yes|no)
        AC_MSG_ERROR([--with-compress-command=COMMAND requires a command to be specified])
        ;;
      *)
        COMPRESS_COMMAND="$withval"
        ;;
    esac
  ])
AC_DEFINE_UNQUOTED([COMPRESS_COMMAND], "$COMPRESS_COMMAND")
AC_SUBST(COMPRESS_COMMAND)

AC_ARG_WITH([uncompress-command],
  AC_HELP_STRING([--with-uncompress-command=COMMAND],
                 [uncompress command (default: /bin/gunzip)]),
  [
    case "$withval" in
      yes|no)
        AC_MSG_ERROR([--with-uncompress-command=COMMAND requires a command to be specified])
        ;;
      *)
        UNCOMPRESS_COMMAND="$withval"
        ;;
    esac
  ])
AC_DEFINE_UNQUOTED([UNCOMPRESS_COMMAND], "$UNCOMPRESS_COMMAND")
AC_SUBST(UNCOMPRESS_COMMAND)

AC_ARG_WITH([compress-extension],
  AC_HELP_STRING([--with-compress-extension=EXTENSION],
                 [compress extension (default: .gz)]),
  [
    case "$withval" in
      .*)
        COMPRESS_EXT="$withval"
        ;;
      *)
        AC_MSG_ERROR([--with-compress-extension=EXTENSION must start with .])
        ;;
    esac
  ])
AC_DEFINE_UNQUOTED([COMPRESS_EXT], "$COMPRESS_EXT")
AC_SUBST(COMPRESS_EXT)

AC_DEFINE_UNQUOTED([ROOT_UID], 0)
AC_SUBST(ROOT_UID)

AC_CHECK_FUNCS([asprintf fork madvise qsort strndup strptime vfork vsyslog])

AC_CONFIG_FILES([Makefile test/Makefile logrotate.8 logrotate.spec])
AC_OUTPUT

AC_MSG_RESULT([
  ${PACKAGE_NAME} ${VERSION}

  default mail command:   ${DEFAULT_MAIL_COMMAND}
  compress command:       ${COMPRESS_COMMAND}
  uncompress command:     ${UNCOMPRESS_COMMAND}
  compress extension:     ${COMPRESS_EXT}
  statefile path:         ${STATE_FILE_PATH}
])
