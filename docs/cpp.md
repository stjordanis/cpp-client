---
title: "Cpp"
---

# Cpp

::: warning
This package is still under development. This page will get more content as it evolves more. In the meantime you can view its source on [Github](https://github.com/ArkEcosystem/cpp-client/).
:::

[[toc]]

## Platforms
* [Arduino](#Arduino)  
* [Linux >= 16.04](#OS)  
* [macOS >= 10.10](#OS)  
* [Windows >= 7](#OS)  

## Usage

The Cpp Client is meant to be used for creating request to an API endpoint.  
For cryptography related functionality, such as generating addresses or creating transactions,  
please see the [Cpp Crypto](https://github.com/ArkEcosystem/cpp-crypto) repository.

### Connection

Before making a request, you should create a `Connection`.  
A `Connection` expects a `host`, which is an url on which the API can be reached.  
An example `Connection`, that interfaces with the API of an Ark Node, would be created as follows:

```cpp
// Create a connection
Ark::Client::Connection<Ark::Client::Api> connection("167.114.29.54", 4003);
```

### Making an API Request

The below example shows how you can perform a request.

```cpp
// Check the API Version
auto apiVersion = connection.api.version();

// Perform an API call using the connection to access endpoint
const auto blockResponse = connection.api.blocks.get("13114381566690093367")

const auto delegateResponse = connection.api.delegates.get("boldninja");

const auto nodeConfiguration = connection.api.node.configuration();

const auto peer = connection.api.peers.get("167.114.29.49");

const auto transaction = connection.api.transactions.get("b324cea5c5a6c15e6ced3ec9c3135a8022eeadb8169f7ba66c80ebc82b0ac850");

const auto vote = connection.api.votes.get("d202acbfa947acac53ada2ac8a0eb662c9f75421ede3b10a42759352968b4ed2");

const auto walletsSearch = connection.api.wallets.search({"username", "baldninja"});
```
> *note: All API response are of the type `std::string`

# 

### Getting an API Path

There are instances when a client may use a gateway or bridge to do http get/post;  
for this we have provided an interface for obtaining a properly formatted API Path for a given API Endpoint.  
Below are examples of how to access the Path interface:

```cpp
Ark::Client::Host dummyHost("0.0.0.0:4003");

std::string blocksAllPath = Ark::Client::API::Paths::Blocks::all(dummyHost, 5 /* limit */, 1 /* page */);
// blocksAllPath will be the string "0.0.0.0:4003/api/v2/blocks?limit=5&page=1"

std::string delegatesGetPath = Ark::Client::API::Paths::Delegates::get(dummyHost, "boldninja");
// delegatesGetPath will be the string "0.0.0.0:4003/api/v2/delegates/boldninja"

std::string nodeConfigurationPath = Ark::Client::API::Paths::Node::configuration(dummyHost);
// nodeConfigurationPath will be the string "0.0.0.0:4003/api/v2/node/configuration"

std::string peersAllPath = Ark::Client::API::Paths::Peers::all(dummyHost, 5 /* limit */, 1 /* page */);
// peersAllPath will be the string "0.0.0.0:4003/api/v2/peers?limit=5&page=1"

std::string transactionsTypesPath = Ark::Client::API::Paths::Transactions::types(dummyHost);
// transactionsTypesPath will be the string "0.0.0.0:4003/api/v2/transactions/types"

std::string votesGetPath = Ark::Client::API::Paths::Votes::get(dummyHost, "d202acbfa947acac53ada2ac8a0eb662c9f75421ede3b10a42759352968b4ed2");
// votesGetPath will be the string "0.0.0.0:4003/api/v2/votes/d202acbfa947acac53ada2ac8a0eb662c9f75421ede3b10a42759352968b4ed2"

// the following is an example of formatting a path and body parameters for an http post
const std::map<std::string, std::string> searchBody = {
    {"username", "baldninja"},
    {"address", "DFJ5Z51F1euNNdRUQJKQVdG4h495LZkc6T"},
    {"publicKey", "03d3c6889608074b44155ad2e6577c3368e27e6e129c457418eb3e5ed029544e8d"}
};

std::pair<std::string, std::string> walletsSearchPath = Ark::Client::API::Paths::Wallets::search(testHost, searchBody, 5, 1);
// walletsSearchPath.first will be the string "0.0.0.0:4003/api/v2/wallets/search?limit=5&page=1"
// walletsSearchPath.second will be the string "address=DFJ5Z51F1euNNdRUQJKQVdG4h495LZkc6T&publicKey=03d3c6889608074b44155ad2e6577c3368e27e6e129c457418eb3e5ed029544e8d&username=baldninja"
```

# Arduino
**Arduino IDE:**  
Download and install the Arduino IDE (>=1.8.5) from the following link:  
```https://www.arduino.cc/en/Main/Software```

Using the Arduino IDE's built in Library Manager,  
install the following Libraries:  
```ArduinoJson v5.13.2```  
```AUnit```

#### Arduino Example using the Adafruit Feather ESP8266

```Arduino
#include <arkClient.h>
#include <ESP8266WiFi.h>

const char* ssid = "your_network";
const char* password = "your_password";

void setup() {
    Serial.begin(115200);

    WiFi.mode(WIFI_STA);
    WiFi.begin(ssid, password);
    while (WiFi.status() != WL_CONNECTED) { delay(500); Serial.print("."); }
    Serial.print("\nConnected, IP address: ");
    Serial.println(WiFi.localIP());

    Ark::Client::Connection<Ark::Client::Api> connection("167.114.29.54", 4003);

    auto allBlocks = arkClient.blocks.all();
    Serial.println(allBlocks.c_str());

    auto allDelegates = arkClient.delegates.all();
    Serial.println(allDelegates.c_str());
  
    auto delegatesCount = arkClient.delegates.count();
    Serial.println(delegatesCount.c_str());
  
    auto allPeers = arkClient.peers.all();
    Serial.println(allPeers.c_str());

    auto allTransactions = arkClient.transactions.all();
    Serial.println(allTransactions.c_str());
}

void loop() {}
```

**PlatformIO IDE:**  

#### Python:  
Use an installer package from the following link or use your preferred method to install Python:  
```https://www.python.org/downloads/```  

Install PlatformIO:

    pip install -U platformio
or

    python -c "$(curl -fsSL https://raw.githubusercontent.com/platformio/platformio/develop/scripts/get-platformio.py)"

Install ArduinoJson@5.13.2 AUnit (2778)

    platformio lib -g install 64@5.13.2 2778

#### Provide your WiFi info for your board to access the internet   

Open the following file in your preferred code editor:  
> "*.../cpp-client/test/IoT/test_main.cpp*"  

lines 19 & 20:  
```
char ssid[] = "your_ssid";     //  your network SSID (name)
const char password[] = "your_password";  // your network password
```

#### running the tests on an Arduino board

    cd Cpp-Client 
    cd test

#### execute the following command to upload test to your board  

>| board | command |
>|:-- |:-- |
>| ESP8266 | ```pio run -e esp8266 -t upload``` |
>| ESP32 | ```pio run -e esp32 -t upload``` |

#

# OS
## Linux, macOS and Windows

**CMake:**  

Use an installer package from the following link, Homebrew, or use your preferred method:  
```https://www.cmake.org/download/```

using
**Homebrew:**  

    brew install cmake

> note: all other dependencies will be automatically installed via CMake and Hunter Package Manager.

### make and build
    cd cpp-client/  
    cmake . && cmake --build .

### run tests
    ./bin/Ark-Cpp-Client-tests
