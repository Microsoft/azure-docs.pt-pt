---
title: Configurar dispositivos para proxies de rede - Azure IoT Edge / Microsoft Docs
description: Como configurar o tempo de execução do Azure IoT Edge e quaisquer módulos IoT Edge virados para a Internet para comunicar através de um servidor proxy.
author: kgremban
ms.author: kgremban
ms.date: 09/03/2020
ms.topic: how-to
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- contperfq1
ms.openlocfilehash: 8d5e5e6cc77c7fe1d32f0834831ef1b930ee834d
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91966173"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Configurar um dispositivo IoT Edge para comunicar através de um servidor proxy

Os dispositivos IoT Edge enviam pedidos HTTPS para comunicar com o IoT Hub. Se o seu dispositivo estiver ligado a uma rede que utilize um servidor proxy, tem de configurar o tempo de execução do IoT Edge para comunicar através do servidor. Os servidores Proxy também podem afetar os módulos IoT Edge individuais se fizerem pedidos HTTP ou HTTPS que não sejam encaminhados através do hub IoT Edge.

Este artigo percorre os seguintes quatro passos para configurar e, em seguida, gerir um dispositivo IoT Edge atrás de um servidor proxy:

1. [**Instale o tempo de execução IoT Edge no seu dispositivo**](#install-the-runtime-through-a-proxy)

   Os scripts de instalação IoT Edge retiram pacotes e ficheiros da internet, pelo que o seu dispositivo necessita de comunicar através do servidor proxy para fazer esses pedidos. Para dispositivos Windows, o script de instalação também fornece uma opção de instalação offline.

   Este passo é um processo único para configurar o dispositivo IoT Edge quando o configurar pela primeira vez. As mesmas ligações também são necessárias quando atualiza o tempo de funcionação do IoT Edge.

2. [**Configure o daemon Do Docker e o daemon IoT Edge no seu dispositivo**](#configure-the-daemons)

   O IoT Edge utiliza dois daemons no dispositivo, ambos os quais precisam de fazer pedidos web através do servidor proxy. O IoT Edge Daemon é responsável pelas comunicações com o IoT Hub. O daemon Moby é responsável pela gestão de contentores, por isso comunica com os registos de contentores.

   Este passo é um processo único para configurar o dispositivo IoT Edge quando o configurar pela primeira vez.

3. [**Configure as propriedades do agente IoT Edge no ficheiro config.yaml no seu dispositivo**](#configure-the-iot-edge-agent)

   O daemon IoT Edge inicia inicialmente o módulo edgeAgent. Em seguida, o módulo edgeAgent recupera o manifesto de implantação do IoT Hub e inicia todos os outros módulos. Para que o agente IoT Edge faça a ligação inicial ao IoT Hub, configuure manualmente as variáveis do ambiente do módulo edgeAgent no próprio dispositivo. Após a ligação inicial, pode configurar remotamente o módulo edgeAgent.

   Este passo é um processo único para configurar o dispositivo IoT Edge quando o configurar pela primeira vez.

4. [**Para todas as futuras implementações de módulos, desacorra variáveis ambientais para qualquer módulo que comunique através do proxy**](#configure-deployment-manifests)

   Uma vez configurado e ligado ao IoT Hub através do servidor proxy, é necessário manter a ligação em todas as futuras implementações do módulo.

   Este passo é um processo contínuo feito remotamente para que cada novo módulo ou atualização de implementação mantenha a capacidade do dispositivo de comunicar através do servidor proxy.

## <a name="know-your-proxy-url"></a>Conheça o seu URL de procuração

Antes de iniciar qualquer um dos passos deste artigo, precisa de conhecer o seu URL de procuração.

Os URLs proxy tomam o seguinte formato: **protocolo****proxy_host**:**proxy_port**.

* O **protocolo** é HTTP ou HTTPS. O daemon Docker pode usar qualquer protocolo, dependendo das definições do registo do seu contentor, mas os recipientes IoT Edge daemon e runtime devem sempre utilizar HTTP para se ligar ao representante.

* O **proxy_host** é um endereço para o servidor proxy. Se o seu servidor proxy necessitar de autenticação, pode fornecer as suas credenciais como parte do anfitrião proxy com o seguinte formato: **utilizador**:**password** \@ **proxy_host**.

* O **proxy_port** é a porta de rede em que o proxy responde ao tráfego de rede.

## <a name="install-the-runtime-through-a-proxy"></a>Instale o tempo de execução através de um representante

Quer o seu dispositivo IoT Edge seja executado no Windows ou linux, tem de aceder aos pacotes de instalação através do servidor proxy. Dependendo do seu sistema operativo, siga os passos para instalar o tempo de execução do IoT Edge através de um servidor proxy.

### <a name="linux-devices"></a>Dispositivos Linux

Se estiver a instalar o tempo de funcionamento do IoT Edge num dispositivo Linux, configurge o gestor de pacotes para passar pelo seu servidor proxy para aceder ao pacote de instalação. Por exemplo, [Configurar apta a utilizar um http-proxy](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Uma vez configurado o seu gestor de pacotes, siga as instruções no tempo de [funcionamento do Azure IoT Edge,](how-to-install-iot-edge.md) como de costume.

### <a name="windows-devices"></a>Dispositivos Windows

Se estiver a instalar o tempo de funcionamento do IoT Edge num dispositivo Windows, tem de passar duas vezes pelo servidor proxy. A primeira ligação descarrega o ficheiro de script do instalador, e a segunda ligação é durante a instalação para descarregar os componentes necessários. Pode configurar informações de procuração nas definições do Windows ou incluir as suas informações de procuração diretamente nos comandos PowerShell.

Os seguintes passos demonstram um exemplo de uma instalação de janelas utilizando o `-proxy` argumento:

1. O comando Invoke-WebRequest precisa de informação por procuração para aceder ao script do instalador. Em seguida, o comando Deploy-IoTEdge precisa da informação proxy para descarregar os ficheiros de instalação.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. O comando Initialize-IoTEdge não precisa de passar pelo servidor proxy, por isso o segundo passo requer apenas informações de procuração para Invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

Se tiver credenciais complicadas para o servidor proxy que não pode ser incluído no URL, utilize o `-ProxyCredential` parâmetro dentro `-InvokeWebRequestParameters` . Por exemplo,

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Para obter mais informações sobre parâmetros de procuração, consulte [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). Para obter mais informações sobre os parâmetros de instalação do Windows, consulte [os scripts PowerShell para IoT Edge no Windows](reference-windows-scripts.md).

## <a name="configure-the-daemons"></a>Configure os daemons

O IoT Edge conta com dois daemons em execução no dispositivo IoT Edge. O Daemon Moby faz pedidos na web para retirar imagens de contentores dos registos de contentores. O daemon IoT Edge faz pedidos web para comunicar com o IoT Hub.

Tanto os daemons Moby como os daemons IoT Edge precisam de ser configurados para utilizar o servidor proxy para a funcionalidade do dispositivo em curso. Este passo ocorre no dispositivo IoT Edge durante a configuração inicial do dispositivo.

### <a name="moby-daemon"></a>Daemon moby

Uma vez que Moby é construído sobre Docker, consulte a documentação do Docker para configurar o daemon Moby com variáveis ambientais. A maioria dos registos de contentores (incluindo registos de contentores DockerHub e Azure) suportam pedidos HTTPS, pelo que o parâmetro que deve definir é **HTTPS_PROXY**. Se estiver a retirar imagens de um registo que não suporta a segurança da camada de transporte (TLS), então deve definir o **parâmetro HTTP_PROXY.**

Escolha o artigo que se aplica ao seu sistema operativo do dispositivo IoT Edge:

* [Configure Docker daemon em Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) O daemon Moby nos dispositivos Linux mantém o nome Docker.
* [Configure daemon Docker no Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration) O daemon Moby nos dispositivos Windows chama-se iotedge-moby. Os nomes são diferentes porque é possível executar tanto o Docker Desktop como o Moby em paralelo num dispositivo Windows.

### <a name="iot-edge-daemon"></a>Daemon IoT Edge

O daemon IoT Edge está configurado de forma semelhante ao daemon Moby. Utilize os seguintes passos para definir uma variável ambiental para o serviço, com base no seu sistema operativo.

O daemon IoT Edge usa sempre HTTPS para enviar pedidos para o IoT Hub.

#### <a name="linux"></a>Linux

Abra um editor no terminal para configurar o daemon IoT Edge.

```bash
sudo systemctl edit iotedge
```

Introduza o seguinte texto, substituindo **\<proxy URL>** pelo endereço do servidor proxy e pela porta. Então, salve e saia.

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Refresque o gestor de serviço para recolher a nova configuração para IoT Edge.

```bash
sudo systemctl daemon-reload
```

Reinicie o IoT Edge para que as alterações entrem em vigor.

```bash
sudo systemctl restart iotedge
```

Verifique se a variável ambiental foi criada e que a nova configuração foi carregada.

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

Abra uma janela PowerShell como administrador e execute o seguinte comando para editar o registo com a nova variável ambiente. **\<proxy url>** Substitua-o pelo endereço do servidor proxy e pela porta.

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Reinicie o IoT Edge para que as alterações entrem em vigor.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>Configure o agente IoT Edge

O agente IoT Edge é o primeiro módulo a iniciar qualquer dispositivo IoT Edge. Começou pela primeira vez com base na informação no ficheiro IoT Edge config.yaml. O agente IoT Edge liga-se então ao IoT Hub para recuperar manifestos de implantação, que declaram que outros módulos devem ser implantados no dispositivo.

Este passo ocorre uma vez no dispositivo IoT Edge durante a configuração inicial do dispositivo.

1. Abra o ficheiro config.yaml no seu dispositivo IoT Edge. Nos sistemas Linux, este ficheiro está localizado em **/etc/iotedge/config.yaml**. Nos sistemas Windows, este ficheiro está localizado em **C:\ProgramData\iotedge\config.yaml**. O ficheiro de configuração está protegido, pelo que precisa de privilégios administrativos para aceder ao mesmo. Nos sistemas Linux, utilize o `sudo` comando antes de abrir o ficheiro no seu editor de texto preferido. No Windows, abra um editor de texto como o Notepad como administrador e, em seguida, abra o ficheiro.

2. No ficheiro config.yaml, encontre a secção de especificações do **módulo Edge Agent.** A definição do agente IoT Edge inclui um parâmetro **env** onde pode adicionar variáveis ambientais.

3. Retire os suportes encaracolados que são espaços reservados para o parâmetro env e adicione a nova variável numa nova linha. Lembre-se que os travões em YAML são dois espaços.

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. O tempo de execução IoT Edge utiliza AMQP por padrão para falar com o IoT Hub. Alguns servidores proxy bloqueiam portas AMQP. Se for esse o caso, também precisa configurar o edgeAgent para usar AMQP em vez do WebSocket. Adicione uma segunda variável ambiental.

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![definição edgeAgent com variáveis ambientais](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. Guarde as alterações para config.yaml e feche o editor. Reinicie o IoT Edge para que as alterações entrem em vigor.

   * Linux:

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows:

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>Configure manifestos de implantação  

Uma vez configurado o seu dispositivo IoT Edge para funcionar com o seu servidor proxy, tem de continuar a declarar a variável ambiente HTTPS_PROXY em futuros manifestos de implementação. Pode editar manifestos de implantação utilizando o assistente do portal Azure ou editando um ficheiro JSON manifesto de implementação.

Configures sempre os dois módulos de tempo de execução, edgeAgent e edgeHub, para comunicar através do servidor proxy para que possam manter uma ligação com o IoT Hub. Se remover as informações de procuração do módulo edgeAgent, a única forma de restabelecer a ligação é editando o ficheiro config.yaml no dispositivo, conforme descrito na secção anterior.

Além dos módulos edgeAgent e edgeHub, outros módulos podem necessitar da configuração proxy. Os módulos que precisam de aceder aos recursos do Azure para além do IoT Hub, como o armazenamento de bolhas, devem ter a variável HTTPS_PROXY especificada no ficheiro manifesto de implantação.

O procedimento a seguir é aplicável ao longo da vida útil do dispositivo IoT Edge.

### <a name="azure-portal"></a>Portal do Azure

Quando utiliza o assistente de **módulos set** para criar implementações para dispositivos IoT Edge, cada módulo tem uma secção **de Variáveis ambientais** onde pode configurar ligações de servidor de procuração.

Para configurar os módulos IoT Edge e IoT Edge hub, selecione **Definições de tempo de execução** no primeiro passo do assistente.

![Configurar configurações avançadas de tempo de execução de borda](./media/how-to-configure-proxy-support/configure-runtime.png)

Adicione a variável **ambiente https_proxy** tanto às definições do agente IoT Edge como do módulo hub IoT Edge. Se incluiu a variável ambiente **UpstreamProtocol** no ficheiro config.yaml no seu dispositivo IoT Edge, adicione isso também à definição do módulo de agente IoT Edge.

![Definir variável de ambiente https_proxy](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Todos os outros módulos que adiciona a um manifesto de implantação seguem o mesmo padrão.

### <a name="json-deployment-manifest-files"></a>Ficheiros manifestos de implantação JSON

Se criar implementações para dispositivos IoT Edge utilizando os modelos no Código do Estúdio Visual ou criando manualmente ficheiros JSON, pode adicionar as variáveis ambientais diretamente a cada definição de módulo.

Utilize o seguinte formato JSON:

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

Com as variáveis ambientais incluídas, a definição do módulo deve parecer o seguinte exemplo edgeHub:

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

Se incluiu a variável ambiente **UpstreamProtocol** no ficheiro confige.yaml no seu dispositivo IoT Edge, adicione isso também à definição do módulo de agente IoT Edge.

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

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre os papéis do tempo de [execução IoT Edge](iot-edge-runtime.md).

Erros de instalação e configuração de resolução de problemas com [questões e resoluções comuns para Azure IoT Edge](troubleshoot.md)
