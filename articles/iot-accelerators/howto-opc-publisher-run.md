---
title: Execute o publicador OPC - Azure | Documentos da Microsoft
description: Executar o Publicador OPC
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 3b386171afc7916e5e803c39a9c7b3520752e6fd
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603753"
---
# <a name="run-opc-publisher"></a>Executar o Publicador OPC

Este artigo descreve como executar a depuração de ad o publicador OPC. Ele também aborda considerações de desempenho e memória.

## <a name="command-line-options"></a>Opções da linha de comandos

Utilização da aplicação é apresentada com o `--help` opção da linha de comandos da seguinte forma:

```sh/cmd
Current directory is: /appdata
Log file is: <hostname>-publisher.log
Log level is: info

OPC Publisher V2.3.0
Informational version: V2.3.0+Branch.develop_hans_methodlog.Sha.0985e54f01a0b0d7f143b1248936022ea5d749f9

Usage: opcpublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]

OPC Edge Publisher to subscribe to configured OPC UA servers and send telemetry to Azure IoT Hub.
To exit the application, just press CTRL-C while it is running.

applicationname: the OPC UA application name to use, required
                  The application name is also used to register the publisher under this name in the
                  IoT Hub device registry.

IoT Hubconnectionstring: the IoT Hub owner connectionstring, optional

There are a couple of environment variables which can be used to control the application:
_HUB_CS: sets the IoT Hub owner connectionstring
_GW_LOGP: sets the filename of the log file to use
_TPC_SP: sets the path to store certificates of trusted stations
_GW_PNFP: sets the filename of the publishing configuration file

Command line arguments overrule environment variable settings.

Options:
      --pf, --publishfile=VALUE
                              the filename to configure the nodes to publish.
                                Default: '/appdata/publishednodes.json'
      --tc, --telemetryconfigfile=VALUE
                              the filename to configure the ingested telemetry
                                Default: ''
  -s, --site=VALUE           the site OPC Publisher is working in. if specified
                                this domain is appended (delimited by a ':' to
                                the 'ApplicationURI' property when telemetry is
                                sent to IoT Hub.
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --ic, --iotcentral     publisher will send OPC UA data in IoTCentral
                                compatible format (DisplayName of a node is used
                                as key, this key is the Field name in IoTCentral)
                                . you need to ensure that all DisplayName's are
                                unique. (Auto enables fetch display name)
                                Default: False
      --sw, --sessionconnectwait=VALUE
                              specify the wait time in seconds publisher is
                                trying to connect to disconnected endpoints and
                                starts monitoring unmonitored items
                                Min: 10
                                Default: 10
      --mq, --monitoreditemqueuecapacity=VALUE
                              specify how many notifications of monitored items
                                can be stored in the internal queue, if the data
                                can not be sent quick enough to IoT Hub
                                Min: 1024
                                Default: 8192
      --di, --diagnosticsinterval=VALUE
                              shows publisher diagnostic info at the specified
                                interval in seconds (need log level info).
                                -1 disables remote diagnostic log and diagnostic
                                output
                                0 disables diagnostic output
                                Default: 0
      --ns, --noshutdown=VALUE
                              same as runforever.
                                Default: False
      --rf, --runforever     publisher can not be stopped by pressing a key on
                                the console, but will run forever.
                                Default: False
      --lf, --logfile=VALUE  the filename of the logfile to use.
                                Default: './<hostname>-publisher.log'
      --lt, --logflushtimespan=VALUE
                              the timespan in seconds when the logfile should be
                                flushed.
                                Default: 00:00:30 sec
      --ll, --loglevel=VALUE the loglevel to use (allowed: fatal, error, warn,
                                info, debug, verbose).
                                Default: info
        --ih, --IoT Hubprotocol=VALUE
                              the protocol to use for communication with IoT Hub (
                                allowed values: Amqp, Http1, Amqp_WebSocket_Only,
                                  Amqp_Tcp_Only, Mqtt, Mqtt_WebSocket_Only, Mqtt_
                                Tcp_Only) or IoT EdgeHub (allowed values: Mqtt_
                                Tcp_Only, Amqp_Tcp_Only).
                                Default for IoT Hub: Mqtt_WebSocket_Only
                                Default for IoT EdgeHub: Amqp_Tcp_Only
      --ms, --IoT Hubmessagesize=VALUE
                              the max size of a message which can be send to
                                IoT Hub. when telemetry of this size is available
                                it will be sent.
                                0 will enforce immediate send when telemetry is
                                available
                                Min: 0
                                Max: 262144
                                Default: 262144
      --si, --IoT Hubsendinterval=VALUE
                              the interval in seconds when telemetry should be
                                send to IoT Hub. If 0, then only the
                                IoT Hubmessagesize parameter controls when
                                telemetry is sent.
                                Default: '10'
      --dc, --deviceconnectionstring=VALUE
                              if publisher is not able to register itself with
                                IoT Hub, you can create a device with name <
                                applicationname> manually and pass in the
                                connectionstring of this device.
                                Default: none
  -c, --connectionstring=VALUE
                              the IoT Hub owner connectionstring.
                                Default: none
      --hb, --heartbeatinterval=VALUE
                              the publisher is using this as default value in
                                seconds for the heartbeat interval setting of
                                nodes without
                                a heartbeat interval setting.
                                Default: 0
      --sf, --skipfirstevent=VALUE
                              the publisher is using this as default value for
                                the skip first event setting of nodes without
                                a skip first event setting.
                                Default: False
      --pn, --portnum=VALUE  the server port of the publisher OPC server
                                endpoint.
                                Default: 62222
      --pa, --path=VALUE     the enpoint URL path part of the publisher OPC
                                server endpoint.
                                Default: '/UA/Publisher'
      --lr, --ldsreginterval=VALUE
                              the LDS(-ME) registration interval in ms. If 0,
                                then the registration is disabled.
                                Default: 0
      --ol, --opcmaxstringlen=VALUE
                              the max length of a string opc can transmit/
                                receive.
                                Default: 131072
      --ot, --operationtimeout=VALUE
                              the operation timeout of the publisher OPC UA
                                client in ms.
                                Default: 120000
      --oi, --opcsamplinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds to request the servers to sample
                                the nodes with this interval
                                this value might be revised by the OPC UA
                                servers to a supported sampling interval.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a negative value will set the sampling interval
                                to the publishing interval of the subscription
                                this node is on.
                                0 will configure the OPC UA server to sample in
                                the highest possible resolution and should be
                                taken with care.
                                Default: 1000
      --op, --opcpublishinginterval=VALUE
                              the publisher is using this as default value in
                                milliseconds for the publishing interval setting
                                of the subscriptions established to the OPC UA
                                servers.
                                please check the OPC UA specification for
                                details how this is handled by the OPC UA stack.
                                a value less than or equal zero will let the
                                server revise the publishing interval.
                                Default: 0
      --ct, --createsessiontimeout=VALUE
                              specify the timeout in seconds used when creating
                                a session to an endpoint. On unsuccessful
                                connection attemps a backoff up to 5 times the
                                specified timeout value is used.
                                Min: 1
                                Default: 10
      --ki, --keepaliveinterval=VALUE
                              specify the interval in seconds the publisher is
                                sending keep alive messages to the OPC servers
                                on the endpoints it is connected to.
                                Min: 2
                                Default: 2
      --kt, --keepalivethreshold=VALUE
                              specify the number of keep alive packets a server
                                can miss, before the session is disconneced
                                Min: 1
                                Default: 5
      --aa, --autoaccept     the publisher trusts all servers it is
                                establishing a connection to.
                                Default: False
      --tm, --trustmyself=VALUE
                              same as trustowncert.
                                Default: False
      --to, --trustowncert   the publisher certificate is put into the trusted
                                certificate store automatically.
                                Default: False
      --fd, --fetchdisplayname=VALUE
                              same as fetchname.
                                Default: False
      --fn, --fetchname      enable to read the display name of a published
                                node from the server. this will increase the
                                runtime.
                                Default: False
      --ss, --suppressedopcstatuscodes=VALUE
                              specifies the OPC UA status codes for which no
                                events should be generated.
                                Default: BadNoCommunication,
                                BadWaitingForInitialData
      --at, --appcertstoretype=VALUE
                              the own application cert store type.
                                (allowed values: Directory, X509Store)
                                Default: 'Directory'
      --ap, --appcertstorepath=VALUE
                              the path where the own application cert should be
                                stored
                                Default (depends on store type):
                                X509Store: 'CurrentUser\UA_MachineDefault'
                                Directory: 'pki/own'
      --tp, --trustedcertstorepath=VALUE
                              the path of the trusted cert store
                                Default: 'pki/trusted'
      --rp, --rejectedcertstorepath=VALUE
                              the path of the rejected cert store
                                Default 'pki/rejected'
      --ip, --issuercertstorepath=VALUE
                              the path of the trusted issuer cert store
                                Default 'pki/issuer'
      --csr                  show data to create a certificate signing request
                                Default 'False'
      --ab, --applicationcertbase64=VALUE
                              update/set this applications certificate with the
                                certificate passed in as bas64 string
      --af, --applicationcertfile=VALUE
                              update/set this applications certificate with the
                                certificate file specified
      --pb, --privatekeybase64=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as base64 string
      --pk, --privatekeyfile=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as file
      --cp, --certpassword=VALUE
                              the optional password for the PEM or PFX or the
                                installed application certificate
      --tb, --addtrustedcertbase64=VALUE
                              adds the certificate to the applications trusted
                                cert store passed in as base64 string (multiple
                                strings supported)
      --tf, --addtrustedcertfile=VALUE
                              adds the certificate file(s) to the applications
                                trusted cert store passed in as base64 string (
                                multiple filenames supported)
      --ib, --addissuercertbase64=VALUE
                              adds the specified issuer certificate to the
                                applications trusted issuer cert store passed in
                                as base64 string (multiple strings supported)
      --if, --addissuercertfile=VALUE
                              adds the specified issuer certificate file(s) to
                                the applications trusted issuer cert store (
                                multiple filenames supported)
      --rb, --updatecrlbase64=VALUE
                              update the CRL passed in as base64 string to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --uc, --updatecrlfile=VALUE
                              update the CRL passed in as file to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --rc, --removecert=VALUE
                              remove cert(s) with the given thumbprint(s) (
                                multiple thumbprints supported)
      --dt, --devicecertstoretype=VALUE
                              the IoT Hub device cert store type.
                                (allowed values: Directory, X509Store)
                                Default: X509Store
      --dp, --devicecertstorepath=VALUE
                              the path of the iot device cert store
                                Default Default (depends on store type):
                                X509Store: 'My'
                                Directory: 'CertificateStores/IoT Hub'
  -i, --install              register OPC Publisher with IoT Hub and then exits.
                                Default:  False
  -h, --help                 show this message and exit
      --st, --opcstacktracemask=VALUE
                              ignored, only supported for backward comaptibility.
      --sd, --shopfloordomain=VALUE
                              same as site option, only there for backward
                                compatibility
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --vc, --verboseconsole=VALUE
                              ignored, only supported for backward comaptibility.
      --as, --autotrustservercerts=VALUE
                              same as autoaccept, only supported for backward
                                cmpatibility.
                                Default: False
      --tt, --trustedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted cert store will always reside in a
                                directory.
      --rt, --rejectedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the rejected cert store will always reside in a
                                directory.
      --it, --issuercertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted issuer cert store will always
                                reside in a directory.
```

