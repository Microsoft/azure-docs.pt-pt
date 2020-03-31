---
title: Adicione IDs de correlação a mensagens IoT c/rastreio distribuído (pré)
description: Aprenda a utilizar a capacidade de rastreio distribuída para rastrear mensagens IoT em todos os serviços Azure utilizados pela sua solução.
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: jlian
ms.openlocfilehash: efee34ddfb2b2f6090d5dc8c43647c7ee1c53ce2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562433"
---
# <a name="trace-azure-iot-device-to-cloud-messages-with-distributed-tracing-preview"></a>Trace Azure IoT mensagens dispositivo-to-cloud com rastreio distribuído (pré-visualização)

O Microsoft Azure IoT Hub suporta atualmente o rastreio distribuído como recurso de [pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O IoT Hub é um dos primeiros serviços Azure a apoiar o rastreio distribuído. À medida que mais serviços Azure suportam rastreios distribuídos, poderá rastrear mensagens IoT em todos os serviços Azure envolvidos na sua solução. Para obter um fundo sobre rastreio distribuído, consulte [Rastreio Distribuído](../azure-monitor/app/distributed-tracing.md).

Permitir o rastreio distribuído para o Hub IoT dá-lhe a capacidade de:

- Monitorize precisamente o fluxo de cada mensagem através do IoT Hub utilizando o [contexto do traço](https://github.com/w3c/trace-context). Este contexto de rastreio inclui IDs de correlação que lhe permitem correlacionar eventos de um componente com eventos de outro componente. Pode ser aplicado para um subconjunto ou todas as mensagens do dispositivo IoT utilizando [o dispositivo twin](iot-hub-devguide-device-twins.md).
- Inicie automaticamente o contexto do rastreio para [os registos de diagnóstico](iot-hub-monitor-resource-health.md)do Monitor Azure .
- Medir e compreender o fluxo de mensagens e a latência dos dispositivos para o Hub IoT e os pontos finais de encaminhamento.
- Comece a considerar como pretende implementar rastreios distribuídos para os serviços não Azure na sua solução IoT.

Neste artigo, utiliza o [dispositivo Azure IoT SDK para C](iot-hub-device-sdk-c-intro.md) com rastreio distribuído. O suporte de rastreio distribuído ainda está em curso para os outros SDKs.

## <a name="prerequisites"></a>Pré-requisitos

- A visualização do rastreio distribuído é atualmente suportada apenas para os Hubs IoT criados nas seguintes regiões:

  - **Europa do Norte**
  - **Ásia Sudeste**
  - **E.U.A.Oeste 2**

- Este artigo assume que está familiarizado com o envio de mensagens de telemetria para o seu centro ioT. Certifique-se de que completou a [telemetria Enviar C Quickstart](quickstart-send-telemetry-c.md).

- Registe um dispositivo com o seu hub IoT (passos disponíveis em cada Quickstart) e anote a cadeia de ligação.

- Instale a versão mais recente do [Git](https://git-scm.com/download/).

## <a name="configure-iot-hub"></a>Configure IoT Hub

Nesta secção, configura um Hub IoT para registar atributos de rastreio distribuídos (IDs de correlação e carimbos de tempo).

1. Navegue até ao seu hub IoT no [portal Azure.](https://portal.azure.com/)

1. No painel esquerdo para o seu hub IoT, desloque-se até à secção **de Monitorização** e clique nas **definições de Diagnóstico**.

1. Se as definições de diagnóstico ainda não estiverem ligadas, clique **em ligar os diagnósticos**. Se já ativou as definições de diagnóstico, clique em **Adicionar definição de diagnóstico**.

1. No campo **Nome,** introduza um nome para uma nova definição de diagnóstico. Por exemplo, **DistributedTracingSettings**.

1. Escolha uma ou mais das seguintes opções que determinam para onde será enviada a exploração madeireira:

    - **Arquivar numa conta**de armazenamento: Configure uma conta de armazenamento para conter as informações de registo.
    - **Stream para um centro de eventos**: Configure um centro de eventos para conter as informações de registo.
    - **Enviar para Log Analytics**: Configure um espaço de trabalho de análise de registo para conter as informações de registo.

1. Na secção **Log,** selecione as operações para as quais pretende registar informações.

    Certifique-se de incluir **DistributedTracing**e configurar uma **Retenção** durante quantos dias pretende que a exploração madeireira seja mantida. A retenção de registos afeta os custos de armazenamento.

    ![Screenshot mostrando onde a categoria DistributedTracing é para configurações de diagnóstico IoT](./media/iot-hub-distributed-tracing/diag-logs.png)

1. Clique em **Guardar** para a nova definição.

1. (Opcional) Para ver as mensagens fluirem para diferentes lugares, estabeleça regras de [encaminhamento para pelo menos dois pontos finais diferentes](iot-hub-devguide-messages-d2c.md).

Uma vez ligada a exploração madeireira, o IoT Hub regista um registo quando uma mensagem contendo propriedades de vestígios válidas é encontrada em qualquer uma das seguintes situações:

- As mensagens chegam à porta de entrada do IoT Hub.
- A mensagem é processada pelo IoT Hub.
- A mensagem é encaminhada para pontos finais personalizados. O encaminhamento deve ser ativado.

Para saber mais sobre estes troncos e seus schemas, consulte rastreio [distribuído em registos de diagnóstico Do IoT Hub](iot-hub-monitor-resource-health.md#distributed-tracing-preview).

## <a name="set-up-device"></a>Configurar dispositivo

Nesta secção, prepara-se um ambiente de desenvolvimento para utilização com o [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). Em seguida, modifica uma das amostras para permitir o rastreio distribuído nas mensagens de telemetria do seu dispositivo.

Estas instruções são para construir a amostra no Windows. Para outros ambientes, consulte [A compilação do C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compile) ou do [SDK C pré-embalado para desenvolvimento específico](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#prepackaged-c-sdk-for-platform-specific-development)da plataforma .

### <a name="clone-the-source-code-and-initialize"></a>Clonar o código fonte e inicializar

1. Instale [a carga de trabalho "Desktop com C++"](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019) para o Estúdio Visual 2019. O Visual Studio 2017 e 2015 também são apoiados.

1. Instalar [cmake](https://cmake.org/). Certifique-se de `PATH` que `cmake -version` está no seu, digitando a partir de um pedido de comando.

1. Abra uma linha de comandos ou a shell do Git Bash. Executar os seguintes comandos para clonar o mais recente lançamento do [repositório Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub:

    ```cmd
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Esta operação deve demorar vários minutos a ser concluída.

1. Crie um subdiretório `cmake` no diretório de raiz do repositório git e navegue para essa pasta. Executar os seguintes `azure-iot-sdk-c` comandos do diretório:

    ```cmd
    mkdir cmake
    cd cmake
    cmake ..
    ```

    Se `cmake` não conseguir encontrar o seu compilador C++, poderá obter erros de construção enquanto executa o comando acima. Se isto acontecer, tente executar o comando seguinte na [linha de comandos do Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

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

### <a name="edit-the-send-telemetry-sample-to-enable-distributed-tracing"></a>Editar a amostra de telemetria de envio para permitir rastreios distribuídos

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/azure-iot-distributed-tracing-sample/blob/master/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c" target="_blank">Obtenha a amostra no GitHub</a>

1. Use um editor `azure-iot-sdk-c/iothub_client/samples/iothub_ll_telemetry_sample/iothub_ll_telemetry_sample.c` para abrir o ficheiro de origem.

1. Procure a declaração da constante `connectionString`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=2)]

    Substitua o `connectionString` valor da constante pela cadeia de ligação do dispositivo que fez uma nota no [registo de uma](./quickstart-send-telemetry-c.md#register-a-device) secção de dispositivo da [telemetria Enviar C Quickstart](./quickstart-send-telemetry-c.md).

1. Alterar `MESSAGE_COUNT` o `5000`definição para:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=3)]

1. Encontre a linha de `IoTHubDeviceClient_LL_SetConnectionStatusCallback` código que liga para registar uma função de chamada de estado de ligação antes do ciclo de envio da mensagem. Adicione código sob essa linha, `IoTHubDeviceClient_LL_EnablePolicyConfiguration` como mostrado abaixo para ligar permitindo o rastreio distribuído para o dispositivo:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_tracing&highlight=5)]

    A `IoTHubDeviceClient_LL_EnablePolicyConfiguration` função permite políticas para funcionalidades específicas do IoTHub que são configuradas através de [gémeos dispositivos](./iot-hub-devguide-device-twins.md). Uma `POLICY_CONFIGURATION_DISTRIBUTED_TRACING` vez ativado com a linha de código acima, o comportamento de rastreio do dispositivo refletirá alterações de rastreio distribuídas feitas no dispositivo twin.

1. Para manter a aplicação de amostra sem utilizar toda a sua quota, adicione um atraso de um segundo no final do ciclo de mensagens de envio:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_sleep&highlight=8)]

### <a name="compile-and-run"></a>Compilar e correr

1. Navegue para o diretório *de projetos iothub_ll_telemetry_sample* do diretório CMake`azure-iot-sdk-c/cmake`() que criou anteriormente, e compile a amostra:

    ```cmd
    cd iothub_client/samples/iothub_ll_telemetry_sample
    cmake --build . --target iothub_ll_telemetry_sample --config Debug
    ```

1. Execute a aplicação. O dispositivo envia telemetria suportando rastreios distribuídos.

    ```cmd
    Debug/iothub_ll_telemetry_sample.exe
    ```

1. Mantenha a aplicação em funcionamento. Observe opcionalmente a mensagem que está a ser enviada para o IoT Hub olhando para a janela da consola.

<!-- For a client app that can receive sampling decisions from the cloud, check out [this sample](https://aka.ms/iottracingCsample).  -->

### <a name="workaround-for-third-party-clients"></a>Seleção para clientes de terceiros

Não é **trivial** visualizar a funcionalidade de rastreio distribuída sem utilizar o C SDK. Assim, esta abordagem não é recomendada.

Em primeiro lugar, deve implementar todos os primitivos do protocolo IoT Hub nas suas mensagens seguindo o guia dev [Criar e ler mensagens IoT Hub](iot-hub-devguide-messages-construct.md). Em seguida, editar as propriedades do protocolo nas mensagens `tracestate` MQTT/AMQP para adicionar como **propriedade do sistema**. Mais concretamente:

* Para o MQTT, adicione `%24.tracestate=timestamp%3d1539243209` ao `1539243209` tópico da mensagem, onde deve ser substituído pelo tempo de criação da mensagem no formato unix timestamp. Como exemplo, consulte a implementação [no C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/iothubtransport_mqtt_common.c#L761)
* Para AMQP, `key("tracestate")` `value("timestamp=1539243209")` adicione e como anotação de mensagem. Para uma implementação de referência, consulte [aqui](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/uamqp_messaging.c#L527).

Para controlar a percentagem de mensagens que contêm esta propriedade, implemente a lógica para ouvir eventos iniciados na nuvem, como atualizações gémeas.

## <a name="update-sampling-options"></a>Atualizar opções de amostragem 

Para alterar a percentagem de mensagens a rastrear a partir da nuvem, tem de atualizar o dispositivo twin. Você pode realizar isso de várias maneiras, incluindo o editor JSON no portal e o serviço IoT Hub SDK. As seguintes subsecções fornecem exemplos.

### <a name="update-using-the-portal"></a>Atualizar usando o portal

1. Navegue para o seu hub IoT no [portal Azure](https://portal.azure.com/)e, em seguida, clique em **dispositivos IoT**.

1. Clique no seu dispositivo.

1. Procure **ativar o rastreio distribuído (pré-visualização)** e, em seguida, selecione **Ativar**.

    ![Ativar rastreio distribuído no portal Azure](./media/iot-hub-distributed-tracing/azure-portal.png)

1. Escolha uma **taxa de amostragem** entre 0% e 100%.

1. Clique em **Guardar**.

1. Aguarde alguns segundos e acerte **em Refresh**, então se for reconhecido com sucesso pelo dispositivo, aparece um ícone de sincronização com uma marca de verificação.

1. Volte para a janela da consola para a aplicação de mensagens de telemetria. Verá mensagens enviadas `tracestate` nas propriedades da aplicação.

    ![Estado de traço](./media/iot-hub-distributed-tracing/MicrosoftTeams-image.png)

1. (Opcional) Mude a taxa de amostragem para um valor diferente `tracestate` e observe a alteração da frequência que as mensagens incluem nas propriedades da aplicação.

### <a name="update-using-azure-iot-hub-for-vs-code"></a>Atualização usando Hub Azure IoT para código VS

1. Instale o Código VS e, em seguida, instale a versão mais recente do Hub Azure IoT para código VS a partir [daqui](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

1. Abra o Código VS e instale a cadeia de [ligação IoT Hub](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit#user-content-prerequisites).

1. Expanda o dispositivo e procure **definição de rastreio distribuído (pré-visualização)**. Por baixo, clique em **Atualizar Definição de Rastreio Distribuído (Pré-visualização)** do subnótono.

    ![Ativar rastreio distribuído na extensão Do Hub Azure IoT](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-1.png)

1. Na janela popup, selecione **'Activar'** e, em seguida, prima Entrar para confirmar 100 como taxa de amostragem.

    ![Atualizar modo de amostragem](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-2.png)

    ![Taxa de amostragem atualizada](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-3.png)

### <a name="bulk-update-for-multiple-devices"></a>Atualização a granel para vários dispositivos

Para atualizar a configuração de amostragem de rastreio distribuída para vários dispositivos, utilize a [configuração automática do dispositivo](iot-hub-auto-device-config.md). Certifique-se de seguir este esquema gémeo:

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

| Nome do elemento | Necessário | Tipo | Descrição |
|-----------------|----------|---------|-----------------------------------------------------|
| `sampling_mode` | Sim | Número inteiro | Atualmente, são suportados dois valores de modo para ligar e desligar a amostragem. `1`está ligado `2` e. |
| `sampling_rate` | Sim | Número inteiro | Este valor é uma percentagem. Só são `0` `100` permitidos valores de (inclusivos).  |

## <a name="query-and-visualize"></a>Consulta e visualização

Para ver todos os vestígios registados por um IoT Hub, consulte a loja de registos que selecionou em definições de diagnóstico. Esta secção passa por algumas opções diferentes.

### <a name="query-using-log-analytics"></a>Consulta usando Log Analytics

Se tiver configurado [o Log Analytics com registos](../azure-monitor/platform/resource-logs-collect-storage.md)de diagnóstico, `DistributedTracing` faça consulta procurando registos na categoria. Por exemplo, esta consulta mostra todos os vestígios registados:

```Kusto
// All distributed traces 
AzureDiagnostics 
| where Category == "DistributedTracing" 
| project TimeGenerated, Category, OperationName, Level, CorrelationId, DurationMs, properties_s 
| order by TimeGenerated asc  
```

Registos de exemplo como mostrado pelo Log Analytics:

| TimeGenerated | OperationName | Categoria | Nível | CorrelationId | DuraçãoMs | Propriedades |
|--------------------------|---------------|--------------------|---------------|---------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-02-22t03:28:28.633Z | DiagnósticoIoTHubD2C | DistributedTracing | Informativo | 00-8cd869a412459a25f5f4f311223344-0144d2590aacd909-01 |  | {"deviceId":"AZ3166","messageSize":"96","callerLocalTimeUtc":"2018-02-22T03:27:28.633Z","calleeLocalTimeUtc":"2018-02-22T03:27:28.687Z"} |
| 2018-02-22t03:28:38.633Z | DiagnosticIoTHubIngress | DistributedTracing | Informativo | 00-8cd869a412459a25f5f4f311223344-349810a9bbd28730-01 | 20 | {"isRoutingEnabled":"falso", "parentSpanId":"0144d2590aacd909"} |
| 2018-02-22t03:28:48.633Z | DiagnósticoIoTHubEgress | DistributedTracing | Informativo | 00-8cd869a412459a25f5f4f311223344-349810a9bbd28730-01 | 23 | {"endpointType":"EventHub","endpointName":"myEventHub", "parentSpanId":"0144d2590aacd909"} |

Para compreender os diferentes tipos de registos, consulte os [registos de diagnóstico do Azure IoT Hub](iot-hub-monitor-resource-health.md#distributed-tracing-preview).

### <a name="application-map"></a>Mapeamento de Aplicações

Para visualizar o fluxo de mensagens IoT, configurar a aplicação de amostra do Mapa de Aplicações. A aplicação de amostra envia os registos de rastreio distribuídos para o Mapa de [Aplicações](../application-insights/app-insights-app-map.md) utilizando uma Função Azure e um Hub de Eventos.

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/e2e-diagnostic-provision-cli" target="_blank">Obtenha a amostra no GitHub</a>

Esta imagem abaixo mostra rastreio distribuído no Mapa de Aplicações com três pontos finais de encaminhamento:

![IoT distribuído rastreio no Mapa de Aplicações](./media/iot-hub-distributed-tracing/app-map.png)

## <a name="understand-azure-iot-distributed-tracing"></a>Compreender azure IoT distribuído rastreio

### <a name="context"></a>Contexto

Muitas soluções IoT, incluindo a nossa própria [arquitetura de referência](https://aka.ms/iotrefarchitecture) (apenas em inglês), geralmente seguem uma variante da arquitetura de [microserviços.](https://docs.microsoft.com/azure/architecture/microservices/) À medida que uma solução IoT se torna mais complexa, acaba-se por usar uma dúzia ou mais de microserviços. Estes microserviços podem ou não ser de Azure. Identificar onde as mensagens IoT estão a cair ou a abrandar pode tornar-se um desafio. Por exemplo, tem uma solução IoT que utiliza 5 diferentes serviços Azure e 1500 dispositivos ativos. Cada dispositivo envia 10 mensagens/segundo sem dispositivo/segundo (num total de 15.000 mensagens/segundo), mas nota que a sua aplicação web vê apenas 10.000 mensagens/segundo. Onde está o problema? Como é que encontras o culpado?

### <a name="distributed-tracing-pattern-in-microservice-architecture"></a>Padrão de rastreio distribuído na arquitetura de microserviços

Para reconstruir o fluxo de uma mensagem IoT em diferentes serviços, cada serviço deve propagar um ID de *correlação* que identifique exclusivamente a mensagem. Uma vez recolhidos num sistema centralizado, os IDs de correlação permitem-lhe ver o fluxo de mensagens. Este método é chamado de padrão de [rastreio distribuído](https://docs.microsoft.com/azure/architecture/microservices/logging-monitoring#distributed-tracing).

Para apoiar uma adoção mais ampla para rastreiodistribuído, a Microsoft está a contribuir para a [proposta padrão W3C para rastreio distribuído](https://w3c.github.io/trace-context/).

### <a name="iot-hub-support"></a>Suporte do Hub IoT

Uma vez ativado, o suporte de rastreio distribuído para o IoT Hub seguirá este fluxo:

1. Uma mensagem é gerada no dispositivo IoT.
1. O dispositivo IoT decide (com a ajuda da nuvem) que esta mensagem deve ser atribuída com um contexto de traço.
1. O SDK `tracestate` adiciona uma propriedade de aplicação de mensagens, contendo o carimbo de tempo de criação de mensagens.
1. O dispositivo IoT envia a mensagem para o IoT Hub.
1. A mensagem chega ao portal ioT hub.
1. O IoT Hub `tracestate` procura as propriedades da aplicação de mensagens e verifica se está no formato correto.
1. Em caso afirmativo, o IoT Hub `trace-id` gera um `span-id` globalmente único para a mensagem, a para o "hop", e regista-os nos registos de diagnóstico do Monitor Azure no âmbito da operação `DiagnosticIoTHubD2C`.
1. Uma vez terminado o processamento de mensagens, o IoT Hub gera outro `span-id` e regista-o juntamente com o existente `trace-id` no âmbito da operação `DiagnosticIoTHubIngress`.
1. Se o encaminhamento estiver ativado para a mensagem, o IoT Hub `span-id` escreve-a para o ponto final personalizado e regista outra com o mesmo `trace-id` na categoria `DiagnosticIoTHubEgress`.
1. Os passos acima são repetidos para cada mensagem gerada.

## <a name="public-preview-limits-and-considerations"></a>Limites e considerações de pré-visualização pública

- A proposta de norma W3C Trace Context é atualmente um projeto de trabalho.
- Atualmente, a única linguagem de desenvolvimento suportada pelo cliente SDK é C.
- A capacidade de dupla cloud-to-device não está disponível para o [nível básico ioT Hub](iot-hub-scaling.md#basic-and-standard-tiers). No entanto, o IoT Hub continuará a fazer log instáctil para o Azure Monitor se vir um cabeçalho de contexto de traço devidamente composto.
- Para garantir um funcionamento eficiente, o IoT Hub imporá um acelerador na taxa de exploração madeireira que pode ocorrer como parte do rastreio distribuído.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre o padrão de rastreio distribuído geral em microserviços, consulte o padrão de [arquitetura microservice: rastreio distribuído](https://microservices.io/patterns/observability/distributed-tracing.html).
- Para configurar a configuração para aplicar as definições de rastreio distribuídas a um grande número de dispositivos, consulte os [dispositivos Configure e monitor IoT à escala](iot-hub-auto-device-config.md).
- Para saber mais sobre o Monitor Azure, veja [o que é o Monitor Azure?](../azure-monitor/overview.md)
