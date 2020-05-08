---
title: Erros comuns - Borda Azure IoT [ Microsoft Docs
description: Use este artigo para resolver questões comuns encontradas ao implementar uma solução IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: ed93d24bc06a6622a8ace2b0ab6b44582da001c0
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82783750"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Problemas comuns e resoluções do Azure IoT Edge

Use este artigo para encontrar medidas para resolver questões comuns que possa experimentar ao implementar soluções IoT Edge. Se precisar de aprender a encontrar registos e erros do seu dispositivo IoT Edge, consulte [troubleshoot no seu dispositivo IoT Edge](troubleshoot.md).

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>Agente ioT Edge para após cerca de um minuto

**Comportamento observado:**

O módulo EdgeAgent começa e funciona com sucesso durante cerca de um minuto e para. Os registos indicam que o agente IoT Edge tenta ligar-se ao IoT Hub através da AMQP e, em seguida, tenta ligar-se usando amQP através do WebSocket. Quando isso falhar, o agente IoT Edge sai.

Registos de edgeAgent de exemplo:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**Causa da raiz:**

Uma configuração de rede na rede de hospedeiros está a impedir que o agente IoT Edge chegue à rede. O agente primeiro tenta ligar através de AMQP (porta 5671). Se a ligação falhar, tenta WebSockets (porta 443).

O runtime do IoT Edge configura uma rede para comunicar em cada um dos módulos. No Linux, esta rede é uma rede de ponte. No Windows, utiliza NAT. Este problema é mais comum nos dispositivos Windows com contentores do Windows que utilizam a rede NAT.

**Resolução:**

Certifique-se de que existe uma rota para a Internet para os endereços IP atribuídos a esta rede de ponte/NAT. Por vezes, uma configuração de VPN no anfitrião sobrepõe-se à rede do IoT Edge.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>O agente IoT Edge não consegue aceder à imagem de um módulo (403)

**Comportamento observado:**

Um recipiente não é executado, e os registos edgeAgent mostram um erro de 403.

**Causa da raiz:**

O agente IoT Edge não tem permissões para aceder à imagem de um módulo.

**Resolução:**

Certifique-se de que as suas credenciais de registo estão corretamente especificadas no seu manifesto de implantação.

## <a name="edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Módulo Edge Agent relata 'ficheiro de config vazio' e nenhum módulo começa no dispositivo

**Comportamento observado:**

O dispositivo tem problemas de arranque de módulos definidos na implementação. Só o agente de borda está a funcionar, mas continuamente a reportar "ficheiro de config vazio...".

**Causa da raiz:**

Por padrão, o IoT Edge inicia módulos na sua própria rede de contentores isolados. O dispositivo pode estar a ter problemas com a resolução de nomes DNS dentro desta rede privada.

**Resolução:**

**Opção 1: Definir o servidor DNS nas definições do motor do recipiente**

Especifique o servidor DNS para o seu ambiente nas definições do motor do recipiente, que se aplicará a todos os módulos de contentores iniciados pelo motor. Crie um `daemon.json` ficheiro nomeado especificando o servidor DNS para usar. Por exemplo:

```json
{
    "dns": ["1.1.1.1"]
}
```

O exemplo acima define o servidor DNS para um serviço DNS acessível ao público. Se o dispositivo de borda não conseguir aceder a este IP a partir do seu ambiente, substitua-o por um endereço de servidor DNS acessível.

Coloque `daemon.json` no local certo para a sua plataforma:

| Plataforma | Localização |
| --------- | -------- |
| Linux | `/etc/docker` |
| Anfitrião de janelas com recipientes Windows | `C:\ProgramData\iotedge-moby\config` |

Se a localização `daemon.json` já contiver ficheiro, adicione a chave **dns** e guarde o ficheiro.

Reinicie o motor do contentor para que as atualizações entrem em vigor.

| Plataforma | Comando |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (Admin PowerShell) | `Restart-Service iotedge-moby -Force` |

**Opção 2: Definir servidor DNS na implementação IoT Edge por módulo**

Pode definir o servidor DNS para as *opções de criação* de cada módulo na implementação do IoT Edge. Por exemplo:

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Certifique-se de definir esta configuração para os módulos *edgeAgent* e *edgeHub* também.

## <a name="iot-edge-hub-fails-to-start"></a>Hub IoT Edge não arranca

**Comportamento observado:**

O módulo EdgeHub não arranca. Pode ver uma mensagem como um dos seguintes erros nos registos:

```output
One or more errors occurred.
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80):
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

Ou

```output
info: edgelet_docker::runtime -- Starting module edgeHub...
warn: edgelet_utils::logging -- Could not start module edgeHub
warn: edgelet_utils::logging --     caused by: failed to create endpoint edgeHub on network nat: hnsCall failed in Win32:  
        The process cannot access the file because it is being used by another process. (0x20)
```

**Causa da raiz:**

Algum outro processo na máquina hospedeira amarrou uma porta que o módulo EdgeHub está a tentar ligar. O hub IoT Edge mapeia portas 443, 5671 e 8883 para uso em cenários de gateway. O módulo não arranca se outro processo já tiver ligado uma dessas portas.

**Resolução:**

Pode resolver esta questão de duas formas:

Se o dispositivo IoT Edge estiver a funcionar como um dispositivo de gateway, então precisa de encontrar e parar o processo que está a utilizar a porta 443, 5671 ou 8883. Um erro para a porta 443 geralmente significa que o outro processo é um servidor web.

Se não precisar de utilizar o dispositivo IoT Edge como porta de entrada, então pode remover as ligações da porta do módulo edgeHub criar opções. Pode alterar as opções de criação no portal Azure ou diretamente no ficheiro deployment.json.

No portal do Azure:

1. Navegue para o seu hub IoT e selecione **IoT Edge**.

2. Selecione o dispositivo IoT Edge que pretende atualizar.

3. Selecione **Módulos de Conjunto**.

4. Selecione Definições de tempo de **execução**.

5. Nas definições do módulo **Edge Hub,** elimine tudo da caixa de texto **Create Options.**

6. Guarde as suas alterações e crie a implantação.

No ficheiro deployment.json:

1. Abra o ficheiro deployment.json que aplicou ao seu dispositivo IoT Edge.

2. Encontre `edgeHub` as definições na secção de propriedades desejadas do EdgeAgent:

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
           "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

3. Retire `createOptions` a linha e a vírem de `image` rasto no final da linha antes:

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.0"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

4. Guarde o ficheiro e aplique-o novamente no seu dispositivo IoT Edge.

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>Daemon de segurança IoT Edge falha com um nome de anfitrião inválido

**Comportamento observado:**

Tentar verificar se os registos do gestor de [segurança IoT Edge](troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs) falham e imprimem a seguinte mensagem:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Causa da raiz:**

O tempo de execução do IoT Edge só pode suportar nomes de anfitriões que sejam inferiores a 64 caracteres. As máquinas físicas geralmente não têm nomes de anfitriões longos, mas o problema é mais comum numa máquina virtual. Os nomes de anfitriões gerados automaticamente para máquinas virtuais Windows alojadas no Azure, em particular, tendem a ser longos.

**Resolução:**

Quando vir este erro, pode resolvê-lo configurando o nome DNS da sua máquina virtual e, em seguida, definindo o nome DNS como nome de anfitrião no comando de configuração.

1. No portal Azure, navegue para a página geral da sua máquina virtual.
2. **Selecione configurar** sob o nome DNS. Se a sua máquina virtual já tiver um nome DNS configurado, não precisa de configurar um novo.

   ![Configurar o nome DNS da máquina virtual](./media/troubleshoot/configure-dns.png)

3. Forneça um valor para a **etiqueta de nome DNS** e selecione **Guardar**.
4. Copie o novo nome DNS, que deve estar no formato ** \<\>DNSnamelabel\< . vmlocation\>.cloudapp.azure.com**.
5. No interior da máquina virtual, utilize o seguinte comando para configurar o tempo de execução do IoT Edge com o seu nome DNS:

   * Em Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * No Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Não consigo obter os registos de daemon IoT Edge no Windows

**Comportamento observado:**

Obtém uma Exceção `Get-WinEvent` do EventLogQuando utilização no Windows.

**Causa da raiz:**

O `Get-WinEvent` comando PowerShell baseia-se numa entrada de registo para `ProviderName`estar presente para encontrar registos por um específico .

**Resolução:**

Estabeleça uma entrada de registo para o daemon IoT Edge. Crie um ficheiro **iotedge.reg** com o seguinte conteúdo e importe para o `reg import iotedge.reg` Registo do Windows clicando-o duas vezes ou utilizando o comando:

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="stability-issues-on-smaller-devices"></a>Problemas de estabilidade em dispositivos mais pequenos

**Comportamento observado:**

Pode experimentar problemas de estabilidade em dispositivos limitados de recursos como o Raspberry Pi, especialmente quando usado como porta de entrada. Os sintomas incluem exceções de memória no módulo hub IoT Edge, dispositivos a jusante que não se conectam, ou o dispositivo não envia mensagens de telemetria após algumas horas.

**Causa da raiz:**

O hub IoT Edge, que faz parte do tempo de execução do IoT Edge, está otimizado para o desempenho por padrão e tenta alocar grandes pedaços de memória. Esta otimização não é ideal para dispositivos de borda restrita e pode causar problemas de estabilidade.

**Resolução:**

Para o hub IoT Edge, detete uma variável ambiental **OptimizeForPerformance** para **falso**. Existem duas formas de definir variáveis ambientais:

No portal do Azure:

No seu Hub IoT, selecione o seu dispositivo IoT Edge e a partir da página de detalhes do dispositivo e selecione**Definições**de tempo de execução de **módulos definidos** > . Crie uma variável ambiental para o módulo hub IoT Edge chamado *OptimizeForPerformance* que está definido para *falso*.

![OptimizeForPerformance definido para falso](./media/troubleshoot/optimizeforperformance-false.png)

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

## <a name="iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error"></a>Módulo IoT Edge falha em enviar uma mensagem para edgeHub com erro de 404

**Comportamento observado:**

Um módulo IoT Edge personalizado não envia uma mensagem para o hub `Module not found` IoT Edge com um erro de 404. O daemon IoT Edge imprime a seguinte mensagem aos registos:

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**Causa da raiz:**

O daemon IoT Edge impõe a identificação do processo para todos os módulos que se ligam ao edgeHub por razões de segurança. Verifica que todas as mensagens enviadas por um módulo provêm do id de processo principal do módulo. Se uma mensagem estiver a ser enviada por um módulo a partir de um ID de processo diferente do inicialmente estabelecido, rejeitará a mensagem com uma mensagem de erro 404.

**Resolução:**

A partir da versão 1.0.7, todos os processos de módulo estão autorizados a ligar. Para mais informações, consulte o [changelog de lançamento 1.0.7](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Se não for possível atualizar para 1.0.7, complete os seguintes passos. Certifique-se de que o mesmo id de processo é sempre utilizado pelo módulo IoT Edge personalizado para enviar mensagens para o edgeHub. Por exemplo, `ENTRYPOINT` certifique-se `CMD` de que em vez de comando no seu ficheiro Docker. O `CMD` comando leva a um processo de IDENTIFICAção para o módulo e `ENTRYPOINT` outro id de processo para o comando de batida que executa o programa principal, mas leva a um único processo DE IDENTIFICAÇÃO.

## <a name="iot-edge-module-deploys-successfully-then-disappears-from-device"></a>Módulo IoT Edge implementa com sucesso e desaparece do dispositivo

**Comportamento observado:**

Depois de definir módulos para um dispositivo IoT Edge, os módulos são implantados com sucesso, mas após alguns minutos desaparecem do dispositivo e dos detalhes do dispositivo no portal Azure. Outros módulos que não os definidos também podem aparecer no dispositivo.

**Causa da raiz:**

Se uma implementação automática atingir um dispositivo, tem prioridade sobre a definição manual dos módulos para um único dispositivo. A funcionalidade **de módulos set** no portal Azure ou **criar implementação para uma única** funcionalidade de dispositivo no Código de Estúdio Visual entrará em vigor por um momento. Vê os módulos que definiu no dispositivo. Em seguida, a prioridade da implementação automática entra em vigor e substitui as propriedades desejadas pelo dispositivo.

**Resolução:**

Utilize apenas um tipo de mecanismo de implantação por dispositivo, seja uma implementação automática ou uma implementação individual do dispositivo. Se tiver várias implementações automáticas direcionadas a um dispositivo, pode alterar descrições prioritárias ou de destino para se certificar de que a correta se aplica a um determinado dispositivo. Também pode atualizar o dispositivo gémeo para deixar de corresponder à descrição do alvo da implementação automática.

Para obter mais informações, consulte [as implementações automáticas De IoT Edge para dispositivos individuais ou em escala](module-deployment-monitoring.md).

## <a name="next-steps"></a>Passos seguintes

Encontrou um erro na plataforma do IoT Edge? [Submeta um problema](https://github.com/Azure/iotedge/issues) para que possamos continuar a melhorar.

Se tiver mais perguntas, crie um pedido de [apoio](https://portal.azure.com/#create/Microsoft.Support) para ajuda.
