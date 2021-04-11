---
title: Resolução de problemas - Azure IoT Edge | Microsoft Docs
description: Use este artigo para aprender habilidades de diagnóstico padrão para Azure IoT Edge, como recuperar o estado do componente e os registos
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b82ad29b02e501d41653fd466e58218e35c3b93c
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012174"
---
# <a name="troubleshoot-your-iot-edge-device"></a>Resolução de problemas do seu dispositivo IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Se sentir problemas em executar Azure IoT Edge no seu ambiente, utilize este artigo como guia para resolução de problemas e diagnósticos.

## <a name="run-the-check-command"></a>Executar o comando 'verificar'

O seu primeiro passo para resolver problemas IoT Edge deve ser usar o `check` comando, que executa uma coleção de testes de configuração e conectividade para questões comuns. O `check` comando está disponível no lançamento [1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) e posterior.

>[!NOTE]
>A ferramenta de resolução de problemas não pode executar verificações de conectividade se o dispositivo IoT Edge estiver por trás de um servidor proxy.

Pode executar o `check` comando da seguinte forma, ou incluir a `--help` bandeira para ver uma lista completa de opções:

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Em Linux:

```bash
sudo iotedge check
```

No Windows:

```powershell
iotedge check
```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.1 -->
:::moniker range=">=iotedge-2020-11"

```bash
sudo iotedge check
```

:::moniker-end
<!-- end 1.2 -->

A ferramenta de resolução de problemas executa muitas verificações que são classificadas nestas três categorias:

* *As verificações* de configuração examinam detalhes que podem impedir que os dispositivos IoT Edge se conectem à nuvem, incluindo problemas com o ficheiro config e o motor do contentor.
* *Verificações de ligação* verificam se o tempo de execução IoT Edge pode aceder às portas do dispositivo anfitrião e que todos os componentes IoT Edge podem ligar-se ao Hub IoT. Este conjunto de verificações retorna erros se o dispositivo IoT Edge estiver por trás de um representante.
* *Os controlos de prontidão da produção* procuram as melhores práticas de produção recomendadas, tais como o estado dos certificados da Autoridade de Certificados do Dispositivo (CA) e a configuração do ficheiro de registo de módulos.

