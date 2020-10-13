---
title: Resolução de problemas - Azure IoT Edge / Microsoft Docs
description: Use este artigo para aprender habilidades de diagnóstico padrão para Azure IoT Edge, como recuperar o estado do componente e os registos
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0e4ec7127df288ec1818df307da1ea9824141309
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87902461"
---
# <a name="troubleshoot-your-iot-edge-device"></a>Resolução de problemas do seu dispositivo IoT Edge

Se sentir problemas em executar Azure IoT Edge no seu ambiente, utilize este artigo como guia para resolução de problemas e diagnósticos.

## <a name="run-the-check-command"></a>Executar o comando 'verificar'

O seu primeiro passo para resolver problemas IoT Edge deve ser usar o `check` comando, que executa uma coleção de testes de configuração e conectividade para questões comuns. O `check` comando está disponível no lançamento [1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) e posterior.

>[!NOTE]
>A ferramenta de resolução de problemas não pode executar verificações de conectividade se o dispositivo IoT Edge estiver por trás de um servidor proxy.

Pode executar o `check` comando da seguinte forma, ou incluir a `--help` bandeira para ver uma lista completa de opções:

Em Linux:

```bash
sudo iotedge check
```

No Windows:

```powershell
iotedge check
```

A ferramenta de resolução de problemas executa muitas verificações que são classificadas nestas três categorias:

* *As verificações* de configuração examinam detalhes que podem impedir que os dispositivos IoT Edge se conectem à nuvem, incluindo problemas com *config.yaml* e o motor do recipiente.
* *Verificações de ligação* verificam se o tempo de execução IoT Edge pode aceder às portas do dispositivo anfitrião e que todos os componentes IoT Edge podem ligar-se ao Hub IoT. Este conjunto de verificações retorna erros se o dispositivo IoT Edge estiver por trás de um representante.
* *Os controlos de prontidão da produção* procuram as melhores práticas de produção recomendadas, tais como o estado dos certificados da Autoridade de Certificados do Dispositivo (CA) e a configuração do ficheiro de registo de módulos.