Normalmente, especificar a cadeia de ligação do IoT Hub proprietário apenas na primeira execução do aplicativo. A cadeia de ligação é encriptada e armazenada no arquivo de certificados de plataforma. Nas execuções posteriores, o aplicativo lê a cadeia de ligação do arquivo de certificados. Se especificar a cadeia de ligação em cada execução, o dispositivo é criado para a aplicação no registo do dispositivo IoT Hub é removido e recriado.

## <a name="run-natively-on-windows"></a>Executar nativamente no Windows

Abra o **opcpublisher.sln** do projeto com o Visual Studio, crie a solução e publicá-lo. Pode iniciar o aplicativo no **diretório de destino** publicou da seguinte forma:

```cmd
dotnet opcpublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-self-built-container"></a>Utilizar um contentor criada automaticamente

Criar seu próprio contentor e inicie-o da seguinte forma:

```sh/cmd
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-container-from-microsoft-container-registry"></a>Utilizar um contentor de registo de contentores da Microsoft

Existe um contentor pré-criadas disponíveis no registo de contentor do Microsoft. Inicie-o da seguinte forma:

```sh/cmd
docker run mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Verifique [Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) para ver os sistemas operativos e arquiteturas de processador. Se sua arquitetura de sistema operacional e da CPU for suportada, o Docker seleciona automaticamente o contentor correto.

## <a name="run-as-an-azure-iot-edge-module"></a>Executar como um módulo do Azure IoT Edge

O publicador OPC está pronto para ser utilizado como um [do Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge) módulo. Quando utilizar o publicador OPC como módulo do IoT Edge, o único suportado são protocolos de transporte **Amqp_Tcp_Only** e **Mqtt_Tcp_Only**.

Para adicionar o publicador OPC como módulo para a sua implementação do IoT Edge, vá para as definições do IoT Hub no portal do Azure e conclua os seguintes passos:

1. Aceda a **IoT Edge** e crie ou selecione o seu dispositivo IoT Edge.
1. Selecione **Definir Módulos**.
1. Selecione **adicione** sob **módulos de implementação** e, em seguida **módulo do IoT Edge**.
1. Na **Name** , insira **publicador**.
1. Na **URI da imagem** , insira `mcr.microsoft.com/iotedge/opc-publisher:<tag>`
1. Pode encontrar as etiquetas disponíveis no [Hub do Docker](https://hub.docker.com/_/microsoft-iotedge-opc-publisher)
1. Cole o seguinte JSON para o **opções de criar contentor** campo:

    ```json
    {
        "Hostname": "publisher",
        "Cmd": [
            "--aa"
        ]
    }
    ```

    Esta configuração configura o IoT Edge para iniciar um contentor chamado **publicador** com a imagem de publicador OPC. O nome de anfitrião do sistema do contentor está definido como **publicador**. O publicador OPC denomina-se com o argumento da linha de comandos seguinte: `--aa`. Com esta opção, o publicador OPC confia em certificados dos servidores OPC UA liga-se ao. Pode utilizar as opções de linha de comandos de publicador OPC. A única limitação é o tamanho do **opções de criar contentor** suportados pelo IoT Edge.

1. Deixe as outras definições inalteradas e selecione **Guardar**.
1. Se quiser processar a saída do publicador OPC localmente com outro módulo do IoT Edge, voltar para o **definir módulos** página. Em seguida, vá para o **especificar rotas** separador e adicionar uma nova rota que se parece com o seguinte JSON:

    ```json
    {
      "routes": {
        "processingModuleToIoT Hub": "FROM /messages/modules/processingModule/outputs/* INTO $upstream",
        "opcPublisherToProcessingModule": "FROM /messages/modules/publisher INTO BrokeredEndpoint(\"/modules/processingModule/inputs/input1\")"
      }
    }
    ```

1. De volta a **definir módulos** página, selecione **próxima**, até chegar à última página da configuração.
1. Selecione **submeter** para enviar a sua configuração para o IoT Edge.
1. Quando tiver iniciado o IoT Edge no seu dispositivo edge e o contentor do docker **publicador** está em execução, pode consultar a saída de registo de publicador OPC utilizando `docker logs -f publisher` ou ao verificar o ficheiro de registo. No exemplo anterior, o ficheiro de registo está acima `d:\iiotegde\publisher-publisher.log`. Também pode utilizar o [ferramenta de iot-edge-opc-Editor-diagnostics](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics).

### <a name="make-the-configuration-files-accessible-on-the-host"></a>Tornar os ficheiros de configuração acessíveis no anfitrião

Para tornar o acessível no sistema de arquivos de anfitrião de ficheiros de configuração do módulo do IoT Edge, utilize o seguinte procedimento **opções de criar contentor**. O exemplo seguinte é de uma implementação através de contentores do Linux para Windows:

```json
{
    "Hostname": "publisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "d:/iiotedge:/appdata"
        ]
    }
}
```

Com estas opções, o publicador OPC lê os nós, deve publicar a partir do ficheiro `./pn.json` e o diretório de trabalho do contentor é definido como `/appdata` na inicialização. Com estas definições, o publicador OPC lê o arquivo `/appdata/pn.json` do contêiner para obter a respetiva configuração. Sem o `--pf` opção, o publicador OPC tenta ler o ficheiro de configuração padrão `./publishednodes.json`.

O ficheiro de registo, usando o nome padrão `publisher-publisher.log`, é escrito `/appdata` e o `CertificateStores` diretório também é criado neste diretório.

Para disponibilizar todos esses arquivos no sistema de arquivos de anfitrião, a configuração do contentor necessita de um volume de montagem de enlace. O `d://iiotedge:/appdata` bind mapeia o diretório `/appdata`, que é o atual diretório de trabalho na inicialização do contentor, para o diretório de anfitrião `d://iiotedge`. Sem esta opção, não existem dados de ficheiro são mantidos quando o contentor começa a seguir.

