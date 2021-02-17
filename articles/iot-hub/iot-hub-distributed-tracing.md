---
title: Adicionar IDs de correlação a mensagens IoT c/rastreio distribuído (pré)
description: Saiba como utilizar a capacidade de rastreio distribuído para rastrear mensagens IoT através dos serviços Azure utilizados pela sua solução.
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: jlian
ms.custom:
- amqp
- mqtt
- fasttrack-edit
- iot
ms.openlocfilehash: adcbf4efc4dfaa7701c18440531327949640cb53
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100581988"
---
# <a name="trace-azure-iot-device-to-cloud-messages-with-distributed-tracing-preview"></a>Trace Azure IoT mensagens dispositivo-nuvem com rastreio distribuído (pré-visualização)

O Microsoft Azure IoT Hub suporta atualmente o rastreio distribuído como [uma funcionalidade de pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O IoT Hub é um dos primeiros serviços da Azure a apoiar o rastreio distribuído. À medida que mais serviços Azure suportam rastreios distribuídos, você será capaz de rastrear mensagens IoT em todos os serviços Azure envolvidos na sua solução. Para obter um fundo sobre o rastreio distribuído, consulte [o Rastreio Distribuído.](../azure-monitor/app/distributed-tracing.md)

Ativar o rastreio distribuído para o IoT Hub dá-lhe a capacidade de:

- Monitorize precisamente o fluxo de cada mensagem através do IoT Hub utilizando [o contexto de traços](https://github.com/w3c/trace-context). Este contexto de traços inclui IDs de correlação que permitem correlacionar eventos de um componente com eventos de outro componente. Pode ser aplicado para um subconjunto ou todas as mensagens de dispositivo IoT utilizando [o dispositivo twin](iot-hub-devguide-device-twins.md).
- Registar automaticamente o contexto de rastreio para [registos do Monitor Azure](monitor-iot-hub.md).
- Meça e compreenda o fluxo de mensagens e a latência dos dispositivos para o IoT Hub e os pontos finais de encaminhamento.
- Comece a considerar como pretende implementar rastreios distribuídos para os serviços não-Azure na sua solução IoT.

Neste artigo, utilize o [dispositivo Azure IoT SDK para C](iot-hub-device-sdk-c-intro.md) com rastreio distribuído. O suporte de rastreio distribuído ainda está em andamento para os outros SDKs.

## <a name="prerequisites"></a>Pré-requisitos

- A pré-visualização do rastreio distribuído é atualmente suportada apenas para os Hubs IoT criados nas seguintes regiões:

  - **Europa do Norte**
  - **Sudeste Asiático**
  - **E.U.A. Oeste 2**

- Este artigo pressupõe que está familiarizado com o envio de mensagens de telemetria para o seu centro de IoT. Certifique-se de que completou o [Envio de Telemetria C Quickstart](quickstart-send-telemetry-c.md).

- Registe um dispositivo com o seu hub IoT (passos disponíveis em cada Quickstart) e anota a cadeia de ligação.

- Instale a versão mais recente do [Git](https://git-scm.com/download/).

## <a name="configure-iot-hub"></a>Configure IoT Hub

Nesta secção, configura-se um Hub IoT para registar atributos de rastreio distribuídos (IDs de correlação e timetamps).

1. Navegue até ao seu hub IoT no [portal Azure](https://portal.azure.com/).

1. No painel esquerdo para o seu hub IoT, desloque-se até à secção **de Monitorização** e clique nas **definições de Diagnóstico**.

1. Clique **na definição de diagnóstico de adicionar**.

1. No campo **Nome,** insira um nome para uma nova definição de diagnóstico. Por exemplo, **DistributedTracingSettings**.

1. Escolha uma ou mais das seguintes opções que determinam para onde será enviada a exploração madeireira:

    - **Arquivar para uma conta de armazenamento**: Configure uma conta de armazenamento para conter as informações de registo.
    - **Transmita para um centro de eventos**: Configure um centro de eventos para conter a informação de registo.
    - **Enviar para Log Analytics**: Configure um espaço de trabalho de análise de registo para conter as informações de registo.

1. Na secção **'Registar',** selecione as operações para as quais pretende registar informações.

    Certifique-se de incluir **DistributedTracing** e configurar uma **Retenção** durante quantos dias pretende que a exploração madeireira seja mantida. A retenção de registos afeta os custos de armazenamento.

    ![Screenshot mostrando onde a categoria DistributedTracing é para configurações de diagnóstico IoT](./media/iot-hub-distributed-tracing/diag-logs.png)

1. Clique em **Guardar** para a nova definição.

1. (Opcional) Para ver as mensagens fluírem para locais diferentes, crie [regras de encaminhamento para pelo menos dois pontos finais diferentes](iot-hub-devguide-messages-d2c.md).

Uma vez que o registo é ligado, o IoT Hub regista um registo quando uma mensagem que contém propriedades de vestígios válidos é encontrada em qualquer uma das seguintes situações:

- As mensagens chegam ao portal do IoT Hub.
- A mensagem é processada pelo IoT Hub.
- A mensagem é encaminhada para pontos finais personalizados. O encaminhamento deve ser ativado.

Para saber mais sobre estes registos e seus esquemas, consulte [o Monitor IoT Hub](monitor-iot-hub.md) e o rastreio distribuído em [registos de recursos do IoT Hub](monitor-iot-hub-reference.md#distributed-tracing-preview).

## <a name="set-up-device"></a>Configurar dispositivo

Nesta secção, você prepara um ambiente de desenvolvimento para uso com o [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). Em seguida, modifica uma das amostras para permitir o rastreio distribuído nas mensagens de telemetria do seu dispositivo.

Estas instruções são para a construção da amostra no Windows. Para outros ambientes, consulte [Compilar o C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compile) ou [C SDK pré-embalado para desenvolvimento específico da plataforma.](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#prepackaged-c-sdk-for-platform-specific-development)

### <a name="clone-the-source-code-and-initialize"></a>Clone o código fonte e inicialize

1. Instale [a carga de trabalho "Desktop development with C++"](/cpp/build/vscpp-step-0-installation?view=vs-2019) para Visual Studio 2019. O Visual Studio 2017 e 2015 também são apoiados.

1. Instale [o CMake](https://cmake.org/). Certifique-se de que está na sua `PATH` ingtilagem `cmake -version` a partir de um pedido de comando.

1. Abra uma linha de comandos ou a shell do Git Bash. Executar os seguintes comandos para clonar a mais recente versão do repositório [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub:

    ```cmd
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Esta operação deve demorar vários minutos a ser concluída.

1. Crie um subdiretório `cmake` no diretório de raiz do repositório git e navegue para essa pasta. Executar os seguintes comandos a partir do `azure-iot-sdk-c` diretório:

    ```cmd
    mkdir cmake
    cd cmake
    cmake ..
    ```

    Se `cmake` não conseguir encontrar o seu compilador C++, poderá obter erros de construção enquanto executa o comando acima. Se isto acontecer, tente executar o comando seguinte na [linha de comandos do Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs). 

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

### <a name="edit-the-send-telemetry-sample-to-enable-distributed-tracing"></a>Editar a amostra de telemetria de envio para permitir o rastreio distribuído

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/azure-iot-distributed-tracing-sample/blob/master/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c" target="_blank">Obtenha a amostra no GitHub</a>

1. Use um editor para abrir o `azure-iot-sdk-c/iothub_client/samples/iothub_ll_telemetry_sample/iothub_ll_telemetry_sample.c` ficheiro fonte.

1. Procure a declaração da constante `connectionString`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=2)]

    Substitua o valor da `connectionString` constante pela cadeia de ligação do dispositivo que fez notar no registo de uma secção de [dispositivo](./quickstart-send-telemetry-c.md#register-a-device) do Envio [de Telemetria C Quickstart](./quickstart-send-telemetry-c.md).

1. Alterar a `MESSAGE_COUNT` definição `5000` para:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=3)]

1. Encontre a linha de código que chama `IoTHubDeviceClient_LL_SetConnectionStatusCallback` para registar uma função de chamada de chamada de estado de ligação antes do loop de mensagem de envio. Adicione o código sob essa linha, tal como mostrado abaixo, para ligar `IoTHubDeviceClient_LL_EnablePolicyConfiguration` permitindo o rastreio distribuído para o dispositivo:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_tracing&highlight=5)]

    A `IoTHubDeviceClient_LL_EnablePolicyConfiguration` função permite políticas para funcionalidades específicas do IoTHub que são configuradas através de [gémeos do dispositivo.](./iot-hub-devguide-device-twins.md) Uma vez `POLICY_CONFIGURATION_DISTRIBUTED_TRACING` ativado com a linha de código acima, o comportamento de rastreio do dispositivo refletirá alterações de rastreio distribuídas feitas no twin do dispositivo.

1. Para manter a aplicação da amostra em funcionamento sem utilizar toda a sua quota, adicione um atraso de um segundo no final do ciclo de mensagem de envio:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_sleep&highlight=8)]

### <a name="compile-and-run"></a>Compilar e correr

1. Navegue para o *iothub_ll_telemetry_sample* diretório de projetos a partir do diretório CMake `azure-iot-sdk-c/cmake` () que criou anteriormente, e compile a amostra:

    ```cmd
    cd iothub_client/samples/iothub_ll_telemetry_sample
    cmake --build . --target iothub_ll_telemetry_sample --config Debug
    ```

1. Execute a aplicação. O dispositivo envia telemetria suportando o rastreio distribuído.

    ```cmd
    Debug/iothub_ll_telemetry_sample.exe
    ```

1. Mantenha a aplicação em funcionamento. Opcionalmente observar a mensagem sendo enviada para o IoT Hub olhando para a janela da consola.

<!-- For a client app that can receive sampling decisions from the cloud, check out [this sample](https://aka.ms/iottracingCsample).  -->

### <a name="workaround-for-third-party-clients"></a>Solução alternativa para clientes de terceiros

Não é **trivial** pré-visualizar a função de rastreio distribuído sem usar o C SDK. Assim, esta abordagem não é recomendada.

Em primeiro lugar, deve implementar todas as primitivas do protocolo IoT Hub nas suas mensagens seguindo o guia dev [Criar e ler mensagens IoT Hub](iot-hub-devguide-messages-construct.md). Em seguida, edite as propriedades do protocolo nas mensagens MQTT/AMQP para adicionar `tracestate` como propriedade do **sistema**. Mais concretamente:

* Para mQTT, adicione `%24.tracestate=timestamp%3d1539243209` ao tópico da mensagem, onde `1539243209` deve ser substituído pelo tempo de criação da mensagem no formato de relógio unix. Como exemplo, consulte a implementação [no C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/iothubtransport_mqtt_common.c#L761)
* Para AMQP, adicione `key("tracestate")` e `value("timestamp=1539243209")` como anotação de mensagem. Para uma implementação de referência, consulte [aqui.](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/uamqp_messaging.c#L527)

Para controlar a percentagem de mensagens que contêm esta propriedade, implemente a lógica para ouvir eventos iniciados na nuvem, como atualizações duplas.

## <a name="update-sampling-options"></a>Atualizar opções de amostragem 

Para alterar a percentagem de mensagens a serem rastreadas a partir da nuvem, tem de atualizar o dispositivo gémeo. Você pode realizar isso de várias maneiras, incluindo o editor JSON no portal e o serviço IoT Hub SDK. As seguintes subsecções fornecem exemplos.

### <a name="update-using-the-portal"></a>Atualizar usando o portal

1. Navegue para o seu hub IoT no [portal Azure](https://portal.azure.com/)e, em seguida, clique em **dispositivos IoT**.

1. Clique no seu dispositivo.

1. Procurar **ativar o rastreio distribuído (pré-visualização)** e, em seguida, selecione **Ativar**.

    ![Ativar o rastreio distribuído no portal Azure](./media/iot-hub-distributed-tracing/azure-portal.png)

1. Escolha uma **taxa de amostragem** entre 0% e 100%.

1. Clique em **Guardar**.

1. Aguarde alguns segundos e bata **em Refresh**, então, se reconhecido com sucesso pelo dispositivo, aparece um ícone de sincronização com uma marca de verificação.

1. Volte para a janela da consola para a aplicação de mensagem de telemetria. Verá mensagens a serem enviadas `tracestate` nas propriedades da aplicação.

    ![Estado de rastreio](./media/iot-hub-distributed-tracing/MicrosoftTeams-image.png)

1. (Opcional) Altere a taxa de amostragem para um valor diferente e observe a alteração de frequência que as mensagens incluem `tracestate` nas propriedades da aplicação.

### <a name="update-using-azure-iot-hub-for-vs-code"></a>Atualizar usando O Hub IoT Azure para código VS

1. Instale o Código VS e, em seguida, instale a versão mais recente do Azure IoT Hub para o Código VS a partir [daqui](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

1. Abra o Código VS e [crie a cadeia de ligação IoT Hub](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit#user-content-prerequisites).

1. Expandir o dispositivo e procurar a **Definição de Rastreio Distribuído (Pré-visualização)**. Nele, clique em **Atualizar Definição de Rastreio Distribuído (Pré-visualização)** do sub-nó.

    ![Ativar o rastreio distribuído na extensão do Hub Azure IoT](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-1.png)

1. Na janela popup, selecione **Ativar** e, em seguida, prima Enter para confirmar 100 como taxa de amostragem.

    ![Atualizar o modo de amostragem](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-2.png)

    ![Taxa de amostragem atualizada](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-3.png)

### <a name="bulk-update-for-multiple-devices"></a>Atualização em massa para vários dispositivos

Para atualizar a configuração de amostragem de rastreio distribuída para vários dispositivos, utilize [a configuração automática do dispositivo](./iot-hub-automatic-device-management.md). Certifique-se de seguir este esquema gémeo:

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
| `sampling_mode` | Sim | Número inteiro | Dois valores de modo são atualmente suportados para ligar e desligar a amostragem. `1` está ligado e, `2` está desligado. |
| `sampling_rate` | Yes | Número inteiro | Este valor é uma percentagem. Apenas `0` `100` são permitidos valores de (inclusive) são permitidos.  |

## <a name="query-and-visualize"></a>Consulta e visualização

Para ver todos os vestígios registados por um Hub IoT, consulte a loja de registos que selecionou nas definições de diagnóstico. Esta secção percorre algumas opções diferentes.

### <a name="query-using-log-analytics"></a>Consulta usando Log Analytics

Se tiver configurado [o Log Analytics com registos de recursos,](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)questione-o procurando registos na `DistributedTracing` categoria. Por exemplo, esta consulta mostra todos os vestígios registados:

```Kusto
// All distributed traces 
AzureDiagnostics 
| where Category == "DistributedTracing" 
| project TimeGenerated, Category, OperationName, Level, CorrelationId, DurationMs, properties_s 
| order by TimeGenerated asc  
```

Registos de exemplo como mostrado por Log Analytics:

| TimeGenerated | OperationName | Categoria | Level | CorrelationId | DurationMs | Propriedades |
|--------------------------|---------------|--------------------|---------------|---------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-02-22T03:28:28.633Z | DiagnósticoIotHubD2C | Tracagem distribuída | Informativo | 00-8cd869a412459a25f5b4f313123344-0144d2590aacd909-01 |  | {"deviceId":"AZ3166","messageSize":"96","callerLocalTimeUtc":"2018-02-22T03:27:28.633Z","calleeLocalTimeUtc":"2018-02-22T03:27:28.687Z"} |
| 2018-02-22T03:28:38.633Z | DiagnósticoIoTHubIngress | Tracagem distribuída | Informativo | 00-8cd869a412459a25f5b4f31312344-349810a9bbd28730-01 | 20 | {"isRoutingEnabled":"falso", "parentSpanId":"0144d2590aacd909"} |
| 2018-02-22T03:28:48.633Z | DiagnósticoIoTHubEgress | Tracagem distribuída | Informativo | 00-8cd869a412459a25f5b4f31312344-349810a9bbd28730-01 | 23 | {"endpointType":"EventHub","endpointName":"myEventHub", "parentSpanId":"0144d2590aacd909"} |

Para compreender os diferentes tipos de registos, consulte [os registos de rastreio distribuídos do Azure IoT Hub](monitor-iot-hub-reference.md#distributed-tracing-preview).

### <a name="application-map"></a>Mapeamento de Aplicações

Para visualizar o fluxo de mensagens IoT, crie a aplicação de amostra do Mapa de Aplicações. A aplicação da amostra envia os registos de rastreio distribuídos para o [Mapa da Aplicação](../azure-monitor/app/app-map.md) utilizando uma Função Azure e um Centro de Eventos.

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/e2e-diagnostic-provision-cli" target="_blank">Obtenha a amostra no GitHub</a>

Esta imagem abaixo mostra o rastreio distribuído no Mapa de Aplicações com três pontos finais de encaminhamento:

![Rastreio distribuído por IoT no Mapa de Aplicações](./media/iot-hub-distributed-tracing/app-map.png)

## <a name="understand-azure-iot-distributed-tracing"></a>Compreenda o rastreio distribuído por IoT Azure

### <a name="context"></a>Contexto

Muitas soluções IoT, incluindo a nossa própria [arquitetura de referência](/azure/architecture/reference-architectures/iot) (apenas inglês), geralmente seguem uma variante da arquitetura de [microserviços.](/azure/architecture/microservices/) À medida que uma solução IoT se torna mais complexa, acaba-se por usar uma dúzia ou mais microserviços. Estes microserviços podem ou não ser de Azure. Identificar onde as mensagens IoT estão a cair ou a abrandar pode tornar-se um desafio. Por exemplo, tem uma solução IoT que utiliza 5 diferentes serviços Azure e 1500 dispositivos ativos. Cada dispositivo envia 10 mensagens de dispositivo para nuvem/segundo (para um total de 15.000 mensagens/segundo), mas nota que a sua aplicação web vê apenas 10.000 mensagens/segundo. Onde está o problema? Como acha o culpado?

### <a name="distributed-tracing-pattern-in-microservice-architecture"></a>Padrão de rastreio distribuído na arquitetura de microservice

Para reconstruir o fluxo de uma mensagem IoT em diferentes serviços, cada serviço deve propagar um *ID de correlação* que identifique exclusivamente a mensagem. Uma vez recolhidos num sistema centralizado, os IDs de correlação permitem-lhe ver o fluxo da mensagem. Este método é chamado de [padrão de rastreio distribuído.](/azure/architecture/microservices/logging-monitoring#distributed-tracing)

Para apoiar uma adoção mais ampla para rastreio distribuído, a Microsoft está a contribuir para a [proposta padrão W3C para rastreio distribuído](https://w3c.github.io/trace-context/).

### <a name="iot-hub-support"></a>Suporte IoT Hub

Uma vez ativado, o suporte de rastreio distribuído para o IoT Hub seguirá este fluxo:

1. Uma mensagem é gerada no dispositivo IoT.
1. O dispositivo IoT decide (com a ajuda da nuvem) que esta mensagem deve ser atribuída com um contexto de rastreamento.
1. O SDK adiciona um `tracestate` à propriedade da mensagem, contendo a estamp de tempo de criação de mensagens.
1. O dispositivo IoT envia a mensagem para o IoT Hub.
1. A mensagem chega ao portal do hub da IoT.
1. O IoT Hub procura as `tracestate` propriedades da mensagem e verifica se está no formato correto.
1. Em caso afirmativo, o IoT Hub gera um exclusivo global `trace-id` para a mensagem, um `span-id` para o "hop", e regista-os em [registos de rastreio distribuídos pelo IoT Hub](monitor-iot-hub-reference.md#distributed-tracing-preview) no âmbito da operação. `DiagnosticIoTHubD2C`
1. Uma vez terminado o processamento da mensagem, o IoT Hub gera outro `span-id` e regista-o juntamente com o existente `trace-id` no âmbito da operação `DiagnosticIoTHubIngress` .
1. Se o encaminhamento estiver ativado para a mensagem, o IoT Hub escreve-a para o ponto final personalizado e regista outro `span-id` com o mesmo na categoria `trace-id` `DiagnosticIoTHubEgress` .
1. Os passos acima são repetidos para cada mensagem gerada.

## <a name="public-preview-limits-and-considerations"></a>Limites e considerações de visualização pública

- A proposta de norma W3C Trace Context é atualmente um projeto de trabalho.
- Atualmente, a única linguagem de desenvolvimento suportada pelo cliente SDK é C.
- A capacidade de twin cloud-to-device não está disponível para [o nível básico IoT Hub](iot-hub-scaling.md#basic-and-standard-tiers). No entanto, o IoT Hub ainda irá registar-se no Azure Monitor se vir um cabeçalho de contexto de traço devidamente composto.
- Para garantir um funcionamento eficiente, o IoT Hub imporá um acelerador à taxa de exploração madeireira que pode ocorrer como parte do rastreio distribuído.

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre o padrão de rastreio distribuído em microserviços, consulte o [padrão de arquitetura Microservice: rastreio distribuído.](https://microservices.io/patterns/observability/distributed-tracing.html)
- Para configurar a configuração para aplicar definições de rastreio distribuídos a um grande número de dispositivos, consulte [dispositivos Configure e monitoriem dispositivos IoT à escala](./iot-hub-automatic-device-management.md).
- Para saber mais sobre o Azure Monitor, veja [o que é o Azure Monitor?](../azure-monitor/overview.md)
- Para saber mais sobre a utilização do Azure Monitor com IoT HUb, consulte [o Monitor IoT Hub](monitor-iot-hub.md)