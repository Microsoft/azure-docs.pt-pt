---
title: Resolução de problemas - o Azure IoT Edge | Documentos da Microsoft
description: Utilize este artigo para aprender competências de diagnóstico padrão para o Azure IoT Edge, como obter o estado do componente e os registos e resolver problemas comuns
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 279b09250aaa3a87e066ade81fc63719adaf07b5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429394"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Problemas comuns e resoluções do Azure IoT Edge

Se ocorrerem problemas com o Azure IoT Edge no seu ambiente, utilize este artigo como guia para a resolução de problemas.

## <a name="run-the-iotedge-check-command"></a>Executar o comando ' check ' do iotedge

Sua primeira etapa ao solucionar problemas IoT Edge deve ser usar o comando `check`, que executa uma coleção de testes de configuração e conectividade para problemas comuns. O comando `check` está disponível na [versão 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) e posterior.

Você pode executar o comando `check` da seguinte maneira ou incluir o sinalizador `--help` para ver uma lista completa de opções:

* No Linux:

  ```bash
  sudo iotedge check
  ```

* No Windows:

  ```powershell
  iotedge check
  ```

Os tipos de verificações executadas pela ferramenta podem ser classificados como:

* Verificações de configuração: examina os detalhes que podem impedir que dispositivos de borda se conectem à nuvem, incluindo problemas com *config. YAML* e o mecanismo de contêiner.
* Verificações de conexão: verifica se o tempo de execução do IoT Edge pode acessar portas no dispositivo host e se todos os componentes do IoT Edge podem se conectar ao Hub IoT.
* Verificações de preparação de produção: procura práticas recomendadas de produção recomendadas, como o estado de certificados de autoridade de certificação de dispositivo (CA) e configuração de arquivo de log de módulo.

