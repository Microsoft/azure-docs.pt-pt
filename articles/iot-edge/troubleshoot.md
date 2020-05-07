---
title: Troubleshoot - Azure IoT Edge [ Azure IoT Edge ] Microsoft Docs
description: Use este artigo para aprender habilidades de diagnóstico padrão para Azure IoT Edge, como recuperar o estado do componente e os registos
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9b6265bed138960a3839091ed1593413fc85710a
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858590"
---
# <a name="troubleshoot-your-iot-edge-device"></a>Problemas de resolução do seu dispositivo IoT Edge

Se tiver problemas em executar o Azure IoT Edge no seu ambiente, use este artigo como guia para resolução de problemas e diagnósticos.

## <a name="run-the-check-command"></a>Executar o comando 'verificar'

O seu primeiro passo ao resolver problemas `check` ioT Edge deve ser usar o comando, que executa uma coleção de testes de configuração e conectividade para problemas comuns. O `check` comando está disponível no [lançamento 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) e mais tarde.

>[!NOTE]
>A ferramenta de resolução de problemas não pode executar verificações de conectividade se o dispositivo IoT Edge estiver por trás de um servidor proxy.

Pode executar `check` o comando da seguinte `--help` forma, ou incluir a bandeira para ver uma lista completa de opções:

Em Linux:

```bash
sudo iotedge check
```

No Windows:

```powershell
iotedge check
```

A ferramenta de resolução de problemas executa muitos controlos que estão classificados nestas três categorias:

* *As verificações* de configuração examinam detalhes que podem impedir que os dispositivos IoT Edge se liguem à nuvem, incluindo problemas com *o config.yaml* e o motor do recipiente.
* *As verificações* de ligação verificam se o tempo de execução do IoT Edge pode aceder às portas do dispositivo hospedeiro e que todos os componentes do IoT Edge podem ligar-se ao Hub IoT. Este conjunto de verificações devolve erros se o dispositivo IoT Edge estiver por trás de um proxy.
* *As verificações* de prontidão de produção procuram as melhores práticas recomendadas de produção, tais como os certificados de autoridade de certificados de dispositivo (CA) e a configuração de ficheiros de registo de módulos.