Se estiver a executar contentores do Windows, em seguida, a sintaxe do `Binds` parâmetro é diferente. Na inicialização do contentor, o diretório de trabalho é `c:\appdata`. Para colocar o ficheiro de configuração no diretório `d:\iiotedge`no anfitrião, especifique o seguinte mapeamento no `HostConfig` secção:

```json
"HostConfig": {
    "Binds": [
        "d:/iiotedge:c:/appdata"
    ]
}
```

Se estiver a executar contentores do Linux no Linux, a sintaxe do `Binds` parâmetro novamente é diferente. Na inicialização do contentor, o diretório de trabalho é `/appdata`. Para colocar o ficheiro de configuração no diretório `/iiotedge` no anfitrião, especifique o seguinte mapeamento no `HostConfig` secção:

```json
"HostConfig": {
    "Binds": [
        "/iiotedge:/appdata"
    ]
}
```

## <a name="considerations-when-using-a-container"></a>Considerações sobre quando utilizar um contentor

As secções seguintes listam alguns aspetos a ter em mente quando utiliza um contentor:

### <a name="access-to-the-opc-publisher-opc-ua-server"></a>Acesso ao servidor OPC publicador OPC UA

Por predefinição, o servidor OPC UA publicador OPC escuta na porta 62222. Para expor essa porta de entrada num contentor, utilize o seguinte comando:

