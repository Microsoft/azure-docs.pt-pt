---
title: Erros comuns - Azure IoT Edge | Microsoft Docs
description: Utilize este artigo para resolver problemas comuns encontrados ao implementar uma solução IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: cc6d7491d9c38f1ddf4aba2adecad4aaee3c344b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103489568"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Problemas comuns e resoluções do Azure IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Utilize este artigo para encontrar medidas para resolver questões comuns que possa experimentar ao implementar soluções IoT Edge. Se precisar de aprender a encontrar registos e erros do seu dispositivo IoT Edge, consulte [Troubleshoot o seu dispositivo IoT Edge](troubleshoot.md).

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>Agente IoT Edge para após cerca de um minuto

**Comportamento observado:**

O módulo edgeAgent começa e funciona com sucesso durante cerca de um minuto e para. Os registos indicam que o agente IoT Edge tenta ligar-se ao IoT Hub sobre AMQP e, em seguida, tenta ligar-se usando AMQP através da WebSocket. Quando isso falhar, o agente IoT Edge sai.

Exemplo edgeAgent registos:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**Causa raiz:**

Uma configuração de rede na rede de anfitriões está a impedir que o agente IoT Edge chegue à rede. O agente primeiro tenta ligar através de AMQP (porta 5671). Se a ligação falhar, experimenta WebSockets (porta 443).

O runtime do IoT Edge configura uma rede para comunicar em cada um dos módulos. No Linux, esta rede é uma rede de ponte. No Windows, utiliza NAT. Este problema é mais comum nos dispositivos Windows com contentores do Windows que utilizam a rede NAT.

**Resolução:**

Certifique-se de que existe uma rota para a Internet para os endereços IP atribuídos a esta rede de ponte/NAT. Por vezes, uma configuração de VPN no anfitrião sobrepõe-se à rede do IoT Edge.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>O agente IoT Edge não consegue aceder à imagem de um módulo (403)

**Comportamento observado:**

Um contentor não funciona e os registos edgeAgent mostram um erro de 403.

**Causa raiz:**

O agente IoT Edge não tem permissões para aceder à imagem de um módulo.

**Resolução:**

Certifique-se de que as suas credenciais de registo estão corretamente especificadas no seu manifesto de implantação.

## <a name="edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Módulo Edge Agent relata "ficheiro config vazio" e nenhum módulo começa no dispositivo

**Comportamento observado:**

O dispositivo tem dificuldade em iniciar módulos definidos na implementação. Apenas o edgeAgent está em execução, mas continuamente relatando "ficheiro config vazio...".

**Causa raiz:**

Por predefinição, o IoT Edge inicia os módulos na sua própria rede de contentores isolados. O dispositivo pode estar a ter problemas com a resolução do nome DNS dentro desta rede privada.

**Resolução:**

**Opção 1: Definir o servidor DNS nas definições do motor do contentor**

Especifique o servidor DNS para o seu ambiente nas definições do motor do contentor, que se aplicará a todos os módulos de contentores iniciados pelo motor. Crie um ficheiro nomeado `daemon.json` especificando o servidor DNS para usar. Por exemplo:

```json
{
    "dns": ["1.1.1.1"]
}
```

O exemplo acima define o servidor DNS num serviço DNS acessível ao público. Se o dispositivo de borda não conseguir aceder a este IP a partir do seu ambiente, substitua-o por um endereço de servidor DNS que esteja acessível.

Local `daemon.json` no local certo para a sua plataforma:

| Plataforma | Localização |
| --------- | -------- |
| Linux | `/etc/docker` |
| Anfitrião de janelas com recipientes Windows | `C:\ProgramData\iotedge-moby\config` |

Se a localização já `daemon.json` contiver ficheiro, adicione a chave **DNS** ao mesmo e guarde o ficheiro.

Reinicie o motor do recipiente para que as atualizações produzam efeitos.

| Plataforma | Comando |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (Admin PowerShell) | `Restart-Service iotedge-moby -Force` |

**Opção 2: Definir o servidor DNS na implementação IoT Edge por módulo**

Pode configurar o servidor DNS para as *opções de criação de* cada módulo na implementação IoT Edge. Por exemplo:

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

## <a name="iot-edge-hub-fails-to-start"></a>IoT Edge hub falha em começar

**Comportamento observado:**

O módulo edgeHub não arranca. Pode ver uma mensagem como um dos seguintes erros nos registos:

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

**Causa raiz:**

Algum outro processo na máquina hospedeira tem uma porta que o módulo EdgeHub está a tentar ligar. O hub IoT Edge mapeia as portas 443, 5671 e 8883 para uso em cenários de gateway. O módulo não arranca se outro processo já tiver ligado uma dessas portas.

**Resolução:**

Pode resolver esta questão de duas formas:

Se o dispositivo IoT Edge estiver a funcionar como um dispositivo de gateway, então precisa de encontrar e parar o processo que está a utilizar a porta 443, 5671 ou 8883. Um erro para a porta 443 geralmente significa que o outro processo é um servidor web.