Para obter informações sobre cada uma das verificações de diagnóstico, esta ferramenta funciona, incluindo o que fazer se tiver um erro ou aviso, consulte as verificações de [problemas de IoT Edge](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="gather-debug-information-with-support-bundle-command"></a>Recolher informações sobre depuração com comando 'support-bundle'

Quando precisa de recolher registos de um dispositivo IoT Edge, `support-bundle` a forma mais conveniente é utilizar o comando. Por padrão, este comando recolhe módulo, gestor de segurança IoT Edge e registos de motores de contentores, `iotedge check` saída JSON e outras informações úteis de depuração. Comprime-os num único ficheiro para uma partilha fácil. O `support-bundle` comando está disponível no [lançamento 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) e mais tarde.

Mande `support-bundle` o `--since` comando com a bandeira para especificar quanto tempo do passado pretende obter registos. Por `6h` exemplo, receberá registos desde `6d` as últimas seis `6m` horas, desde os últimos seis dias, desde os últimos seis minutos e assim por diante. Inclua `--help` a bandeira para ver uma lista completa de opções.

Em Linux:

```bash
sudo iotedge support-bundle --since 6h
```

No Windows:

```powershell
iotedge support-bundle --since 6h
```

> [!WARNING]
> A saída `support-bundle` do comando pode conter nomes de hospedeiro, dispositivo e módulo, informações registadas pelos seus módulos, etc. Por favor, esteja ciente disso se partilhar a produção em um fórum público.

## <a name="check-your-iot-edge-version"></a>Verifique a sua versão IoT Edge

Se estiver a executar uma versão mais antiga do IoT Edge, então a atualização pode resolver o seu problema. A `iotedge check` ferramenta verifica que o daemon de segurança IoT Edge é a versão mais recente, mas não verifica as versões do hub IoT Edge e módulos de agente. Para verificar a versão dos módulos de tempo de `iotedge logs edgeAgent` `iotedge logs edgeHub`execução no seu dispositivo, utilize os comandos e . O número da versão é declarado nos registos quando o módulo está aceso.

Para obter instruções sobre como atualizar o seu dispositivo, consulte [Atualizar o daemon de segurança IoT Edge e o tempo](how-to-update-iot-edge.md)de funcionação .

## <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Verifique o estado do gestor de segurança IoT Edge e seus registos

O [gestor de segurança IoT Edge](iot-edge-security-manager.md) é responsável por operações como a inicialização do sistema IoT Edge em dispositivos de arranque e provisionamento. Se o IoT Edge não começar, os registos do gestor de segurança podem fornecer informações úteis.

Em Linux:

* Ver o estado do gestor de segurança IoT Edge:

   ```bash
   sudo systemctl status iotedge
   ```

* Veja os registos do gestor de segurança IoT Edge:

    ```bash
    sudo journalctl -u iotedge -f
    ```

* Consulte registos mais detalhados do gestor de segurança IoT Edge:

  * Editar as definições de daemon IoT Edge:

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

* Ver o estado do gestor de segurança IoT Edge:

   ```powershell
   Get-Service iotedge
   ```

* Veja os registos do gestor de segurança IoT Edge:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

* Veja apenas os últimos 5 minutos dos registos do gestor de segurança IoT Edge:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog -StartTime ([datetime]::Now.AddMinutes(-5))
   ```

* Consulte registos mais detalhados do gestor de segurança IoT Edge:

  * Adicione uma variável ambiental ao nível do sistema:

      ```powershell
      [Environment]::SetEnvironmentVariable("IOTEDGE_LOG", "edgelet=debug", [EnvironmentVariableTarget]::Machine)
      ```

  * Reinicie o Daemon de Segurança IoT Edge:

      ```powershell
      Restart-Service iotedge
      ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Se o gestor de segurança IoT Edge não estiver em execução, verifique o seu ficheiro de configuração do yaml

> [!WARNING]
> Os ficheiros YAML não podem conter os separadores como recuo. Utilize 2 espaços em vez disso. Elementos de alto nível não devem ter espaços de liderança.

Em Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

No Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="restart-the-iot-edge-security-manager"></a>Reinicie o gestor de segurança IoT Edge

Se o problema ainda persistir, pode tentar reiniciar o gestor de segurança IoT Edge.

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

## <a name="check-container-logs-for-issues"></a>Verifique os registos de contentores para obter problemas

Assim que o daemon de segurança ioT Edge estiver em funcionamento, olhe para os registos dos contentores para detetar problemas. Comece com os recipientes implantados e, em seguida, olhe para os recipientes que compõem o tempo de execução ioT Edge: edgeAgent e edgeHub. Os registos do agente IoT Edge normalmente fornecem informações sobre o ciclo de vida de cada recipiente. Os registos do hub IoT Edge fornecem informações sobre mensagens e encaminhamento.

```cmd
iotedge logs <container name>
```

## <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Veja as mensagens que passam pelo centro de IoT Edge

Pode ver as mensagens que passam pelo centro ioT Edge e recolher informações dos registos verbosos dos recipientes de tempo de execução. Para ligar os registos verbosos `RuntimeLogLevel` nestes recipientes, coloque no seu ficheiro de configuração do yaml. Para abrir o ficheiro:

Em Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

No Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Por padrão, `agent` o elemento será o seguinte exemplo:

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
   > Os ficheiros YAML não podem conter separadores como identificação. Utilize 2 espaços em vez disso. Itens de alto nível não podem ter espaço branco líder.

Guarde o ficheiro e reinicie o gestor de segurança IoT Edge.

Também pode verificar as mensagens que são enviadas entre o Hub IoT e os dispositivos do IoT Edge. Veja estas mensagens utilizando a [extensão Do Hub Azure IoT para O Código do Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). Para mais informações, consulte a [ferramenta Handy quando se desenvolver com o Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

## <a name="restart-containers"></a>Reiniciar contentores

Depois de investigar os registos e mensagens para obter informações, pode tentar reiniciar os contentores:

```cmd
iotedge restart <container name>
```

Reiniciar os recipientes de tempo de execução IoT Edge:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

## <a name="check-your-firewall-and-port-configuration-rules"></a>Verifique as regras de configuração da sua firewall e porta

O Azure IoT Edge permite a comunicação de um servidor no local para a nuvem Azure utilizando protocolos IoT Hub suportados, ver escolher um protocolo de [comunicação](../iot-hub/iot-hub-devguide-protocols.md). Para uma maior segurança, os canais de comunicação entre o Azure IoT Edge e o Azure IoT Hub estão sempre configurados para serem Outbound. Esta configuração baseia-se no padrão de [Comunicação Assistida](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/)de Serviços, que minimiza a superfície de ataque para uma entidade maliciosa explorar. A comunicação de entrada só é necessária para cenários específicos em que o Hub Azure IoT precisa de empurrar mensagens para o dispositivo Azure IoT Edge. As mensagens cloud-to-device estão protegidas utilizando canais TLS seguros e podem ser mais seguras utilizando certificados X.509 e módulos de dispositivoTPM. O Gestor de Segurança de Borda Sarna Azure IoT rege a forma como esta comunicação pode ser estabelecida, consulte [IoT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

Embora o IoT Edge forneça uma configuração melhorada para assegurar o tempo de funcionamento do Edge Azure IoT edge e os módulos implantados, ainda está dependente da configuração da máquina e da rede subjacentes. Por conseguinte, é imperativo garantir que sejam criadas regras adequadas de rede e firewall para uma borda segura para a comunicação em nuvem. A tabela a seguir pode ser utilizada como uma orientação quando as regras de firewall de configuração para os servidores subjacentes onde o tempo de execução do Edge Azure IoT edge é hospedado:

|Protocolo|Porta|Dados|Saída|Orientação|
|--|--|--|--|--|
|MQTT|8883|BLOQUEADO (Padrão)|BLOQUEADO (Padrão)|<ul> <li>Configure a saída (outbound) para estar aberta ao utilizar o MQTT como protocolo de comunicação.<li>1883 para MQTT não é suportado por IoT Edge. <li>As ligações de entrada (entrada) devem ser bloqueadas.</ul>|
|AMQP|5671|BLOQUEADO (Padrão)|ABERTO (Padrão)|<ul> <li>Protocolo de comunicação padrão para IoT Edge. <li> Deve ser configurado para estar aberto se o Azure IoT Edge não estiver configurado para outros protocolos suportados ou AMQP é o protocolo de comunicação pretendido.<li>5672 para AMQP não é suportado por IoT Edge.<li>Bloqueie esta porta quando o Azure IoT Edge utilizar um protocolo suportado pelo IoT Hub.<li>As ligações de entrada (entrada) devem ser bloqueadas.</ul></ul>|
|HTTPS|443|BLOQUEADO (Padrão)|ABERTO (Padrão)|<ul> <li>Configure Outgoing (Outbound) para ser aberto em 443 para o fornecimento de IoT Edge. Esta configuração é necessária quando se utilizam scripts manuais ou serviço de provisionamento de dispositivos Azure IoT (DPS). <li>A ligação de entrada (entrada) deve ser aberta apenas para cenários específicos: <ul> <li>  Se tiver um portal transparente com dispositivos de folhas que possam enviar pedidos de método. Neste caso, o Porto 443 não precisa de estar aberto a redes externas para ligar ao IoTHub ou fornecer serviços IoTHub através do Azure IoT Edge. Assim, a regra de entrada poderia limitar-se a abrir apenas a entrada (Entrada) da rede interna. <li> Para cenários de cliente para dispositivo (C2D).</ul><li>80 para HTTP não é suportado por IoT Edge.<li>Se os protocolos não HTTP (por exemplo, AMQP ou MQTT) não puderem ser configurados na empresa; as mensagens podem ser enviadas por WebSockets. A porta 443 será utilizada para a comunicação WebSocket neste caso.</ul>|

## <a name="next-steps"></a>Passos seguintes

Encontrou um erro na plataforma do IoT Edge? [Submeta um problema](https://github.com/Azure/iotedge/issues) para que possamos continuar a melhorar.

Se tiver mais perguntas, crie um pedido de [apoio](https://portal.azure.com/#create/Microsoft.Support) para ajuda.