```sh/cmd
docker run -p 62222:62222 mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="enable-intercontainer-name-resolution"></a>Habilitar a resolução de nome intercontainer

Para permitir a resolução do nome de dentro do contêiner para outros contentores, crie um usuário definir a rede de bridge do docker e ligar o contentor para esta rede com o `--network` opção. Também atribuir o contentor de um nome a utilizar o `--name` opção da seguinte forma:

```sh/cmd
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

O contentor está agora acessível através do nome `publisher` por outros contentores na mesma rede.

### <a name="access-other-systems-from-within-the-container"></a>Aceder a outros sistemas de dentro do contentor

Outros contentores podem ser contactados através de parâmetros descritos na secção anterior. Se o sistema operativo no qual o Docker está alojado é ativado de DNS, em seguida, aceder a todos os sistemas que são conhecidos por DNS funciona.

Em redes que usam a resolução de nomes NetBIOS, ativar o acesso a outros sistemas ao iniciar o contentor com o `--add-host` opção. Esta opção adiciona efetivamente uma entrada ao ficheiro de anfitrião do contentor:

```cmd/sh
docker run --add-host mydevbox:192.168.178.23  mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="assign-a-hostname"></a>Atribuir um nome de anfitrião

O publicador OPC utiliza o nome do anfitrião da máquina está em execução para a geração do certificado e ponto final. Docker escolhe um nome de anfitrião aleatório, se não está definidos pelo `-h` opção. O exemplo seguinte mostra como definir o nome interno do anfitrião do contentor como `publisher`:

```sh/cmd
docker run -h publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-bind-mounts-shared-filesystem"></a>Utilizar o enlace monta (sistema de ficheiros partilhado)

