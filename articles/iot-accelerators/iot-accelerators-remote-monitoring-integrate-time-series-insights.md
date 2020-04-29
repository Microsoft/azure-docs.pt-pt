---
title: Integrar insights da Série de Tempo com Monitorização Remota - Azure / Microsoft Docs
description: Neste how-to você aprenderá como configurar Time Series Insights para uma solução de Monitorização Remota existente que ainda não inclui Insights da Série Tempo.
author: Philmea
manager: timlt
ms.author: philmea
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 752529454a5b6293d9cbfdf8378b46947aed5a0e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77564649"
---
# <a name="integrate-azure-time-series-insights-with-remote-monitoring"></a>Integrar o Azure Time Series Insights com a Monitorização Remota

Azure Time Series Insights é um serviço de análise, armazenamento e visualização totalmente gerido para gerir dados da série de tempo em escala IoT na nuvem. Você pode usar Time Series Insights para armazenar e gerir dados de séries de tempo, explorar e visualizar eventos simultaneamente, realizar análises de causa-raiz, e comparar vários sites e ativos.

O acelerador de soluções de monitorização remota fornece agora a implantação automática e integração com insights da Série Time. Neste como- aprender, você aprende como configurar Time Series Insights para uma solução de Monitorização Remota existente que ainda não inclui Insights da Série Tempo.

> [!NOTE]
> A Time Series Insights não está atualmente disponível na nuvem Azure China. Novas implementações de aceleradores de solução de monitorização remota na nuvem Azure China utilizam cosmos DB para todo o armazenamento.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este how-to, você precisa já ter implementado uma solução de Monitorização Remota:

