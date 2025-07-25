# Notes

## Steps

  * [done] MQTT
  * Telegraf / InfluxDB
  * Grafana

## MQTT

Wrote a simple compose.yml that uses `eclipse-mosquitto:latest` with default configuration.

    docker compose up

shows

```
mosquitto  | 1753469439: mosquitto version 2.0.22 starting
mosquitto  | 1753469439: Config loaded from /mosquitto/config/mosquitto.conf.
mosquitto  | 1753469439: Starting in local only mode. Connections will only be possible from clients running on this machine.
mosquitto  | 1753469439: Create a configuration file which defines a listener to allow remote access.
mosquitto  | 1753469439: For more details see https://mosquitto.org/documentation/authentication-methods/
```

I'll do that later.

    docker exec -it mosquitto mosquitto_sub -t '/#' -v

to subscribe, and

    docker exec -it mosquitto mosquitto_pub -t /test/topic -m 'hello there'

to publish shows

    /test/topic hello there

as expected.

Added authentication for `mqtt/mqtt` and mounted `./mosquitto/conf` read-only to avoid
mosquitto chown'ing files and causing git to think there are metadata changes.
(See https://github.com/eclipse-mosquitto/mosquitto/issues/3284)

Now

    docker exec -it mosquitto mosquitto_sub -u mqtt -P mqtt -t '/#' -v

to subscribe, and

    docker exec -it mosquitto mosquitto_pub -u mqtt -P mqtt -t /test/topic -m 'new and improved'

to publish works as expected.

Pinned eclipse-mosquitto to 2.0.22 to avoid sudden breakage if they put teeth in their warning
about password file ownership.

## InfluxDB / Telegraf

The nuisance part here, at least with InfluxDB2, is that you need to run `influx` once to
create the token that Telgraf needs to talk to InfluxDB. There's a bunch of advice that's
implicitly based on the older v1 influx.

https://community.influxdata.com/t/ti-stack-with-docker-compose/19337/6 has an approach
that looks worth trying.

And, some futzing later, that seems to work. Next up is to hook in Telegraf.
