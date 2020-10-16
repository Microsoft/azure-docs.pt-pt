---
title: Integrar Insights de Séries Tempoturais com Monitorização Remota - Azure / Microsoft Docs
description: Neste como-a-fazer, aprenderás a configurar insights de séries de tempo para uma solução de monitorização remota existente que já não inclui insights de séries temporizes.
author: Philmea
manager: timlt
ms.author: philmea
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 8b6219c82dc23deb467e87a6866839b8eace8b5d
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92072650"
---
# <a name="integrate-azure-time-series-insights-with-remote-monitoring"></a>Integrar o Azure Time Series Insights com a Monitorização Remota

Azure Time Series Insights é um serviço de análise, armazenamento e visualização totalmente gerido para gerir dados de séries de tempo à escala IoT na nuvem. Pode utilizar o Time Series Insights para armazenar e gerir dados de séries de tempo, explorar e visualizar eventos simultaneamente, realizar análises de causas de raiz e comparar vários sites e ativos.

O acelerador de solução de monitorização remota fornece agora a implementação e integração automáticas com o Time Series Insights. Neste como-fazer, aprende-se a configurar insights de séries de tempo para uma solução de monitorização remota existente que já não inclui insights de séries temporítros.

> [!NOTE]
> A Time Series Insights não está atualmente disponível na nuvem Azure China. As novas implementações de aceleradores de solução de monitorização remota na nuvem Azure China utilizam o Cosmos DB para todo o armazenamento.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este como fazer, é necessário já ter implementado uma solução de Monitorização Remota:

* [Implementar o acelerador de solução de monitorização remota](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Criar um grupo de consumidores

Crie um grupo de consumidores dedicado no seu Hub IoT para ser utilizado para transmitir dados à Time Series Insights.

> [!NOTE]
> Os grupos de consumidores são utilizados por aplicações para retirar dados do Azure IoT Hub. Cada grupo de consumidores permite até cinco consumidores de produção. Deve criar um novo grupo de consumidores para cada cinco lavatórios de produção e pode criar até 32 grupos de consumidores.

1. No portal Azure, clique no botão Cloud Shell.

1. Execute o seguinte comando para criar um novo grupo de consumidores. Utilize o nome do hub IoT na sua implementação de Monitorização Remota e o nome da sua implementação de Monitorização Remota como nome do grupo de recursos:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="deploy-time-series-insights"></a>Implementar insights de séries de tempo

Em seguida, implemente o Time Series Insights como um recurso adicional na sua solução de Monitorização Remota e conecte-o ao hub IoT.

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Selecione **Criar um recurso**Internet of  >  **Things**Time  >  **Series Insights**.

    ![Novos Insights da Série Temporal](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights.png)

1. Para criar o seu ambiente de Insights de Séries Tempo, utilize os valores na tabela seguinte:

    | Definição | Valor |
    | ------- | ----- |
    | Nome do Ambiente | A imagem que se segue utiliza o nome **contorosrmtsi**. Escolha o seu próprio nome único quando completar este passo. |
    | Subscrição | Selecione a sua subscrição do Azure na lista pendente. |
    | Grupo de recursos | **Utilize a existência**. Selecione o nome do seu grupo de recursos de monitorização remota existente. |
    | Localização | Estamos a usar **os EUA Orientais.** Crie o seu ambiente na mesma região que a sua solução de Monitorização Remota, se possível. |
    | Sku |**S1** |
    | Capacidade | **1** |

    ![Criar o Time Series Insights](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights-create.png)

1. Clique em **Criar**. Pode levar um momento para que o ambiente seja criado.

## <a name="create-event-source"></a>Crie a origem de eventos

Crie uma nova fonte de eventos para ligar ao seu hub IoT. Certifique-se de que utiliza o grupo de consumidores criado nos passos anteriores. A Time Series Insights exige que cada serviço tenha um grupo de consumidores dedicado que não seja utilizado por outro serviço.

1. Navegue para o seu novo ambiente time series Insights.

1. À esquerda, selecione **Fontes de Eventos**.

    ![Ver Fontes de Evento](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources.png)

1. Clique em **Adicionar**.

    ![Adicionar Fonte de Evento](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources-add.png)

1. Para configurar o seu hub IoT como uma nova fonte de eventos, utilize os valores na tabela seguinte:

    | Definição | Valor |
    | ------- | ----- |
    | Nome da origem de evento | A imagem que se segue utiliza o nome **contosorm-iot-hub**. Use o seu próprio nome único quando completar este passo. |
    | Origem | **Hub IoT** |
    | Opção de Importar | **Use o IoT Hub a partir de subscrições disponíveis** |
    | ID da subscrição | Selecione a sua subscrição do Azure na lista pendente. |
    | Nome do hub IoT | **contosorma57a6**. Utilize o nome do seu hub IoT a partir da sua solução de Monitorização Remota. |
    | Nome da política do hub IoT | **iothubowner** Certifique-se de que a apólice utilizada é uma política de proprietários. |
    | Chave política do hub Iot | Este campo é povoado automaticamente. |
    | Grupo de consumidores do hut IoT | **timeseriesinsights** |
    | Formato de serialização de eventos | **JSON**     | 
    | Nome da propriedade Carimbo de data/hora | Deixar em branco |

    ![Criar Fonte de Eventos](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-source-create.png)

1. Clique em **Criar**.

## <a name="configure-the-data-access-policy"></a>Configure a política de acesso a dados

Para garantir que todos os utilizadores que tenham acesso à sua solução de Monitorização Remota sejam capazes de explorar dados no explorador time series Insights, adicione a sua aplicação e utilizadores ao abrigo de políticas de acesso a dados no portal Azure. 

1. Na lista de navegação, selecione **Grupos de recursos**.

1. Escolha o grupo de recursos **ContosoRM.**

1. Escolha **contosormtsi** na lista de recursos da Azure.

1. Escolha políticas de acesso a **dados** para ver a lista atual de atribuições de funções.

1. Escolha **Adicionar** para abrir o **painel de regras do utilizador selecionado.**

   Se não tiver permissões para atribuir funções, não vê a opção **Adicionar.**

1. Na lista de drop-down **role,** selecione uma função como **Leitor** e **Colaborador**.

1. Na lista **Selecionar**, selecione um utilizador, grupo ou aplicação. Se não vir o principal de segurança na lista, pode escrever na caixa **Selecionar** para procurar no diretório os nomes a apresentar, endereços de e-mail e identificadores de objetos.

1. Escolha **Guardar** para criar a atribuição de função. Após alguns momentos, o diretor de segurança é atribuído ao papel nas políticas de acesso a dados.

> [!NOTE]
> Se precisar de conceder aos utilizadores adicionais acesso ao explorador Time Series Insights, pode utilizar estes passos para conceder acesso aos [dados.](../time-series-insights/concepts-access-policies.md#grant-data-access)

## <a name="configure-azure-stream-analytics"></a>Configurar Azure Stream Analytics 

O próximo passo é configurar o microservice Azure Stream Analytics Manager para descontinuar o envio de mensagens para a Cosmos DB e armazená-las apenas no Time Series Insights. Ignore este passo se quiser duplicar as suas mensagens no Cosmos DB.

1. Na lista de navegação, selecione **Grupos de recursos**.

1. Escolha o grupo de recursos **ContosoRM.**

1. Encontre o trabalho de streaming Azure Stream Analytics (ASA) na lista de recursos. O nome do recurso começa com **trabalhos de streaming.**

1. No topo, clique no botão para parar os trabalhos de streaming asa.

1. Edite a consulta ASA e remova as cláusulas **SELECT**, **INTO**e FROM que apontam para o fluxo **de** mensagens em Cosmos DB. Estas cláusulas devem estar na parte inferior da consulta e parecer o seguinte exemplo:

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

7. Puxe as alterações mais recentes no microserviço do gestor Azure Stream Analytics digitando o seguinte comando na solicitação de comando:

.NET: 

```cmd/sh
docker pull azureiotpcs/asa-manager-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/asa-manager-java:1.0.2
```

## <a name="configure-the-telemetry-microservice"></a>Configure o microserviço da Telemetria

Puxe o mais recente microserviço de telemetria digitando o seguinte comando na solicitação de comando:

.NET:

```cmd/sh
docker pull azureiotpcs/telemetry-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/telemetry-java:1.0.2
```

## <a name="optional-configure-the-web-ui-to-link-to-the-time-series-insights-explorer"></a>*[Opcional]* Configure a UI web para ligar ao explorador de Insights da Série Tempo

Para visualizar facilmente os seus dados no explorador time series Insights, recomendamos personalizar a UI para ligar facilmente ao ambiente. Para tal, retire as alterações mais recentes para o UI web utilizando o seguinte comando:

```cmd/sh
docker pull azureiotpcs/pcs-remote-monitoring-webui:1.0.2
```

## <a name="configure-the-environment-variables"></a>Configure as variáveis ambientais

Para completar a integração do Time Series Insights, terá de configurar o ambiente da sua implementação para os microserviços atualizados.

### <a name="basic-deployments"></a>Implementações básicas

Configure o ambiente de `basic` implantação para os microserviços atualizados.

1. No portal Azure, clique no separador **Azure Ative Directory** no painel esquerdo.

1. Clique nas **inscrições da App.**

1. Procure e clique na sua aplicação **ContosoRM.**

1. Navegue para **As Teclas de Definições**  >  **Keys** e, em seguida, crie uma nova chave para a sua aplicação. Certifique-se de copiar o Valor Chave para um local seguro.

1. Puxe o [mais recente ficheiro yaml composição](https://github.com/Azure/pcs-cli/tree/5a9b4e0dbe313172eff19236e54a4d461d4f3e51/solutions/remotemonitoring/single-vm) do gitHub repo usando a etiqueta mais recente. 

1. SSH no VM seguindo os passos delineados sobre [como criar e utilizar chaves SSH](../virtual-machines/linux/ssh-from-windows.md).

1. Uma vez ligado, escreva `cd /app` .

1. Adicione as seguintes variáveis ambientais a cada microserviço no ficheiro yaml de composição do estiva e o `env-setup` script no VM:

    ```sh
    PCS_TELEMETRY_STORAGE_TYPE=tsi
    PCS_TSI_FQDN={TSI Data Access FQDN}
    PCS_AAD_TENANT={AAD Tenant Id}
    PCS_AAD_APPID={AAD application Id}
    PCS_AAD_APPSECRET={AAD application key}
    ```

1. Navegue para o **serviço de telemetria** e edite também o ficheiro de composição do estiva adicionando as mesmas variáveis ambientais acima.

1. Navegue para o **serviço de gerente da ASA** e edite o ficheiro de composição do estivar adicionando `PCS_TELEMETRY_STORAGE_TYPE` .

1. Reinicie os recipientes de estivador utilizando `sudo ./start.sh` a partir do VM.

> [!NOTE]
> A configuração acima das variáveis ambientais é válida para versões de monitorização remota antes de 1.0.2

### <a name="standard-deployments"></a>Implementações padrão

Configure o ambiente de `standard` implantação para os micro serviços atualizados acima

1. Na linha de comando, `kubectl proxy` corra. Para mais informações, consulte [o acesso à API de Kubernetes.](https://kubernetes.io/docs/reference/access-authn-authz/#using-kubectl-to-start-a-proxy-server)

1. Abra a consola de gestão Kubernetes.

1. Encontre o mapa de configuração para adicionar as seguintes novas variáveis ambientais para a TSI:

    ```yaml
    telemetry.storage.type: "tsi"
    telemetry.tsi.fqdn: "{TSI Data Access FQDN}"
    security.auth.serviceprincipal.secret: "{AAD application service principal secret}"
    ```

4. Editar o ficheiro yaml do modelo para o pod de serviço de telemetria:

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

5. Editar o ficheiro yaml do modelo para o pod de serviço do gestor ASA:

    ```yaml
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    ```

## <a name="next-steps"></a>Passos seguintes

* Para saber como explorar os seus dados e diagnosticar um alerta no explorador de Insights da Série Tempo, consulte o nosso tutorial sobre [a realização de uma análise de causa raiz.](iot-accelerators-remote-monitoring-root-cause-analysis.md)

* Para aprender a explorar e consultar dados no explorador de Insights da Série Tempo, consulte a documentação sobre o [explorador Azure Time Series Insights](../time-series-insights/time-series-insights-explorer.md).