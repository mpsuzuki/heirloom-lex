AC_PREREQ([2.62])
AC_INIT([heirloom-lex], [0.0.20240915], [https://github.com/mpsuzuki/heirloom-lex/-/issues])
AM_INIT_AUTOMAKE([foreign])

# Set default prefix
AC_PREFIX_DEFAULT(["/usr/ccs"])


# Checks for programs.
AC_PROG_CC
AX_CHECK_COMPILE_FLAG([-m32],[
  CFLAGS="-m32 "${CFLAGS}
  AC_CFLAGS=${CFLAGS}
])
AC_SUBST([AC_CFLAGS])
AC_CHECK_SIZEOF([int])
AC_CHECK_SIZEOF([void*])
if test ${ac_cv_sizeof_int} != ${ac_cv_sizeof_voidp}
then
  AC_MSG_WARN([original lex is written for ILP32 platforms, maybe you would receive compiler errors])
fi

# Checks for AT&T yacc
AC_ARG_WITH([yacc],
AS_HELP_STRING([--with-yacc=path_prog_yacc],
  [path to "yacc" program to process AT&T lex source.
   BSD Yacc can do, but GNU Bison cannot]),
  [
    YACC="${withval}"
    ac_prefix_yacc=`dirname ${YACC}`
    ac_prefix_yacc=`cd ${ac_prefix_yacc} && pwd`
    AC_DEFINE_UNQUOTED([YACC],"${withval}",[path to yacc])
  ],[
    AC_PATH_PROGS(YACC, byacc yacc)
    ac_prefix_yacc=`dirname ${YACC}`
    AC_MSG_CHECKING([whether ${YACC} supports -V])
    if test `${YACC} -V 1>/dev/null 2>/dev/null && echo yes` = yes
    then
      AC_MSG_RESULT([yes])
      AC_MSG_CHECKING([whether ${YACC} is GNU Bison])
      if test `${YACC} -V 2>&1 | fgrep -i bison | wc -l` -gt 0
      then
        AC_MSG_RESULT([yes])
        AC_MSG_WARN([GNU Bison is known to be unable to process AT&T lex source])
      else
        AC_MSG_RESULT([no])
      fi
    fi
  ])

AC_MSG_CHECKING([whether ${YACC} is already installed and working])
cat > ac_test0.y << EOF
%token NAME NUMBER
%%
statement: NAME '=' NUMBER;
EOF
ac_cv_yacc_works=no
${YACC} ${YFLAGS} ac_test0.y 1>/dev/null 2>/dev/null && ac_cv_yacc_works=yes
if test ${ac_cv_yacc_works} = yes
then
  AC_MSG_RESULT([yes])
else
  AC_MSG_RESULT([no])
  AC_MSG_CHECKING([whether ${YACC} -P ${ac_prefix_yacc}/yaccpar works])
#  cat > ac_test1 << EOF
#ac_test1
#EOF
  rm -f y.tab.c
#  ac_cv_yacc_accept_P=no
#  ${YACC} -P ac_test1 ac_test0.y 1>/dev/null 2>/dev/null && ac_cv_yacc_accept_P=yes
  ${YACC} ${YFLAGS} -P ${ac_prefix_yacc}/yaccpar ac_test0.y 1>/dev/null 2>/dev/null && ac_cv_yacc_accept_P=yes
  if test ${ac_cv_yacc_accept_P} = yes # -a `fgrep ac_test y.tab.c | wc -l` -gt 0
  then
    #ac_cv_yacc_accept_P=yes
    AC_MSG_RESULT([yes])
    #if test -r ${ac_prefix_yacc}/yaccpar
    #then
      YFLAGS="${YFLAGS} -P ${ac_prefix_yacc}/yaccpar"
      AC_MSG_WARN([add "-P ${ac_prefix_yacc}/yaccpar" to YFLAGS])
    #fi
  else
    #ac_cv_yacc_accept_P=no
    AC_MSG_RESULT([no])
    AC_MSG_WARN([${yacc} is not working])
  fi
fi
rm -f ac_test0.y ac_test
AC_SUBST([YACC])
AC_SUBST([YFLAGS])

AC_PROG_MAKE_SET
AC_PROG_RANLIB

AC_ARG_ENABLE([form-path],[
AS_HELP_STRING([--form-path],
               [specify the default path of the C source to insert the generated C source])
  ],[
    FORMPATH=${enableval}
  ],[
    FORMPATH='$(prefix)/lib/lex'
  ])
AC_SUBST([FORMPATH])

AC_CONFIG_HEADERS([config.h])
AC_CONFIG_FILES([GNUmakefile])
AC_OUTPUT