Para obter uma lista completa de verificações de diagnóstico, consulte [funcionalidade interna de solução de problemas](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="standard-diagnostic-steps"></a>Passos de diagnóstico padrão

Se você encontrar um problema, poderá saber mais sobre o estado do seu dispositivo de IoT Edge examinando os logs de contêiner e as mensagens que passam de e para o dispositivo. Utilize as ferramentas e comandos nesta secção para recolher informações.

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Verificar o status do IoT Edge Security Manager e seus logs

No Linux:

* Para ver o estado do Gestor de segurança de borda de IoT:

   ```bash
   sudo systemctl status iotedge
   ```

* Para ver os registos do Gestor de segurança de borda de IoT:

    ```bash
    sudo journalctl -u iotedge -f
    ```

* Para ver mais detalhadas registos do Gestor de segurança de borda de IoT:

  * Edite as definições de daemon de iotedge:

      ```bash
      sudo systemctl edit iotedge.service
      ```

  * Atualize as seguintes linhas:

      ```bash
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```

  * Reinicie o Daemon de segurança de IoT Edge:

      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

No Windows:

* Para ver o estado do Gestor de segurança de borda de IoT:

   ```powershell
   Get-Service iotedge
   ```

* Para ver os registos do Gestor de segurança de borda de IoT:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Se o Gestor de segurança do IoT Edge não está em execução, verifique se o ficheiro de configuração yaml

> [!WARNING]
> Os arquivos YAML não podem conter guias como recuo. Utilize espaços de 2 em vez disso.

No Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

No Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Verifique os registos de contentor para problemas

Assim que o Daemon de segurança do IoT Edge está em execução, consulte os registos dos contentores para detetar problemas. Comece com seus contêineres implantados e examine os contêineres que compõem o tempo de execução de IoT Edge: edgeAgent e edgeHub. Os logs do agente de IoT Edge normalmente fornecem informações sobre o ciclo de vida de cada contêiner. Os logs de Hub de IoT Edge fornecem informações sobre mensagens e roteamento. 

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Exibir as mensagens que passam pelo hub de IoT Edge

Você pode exibir as mensagens que passam pelo hub de IoT Edge e coletar informações de logs detalhados dos contêineres de tempo de execução. Para ativar registos verbosos nestes contentores, defina `RuntimeLogLevel` no ficheiro de configuração de yaml. Para abrir o ficheiro:

No Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

No Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Por predefinição, o `agent` elemento terá um aspeto semelhante ao seguinte exemplo:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

Substitua `env: {}` com:

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > Ficheiros YAML não podem conter separadores como identation. Utilize espaços de 2 em vez disso.

Guarde o ficheiro e reinicie o Gestor de segurança de IoT Edge.

Também pode verificar as mensagens que são enviadas entre o Hub IoT e os dispositivos do IoT Edge. Exiba essas mensagens usando a extensão do [Kit de ferramentas do Hub IOT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (anteriormente a extensão do kit de ferramentas do Azure IOT) para Visual Studio Code. Para obter mais informações, consulte [ferramenta útil ao desenvolver com o Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

### <a name="restart-containers"></a>Reiniciar contentores

Depois de investigar os registos e as mensagens para informações, pode tentar reiniciar contentores:

```cmd
iotedge restart <container name>
```

Reinicie os contentores de runtime do IoT Edge:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Reinicie o Gestor de segurança de IoT Edge

Se o problema ainda mantém, pode tentar reiniciar o Gestor de segurança de IoT Edge.

No Linux:

   ```cmd
   sudo systemctl restart iotedge
   ```

No Windows:

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>IoT Edge agente parar após cerca de um minuto

O módulo edgeAgent é iniciado e executado com êxito por cerca de um minuto e, em seguida, para. Os logs indicam que o agente de IoT Edge tenta se conectar ao Hub IoT em AMQP e, em seguida, tenta se conectar usando o AMQP por WebSocket. Quando isso falhar, o agente de IoT Edge será encerrado.

EdgeAgent logs de exemplo:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**Causa raiz**

Uma configuração de rede na rede do host está impedindo o agente de IoT Edge de acessar a rede. O agente primeiro tenta ligar através de AMQP (porta 5671). Se a ligação falhar, tenta WebSockets (porta 443).

O runtime do IoT Edge configura uma rede para comunicar em cada um dos módulos. No Linux, esta rede é uma rede de ponte. No Windows, utiliza NAT. Este problema é mais comum nos dispositivos Windows com contentores do Windows que utilizam a rede NAT.

**Resolução**

Certifique-se de que existe uma rota para a Internet para os endereços IP atribuídos a esta rede de ponte/NAT. Por vezes, uma configuração de VPN no anfitrião sobrepõe-se à rede do IoT Edge.

## <a name="iot-edge-hub-fails-to-start"></a>Falha ao iniciar o Hub de IoT Edge

O módulo edgeHub falha ao iniciar e imprime a seguinte mensagem nos logs:

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

**Causa raiz**

Outro processo no computador anfitrião vinculou a porta 443. O Hub IoT Edge mapeia as portas 5671 e 443 para uso em cenários de gateway. Este mapeamento de portas falha se outro processo já tiver vinculado esta porta.

**Resolução**

Encontre e pare o processo que está a utilizar a porta 443. Este processo é normalmente um servidor Web.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>O agente de IoT Edge não pode acessar a imagem de um módulo (403)

Um contêiner não é executado e os logs do edgeAgent mostram um erro 403.

**Causa raiz**

O agente do IOT Edge não tem permissões para acessar a imagem de um módulo.

**Resolução**

Certifique-se de que as suas credenciais de registo estão especificados corretamente no seu manifesto de implantação

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>O daemon de segurança de IoT Edge falha com um nome de anfitrião inválido

O comando `sudo journalctl -u iotedge` falhar e imprime a mensagem seguinte:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Causa raiz**

O runtime do IoT Edge só pode suportar os nomes de anfitrião que são mais curta do que 64 carateres. Máquinas físicas, normalmente, não têm nomes de anfitrião longo, mas o problema é mais comum numa máquina virtual. Os nomes de anfitrião gerados automaticamente para máquinas de virtuais do Windows alojadas no Azure, em particular, tendem a ser longos. 

**Resolução**

Quando vir este erro, pode resolvê-lo ao configurar o nome DNS da sua máquina virtual e, em seguida, definir o nome DNS como o nome de anfitrião no comando de configuração.

1. No portal do Azure, navegue para a página de descrição geral da sua máquina virtual.
2. Selecione **configurar** sob o nome DNS. Se a sua máquina virtual já tem um nome DNS configurado, não precisa de configurar um novo.

   ![Configurar nome DNS da máquina virtual](./media/troubleshoot/configure-dns.png)

3. Forneça um valor para **etiqueta de nome DNS** e selecione **guardar**.
4. Copie o novo nome DNS, o que deve estar no formato  **\<DNSnamelabel\>.\< uma vmlocation\>. cloudapp.azure.com**.
5. Dentro da máquina virtual, utilize o seguinte comando para configurar o runtime do IoT Edge com o nome de DNS:

   * No Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * No Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Problemas de estabilidade no recurso restrita dispositivos

Pode encontrar problemas de estabilidade nos dispositivos restritos, como o Raspberry Pi, especialmente quando utilizado como um gateway. Os sintomas incluem exceções de memória no módulo de hub do edge insuficiente, não é possível ligar dispositivos downstream ou o dispositivo deixa de enviar mensagens de telemetria após algumas horas.

**Causa raiz**

O Hub de IoT Edge, que faz parte do tempo de execução do IoT Edge, é otimizado para desempenho por padrão e tenta alocar grandes partes de memória. Esta otimização não é ideal para dispositivos periféricos restrita e pode causar problemas de estabilidade.

**Resolução**

Para o Hub de IoT Edge, defina uma variável de ambiente **OptimizeForPerformance** como **false**. Existem duas formas de fazer isso:

No portal do Azure:

No Hub IoT, selecione o dispositivo IoT Edge e, na página detalhes do dispositivo e selecione **definir módulos** > **configurações de tempo de execução**. Crie uma variável de ambiente para o módulo de Hub do Edge chamado *OptimizeForPerformance* que está definido como *false*.

![OptimizeForPerformance definido como falso](./media/troubleshoot/optimizeforperformance-false.png)

**OR**

No manifesto de implantação:

```json
  "edgeHub": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
      "createOptions": <snipped>
    },
    "env": {
      "OptimizeForPerformance": {
          "value": "false"
      }
    },
```

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Não é possível obter registos de daemon o IoT Edge no Windows

Se obtiver um EventLogException quando utilizar `Get-WinEvent` no Windows, verifique as suas entradas de registo.

**Causa raiz**

O `Get-WinEvent` comando do PowerShell se baseia numa entrada de registo estar presente para localizar os registos por um específico `ProviderName`.

**Resolução**

Defina uma entrada de registo para o daemon de IoT Edge. Criar uma **iotedge.reg** com o seguinte conteúdo de ficheiros e importar para o registo do Windows ao fazer duplo clique ou utilizar o `reg import iotedge.reg` comando:

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>Falha do módulo do IoT Edge enviar uma mensagem para o edgeHub com o erro 404

Um personalizado ocorre uma falha do módulo de IoT Edge para enviar uma mensagem para o edgeHub com um 404 `Module not found` erro. O daemon de IoT Edge imprime a mensagem seguinte nos registos: 

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 ) 
```

**Causa raiz**

O daemon de IoT Edge impõe a identificação de processo para ligar ao edgeHub por motivos de segurança de todos os módulos. Verifica que todas as mensagens sendo enviadas por um módulo é proveniente do ID de processo principal do módulo. Se uma mensagem está a ser enviada por um módulo de um ID de processo diferente do que inicialmente estabelecido, ele rejeitará a mensagem com uma mensagem de 404 erro.

**Resolução**

A partir da versão 1.0.7, todos os processos de módulo estão autorizados a se conectar. Se a atualização para 1.0.7 não for possível, conclua as etapas a seguir. Para obter mais informações, consulte a [versão de changelog do 1.0.7](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Certifique-se de que o mesmo ID de processo é sempre usado pelo módulo do IoT Edge personalizado para enviar mensagens para o edgeHub. Por exemplo, certifique-se de `ENTRYPOINT` em vez de `CMD` comando no arquivo do Docker, já que `CMD` levará a uma ID de processo para o módulo e outra ID de processo para o comando bash que executa o programa principal, enquanto `ENTRYPOINT` levará a uma única ID de processo.

## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Regras de configuração de firewall e a porta para implementação do IoT Edge

Azure IoT Edge permite a comunicação de um servidor local para a nuvem do Azure usando protocolos de Hub IoT com suporte, consulte [escolhendo um protocolo de comunicação](../iot-hub/iot-hub-devguide-protocols.md). Para maior segurança, os canais de comunicação entre o Azure IoT Edge e IoT Hub do Azure estão sempre configurados para serem saída. Esta configuração se baseia a [padrão de comunicação Auxiliado de serviços](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/), que minimiza a superfície de ataque para uma entidade maliciosa explorar. Comunicação de entrada só é necessário para cenários específicos em que precisa enviar mensagens para o dispositivo Azure IoT Edge IoT Hub do Azure. Mensagens da cloud para dispositivo estão protegidas através de canais TLS seguras e podem ser ainda mais protegidas usando certificados X.509 e módulos de dispositivo TPM. O Gestor de segurança do Azure IoT Edge rege como essa comunicação pode ser estabelecida, consulte [Gestor de segurança do Microsoft Edge IoT](../iot-edge/iot-edge-security-manager.md).

Embora o IoT Edge fornece uma configuração avançada para proteger o runtime do Azure IoT Edge e implementar módulos, é ainda depende da configuração de máquina e da rede subjacente. Portanto, é imperativo garantir que as regras adequadas de rede e firewall sejam configuradas para proteger a comunicação em nuvem. A tabela a seguir pode ser usada como uma diretriz quando as regras de firewall de configuração para os servidores subjacentes em que o tempo de execução Azure IoT Edge está hospedado:

|Protocolo|Porta|entrada|Saída|Orientações|
|--|--|--|--|--|
|MQTT|8883|BLOQUEADO (predefinição)|BLOQUEADO (predefinição)|<ul> <li>Configure o envio (saída) para ser aberto quando utiliza MQTT como o protocolo de comunicação.<li>1883 para MQTT não é suportado pelo IoT Edge. <li>Ligações de entrada (entrada) devem ser bloqueadas.</ul>|
|AMQP|5671|BLOQUEADO (predefinição)|ABERTO (predefinição)|<ul> <li>Protocolo de comunicação padrão para o IoT Edge. <li> Tem de ser configurado para ser aberto, se o Azure IoT Edge não está configurado para outros protocolos suportados ou AMQP é o protocolo de comunicação desejada.<li>5672 para AMQP não é suportado pelo IoT Edge.<li>Bloquear esta porta, quando o protocolo de suporte do Azure IoT Edge utiliza um Hub de IoT diferentes.<li>Ligações de entrada (entrada) devem ser bloqueadas.</ul></ul>|
|HTTPS|443|BLOQUEADO (predefinição)|ABERTO (predefinição)|<ul> <li>Configure o envio (saída) para ser aberto em 443 para o aprovisionamento do IoT Edge. Esta configuração é necessária quando usar scripts manuais ou do Azure IoT dispositivo aprovisionamento DPS (serviço). <li>Conexão de entrada (entrada) deve ser aberto apenas para cenários específicos: <ul> <li>  Se tiver um gateway transparente com dispositivos de folha que poderá enviar pedidos de método. Neste caso, porta 443 não precisa de estar abertas a redes externas para ligar ao IoTHub ou fornecer serviços de IoTHub através do Azure IoT Edge. Assim, a regra de entrada pode ser restrita para abrirem apenas de entrada (entrada) a partir da rede interna. <li> Para o cliente para cenários de dispositivo (C2D).</ul><li>80 para HTTP não é suportado pelo IoT Edge.<li>Se protocolos diferentes do HTTP (por exemplo, AMQP ou MQTT) não podem ser configurados na empresa; as mensagens podem ser enviadas por WebSockets. Porta 443 será utilizada para a comunicação WebSocket nesse caso.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>O módulo do agente do Edge relata continuamente o ' arquivo de configuração vazio ' e nenhum módulo inicia no dispositivo

O dispositivo tem problemas ao iniciar os módulos definidos na implantação. Somente o edgeAgent está em execução, mas está continuamente relatando ' arquivo de configuração vazio... '.

**Causa raiz**

Por padrão, IoT Edge inicia os módulos em sua própria rede de contêiner isolado. O dispositivo pode estar tendo problemas com a resolução de nomes DNS nessa rede privada.

**Resolução**

**Opção 1: definir o servidor DNS em configurações do mecanismo de contêiner**

Especifique o servidor DNS para seu ambiente nas configurações do mecanismo de contêiner que serão aplicadas a todos os módulos de contêiner iniciados pelo mecanismo. Crie um arquivo chamado `daemon.json` especificando o servidor DNS a ser usado. Por exemplo:

```json
{
    "dns": ["1.1.1.1"]
}
```

O exemplo acima define o servidor DNS para um serviço DNS acessível publicamente. Se o dispositivo de borda não puder acessar esse IP de seu ambiente, substitua-o pelo endereço do servidor DNS que está acessível.

Coloque `daemon.json` no local certo para sua plataforma: 

| Plataforma | Localização |
| --------- | -------- |
| Linux | `/etc/docker` |
| Host do Windows com contêineres do Windows | `C:\ProgramData\iotedge-moby\config` |

Se o local já contiver `daemon.json` arquivo, adicione a chave **DNS** a ele e salve o arquivo.

*Reinicie o mecanismo de contêiner para que as atualizações entrem em vigor*

| Plataforma | Comando |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (PowerShell do administrador) | `Restart-Service iotedge-moby -Force` |

**Opção 2: definir o servidor DNS na implantação IoT Edge por módulo**

Você pode definir o servidor DNS para *criaroptions* de cada módulo na implantação do IOT Edge. Por exemplo:

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Certifique-se de definir isso para os módulos *edgeAgent* e *edgeHub* também.

## <a name="next-steps"></a>Passos seguintes

Encontrou um erro na plataforma do IoT Edge? [Submeter um problema](https://github.com/Azure/iotedge/issues) para que possamos continuar a melhorar. 

Se tiver mais perguntas, crie uma [pedido de suporte](https://portal.azure.com/#create/Microsoft.Support) para obter ajuda.
