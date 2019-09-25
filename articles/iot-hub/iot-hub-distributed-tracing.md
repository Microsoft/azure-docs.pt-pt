---
title: Adicionar IDs de correlação a mensagens de IoT com rastreamento distribuído (versão prévia)
description: ''
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: jlian
ms.openlocfilehash: e4403c245a3cae671f83260ae313ed400b0f7721
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259361"
---
# <a name="trace-azure-iot-device-to-cloud-messages-with-distributed-tracing-preview"></a>Rastrear mensagens do dispositivo para a nuvem do IoT do Azure com o rastreamento distribuído (versão prévia)

Microsoft Azure Hub IoT atualmente dá suporte ao rastreamento distribuído como um [recurso de visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Hub IoT é um dos primeiros serviços do Azure para dar suporte ao rastreamento distribuído. À medida que mais serviços do Azure dão suporte ao rastreamento distribuído, você poderá rastrear mensagens de IoT em todos os serviços do Azure envolvidos em sua solução. Para obter um plano de fundo sobre o rastreamento distribuído, consulte [rastreamento distribuído](../azure-monitor/app/distributed-tracing.md).

Habilitar o rastreamento distribuído para o Hub IoT oferece a capacidade de:

- Monitore precisamente o fluxo de cada mensagem por meio do Hub IoT usando o [contexto de rastreamento](https://github.com/w3c/trace-context). Esse contexto de rastreamento inclui IDs de correlação que permitem correlacionar eventos de um componente com eventos de outro componente. Ele pode ser aplicado a um subconjunto ou a todas as mensagens do dispositivo IoT usando o [dispositivo](iot-hub-devguide-device-twins.md).
- Registre automaticamente o contexto de rastreamento para [Azure monitor logs de diagnóstico](iot-hub-monitor-resource-health.md).
- Meça e entenda o fluxo de mensagens e a latência de dispositivos para o Hub IoT e pontos de extremidade de roteamento.
- Comece a considerar como você deseja implementar o rastreamento distribuído para os serviços que não são do Azure em sua solução de IoT.

Neste artigo, você usa o [SDK do dispositivo IOT do Azure para C](./iot-hub-device-sdk-c-intro.md) com o rastreamento distribuído. O suporte ao rastreamento distribuído ainda está em andamento para os outros SDKs.

## <a name="prerequisites"></a>Pré-requisitos

- Atualmente, a visualização do rastreamento distribuído só tem suporte para hubs IoT criados nas seguintes regiões:

  - **Europa Setentrional**
  - **Sudeste da Ásia**
  - **Oeste dos EUA 2**

- Este artigo pressupõe que você esteja familiarizado com o envio de mensagens de telemetria para o Hub IoT. Verifique se você concluiu o [início rápido enviar telemetria C](./quickstart-send-telemetry-c.md).

- Registre um dispositivo com o Hub IoT (etapas disponíveis em cada início rápido) e anote a cadeia de conexão.

- Instale a versão mais recente do [Git](https://git-scm.com/download/).

## <a name="configure-iot-hub"></a>Configurar o Hub IoT

Nesta seção, você configura um hub IoT para registrar em log os atributos de rastreamento distribuído (IDs de correlação e carimbos de data/hora).

1. Navegue até o Hub IoT no [portal do Azure](https://portal.azure.com/).

1. No painel esquerdo do Hub IoT, role para baixo até a seção **monitoramento** e clique em **configurações de diagnóstico**.

1. Se as configurações de diagnóstico ainda não estiverem ativadas, clique em **Ativar diagnóstico**. Se você já tiver habilitado as configurações de diagnóstico, clique em **Adicionar configuração de diagnóstico**.

1. No campo **nome** , insira um nome para uma nova configuração de diagnóstico. Por exemplo, **DistributedTracingSettings**.

1. Escolha uma ou mais das seguintes opções que determinam onde o log será enviado:

    - **Arquivar em uma conta de armazenamento**: Configure uma conta de armazenamento para conter as informações de log.
    - **Transmitir para um hub de eventos**: Configure um hub de eventos para conter as informações de log.
    - **Enviar para log Analytics**: Configure um espaço de trabalho do log Analytics para conter as informações de log.

1. Na seção **log** , selecione as operações para as quais você deseja registrar informações.

    Certifique-se de incluir **DistributedTracing**e configurar uma **retenção** de quantos dias você deseja que o log seja retido. A retenção de log afeta os custos de armazenamento.

    ![Captura de tela mostrando onde a categoria DistributedTracing é para configurações de diagnóstico de IoT](./media/iot-hub-distributed-tracing/diag-logs.png)

1. Clique em **salvar** para a nova configuração.

1. Adicional Para ver o fluxo de mensagens para locais diferentes, configure [as regras de roteamento para pelo menos dois pontos de extremidade diferentes](iot-hub-devguide-messages-d2c.md).

Depois que o registro em log for ativado, o Hub IoT registrará um log quando uma mensagem contendo propriedades de rastreamento válidas for encontrada em qualquer uma das seguintes situações:

- As mensagens chegam ao gateway do Hub IoT.
- A mensagem é processada pelo Hub IoT.
- A mensagem é roteada para pontos de extremidade personalizados. O roteamento deve estar habilitado.

Para saber mais sobre esses logs e seus esquemas, consulte [rastreamento distribuído em logs de diagnóstico do Hub IOT](iot-hub-monitor-resource-health.md#distributed-tracing-preview).

## <a name="set-up-device"></a>Configurar dispositivo

Nesta seção, você prepara um ambiente de desenvolvimento para uso com o [SDK do Azure IOT C](https://github.com/Azure/azure-iot-sdk-c). Em seguida, modifique um dos exemplos para habilitar o rastreamento distribuído nas mensagens de telemetria do dispositivo.

Estas instruções são para a criação do exemplo no Windows. Para outros ambientes, consulte [compilar o SDK do c ou o](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compile) [SDK do c empacotado para o desenvolvimento específico da plataforma](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#prepackaged-c-sdk-for-platform-specific-development).

### <a name="clone-the-source-code-and-initialize"></a>Clonar o código-fonte e inicializar

1. Instale a [carga de trabalho C++"desenvolvimento de desktops com"](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2017) para o Visual Studio 2015 ou 2017.

1. Instale o [CMake](https://cmake.org/). Verifique se ele está no seu `PATH` digitando `cmake -version` a partir de um prompt de comando.

1. Abra uma linha de comandos ou a shell do Git Bash. Execute o seguinte comando para clonar o [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c) no repositório do GitHub:

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    Esta operação deve demorar vários minutos a ser concluída.

1. Crie um subdiretório `cmake` no diretório de raiz do repositório git e navegue para essa pasta.

    ```cmd
    cd azure-iot-sdk-c    
    mkdir cmake
    cd cmake
    cmake ..
    ```

    Se `cmake` o não conseguir C++ localizar seu compilador, você poderá obter erros de compilação ao executar o comando acima. Se isto acontecer, tente executar o comando seguinte na [linha de comandos do Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Assim que a compilação for concluída com êxito, as últimas linhas de saída terão um aspeto semelhante ao seguinte:

    ```cmd
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

### <a name="edit-the-send-telemetry-sample-to-enable-distributed-tracing"></a>Editar o exemplo enviar telemetria para habilitar o rastreamento distribuído

1. Use um editor para abrir o `azure-iot-sdk-c/iothub_client/samples/iothub_ll_telemetry_sample/iothub_ll_telemetry_sample.c` arquivo de origem.

1. Procure a declaração da constante `connectionString`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=2)]

    Substitua o valor da `connectionString` constante pela cadeia de conexão do dispositivo anotada na seção [registrar um dispositivo](./quickstart-send-telemetry-c.md#register-a-device) do [início rápido enviar telemetria C](./quickstart-send-telemetry-c.md).

1. Altere a `MESSAGE_COUNT` definição de `5000`para:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=3)]

1. Localize a linha de código que chama `IoTHubDeviceClient_LL_SetConnectionStatusCallback` para registrar uma função de retorno de chamada de status de conexão antes do loop de mensagem de envio. Adicione o código sob essa linha, conforme mostrado abaixo `IoTHubDeviceClient_LL_EnablePolicyConfiguration` para chamar habilitando o rastreamento distribuído para o dispositivo:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_tracing&highlight=5)]

    A `IoTHubDeviceClient_LL_EnablePolicyConfiguration` função habilita políticas para recursos específicos do IoTHub configurados por meio do [dispositivo gêmeos](./iot-hub-devguide-device-twins.md). Uma `POLICY_CONFIGURATION_DISTRIBUTED_TRACING` vez habilitado com a linha de código acima, o comportamento de rastreamento do dispositivo refletirá as alterações de rastreamento distribuídas feitas no dispositivo.

1. Para manter o aplicativo de exemplo em execução sem usar toda a sua cota, adicione um atraso de um segundo no final do loop de envio de mensagem:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_sleep&highlight=8)]

### <a name="compile-and-run"></a>Compilar e executar

1. Navegue até o diretório do projeto *iothub_ll_telemetry_sample* do diretório CMake (`azure-iot-sdk-c/cmake`) criado anteriormente e compile o exemplo:

    ```cmd
    cd iothub_client/samples/iothub_ll_telemetry_sample
    cmake --build . --target iothub_ll_telemetry_sample --config Debug
    ```

1. Execute a aplicação. O dispositivo envia telemetria com suporte ao rastreamento distribuído.

    ```cmd
    Debug/iothub_ll_telemetry_sample.exe
    ```

1. Mantenha o aplicativo em execução. Como opção, observe a mensagem que está sendo enviada ao Hub IoT examinando a janela do console.

<!-- For a client app that can receive sampling decisions from the cloud, check out [this sample](https://aka.ms/iottracingCsample).  -->

### <a name="workaround-for-third-party-clients"></a>Solução alternativa para clientes de terceiros

Não é **trivial** Visualizar o recurso de rastreamento distribuído sem usar o SDK do C. Portanto, essa abordagem não é recomendada.

Primeiro, você deve implementar todos os primitivos de protocolo do Hub IoT em suas mensagens seguindo o guia de desenvolvimento [criar e ler mensagens do Hub IOT](iot-hub-devguide-messages-construct.md). Em seguida, edite as propriedades de protocolo nas mensagens MQTT/AMQP `tracestate` para adicionar como **Propriedade do sistema**. Mais concretamente:

* Para MQTT, adicione `%24.tracestate=timestamp%3d1539243209` ao tópico da mensagem, onde `1539243209` deve ser substituído pelo horário de criação da mensagem no formato de carimbo de data/hora do UNIX. Por exemplo, consulte a implementação [no SDK do C](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/iothubtransport_mqtt_common.c#L761)
* Para AMQP, adicione `key("tracestate")` e `value("timestamp=1539243209")` como anotação de mensagem. Para obter uma implementação de referência, consulte [aqui](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/uamqp_messaging.c#L527).

Para controlar a porcentagem de mensagens que contêm essa propriedade, implemente a lógica para escutar eventos iniciados na nuvem, como atualizações de atualização.

## <a name="update-sampling-options"></a>Atualizar opções de amostragem 

Para alterar a porcentagem de mensagens a serem rastreadas da nuvem, você deve atualizar o dispositivo. Você pode fazer isso de várias maneiras, incluindo o editor de JSON no portal e o SDK do serviço de Hub IoT. As subseções a seguir fornecem exemplos.

### <a name="update-using-the-portal"></a>Atualizar usando o portal

1. Navegue até o Hub IoT em [portal do Azure](https://portal.azure.com/)e clique em **dispositivos IOT**.

1. Clique em seu dispositivo.

1. Procure **habilitar o rastreamento distribuído (versão prévia)** e, em seguida, selecione **habilitar**.

    ![Habilitar o rastreamento distribuído no portal do Azure](./media/iot-hub-distributed-tracing/azure-portal.png)

1. Escolha uma **taxa de amostragem** entre 0% e 100%.

1. Clique em **Guardar**.

1. Aguarde alguns segundos e clique em **Atualizar**e, se for confirmado com êxito pelo dispositivo, um ícone de sincronização com uma marca de seleção será exibido.

1. Volte para a janela do console do aplicativo de mensagem de telemetria. Você verá as mensagens sendo enviadas com `tracestate` as propriedades do aplicativo.

    ![Estado de rastreamento](./media/iot-hub-distributed-tracing/MicrosoftTeams-image.png)

1. Adicional Altere a taxa de amostragem para um valor diferente e observe a alteração na frequência em que as `tracestate` mensagens incluem nas propriedades do aplicativo.

### <a name="update-using-azure-iot-hub-toolkit-for-vs-code"></a>Atualizar usando o kit de ferramentas do Hub IoT do Azure para VS Code

1. Instale o VS Code e instale a versão mais recente do kit de ferramentas do Hub IoT do Azure para VS Code [aqui](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

1. Abra VS Code e [Configure a cadeia de conexão do Hub IOT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit#user-content-prerequisites).

1. Expanda o dispositivo e procure **configuração de rastreamento distribuído (versão prévia)** . Sob ele, clique em **Atualizar configuração de rastreamento distribuído (versão prévia)** do subnó.

    ![Habilitar o rastreamento distribuído no kit de ferramentas do Hub IoT do Azure](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-1.png)

1. Na janela pop-up, selecione **habilitar**e pressione ENTER para confirmar 100 como taxa de amostragem.

    ![Atualizar modo de amostragem](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-2.png)

    ![Taxa de amostragem de atualização](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-3.png)

### <a name="bulk-update-for-multiple-devices"></a>Atualização em massa para vários dispositivos

Para atualizar a configuração de amostragem de rastreamento distribuído para vários dispositivos, use [configuração automática de dispositivo](iot-hub-auto-device-config.md). Certifique-se de seguir este esquema de entrelaçamento:

```json
{
    "properties": {
        "desired": {
            "azureiot*com^dtracing^1": {
                "sampling_mode": 1,
                "sampling_rate": 100
            }
        }
    }
}
```

| Nome do elemento | Requerido | Tipo | Descrição |
|-----------------|----------|---------|-----------------------------------------------------|
| `sampling_mode` | Sim | Integer | Atualmente, há suporte para dois valores de modo para ativar e desativar a amostragem. `1`é on e, `2` está desativado. |
| `sampling_rate` | Sim | Integer | Esse valor é uma porcentagem. Somente os valores `0` de `100` para (inclusivo) são permitidos.  |

## <a name="query-and-visualize"></a>Consulta e visualização

Para ver todos os rastreamentos registrados por um hub IoT, consulte o repositório de logs que você selecionou nas configurações de diagnóstico. Esta seção percorre algumas opções diferentes.

### <a name="query-using-log-analytics"></a>Consultar usando Log Analytics

Se você tiver configurado [log Analytics com logs de diagnóstico](../azure-monitor/platform/resource-logs-collect-storage.md), consulte procurando logs na `DistributedTracing` categoria. Por exemplo, essa consulta mostra todos os rastreamentos registrados em log:

```Kusto
// All distributed traces 
AzureDiagnostics 
| where Category == "DistributedTracing" 
| project TimeGenerated, Category, OperationName, Level, CorrelationId, DurationMs, properties_s 
| order by TimeGenerated asc  
```

Logs de exemplo, conforme mostrado por Log Analytics:

| TimeGenerated | OperationName | Category | Nível | CorrelationId | DurationMs | properties |
|--------------------------|---------------|--------------------|---------------|---------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-02-22T03:28:28.633Z | DiagnosticIoTHubD2C | DistributedTracing | Informativo | 00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01 |  | {"deviceId":"AZ3166","messageSize":"96","callerLocalTimeUtc":"2018-02-22T03:27:28.633Z","calleeLocalTimeUtc":"2018-02-22T03:27:28.687Z"} |
| 2018-02-22T03:28:38.633Z | DiagnosticIoTHubIngress | DistributedTracing | Informativo | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 20 | {"isRoutingEnabled":"false","parentSpanId":"0144d2590aacd909"} |
| 2018-02-22T03:28:48.633Z | DiagnosticIoTHubEgress | DistributedTracing | Informativo | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 23 | {"endpointType":"EventHub","endpointName":"myEventHub", "parentSpanId":"0144d2590aacd909"} |

Para entender os diferentes tipos de logs, consulte [logs de diagnóstico do Hub IOT do Azure](iot-hub-monitor-resource-health.md#distributed-tracing-preview).

### <a name="application-map"></a>Mapeamento de Aplicações

Para visualizar o fluxo de mensagens de IoT, configure o aplicativo de exemplo do mapa do aplicativo. O aplicativo de exemplo envia os logs de rastreamento distribuídos para o [mapa do aplicativo](../application-insights/app-insights-app-map.md) usando uma função do Azure e um hub de eventos.

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/e2e-diagnostic-provision-cli" target="_blank">Obter o exemplo no github</a>

Esta imagem abaixo mostra o rastreamento distribuído no mapa do aplicativo com três pontos de extremidade de roteamento:

![Rastreamento distribuído de IoT no mapa do aplicativo](./media/iot-hub-distributed-tracing/app-map.png)

## <a name="understand-azure-iot-distributed-tracing"></a>Entender o rastreamento distribuído do Azure IoT

### <a name="context"></a>Contexto

Muitas soluções de IoT, incluindo nossa própria [arquitetura de referência](https://aka.ms/iotrefarchitecture) (somente em inglês), geralmente seguem uma variante da arquitetura de [microserviço](https://docs.microsoft.com/azure/architecture/microservices/). À medida que uma solução de IoT cresce mais complexa, você acaba usando uma dúzia ou mais de microserviços. Esses microserviços podem ou não ser do Azure. Identificar onde as mensagens de IoT estão descartando ou diminuindo pode se tornar desafiador. Por exemplo, você tem uma solução de IoT que usa 5 serviços do Azure e 1500 dispositivos ativos diferentes. Cada dispositivo envia 10 mensagens do dispositivo para a nuvem/segundo (para um total de 15.000 mensagens/segundo), mas você percebe que seu aplicativo Web vê apenas 10.000 mensagens/segundo. Onde está o problema? Como você acha o culpado?

### <a name="distributed-tracing-pattern-in-microservice-architecture"></a>Padrão de rastreamento distribuído na arquitetura de microserviço

Para reconstruir o fluxo de uma mensagem de IoT entre diferentes serviços, cada serviço deve propagar uma *ID de correlação* que identifica exclusivamente a mensagem. Depois de coletados em um sistema centralizado, as IDs de correlação permitem ver o fluxo de mensagens. Esse método é chamado de [padrão de rastreamento distribuído](https://docs.microsoft.com/azure/architecture/microservices/logging-monitoring#distributed-tracing).

Para dar suporte à adoção mais ampla para o rastreamento distribuído, a Microsoft está contribuindo para a [proposta padrão do W3C para rastreamento distribuído](https://w3c.github.io/trace-context/).

### <a name="iot-hub-support"></a>Suporte ao Hub IoT

Uma vez habilitado, o suporte de rastreamento distribuído para o Hub IoT seguirá este fluxo:

1. Uma mensagem é gerada no dispositivo IoT.
1. O dispositivo IoT decide (com ajuda da nuvem) que essa mensagem deve ser atribuída com um contexto de rastreamento.
1. O SDK adiciona um `tracestate` à propriedade de aplicativo de mensagem, que contém o carimbo de data/hora de criação da mensagem.
1. O dispositivo IoT envia a mensagem para o Hub IoT.
1. A mensagem chega ao gateway do Hub IoT.
1. O Hub IOT procura o `tracestate` nas propriedades do aplicativo de mensagem e verifica se ele está no formato correto.
1. Nesse caso, o Hub IOT gera e registra `trace-id` o `span-id` e para Azure monitor logs de diagnóstico na `DiagnosticIoTHubD2C`categoria.
1. Depois que o processamento da mensagem for concluído, o Hub `span-id` IOT gerará outro e o registrará junto com `DiagnosticIoTHubIngress`o existente `trace-id` na categoria.
1. Se o roteamento estiver habilitado para a mensagem, o Hub IOT o gravará no ponto de extremidade personalizado `span-id` e registrará `trace-id` outro com o `DiagnosticIoTHubEgress`mesmo na categoria.
1. As etapas acima são repetidas para cada mensagem gerada.

## <a name="public-preview-limits-and-considerations"></a>Considerações e limites de visualização pública

- A proposta para o padrão de contexto de rastreamento W3C atualmente é um rascunho funcional.
- Atualmente, a única linguagem de desenvolvimento com suporte do SDK do cliente é C.
- A funcionalidade de computação de nuvem para dispositivo não está disponível para a [camada básica do Hub IOT](iot-hub-scaling.md#basic-and-standard-tiers). No entanto, o Hub IoT ainda fará logon no Azure Monitor se vir um cabeçalho de contexto de rastreamento composto corretamente.
- Para garantir uma operação eficiente, o Hub IoT imporá uma limitação na taxa de registro em log que pode ocorrer como parte do rastreamento distribuído.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre o padrão de rastreamento distribuído geral em microservices, consulte [padrão de arquitetura de microserviço: rastreamento distribuído](https://microservices.io/patterns/observability/distributed-tracing.html).
- Para definir a configuração para aplicar configurações de rastreamento distribuído a um grande número de dispositivos, consulte [configurar e monitorar dispositivos IOT em escala](iot-hub-auto-device-config.md).
- Para saber mais sobre Azure Monitor, confira [o que é Azure monitor?](../azure-monitor/overview.md).
