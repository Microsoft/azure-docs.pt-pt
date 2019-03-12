---
title: Visualizar dados do Azure Data Explorer no Grafana
description: Este procedimento, irá aprender a configurar o Explorador de dados do Azure como uma origem de dados para o Grafana e, em seguida, visualize dados a partir de um cluster de exemplo.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/05/2018
ms.openlocfilehash: 141859d155383b01cfea998c6b7158848517eac2
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57531957"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Visualizar dados do Azure Data Explorer no Grafana

O Grafana é uma plataforma de análise que permite-lhe consultar e visualizar dados, em seguida, criar e partilhar dashboards com base nas suas visualizações. O Grafana fornece um Explorador de dados do Azure *Plug-in*, que permite-lhe ligar a e visualize os dados a partir do Explorador de dados do Azure. Neste artigo, saiba como configurar o Explorador de dados do Azure como uma origem de dados para o Grafana e, em seguida, visualize dados a partir de um cluster de exemplo.

## <a name="prerequisites"></a>Pré-requisitos

É necessário o seguinte para concluir este como:

* [O Grafana versão 5.3.0 ou posterior](https://docs.grafana.org/installation/) do seu sistema operativo

* O [Plug-in do Explorador de dados do Azure](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) para o Grafana

* Um cluster que inclui os dados de exemplo StormEvents. Para obter mais informações, consulte [início rápido: Criar um cluster do Explorador de dados do Azure e a base de dados](create-cluster-database-portal.md) e [ingerir dados de exemplo no Explorador de dados do Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="configure-the-data-source"></a>Configurar a origem de dados

Execute os seguintes passos para configurar o Explorador de dados do Azure como uma origem de dados para o Grafana. Vamos abordar essas etapas mais detalhadamente nesta secção:

1. Crie um Azure Active Directory (Azure AD) principal de serviço. O principal de serviço é utilizado pelo Grafana para aceder ao serviço do Explorador de dados do Azure.

1. Adicionar o principal de serviço do Azure AD para o *visualizadores* função da base de dados do Explorador de dados do Azure.

1. Especificar as propriedades de ligação da Grafana com base nas informações do principal de serviço do Azure AD, em seguida, testar a ligação.

### <a name="create-a-service-principal"></a>Criar um principal de serviço

Pode criar o principal de serviço no [portal do Azure](#azure-portal) ou utilizando o [CLI do Azure](#azure-cli) experiência da linha de comandos. Independentemente do método utilizar, após a criação de que obter os valores para quatro propriedades de ligação que pretende utilizar em passos posteriores.

#### <a name="azure-portal"></a>Portal do Azure

1. Para criar o principal de serviço, siga as instruções no [documentação do portal do Azure](/azure/active-directory/develop/howto-create-service-principal-portal).

    1. Na [atribuir a aplicação a uma função](/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) secção, atribuir um tipo de função de **leitor** ao seu cluster do Explorador de dados do Azure.

    1. Na [obter os valores para iniciar sessão](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) secção, copie os valores de três propriedade abrangidos nos passos: **ID do diretório** (ID de inquilino), **ID da aplicação**, e **palavra-passe**.

1. No portal do Azure, selecione **subscrições** , em seguida, copie o ID da subscrição na qual criou o principal de serviço.

    ![ID de subscrição - portal](media/grafana/subscription-id-portal.png)

#### <a name="azure-cli"></a>CLI do Azure

1. Crie um principal de serviço. Definir um âmbito adequado e um tipo de função de `reader`.

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourGrafana}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    Para obter mais informações, consulte [criar um Azure principal de serviço com a CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

1. O comando devolve um resultado de definir semelhante ao seguinte. Copie os valores de propriedade de três: **appID**, **palavra-passe**, e **inquilino**.

    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourGrafana}:{PortNumber}",
      "name": "https://{UrlToYourGrafana}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Obter uma lista das suas subscrições.

    ```azurecli
    az account list --output table
    ```

    Copie o ID de subscrição adequada.

    ![ID de subscrição - CLI](media/grafana/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>Adicionar o principal de serviço à função visualizadores

Agora que tem um principal de serviço, adicione-o para o *visualizadores* função da base de dados do Explorador de dados do Azure. Pode realizar esta tarefa sob **permissões** no portal do Azure ou em **consulta** utilizando um comando de gestão.

#### <a name="azure-portal---permissions"></a>Portal do Azure – permissões

1. No portal do Azure, aceda ao seu cluster do Explorador de dados do Azure.

1. Na **descrição geral** secção, selecione a base de dados com os dados de exemplo StormEvents.

    ![Selecione a base de dados](media/grafana/select-database.png)

1. Selecione **permissões** , em seguida, **adicionar**.

    ![Permissões de base de dados](media/grafana/database-permissions.png)

1. Sob **adicionar permissões de base de dados**, selecione a **Visualizador** , em seguida, a função **selecionar principais**.

    ![Adicionar permissões de base de dados](media/grafana/add-permission.png)

1. Procure o principal de serviço que criou (o exemplo mostra o principal **mb grafana**). Selecione o principal, em seguida, **selecione**.

    ![Gerir as permissões no portal do Azure](media/grafana/new-principals.png)

1. Selecione **Guardar**.

    ![Gerir as permissões no portal do Azure](media/grafana/save-permission.png)

#### <a name="management-command---query"></a>Comandos de gestão - consulta

1. No portal do Azure, aceda ao seu cluster do Azure Data Explorer e selecione **consulta**.

    ![Consulta](media/grafana/query.png)

1. Execute o seguinte comando na janela da consulta. Utilize o ID da aplicação e ID de inquilino a partir do portal do Azure ou da CLI.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    O comando devolve um resultado de definir semelhante ao seguinte. Neste exemplo, a primeira linha é para um utilizador existente na base de dados e a segunda linha destina-se o principal de serviço que acabou de ser adicionada.

    ![Conjunto de resultados](media/grafana/result-set.png)

### <a name="specify-properties-and-test-the-connection"></a>Especificar as propriedades e testar a ligação

Com o principal de serviço atribuído para o *visualizadores* função, pode agora especificar as propriedades na sua instância do Grafana e testar a ligação ao Explorador de dados do Azure.

1. No Grafana, no menu da esquerda, selecione o ícone de engrenagem, em seguida, **origens de dados**.

    ![Origens de dados](media/grafana/data-sources.png)

1. Selecione **Adicionar origem de dados**.

1. Sobre o **origens de dados / novos** página, introduza um nome para a origem de dados, em seguida, selecione o tipo **origem de dados do Azure Data Explorer**.

    ![Nome da ligação e o tipo](media/grafana/connection-name-type.png)

1. Introduza o nome do seu cluster no https://{ClusterName formulário}. {Region}. kusto.windows.net. Introduza os outros valores a partir do portal do Azure ou da CLI. Consulte a tabela abaixo da imagem seguinte para um mapeamento.

    ![Propriedades de ligação](media/grafana/connection-properties.png)

    | Interface de Usuário o Grafana | Portal do Azure | CLI do Azure |
    | --- | --- | --- |
    | ID da subscrição | ID DA SUBSCRIÇÃO | SubscriptionId |
    | Id de Inquilino | ID do diretório | inquilino |
    | ID de Cliente | ID da aplicação | appId |
    | Segredo do cliente | Palavra-passe | palavra-passe |
    | | | |

1. Selecione **guardar e testar**.

    Se o teste for bem-sucedida, vá para a secção seguinte. Se encontrar algum problema, verifique os valores que especificou no Grafana e os passos anteriores de revisão.

## <a name="visualize-data"></a>Visualizar os dados

Agora que tiver terminado de configurar o Explorador de dados do Azure como uma origem de dados para o Grafana, chegou a hora para visualizar os dados. Vamos mostrar aqui um exemplo básico, mas há muito mais que pode fazer. Recomendamos olhar [escrever consultas para o Explorador de dados do Azure](write-queries.md) para obter exemplos de outras consultas a serem executadas em relação ao conjunto de dados de exemplo.

1. No Grafana, no menu da esquerda, selecione o ícone de adição, em seguida, **Dashboard**.

    ![criar dashboard](media/grafana/create-dashboard.png)

1. Sob o **Add** separador, selecione **gráfico**.

    ![Adicionar gráfico](media/grafana/add-graph.png)

1. No painel de gráfico, selecione **título do painel** , em seguida, **editar**.

    ![Editar o painel](media/grafana/edit-panel.png)

1. Na parte inferior do painel, selecione **origem de dados** , em seguida, selecione a origem de dados que configurou.

    ![Selecionar origem de dados](media/grafana/select-data-source.png)

1. No painel de consulta, copie a seguinte consulta, em seguida, selecione **executar**. A consulta buckets de contagem de eventos por dia para o conjunto de dados de exemplo.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Executar consulta](media/grafana/run-query.png)

1. O gráfico não mostra os resultados, porque ele tem um âmbito por padrão a dados das últimas seis horas. No menu superior, selecione **últimas 6 horas**.

    ![Últimos seis horas](media/grafana/last-six-hours.png)

1. Especifique um intervalo personalizado que abrange 2007, o ano incluído no nosso conjunto de dados de exemplo StormEvents. Selecione **Aplicar**.

    ![Intervalo de datas personalizadas](media/grafana/custom-date-range.png)

    Agora o gráfico mostra os dados de 2007, registados por dia.

    ![Gráfico concluído](media/grafana/finished-graph.png)

1. No menu superior, selecione o salvamento ícone: ![Ícone de guardar](media/grafana/save-icon.png).

## <a name="next-steps"></a>Passos Seguintes

[Escrever consultas do Azure Data Explorer](write-queries.md)

[Tutorial: Visualize os dados a partir do Explorador de dados do Azure no Power BI](visualize-power-bi.md)