Para obter informações sobre cada uma das verificações de diagnóstico, esta ferramenta funciona, incluindo o que fazer se tiver um erro ou aviso, consulte [as verificações de resolução de problemas do IoT Edge](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="gather-debug-information-with-support-bundle-command"></a>Recolha informações de depurar com comando 'support-bundle'

Quando precisa de recolher registos de um dispositivo IoT Edge, a maneira mais conveniente é utilizar o `support-bundle` comando. Por predefinição, este comando recolhe módulo, gestor de segurança IoT Edge e troncos de motor de contentores, `iotedge check` saída JSON e outras informações úteis de depuração. Comprime-os num único ficheiro para uma partilha fácil. O `support-bundle` comando está disponível no lançamento [1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) e posterior.

Executar o `support-bundle` comando com a bandeira para `--since` especificar quanto tempo a partir do passado deseja obter registos. Por `6h` exemplo, receberá registos desde as últimas seis horas, `6d` desde os últimos seis dias, `6m` desde os últimos seis minutos e assim por diante. Inclua a `--help` bandeira para ver uma lista completa de opções.

Em Linux:

```bash
sudo iotedge support-bundle --since 6h
```

No Windows:

```powershell
iotedge support-bundle --since 6h
```

> [!WARNING]
> A saída do `support-bundle` comando pode conter nomes de hospedeiros, dispositivos e módulos, informações registadas pelos seus módulos, etc. Por favor, esteja ciente disso se partilhar a produção em um fórum público.

## <a name="check-your-iot-edge-version"></a>Verifique a sua versão IoT Edge

Se estiver a executar uma versão mais antiga do IoT Edge, uma atualização poderá resolver o problema. A `iotedge check` ferramenta verifica se o daemon de segurança IoT Edge é a versão mais recente, mas não verifica as versões do hub e dos módulos de agente IoT Edge. Para verificar a versão dos módulos de tempo de execução no seu dispositivo, utilize os comandos `iotedge logs edgeAgent` e `iotedge logs edgeHub` . O número da versão é declarado nos registos quando o módulo é iniciado.

Para obter instruções sobre como atualizar o seu dispositivo, consulte [atualizar o daemon de segurança IoT Edge e o tempo de funcionaamento](how-to-update-iot-edge.md).

## <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Verifique o estado do gestor de segurança IoT Edge e seus registos

O [gestor de segurança IoT Edge](iot-edge-security-manager.md) é responsável por operações como a inicialização do sistema IoT Edge em dispositivos de arranque e provisionamento. Se o IoT Edge não estiver a começar, os registos do gestor de segurança podem fornecer informações úteis.

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

  * Editar as definições do daemon IoT Edge:

      ```bash
      sudo systemctl edit iotedge.service
      ```

  * Atualizar as seguintes linhas:

      ```bash
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```

  * Reinicie o Daemon de Segurança IoT Edge:

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

  * Adicione uma variável de ambiente ao nível do sistema:

      ```powershell
      [Environment]::SetEnvironmentVariable("IOTEDGE_LOG", "debug", [EnvironmentVariableTarget]::Machine)
      ```

  * Reinicie o Daemon de Segurança IoT Edge:

      ```powershell
      Restart-Service iotedge
      ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Se o gestor de segurança IoT Edge não estiver em execução, verifique o seu ficheiro de configuração yaml

> [!WARNING]
> Os ficheiros YAML não podem conter separadores como recuo. Utilize 2 espaços em vez disso. Elementos de alto nível não devem ter espaços de liderança.

Em Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

No Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="restart-the-iot-edge-security-manager"></a>Reiniciar o gestor de segurança IoT Edge

Se o problema persistir, pode tentar reiniciar o gestor de segurança IoT Edge.

Em Linux:

   ```cmd
   sudo systemctl restart iotedge
   ```

No Windows:

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="check-container-logs-for-issues"></a>Verifique os registos dos contentores para obter problemas

Assim que o daemon de segurança IoT Edge estiver em funcionamento, olhe para os registos dos contentores para detetar problemas. Comece com os recipientes implantados e, em seguida, olhe para os recipientes que compõem o tempo de funcionação do IoT Edge: edgeAgent e edgeHub. Os registos de agente IoT Edge normalmente fornecem informações sobre o ciclo de vida de cada recipiente. Os registos do hub IoT Edge fornecem informações sobre mensagens e encaminhamento.

```cmd
iotedge logs <container name>
```

## <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Veja as mensagens que passam pelo hub IoT Edge

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
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

Substitua `env: {}` por:

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > Os ficheiros YAML não podem conter separadores como identificação. Utilize 2 espaços em vez disso. Itens de alto nível não podem ter espaço em branco líder.

Guarde o ficheiro e reinicie o gestor de segurança IoT Edge.

Também pode verificar as mensagens que são enviadas entre o Hub IoT e os dispositivos do IoT Edge. Consulte estas mensagens utilizando a [extensão Azure IoT Hub para Código de Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). Para obter mais informações, consulte [a ferramenta Handy quando se desenvolver com a Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

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

O Azure IoT Edge permite a comunicação de um servidor no local para a nuvem Azure utilizando protocolos IoT Hub suportados, consulte [a escolha de um protocolo de comunicação](../iot-hub/iot-hub-devguide-protocols.md). Para uma maior segurança, os canais de comunicação entre Azure IoT Edge e Azure IoT Hub estão sempre configurados para serem outbound. Esta configuração baseia-se no [padrão de Comunicação Assistida de Serviços,](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/)que minimiza a superfície de ataque para uma entidade maliciosa explorar. A comunicação de entrada só é necessária para cenários específicos onde o Azure IoT Hub precisa de empurrar mensagens para o dispositivo Azure IoT Edge. As mensagens cloud-to-device são protegidas utilizando canais TLS seguros e podem ser mais protegidas utilizando certificados X.509 e módulos de dispositivoS TPM. O Azure IoT Edge Security Manager governa como esta comunicação pode ser estabelecida, ver [IoT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

Embora o IoT Edge forneça uma configuração melhorada para assegurar o tempo de execução do Azure IoT Edge e os módulos implantados, ainda depende da configuração da máquina e da rede subjacentes. Por isso, é imperativo garantir que as regras adequadas de rede e firewall são criadas para uma borda segura para a comunicação na nuvem. A tabela a seguir pode ser utilizada como uma orientação quando a configuração de firewall reina para os servidores subjacentes onde o tempo de execução do Azure IoT Edge está hospedado:

|Protocolo|Porta|Dados|Saída|Orientação|
|--|--|--|--|--|
|MQTT|8883|BLOQUEADO (Padrão)|BLOQUEADO (Padrão)|<ul> <li>Configure outgoing (Outbound) para estar aberto quando utilizar mQTT como protocolo de comunicação.<li>1883 para MQTT não é suportado pela IoT Edge. <li>As ligações de entrada (entrada) devem ser bloqueadas.</ul>|
|AMQP|5671|BLOQUEADO (Padrão)|ABRIR (Predefinição)|<ul> <li>Protocolo de comunicação padrão para IoT Edge. <li> Deve ser configurado para ser aberto se Azure IoT Edge não estiver configurado para outros protocolos suportados ou AMQP é o protocolo de comunicação pretendido.<li>5672 para AMQP não é suportado pela IoT Edge.<li>Bloqueie esta porta quando o Azure IoT Edge utilizar um protocolo ioT hub diferente.<li>As ligações de entrada (entrada) devem ser bloqueadas.</ul></ul>|
|HTTPS|443|BLOQUEADO (Padrão)|ABRIR (Predefinição)|<ul> <li>Configure outgoing (Outbound) para ser aberto em 443 para provisão IoT Edge. Esta configuração é necessária quando se utilizam scripts manuais ou serviço de provisionamento de dispositivos Azure IoT (DPS). <li>A ligação de entrada (entrada) deve ser aberta apenas para cenários específicos: <ul> <li>  Se tiver uma porta de entrada transparente com dispositivos de folha que pode enviar pedidos de método. Neste caso, o Porto 443 não precisa de estar aberto a redes externas para ligar ao IoTHub ou fornecer serviços IoTHub através do Azure IoT Edge. Assim, a regra de entrada poderia ser restrita apenas a abrir a Entrada (Entrada) da rede interna. <li> Para cenários de Cliente a Dispositivo (C2D).</ul><li>80 para HTTP não é suportado pela IoT Edge.<li>Se os protocolos não-HTTP (por exemplo, AMQP ou MQTT) não puderem ser configurados na empresa; as mensagens podem ser enviadas por WebSockets. A porta 443 será utilizada para a comunicação WebSocket nesse caso.</ul>|

## <a name="next-steps"></a>Passos seguintes

Encontrou um erro na plataforma do IoT Edge? [Submeter um problema](https://github.com/Azure/iotedge/issues) para que possamos continuar a melhorar.

Se tiver mais perguntas, crie um pedido de ajuda [de Apoio.](https://portal.azure.com/#create/Microsoft.Support)