A ferramenta de verificação IoT Edge utiliza um recipiente para executar os seus diagnósticos. A imagem do `mcr.microsoft.com/azureiotedge-diagnostics:latest` recipiente, está disponível através do [Registo de Contentores da Microsoft.](https://github.com/microsoft/containerregistry) Se precisar de verificar um dispositivo sem acesso direto à internet, os seus dispositivos terão de ter acesso à imagem do contentor.

Para obter informações sobre cada uma das verificações de diagnóstico, esta ferramenta funciona, incluindo o que fazer se tiver um erro ou aviso, consulte [as verificações de resolução de problemas do IoT Edge](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="gather-debug-information-with-support-bundle-command"></a>Recolha informações de depurar com comando 'support-bundle'

Quando precisa de recolher registos de um dispositivo IoT Edge, a maneira mais conveniente é utilizar o `support-bundle` comando. Por predefinição, este comando recolhe módulo, gestor de segurança IoT Edge e troncos de motor de contentores, `iotedge check` saída JSON e outras informações úteis de depuração. Comprime-os num único ficheiro para uma partilha fácil. O `support-bundle` comando está disponível no lançamento [1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) e posterior.

Executar o `support-bundle` comando com a bandeira para `--since` especificar quanto tempo a partir do passado deseja obter registos. Por `6h` exemplo, receberá registos desde as últimas seis horas, `6d` desde os últimos seis dias, `6m` desde os últimos seis minutos e assim por diante. Inclua a `--help` bandeira para ver uma lista completa de opções.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Em Linux:

```bash
sudo iotedge support-bundle --since 6h
```

No Windows:

```powershell
iotedge support-bundle --since 6h
```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

```bash
sudo iotedge support-bundle --since 6h
```

:::moniker-end
<!-- end 1.2 -->

Também pode utilizar uma chamada [de método direto](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics) para o seu dispositivo para carregar a saída do comando do pacote de suporte para o Azure Blob Storage.

> [!WARNING]
> A saída do `support-bundle` comando pode conter nomes de hospedeiros, dispositivos e módulos, informações registadas pelos seus módulos, etc. Por favor, esteja ciente disso se partilhar a produção em um fórum público.

## <a name="check-your-iot-edge-version"></a>Verifique a sua versão IoT Edge

Se estiver a executar uma versão mais antiga do IoT Edge, uma atualização poderá resolver o problema. A `iotedge check` ferramenta verifica se o daemon de segurança IoT Edge é a versão mais recente, mas não verifica as versões do hub e dos módulos de agente IoT Edge. Para verificar a versão dos módulos de tempo de execução no seu dispositivo, utilize os comandos `iotedge logs edgeAgent` e `iotedge logs edgeHub` . O número da versão é declarado nos registos quando o módulo é iniciado.

Para obter instruções sobre como atualizar o seu dispositivo, consulte [atualizar o daemon de segurança IoT Edge e o tempo de funcionaamento](how-to-update-iot-edge.md).

## <a name="verify-the-installation-of-iot-edge-on-your-devices"></a>Verifique a instalação de IoT Edge nos seus dispositivos

Pode verificar a instalação do IoT Edge nos seus dispositivos [monitorizando o twin módulo edgeAgent](./how-to-monitor-module-twins.md).

Para obter o mais recente twin módulo edgeAgent, executar o seguinte comando a partir de [Azure Cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub module-twin show --device-id <edge_device_id> --module-id $edgeAgent --hub-name <iot_hub_name>
   ```

Este comando irá desausar todas as [propriedades reportadas](./module-edgeagent-edgehub.md)edgeAgent . Aqui estão algumas úteis monitorizam o estado do dispositivo:

* estado de execução
* tempo de início de execução
* tempo de execução última hora de saída
* contagem de reinício de tempo de execução

## <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Verifique o estado do gestor de segurança IoT Edge e seus registos

O [gestor de segurança IoT Edge](iot-edge-security-manager.md) é responsável por operações como a inicialização do sistema IoT Edge em dispositivos de arranque e provisionamento. Se o IoT Edge não estiver a começar, os registos do gestor de segurança podem fornecer informações úteis.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Em Linux:

* Veja o estado do gestor de segurança IoT Edge:

   ```bash
   sudo systemctl status iotedge
   ```

* Ver os registos do gestor de segurança IoT Edge:

   ```bash
   sudo journalctl -u iotedge -f
   ```

* Ver registos mais detalhados do gestor de segurança IoT Edge:

  1. Editar as definições do daemon IoT Edge:

     ```bash
     sudo systemctl edit iotedge.service
     ```

  2. Atualizar as seguintes linhas:

     ```bash
     [Service]
     Environment=IOTEDGE_LOG=debug
     ```

  3. Reinicie o daemon de segurança IoT Edge:

     ```bash
     sudo systemctl cat iotedge.service
     sudo systemctl daemon-reload
     sudo systemctl restart iotedge
     ```

No Windows:

* Veja o estado do gestor de segurança IoT Edge:

   ```powershell
   Get-Service iotedge
   ```

* Ver os registos do gestor de segurança IoT Edge:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

* Veja apenas os últimos 5 minutos dos registos do gestor de segurança IoT Edge:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog -StartTime ([datetime]::Now.AddMinutes(-5))
   ```

* Ver registos mais detalhados do gestor de segurança IoT Edge:

  1. Adicione uma variável de ambiente ao nível do sistema:

     ```powershell
     [Environment]::SetEnvironmentVariable("IOTEDGE_LOG", "debug", [EnvironmentVariableTarget]::Machine)
     ```

  2. Reinicie o Daemon de Segurança IoT Edge:

     ```powershell
     Restart-Service iotedge
     ```

:::moniker-end
<!--end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

* Veja o estado dos serviços do sistema IoT Edge:

   ```bash
   sudo iotedge system status
   ```

* Ver os registos dos serviços do sistema IoT Edge:

   ```bash
   sudo iotedge system logs -- -f
   ```

* Permitir que os registos de nível de depurado vejam registos mais detalhados dos serviços do sistema IoT Edge:

  1. Ativar registos de nível de depurado.

     ```bash
     sudo iotedge system set-log-level debug
     sudo iotedge system restart
     ```

  1. Volte para os registos de nível de informação predefinidos após a depuração.

     ```bash
     sudo iotedge system set-log-level info
     sudo iotedge system restart
     ```

:::moniker-end
<!-- end 1.2 -->

## <a name="check-container-logs-for-issues"></a>Verifique os registos dos contentores para obter problemas

Assim que o daemon de segurança IoT Edge estiver em funcionamento, olhe para os registos dos contentores para detetar problemas. Comece com os recipientes implantados e, em seguida, olhe para os recipientes que compõem o tempo de funcionação do IoT Edge: edgeAgent e edgeHub. Os registos de agente IoT Edge normalmente fornecem informações sobre o ciclo de vida de cada recipiente. Os registos do hub IoT Edge fornecem informações sobre mensagens e encaminhamento.

```cmd
iotedge logs <container name>
```

Também pode utilizar uma chamada [de método direto](how-to-retrieve-iot-edge-logs.md#upload-module-logs) para um módulo no seu dispositivo para fazer o upload dos registos desse módulo para o Azure Blob Storage.

## <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Veja as mensagens que passam pelo hub IoT Edge

<!--1.1 -->
:::moniker range="iotedge-2018-06"

Pode visualizar as mensagens que passam pelo hub IoT Edge e recolher insights dos registos verbosos dos recipientes de tempo de execução. Para ligar os registos verbosos nestes recipientes, coloque `RuntimeLogLevel` no seu ficheiro de configuração yaml. Para abrir o ficheiro:

Em Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

No Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Por padrão, o `agent` elemento será o seguinte exemplo:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.1
       auth: {}
   ```

Substitua `env: {}` por:

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > Os ficheiros YAML não podem conter separadores como recuo. Utilize 2 espaços em vez disso. Itens de alto nível não podem ter espaço em branco líder.

Guarde o ficheiro e reinicie o gestor de segurança IoT Edge.

<!-- end 1.1 -->
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Pode visualizar as mensagens que passam pelo hub IoT Edge e recolher insights dos registos verbosos dos recipientes de tempo de funcionação. Para ligar os troncos verbosos nestes recipientes, deite a `RuntimeLogLevel` variável ambiente no manifesto de implantação.

Para visualizar as mensagens que passam pelo hub IoT Edge, desacorda a `RuntimeLogLevel` variável ambiente `debug` para o módulo edgeHub.

Tanto os módulos edgeHub como edgeAgent têm esta variável de ambiente de log de tempo de execução, com o valor padrão definido para `info` . Esta variável ambiental pode assumir os seguintes valores:

* fatal
* erro
* aviso
* informações
* depurar
* verbose

<!-- end 1.2 -->
:::moniker-end

Também pode verificar as mensagens que estão a ser enviadas entre dispositivos IoT Hub e IoT. Consulte estas mensagens utilizando a [extensão Azure IoT Hub para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). Para obter mais informações, consulte [a ferramenta Handy quando se desenvolver com a Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

## <a name="restart-containers"></a>Reiniciar contentores

Depois de investigar os registos e mensagens para obter informações, pode tentar reiniciar os recipientes:

```cmd
iotedge restart <container name>
```

Reiniciar os recipientes de tempo de funcionaamento IoT Edge:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

## <a name="check-your-firewall-and-port-configuration-rules"></a>Verifique as regras de firewall e configuração da porta

O Azure IoT Edge permite a comunicação de um servidor no local para a nuvem Azure utilizando protocolos IoT Hub suportados, consulte [a escolha de um protocolo de comunicação](../iot-hub/iot-hub-devguide-protocols.md). Para uma maior segurança, os canais de comunicação entre Azure IoT Edge e Azure IoT Hub estão sempre configurados para serem outbound. Esta configuração baseia-se no [padrão de Comunicação Assistida de Serviços,](/archive/blogs/clemensv/service-assisted-communication-for-connected-devices)que minimiza a superfície de ataque para uma entidade maliciosa explorar. A comunicação de entrada só é necessária para cenários específicos onde o Azure IoT Hub precisa de empurrar mensagens para o dispositivo Azure IoT Edge. As mensagens cloud-to-device são protegidas utilizando canais TLS seguros e podem ser mais protegidas utilizando certificados X.509 e módulos de dispositivoS TPM. O Azure IoT Edge Security Manager governa como esta comunicação pode ser estabelecida, ver [IoT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

Embora o IoT Edge forneça uma configuração melhorada para assegurar o tempo de execução do Azure IoT Edge e os módulos implantados, ainda depende da configuração da máquina e da rede subjacentes. Por isso, é imperativo garantir que as regras adequadas de rede e firewall são criadas para uma borda segura para a comunicação na nuvem. A tabela a seguir pode ser utilizada como uma orientação quando a configuração de firewall reina para os servidores subjacentes onde o tempo de execução do Azure IoT Edge está hospedado:

|Protocolo|Porta|Dados|Saída|Orientação|
|--|--|--|--|--|
|MQTT|8883|BLOQUEADO (Padrão)|BLOQUEADO (Padrão)|<ul> <li>Configure outgoing (Outbound) para estar aberto quando utilizar mQTT como protocolo de comunicação.<li>1883 para MQTT não é suportado pela IoT Edge. <li>As ligações de entrada (entrada) devem ser bloqueadas.</ul>|
|AMQP|5671|BLOQUEADO (Padrão)|ABRIR (Predefinição)|<ul> <li>Protocolo de comunicação padrão para IoT Edge. <li> Deve ser configurado para ser aberto se Azure IoT Edge não estiver configurado para outros protocolos suportados ou AMQP é o protocolo de comunicação pretendido.<li>5672 para AMQP não é suportado pela IoT Edge.<li>Bloqueie esta porta quando o Azure IoT Edge utilizar um protocolo ioT hub diferente.<li>As ligações de entrada (entrada) devem ser bloqueadas.</ul></ul>|
|HTTPS|443|BLOQUEADO (Padrão)|ABRIR (Predefinição)|<ul> <li>Configure outgoing (Outbound) para ser aberto em 443 para provisão IoT Edge. Esta configuração é necessária quando se utilizam scripts manuais ou serviço de provisionamento de dispositivos Azure IoT (DPS). <li>A ligação de entrada (entrada) deve ser aberta apenas para cenários específicos: <ul> <li>  Se tiver uma porta de entrada transparente com dispositivos de folha que pode enviar pedidos de método. Neste caso, o Porto 443 não precisa de estar aberto a redes externas para ligar ao IoTHub ou fornecer serviços IoTHub através do Azure IoT Edge. Assim, a regra de entrada poderia ser restrita apenas a abrir a Entrada (Entrada) da rede interna. <li> Para cenários de Cliente a Dispositivo (C2D).</ul><li>80 para HTTP não é suportado pela IoT Edge.<li>Se os protocolos não-HTTP (por exemplo, AMQP ou MQTT) não puderem ser configurados na empresa; as mensagens podem ser enviadas por WebSockets. A porta 443 será utilizada para a comunicação WebSocket nesse caso.</ul>|

## <a name="next-steps"></a>Passos seguintes

Encontrou um erro na plataforma do IoT Edge? [Submeter um problema](https://github.com/Azure/iotedge/issues) para que possamos continuar a melhorar.

Se tiver mais perguntas, crie um pedido de ajuda [de Apoio.](https://portal.azure.com/#create/Microsoft.Support)