Em vez de usar o sistema de ficheiros de contentor, pode optar pelo anfitrião do sistema de ficheiros para ficheiros de registo e armazenar informações de configuração. Para configurar esta opção, utilize o `-v` opção de `docker run` no modo de montagem de enlace.

## <a name="opc-ua-x509-certificates"></a>Certificados OPC UA X.509

OPC UA que usa certificados X.509 para autenticar o cliente de OPC UA e o servidor quando estes estabeleçam uma ligação e para encriptar a comunicação entre eles. O publicador OPC usa arquivos de certificados mantidos pela pilha OPC UA para gerenciar todos os certificados. Na inicialização, o publicador OPC verifica se existe um certificado para si mesmo. Se não é nenhum certificado no arquivo de certificados e não é uma passado na linha de comando, o publicador OPC cria um certificado autoassinado. Para obter mais informações, consulte a **InitApplicationSecurityAsync** método na `OpcApplicationConfigurationSecurity.cs`.

Certificados autoassinados não fornecem nenhuma segurança, à medida que não sejam assinados por uma AC fidedigna.

O publicador OPC oferece opções de linha de comandos para:

- Obter as informações de CSR do certificado de aplicação atual utilizada pelo publicador OPC.
- Aprovisionar o publicador OPC com acesso condicional para um certificado autoassinado.
- Aprovisionar o publicador OPC com um novo par de chaves e a AC de correspondência de certificado autoassinado.
- Adicione certificados a um elemento de rede fidedigno ou o arquivo de certificados de emissor fidedigno.
- Adicione uma CRL.
- Remover um certificado do elemento de rede fidedigno ou arquivo de certificados de emissores fidedignos.

