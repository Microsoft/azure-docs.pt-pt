---
title: Faça upload de dados de faturação para a Azure e veja-os no portal Azure
description: Faça upload de dados de faturação para a Azure e veja-os no portal Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 9da725c433ad5d6233fd164d256692ca407714fc
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92206457"
---
# <a name="upload-billing-data-to-azure-and-view-it-in-the-azure-portal"></a>Faça upload de dados de faturação para a Azure e veja-os no portal Azure

> [!IMPORTANT] 
>  Não há qualquer custo para utilizar os serviços de dados habilitados a Azure Arc durante o período de pré-visualização. Embora o sistema de faturação funcione até ao fim do medidor de faturação está definido para $0.  Se seguir este cenário, verá entradas na sua faturação para um serviço atualmente denominado **serviços de dados híbridos** e para recursos de um tipo chamado **Microsoft. AzureData/ `<resource type>` **. Poderá ver um registo de cada serviço de dados - Azure Arc que criar, mas cada registo será cobrado por $0.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connectivity-modes---implications-for-billing-data"></a>Modos de Conectividade - Implicações para dados de faturação

No futuro, haverá dois modos em que poderá executar os seus serviços de dados habilitados a Azure Arc:

- **Indiretamente ligado** - Não existe ligação direta ao Azure. Os dados são enviados para a Azure apenas através de um processo de exportação/upload. Todas as implementações de serviços de dados Azure Arc funcionam neste modo hoje em pré-visualização.
- **Conectado diretamente** - Neste modo, haverá uma dependência do Arco Azure que permitiu que o serviço Kubernetes fornecesse uma ligação direta entre a Azure e o cluster Kubernetes em que os serviços de dados ativados pelo Arco Azure estão a funcionar. Isto irá permitir-lhe mais capacidades e também permitir-lhe-á utilizar o portal Azure e o CLI Azure para gerir os seus serviços de dados habilitados a Azure Arc, tal como gere os seus serviços de dados em Azure PaaS.  Este modo de conectividade ainda não está disponível na pré-visualização, mas será em breve.

Pode ler mais sobre a diferença entre os [modos de conectividade](./connectivity.md).

No modo indireto ligado, os dados de faturação são periodicamente exportados para fora do controlador de dados do Arco Azure para um ficheiro seguro e depois enviados para Azure e processados.  No próximo modo ligado diretamente, os dados de faturação serão automaticamente enviados para a Azure aproximadamente 1/hora para dar uma visão quase em tempo real sobre os custos dos seus serviços. O processo de exportação e upload dos dados no modo indiretamente conectado também pode ser automatizado usando scripts ou podemos construir um serviço que o fará por si.

## <a name="upload-billing-data-to-azure"></a>Enviar dados de faturação para a Azure

Para carregar os dados de faturação para a Azure, o seguinte deve acontecer primeiro:

1. Crie um serviço de dados ativado a Azure Arc se ainda não o tiver. Por exemplo, criar um dos seguintes:
   - [Criar um exemplo gerido pelo Azure SQL em Azure Arc](create-sql-managed-instance.md)
   - [Criar um grupo de servidores do PostgreSQL Hyperscale preparado para o Azure Arc](create-postgresql-hyperscale-server-group.md)
1. [Faça upload do inventário de recursos, dados de utilização, métricas e registos para o Azure Monitor](upload-metrics-and-logs-to-azure-monitor.md) se ainda não o fez.
1. Aguarde pelo menos 2 horas desde a criação do serviço de dados para que o processo de recolha de telemetria de faturação possa recolher alguns dados de faturação.

Executar o seguinte comando para exportar os dados de faturação:

```console
azdata arc dc export -t usage -p usage.json
```

Por enquanto, o ficheiro não está encriptado para que possa ver o conteúdo. Sinta-se livre para abrir num editor de texto e ver como são os conteúdos.

Notará que existem dois conjuntos de dados: `resources` e `data` . São `resources` o controlador de dados, os grupos de servidores de hiperescala PostgreSQL e as instâncias geridas sql. Os `resources` registos nos dados captam os acontecimentos pertinentes na história de um recurso - quando foi criado, quando foi atualizado, e quando foi eliminado. Os `data` registos captam quantos núcleos estavam disponíveis para serem usados por um dado exemplo para cada hora.

Exemplo de uma `resource` entrada:

```console
    {
        "customObjectName": "<resource type>-2020-29-5-23-13-17-164711",
        "uid": "4bc3dc6b-9148-4c7a-b7dc-01afc1ef5373",
        "instanceName": "sqlInstance001",
        "instanceNamespace": "arc",
        "instanceType": "<resource>",
        "location": "eastus",
        "resourceGroupName": "production-resources",
        "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
        "isDeleted": false,
        "externalEndpoint": "32.191.39.83:1433",
        "vCores": "2",
        "createTimestamp": "05/29/2020 23:13:17",
        "updateTimestamp": "05/29/2020 23:13:17"
    }
```

Exemplo de uma `data` entrada:

```console
        {
          "requestType": "usageUpload",
          "clusterId": "4b0917dd-e003-480e-ae74-1a8bb5e36b5d",
          "name": "DataControllerTestName",
          "subscriptionId": "482c901a-129a-4f5d-86e3-cc6b294590b2",
          "resourceGroup": "production-resources",
          "location": "eastus",
          "uploadRequest": {
            "exportType": "usages",
            "dataTimestamp": "2020-06-17T22:32:24Z",
            "data": "[{\"name\":\"sqlInstance001\",
                       \"namespace\":\"arc\",
                       \"type\":\"<resource type>\",
                       \"eventSequence\":1, 
                       \"eventId\":\"50DF90E8-FC2C-4BBF-B245-CB20DC97FF24\",
                       \"startTime\":\"2020-06-17T19:11:47.7533333\",
                       \"endTime\":\"2020-06-17T19:59:00\",
                       \"quantity\":1,
                       \"id\":\"4BC3DC6B-9148-4C7A-B7DC-01AFC1EF5373\"}]",
           "signature":"MIIE7gYJKoZIhvcNAQ...2xXqkK"
          }
        }
```

Executar o seguinte comando para carregar o usage.jsno ficheiro para Azure:

```console
azdata arc dc upload -p usage.json
```

## <a name="view-billing-data-in-azure-portal"></a>Ver dados de faturação no portal Azure

Siga estes passos para ver os dados de faturação no portal Azure:

1. Abra o portal Azure utilizando o URL especial:  [https://aka.ms/arcdata](https://aka.ms/arcdata) .
1. Na caixa de pesquisa no topo do tipo de ecrã em Gestão de **Custos** e clique no serviço de Gestão de Custos.
1. Clique no separador **de análise de custos** à esquerda.
1. Clique no botão **Custo por recurso** na parte superior da vista.
1. Certifique-se de que o seu Âmbito está definido para a subscrição na qual foram criados os seus recursos de serviço de dados.
1. Selecione **Custo por recurso** na versão gota para baixo ao lado do seletor De âmbito perto da parte superior da vista.
1. Certifique-se de que o filtro de data está definido para **este mês** ou em algum outro intervalo de tempo que faça sentido dado o momento de quando criou os seus recursos de serviço de dados.
1. Clique **em Adicionar filtro** para adicionar um filtro por tipo de **recurso** se  =  `microsoft.azuredata/<data service type>` pretender filtrar para baixo para apenas um tipo de serviço de dados ativado pelo Arco Azure.
1. Agora verá uma lista de todos os recursos que foram criados e enviados para Azure. Como o medidor de faturação é $0, verá que o custo é sempre $0.

## <a name="download-billing-data"></a>Baixar dados de faturação

Pode baixar os dados do resumo da faturação diretamente do portal Azure.

1. Na mesma **análise de Custo -> vista por** visão do tipo de recurso que alcançou seguindo as instruções acima, clique no botão Descarregar perto da parte superior.
1. Escolha o seu tipo de ficheiro de descarregamento - Excel ou CSV - e clique no botão **de dados de Descarregamento.**
1. Abra o ficheiro num editor apropriado, dado o tipo de ficheiro selecionado.

## <a name="export-billing-data"></a>Dados de faturação de exportação

Também pode exportar periodicamente, automaticamente, dados de utilização e faturação **detalhados** para um contentor de armazenamento Azure, criando um trabalho de exportação de faturação. Isto é útil se você quiser ver os detalhes da sua faturação, como quantas horas uma determinada instância foi faturada no período de faturação.

Siga estes passos para criar um trabalho de exportação de faturação:

1. Clique em **Exportações** à esquerda.
1. Clique em **Adicionar**.
1. Insira um nome e frequência de exportação e clique em Seguinte.
1. Opte por criar uma nova conta de armazenamento ou utilize uma existente e preencha o formulário para especificar a conta de armazenamento, o contentor e o caminho do diretório para exportar os ficheiros de dados de faturação para e clicar em Seguinte.
1. Clique em **Criar**.

Os ficheiros de exportação de dados de faturação estarão disponíveis em aproximadamente 4 horas e serão exportados no horário especificado ao criar o trabalho de exportação de faturação.

Siga estes passos para ver os ficheiros de dados de faturação que são exportados:

Pode validar os ficheiros de dados de faturação no portal Azure. 

> [!IMPORTANT]
> Depois de criar o trabalho de exportação de faturação, aguarde 4 horas antes de prosseguir com os seguintes passos.

1. Na caixa de pesquisa no topo do portal, digite as **contas de Armazenamento** e clique nas Contas de **Armazenamento.**
3. Clique na conta de armazenamento que especificou ao criar o trabalho de exportação de faturação acima.
4. Clique em Recipientes à esquerda.
5. Clique no recipiente especificado ao criar o trabalho de exportação de faturação acima.
6. Clique na pasta especificada ao criar o trabalho de exportação de faturação acima.
7. Desabraça as pastas e ficheiros gerados e clique num dos ficheiros .csv gerados.
8. Clique no botão **Descarregar** que irá guardar o ficheiro para a pasta Downloads local.
9. Abra o ficheiro utilizando um visualizador de ficheiros .csv, como o Excel.
10. Filtrar os resultados para mostrar apenas as linhas com o **Tipo de Recurso**  =  `Microsoft.AzureData/<data service resource type` .
11. Verá o número de horas em que o caso foi utilizado no atual período de 24 horas na coluna UsageQuantity.
