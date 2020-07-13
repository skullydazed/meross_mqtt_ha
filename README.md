# Local control of your Meross outlets for Home Assistant

Listens on MQTT for Meross outlets and bridges them to Home Assistant. This has been tested with mss110 outlets, it should work with similar outlets.

# Installation (Docker)

The easiest way to use this is with the docker container. You can probably run it like this:

    docker run skullydazed/meross_mqtt_ha

You can set the following environment variables to configure behavior:

| Variable | Default | Description |
|----------|---------|-------------|
| `HOMEASSISTANT_PREFIX` | `homeassistant` | The prefix for the Home Assistant MQTT discovery topic. |
| `MEROSS_SHARED_KEY` | | The shared key you configured at pairing time. Untested. |
| `MQTT_CLIENT_ID` | `meross_mqtt_ha` | The MQTT client ID to use |
| `MQTT_HOST` | `localhost` | The MQTT broker to connect to |
| `MQTT_PORT` | `1883` | The port to connect to on the MQTT broker |
| `MQTT_QOS` | `1` | The MQTT QOS level |

# Pairing Outlets

After setting this up you must pair your outlets with your local MQTT broker using this:

    https://github.com/bytespider/Meross/wiki/MQTT

These are the steps I used to pair my outlet:

* Setup mosquitto. The instructions say to use TLSv1 but it's been deprecated. Just leave that setting commented out, it works fine.
* Listen for messages on your mqtt server: `screen mosquitto_sub -t /appliance/# -t 'homeassistant/#' -v`
    * If your ssh disconnects while you're pairing you can ssh in again and run `screen -x` to reattach it.
* Pair an outlet (do this from a different machine than your mosquitto server):
    * `git clone https://github.com/bytespider/Meross`
    * `cd Meross/bin/src`
    * `npm install`
    * Unplug my meross outlet, hold button down, plug in outlet, release button after 5-10 seconds
    * Join the newly exposed AP
    * `./meross info --gateway 10.10.10.1`
    * Make sure you get back sane looking results
    * `./meross setup --gateway 10.10.10.1 --wifi-ssid <SSID> --wifi-pass <PASSWORD> --mqtt <SERVER_IP>:8883`
* Check your screen session, you should see several messages from your outlet(s).
* You're ready to go!

# Outlet Information

Once you have paired your outlets you can use `meross_info <ip_of_outlet>` to get information about the outlet.

## More information

I found these pages extremely helpful when putting this together:

* https://github.com/albertogeniola/MerossIot/issues/1
* https://github.com/bytespider/Meross
* https://github.com/shodge12/Meross-Node-Red-Comm
