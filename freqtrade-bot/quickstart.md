```bash
mkdir ft_userdata
cd ft_userdata/
# Download the docker-compose file from the repository
curl https://raw.githubusercontent.com/freqtrade/freqtrade/stable/docker-compose.yml -o docker-compose.yml

# Pull the freqtrade image
docker-compose pull

# Create user directory structure
docker-compose run --rm freqtrade create-userdir --userdir user_data

# Create configuration - Requires answering interactive questions
docker-compose run --rm freqtrade new-config --config user_data/config.json
```

La explicacion de las opciones que se utilizan en el config.json estan [aca](https://www.freqtrade.io/en/stable/configuration/)

Para descargar la data se puede usar el siguiente yaml, donde el comando principal fue sacado de [aca](https://www.freqtrade.io/en/stable/data-download/):
```yaml
---
version: '3'
services:
  freqtrade:
    image: freqtradeorg/freqtrade:stable
    # image: freqtradeorg/freqtrade:develop
    # Use plotting image
    # image: freqtradeorg/freqtrade:develop_plot
    # Build step - only needed when additional dependencies are needed
    # build:
    #   context: .
    #   dockerfile: "./docker/Dockerfile.custom"
    restart: unless-stopped
    container_name: freqtrade
    volumes:
      - "./user_data:/freqtrade/user_data"
    # Expose api on port 8080 (localhost only)
    # Please read the https://www.freqtrade.io/en/stable/rest-api/ documentation
    # before enabling this.
    ports:
      - "127.0.0.1:8080:8080"
    # Default command used when running `docker compose up`
    command: >
      download-data --exchange binance --pairs ETH/USDT XRP/USDT BTC/USDT
```

TODO: hacer que ande este benchmark
Para hacer el benchmark podemos chequear [aca](https://www.freqtrade.io/en/stable/backtesting/)

TODO: falta chequear por que no tradea
Para correr el bot de trading con el siguiente yaml:
```yaml
---
version: '3'
services:
  freqtrade:
    image: freqtradeorg/freqtrade:stable
    # image: freqtradeorg/freqtrade:develop
    # Use plotting image
    # image: freqtradeorg/freqtrade:develop_plot
    # Build step - only needed when additional dependencies are needed
    # build:
    #   context: .
    #   dockerfile: "./docker/Dockerfile.custom"
    restart: unless-stopped
    container_name: freqtrade
    volumes:
      - "./user_data:/freqtrade/user_data"
    # Expose api on port 8080 (localhost only)
    # Please read the https://www.freqtrade.io/en/stable/rest-api/ documentation
    # before enabling this.
    ports:
      - "127.0.0.1:8080:8080"
    # Default command used when running `docker compose up`
    command: >
      trade
      --logfile /freqtrade/user_data/logs/freqtrade.log
      --db-url sqlite:////freqtrade/user_data/tradesv3.sqlite
      --config /freqtrade/user_data/config.json
      --strategy SampleStrategy
```

TODO: investigar hyper opt
Para optimizar parametros de nuestra estrategia / modelo podemos usar [esto](https://www.freqtrade.io/en/stable/hyperopt/)