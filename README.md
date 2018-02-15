Doger
=====

IRC tip bot written in Python.

**Requirements:**

- **RPC library** - From [here](https://github.com/jcsaaddupuy/dogecoin-python) or pypi
- **Dogecoind** - From [here](https://github.com/dogecoin/dogecoin/), because you'll need the `dogecoind` binary
- **PostgreSQL** - From [here](http://www.postgresql.org/)
- **Psycopg 2** - From [here](https://pypi.python.org/pypi/psycopg2), which provides Python binding to PostgreSQL
- **Python** - Obviously

**Setup:**

- Create a file in the same folder as the source code named `Config.py` that contains the following, then customize it according to your needs:

```
config = {
	"host": "irc-server-hostname.example.com",
	"port": 6667,
# optional:
#	"ipv6": True,
#	"bindhost": "10.0.0.1",
	"user": "identname",
	"rname": "Real name",
	"confirmations": 4,
	"account": "nickservaccountname",
	"password": "nickservpassword",
	"admins": {
		"unaffiliated/johndoe": True # hosts/cloaks of admins
	},
	"prefix": "!", # the trigger character
# optional:
#	"ssl": {
#		"certs": "/etc/ssl/certs/ca-certificates.crt"
#	},
	"instances": {
		"nick1": ["#channel1", "#channel2"],
		"nick2": ["#channel3"]
	},
# optional:
#	"ignore": {
#		"cost": 10, # score added for every command
#		"limit": 80, # max allowed score
#		"timeout": 240 # ignore length
#	},
	"logfile": "path/to/log",
# optional:
#	"irclog": ("nick1", "#logchannel"),
	"database": "name of pgsql database"
}
```

- Add the following to the `dogecoin.conf` file:

```
rpcthreads=100
daemon=1
irc=0
dnsseed=1
paytxfee=1.0
blocknotify=/usr/bin/touch blocknotify/blocknotify
```

- Create a PostgreSQL database, connect to it, then set up the following schema:

```
CREATE TABLE accounts (account character varying(16) NOT NULL, balance bigint DEFAULT 0, CONSTRAINT balance CHECK ((balance >= 0)));
CREATE TABLE address_account (address character varying(34) NOT NULL, account character varying(16), used bit(1) DEFAULT B'0'::"bit" NOT NULL);
CREATE TABLE locked (account character varying(16));
CREATE TABLE lastblock (block character varying(64));
CREATE TABLE txlog (timestamp double precision, token character varying(8), source character varying(16), destination character varying(16), amount bigint, transaction character varying(64), address character varying(34));
INSERT INTO lastblock VALUES ('0');
ALTER TABLE accounts ADD CONSTRAINT accounts_pkey PRIMARY KEY (account);
ALTER TABLE address_account ADD CONSTRAINT address_account_pkey PRIMARY KEY (address);
ALTER TABLE address_account ADD CONSTRAINT address_account_account_fkey FOREIGN KEY (account) REFERENCES accounts(account);
ALTER TABLE locked ADD CONSTRAINT locked_pkey PRIMARY KEY (account);
```
    
**Running Doger:**

- Start up the Dogecoin daemon (`dogecoind`)
- Launch the Doger bot with `python Main.py`