Todas essas opções permitem-lhe transmitir parâmetros de uso de arquivos ou cadeias de caracteres codificada em base64.

O tipo de arquivo padrão para todos os arquivos de certificados é o sistema de ficheiros, que pode alterar a utilizar as opções de linha de comandos. Uma vez que o contentor não fornece armazenamento persistente no seu sistema de ficheiros, tem de escolher um tipo de arquivo diferente. Utilizar o Docker `-v` opção para manter o certificado armazena no sistema de arquivos de anfitrião ou num Docker volume. Se usar um volume de Docker, pode passar certificados através de cadeias de caracteres codificada em base64.

O ambiente de tempo de execução afeta a forma como os certificados são mantidos. Evite a criação de novos arquivos de certificados sempre que executar a aplicação:

- Executar nativamente no Windows, não é possível utilizar um arquivo de certificados de aplicação do tipo `Directory` porque o acesso à chave privada falha. Neste caso, utilize a opção `--at X509Store`.
- Em execução como o contentor de docker do Linux, pode mapear os arquivos de certificados para o sistema de ficheiros do anfitrião com o docker a executar a opção `-v <hostdirectory>:/appdata`. Esta opção faz o certificado persistente nas execuções do aplicativo.
- Em execução como Linux contentor do docker e quiser utilizar uma X509 armazenar o certificado de aplicação, utilize o docker a executar a opção `-v x509certstores:/root/.dotnet/corefx/cryptography/x509stores` e a opção de aplicações `--at X509Store`

## <a name="performance-and-memory-considerations"></a>Considerações sobre desempenho e memória

Esta secção descreve as opções para o gerenciamento de memória e desempenho:

### <a name="command-line-parameters-to-control-performance-and-memory"></a>Parâmetros da linha de comandos para o controlo de desempenho e memória

