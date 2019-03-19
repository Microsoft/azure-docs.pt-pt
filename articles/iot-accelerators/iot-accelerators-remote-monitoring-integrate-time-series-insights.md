---
title: Integrar o Time Series Insights com a monitorização remota - Azure | Documentos da Microsoft
description: Nesta explicação de procedimento, aprenderá como configurar o Time Series Insights para uma solução de monitorização remota existente que já não inclui o Time Series Insights.
author: aditidugar
manager: timlt
ms.author: adugar
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 850d8bbb525763e0e7d0c0441173180b7c469dd8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58085155"
---
# <a name="integrate-azure-time-series-insights-with-remote-monitoring"></a>Integrar o Azure Time Series Insights com a Monitorização Remota

O Azure Time Series Insights é um serviço totalmente gerido de análise, armazenamento e visualização para a gestão de dados de séries temporais à escala de IoT na cloud. Pode utilizar o Time Series Insights para armazenar e gerir dados de séries temporais, explorar e visualizar eventos em simultâneo, realizar análises de causa raiz e comparar vários sites e recursos.

O acelerador de solução de monitorização remota agora fornece implementação automática e a integração com o Time Series Insights. Este procedimento, irá aprender a configurar o Time Series Insights para uma solução de monitorização remota existente que já não inclui o Time Series Insights.

> [!NOTE]
> O Time Series Insights não está atualmente disponível na cloud do Azure China. Novas implementações de acelerador de solução de monitorização remota na cloud do Azure China utilizem Cosmos DB para todo o armazenamento.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este procedimentos, terá de se já tiver implementado uma solução de monitorização remota:

* [Implementar o acelerador de solução de monitorização remota](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Criar um grupo de consumidores

Crie um grupo de consumidores dedicado no seu IoT Hub para ser utilizado para transmissão em fluxo de dados para o Time Series Insights.

> [!NOTE]
> Grupos de consumidores são utilizados por aplicações para obter dados do IoT Hub do Azure. Cada grupo de consumidores permite que até cinco consumidores de saída. Deve criar um novo grupo de consumidores para cada cinco sinks de saída e pode criar até 32 grupos de consumidores.

1. No portal do Azure, clique no botão do Cloud Shell.

1. Execute o seguinte comando para criar um novo grupo de consumidores. Utilize o nome do hub IoT na sua implementação de monitorização remota e o nome da sua implementação de monitorização remota, como o nome do grupo de recursos:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="deploy-time-series-insights"></a>Implementar o Time Series Insights

Em seguida, implemente o Time Series Insights como um recurso adicional na sua solução de monitorização remota e ligá-la para o hub IoT.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione **criar um recurso** > **Internet das coisas** > **Time Series Insights**.

    ![O Time Series Insights novo](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights.png)

1. Para criar o seu ambiente do Time Series Insights, utilize os valores na tabela a seguir:

    | Definição | Value |
    | ------- | ----- |
    | Nome do ambiente | Captura de ecrã seguinte utiliza o nome **contorosrmtsi**. Escolha o seu próprio nome exclusivo quando concluir este passo. |
    | Subscrição | Selecione a sua subscrição do Azure na lista pendente. |
    | Grupo de recursos | **Utilizar existente**. Selecione o nome do seu grupo de recursos de monitorização remota existente. |
    | Localização | Estamos a utilizar **E.U.A. Leste**. Crie o seu ambiente na mesma região que a sua solução de monitorização remota se possível. |
    | Sku |**S1** |
    | Capacidade | **1** |

    ![Criar o Time Series Insights](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights-create.png)

1. Clique em **Criar**. Pode demorar um momento para o ambiente ser criado.

## <a name="create-event-source"></a>Crie a origem de eventos

Crie uma nova origem de evento para ligar ao seu hub IoT. Certifique-se de que utilize o grupo de consumidores, criado nos passos anteriores. O Time Series Insights exige que cada serviço tem um grupo de consumidores dedicado não está em utilização por outro serviço.

1. Navegue para o novo ambiente do Time Series Insights.

1. No lado esquerdo, selecione **origens de eventos**.

    ![Visualizar origens de eventos](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources.png)

1. Clique em **Adicionar**.

    ![Adicionar origem de eventos](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources-add.png)

1. Para configurar o seu hub IoT como uma nova origem de evento, utilize os valores na tabela a seguir:

    | Definição | Value |
    | ------- | ----- |
    | Nome da origem de evento | Captura de ecrã seguinte utiliza o nome **contosorm-iot-hub**. Utilize o seu próprio nome exclusivo quando concluir este passo. |
    | Origem | **Hub IoT** |
    | Opção de Importar | **Utilize o IoT Hub a partir de subscrições disponíveis** |
    | ID da subscrição | Selecione a sua subscrição do Azure na lista pendente. |
    | Nome do hub IoT | **contosorma57a6**. Utilize o nome do hub IoT partir da sua solução de monitorização remota. |
    | nome de política do hub IoT | **iothubowner** Certifique-se de que a diretiva usada é uma política de proprietário. |
    | chave de política do hub IoT | Este campo é preenchido automaticamente. |
    | Grupo de consumidores do hub IoT | **timeseriesinsights** |
    | Formato de serialização de eventos | **JSON**     | 
    | Nome da propriedade de carimbo de data/hora | Deixar em branco |

    ![Criar origem de evento](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-source-create.png)

1. Clique em **Criar**.

## <a name="configure-the-data-access-policy"></a>Configurar a política de acesso de dados

Para certificar-se de que todos os utilizadores que têm acesso à sua solução de monitorização remota são capazes de explorar os dados no Explorador do Time Series Insights, adicione a aplicação e os utilizadores em políticas de acesso de dados no portal do Azure. 

1. Na lista de navegação, selecione **Grupos de recursos**.

1. Escolha o **ContosoRM** grupo de recursos.

1. Escolher **contosormtsi** na lista de recursos do Azure.

1. Escolher **políticas de acesso de dados** para ver a lista atual de atribuições de funções.

1. Escolher **Add** para abrir o **selecione a função de utilizador** painel.

   Se não tiver permissões para atribuir funções, não vir a **adicionar** opção.

1. Na **função** na lista pendente, selecione uma função, como **leitor** e **contribuinte**.

1. Na lista **Selecionar**, selecione um utilizador, grupo ou aplicação. Se não vir o principal de segurança na lista, pode escrever na caixa **Selecionar** para procurar no diretório os nomes a apresentar, endereços de e-mail e identificadores de objetos.

1. Escolha **Guardar** para criar a atribuição de função. Após alguns instantes, a entidade de segurança é atribuída a função em políticas de acesso de dados.

> [!NOTE]
> Se precisar de conceder acesso de utilizadores adicionais para o Explorador do Time Series Insights, pode utilizar estes passos para [conceder acesso a dados](../time-series-insights/time-series-insights-data-access.md#grant-data-access).

## <a name="configure-azure-stream-analytics"></a>Configurar o Azure Stream Analytics 

A próxima etapa é configurar o microsserviços do Azure Stream Analytics Manager para interromper o envio de mensagens para o Cosmos DB e armazená-los apenas no Time Series Insights. Se gostaria de duplicar as mensagens no Cosmos DB, ignore este passo.

1. Na lista de navegação, selecione **Grupos de recursos**.

1. Escolha o **ContosoRM** grupo de recursos.

1. Encontre o Azure Stream Analytics (ASA) tarefa na lista de recursos de transmissão em fluxo. O nome de recurso começa com **streamingjobs -**.

1. Na parte superior, clique no botão para parar o ASA tarefas de transmissão em fluxo.

1. Editar a consulta do ASA e remover as **SELECIONAR**, **INTO**, e **FROM** cláusulas que apontam para as mensagens de transmitir em fluxo no Cosmos DB. Essas cláusulas devem ser na parte inferior da consulta e ter um aspeto semelhante ao seguinte exemplo:

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

6. Reinicie as tarefas de transmissão em fluxo do Azure Stream Analytics.

7. Extrair as alterações mais recentes para os microsserviços de Gestor do Azure Stream Analytics, escrevendo o seguinte comando no prompt de comando:

.NET: 

```cmd/sh
docker pull azureiotpcs/asa-manager-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/asa-manager-java:1.0.2
```

## <a name="configure-the-telemetry-microservice"></a>Configurar os microsserviços de telemetria

Extrair os microsserviços de telemetria mais recente, escrevendo o seguinte comando no prompt de comando:

.NET:

```cmd/sh
docker pull azureiotpcs/telemetry-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/telemetry-java:1.0.2
```

## <a name="optional-configure-the-web-ui-to-link-to-the-time-series-insights-explorer"></a>*[Opcional]*  Configurar a web da interface do Usuário para ligar ao Explorador do Time Series Insights

Para ver facilmente os seus dados no Explorador do Time Series Insights, é recomendável personalizando a interface do Usuário para ligar facilmente ao ambiente. Para tal, extrair as alterações mais recentes para a IU da Web com o seguinte comando:

```cmd/sh
docker pull azureiotpcs/pcs-remote-monitoring-webui:1.0.2
```

## <a name="configure-the-environment-variables"></a>Configurar as variáveis de ambiente

Para concluir a integração do Time Series Insights, terá de configurar o ambiente da sua implementação para os microsserviços atualizados.

### <a name="basic-deployments"></a>Implementações básicas

Configurar o ambiente de `basic` implementação para os microsserviços atualizados.

1. No portal do Azure, clique nas **do Azure Active Directory** separador no painel à esquerda.

1. Clique em **registos das aplicações**.

1. Procure e clique no seu **ContosoRM** aplicação.

1. Navegue para **configurações** > **chaves** e, em seguida, crie uma nova chave para a sua aplicação. Certifique-se copiar o valor de chave em local seguro.

1. Extrair os [mais recente docker compose ficheiro yaml](https://github.com/Azure/pcs-cli/tree/5a9b4e0dbe313172eff19236e54a4d461d4f3e51/solutions/remotemonitoring/single-vm) do repositório do GitHub com a etiqueta mais recente. 

1. SSH para a VM ao seguir os passos descritos em [como criar e utilizar chaves SSH](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

1. Assim que estiver ligado, escreva `cd /app`.

1. Adicione as seguintes variáveis de ambiente para cada microsserviço no docker compõem ficheiro yaml e o `env-setup` script na VM:

    ```sh
    PCS_TELEMETRY_STORAGE_TYPE=tsi
    PCS_TSI_FQDN={TSI Data Access FQDN}
    PCS_AAD_TENANT={AAD Tenant Id}
    PCS_AAD_APPID={AAD application Id}
    PCS_AAD_APPSECRET={AAD application key}
    ```

1. Navegue para o **o serviço de telemetria** e também editar o docker compose ficheiro adicionando as variáveis de ambiente mesmo acima.

1. Navegue para o **serviço de Gestor do ASA** e editar o docker compose ficheiro adicionando `PCS_TELEMETRY_STORAGE_TYPE`.

1. Reinicie os contentores de docker com `sudo ./start.sh` da VM.

### <a name="standard-deployments"></a>Implementações padrão

Configurar o ambiente de `standard` implementação para os microsserviços atualizados acima

1. Na linha de comandos, execute `kubectl proxy`. Para obter mais informações, consulte [aceder à API do Kubernetes](https://kubernetes.io/docs/tasks/access-kubernetes-api/http-proxy-access-api/#using-kubectl-to-start-a-proxy-server).

1. Abra a consola de gestão do Kubernetes.

1. Encontre o mapa de configuração para adicionar as seguintes variáveis de ambiente novo do TSI:

    ```yaml
    telemetry.storage.type: "tsi"
    telemetry.tsi.fqdn: "{TSI Data Access FQDN}"
    security.auth.serviceprincipal.secret: "{AAD application service principal secret}"
    ```

4. Edite o ficheiro yaml de modelo para pod do serviço de telemetria:

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

5. Edite o ficheiro yaml de modelo de pod de serviço do ASA manager:

    ```yaml
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    ```

## <a name="next-steps"></a>Passos Seguintes

* Para saber mais sobre como explorar os seus dados e diagnosticar um alerta no Explorador do Time Series Insights, consulte o nosso tutorial sobre [realizando uma raiz analisar a causa](iot-accelerators-remote-monitoring-root-cause-analysis.md).

* Para saber como a exploração e consultar dados no Explorador do Time Series Insights, consulte a documentação sobre o [Explorador do Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).
