---
title: Adicionar IDs de correlação para mensagens de IoT com o rastreio distribuído (pré-visualização)
description: ''
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: jlian
ms.openlocfilehash: 0553bd904cfaabaefce4e6ab3f7fbf5d356922d3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58100365"
---
# <a name="trace-azure-iot-device-to-cloud-messages-with-distributed-tracing-preview"></a>Mensagens de dispositivo-para-cloud do Azure IoT com o rastreio distribuído (pré-visualização) de rastreio

IoT Hub do Microsoft Azure suporta atualmente o rastreio distribuído como um [funcionalidade de pré-visualização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O IoT Hub é um dos primeiros serviços do Azure para suportar o rastreio distribuído. As mais serviços do Azure suportam o rastreio distribuído, estará mensagens de IoT de capaz de rastreamento em todo os envolvidos na sua solução de serviços do Azure. Para um plano de fundo no rastreio distribuído, consulte [rastreio distribuído](../azure-monitor/app/distributed-tracing.md).

Ativar o rastreio distribuído para o IoT Hub dá-lhe a capacidade de:

- Monitorizar com precisão o fluxo de cada mensagem através do IoT Hub com [contexto de rastreio](https://github.com/w3c/trace-context). Este contexto de rastreio inclui IDs que permitem-lhe correlacionar eventos de um componente com eventos a partir de outro componente de correlação. Pode ser aplicada para um subconjunto ou todas as mensagens de dispositivo de IoT usando [dispositivo duplo](iot-hub-devguide-device-twins.md).
- O contexto de rastreio para início de sessão automático [registos de diagnóstico do Azure Monitor](iot-hub-monitor-resource-health.md).
- Analise e compreenda o fluxo de mensagens e a latência dos dispositivos ao IoT Hub e o encaminhamento de pontos de extremidade.
- Começar a considerar a forma como pretende implementar o rastreio distribuído para os serviços não pertencente ao Azure na sua solução de IoT.

Neste artigo, vai utilizar o [Azure IoT device SDK para C](./iot-hub-device-sdk-c-intro.md) com o rastreio distribuído. Suporte de rastreio distribuído ainda está em curso para os outros SDKs.

## <a name="prerequisites"></a>Pré-requisitos

- A pré-visualização do rastreio distribuído é atualmente suportado apenas para os Hubs IoT criado nas seguintes regiões:

  - **Europa do Norte**
  - **Sudeste asiático**
  - **E.U.A. oeste 2**

- Este artigo pressupõe que esteja familiarizado com o envio de mensagens de telemetria ao seu hub IoT. Certifique-se de que concluiu o [enviar telemetria início rápido de C](./quickstart-send-telemetry-c.md).

- Registe um dispositivo com o seu IoT hub (passos disponíveis em cada início rápido) e anote a cadeia de ligação.

- Instale a versão mais recente do [Git](https://git-scm.com/download/).

## <a name="configure-iot-hub"></a>Configurar o IoT Hub

Nesta secção, configurou um IoT Hub para iniciar o rastreio distribuído atributos (IDs de correlação e carimbos de data /).

1. Navegue até ao seu hub IoT no [portal do Azure](https://portal.azure.com/).

1. No painel esquerdo do seu hub IoT, desloque para baixo para o **monitorização** secção e clique em **as definições de diagnóstico**.

1. Se as definições de diagnóstico já não estiverem ativadas, clique em **ativar os diagnósticos**. Se já tiver ativado as definições de diagnóstico, clique em **Adicionar definição de diagnóstico**.

1. Na **nome** , insira um nome para uma nova definição de diagnóstico. Por exemplo, **DistributedTracingSettings**.

1. Escolha um ou mais das seguintes opções que determinam em que será enviado o registo:

    - **Arquivo para uma conta de armazenamento**: Configure uma conta de armazenamento para armazenar as informações de registo.
    - **Stream para um hub de eventos**: Configure um hub de eventos para conter as informações de registo.
    - **Enviar para o Log Analytics**: Configure uma área de trabalho do log analytics para conter as informações de registo.

1. Na **Log** secção, selecione as operações que pretende que as informações de registo para.

    Certifique-se de incluir **DistributedTracing**e configurar um **retenção** quantos dias pretende obter o Registro em log retido. Retenção do registo afeta os custos de armazenamento.

    ![Captura de ecrã que mostra onde a categoria de DistributedTracing é para as definições de diagnóstico de IoT](./media/iot-hub-distributed-tracing/diag-logs.png)

1. Clique em **guardar** para a nova definição.

1. (Opcional) Para ver as mensagens fluam para locais diferentes e configure as [regras de encaminhamento para, pelo menos, dois pontos de extremidade diferentes](iot-hub-devguide-messages-d2c.md).

Assim que o registo está ativado, o IoT Hub regista um registo de quando uma mensagem que contém as propriedades de rastreio válido é encontrada em qualquer uma das seguintes situações:

- As mensagens chegam gateway do IoT Hub.
- A mensagem é processada pelo IoT Hub.
- A mensagem é encaminhada para os pontos finais personalizados. Encaminhamento tem de estar ativado.

Para saber mais sobre estes registos e dos respetivos esquemas, veja [distribuídas de rastreio nos registos de diagnóstico do IoT Hub](iot-hub-monitor-resource-health.md#distributed-tracing-preview).

## <a name="set-up-device"></a>Configurar o dispositivo

Nesta secção, deve preparar-se um ambiente de desenvolvimento para utilização com o [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c). Em seguida, modifique um dos exemplos para ativar o rastreio distribuído em mensagens de telemetria do seu dispositivo.

Estas instruções são para a criação de exemplo no Windows. Para outros ambientes, veja [Compile o SDK de C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compile) ou [previamente incluídas em pacotes SDK de C para plataforma de desenvolvimento específicas](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#prepackaged-c-sdk-for-platform-specific-development).

### <a name="clone-the-source-code-and-initialize"></a>Clonar o código-fonte e inicializar

1. Instale [carga de trabalho "Ambiente de trabalho de desenvolvimento com C++"](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2017) para Visual Studio 2015 ou 2017.

1. Instale [CMake](https://cmake.org/). Certifique-se de que ele está no seu `PATH` digitando `cmake -version` num prompt de comando.

1. Abra uma linha de comandos ou a shell do Git Bash. Execute o seguinte comando para clonar o [SDK C do Azure IoT](https://github.com/Azure/azure-iot-sdk-c) no repositório do GitHub:

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    Atualmente, o tamanho deste repositório é de cerca de 220 MB. Esta operação deve demorar vários minutos a ser concluída.

1. Crie um subdiretório `cmake` no diretório de raiz do repositório git e navegue para essa pasta.

    ```cmd
    cd azure-iot-sdk-c    
    mkdir cmake
    cd cmake
    cmake ..
    ```

    Se `cmake` não é possível encontrar o compilador de C++, poderá obter erros de compilação ao executar o comando acima. Se isto acontecer, tente executar o comando seguinte na [linha de comandos do Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

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

### <a name="edit-the-send-telemetry-sample-to-enable-distributed-tracing"></a>Editar o exemplo de telemetria de envio para ativar o rastreio distribuído

1. Utilize um editor para abrir o `azure-iot-sdk-c/iothub_client/samples/iothub_ll_telemetry_sample/iothub_ll_telemetry_sample.c` ficheiro de origem.

1. Procure a declaração da constante `connectionString`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=2)]

    Substitua o valor do `connectionString` constante com a cadeia de ligação do dispositivo anotou no [registar um dispositivo](./quickstart-send-telemetry-c.md#register-a-device) seção do [enviar telemetria início rápido de C](./quickstart-send-telemetry-c.md).

1. Alteração da `MESSAGE_COUNT` definir para `5000`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=3)]

1. Localize a linha de código que chama `IoTHubDeviceClient_LL_SetConnectionStatusCallback` registrar uma função de retorno de chamada de estado de ligação antes do loop de mensagem de envio. Adicionar código sob essa linha, conforme mostrado abaixo, para chamar `IoTHubDeviceClient_LL_EnablePolicyConfiguration` ativar o rastreio distribuído para o dispositivo:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_tracing&highlight=5)]

    O `IoTHubDeviceClient_LL_EnablePolicyConfiguration` função permite que as políticas para funcionalidades específicas do IoTHub que estão configuradas por meio [dispositivos duplos](./iot-hub-devguide-device-twins.md). Uma vez `POLICY_CONFIGURATION_DISTRIBUTED_TRACING` está ativado com a linha de código acima, o comportamento de rastreamento do dispositivo irá refletir as alterações de rastreio distribuído feitas no dispositivo duplo.

1. Para manter a aplicação de exemplo em execução sem utilizar a segurança de todos os sua quota, adicione um atraso de um segundo no final do loop de mensagem de envio:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_sleep&highlight=8)]

### <a name="compile-and-run"></a>Compilar e executar

1. Navegue para o *iothub_ll_telemetry_sample* diretório do projeto do diretório CMake (`azure-iot-sdk-c/cmake`) que criou anteriormente e compile o exemplo:

    ```cmd
    cd iothub_client/samples/iothub_ll_telemetry_sample
    cmake --build . --target iothub_ll_telemetry_sample --config Debug
    ```

1. Execute a aplicação. O dispositivo envia telemetria de rastreio distribuído de suporte.

    ```cmd
    Debug/iothub_ll_telemetry_sample.exe
    ```

1. Mantenha a executar a aplicação. Opcionalmente, observe a mensagem enviada para o IoT Hub ao observar a janela da consola.

<!-- For a client app that can receive sampling decisions from the cloud, check out [this sample](https://aka.ms/iottracingCsample).  -->

### <a name="using-third-party-clients"></a>Usando os clientes de terceiros

Se não estiver a utilizar o SDK de C e ainda gostaria de pré-visualizar o rastreio distribuído para o IoT Hub, construir a mensagem para conter um `tracestate` propriedade da aplicação com a hora de criação da mensagem no formato unix timestamp. Por exemplo, `tracestate=timestamp=1539243209`. Para controlar a percentagem de mensagens que contêm esta propriedade, implemente a lógica para escutar eventos iniciadas por nuvem, como atualizações de duplo.

## <a name="update-sampling-options"></a>Opções de atualização de amostragem 

Para alterar a percentagem de mensagens de rastreamento da cloud, tem de atualizar o dispositivo duplo. Pode conseguir este várias formas, incluindo o editor de JSON no portal e o SDK do serviço IoT Hub. As subsecções seguintes fornecem exemplos.

### <a name="update-using-the-portal"></a>Atualizar com o portal

1. Navegue até ao seu IoT hub no [portal do Azure](https://portal.azure.com/), em seguida, clique em **dispositivos IoT**.

1. Clique no seu dispositivo.

1. Procure **distribuídas de ativar o rastreio (pré-visualização)**, em seguida, selecione **ativar**.

    ![Ativar o rastreio distribuído no portal do Azure](./media/iot-hub-distributed-tracing/azure-portal.png)

1. Escolher uma **taxa de amostragem** entre 0% e 100%.

1. Clique em **Guardar**.

1. Aguarde alguns segundos e prima **atualizar**, em seguida, se confirmar com êxito por dispositivo, é apresentado um ícone de sincronização com uma marca de verificação.

1. Volte para a janela da consola para a aplicação de mensagem de telemetria. Verá as mensagens enviadas com `tracestate` nas propriedades da aplicação.

    ![Estado de rastreio](./media/iot-hub-distributed-tracing/MicrosoftTeams-image.png)

1. (Opcional) Alterar a frequência de amostragem para um valor diferente e observe a alteração na frequência com que as mensagens incluem `tracestate` nas propriedades da aplicação.

### <a name="update-using-azure-iot-hub-toolkit-for-vs-code"></a>Atualização usando o Kit de ferramentas do Azure IoT Hub para o VS Code

1. Instalar o VS Code, em seguida, instale a versão mais recente do Kit de ferramentas do Azure IoT Hub para o código VS a partir [aqui](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

1. Abra o VS Code e [configurar a cadeia de ligação do IoT Hub](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit#user-content-prerequisites).

1. Expanda o dispositivo e procure **distribuído rastreio definição (pré-visualização)**. Clique em **distribuído rastreio definição de atualização (pré-visualização)** do nó de sub-rotina.

    ![Ativar o rastreio distribuído no Kit de ferramentas do Azure IoT Hub](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-1.png)

1. Na janela de pop-up, selecione **ativar**, em seguida, prima Enter para confirmar a 100 como taxa de amostragem.

    ![Modo de amostragem de atualização](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-2.png)

    ![Frequência de amostragem de atualização](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-3.png)

### <a name="bulk-update-for-multiple-devices"></a>Atualização em massa para vários dispositivos

Para atualizar a configuração de amostragem de rastreio distribuído para vários dispositivos, utilize [configuração do dispositivo automática](iot-hub-auto-device-config.md). Certifique-se de que segue este esquema de duplo:

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
| `sampling_mode` | Sim | Número inteiro | Dois valores de modo são atualmente suportados para ativar e desativar a amostragem. `1` está ativada e, `2` está desativada. |
| `sampling_rate` | Sim | Número inteiro | Este valor é uma percentagem. Apenas os valores da `0` para `100` (inclusive) são permitidos.  |

## <a name="query-and-visualize"></a>Consultar e visualizar

Para ver todos os rastreios registados por um IoT Hub, consulte o arquivo de log que selecionou nas definições de diagnóstico. Esta secção descreve algumas opções diferentes.

### <a name="query-using-log-analytics"></a>Consultar com o Log Analytics

Se configurou [Log Analytics com os registos de diagnóstico](../azure-monitor/platform/diagnostic-logs-stream-log-store.md), consulta, procurando registos no `DistributedTracing` categoria. Por exemplo, esta consulta apresenta todos os rastreios registados:

```Kusto
// All distributed traces 
AzureDiagnostics 
| where Category == "DistributedTracing" 
| project TimeGenerated, Category, OperationName, Level, CorrelationId, DurationMs, properties_s 
| order by TimeGenerated asc  
```

Registos de exemplo conforme apresentado pelo Log Analytics:

| TimeGenerated | OperationName | Categoria | Nível | CorrelationId | DurationMs | Propriedades |
|--------------------------|---------------|--------------------|---------------|---------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-02-22T03:28:28.633Z | DiagnosticIoTHubD2C | DistributedTracing | Informativo | 00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01 |  | {"deviceId":"AZ3166","messageSize":"96","callerLocalTimeUtc":"2018-02-22T03:27:28.633Z","calleeLocalTimeUtc":"2018-02-22T03:27:28.687Z"} |
| 2018-02-22T03:28:38.633Z | DiagnosticIoTHubIngress | DistributedTracing | Informativo | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 20 | {"isRoutingEnabled":"false","parentSpanId":"0144d2590aacd909"} |
| 2018-02-22T03:28:48.633Z | DiagnosticIoTHubEgress | DistributedTracing | Informativo | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 23 | {"endpointType":"EventHub","endpointName":"myEventHub", "parentSpanId":"0144d2590aacd909"} |

Para compreender os diferentes tipos de registos, consulte [registos de diagnóstico do IoT Hub do Azure](iot-hub-monitor-resource-health.md#distributed-tracing-preview).

### <a name="application-map"></a>Mapeamento de Aplicações

Para visualizar o fluxo de mensagens de IoT, configure a aplicação de exemplo do mapa da aplicação. A aplicação de exemplo envia os registos de rastreio distribuído para [mapa da aplicação](../application-insights/app-insights-app-map.md) usando uma função do Azure e um Hub de eventos.

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/e2e-diagnostic-provision-cli" target="_blank">Obter o exemplo no Github</a>

Esta imagem abaixo mostra o rastreio distribuído no mapa da aplicação com três pontos de extremidade de encaminhamento:

![IoT distribuído rastreio no mapa da aplicação](./media/iot-hub-distributed-tracing/app-map.png)

## <a name="understand-azure-iot-distributed-tracing"></a>Compreender o que rastreamento de distribuídas de IoT do Azure

### <a name="context"></a>Contexto

Muitas soluções de IoT, incluindo a nossa própria [arquitetura de referência](https://aka.ms/iotrefarchitecture) (apenas em inglês), em geral, siga uma variante do [uma arquitetura de microsserviço](https://docs.microsoft.com/azure/architecture/microservices/). À medida que uma solução de IoT aumenta mais complexa, acaba com microsserviços de uma dúzia ou mais. Estes microsserviços podem ou não ser do Azure. Indicar onde as mensagens IoT são remover ou tornar mais lentos pode se tornar um desafio. Por exemplo, tem uma solução de IoT que utiliza 5 diferentes serviços do Azure e os dispositivos ativos 1500. Cada dispositivo envia 10 dispositivo-para-cloud mensagens por segundo (para um total de 15 000 mensagens por segundo), mas notar que a aplicação web vê apenas 10 000 mensagens por segundo. Onde está o problema? Como encontra o culpado?

### <a name="distributed-tracing-pattern-in-microservice-architecture"></a>Padrão de rastreio distribuído numa arquitetura de microsserviço

Para reconstruir o fluxo de uma mensagem de IoT em todos os serviços diferentes, cada serviço deve propagar uma *ID de correlação* que identifica exclusivamente a mensagem. Depois de recolhidos num sistema centralizado, o IDs de correlação permitem-lhe ver o fluxo de mensagens. Esse método é chamado de [padrão de rastreio distribuído](https://docs.microsoft.com/azure/architecture/microservices/logging-monitoring#distributed-tracing).

Para suportar mais ampla adoção para rastreio distribuído, a Microsoft está contribuindo com [proposta padrão do W3C para rastreio distribuído](https://w3c.github.io/trace-context/).

### <a name="iot-hub-support"></a>Suporte do IoT Hub

Uma vez ativada, o suporte de rastreio distribuído para o IoT Hub seguir este fluxo:

1. É gerada uma mensagem no dispositivo IoT.
1. O dispositivo de IoT decide (com a ajuda da cloud) que esta mensagem deve ser atribuída com um contexto de rastreio.
1. O SDK adiciona um `tracestate` para a propriedade de aplicativo de mensagem, que contém o carimbo de hora de criação de mensagem.
1. O dispositivo de IoT envia a mensagem para o IoT Hub.
1. A mensagem chega no gateway do hub IoT.
1. IoT Hub procura o `tracestate` nas propriedades da mensagem de aplicativo e verifica se está no formato correto.
1. Se, por isso, o IoT Hub gera e faz a `trace-id` e `span-id` para os registos de diagnóstico do Azure Monitor na categoria `DiagnosticIoTHubD2C`.
1. Quando o processamento da mensagem for concluído, o IoT Hub gera outra `span-id` e regista-lo juntamente com a atual `trace-id` sob a categoria `DiagnosticIoTHubIngress`.
1. Se o encaminhamento é ativado para a mensagem, o IoT Hub escreve-os para o ponto final personalizado e registos de outro `span-id` com o mesmo `trace-id` sob a categoria `DiagnosticIoTHubEgress`.
1. Os passos acima são repetidos para cada mensagem gerada.

## <a name="public-preview-limits-and-considerations"></a>Limites de pré-visualização pública e considerações

- Proposta para o standard de contexto de rastreio do W3C é atualmente um rascunho funcional.
- Atualmente, a linguagem de desenvolvimento apenas suportada pelo SDK de cliente é C.
- Não está disponível para a capacidade de nuvem para o dispositivo duplo [escalão básico do IoT Hub](iot-hub-scaling.md#basic-and-standard-tiers). No entanto, o IoT Hub ainda regista para o Azure Monitor se encontrar um cabeçalho de contexto de rastreio corretamente formado.
- Para garantir a operação eficiente, o IoT Hub irá impor uma limitação à tarifa de registo que pode ocorrer como parte do rastreio distribuído.

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre o padrão geral de rastreio distribuído em microsserviços, veja [padrão de arquitetura de Microsserviços: distribuído rastreio](https://microservices.io/patterns/observability/distributed-tracing.html).
- Para definir a configuração para aplicar definições de rastreio distribuído para um grande número de dispositivos, consulte [configurar e monitorizar dispositivos de IoT em escala](iot-hub-auto-device-config.md).
- Para saber mais sobre o Azure Monitor, consulte [o que é o Azure Monitor?](../azure-monitor/overview.md).
