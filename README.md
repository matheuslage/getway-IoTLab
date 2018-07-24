# Hardware_RaspberryPi3
## Getway Raspberry Pi 3 via MQTTLens para Bluetooth
### Caracteristicas do Raspberry
- Raspberry Pi 3 Model B
- Processador Broadcom BCM2837 64bit ARMv8 Cortex-A53 Quad-Core
- Clock 1.2 GHz
- Memória RAM: 1GB
- Adaptador Wifi 802.11n integrado
- Bluetooth 4.1 BLE integrado
- Conector de vídeo HDMI
- 4 portas USB 2.0
- Conector Ethernet
- Interface para câmera (CSI)
- Interface para display (DSI)
- Slot para cartão microSD
- Conector de áudio e vídeo
- GPIO de 40 pinos
- Dimensões: 85 x 56 x 17mm

![image](https://cloud.githubusercontent.com/assets/26795001/25636568/2b5887d4-2f58-11e7-90ac-651d2f7d2814.png)
![image](https://cloud.githubusercontent.com/assets/26795001/25636940/6d4f5162-2f59-11e7-916c-526d29b72cf5.png)

* [1º Passo: Instalar o Raspbian no Raspberry pi 3:](#passo1)
* [2º Passo: Configurar o Raspberry pi 3 a uma rede:](#passo2)
* [3º Passo: Configurar o Arduino:](#passo3)
* [4º Passo: Conectando Raspberry pi 3 ao MQTTLens:](#passo4)
* [5º Passo: Mesclando os dois programas](#passo5)

<a name="passo1"></a>
## 1º Passo: Instalar o Raspbian no Raspberry pi 3:
O slot para cartão micro SD é uma parte importante do Raspberry, pois é através de um cartão como esse que iremos instalar o Raspbian, um sistema operacional baseado em Linux e otimizado para uso com o Raspberry. Para instalar o Raspbian no cartão SD ele deve estar vazio.
Vá até a seção de downloads do site oficial do Raspberry Pi Foundation (www.raspberrypi.org) e procure pelo download Raspbian – Raspbian Jessie With Pixel, clique em Download ZIP para baixar o arquivo. Após baixar o arquivo extraia a imagem do Raspbian em uma pasta no computador.

![image](https://cloud.githubusercontent.com/assets/26795001/25637387/2fd73384-2f5b-11e7-96c1-c4a469a5136f.png)

Baixe e instale o utilitário gravador de cartão SD Etcher disponível no link https://etcher.io/ . Execute o Etcher, selecione a imagem do Raspbian extraída, selecione a unidade de cartão micro SD (Talvez o Etcher já tenha selecionado a unidade correta) e logo após clique em flash para instalar o Raspbian no cartão SD. Quando concluído remova o cartão micro SD do computador (é seguro remover o cartão micro SD diretamente porque o Etcher ejeta ou desmonta automaticamente o cartão após a conclusão) e insira o cartão SD no seu Raspberry pi.

![image](https://cloud.githubusercontent.com/assets/26795001/25637476/868213ca-2f5b-11e7-83ee-7ae7129bc5e4.png)

Ligue o Raspberry utilizando uma fonte de alimentação e um cabo micro USB (Observação: É importante usar uma fonte de alimentação do kit que tenha pelo menos 2A para confirmar que o Raspberry será alimentado com capacidade suficiente para funcionar corretamente.).

<a name="passo2"></a>
## 2º Passo: Configurar o Raspberry pi 3 a uma rede:
Em primeiro lugar, deve-se instalar alguns periféricos para facilitar a configuração da placa, tais como: 
•	Teclado USB.
•	Mouse USB.
•	Fonte de alimentação de 5v / 2A com conexão micro USB (Lembre-se que a USB do computador suporta no máximo 500 mA, portanto não ligue o Raspberry diretamente nessa porta).
•	Um monitor de vídeo com entrada HDMI ou DVI.

![image](https://cloud.githubusercontent.com/assets/26795001/25637741/740b5b9c-2f5c-11e7-8a8c-6730c70f87d3.png)

Da versão de novembro de 2016 em diante, o Raspbian tem o servidor SSH desabilitado por padrão. Você precisa habilitá-lo manualmente. Você pode conectar um monitor e ir para Preferências-> Configuração do Raspberry Pi para habilitar o SSH.
Pode-se conectar a Raspberry pi 3 a uma rede com fio ou sem fio. Para conecta-lo a uma rede com fio basta apenas conectar o cabo Ethernet no seu conector, os dois LED’s do pi serão acesos quando a conexão for estabelecida. Para conecta-lo a uma rede sem fio utilizaremos a própria interfase do Raspberry, na área de trabalho do Raspbian, clique no símbolo do Wifi no canto superior direito e conecte-se na rede desejada.

![image](https://cloud.githubusercontent.com/assets/26795001/25637809/a8a7db5a-2f5c-11e7-81b3-596a078e677c.png)

<a name="passo3"></a>
## 3º Passo: Configurar Arduino:

Nesse passo vamos montar o circuito e desenvolver o programa para o controle do Led via bluetooth. Para montar o circuito precisa dos seguintes componentes:


•	Arduino
•	Modulo Bluetooth
•	Led
•	Resistor


Vamos receber comandos da Raspberry Pi para acender e apagar o led via bluetooth. Como alimentação do bluetooth, vamos utilizar os 5V da placa, os pinos RX/TX são colocados invertidos na placa, e o terra no GND da placa.
O led vamos utilizar o pino 13 .

O bluetooth envia os dados para o arduino utilizando os pinos RX/TX, os mesmos da comunicação serial, lembre-se de descontecar o bluetooth para gravar o firmware.

(FOTO DO CIRCUITO)
![image](https://cloud.githubusercontent.com/assets/26795001/25638300/4f4ca6c4-2f5e-11e7-964d-cccce0e6d647.png)

Depois de feita a montagem do circuito vamos baixar o programa a seguir utilizando a IDE do arduino:
```js
//Variaveis que serão acionadas nos pinos do Arduino.
int ledPin = 13;

//Variavel que armazenará letras/números para o acionamento dos leds.
char caracter=0;

void setup() {

// Configuração da velocidade que o módulo Bluetooth ou a porta serial vai trabalhar. 
Serial.begin(9600);
// Configuração como pino de saida
pinMode(ledPin,OUTPUT) ;  
}

void loop() {
    if(Serial.available()){
      caracter = Serial.read();
      Serial.println(caracter);
      if(caracter == 'l'){
        digitalWrite(ledPin,1);
        delay(1000);
        Serial.print("aceso");
      }
      if(caracter == 'd'){
        digitalWrite(ledPin,0);
        delay(1000);
        Serial.print("apagado");
      }
    }
}   
```     

<a name="passo4"></a>
## 4º Passo: Conectando Raspberry pi 3 ao MQTTLens:

Esse passo será dividido em 3 partes de como criar e configurar uma instancia no cloudMQTTLens, como baixar o Paho-MQTT (cliente MQTT oficial) no Raspberry e como rodar o programa no python.
### 1ª Parte: Criando Instancia CloudMQTT

Acesse o site https://www.cloudmqtt.com/ para criar uma nova instância. Logue na sua conta e siga os proximos passos.

Crie uma nova instancia

FOTO CREATE NEW INstance

De um nome para sua instância

FOTO INSTANCIA

Acesse sua instância, e você vai ter acesso as informações necessarias para conectar seu programa a ela.

FOTO PRINT

### 2ª Parte: instalando Paho-MQTT
	
Abra o LX terminal (prompt de comando) do Raspberry e digite os seguintes comandos para clonar o repositório do Paho-MQTT para python.
Lembre-se de instalar o Paho-MQTT na pasta onde vai desenvolver seu projeto.

```js
	git clone https://github.com/eclipse/paho.mqtt.python
	cd paho.mqtt.python
	python setup.py install 
```

Depois desses comandos a instalação do Paho-MQTT está feita e pronta para serem desenvolvidos projetos em python que utilizem o protocolo MQTT.

### 3ª Parte: criando o código

Nessa parte já vamos usar um código pronto, crie um arquivo de texto.py e digite o seguinte código:

Em definições, altere as variaveis de acordo com a instancia criada em sua conta no cloudMQTT

```js
import paho.mqtt.client as mqtt
import RPi.GPIO as GPIO
import sys


#definicoes: 
Broker = "m11.cloudmqtt.com"
PortaBroker = 18548
KeepAliveBroker = 60
TopicoSubscribe = "mac"
TopicoSubscribe2 = "remac" 

#Callback - conexao ao broker realizada
def on_connect(client, userdata, flags, rc):
    print("[STATUS] Conectado ao Broker. Resultado de conexao: "+str(rc))
    #faz subscribe automatico no topico
    client.subscribe(TopicoSubscribe)
    print("[STATUS] Conectado ao Broker. Resultado de conexao: "+str(rc))
    
#Callback - mensagem recebida do broker
def on_message(client, userdata, msg):
	MensagemRecebida = str(msg.payload)
	print("[MSG RECEBIDA] Topico: "+msg.topic+" / Mensagem: "+MensagemRecebida)
	if(MensagemRecebida =='L'):
            GPIO.output(7, 1)
            client.publish(TopicoSubscribe2, 'Led acesso', qos, retain)
        if(MensagemRecebida =='D'):
            GPIO.output(7, 0)
            client.publish(TopicoSubscribe2, 'Led apagado', qos, retain)
	
#programa principal:
if __name__ == '__main__':    
    print("[STATUS] Inicializando MQTT...")
    client = mqtt.Client()
    client.username_pw_set("utjsvywa","asT-f74UJ1Zh")
    client.on_connect = on_connect
    client.on_message = on_message
    client.connect(Broker, PortaBroker, KeepAliveBroker)
    
    
    GPIO.setmode(GPIO.BOARD)
    GPIO.setwarnings(False);
    GPIO.setup(7, GPIO.OUT)
    GPIO.output(7,0)
    
    qos = 1
    retain = False
    publish_delay = 15
    run = True
    while run:
	client.loop()
```

<a name="passo5"></a>
## 5º Passo: Mesclando os dois programas

Esse é o último passo para o Raspberry Pi 3 conseguir enviar e receber as informações para a extensão MQTTLens, o que vamos fazer agora é apenas parear as duas placas.
Usando o programa do último passo iremos incluir algumas coisas tais como biblioteca do sensor, qual pino está sendo usado e fazer ler a temperatura e umidade para mandar ao MQTTLens. Depois de feito tudo isso o código ficara assim:

```js
import paho.mqtt.client as paho
import Adafruit_DHT
import RPi.GPIO as GPIO
import time
from time import gmtime, strftime

sensor = Adafruit_DHT.DHT11
GPIO.setmode(GPIO.BOARD)
pino_sensor = 25

def rc_answers_to_strings(argument):
    switcher = {
        0: "Connection successful",
        1: "Connection refused - incorrect protocol version",
        2: "Connection refused - invalid client identifier",
        3: "Connection refused - server unavailable",
        4: "Connection refused - bad username or password",
        5: "Connection refused - not authorised",
        6-255: "Currently unused",
    }
    return switcher.get(argument, "nothing")

def OnConnectHandler(client, userdata, flags, rc):
        print(rc_answers_to_strings(rc))
        
        topic="IoTLab"
        qos = 1
        
        print("Subscribing to the topic %s with QoS %d" %(topic,qos))
        client.subscribe(topic, qos)


def OnDisconnecthandler(client, userdata, rc): 
#       called when the client disconnects from the broker.
#       The rc parameter indicates the disconnection state. If MQTT_ERR_SUCCESS
#       (0), the callback was called in response to a disconnect() call. If any
#       other value the disconnection was unexpected, such as might be caused by
#       a network error.
    print("Disconnection returned" + str(rc))

def OnMessageHandler(client, userdata, message): 
#     called when a message has been received on a
#     topic that the client subscribes to. The message variable is a
#     MQTTMessage that describes all of the message parameters.
    print("###################################")
    print("New message received:")
    print("Topic: " + str(message.topic))
    print("QoS: " + str(message.qos))
    print("Payload: " + str(message.payload))
    print("###################################")

def OnPublishHandler(client, userdata, mid): 
#       called when a message that was to be sent using the
#       publish() call has completed transmission to the broker. For messages
#       with QoS levels 1 and 2, this means that the appropriate handshakes have
#       completed. For QoS 0, this simply means that the message has left the
#       client. The mid variable matches the mid variable returned from the
#       corresponding publish() call, to allow outgoing messages to be tracked.
#       This callback is important because even if the publish() call returns
#       success, it does not always mean that the message has been sent.
    print("Publish approved!")

def OnSubscribeHandler(client, userdata, mid, granted_qos): 
#       called when the broker responds to a
#       subscribe request. The mid variable matches the mid variable returned
#       from the corresponding subscribe() call. The granted_qos variable is a
#       list of integers that give the QoS level the broker has granted for each
#       of the different subscription requests.
    print("Subscribe successful with QoS: " + str(granted_qos))

def OnUnsubscribeHandler(client, userdata, mid): 
#     called when the broker responds to an unsubscribe
#       request. The mid variable matches the mid variable returned from the
#       corresponding unsubscribe() call.
    print("Unsubscription returned ")

def OnLogHandler(client, userdata, level, buf): 
#     called when the client has log information. Define
#       to allow debugging. The level variable gives the severity of the message
#       and will be one of MQTT_LOG_INFO, MQTT_LOG_NOTICE, MQTT_LOG_WARNING,
#       MQTT_LOG_ERR, and MQTT_LOG_DEBUG. The message itself is in buf.
    print("Log: " + str(buf))


if __name__ == '__main__':
    
    client = paho.Client()
    
    client.on_connect = OnConnectHandler
    client.on_disconnect = OnDisconnecthandler
    client.on_message = OnMessageHandler
    client.on_subscribe = OnSubscribeHandler
    client.on_publish = OnPublishHandler
    
    host="iot.eclipse.org"
    port = 1883
    keepalive=60
    bind_address=""
    
    print("Trying to connect to %s" %host)
    
    client.connect(host, port, keepalive, bind_address)
    
    topic = "raspberry"
    payload_str = "28"
    qos = 1
    retain = False
    publish_delay = 10
    
     
    run = True
    while run:
       client.loop()
       # Efetua a leitura do sensor
       umid, temp = Adafruit_DHT.read_retry(sensor, pino_sensor);
       # Caso leitura esteja ok, mostra os valores na tela
       if umid is not None and temp is not None:
                #  time.sleep(20)
         payload_str = ("Temperatura = {0:0.1f}  Umidade = {1:0.1f}\n").format(temp, umid);
         payload = strftime("%a, %d %b %Y %H:%M:%S +0000, ", gmtime()) + payload_str   
       if(publish_delay < 1):
            print("Publishing new data on %s" %topic)
            #payload = strftime("%a, %d %b %Y %H:%M:%S +0000, ", gmtime()) + payload_str
            client.publish(topic, payload_str, qos, retain)
            publish_delay=5
       else:    
            publish_delay=publish_delay-1
       print("Remaining time for new publishing: %d" %(publish_delay))
```

Depois de digitado, salve e pressione F5 para rodar o programa.



