# cryptowat_exporter

A [Prometheus](https://prometheus.io/) exporter to fetch values from [cryptowat.ch](https://cryptowat.ch) written in Go.

## Installation

Binaries can be downloaded from the Github releases page. A Docker image will be released soon.

Building yourself is rather easy, just install `golang` and `make` and run:

```
make
```

To build a docker image, adjust the Docker architectures in the `Makefile` and run:

```
make docker
```

## Usage

```
./cryptowat_exporter
```

Then visit `http://localhost:9745/metrics`

## Configuration

The exporter takes some command line parameters that allow configuring a few things:

* `--cryptowat.exchanges`: Comma separated list of exchanges to scrap (default: `bitstamp,kraken,coinbase-pro`)
* `--cryptowat.pairs`: Comma separated list of pairs to scrap (default: `btcusd,ltcusd`) 
* `--cryptowat.cachesecs`: Number of seconds to cache values for (default: 60) 
* `--web.listen-address`: Address and port to listen on (default: `:9745`)

## Generated metrics

All crypto-related metrics generated by the exporter are prefixed with `crypto_`. At the moment the exporter scraps the last known value and daily highs, lows and changes (absolute values and percents).

Example:

```
...
# HELP crypto_high_24h_value The 24h highest value in a given market (exchange/pair)
# TYPE crypto_high_24h_value gauge
crypto_high_24h_value{exchange="bitstamp",pair="btcusd"} 8984.16
crypto_high_24h_value{exchange="bitstamp",pair="ltcusd"} 43.31
crypto_high_24h_value{exchange="coinbase-pro",pair="btcusd"} 8977
crypto_high_24h_value{exchange="coinbase-pro",pair="ltcusd"} 43.31
crypto_high_24h_value{exchange="kraken",pair="btcusd"} 8975
crypto_high_24h_value{exchange="kraken",pair="ltcusd"} 43.31
# HELP crypto_last_value The last known value in a given market (exchange/pair)
# TYPE crypto_last_value gauge
crypto_last_value{exchange="bitstamp",pair="btcusd"} 8902.94
crypto_last_value{exchange="bitstamp",pair="ltcusd"} 42.6
crypto_last_value{exchange="coinbase-pro",pair="btcusd"} 8898.77
crypto_last_value{exchange="coinbase-pro",pair="ltcusd"} 42.58
crypto_last_value{exchange="kraken",pair="btcusd"} 8892.1
crypto_last_value{exchange="kraken",pair="ltcusd"} 42.55
...
```

## Prometheus configuration

```
scrape_configs:
  - job_name: cryptowat
    static_configs:
      - targets: ['localhost:9745']
```

You can poll as often as you want but, by default, the exporter only refreshes the cached values every minute. This can be changed using `--cryptowat.cachesecs` but bear in mind that Cryptowat.ch's API does rate limiting.

## Example Grafana visualisations

![One](grafana1.png)