* [Implementar o acelerador de solução de monitorização remota](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Criar um grupo de consumidores

Crie um grupo de consumidores dedicado no seu IoT Hub para ser utilizado para transmitir dados para Time Series Insights.

> [!NOTE]
> Os grupos de consumidores são utilizados por aplicações para retirar dados do Azure IoT Hub. Cada grupo de consumidores permite até cinco consumidores de produção. Deve criar um novo grupo de consumidores para cada cinco afundadores de produção e pode criar até 32 grupos de consumidores.

1. No portal Azure, clique no botão Cloud Shell.

1. Execute o seguinte comando para criar um novo grupo de consumidores. Utilize o nome do hub IoT na sua implementação de Monitorização Remota e o nome da sua implementação de Monitorização Remota como nome do grupo de recursos:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="deploy-time-series-insights"></a>Implementar insights da série de tempo

Em seguida, implemente os Time Series Insights como um recurso adicional na sua solução de Monitorização Remota e conecte-o ao hub IoT.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione **Criar um recurso** > **Internet of Things** > **Time Series Insights**.

    ![Insights da série new time](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights.png)

1. Para criar o seu ambiente Time Series Insights, utilize os valores na tabela seguinte:

    | Definição | Valor |
    | ------- | ----- |
    | Nome do Ambiente | A imagem seguinte usa o nome **contorosrmtsi**. Escolha o seu nome único quando completar este passo. |
    | Subscrição | Selecione a sua subscrição do Azure na lista pendente. |
    | Grupo de recursos | **Utilize o existente**. Selecione o nome do seu grupo de recursos de Monitorização Remota existente. |
    | Localização | Estamos a usar os **EUA Orientais.** Crie o seu ambiente na mesma região que a sua solução de Monitorização Remota, se possível. |
    | Sku |**S1** |
    | Capacidade | **1** |

    ![Criar insights da série de tempo](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights-create.png)

1. Clique em **Criar**. Pode levar um momento para que o ambiente seja criado.

## <a name="create-event-source"></a>Crie a origem de eventos

Crie uma nova fonte de evento para se conectar ao seu hub IoT. Certifique-se de que utiliza o grupo de consumidores criado nos passos anteriores. Time Series Insights exige que cada serviço tenha um grupo de consumidores dedicado que não seja utilizado por outro serviço.

1. Navegue para o seu novo ambiente time series Insights.

1. À esquerda, selecione **Fontes**de Evento .

    ![Ver Fontes de Eventos](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources.png)

1. Clique em **Adicionar**.

    ![Adicionar Fonte de Evento](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources-add.png)

1. Para configurar o seu hub IoT como uma nova fonte de evento, utilize os valores na tabela seguinte:

    | Definição | Valor |
    | ------- | ----- |
    | Nome da origem de evento | A imagem seguinte utiliza o nome **contosorm-iot-hub**. Use o seu próprio nome único quando completar este passo. |
    | Origem | **IoT Hub** |
    | Opção de Importar | **Use o IoT Hub a partir de subscrições disponíveis** |
    | ID da subscrição | Selecione a sua subscrição do Azure na lista pendente. |
    | Nome do hub IoT | **Contosorma57a6.** Utilize o nome do seu hub IoT a partir da sua solução de Monitorização Remota. |
    | Nome da política do hub IoT | **iothubowner** Certifique-se de que a política utilizada é uma política do proprietário. |
    | Chave política do hub de iot | Este campo é povoado automaticamente. |
    | Grupo de consumidores do hut IoT | **timeseriesinsights** |
    | Formato de serialização de eventos | **JSON**     | 
    | Nome da propriedade Carimbo de data/hora | Deixar em branco |

    ![Criar fonte de evento](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-source-create.png)

1. Clique em **Criar**.

## <a name="configure-the-data-access-policy"></a>Configure a política de acesso a dados

Para garantir que todos os utilizadores que tenham acesso à sua solução de Monitorização Remota são capazes de explorar dados no explorador Time Series Insights, adicione a sua aplicação e utilizadores ao abrigo de políticas de acesso a dados no portal Azure. 

1. Na lista de navegação, selecione **Grupos de recursos**.

1. Escolha o grupo de recursos **ContosoRM.**

1. Escolha **contosormtsi** na lista de recursos azure.

1. Escolha Políticas de Acesso a **Dados** para ver a lista atual de atribuições de funções.

1. Escolha **Adicionar** para abrir o painel de regra do **utilizador select.**

   Se não tiver permissão para atribuir funções, não vê a opção **Adicionar.**

1. Na lista de abandono de **funções,** selecione um papel como **Reader** e **Contributor**.

1. Na lista **Selecionar**, selecione um utilizador, grupo ou aplicação. Se não vir o principal de segurança na lista, pode escrever na caixa **Selecionar** para procurar no diretório os nomes a apresentar, endereços de e-mail e identificadores de objetos.

1. Escolha **Guardar** para criar a atribuição de função. Após alguns momentos, o diretor de segurança é atribuído o papel nas políticas de acesso a dados.

> [!NOTE]
> Se necessitar de conceder acesso adicional aos utilizadores ao explorador de Insights da Série Time, pode utilizar estes passos para [conceder acesso](../time-series-insights/time-series-insights-data-access.md#grant-data-access)aos dados .

## <a name="configure-azure-stream-analytics"></a>Configure Azure Stream Analytics 

O próximo passo é configurar o microserviço Do Gestor de Análise de Fluxo saqueado para descontinuar o envio de mensagens para cosmos DB e armazená-las apenas em Time Series Insights. Ignore este passo se quiser duplicar as suas mensagens em Cosmos DB.

1. Na lista de navegação, selecione **Grupos de recursos**.

1. Escolha o grupo de recursos **ContosoRM.**

1. Encontre o trabalho de streaming azure Stream Analytics (ASA) na lista de recursos. O nome do recurso começa com **trabalhos de streaming.**

1. Na parte superior, clique no botão para parar os trabalhos de streaming ASA.

1. Editar a consulta ASA e remover as cláusulas **SELECT,** **INTO**e **FROM** que apontam para o fluxo de mensagens em Cosmos DB. Estas cláusulas devem estar na parte inferior da consulta e parecer o seguinte exemplo:

    ```sql
    SELECT
            CONCAT(T.IoTHub.ConnectionDeviceId, ';', CAST(DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) AS nvarchar(max))) as id,
            1 as [doc.schemaVersion],
            'd2cmessage' as [doc.schema],
            T.IoTHub.ConnectionDeviceId as [device.id],
            'device-sensors;v1' as [device.msg.schema],
            'StreamingJobs' as [data.schema],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', System.Timestamp) as [device.msg.created],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) as [device.msg.received],
            udf.removeUnusedProperties(T) as Data
    INTO
        Messages
    FROM
        DeviceTelemetry T PARTITION BY PartitionId TIMESTAMP BY T.EventEnqueuedUtcTime
    ```

6. Reinicie os trabalhos de streaming do Azure Stream Analytics.

7. Puxe as últimas alterações para o microserviço do gestor do Azure Stream Analytics digitando o seguinte comando no pedido de comando:

.NET: 

```cmd/sh
docker pull azureiotpcs/asa-manager-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/asa-manager-java:1.0.2
```

## <a name="configure-the-telemetry-microservice"></a>Configure o microserviço de Telemetria

Puxe o mais recente microserviço de Telemettry digitando o seguinte comando no pedido de comando:

.NET:

```cmd/sh
docker pull azureiotpcs/telemetry-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/telemetry-java:1.0.2
```

## <a name="optional-configure-the-web-ui-to-link-to-the-time-series-insights-explorer"></a>*[Opcional]* Configure o UI web para ligar ao explorador de Insights da Série Tempo

Para visualizar facilmente os seus dados no explorador time series Insights, recomendamos personalizar o UI para facilmente ligar ao ambiente. Para isso, puxe as últimas alterações para a Web UI utilizando o seguinte comando:

```cmd/sh
docker pull azureiotpcs/pcs-remote-monitoring-webui:1.0.2
```

## <a name="configure-the-environment-variables"></a>Configure as variáveis ambientais

Para completar a integração time series Insights, terá de configurar o ambiente da sua implementação para os microserviços atualizados.

### <a name="basic-deployments"></a>Implementações básicas

Configure o `basic` ambiente de implantação dos microserviços atualizados.

1. No portal Azure, clique no separador **Azure Ative Diretório** no painel à esquerda.

1. Clique nos **registos da App**.

1. Procure e clique na sua aplicação **ContosoRM.**

1. Navegue para **Definições** > **De Teclas** e, em seguida, crie uma nova chave para a sua aplicação. Certifique-se de copiar o Valor Chave para um local seguro.

1. Puxe o [mais recente ficheiro yaml de composição](https://github.com/Azure/pcs-cli/tree/5a9b4e0dbe313172eff19236e54a4d461d4f3e51/solutions/remotemonitoring/single-vm) do GitHub repo usando a última etiqueta. 

1. SSH no VM seguindo os passos delineados sobre [como criar e utilizar teclas SSH](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

1. Uma vez `cd /app`ligado, escreva .

1. Adicione as seguintes variáveis ambientais a cada microserviço no `env-setup` ficheiro de composição do estivador yaml e o script no VM:

    ```sh
    PCS_TELEMETRY_STORAGE_TYPE=tsi
    PCS_TSI_FQDN={TSI Data Access FQDN}
    PCS_AAD_TENANT={AAD Tenant Id}
    PCS_AAD_APPID={AAD application Id}
    PCS_AAD_APPSECRET={AAD application key}
    ```

1. Navegue para o serviço de **telemetria** e edite também o ficheiro de composição do estivador adicionando as mesmas variáveis ambientais acima.

1. Navegue para o **serviço de gerente da ASA** e edite o ficheiro de composição do estivador adicionando `PCS_TELEMETRY_STORAGE_TYPE`.

1. Reiniciar os recipientes `sudo ./start.sh` de estivador utilizados a partir do VM.

> [!NOTE]
> A configuração acima das variáveis ambientais é válida para versões de Monitorização Remota antes de 1.0.2

### <a name="standard-deployments"></a>Implementações padrão

Configure o `standard` ambiente de implantação dos micro serviços atualizados acima

1. Na linha de `kubectl proxy`comando, corra. Para mais informações, consulte [o acesso à API kubernetes](https://kubernetes.io/docs/tasks/access-kubernetes-api/http-proxy-access-api/#using-kubectl-to-start-a-proxy-server).

1. Abra a consola de gestão Kubernetes.

1. Encontre o mapa de configuração para adicionar as seguintes novas variáveis ambientais para a TSI:

    ```yaml
    telemetry.storage.type: "tsi"
    telemetry.tsi.fqdn: "{TSI Data Access FQDN}"
    security.auth.serviceprincipal.secret: "{AAD application service principal secret}"
    ```

4. Editar o ficheiro de modelo yaml para a cápsula de serviço de telemetria:

    ```yaml
    - name: PCS_AAD_TENANT
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.tenant
    - name: PCS_AAD_APPID
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.audience
    - name: PCS_AAD_APPSECRET
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.serviceprincipal.secret
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    - name: PCS_TSI_FQDN
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.tsi.fqdn
    ```

5. Editar o ficheiro de modelo yaml para o serviço de gerente ASA:

    ```yaml
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    ```

## <a name="next-steps"></a>Passos seguintes

* Para saber como explorar os seus dados e diagnosticar um alerta no explorador time series Insights, consulte o nosso tutorial sobre a realização de uma análise de [causas de raiz](iot-accelerators-remote-monitoring-root-cause-analysis.md).

* Para aprender a explorar e consultar dados no explorador time series Insights, consulte documentação sobre o explorador de Insights da [Série De Tempo Azure](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).
