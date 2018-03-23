project('curl', 'c')

if target_machine.system() != 'windows' and target_machine.system() != 'linux'
  error('Only linux and windows supported')
endif

cc = meson.get_compiler('c')

checked_flags = [
  '-pedantic', 
  '-Wall', 
  '-W', 
  '-Wpointer-arith',
  '-Wwrite-strings',
  '-Wunused',
  '-Wshadow',
  '-Winline',
  '-Wnested-externs',
  '-Wmissing-declarations',
  '-Wmissing-prototypes',
  '-Wno-long-long',
  '-Wfloat-equal',
  '-Wno-multichar',
  '-Wsign-compare',
  '-Wundef',
  '-Wno-format-nonliteral',
  '-Wendif-labels',
  '-Wstrict-prototypes',
  '-Wdeclaration-after-statement',
  '-Wstrict-aliasing=3',
  '-Wcast-align',
  '-Wtype-limits',
  '-Wold-style-declaration',
  '-Wmissing-parameter-type',
  '-Wempty-body',
  '-Wclobbered',
  '-Wignored-qualifiers',
  '-Wconversion',
  '-Wno-sign-conversion',
  '-Wvla',
  '-Wdouble-promotion',
  '-Wno-system-headers'
]

cflags = []

if cc.get_id() == 'clang' or cc.get_id() == 'gcc'
  foreach flag : checked_flags
    if cc.has_argument(flag)
      cflags += flag
    endif
  endforeach
endif

cflags += '-DBUILDING_LIBCURL'

if target_machine.system() == 'windows'
  cflags += '-D_WINSOCKAPI_='
endif

cfg = configuration_data()
cfg.set_quoted('OS', target_machine.system())

#TODO: add options
cfg.set('CURL_DISABLE_FTP', false)
cfg.set('CURL_DISABLE_LDAP', true)
cfg.set('CURL_DISABLE_LDAPS', true)
cfg.set('CURL_DISABLE_TELNET', true)
cfg.set('CURL_DISABLE_DICT', true)
cfg.set('CURL_DISABLE_FILE', true)
cfg.set('CURL_DISABLE_TFTP', true)
cfg.set('CURL_DISABLE_RTSP', true)
cfg.set('CURL_DISABLE_POP3', true)
cfg.set('CURL_DISABLE_IMAP', true)
cfg.set('CURL_DISABLE_SMTP', true)
cfg.set('CURL_DISABLE_GOPHER', true)

libs = []

lib_defs = [
  'dl',
  'socket',
  'bind',
  'bnetapi',
  'ws2_32',
  'winmm',
]

foreach ld : lib_defs
  l = cc.find_library(ld, required : false)
  if l.found()
    libs += l
    cfg.set('HAVE_' + ld.to_upper(), true)
  endif
endforeach

if cc.has_member('struct sockaddr_un', 'sun_path', prefix : '#include<sys/un.h>')
  cfg.set('USE_UNIX_SOCKETS', true)
endif

inc_files = [
  'stdio.h',
  'inttypes.h',
  'sys/filio.h',
  'sys/ioctl.h',
  'sys/param.h',
  'sys/poll.h',
  'sys/resource.h',
  'sys/select.h',
  'sys/socket.h',
  'sys/sockio.h',
  'sys/stat.h',
  'sys/time.h',
  'sys/types.h',
  'sys/uio.h',
  'sys/un.h',
  'sys/utime.h',
  'sys/xattr.h',
  'alloca.h',
  'arpa/inet.h',
  'arpa/tftp.h',
  'assert.h',
  'crypto.h',
  'des.h',
  'err.h',
  'errno.h',
  'fcntl.h',
  'idn2.h',
  'ifaddrs.h',
  'io.h',
  'krb.h',
  'libgen.h',
  'locale.h',
  'net/if.h',
  'netdb.h',
  'netinet/in.h',
  'netinet/tcp.h',
  'pem.h',
  'poll.h',
  'pwd.h',
  'rsa.h',
  'setjmp.h',
  'sgtty.h',
  'signal.h',
  'ssl.h',
  'stdbool.h',
  'stdint.h',
  'stdio.h',
  'stdlib.h',
  'string.h',
  'strings.h',
  'stropts.h',
  'termio.h',
  'termios.h',
  'time.h',
  'unistd.h',
  'utime.h',
  'x509.h',
  'process.h',
  'stddef.h',
  'dlfcn.h',
  'malloc.h',
  'memory.h',
  'netinet/if_ether.h',
  'stdint.h',
  'sockio.h',
  'sys/utsname.h',
]

if target_machine.system() == 'windows'
  inc_files += ['windows.h', 'winsock.h', 'ws2tcpip.h', 'winsock2.h']
endif

