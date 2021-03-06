# Copyright (c) 2017 EXVO developers
# Distributed under the MIT software license, see the accompanying
# file COPYING or http://www.opensource.org/licenses/mit-license.php.

DIST_SUBDIRS = secp256k1 univalue

AM_LDFLAGS = $(PTHREAD_CFLAGS) $(LIBTOOL_LDFLAGS) $(HARDENED_LDFLAGS)
AM_CXXFLAGS = $(HARDENED_CXXFLAGS) $(ERROR_CXXFLAGS)
AM_CPPFLAGS = $(HARDENED_CPPFLAGS)
EXTRA_LIBRARIES =

if EMBEDDED_UNIVALUE
LIBUNIVALUE = univalue/libunivalue.la

$(LIBUNIVALUE): $(wildcard univalue/lib/*) $(wildcard univalue/include/*)
	$(AM_V_at)$(MAKE) $(AM_MAKEFLAGS) -C $(@D) $(@F)
else
LIBUNIVALUE = $(UNIVALUE_LIBS)
endif

EXVO_CONFIG_INCLUDES=-I$(builddir)/config
EXVO_INCLUDES=-I$(builddir) -I$(builddir)/obj $(BDB_CPPFLAGS) $(BOOST_CPPFLAGS) $(LEVELDB_CPPFLAGS) $(CRYPTO_CFLAGS) $(SSL_CFLAGS)

EXVO_INCLUDES += -I$(srcdir)/secp256k1/include
EXVO_INCLUDES += $(UNIVALUE_CFLAGS)

LIBEXVO_SERVER=libexvo_server.a
LIBEXVO_COMMON=libexvo_common.a
LIBEXVO_CONSENSUS=libexvo_consensus.a
LIBEXVO_CLI=libexvo_cli.a
LIBEXVO_UTIL=libexvo_util.a
LIBEXVO_CRYPTO=crypto/libexvo_crypto.a
LIBEXVOQT=qt/libexvoqt.a
LIBSECP256K1=secp256k1/libsecp256k1.la

if ENABLE_ZMQ
LIBEXVO_ZMQ=libexvo_zmq.a
endif
if BUILD_EXVO_LIBS
LIBEXVOCONSENSUS=libexvoconsensus.la
endif
if ENABLE_WALLET
LIBEXVO_WALLET=libexvo_wallet.a
endif

$(LIBSECP256K1): $(wildcard secp256k1/src/*) $(wildcard secp256k1/include/*)
	$(AM_V_at)$(MAKE) $(AM_MAKEFLAGS) -C $(@D) $(@F)

# Make is not made aware of per-object dependencies to avoid limiting building parallelization
# But to build the less dependent modules first, we manually select their order here:
EXTRA_LIBRARIES += \
  $(LIBEXVO_CRYPTO) \
  $(LIBEXVO_UTIL) \
  $(LIBEXVO_COMMON) \
  $(LIBEXVO_CONSENSUS) \
  $(LIBEXVO_SERVER) \
  $(LIBEXVO_CLI) \
  $(LIBEXVO_WALLET) \
  $(LIBEXVO_ZMQ)

lib_LTLIBRARIES = $(LIBEXVOCONSENSUS)

bin_PROGRAMS =
noinst_PROGRAMS =
TESTS =
BENCHMARKS =

if BUILD_EXVOD
  bin_PROGRAMS += exvod
endif

if BUILD_EXVO_UTILS
  bin_PROGRAMS += exvo-cli exvo-tx
endif

.PHONY: FORCE check-symbols check-security
# exvo core #
EXVO_CORE_H = \
  activemasternode.h \
  addrdb.h \
  addrman.h \
  base58.h \
  bloom.h \
  blockencodings.h \
  chain.h \
  chainparams.h \
  chainparamsbase.h \
  chainparamsseeds.h \
  checkpoints.h \
  checkqueue.h \
  clientversion.h \
  coins.h \
  compat.h \
  compat/byteswap.h \
  compat/endian.h \
  compat/sanity.h \
  compressor.h \
  consensus/consensus.h \
  core_io.h \
  core_memusage.h \
  cuckoocache.h \
  httprpc.h \
  httpserver.h \
  indirectmap.h \
  init.h \
  key.h \
  keepass.h \
  keystore.h \
  dbwrapper.h \
  limitedmap.h \
  masternode.h \
  masternode-pos.h \
  masternodeman.h \
  masternodeconfig.h \
  memusage.h \
  merkleblock.h \
  miner.h \
  net.h \
  net_processing.h \
  netaddress.h \
  netbase.h \
  netmessagemaker.h \
  noui.h \
  policy/fees.h \
  policy/policy.h \
  policy/rbf.h \
  pow.h \
  protocol.h \
  random.h \
  reverselock.h \
  rpc/client.h \
  rpc/protocol.h \
  rpc/server.h \
  rpc/register.h \
  scheduler.h \
  script/sigcache.h \
  script/sign.h \
  script/standard.h \
  script/ismine.h \
  streams.h \
  support/allocators/secure.h \
  support/allocators/zeroafterfree.h \
  support/cleanse.h \
  support/events.h \
  support/lockedpool.h \
  sync.h \
  spork.h \
  threadsafety.h \
  threadinterrupt.h \
  timedata.h \
  torcontrol.h \
  txdb.h \
  txmempool.h \
  ui_interface.h \
  undo.h \
  util.h \
  utilmoneystr.h \
  utiltime.h \
  validation.h \
  validationinterface.h \
  versionbits.h \
  wallet/coincontrol.h \
  wallet/crypter.h \
  wallet/db.h \
  wallet/rpcwallet.h \
  wallet/wallet.h \
  wallet/walletdb.h \
  warnings.h \
  zmq/zmqabstractnotifier.h \
  zmq/zmqconfig.h\
  zmq/zmqnotificationinterface.h \
  zmq/zmqpublishnotifier.h


obj/build.h: FORCE
	@$(MKDIR_P) $(builddir)/obj
	@$(top_srcdir)/share/genbuild.sh $(abs_top_builddir)/src/obj/build.h \
	  $(abs_top_srcdir)
libexvo_util_a-clientversion.$(OBJEXT): obj/build.h

# server: shared between exvod and exvo-qt
libexvo_server_a_CPPFLAGS = $(AM_CPPFLAGS) $(EXVO_INCLUDES) $(MINIUPNPC_CPPFLAGS) $(EVENT_CFLAGS) $(EVENT_PTHREADS_CFLAGS)
libexvo_server_a_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)
libexvo_server_a_SOURCES = \
  activemasternode.cpp \
  addrman.cpp \
  addrdb.cpp \
  bloom.cpp \
  blockencodings.cpp \
  bitpool.cpp \
  chain.cpp \
  checkpoints.cpp \
  httprpc.cpp \
  httpserver.cpp \
  init.cpp \
  dbwrapper.cpp \
  merkleblock.cpp \
  miner.cpp \
  net.cpp \
  net_processing.cpp \
  noui.cpp \
  policy/fees.cpp \
  policy/policy.cpp \
  pow.cpp \
  rest.cpp \
  rpc/blockchain.cpp \
  rpc/mining.cpp \
  rpc/misc.cpp \
  rpc/net.cpp \
  rpc/rawtransaction.cpp \
  rpc/server.cpp \
  script/sigcache.cpp \
  script/ismine.cpp \
  timedata.cpp \
  torcontrol.cpp \
  txdb.cpp \
  txmempool.cpp \
  ui_interface.cpp \
  validation.cpp \
  validationinterface.cpp \
  versionbits.cpp \
  $(EXVO_CORE_H)

if ENABLE_ZMQ
libexvo_zmq_a_CPPFLAGS = $(EXVO_INCLUDES) $(ZMQ_CFLAGS)
libexvo_zmq_a_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)
libexvo_zmq_a_SOURCES = \
  zmq/zmqabstractnotifier.cpp \
  zmq/zmqnotificationinterface.cpp \
  zmq/zmqpublishnotifier.cpp
endif


# wallet: shared between exvod and exvo-qt, but only linked
# when wallet enabled
libexvo_wallet_a_CPPFLAGS = $(AM_CPPFLAGS) $(EXVO_INCLUDES)
libexvo_wallet_a_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)
libexvo_wallet_a_SOURCES = \
  activemasternode.cpp \
  wallet/crypter.cpp \
  wallet/db.cpp \
  wallet/rpcdump.cpp \
  wallet/rpcwallet.cpp \
  wallet/wallet.cpp \
  wallet/walletdb.cpp \
  policy/rbf.cpp \
  $(EXVO_CORE_H)

# crypto primitives library
crypto_libexvo_crypto_a_CPPFLAGS = $(AM_CPPFLAGS) $(EXVO_CONFIG_INCLUDES)
crypto_libexvo_crypto_a_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)
crypto_libexvo_crypto_a_SOURCES = \
  crypto/aes.cpp \
  crypto/aes.h \
  crypto/common.h \
  crypto/hmac_sha256.cpp \
  crypto/hmac_sha256.h \
  crypto/hmac_sha512.cpp \
  crypto/hmac_sha512.h \
  crypto/ripemd160.cpp \
  crypto/ripemd160.h \
  crypto/sha1.cpp \
  crypto/sha1.h \
  crypto/sha256.cpp \
  crypto/sha256.h \
  crypto/sha512.cpp \
  crypto/sha512.h \
  crypto/aes_helper.c \
  crypto/Lyra2RE/Sponge.h \
  crypto/Lyra2RE/Sponge.c \
  crypto/Lyra2RE/Lyra2.h \
  crypto/Lyra2RE/Lyra2.c \
  crypto/Lyra2RE/blake.c \
  crypto/Lyra2RE/blake.h \
  crypto/Lyra2RE/keccak.c \
  crypto/Lyra2RE/keccak.h \
  crypto/Lyra2RE/skein.c \
  crypto/Lyra2RE/skein.h \
  crypto/Lyra2RE/cubehash.c \
  crypto/Lyra2RE/cubehash.h \
  crypto/Lyra2RE/bmw.c \
  crypto/Lyra2RE/bmw.h

# consensus: shared between all executables that validate any consensus rules.
libexvo_consensus_a_CPPFLAGS = $(AM_CPPFLAGS) $(EXVO_INCLUDES)
libexvo_consensus_a_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)
libexvo_consensus_a_SOURCES = \
  amount.h \
  arith_uint256.cpp \
  arith_uint256.h \
  consensus/merkle.cpp \
  consensus/merkle.h \
  consensus/params.h \
  consensus/validation.h \
  hash.cpp \
  hash.h \
  prevector.h \
  primitives/block.cpp \
  primitives/block.h \
  primitives/transaction.cpp \
  primitives/transaction.h \
  pubkey.cpp \
  pubkey.h \
  script/exvoconsensus.cpp \
  script/interpreter.cpp \
  script/interpreter.h \
  script/script.cpp \
  script/script.h \
  script/script_error.cpp \
  script/script_error.h \
  serialize.h \
  tinyformat.h \
  uint256.cpp \
  uint256.h \
  utilstrencodings.cpp \
  utilstrencodings.h \
  version.h

# common: shared between exvod, and exvo-qt and non-server tools
libexvo_common_a_CPPFLAGS = $(AM_CPPFLAGS) $(EXVO_INCLUDES)
libexvo_common_a_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)
libexvo_common_a_SOURCES = \
  activemasternode.cpp \
  masternode.cpp \
  masternode-pos.cpp \
  masternodeconfig.cpp \
  masternodeman.cpp \
  amount.cpp \
  base58.cpp \
  chainparams.cpp \
  coins.cpp \
  compressor.cpp \
  core_read.cpp \
  core_write.cpp \
  key.cpp \
  keystore.cpp \
  netaddress.cpp \
  netbase.cpp \
  protocol.cpp \
  scheduler.cpp \
  script/sign.cpp \
  script/standard.cpp \
  spork.cpp \
  warnings.cpp \
  $(EXVO_CORE_H)

# util: shared between all executables.
# This library *must* be included to make sure that the glibc
# backward-compatibility objects and their sanity checks are linked.
libexvo_util_a_CPPFLAGS = $(AM_CPPFLAGS) $(EXVO_INCLUDES)
libexvo_util_a_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)
libexvo_util_a_SOURCES = \
  support/lockedpool.cpp \
  chainparamsbase.cpp \
  clientversion.cpp \
  compat/glibc_sanity.cpp \
  compat/glibcxx_sanity.cpp \
  compat/strnlen.cpp \
  random.cpp \
  rpc/protocol.cpp \
  support/cleanse.cpp \
  sync.cpp \
  threadinterrupt.cpp \
  util.cpp \
  utilmoneystr.cpp \
  utilstrencodings.cpp \
  utiltime.cpp \
  $(EXVO_CORE_H)

if GLIBC_BACK_COMPAT
libexvo_util_a_SOURCES += compat/glibc_compat.cpp
endif

# cli: shared between exvo-cli and exvo-qt
libexvo_cli_a_CPPFLAGS = $(AM_CPPFLAGS) $(EXVO_INCLUDES)
libexvo_cli_a_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)
libexvo_cli_a_SOURCES = \
  rpc/client.cpp \
  $(EXVO_CORE_H)

nodist_libexvo_util_a_SOURCES = $(srcdir)/obj/build.h
#

# exvod binary #
exvod_SOURCES = exvod.cpp
exvod_CPPFLAGS = $(AM_CPPFLAGS) $(EXVO_INCLUDES)
exvod_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)
exvod_LDFLAGS = $(RELDFLAGS) $(AM_LDFLAGS) $(LIBTOOL_APP_LDFLAGS)

if TARGET_WINDOWS
exvod_SOURCES += exvod-res.rc
endif

exvod_LDADD = \
  $(LIBEXVO_SERVER) \
  $(LIBEXVO_COMMON) \
  $(LIBUNIVALUE) \
  $(LIBEXVO_UTIL) \
  $(LIBEXVO_WALLET) \
  $(LIBEXVO_ZMQ) \
  $(LIBEXVO_CONSENSUS) \
  $(LIBEXVO_CRYPTO) \
  $(LIBLEVELDB) \
  $(LIBMEMENV) \
  $(LIBSECP256K1)

exvod_LDADD += $(BOOST_LIBS) $(BDB_LIBS) $(SSL_LIBS) $(CRYPTO_LIBS) $(MINIUPNPC_LIBS) $(EVENT_PTHREADS_LIBS) $(EVENT_LIBS) $(ZMQ_LIBS)

# exvo-cli binary #
exvo_cli_SOURCES = exvo-cli.cpp
exvo_cli_CPPFLAGS = $(AM_CPPFLAGS) $(EXVO_INCLUDES) $(EVENT_CFLAGS)
exvo_cli_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)
exvo_cli_LDFLAGS = $(RELDFLAGS) $(AM_LDFLAGS) $(LIBTOOL_APP_LDFLAGS)

if TARGET_WINDOWS
exvo_cli_SOURCES += exvo-cli-res.rc
endif

exvo_cli_LDADD = \
  $(LIBEXVO_CLI) \
  $(LIBUNIVALUE) \
  $(LIBEXVO_UTIL) \
  $(LIBEXVO_CRYPTO)

exvo_cli_LDADD += $(BOOST_LIBS) $(SSL_LIBS) $(CRYPTO_LIBS) $(EVENT_LIBS)
#

# exvo-tx binary #
exvo_tx_SOURCES = exvo-tx.cpp
exvo_tx_CPPFLAGS = $(AM_CPPFLAGS) $(EXVO_INCLUDES)
exvo_tx_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)
exvo_tx_LDFLAGS = $(RELDFLAGS) $(AM_LDFLAGS) $(LIBTOOL_APP_LDFLAGS)

if TARGET_WINDOWS
exvo_tx_SOURCES += exvo-tx-res.rc
endif

exvo_tx_LDADD = \
  $(LIBUNIVALUE) \
  $(LIBEXVO_COMMON) \
  $(LIBEXVO_UTIL) \
  $(LIBEXVO_CONSENSUS) \
  $(LIBEXVO_CRYPTO) \
  $(LIBSECP256K1)

exvo_tx_LDADD += $(BOOST_LIBS) $(CRYPTO_LIBS)
#

# exvoconsensus library #
if BUILD_EXVO_LIBS
include_HEADERS = script/exvoconsensus.h
libexvoconsensus_la_SOURCES = $(crypto_libexvo_crypto_a_SOURCES) $(libexvo_consensus_a_SOURCES)

if GLIBC_BACK_COMPAT
  libexvoconsensus_la_SOURCES += compat/glibc_compat.cpp
endif

libexvoconsensus_la_LDFLAGS = $(AM_LDFLAGS) -no-undefined $(RELDFLAGS)
libexvoconsensus_la_LIBADD = $(LIBSECP256K1)
libexvoconsensus_la_CPPFLAGS = $(AM_CPPFLAGS) -I$(builddir)/obj -I$(srcdir)/secp256k1/include -DBUILD_EXVO_INTERNAL
libexvoconsensus_la_CXXFLAGS = $(AM_CXXFLAGS) $(PIE_FLAGS)

endif
#

CTAES_DIST =  crypto/ctaes/bench.c
CTAES_DIST += crypto/ctaes/ctaes.c
CTAES_DIST += crypto/ctaes/ctaes.h
CTAES_DIST += crypto/ctaes/README.md
CTAES_DIST += crypto/ctaes/test.c

CLEANFILES = $(EXTRA_LIBRARIES)

CLEANFILES += *.gcda *.gcno
CLEANFILES += compat/*.gcda compat/*.gcno
CLEANFILES += consensus/*.gcda consensus/*.gcno
CLEANFILES += crypto/*.gcda crypto/*.gcno
CLEANFILES += policy/*.gcda policy/*.gcno
CLEANFILES += primitives/*.gcda primitives/*.gcno
CLEANFILES += script/*.gcda script/*.gcno
CLEANFILES += support/*.gcda support/*.gcno
CLEANFILES += univalue/*.gcda univalue/*.gcno
CLEANFILES += wallet/*.gcda wallet/*.gcno
CLEANFILES += wallet/test/*.gcda wallet/test/*.gcno
CLEANFILES += zmq/*.gcda zmq/*.gcno

DISTCLEANFILES = obj/build.h

EXTRA_DIST = $(CTAES_DIST)

clean-local:
	-$(MAKE) -C secp256k1 clean
	-$(MAKE) -C univalue clean
	-rm -f leveldb/*/*.gcda leveldb/*/*.gcno leveldb/helpers/memenv/*.gcda leveldb/helpers/memenv/*.gcno
	-rm -f config.h
	-rm -rf test/__pycache__

.rc.o:
	@test -f $(WINDRES)
	## FIXME: How to get the appropriate modulename_CPPFLAGS in here?
	$(AM_V_GEN) $(WINDRES) $(DEFS) $(DEFAULT_INCLUDES) $(INCLUDES) $(CPPFLAGS) -DWINDRES_PREPROC -i $< -o $@

.mm.o:
	$(AM_V_CXX) $(OBJCXX) $(DEFS) $(DEFAULT_INCLUDES) $(INCLUDES) $(AM_CPPFLAGS) \
	  $(CPPFLAGS) $(AM_CXXFLAGS) $(QT_INCLUDES) $(AM_CXXFLAGS) $(PIE_FLAGS) $(CXXFLAGS) -c -o $@ $<

check-symbols: $(bin_PROGRAMS)
if GLIBC_BACK_COMPAT
	@echo "Checking glibc back compat..."
	$(AM_V_at) READELF=$(READELF) CPPFILT=$(CPPFILT) $(top_srcdir)/contrib/devtools/symbol-check.py < $(bin_PROGRAMS)
endif

check-security: $(bin_PROGRAMS)
if HARDEN
	@echo "Checking binary security..."
	$(AM_V_at) READELF=$(READELF) OBJDUMP=$(OBJDUMP) $(top_srcdir)/contrib/devtools/security-check.py < $(bin_PROGRAMS)
endif

%.pb.cc %.pb.h: %.proto
	@test -f $(PROTOC)
	$(AM_V_GEN) $(PROTOC) --cpp_out=$(@D) --proto_path=$(<D) $<

if EMBEDDED_LEVELDB
include Makefile.leveldb.include
endif

if ENABLE_TESTS
include Makefile.test.include
endif

if ENABLE_BENCH
include Makefile.bench.include
endif

if ENABLE_QT
include Makefile.qt.include
endif

if ENABLE_QT_TESTS
include Makefile.qttest.include
endif