Quando executa o publicador OPC, precisa estar atento a seus requisitos de desempenho e os recursos de memória disponíveis no seu anfitrião.

Memória e desempenho são interdependentes e ambos dependem da configuração de quantos nós que configurar para publicar. Certifique-se de que os seguintes parâmetros de satisfazer os seus requisitos:

- IoT Hub envia intervalo: `--si`
- Tamanho de mensagem do IoT Hub (predefinição `1`): `--ms`
- Capacidade de fila de itens monitorizados: `--mq`

O `--mq` parâmetro controla o limite superior da capacidade de fila interna, o que provoca a todas as notificações de alteração do valor de nó do OPC. Se o publicador OPC não é possível enviar mensagens para o IoT Hub rapidez necessária, este buffers de fila as notificações. O parâmetro define o número de notificações que podem ser colocados em memória intermédia. Se vir o número de itens nesta fila aumentando de suas execuções de teste, em seguida, para evitar perder mensagens deve:

- Reduzir o intervalo de envio do IoT Hub
- Aumentar o tamanho de mensagem do IoT Hub

O `--si` parâmetro força o publicador de OPC para enviar mensagens para o IoT Hub, o intervalo especificado. O publicador OPC envia uma mensagem assim que o tamanho da mensagem especificado pelos `--ms` parâmetro é atingido ou logo que o intervalo especificado pelo `--si` parâmetro for atingido. Para desativar a opção de tamanho de mensagem, utilize `--ms 0`. Neste caso, o publicador OPC usa o maior tamanho de mensagem de IoT Hub possíveis de 256 kB de dados de lotes.

O `--ms` permite de parâmetro que as mensagens enviadas ao IoT Hub do batch. O protocolo que está a utilizar determina se o overhead do envio uma mensagem para o IoT Hub é alta em comparação com a hora real do envio da carga. Se o seu cenário permite latência quando os dados ingeridos pelo IoT Hub, configure o publicador de OPC para utilizar o maior tamanho de mensagem de 256 kB.

Antes de utilizar o publicador OPC em cenários de produção, teste o desempenho e a utilização de memória em condições de produção. Pode utilizar o `--di` parâmetro para especificar o intervalo em segundos, que o publicador OPC escreve informações de diagnóstico.

### <a name="test-measurements"></a>Medições de teste

O diagnóstico de exemplo seguinte mostra medidas com valores diferentes para `--si` e `--ms` parâmetros de publicação de 500 nós com um intervalo de publicação do OPC de 1 segundo.  O teste utilizou uma compilação de depuração de publicador OPC no Windows 10 nativamente para 120 segundos. O protocolo de IoT Hub foi o protocolo MQTT de predefinição.

#### <a name="default-configuration---si-10---ms-262144"></a>Configuração predefinida (– is 10--ms 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:33:05 (started @ 26.10.2017 15:31:09)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54363
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54363
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:33:04
bytes sent to IoT Hub: 12709429
avg msg size: 116600
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 10
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

A configuração predefinida envia dados para o IoT Hub cada 10 segundos, ou quando 256 kB de dados está disponível para o IoT Hub ingerir. Esta configuração adiciona uma latência moderada de cerca de 10 segundos, mas tem menor probabilidade de perder dados devido ao tamanho grande de mensagem. O resultado de diagnóstico mostra que existem não existem atualizações do nó OPC perdidas: `monitored item notifications enqueue failure: 0`.

#### <a name="constant-send-interval---si-1---ms-0"></a>Intervalo de envio constante (– is 1-- ms 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:35:59 (started @ 26.10.2017 15:34:03)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54243
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54243
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:35:59
bytes sent to IoT Hub: 12683836
avg msg size: 116365
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 1
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

Quando o tamanho da mensagem é definido como 0, em seguida, o publicador OPC internamente dos lotes com o maior suportado IoT Hub tamanho da mensagem, que é 256 kB de dados. A saída de diagnóstico mostra que o tamanho de mensagem médio é 115,019 bytes. Nesta configuração o publicador OPC não perder qualquer nó OPC de atualizações de valor e, em comparação com o padrão tem uma latência mais baixa.