Se não precisar de utilizar o dispositivo IoT Edge como porta de entrada, então pode remover as ligações da porta do módulo edgeHub criar opções. Pode alterar as opções de criação no portal Azure ou diretamente no deployment.jsficheiro.

No portal do Azure:

1. Navegue até ao seu hub IoT e selecione **IoT Edge**.

2. Selecione o dispositivo IoT Edge que pretende atualizar.

3. Selecione **módulos de conjunto**.

4. Selecione **definições de tempo de execução**.

5. Nas definições do módulo **Edge Hub,** elimine tudo da caixa de texto **Create Options.**

6. Guarde as suas alterações e crie a implementação.

No deployment.jsarquivado:

1. Abra a deployment.jsno ficheiro que aplicaste ao seu dispositivo IoT Edge.

2. Encontre as `edgeHub` definições na secção de propriedades desejadas pela EdgeAgent:

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
           "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

3. Retire a `createOptions` linha, e a vírgula no final da `image` linha antes dela:

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.1"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

4. Guarde o ficheiro e aplique-o novamente no seu dispositivo IoT Edge.

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>Daemon de segurança IoT Edge falha com um nome de hospedeiro inválido

**Comportamento observado:**

A tentativa de [verificar os registos do gestor de segurança IoT Edge](troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs) falha e imprime a seguinte mensagem:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Causa raiz:**

O tempo de execução IoT Edge só pode suportar os anfitriões com menos de 64 caracteres. As máquinas físicas geralmente não têm nomes de hospedeiros longos, mas o problema é mais comum numa máquina virtual. Os hostnames gerados automaticamente para máquinas virtuais Windows hospedados em Azure, em particular, tendem a ser longos.

**Resolução:**

Quando vir este erro, pode resolvê-lo configurando o nome DNS da sua máquina virtual e, em seguida, definindo o nome DNS como o nome de anfitrião no comando de configuração.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. No portal Azure, navegue para a página geral da sua máquina virtual.
2. **Selecione configurar** em nome DNS. Se a sua máquina virtual já tiver um nome DNS configurado, não precisa de configurar um novo.

   ![Configurar o nome DNS da máquina virtual](./media/troubleshoot/configure-dns.png)

3. Forneça um valor para **a etiqueta de nome DNS** e selecione **Save**.
4. Copie o novo nome DNS, que deve estar no formato **\<DNSnamelabel\> . \<vmlocation\> . cloudapp.azure.com.**
5. Dentro da máquina virtual, utilize o seguinte comando para configurar o tempo de execução IoT Edge com o seu nome DNS:

   * Em Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * No Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. No portal Azure, navegue para a página geral da sua máquina virtual.

2. **Selecione configurar** em nome DNS. Se a sua máquina virtual já tiver um nome DNS configurado, não precisa de configurar um novo.

   ![Configurar o nome DNS da máquina virtual](./media/troubleshoot/configure-dns.png)

3. Forneça um valor para **a etiqueta de nome DNS** e selecione **Save**.

4. Copie o novo nome DNS, que deve estar no formato **\<DNSnamelabel\> . \<vmlocation\> . cloudapp.azure.com.**

5. No dispositivo IoT Edge, abra o ficheiro config.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

6. Substitua o valor do `hostname` seu nome DNS.

7. Guarde e feche o ficheiro e, em seguida, aplique as alterações no IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Não consigo obter os registos do daemon IoT Edge no Windows

**Comportamento observado:**

Obtém-se um EventLogException ao utilizar `Get-WinEvent` no Windows.

**Causa raiz:**

O `Get-WinEvent` comando PowerShell baseia-se numa entrada de registo para estar presente para encontrar registos por um específico `ProviderName` .

**Resolução:**

Desafete uma entrada de registo para o daemon IoT Edge. Crie um ficheiro **iotedge.reg** com o seguinte conteúdo e importe-o para o Registo do Windows clicando duas vezes nele ou utilizando o `reg import iotedge.reg` comando:

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="stability-issues-on-smaller-devices"></a>Problemas de estabilidade em dispositivos mais pequenos

**Comportamento observado:**

Pode ter problemas de estabilidade em dispositivos constrangidos de recursos como o Raspberry Pi, especialmente quando usado como porta de entrada. Os sintomas incluem exceções de memória no módulo hub IoT Edge, dispositivos a jusante que não conseguem ligar ou o dispositivo não envia mensagens de telemetria após algumas horas.

**Causa raiz:**

O hub IoT Edge, que faz parte do tempo de execução do IoT Edge, é otimizado para o desempenho por padrão e tenta alocar grandes pedaços de memória. Esta otimização não é ideal para dispositivos de borda restrita e pode causar problemas de estabilidade.

**Resolução:**

Para o hub IoT Edge, desaperte uma variável ambiental **OptimizeForPerformance** em **falso**. Existem duas formas de definir variáveis ambientais:

No portal do Azure:

No seu IoT Hub, selecione o seu dispositivo IoT Edge e a partir da página de detalhes do dispositivo e selecione   >  **Definições de tempo de execução de módulos de definição**. Crie uma variável ambiental para o módulo hub IoT Edge chamado *OptimizeForPerformance* que está definido como *falso*.

![OtimizarForPerformance definido para falso](./media/troubleshoot/optimizeforperformance-false.png)

No manifesto de implantação:

```json
"edgeHub": {
  "type": "docker",
  "settings": {
    "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
    "createOptions": <snipped>
  },
  "env": {
    "OptimizeForPerformance": {
      "value": "false"
    }
  },
```

## <a name="iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error"></a>Módulo IoT Edge falha em enviar uma mensagem para edgeHub com erro 404

**Comportamento observado:**

Um módulo IoT Edge personalizado não envia uma mensagem para o hub IoT Edge com um erro de 404. `Module not found` O daemon IoT Edge imprime a seguinte mensagem nos registos:

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**Causa raiz:**

O daemon IoT Edge impõe a identificação do processo para todos os módulos que se ligam ao edgeHub por razões de segurança. Verifica que todas as mensagens enviadas por um módulo provêm do ID de processo principal do módulo. Se uma mensagem estiver a ser enviada por um módulo de um ID de processo diferente do inicialmente estabelecido, rejeitará a mensagem com uma mensagem de erro 404.

**Resolução:**

A partir da versão 1.0.7, todos os processos do módulo estão autorizados a ligar. Para obter mais informações, consulte o [1.0.7 release changelog](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Se não for possível atualizar para 1.0.7, complete os seguintes passos. Certifique-se de que o mesmo ID de processo é sempre utilizado pelo módulo IoT Edge personalizado para enviar mensagens para o edgeHub. Por exemplo, certifique-se `ENTRYPOINT` de em vez de comandar no seu ficheiro `CMD` Docker. O `CMD` comando leva a um ID de processo para o módulo e outro ID de processo para o comando de bash que executa o programa principal, mas leva a um único `ENTRYPOINT` ID de processo.

## <a name="iot-edge-module-deploys-successfully-then-disappears-from-device"></a>Módulo IoT Edge implementa com sucesso e desaparece do dispositivo

**Comportamento observado:**

Depois de configurar os módulos para um dispositivo IoT Edge, os módulos são implantados com sucesso, mas passados alguns minutos desaparecem do dispositivo e dos detalhes do dispositivo no portal Azure. Outros módulos que não os definidos também podem aparecer no dispositivo.

**Causa raiz:**

Se uma implementação automática atingir um dispositivo, tem prioridade sobre a definição manual dos módulos para um único dispositivo. A funcionalidade **de conjunto de módulos** no portal Azure ou **criar implementação para** funcionalidade de dispositivo único no Código do Estúdio Visual entrará em vigor por um momento. Vê os módulos que definiu iniciar o dispositivo. Em seguida, a prioridade da implantação automática entra em vigor e substitui as propriedades desejadas do dispositivo.

**Resolução:**

Utilize apenas um tipo de mecanismo de implantação por dispositivo, seja uma implantação automática ou uma implantação individual do dispositivo. Se tiver várias implementações automáticas direcionadas para um dispositivo, pode alterar descrições prioritárias ou de destino para se certificar de que a correta se aplica a um determinado dispositivo. Também pode atualizar o dispositivo twin para deixar de corresponder à descrição do alvo da implementação automática.

Para obter mais informações, consulte [as configurações automáticas Understand IoT Edge para dispositivos individuais ou à escala.](module-deployment-monitoring.md)

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

## <a name="iot-edge-behind-a-gateway-cannot-perform-http-requests-and-start-edgeagent-module"></a>IoT Edge atrás de um gateway não pode executar pedidos HTTP e iniciar módulo edgeAgent

**Comportamento observado:**

O daemon IoT Edge está ativo com um ficheiro de configuração válido, mas não pode iniciar o módulo edgeAgent. O comando `iotedge list` devolve uma lista vazia. O relatório de registos de daemon ioT `Could not perform HTTP request` Edge.

**Causa raiz:**

Os dispositivos IoT Edge por trás de um gateway obtêm as suas imagens de módulo do dispositivo IoT Edge dos pais especificados no `parent_hostname` campo do ficheiro config. O `Could not perform HTTP request` erro significa que o dispositivo da criança não é capaz de chegar ao seu dispositivo principal através de HTTP.

**Resolução:**

Certifique-se de que o dispositivo IoT Edge dos pais pode receber pedidos de entrada do dispositivo IoT Edge da criança. Tráfego de rede aberto nas portas 443 e 6617 para pedidos provenientes do dispositivo infantil.

:::moniker-end

## <a name="next-steps"></a>Passos seguintes

Encontrou um erro na plataforma do IoT Edge? [Submeter um problema](https://github.com/Azure/iotedge/issues) para que possamos continuar a melhorar.

Se tiver mais perguntas, crie um pedido de ajuda [de Apoio.](https://portal.azure.com/#create/Microsoft.Support)
