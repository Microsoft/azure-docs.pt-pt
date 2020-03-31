---
title: Configure dispositivos para proxies de rede - Azure IoT Edge [ Microsoft Docs
description: Como configurar o tempo de funcionamento do Azure IoT Edge e quaisquer módulos IoT Edge virados para a Internet para comunicar através de um servidor proxy.
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0600568ace5384cfb13688d14d1cf79e473f3208
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133222"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Configurar um dispositivo IoT Edge para comunicar através de um servidor proxy

Os dispositivos IoT Edge enviam pedidos HTTPS para comunicar com o IoT Hub. Se o seu dispositivo estiver ligado a uma rede que utiliza um servidor proxy, é necessário configurar o tempo de execução do IoT Edge para comunicar através do servidor. Os servidores proxy também podem afetar os módulos IoT Edge individuais se fizerem pedidos HTTP ou HTTPS que não sejam encaminhados através do hub IoT Edge.

Este artigo percorre os quatro passos seguintes para configurar e, em seguida, gerir um dispositivo IoT Edge atrás de um servidor proxy:

1. **Instale o tempo de execução do IoT Edge no seu dispositivo.**

   Os scripts de instalação IoT Edge retiram pacotes e ficheiros da internet, pelo que o seu dispositivo precisa de comunicar através do servidor proxy para fazer esses pedidos. Para obter passos detalhados, consulte o [tempo de execução através](#install-the-runtime-through-a-proxy) de uma secção de procuração deste artigo. Para dispositivos Windows, o script de instalação também fornece uma opção de [instalação Offline.](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation)

   Este passo é um processo único realizado no dispositivo IoT Edge quando o configurar pela primeira vez. As mesmas ligações também são necessárias quando atualiza o tempo de execução do IoT Edge.

2. **Configure o daemon do Docker e o daemon IoT Edge no seu dispositivo.**

   O IoT Edge utiliza dois daemons no dispositivo, ambos necessários para fazer pedidos web através do servidor proxy. O daemon IoT Edge é responsável pelas comunicações com o IoT Hub. O daemon Moby é responsável pela gestão de contentores, por isso comunica com registos de contentores. Para obter passos detalhados, consulte a secção [Desemons](#configure-the-daemons) do artigo.

   Este passo é um processo único realizado no dispositivo IoT Edge quando o configurar pela primeira vez.

3. **Configure as propriedades do agente IoT Edge no ficheiro config.yaml no seu dispositivo.**

   O daemon IoT Edge inicia inicialmente o módulo EdgeAgent, mas depois o módulo EdgeAgent é responsável por recuperar o manifesto de implantação do IoT Hub e iniciar todos os outros módulos. Para que o agente IoT Edge faça a ligação inicial ao IoT Hub, configure manualmente as variáveis ambientais do módulo EdgeAgent manualmente no próprio dispositivo. Após a ligação inicial, pode configurar remotamente o módulo EdgeAgent. Para obter passos detalhados, consulte a secção de [agente IoT Edge](#configure-the-iot-edge-agent) deste artigo.

   Este passo é um processo único realizado no dispositivo IoT Edge quando o configurar pela primeira vez.

4. **Para todas as futuras implementações de módulos, detete as variáveis ambientais para qualquer módulo que se comunique através do proxy.**

   Uma vez que o seu dispositivo IoT Edge é configurado e ligado ao IoT Hub através do servidor proxy, você precisa manter a ligação em todas as futuras implementações do módulo. Para obter [passos detalhados,](#configure-deployment-manifests) consulte a secção de desdobramento do Configure manifestos deste artigo.

   Este passo é um processo contínuo realizado remotamente para que cada novo módulo ou atualização de implementação mantenha a capacidade do dispositivo de comunicar através do servidor proxy.

## <a name="know-your-proxy-url"></a>Conheça o seu URL proxy

Antes de iniciar qualquer um dos passos deste artigo, precisa de conhecer o seu URL de procuração.

Os URLs proxy tomam o seguinte formato: **protocolo://****proxy_host**:**proxy_port**.

* O **protocolo** é HTTP ou HTTPS. O daemon Docker pode utilizar qualquer um dos protocolos, dependendo das definições do registo do seu contentor, mas os recipientes ioT Edge e os recipientes de tempo de funcionação devem sempre utilizar http para se ligar ao proxy.

* O **proxy_host** é um endereço para o servidor proxy. Se o seu servidor proxy necessitar de autenticação, pode fornecer as suas credenciais como parte do anfitrião proxy com o seguinte formato: **utilizador**:**palavra-passe**\@**proxy_host**.

* O **proxy_port** é a porta de rede onde o representante responde ao tráfego da rede.

## <a name="install-the-runtime-through-a-proxy"></a>Instale o tempo de execução através de um proxy

Quer o seu dispositivo IoT Edge seja executado no Windows ou no Linux, é necessário aceder aos pacotes de instalação através do servidor proxy. Dependendo do seu sistema operativo, siga os passos para instalar o tempo de funcionamento do IoT Edge através de um servidor proxy.

### <a name="linux-devices"></a>Dispositivos Linux

Se estiver a instalar o tempo de execução do IoT Edge num dispositivo Linux, configure o gestor do pacote para aceder ao seu servidor proxy para aceder ao pacote de instalação. Por exemplo, [configurar o apt-get para usar um http-proxy](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Assim que o gestor do pacote estiver configurado, siga as instruções no tempo de execução do [Azure IoT Edge no Linux,](how-to-install-iot-edge-linux.md) como de costume.

### <a name="windows-devices"></a>Dispositivos Windows

Se estiver a instalar o tempo de execução do IoT Edge num dispositivo Windows, tem de passar duas vezes pelo servidor proxy. A primeira ligação descarrega o ficheiro de script do instalador e a segunda ligação é durante a instalação para descarregar os componentes necessários. Pode configurar informações de procuração nas definições do Windows ou incluir as suas informações de procuração diretamente nos comandos PowerShell.

Os seguintes passos demonstram um `-proxy` exemplo de uma instalação de janelas utilizando o argumento:

1. O comando Invoke-WebRequest necessita de informações por procuração para aceder ao script do instalador. Em seguida, o comando Deploy-IoTEdge precisa da informação de procuração para descarregar os ficheiros de instalação.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. O comando Initialize-IoTEdge não precisa de passar pelo servidor proxy, pelo que o segundo passo requer apenas informações de procuração para Invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

Se tiver credenciais complicadas para o servidor proxy que não `-ProxyCredential` podem ser `-InvokeWebRequestParameters`incluídas no URL, utilize o parâmetro dentro de . Por exemplo,

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Para obter mais informações sobre parâmetros proxy, consulte [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). Para obter mais informações sobre as opções de instalação do Windows, incluindo a instalação offline, consulte instalação do tempo de [funcionamento do Azure IoT Edge no Windows](how-to-install-iot-edge-windows.md).

## <a name="configure-the-daemons"></a>Configure os daemons

O IoT Edge conta com dois daemons em execução no dispositivo IoT Edge. O daemon Moby faz pedidos web para retirar imagens de contentores dos registos de contentores. O daemon IoT Edge faz pedidos web para comunicar com o IoT Hub.

Tanto os damas de Moby como do IoT Edge precisam de ser configurados para utilizar o servidor proxy para a funcionalidade do dispositivo em curso. Este passo ocorre no dispositivo IoT Edge durante a configuração inicial do dispositivo.

### <a name="moby-daemon"></a>Moby daemon

Uma vez que Moby é construído sobre Docker, consulte a documentação do Docker para configurar o daemon Moby com variáveis ambientais. A maioria dos registos de contentores (incluindo os registos de contentores DockerHub e Azure) suportam pedidos HTTPS, pelo que o parâmetro que deve definir é **HTTPS_PROXY**. Se estiver a retirar imagens de um registo que não suporta a segurança da camada de transporte (TLS), então deve definir o **parâmetro HTTP_PROXY.**

Escolha o artigo que se aplica ao seu sistema operativo ioT Edge:

* [Configure Docker daemon em Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) O daemon Moby nos dispositivos Linux mantém o nome Docker.
* [Configure Docker daemon no Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration) O daemon Moby nos dispositivos Windows chama-se iotedge-moby. Os nomes são diferentes porque é possível executar tanto o Docker Desktop como o Moby em paralelo num dispositivo Windows.

### <a name="iot-edge-daemon"></a>IoT Edge daemon

O daemon IoT Edge é configurado de forma semelhante ao daemon Moby. Utilize os seguintes passos para definir uma variável ambiental para o serviço, com base no seu sistema operativo.

O daemon IoT Edge usa sempre HTTPS para enviar pedidos para ioT Hub.

#### <a name="linux"></a>Linux

Abra um editor no terminal para configurar o daemon IoT Edge.

```bash
sudo systemctl edit iotedge
```

Introduza o seguinte ** \<** texto, substituindo o URL proxy>pelo seu endereço de servidor proxy e porta. Então, salve e saia.

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Refresque o gestor de serviços para pegar a nova configuração para IoT Edge.

```bash
sudo systemctl daemon-reload
```

Reinicie a Borda IoT para que as alterações entrem em vigor.

```bash
sudo systemctl restart iotedge
```

Verifique se a sua variável ambiental foi criada e a nova configuração foi carregada.

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

Abra uma janela PowerShell como administrador e execute o seguinte comando para editar o registo com a nova variável ambiental. Substitua o ** \<url de procuração>** pelo endereço do servidor proxy e pela porta.

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Reinicie a Borda IoT para que as alterações entrem em vigor.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>Configure o agente IoT Edge

O agente IoT Edge é o primeiro módulo a iniciar qualquer dispositivo IoT Edge. Começou pela primeira vez com base na informação no ficheiro IoT Edge config.yaml. O agente IoT Edge liga-se então ao IoT Hub para recuperar manifestos de implantação, que declaram quais os outros módulos que devem ser implantados no dispositivo.

Este passo ocorre uma vez no dispositivo IoT Edge durante a configuração inicial do dispositivo.

1. Abra o ficheiro config.yaml no seu dispositivo IoT Edge. Nos sistemas Linux, este ficheiro está localizado em **/etc/iotedge/config.yaml**. Nos sistemas Windows, este ficheiro está localizado em **C:\ProgramData\iotedge\config.yaml**. O ficheiro de configuração está protegido, por isso precisa de privilégios administrativos para aceder ao mesmo. Nos sistemas Linux, utilize o `sudo` comando antes de abrir o ficheiro no seu editor de texto preferido. No Windows, abra um editor de texto como o Notepad como administrador e abra o ficheiro.

2. No ficheiro config.yaml, encontre a secção de especificações do **módulo Edge Agent.** A definição do agente IoT Edge inclui um parâmetro **env** onde pode adicionar variáveis ambientais.

3. Retire os suportes encaracolados que são espaços reservados para o parâmetro env e adicione a nova variável numa nova linha. Lembre-se que os entalhes em YAML são dois espaços.

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. O tempo de execução do IoT Edge utiliza AMQP por padrão para falar com o IoT Hub. Alguns servidores proxy bloqueiam portas AMQP. Se for esse o caso, também precisa de configurar o EdgeAgent para utilizar o AMQP sobre o WebSocket. Adicione uma segunda variável ambiental.

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![definição edgeAgent com variáveis ambientais](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. Guarde as alterações para config.yaml e feche o editor. Reinicie a Borda IoT para que as alterações entrem em vigor.

   * Linux:

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows:

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>Configure manifestos de implantação  

Uma vez configurado o seu dispositivo IoT Edge para funcionar com o seu servidor proxy, tem de continuar a declarar a variável ambiente HTTPS_PROXY em futuros manifestos de implementação. Pode editar manifestos de implantação utilizando o assistente do portal Azure ou editando um ficheiro JSON manifesto de implantação.

Configure sempre os dois módulos de tempo de execução, edgeAgent e edgeHub, para comunicar através do servidor proxy para que possam manter uma ligação com o IoT Hub. Se remover as informações de procuração do módulo EdgeAgent, a única forma de restabelecer a ligação é editando o ficheiro config.yaml no dispositivo, conforme descrito na secção anterior.

Além dos módulos EdgeAgent e EdgeHub, outros módulos podem necessitar da configuração proxy. Estes são módulos que precisam de aceder aos recursos do Azure para além do Hub IoT, como o armazenamento de bolhas, e devem ter a variável HTTPS_PROXY especificada para esse módulo no ficheiro manifesto de implantação.

O seguinte procedimento é aplicável ao longo da vida útil do dispositivo IoT Edge.

### <a name="azure-portal"></a>Portal do Azure

Quando utiliza o assistente de **módulos set** para criar implementações para dispositivos IoT Edge, cada módulo tem uma secção **de Variáveis ambientais** que pode utilizar para configurar as ligações do servidor proxy.

Para configurar os módulos do agente IoT Edge e do hub IoT Edge, selecione Definições de tempo de **execução** no primeiro passo do assistente.

![Configurar definições avançadas de prazo de execução de borda](./media/how-to-configure-proxy-support/configure-runtime.png)

Adicione a variável **ambiente https_proxy** tanto ao agente IoT Edge como às definições do módulo do hub IoT Edge. Se incluiu a variável ambiente **UpstreamProtocol** no ficheiro config.yaml no seu dispositivo IoT Edge, adicione isso à definição do módulo do agente IoT Edge também.

![Definir https_proxy variável ambiental](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Todos os outros módulos que adiciona a um manifesto de implantação seguem o mesmo padrão. Na página onde define o nome e a imagem do módulo, existe uma secção de variáveis ambientais.

### <a name="json-deployment-manifest-files"></a>Ficheiros manifestode implantação jSON

Se criar implementações para dispositivos IoT Edge utilizando os modelos no Código do Estúdio Visual ou através da criação manual de ficheiros JSON, pode adicionar as variáveis ambientais diretamente a cada definição de módulo.

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

Se incluiu a variável ambiente **UpstreamProtocol** no ficheiro confige.yaml no seu dispositivo IoT Edge, adicione isso à definição do módulo do agente IoT Edge também.

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

Saiba mais sobre os papéis do tempo de [execução ioT Edge](iot-edge-runtime.md).

Erros de instalação e configuração de resolução de problemas com [questões e resoluções comuns para O Edge Azure IoT](troubleshoot.md)
