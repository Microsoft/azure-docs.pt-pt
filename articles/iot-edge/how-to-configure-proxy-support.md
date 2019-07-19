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
ms.openlocfilehash: 1bf66fdb18398aaf233980ee7f2fd45fe1cc543b
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68260496"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Configurar um dispositivo IoT Edge para comunicar através de um servidor proxy

Os dispositivos de IoT Edge enviam pedidos HTTPS para comunicar com IoT Hub. Se o dispositivo estiver ligado a uma rede que utiliza um servidor proxy, tem de configurar o runtime do IoT Edge para comunicar através do servidor. Os servidores proxy também podem afetar os módulos de IoT Edge individuais se fizerem solicitações HTTP ou HTTPS que não são roteadas por meio do hub de IoT Edge. 

Este artigo percorre as quatro etapas a seguir para configurar e gerenciar um dispositivo IoT Edge atrás de um servidor proxy: 

1. **Instale o IoT Edge tempo de execução em seu dispositivo.**

   Os scripts de instalação do IoT Edge efetuam pull de pacotes e arquivos da Internet, para que seu dispositivo precise se comunicar por meio do servidor proxy para fazer essas solicitações. Para obter etapas detalhadas, consulte a seção [instalar o tempo de execução por meio de um proxy](#install-the-runtime-through-a-proxy) deste artigo. Para dispositivos Windows, o script de instalação também fornece uma opção de [instalação offline](how-to-install-iot-edge-windows.md#offline-installation) . 

   Essa etapa é um processo único executado no dispositivo IoT Edge quando você o configura pela primeira vez. As mesmas conexões também são necessárias quando você atualiza o tempo de execução do IoT Edge. 

2. **Configure o daemon do Docker e o daemon do IoT Edge em seu dispositivo.**

   IoT Edge usa dois daemons no dispositivo, ambos precisam fazer solicitações da Web por meio do servidor proxy. O daemon de IoT Edge é responsável pelas comunicações com o Hub IoT. O daemon Moby é responsável pelo gerenciamento de contêineres, portanto, comunica-se com registros de contêiner. Para obter etapas detalhadas, consulte a seção [configurar os daemons](#configure-the-daemons) deste artigo. 

   Essa etapa é um processo único executado no dispositivo IoT Edge quando você o configura pela primeira vez.

3. **Configure as propriedades do agente de IoT Edge no arquivo config. YAML em seu dispositivo.**

   O daemon de IoT Edge inicia inicialmente o módulo edgeAgent, mas, em seguida, o módulo edgeAgent é responsável por recuperar o manifesto de implantação do Hub IoT e iniciar todos os outros módulos. Para que o agente de IoT Edge faça a conexão inicial com o Hub IoT, configure manualmente as variáveis de ambiente do módulo edgeAgent no próprio dispositivo. Após a conexão inicial, você pode configurar o módulo edgeAgent remotamente. Para obter etapas detalhadas, consulte a seção [Configurar o agente de IOT Edge](#configure-the-iot-edge-agent) deste artigo.

   Essa etapa é um processo único executado no dispositivo IoT Edge quando você o configura pela primeira vez.

4. **Para todas as implantações de módulo futuras, defina variáveis de ambiente para qualquer módulo que se comunique por meio do proxy.**

   Depois que o dispositivo de IoT Edge estiver configurado e conectado ao Hub IoT por meio do servidor proxy, você precisará manter a conexão em todas as implantações de módulo futuras. Para obter etapas detalhadas, consulte a seção [Configurar manifestos de implantação](#configure-deployment-manifests) deste artigo. 

   Esta etapa é um processo contínuo executado remotamente para que cada novo módulo ou atualização de implantação mantenha a capacidade do dispositivo de se comunicar por meio do servidor proxy. 

## <a name="know-your-proxy-url"></a>Saber o URL de proxy

Antes de começar qualquer uma das etapas deste artigo, você precisa saber a URL do proxy.

URLs de proxy efetuar o seguinte formato: **protocolo**://**proxy_host**:**porta_proxy**.

* O **protocolo** é HTTP ou HTTPS. O daemon do Docker pode usar qualquer um dos protocolos, dependendo das configurações do registro de contêiner, mas o daemon de IoT Edge e os contêineres de tempo de execução sempre devem usar HTTP para se conectar ao proxy.

* O **proxy_host** é um endereço do servidor proxy. Se o servidor proxy exigir autenticação, você poderá fornecer suas credenciais como parte do host de proxy com o seguinte formato: **User**:**password**\@**proxy_host**.

* O **porta_proxy** é a porta de rede em que o proxy responde ao tráfego de rede.

## <a name="install-the-runtime-through-a-proxy"></a>Instalar o tempo de execução por meio de um proxy

Se o dispositivo IoT Edge for executado no Windows ou no Linux, você precisará acessar os pacotes de instalação por meio do servidor proxy. Dependendo do seu sistema operacional, siga as etapas para instalar o IoT Edge tempo de execução por meio de um servidor proxy. 

### <a name="linux"></a>Linux

Se estiver a instalar o runtime do IoT Edge num dispositivo Linux, configure o Gestor de pacotes para passar pelo seu servidor de proxy para aceder ao pacote de instalação. Por exemplo, [definir apt-get para utilizar um proxy http](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Depois de configurar o seu Gestor de pacote, siga as instruções em [tempo de execução de instalar o Azure IoT Edge no Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) ou [instalar o runtime do Azure IoT Edge no Linux (x64)](how-to-install-iot-edge-linux.md) como de costume.

### <a name="windows"></a>Windows

Se estiver instalando o tempo de execução do IoT Edge em um dispositivo Windows, você precisará passar pelo servidor proxy duas vezes. A primeira conexão baixa o arquivo de script do instalador e a segunda conexão é durante a instalação para baixar os componentes necessários. Você pode configurar as informações de proxy nas configurações do Windows ou incluir suas informações de proxy diretamente nos comandos do PowerShell. 

As etapas a seguir demonstram um exemplo de instalação do Windows `-proxy` usando o argumento:

1. O comando Invoke-WebRequest precisa de informações de proxy para acessar o script do instalador. Em seguida, o comando Deploy-IoTEdge precisa das informações de proxy para baixar os arquivos de instalação. 

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. O comando Initialize-IoTEdge não precisa passar pelo servidor proxy, portanto, a segunda etapa requer apenas informações de proxy para Invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

Se você tiver credenciais complicadas para o servidor proxy que não podem ser incluídas na URL, use `-ProxyCredential` o parâmetro `-InvokeWebRequestParameters`em. Por exemplo,

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Para obter mais informações sobre parâmetros de proxy, consulte [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). Para obter mais informações sobre as opções de instalação do Windows, incluindo a instalação offline, consulte [instalar Azure IOT Edge tempo de execução no Windows](how-to-install-iot-edge-windows.md).

## <a name="configure-the-daemons"></a>Configurar os daemons

IoT Edge se baseia em dois daemons em execução no dispositivo IoT Edge. O daemon Moby faz solicitações da Web para efetuar pull de imagens de contêiner de registros de contêiner. O daemon de IoT Edge faz solicitações da web para comunicar com IoT Hub.

Os daemons Moby e IoT Edge precisam ser configurados para usar o servidor proxy para a funcionalidade de dispositivo contínua. Esta etapa ocorre no dispositivo de IoT Edge durante a configuração inicial do dispositivo. 

### <a name="moby-daemon"></a>Daemon do Moby

Como o Moby é criado no Docker, consulte a documentação do Docker para configurar o daemon do Moby com variáveis de ambiente. A maioria dos registos de contentores (incluindo DockerHub e registos de contentores do Azure) suportam pedidos HTTPS, pelo que é o parâmetro que deve definir **HTTPS_PROXY**. Se está obtendo imagens a partir de um registo que não suporta a segurança de camada de transporte (TLS), então deve definir os **HTTP_PROXY** parâmetro. 

Escolha o artigo que se aplica ao sistema operacional do dispositivo de IoT Edge: 

* [Configurar o daemon do Docker no Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
    * O daemon do Moby em dispositivos Linux mantém o Docker de nome.
* [Configurar o daemon do Docker no Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)
    * O daemon do Moby em dispositivos Windows é chamado de iotedge-Moby. Os nomes são diferentes porque é possível executar o Docker desktop e o Moby em paralelo em um dispositivo Windows. 

### <a name="iot-edge-daemon"></a>Daemon de IoT Edge

O daemon de IoT Edge é configurado de maneira semelhante ao daemon do Moby. Utilize os seguintes passos para definir uma variável de ambiente para o serviço, com base no seu sistema operativo. 

O daemon de IoT Edge sempre usa HTTPS para enviar solicitações ao Hub IoT.

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

Atualize o Service Manager para selecionar a nova configuração para IoT Edge.

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

## <a name="configure-the-iot-edge-agent"></a>Configurar o agente de IoT Edge

O agente de IoT Edge é o primeiro módulo a ser iniciado em qualquer dispositivo IoT Edge. Ele é iniciado pela primeira vez com base nas informações no ficheiro de config.yaml do IoT Edge. Em seguida, o agente de IoT Edge se conecta ao Hub IoT para recuperar os manifestos de implantação, que declaram quais outros módulos devem ser implantados no dispositivo.

Esta etapa ocorre uma vez no dispositivo IoT Edge durante a configuração inicial do dispositivo. 

1. Abra o ficheiro de config.yaml no seu dispositivo IoT Edge. Em sistemas Linux, este ficheiro está localizado em **/etc/iotedge/config.yaml**. Em sistemas Windows, este ficheiro está localizado em **C:\ProgramData\iotedge\config.yaml**. O ficheiro de configuração está protegido, pelo que necessita de privilégios administrativos para aceder ao mesmo. Em sistemas Linux, use o `sudo` comando antes de abrir o arquivo em seu editor de texto preferido. No Windows, abra um editor de texto como o bloco de notas como administrador e, em seguida, abra o arquivo. 

2. No ficheiro config.yaml, localize a **especificação do módulo de agente do Edge** secção. A definição do agente de IoT Edge inclui um parâmetro **env** em que você pode adicionar variáveis de ambiente. 

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

Depois que o dispositivo de IoT Edge estiver configurado para trabalhar com o servidor proxy, você precisará continuar a declarar as variáveis de ambiente em manifestos de implantação futuros. Você pode editar os manifestos de implantação usando o assistente de portal do Azure ou editando um arquivo JSON de manifesto de implantação. 

Sempre configure os dois módulos de tempo de execução, edgeAgent e edgeHub, para se comunicar por meio do servidor proxy para que eles possam manter uma conexão com o Hub IoT. Se você remover as informações de proxy do módulo edgeAgent, a única maneira de restabelecer a conexão é editando o arquivo config. YAML no dispositivo, conforme descrito na seção anterior. 

Outros módulos IoT Edge que se conectam à Internet também devem ser configurados para se comunicar por meio do servidor proxy. No entanto, os módulos que roteiam suas mensagens por meio de edgeHub ou que se comunicam apenas com outros módulos no dispositivo não precisam dos detalhes do servidor proxy. 

Esta etapa está em andamento durante a vida útil do dispositivo IoT Edge. 

### <a name="azure-portal"></a>Portal do Azure

Quando utiliza a **definir módulos** Assistente para criar implementações para o IoT Edge dispositivos, cada módulo é um **variáveis de ambiente** secção que pode utilizar para configurar ligações de servidor proxy. 

Para configurar o agente de IoT Edge e IoT Edge módulos de Hub, selecione **definir configurações avançadas de tempo de execução do Edge** na primeira etapa do assistente. 

![Configurar as definições avançadas de Runtime do Edge](./media/how-to-configure-proxy-support/configure-runtime.png)

Adicione a variável de ambiente **https_proxy** ao agente de IoT Edge e IOT Edge definições de módulo de Hub. Se você tiver incluído a variável de ambiente **UpstreamProtocol** no arquivo config. YAML em seu dispositivo IOT Edge, adicione-a à definição de módulo do agente de IOT Edge também. 

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

Se você tiver incluído a variável de ambiente **UpstreamProtocol** no arquivo config. YAML em seu dispositivo IOT Edge, adicione-a à definição de módulo do agente de IOT Edge também. 

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