### <a name="send-each-opc-node-value-update---si-0---ms-0"></a>Enviar cada atualização de valor do nó OPC (– is 0--ms 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:39:33 (started @ 26.10.2017 15:37:37)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 8184
monitored item notifications enqueued: 54232
monitored item notifications enqueue failure: 44624
monitored item notifications dequeued: 1424
---------------------------------
messages sent to IoT Hub: 1423
last successful msg sent @: 26.10.2017 15:39:33
bytes sent to IoT Hub: 333046
avg msg size: 234
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 96
--si setting: 0
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

Esta configuração envia para cada valor do nó OPC uma mensagem de alteração para o IoT Hub. O show do diagnóstico do tamanho de mensagem médio é 234 bytes, que é pequeno. A vantagem desta configuração é que o publicador OPC não adiciona qualquer latência. O número de atualizações de valor de nó OPC perdidos (`monitored item notifications enqueue failure: 44624`) é elevada, que torna esta configuração não são adequados para cenários com grandes volumes de telemetria a ser publicado.

### <a name="maximum-batching---si-0---ms-262144"></a>Criação de batches máximo (-0--ms 262144 de si)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:42:55 (started @ 26.10.2017 15:41:00)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54137
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54137
---------------------------------
messages sent to IoT Hub: 48
last successful msg sent @: 26.10.2017 15:42:55
bytes sent to IoT Hub: 12565544
avg msg size: 261782
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 0
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

Esta configuração dos lotes tantas atualizações de valor do nó OPC possível. O tamanho de mensagem do IoT Hub máximo é 256 kB, o que está configurada aqui. Não existe nenhum intervalo de envio solicitada, o que significa que a quantidade de dados para o IoT Hub ingerir determina a latência. Esta configuração tem a menor probabilidade de perder quaisquer valores de nó OPC e é adequada para a publicação de um elevado número de nós. Quando utiliza esta configuração, certifique-se de que seu cenário não tem condições em que foi introduzida a alta latência se não for atingido o tamanho da mensagem de 256 kB.

## <a name="debug-the-application"></a>Depurar a aplicação

Para depurar a aplicação, abra a **opcpublisher.sln** solução arquivo com o Visual Studio e usar as ferramentas de depuração do Visual Studio.

Se precisar de aceder ao servidor OPC UA no publicador OPC, certifique-se de que a firewall permite o acesso à porta que o servidore escutar. A porta predefinida é: 62222.

## <a name="control-the-application-remotely"></a>Controlar o aplicativo remotamente

Configurar os nós para publicar pode ser feito através do IoT Hub métodos diretos.

O publicador OPC implementa algumas chamadas de método direto IoT Hub adicionais para ler:

- Informações gerais.
- Informações de diagnóstico em sessões OPC, subscrições e itens monitorizados.
- Informações de diagnóstico em mensagens do IoT Hub e eventos.
- O registo de arranque.
- As últimas 100 linhas do registo.
- Encerre o aplicativo.

Os seguintes repositórios de GitHub contêm ferramentas para [configurar os nós para publicar](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) e [ler as informações de diagnóstico](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics). Ambas as ferramentas também estão disponíveis como contentores do Docker Hub.

## <a name="use-a-sample-opc-ua-server"></a>Utilizar um servidor OPC UA de exemplo

Se não tiver um servidor OPC UA real, pode utilizar o [OPC UA PLC de exemplo](https://github.com/Azure-Samples/iot-edge-opc-plc) para começar a utilizar. Este exemplo PLC também está disponível no Docker Hub.

Ele implementa um número de etiquetas, o que gerar dados aleatórios e etiquetas com anomalias. Pode estender o exemplo se tiver de simular os valores de marca adicionais.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu como executar o publicador OPC, as próximas etapas recomendadas são para saber mais sobre [OPC duplo](overview-opc-twin.md) e [OPC cofre](overview-opc-vault.md).
