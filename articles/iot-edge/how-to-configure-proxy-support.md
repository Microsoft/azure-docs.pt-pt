---
title: Configurar dispositivos para os proxies de rede - Azure IoT Edge | Documentos da Microsoft
description: Como configurar o tempo de execução do Azure IoT Edge e quaisquer módulos do IoT Edge de acesso à internet para comunicar através de um servidor proxy.
author: kgremban
manager: ''
ms.author: kgremban
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 34a94a1b9c38070f5c9de22d9a9e4f24183a876c
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151008"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Configurar um dispositivo IoT Edge para comunicar através de um servidor proxy

Os dispositivos de IoT Edge enviam pedidos HTTPS para comunicar com IoT Hub. Se o dispositivo estiver ligado a uma rede que utiliza um servidor proxy, tem de configurar o runtime do IoT Edge para comunicar através do servidor. Servidores proxy também podem afetar os módulos do IoT Edge individuais se efetuam os pedidos HTTP ou HTTPS que não são encaminhados através do hub do IoT Edge. 

Este artigo explica os quatro passos seguintes para configurar e gerir um dispositivo IoT Edge por trás de um servidor proxy: 

1. **Instale o runtime do IoT Edge no dispositivo.**

   Os scripts de instalação do IoT Edge extrair pacotes e os ficheiros da internet, pelo que o dispositivo tem de comunicar através do servidor de proxy para fazer esses pedidos. Para obter passos detalhados, consulte a [instalar o tempo de execução através de um proxy](#install-the-runtime-through-a-proxy) seção deste artigo. Para dispositivos Windows, o script de instalação também fornece uma [instalação Offline](how-to-install-iot-edge-windows.md#offline-installation) opção. 

   Este passo é um processo único executado no dispositivo IoT Edge, quando primeiro configurá-lo. As mesmas ligações também são necessárias quando atualizar o runtime do IoT Edge. 

2. **Configure o daemon do Docker e o daemon de IoT Edge no dispositivo.**

   IoT Edge utiliza dois daemons no dispositivo, o que precisam para fazer pedidos web através do servidor proxy. O daemon de IoT Edge é responsável por comunicações com o IoT Hub. O daemon de Moby é responsável pela gestão de contentores, portanto, se comunica com registos de contentores. Para obter passos detalhados, consulte a [configurar os daemons](#configure-the-daemons) seção deste artigo. 

   Este passo é um processo único executado no dispositivo IoT Edge, quando primeiro configurá-lo.

3. **Configure as propriedades de agente do IoT Edge no arquivo config.yaml no seu dispositivo.**

   O daemon de IoT Edge é iniciado o módulo de edgeAgent inicialmente, mas, em seguida, o módulo de edgeAgent é responsável por obter o manifesto de implantação a partir do IoT Hub e a partir de todos os outros módulos. Para o agente do IoT Edge fazer a ligação inicial ao IoT Hub, configure as variáveis de ambiente de módulo edgeAgent manualmente no próprio dispositivo. Após a ligação inicial, pode configurar o módulo de edgeAgent remotamente. Para obter passos detalhados, consulte a [configurar o agente do IoT Edge](#configure-the-iot-edge-agent) seção deste artigo.

   Este passo é um processo único executado no dispositivo IoT Edge, quando primeiro configurá-lo.

4. **Para todas as implementações de módulo futuras, defina variáveis de ambiente para qualquer módulo comunicar através do proxy.**

   Assim que o seu dispositivo IoT Edge é configurado e ligado ao IoT Hub através do servidor proxy, tem de manter a ligação em todas as implementações de módulo futuras. Para obter passos detalhados, consulte a [configurar manifestos de implantação](#configure-deployment-manifests) seção deste artigo. 

   Este passo é um processo contínuo executado remotamente, para que cada nova atualização de módulo ou implementação mantém a capacidade do dispositivo de comunicar através do servidor proxy. 

## <a name="know-your-proxy-url"></a>Saber o URL de proxy

Antes de iniciar qualquer um dos passos neste artigo, precisa saber o URL de proxy.

URLs de proxy efetuar o seguinte formato: **protocolo**://**proxy_host**:**porta_proxy**.

* O **protocolo** é HTTP ou HTTPS. O daemon do Docker pode utilizar qualquer protocolo, dependendo das suas definições de registo de contentor, mas os contentores de daemon e o runtime do IoT Edge devem sempre usar HTTP para estabelecer ligação ao proxy.

* O **proxy_host** é um endereço do servidor proxy. Se o servidor proxy requer autenticação, pode fornecer as suas credenciais como parte do anfitrião proxy com o seguinte formato: **usuário**:**palavra-passe**\@**proxy_host** .

* O **porta_proxy** é a porta de rede em que o proxy responde ao tráfego de rede.

## <a name="install-the-runtime-through-a-proxy"></a>Instalar o tempo de execução através de um proxy

Se o seu dispositivo IoT Edge é executado no Windows ou Linux, precisa acessar os pacotes de instalação através do servidor proxy. Consoante o sistema operativo, siga os passos para instalar o runtime do IoT Edge através de um servidor proxy. 

### <a name="linux"></a>Linux

Se estiver a instalar o runtime do IoT Edge num dispositivo Linux, configure o Gestor de pacotes para passar pelo seu servidor de proxy para aceder ao pacote de instalação. Por exemplo, [definir apt-get para utilizar um proxy http](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Depois de configurar o seu Gestor de pacote, siga as instruções em [tempo de execução de instalar o Azure IoT Edge no Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) ou [instalar o runtime do Azure IoT Edge no Linux (x64)](how-to-install-iot-edge-linux.md) como de costume.

### <a name="windows"></a>Windows

Se estiver a instalar o runtime do IoT Edge num dispositivo Windows, terá de ir através do servidor proxy duas vezes. A primeira ligação transfere o ficheiro de script do instalador, e a segunda conexão é durante a instalação para transferir os componentes necessários. Pode configurar as informações de proxy nas definições do Windows, ou pode incluir as informações de proxy diretamente nos comandos do PowerShell. 

Os passos seguintes demonstram um exemplo de uma instalação do windows usando o `-proxy` argumento:

1. O comando Invoke-WebRequest precisa de informações de proxy para aceder ao mesmo instalador. Em seguida, o comando de implementar IoTEdge precisa das informações de proxy para transferir os ficheiros de instalação. 

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. O comando Initialize IoTEdge não precisa passar pelo servidor proxy, portanto, a segunda etapa requer apenas informações de proxy para Invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge

If you have complicated credentials for the proxy server that can't be included in the URL, use the `-ProxyCredential` parameter within `-InvokeWebRequestParameters`. For example,

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Para obter mais informações sobre parâmetros de proxy, consulte [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). Para obter mais informações sobre opções de instalação do Windows, incluindo a instalação offline, consulte [runtime de instalar o Azure IoT Edge no Windows](how-to-install-iot-edge-windows.md).

## <a name="configure-the-daemons"></a>Configurar os daemons

IoT Edge se baseia em dois daemons em execução no dispositivo IoT Edge. O daemon de Moby faz solicitações da web para extrair imagens de contentores de registos de contentores. O daemon de IoT Edge faz solicitações da web para comunicar com IoT Hub.

O Moby e os daemons do IoT Edge tem de ser configurado para utilizar o servidor proxy para a funcionalidade de dispositivo em curso. Este passo ocorre no dispositivo IoT Edge durante a configuração inicial do dispositivo. 

### <a name="moby-daemon"></a>Daemon de Moby

Uma vez que Moby é baseado no Docker, consulte a documentação do Docker para configurar o daemon de Moby com variáveis de ambiente. A maioria dos registos de contentores (incluindo DockerHub e registos de contentores do Azure) suportam pedidos HTTPS, pelo que é o parâmetro que deve definir **HTTPS_PROXY**. Se está obtendo imagens a partir de um registo que não suporta a segurança de camada de transporte (TLS), então deve definir os **HTTP_PROXY** parâmetro. 

Escolha o artigo que se aplica ao seu sistema de operativo de dispositivo do IoT Edge: 

* [Configurar o daemon do Docker no Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
    * O daemon de Moby em dispositivos de Linux mantém o nome do Docker.
* [Configurar o daemon do Docker no Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)
    * O daemon de Moby em dispositivos do Windows é chamado iotedge moby. Os nomes são diferentes, uma vez que é possível executar o ambiente de trabalho do Docker e Moby em paralelo num dispositivo Windows. 

### <a name="iot-edge-daemon"></a>Daemon de IoT Edge

O daemon de IoT Edge é configurado de forma semelhante para o daemon de Moby. Utilize os seguintes passos para definir uma variável de ambiente para o serviço, com base no seu sistema operativo. 

O daemon de IoT Edge utiliza sempre HTTPS para enviar pedidos para o IoT Hub.

#### <a name="linux"></a>Linux

Abra um editor no terminal para configurar o daemon de IoT Edge. 

```bash
sudo systemctl edit iotedge
```

Introduza o texto seguinte, substituindo  **\<URL de proxy >** com o seu endereço do servidor proxy e porta. Em seguida, guarde e saia. 

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Atualize o service manager para recolher a nova configuração para o IoT Edge.

```bash
sudo systemctl daemon-reload
```

Reinicie o IoT Edge, para que as alterações entrem em vigor.

```bash
sudo systemctl restart iotedge
```

Certifique-se de que a variável de ambiente foi criada e a nova configuração foi carregada. 

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

Abra uma janela do PowerShell como administrador e execute o seguinte comando para editar o registo com a nova variável de ambiente. Substitua  **\<url de proxy >** com o seu endereço do servidor proxy e porta. 

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Reinicie o IoT Edge, para que as alterações entrem em vigor.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>Configurar o agente do IoT Edge

O agente do IoT Edge é o módulo de primeira para começar em qualquer dispositivo IoT Edge. Ele é iniciado pela primeira vez com base nas informações no ficheiro de config.yaml do IoT Edge. O agente do IoT Edge, em seguida, liga ao IoT Hub para obter os manifestos de implantação, declarar o que outros módulos devem ser implementado no dispositivo.

Este passo ocorre uma vez no dispositivo IoT Edge durante a configuração inicial do dispositivo. 

1. Abra o ficheiro de config.yaml no seu dispositivo IoT Edge. Em sistemas Linux, este ficheiro está localizado em **/etc/iotedge/config.yaml**. Em sistemas Windows, este ficheiro está localizado em **C:\ProgramData\iotedge\config.yaml**. O ficheiro de configuração está protegido, pelo que necessita de privilégios administrativos para aceder ao mesmo. Em sistemas Linux, utilize o `sudo` comando antes de abrir o ficheiro no seu editor de texto preferido. No Windows, abra um editor de texto como o bloco de notas como administrador e, em seguida, abra o ficheiro. 

2. No ficheiro config.yaml, localize a **especificação do módulo de agente do Edge** secção. A definição de agente do IoT Edge inclui uma **env** parâmetro onde pode adicionar variáveis de ambiente. 

3. Remova as chavetas que marcadores de posição para o parâmetro env e adicionar a nova variável numa nova linha. Lembre-se de que recua no YAML é dois espaços. 

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. O runtime do IoT Edge utiliza o AMQP por predefinição para comunicar com o IoT Hub. Alguns servidores de proxy bloqueiam portas AMQP. Se for esse o caso, em seguida, terá também de configurar edgeAgent para utilizar o AMQP sobre WebSocket. Adicione uma segunda variável de ambiente.

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![definição de edgeAgent com variáveis de ambiente](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. Guardar as alterações à config.yaml e feche o editor. Reinicie o IoT Edge, para que as alterações entrem em vigor. 

   * Linux: 

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows:

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>Configurar manifestos de implantação  

Assim que o seu dispositivo IoT Edge está configurado para trabalhar com o servidor proxy, tem de continuar para declarar as variáveis de ambiente no futuro manifestos de implantação. Pode editar manifestos de implantação usando o Assistente do portal do Azure ou ao editar uma implementação de manifesto do ficheiro JSON. 

Configure sempre os dois módulos de tempo de execução, edgeAgent e edgeHub, para comunicar através do servidor proxy para que podem manter uma ligação com o IoT Hub. Se remover as informações de proxy do módulo edgeAgent, é a única forma de restabelecer a ligação ao editar o ficheiro de config.yaml no dispositivo, conforme descrito na secção anterior. 

Outros módulos do IoT Edge que se ligam à internet devem ser configurados para comunicar através do servidor de proxy, também. No entanto, os módulos que rotear suas mensagens por meio de edgeHub ou que só comunicam com outros módulos no dispositivo não tem os detalhes do servidor proxy. 

Este passo está em curso em todo o ciclo de vida do dispositivo IoT Edge. 

### <a name="azure-portal"></a>Portal do Azure

Quando utiliza a **definir módulos** Assistente para criar implementações para o IoT Edge dispositivos, cada módulo é um **variáveis de ambiente** secção que pode utilizar para configurar ligações de servidor proxy. 

Para configurar o agente do IoT Edge e os módulos do IoT Edge hub, selecione **configurar definições de Runtime do Edge avançadas** no primeiro passo do assistente. 

![Configurar as definições avançadas de Runtime do Edge](./media/how-to-configure-proxy-support/configure-runtime.png)

Adicionar a **https_proxy** variável de ambiente para o agente do IoT Edge e definições do módulo do IoT Edge hub. Se tiver incluído os **UpstreamProtocol** variável de ambiente no arquivo config.yaml no seu dispositivo IoT Edge, adicioná-la para a definição de módulo do IoT Edge agente demasiado. 

![Variável de ambiente de https_proxy de conjunto](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Todos os outros módulos que adicionar a um manifesto de implantação seguem o mesmo padrão. Na página em que definir o nome do módulo e a imagem, há uma secção de variáveis de ambiente.

### <a name="json-deployment-manifest-files"></a>Ficheiros de manifesto de implantação de JSON

Se criar implementações para o IoT Edge em dispositivos com os modelos no Visual Studio Code ou criando manualmente os ficheiros JSON, pode adicionar as variáveis de ambiente diretamente para cada definição de módulo. 

Utilize o seguinte formato JSON: 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

Com as variáveis de ambiente incluídas, a definição de módulo deve ter um aspeto semelhante ao seguinte exemplo de edgeHub:

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": ""
    },
    "env": {
        "https_proxy": {
            "value": "http://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Se tiver incluído os **UpstreamProtocol** variável de ambiente no arquivo confige.yaml no seu dispositivo IoT Edge, adicioná-la para a definição de módulo do IoT Edge agente demasiado. 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre as funções do [runtime do IoT Edge](iot-edge-runtime.md).

Resolver erros de instalação e configuração com [problemas comuns e resoluções para o Azure IoT Edge](troubleshoot.md)