foreach i : inc_files
  if cc.has_header(i)
    cfg.set('HAVE_' + i.to_upper().underscorify(), true)
  endif
endforeach

size_defs = [
  'size_t',
  'ssize_t',
  'long long',
  'long',
  'short',
  'int',
  '__int64',
  'long double',
  'time_t',
]

foreach s : size_defs
  size = cc.sizeof(s, prefix : '#include<time.h>')
  if size > 0
    cfg.set('HAVE_SIZEOF_' + '_'.join(s.split()).to_upper(), true)
    cfg.set('SIZEOF_' + s.to_upper(), size)
  endif
endforeach

if not cfg.has('HAVE_SIZEOF_SSIZE_T')
  if cfg.get('SIZEOF_LONG') == cfg.get('SIZEOF_SIZE_T')
    cfg.set('ssize_t', 'long')
  elif cfg.has('SIZEOF___INT64') and (cfg.get('SIZEOF___INT64') == cfg.get('SIZEOF_SIZE_T'))
    cfg.set('ssize_t', '__int64')
  endif
endif

if cfg.has('HAVE_SIZEOF_LONG_LONG')
  cfg.set('HAVE_LONGLONG', true)
  cfg.set('HAVE_LL', true)
endif

syms = [
  'basename',
  'socket',
  'select',
  'strdup',
  'strstr',
  'strtok_r',
  'strftime',
  'uname',
  'strcasecmp',
  'stricmp',
  'strcmpi',
  'strncmpi',
  'alarm',
  'gethostbyaddr',
  'gethostbyaddr_r',
  'gettimeofday',
  'inet_addr',
  'inet_ntoa',
  'inet_ntoa_r',
  'tcsetattr',
  'tcgetattr',
  'perror',
  'closesocket',
  'setvbuf',
  'sigsetjmp',
  'getpass_r',
  'strlcat',
  'getpwuid',
  'geteuid',
  'utime',
  'gmtime_r',
  'localtime_r',
  'gethostbyname',
  'gethostbyname_r',
  'signal',
  'SIGALRM',
  'uname',
  'strtoll',
  '_strtoi64',
  'strerror_r',
  'siginterrupt',
  'perror',
  'fork',
  'getaddrinfo',
  'freeaddrinfo',
  'freeifaddrs',
  'pipe',
  'ftruncate',
  'getprotobyname',
  'getrlimit',
  'setlocale',
  'setmode',
  'setrlimit',
  'fcntl',
  'ioctl',
  'setsockopt',
  'mach_absolute_time',
  'fsetxattr',
  'inet_pton',
]

if target_machine.system() != 'darwin'
  syms += 'poll'
endif

foreach s : syms
  if cc.has_function(s)
    cfg.set('HAVE_' + s.to_upper(), true)
  endif
endforeach

if cfg.has('HAVE_SIGNAL_FUNC') and cfg.has('HAVE_SIGNAL_MACRO')
  cfg.set('HAVE_SIGNAL', true)
  if cc.has_function('sigaction', prefix : '#include "signal.h"')
    cfg.set('HAVE_SIGACTION', true)
  endif
endif

if not cfg.has('HAVE_STRNCMPI')
  cfg.set('HAVE_STRCMPI', true)
endif

if not cfg.has('HAVE_SIGSETJMP')
  if cfg.has('HAVE_SETJMP_H')
    if cc.has_function('sigsetjmp', prefix : '#include "setjmp.h"')
      cfg.set('HAVE_MACRO_SIGSETJMP', true)
      cfg.set('HAVE_SIGSETJMP', true)
    endif
  endif
endif

if target_machine.system() == 'windows' and cfg.has('HAVE_INET_PTON')
  #TODO: add winnt flags
endif

cfg2 = cfg
tmp_cfg_file = configure_file(configuration : cfg2, input : 'lib/curl_config.h.meson', output : 'cfg_temp.h')

