---
title: Run OPC Publisher - Azure | Microsoft Docs
description: Este artigo descreve como executar e depurar a Editora OPC. Aborda também considerações de desempenho e memória.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f7d6581a1892ebd74a1adba5c09c0af9d3cf9d43
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646292"
---
# <a name="run-opc-publisher"></a>Executar o Publicador OPC

> [!IMPORTANT]
> Enquanto atualizamos este artigo, consulte [a Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

Este artigo descreve como executar ad debug OPC Publisher. Aborda também considerações de desempenho e memória.

## <a name="command-line-options"></a>Opções da linha de comandos

A utilização da aplicação é mostrada utilizando a `--help` opção de linha de comando da seguinte forma:

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

Normalmente, especifica a cadeia de ligação do proprietário do IoT Hub apenas na primeira execução da aplicação. A cadeia de ligação é encriptada e armazenada na loja de certificados da plataforma. Em execuções posteriores, a aplicação lê a cadeia de ligação da loja de certificados. Se especificar a cadeia de ligação em cada execução, o dispositivo criado para a aplicação no registo do dispositivo IoT Hub é removido e recriado.

## <a name="run-natively-on-windows"></a>Executar de forma nativa no Windows

Abra o **projeto opcpublisher.sln** com o Visual Studio, construa a solução e publique-a. Pode iniciar a aplicação no **diretório target** que publicou para o seguinte:

```cmd
dotnet opcpublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-self-built-container"></a>Use um recipiente auto-construído

Construa o seu próprio recipiente e inicie-o da seguinte forma:

```sh/cmd
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-container-from-microsoft-container-registry"></a>Utilize um recipiente do Registo de Contentores da Microsoft

Há um contentor pré-construído disponível no Registo de Contentores da Microsoft. Comece da seguinte forma:

```sh/cmd
docker run mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

Consulte o [Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) para ver os sistemas operativos suportados e as arquiteturas de processadores. Se a sua arquitetura de SO e CPU for suportada, o Docker seleciona automaticamente o recipiente correto.

## <a name="run-as-an-azure-iot-edge-module"></a>Corra como um módulo Azure IoT Edge

O OPC Publisher está pronto para ser usado como um módulo [Azure IoT Edge.](../iot-edge/index.yml) Quando utiliza o OPC Publisher como módulo IoT Edge, os únicos protocolos de transporte suportados são **Amqp_Tcp_Only** e **Mqtt_Tcp_Only**.

Para adicionar o OPC Publisher como módulo à sua implementação IoT Edge, aceda às definições do IoT Hub no portal Azure e complete os seguintes passos:

1. Vá ao **IoT Edge** e crie ou selecione o seu dispositivo IoT Edge.
1. Selecione **módulos de conjunto**.
1. **Selecione Adicionar** em **Módulos de Implementação** e, em seguida, **Módulo de Borda IoT**.
1. No campo **Nome,** **insira a editora.**
1. No campo **Image URI,** insira `mcr.microsoft.com/iotedge/opc-publisher:<tag>`
1. Pode encontrar as etiquetas disponíveis no [Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher)
1. Cole o seguinte JSON no campo **Opções de Criação de Recipientes:**

    ```json
    {
        "Hostname": "publisher",
        "Cmd": [
            "--aa"
        ]
    }
    ```

    Esta configuração configura o IoT Edge para iniciar um recipiente chamado **editor** utilizando a imagem da Editora OPC. O nome de anfitrião do sistema do contentor está definido para **o editor**. A OPC Publisher é chamada com o seguinte argumento de linha de comando: `--aa` . Com esta opção, a OPC Publisher confia nos certificados dos servidores UA OPC a que se conecta. Pode utilizar todas as opções de linha de comando do OPC Publisher. A única limitação é o tamanho das **Opções de Criação** de Recipientes suportadas pela IoT Edge.

1. Deixe as outras definições inalteradas e selecione **Guardar**.
1. Se pretender processar a saída do Editor OPC localmente com outro módulo IoT Edge, volte para a página **'set Modules'.** Em seguida, vá ao **separador Rotas Especificar** e adicione uma nova rota que se pareça com o seguinte JSON:

    ```json
    {
      "routes": {
        "processingModuleToIoT Hub": "FROM /messages/modules/processingModule/outputs/* INTO $upstream",
        "opcPublisherToProcessingModule": "FROM /messages/modules/publisher INTO BrokeredEndpoint(\"/modules/processingModule/inputs/input1\")"
      }
    }
    ```

1. De volta à página **'Definir Módulos',** selecione **Seguinte**, até chegar à última página da configuração.
1. **Selecione Enviar** para enviar a sua configuração para IoT Edge.
1. Quando iniciar o IoT Edge no seu dispositivo de borda e o **editor** de contentores docker estiver em funcionamento, pode verificar a saída de registo da OPC Publisher, utilizando `docker logs -f publisher` ou verificando o ficheiro de registo. No exemplo anterior, o ficheiro de registo está acima `d:\iiotegde\publisher-publisher.log` . Também pode utilizar a [ferramenta iot-edge-opc-editor-diagnósticos](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics).

### <a name="make-the-configuration-files-accessible-on-the-host"></a>Tornar os ficheiros de configuração acessíveis no anfitrião

Para tornar os ficheiros de configuração do módulo IoT Edge acessíveis no sistema de ficheiros do anfitrião, utilize as **seguintes Opções de Criação de Recipientes**. Segue-se o exemplo de uma implantação utilizando recipientes Linux para windows:

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

Com estas opções, a OPC Publisher lê os nós que deve publicar a partir do ficheiro `./pn.json` e o diretório de trabalho do contentor está definido para `/appdata` o arranque. Com estas definições, a OPC Publisher lê o ficheiro `/appdata/pn.json` do recipiente para obter a sua configuração. Sem a `--pf` opção, a OPC Publisher tenta ler o ficheiro de configuração predefinido `./publishednodes.json` .

O ficheiro de registo, utilizando o nome `publisher-publisher.log` predefinido, é escrito e o `/appdata` `CertificateStores` diretório também é criado neste diretório.

Para disponibilizar todos estes ficheiros no sistema de ficheiros hospedeiros, a configuração do recipiente requer um volume de montagem de encaixe. O `d://iiotedge:/appdata` vinculado mapeia o diretório `/appdata` , que é o atual diretório de trabalho na startup de contentores, para o diretório anfitrião. `d://iiotedge` Sem esta opção, não são persistidos dados de ficheiro quando o recipiente começa a seguir.

Se estiver a executar recipientes Windows, então a sintaxe do `Binds` parâmetro é diferente. Na startup de contentores, o diretório de trabalho é `c:\appdata` . Para colocar o ficheiro de configuração no `d:\iiotedge` diretório no anfitrião, especifique o seguinte mapeamento na `HostConfig` secção:

```json
"HostConfig": {
    "Binds": [
        "d:/iiotedge:c:/appdata"
    ]
}
```

Se estiver a executar recipientes Linux em Linux, a sintaxe do `Binds` parâmetro é novamente diferente. Na startup de contentores, o diretório de trabalho é `/appdata` . Para colocar o ficheiro de configuração no `/iiotedge` diretório no anfitrião, especifique o seguinte mapeamento na `HostConfig` secção:

```json
"HostConfig": {
    "Binds": [
        "/iiotedge:/appdata"
    ]
}
```

## <a name="considerations-when-using-a-container"></a>Considerações ao utilizar um recipiente

As seguintes secções listam algumas coisas a ter em mente quando se utiliza um recipiente:

### <a name="access-to-the-opc-publisher-opc-ua-server"></a>Acesso ao servidor OPC Publisher OPC UA

Por padrão, o servidor UA da OPC Publisher OPC ouve na porta 62222. Para expor esta porta de entrada num recipiente, utilize o seguinte comando:

```sh/cmd
docker run -p 62222:62222 mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="enable-intercontainer-name-resolution"></a>Permitir a resolução do nome do intercontainer

Para permitir a resolução de nomes de dentro do recipiente para outros recipientes, crie uma rede de ponte de estivador e ligue o recipiente a esta rede utilizando a `--network` opção. Atribua também ao recipiente um nome utilizando a `--name` opção da seguinte forma:

```sh/cmd
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

O recipiente está agora acessível utilizando o nome `publisher` por outros recipientes da mesma rede.

### <a name="access-other-systems-from-within-the-container"></a>Aceda a outros sistemas a partir do interior do contentor

Outros recipientes podem ser alcançados utilizando os parâmetros descritos na secção anterior. Se o sistema operativo em que o Docker está hospedado estiver ativado em DNS, então aceda a todos os sistemas que são conhecidos do DNS funciona.

Nas redes que utilizam a resolução de nomes NetBIOS, possibilite o acesso a outros sistemas iniciando o seu contentor com a `--add-host` opção. Esta opção adiciona efetivamente uma entrada no ficheiro de anfitrião do recipiente:

```cmd/sh
docker run --add-host mydevbox:192.168.178.23  mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="assign-a-hostname"></a>Atribuir um nome de anfitrião

A OPC Publisher usa o nome de anfitrião da máquina em que está a funcionar para a produção de certificados e ponto final. Docker escolhe um nome de anfitrião aleatório se não for definido pela `-h` opção. O exemplo a seguir mostra como definir o nome de hospedeiro interno do recipiente `publisher` para:

```sh/cmd
docker run -h publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-bind-mounts-shared-filesystem"></a>Utilize suportes de encaixe (sistema de ficheiros partilhados)

Em vez de utilizar o sistema de ficheiros de contentores, pode escolher o sistema de ficheiros hospedeiros para armazenar informações de configuração e ficheiros de registo. Para configurar esta opção, utilize a `-v` opção de `docker run` montagem de ligação.

## <a name="opc-ua-x509-certificates"></a>Certificados OPC UA X.509

A OPC UA utiliza certificados X.509 para autenticar o cliente e servidor da OPC UA quando estabelecem uma ligação e encriptam a comunicação entre eles. A OPC Publisher utiliza lojas de certificados mantidas pela pilha UA da OPC para gerir todos os certificados. No arranque, a OPC Publisher verifica se há um certificado para si. Se não houver certificado na loja de certificados, e não for um que seja passado na linha de comando, a OPC Publisher cria um certificado auto-assinado. Para obter mais informações, consulte o método **InitApplicationSecurityAsync** em `OpcApplicationConfigurationSecurity.cs` .

Os certificados auto-assinados não fornecem qualquer segurança, uma vez que não são assinados por um AC de confiança.

A OPC Publisher fornece opções de linha de comando para:

- Recuperar informações sobre a CSR do certificado de candidatura atual utilizado pela OPC Publisher.
- Provision OPC Publisher com certificado assinado ca.
- Provision OPC Publisher com um novo par de chaves e certificado assinado ca correspondente.
- Adicione certificados a um perito de confiança ou loja de certificados de emitente fidedigno.
- Adicione um CRL.
- Remova um certificado do armazém de confiança ou dos emitentes fidedignos.

Todas estas opções permitem-lhe passar em parâmetros usando ficheiros ou cadeias codificadas base64.

O tipo de loja predefinido para todas as lojas de certificados é o sistema de ficheiros, que pode ser alterado utilizando opções de linha de comando. Como o recipiente não fornece armazenamento persistente no seu sistema de ficheiros, deve escolher um tipo de loja diferente. Utilize a opção Docker `-v` para persistir nas lojas de certificados no sistema de ficheiros anfitrião ou num volume Docker. Se utilizar um volume Docker, pode passar em certificados utilizando cordas codificadas base64.

O ambiente de tempo de execução afeta a forma como os certificados são persistentes. Evite criar novas lojas de certificados sempre que executar a aplicação:

- Funcionando de forma nativa no Windows, não é possível utilizar uma loja de certificados de aplicação do tipo `Directory` porque o acesso à chave privada falha. Neste caso, utilize a opção `--at X509Store` .
- Funcionando como recipiente linux docker, você pode mapear as lojas de certificados para o sistema de ficheiros anfitrião com a opção de execução do estivador `-v <hostdirectory>:/appdata` . Esta opção torna o certificado persistente em todas as aplicações.
- Funcionando como recipiente linux docker e você quer usar uma loja X509 para o certificado de aplicação, use a opção de execução do estivador `-v x509certstores:/root/.dotnet/corefx/cryptography/x509stores` e a opção de aplicação `--at X509Store`

## <a name="performance-and-memory-considerations"></a>Considerações de desempenho e memória

Esta secção discute opções para gerir a memória e o desempenho:

### <a name="command-line-parameters-to-control-performance-and-memory"></a>Parâmetros de linha de comando para controlar o desempenho e a memória

Quando gere o OPC Publisher, tem de estar atento aos seus requisitos de desempenho e aos recursos de memória disponíveis no seu anfitrião.

A memória e o desempenho são interdependentes e ambos dependem da configuração de quantos nós configura para publicar. Certifique-se de que os seguintes parâmetros satisfazem os seus requisitos:

- IoT Hub envia intervalo: `--si`
- Tamanho da mensagem IoT Hub `1` (padrão): `--ms`
- Capacidade de fila de itens monitorizados: `--mq`

O `--mq` parâmetro controla o limite superior da capacidade da fila interna, que tampona todas as notificações de alteração de valor do nó OPC. Se o Editor OPC não puder enviar mensagens para o IoT Hub com rapidez suficiente, esta fila tampona as notificações. O parâmetro define o número de notificações que podem ser tamponadas. Se vir o número de itens nesta fila a aumentar nos seus testes, então para evitar perder mensagens deve:

- Reduzir o intervalo de envio do IoT Hub
- Aumente o tamanho da mensagem IoT Hub

O `--si` parâmetro obriga o Editor OPC a enviar mensagens para o IoT Hub no intervalo especificado. O OPC Publisher envia uma mensagem assim que o tamanho da mensagem especificado pelo `--ms` parâmetro é atingido, ou assim que o intervalo especificado pelo `--si` parâmetro é atingido. Para desativar a opção de tamanho da mensagem, utilize `--ms 0` . Neste caso, a OPC Publisher utiliza o maior tamanho possível de mensagem IoT Hub de 256 kB para os dados do lote.

O `--ms` parâmetro permite-lhe enviar mensagens de lote enviadas para o IoT Hub. O protocolo que está a usar determina se a sobrecarga do envio de uma mensagem para o IoT Hub é elevada em comparação com o tempo real de envio da carga útil. Se o seu cenário permitir a latência quando os dados ingeridos pelo IoT Hub, configuure a OPC Publisher para utilizar o maior tamanho de mensagem de 256 kB.

Antes de utilizar o OPC Publisher em cenários de produção, teste o desempenho e o uso da memória em condições de produção. Pode utilizar o `--di` parâmetro para especificar o intervalo, em segundos, que a OPC Publisher escreve informações de diagnóstico.

### <a name="test-measurements"></a>Medições de teste

Os diagnósticos de exemplo a seguir mostram medições com `--si` valores diferentes para e `--ms` parâmetros que publicam 500 nós com um intervalo de publicação de OPC de 1 segundo.  O teste utilizou um depur de um OPC Publisher construído no Windows 10 de forma nativa durante 120 segundos. O protocolo IoT Hub era o protocolo padrão de MQTT.

#### <a name="default-configuration---si-10---ms-262144"></a>Configuração padrão (-si 10 --ms 262144)

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

A configuração padrão envia dados para o IoT Hub a cada 10 segundos, ou quando 256 kB de dados estão disponíveis para o IoT Hub ingerir. Esta configuração adiciona uma latência moderada de cerca de 10 segundos, mas tem a menor probabilidade de perder dados devido ao tamanho da mensagem grande. A saída de diagnóstico mostra que não há atualizações perdidas do nó OPC: `monitored item notifications enqueue failure: 0` .

#### <a name="constant-send-interval---si-1---ms-0"></a>Intervalo de envio constante (-si 1 --ms 0)

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

Quando o tamanho da mensagem é definido para 0, então o OPC Publisher empreita internamente dados usando o maior tamanho de mensagem IoT Hub suportado, que é de 256 kB. A saída de diagnóstico mostra que o tamanho médio da mensagem é de 115.019 bytes. Nesta configuração, o OPC Publisher não perde nenhuma atualização do valor do nó OPC, e em comparação com o padrão tem menor latência.

### <a name="send-each-opc-node-value-update---si-0---ms-0"></a>Envie cada atualização do valor do nó OPC (-si 0 --ms 0)

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

Esta configuração envia para cada valor do nó OPC alterar uma mensagem para IoT Hub. Os diagnósticos mostram que o tamanho médio da mensagem é de 234 bytes, o que é pequeno. A vantagem desta configuração é que a OPC Publisher não adiciona qualquer latência. O número de atualizações de valor do nó OPC perdidos é `monitored item notifications enqueue failure: 44624` elevado, o que torna esta configuração inadequada para cenários com volumes elevados de telemetria a serem publicados.

### <a name="maximum-batching---si-0---ms-262144"></a>Lote máximo (-si 0 --ms 262144)

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

Esta configuração lote o maior número possível de atualizações de valor do nó OPC. O tamanho máximo da mensagem IoT Hub é de 256 kB, que está configurado aqui. Não há nenhum intervalo de envio solicitado, o que significa que a quantidade de dados para ioT Hub para ingerir determina a latência. Esta configuração tem a menor probabilidade de perder quaisquer valores de nó OPC e é adequada para a publicação de um número elevado de nós. Quando utilizar esta configuração, certifique-se de que o seu cenário não tem condições em que seja introduzida uma elevada latência se o tamanho da mensagem de 256 kB não for atingido.

## <a name="debug-the-application"></a>Depurar a aplicação

Para depurar a aplicação, abra o **opcpublisher.sln** ficheiro de solução com o Visual Studio e utilize as ferramentas de depurar do Visual Studio.

Se precisar de aceder ao servidor OPC UA no OPC Publisher, certifique-se de que a sua firewall permite o acesso à porta onde o servidor escuta. A porta padrão é: 62222.

## <a name="control-the-application-remotely"></a>Controle a aplicação remotamente

Configurar os nós para publicar pode ser feito usando métodos diretos IoT Hub.

A OPC Publisher implementa algumas chamadas adicionais do método direto IoT Hub para ler:

- Informação geral.
- Informação de diagnóstico sobre sessões de OPC, subscrições e itens monitorizados.
- Informação de diagnóstico sobre mensagens e eventos do IoT Hub.
- O registo de arranque.
- As últimas 100 linhas do tronco.
- Desligue o pedido.

Os repositórios do GitHub que se seguem contêm ferramentas para [configurar os nós para publicar](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration) e ler a [informação de diagnóstico](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics). Ambas as ferramentas também estão disponíveis como recipientes em Docker Hub.

## <a name="use-a-sample-opc-ua-server"></a>Use uma amostra do servidor UA OPC

Se não tiver um servidor OPC UA real, pode utilizar a [amostra OPC UA PLC](https://github.com/Azure-Samples/iot-edge-opc-plc) para começar. Esta amostra PLC também está disponível no Docker Hub.

Implementa uma série de tags, que geram dados aleatórios e tags com anomalias. Pode estender a amostra se precisar de simular valores adicionais de etiqueta.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a gerir a OPC Publisher, os próximos passos recomendados são aprender sobre [o OPC Twin](overview-opc-twin.md) e [o OPC Vault.](overview-opc-vault.md)