curl_tests = [
  'HAVE_FCNTL_O_NONBLOCK',
  'HAVE_IOCTLSOCKET',
  'HAVE_IOCTLSOCKET_CAMEL',
  'HAVE_IOCTLSOCKET_CAMEL_FIONBIO',
  'HAVE_IOCTLSOCKET_FIONBIO',
  'HAVE_IOCTL_FIONBIO',
  'HAVE_IOCTL_SIOCGIFADDR',
  'HAVE_SETSOCKOPT_SO_NONBLOCK',
  'HAVE_SOCKADDR_IN6_SIN6_SCOPE_ID',
  'TIME_WITH_SYS_TIME',
  'HAVE_O_NONBLOCK',
  'HAVE_GETHOSTBYADDR_R_5',
  'HAVE_GETHOSTBYADDR_R_7',
  'HAVE_GETHOSTBYADDR_R_8',
  'HAVE_GETHOSTBYADDR_R_5_REENTRANT',
  'HAVE_GETHOSTBYADDR_R_7_REENTRANT',
  'HAVE_GETHOSTBYADDR_R_8_REENTRANT',
  'HAVE_GETHOSTBYNAME_R_3',
  'HAVE_GETHOSTBYNAME_R_5',
  'HAVE_GETHOSTBYNAME_R_6',
  'HAVE_GETHOSTBYNAME_R_3_REENTRANT',
  'HAVE_GETHOSTBYNAME_R_5_REENTRANT',
  'HAVE_GETHOSTBYNAME_R_6_REENTRANT',
  'HAVE_SOCKLEN_T',
  'HAVE_IN_ADDR_T',
  'HAVE_BOOL_T',
  'STDC_HEADERS',
  'RETSIGTYPE_TEST',
  'HAVE_INET_NTOA_R_DECL',
  'HAVE_INET_NTOA_R_DECL_REENTRANT',
  'HAVE_GETADDRINFO',
  'HAVE_FILE_OFFSET_BITS',
]

if cfg.has('HAVE_FSETXATTR')
  curl_tests += ['HAVE_FSETXATTR5']
  curl_tests += ['HAVE_FSETXATTR6']
endif

test_code = '#include "' + meson.current_build_dir() + '/cfg_temp.h"\n'
test_code = '#include "' + meson.current_source_dir() + '/CMake/CurlTests.c"\n'

foreach t : curl_tests
  prefix = '#define ' + t + '\n'
  if cc.compiles(prefix + test_code, name : 'curl compile test ' + t)
    cfg.set(t, true)
  endif
endforeach

file_offset_prefix = ''

if cfg.has('HAVE_FILE_OFFSET_BITS')
  cfg.set('_FILE_OFFSET_BITS', 64)
  cflags += '-D_FILE_OFFSET_BITS=64'
  file_offset_prefix = '#define _FILE_OFFSET_BITS 64\n#include "sys/types.h"'
endif

size = cc.sizeof('off_t', prefix : file_offset_prefix)
cfg.set('SIZEOF_OFF_T', size)

curl_off_t_prefix = file_offset_prefix + '\n#include "' + meson.current_source_dir() + '/include/curl/system.h"'
cfg.set('SIZEOF_CURL_OFF_T', cc.sizeof('curl_off_t', prefix : curl_off_t_prefix))

curl_run_tests = [
  'HAVE_GLIBC_STRERROR_R',
  'HAVE_POSIX_STRERROR_R',
]

foreach t : curl_run_tests
  prefix = '#define ' + t + '\n'
  rv = cc.run(prefix + test_code, name : 'curl run test ' + t)
  if rv.returncode() == 0
    cfg.set(t, true)
  endif
endforeach

need_reentrant = false

foreach t : [
    'HAVE_GETHOSTBYADDR_R_5',
    'HAVE_GETHOSTBYADDR_R_7',
    'HAVE_GETHOSTBYADDR_R_8',
    'HAVE_GETHOSTBYNAME_R_3',
    'HAVE_GETHOSTBYNAME_R_5',
    'HAVE_GETHOSTBYNAME_R_6',
    'HAVE_INET_NTOA_R_DECL_REENTRANT',
  ]
  if not cfg.has(t)
    if cfg.has(t + '_REENTRANT')
      need_reentrant = false
    endif
  endif
endforeach

if need_reentrant
  foreach t : [
      'HAVE_GETHOSTBYADDR_R_5',
      'HAVE_GETHOSTBYADDR_R_7',
      'HAVE_GETHOSTBYADDR_R_8',
      'HAVE_GETHOSTBYNAME_R_3',
      'HAVE_GETHOSTBYNAME_R_5',
      'HAVE_GETHOSTBYNAME_R_6',
    ]

    cfg.set(t, false)
    if cfg.has(t + '_REENTRANT')
      cfg.set(t, true)
    endif
  endforeach
endif

if cfg.has('HAVE_INET_NTOA_R_DECL_REENTRANT')
  cfg.set('HAVE_INET_NTOA_R_DECL', true)
  need_reentrant = true
endif

if need_reentrant
  cfg.set('NEED_REENTRANT', true)
endif

if not cfg.has('HAVE_IN_ADDR_T')
  cfg.set('in_addr_t', 'unsigned long')
endif

if not(cfg.has('HAVE_FIONBIO') or cfg.has('HAVE_IOCTLSOCKET') or cfg.has('HAVE_IOCTLSOCKET_CASE') or cfg.has('HAVE_O_NONBLOCK'))
  cfg.set('HAVE_DISABLED_NONBLOCKING', true)
endif

if cfg.has('RETSIGTYPE_TEST')
  cfg.set('RETSIGTYPE', 'void')
else
  cfg.set('RETSIGTYPE', 'int')
endif

sock_test = ''
if target_machine.system() == 'windows'
  if cfg.has('HAVE_WINSOCK2_H')
    sock_test += '#include "winsock2.h"\n'
  endif
  if cfg.has('HAVE_WINDOWS_H')
    sock_test += '#include "windows.h"\n'
  endif
  if cfg.has('HAVE_WINSOCK_H')
    sock_test += '#include "winsock.h"\n'
  endif
  if(HAVE_LIBWS2_32)
    sock_test += ' #pragma comment(lib, "Ws2_32.lib")\n'
  endif
else
  if cfg.has('HAVE_SYS_TYPES_H')
    sock_test += '#include "sys/types.h"\n'
  endif
  if cfg.has('HAVE_SYS_SOCKET_H')
    sock_test += '#include "sys/socket.h"\n'
  endif
endif

cfg.set('HAVE_RECV', true)
cfg.set('HAVE_SEND', true)
cfg.set('HAVE_STRUCT_TIMEVAL', true)
if target_machine.system() == 'windows'
  cfg.set('RECV_TYPE_RETV', 'int')
  cfg.set('RECV_TYPE_ARG1', 'SOCKET')
  cfg.set('RECV_TYPE_ARG2', 'char*')
  cfg.set('RECV_TYPE_ARG3', 'int')
  cfg.set('RECV_TYPE_ARG4', 'int')
  cfg.set('SEND_TYPE_RETV', 'int')
  cfg.set('SEND_TYPE_ARG1', 'SOCKET')
  cfg.set('SEND_QUAL_ARG2', 'const')
  cfg.set('SEND_TYPE_ARG2', 'char*')
  cfg.set('SEND_TYPE_ARG3', 'int')
  cfg.set('SEND_TYPE_ARG4', 'int')
else
  cfg.set('RECV_TYPE_RETV', 'ssize_t')
  cfg.set('RECV_TYPE_ARG1', 'int')
  cfg.set('RECV_TYPE_ARG2', 'void*')
  cfg.set('RECV_TYPE_ARG3', 'size_t')
  cfg.set('RECV_TYPE_ARG4', 'int')
  cfg.set('SEND_TYPE_RETV', 'ssize_t')
  cfg.set('SEND_TYPE_ARG1', 'int')
  cfg.set('SEND_QUAL_ARG2', 'const')
  cfg.set('SEND_TYPE_ARG2', 'void*')
  cfg.set('SEND_TYPE_ARG3', 'size_t')
  cfg.set('SEND_TYPE_ARG4', 'int')
endif

sys_poll_prefix = sock_test
if cfg.has('HAVE_SYS_POLL_H')
  sys_poll_prefix = '#include <sys/poll.h>\n'
endif

sys_poll_test = '''
  int main(void) {
    return poll((void *)0, 0, 10 /*ms*/);
  }
'''

sys_poll_runs = cc.run(sys_poll_prefix + sys_poll_test, name : 'curl run test HAVE_POLL_FINE')
if sys_poll_runs.returncode() == 0
  cfg.set('HAVE_POLL_FINE', true)
endif

cfg.set('HAVE_SIG_ATOMIC_T', true)
signal_prefix = ''
if cfg.has('HAVE_SIGNAL_H')
  signal_prefix = '#include <signal.h>\n'
endif

sig_atomic_size = cc.sizeof('sig_atomic_t', prefix : signal_prefix)
if sig_atomic_size > 0
  cfg.set('SIZEOF_SIG_ATOMIC_T', sig_atomic_size)
  cfg.set('HAVE_SIZEOF_SIG_ATOMIC_T', true)
  test = '''
     int main(void) {
       static volatile sig_atomic_t dummy = 0;
       (void)dummy;
       return 0;
     }'''

  if cc.compiles(signal_prefix + test, name : 'curl test HAVE_SIG_ATOMIC_T_NOT_VOLATILE')
    cfg.set('HAVE_SIG_ATOMIC_T_NOT_VOLATILE', true)
  else
    cfg.set('HAVE_SIG_ATOMIC_T_VOLATILE', true)
  endif
endif


sockaddr_storage_size = cc.sizeof('struct sockaddr_storage', prefix : sock_test)
if sockaddr_storage_size > 0
  cfg.set('HAVE_SIZEOF_STRUCT_SOCKADDR_STORAGE', true)
  cfg.set('HAVE_STRUCT_SOCKADDR_STORAGE', true)
  cfg.set('SIZEOF_STRUCT_SOCKADDR_STORAGE', sockaddr_storage_size)
endif

cflags += '-DHAVE_CONFIG_H'

subdir('lib')
