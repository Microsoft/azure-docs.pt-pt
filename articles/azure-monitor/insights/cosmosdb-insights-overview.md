---
title: Monitorar Azure Cosmos DB com Azure Monitor para Cosmos DB (versão prévia) | Microsoft Docs
description: Este artigo descreve o Azure Monitor para Cosmos DB recurso que fornece aos Cosmos DB proprietários uma rápida compreensão dos problemas de desempenho e utilização com suas contas do CosmosDB.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/27/2019
ms.openlocfilehash: 8e265b592bebfc506ae0116c955403dd1070ad3f
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73166411"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db-preview"></a>Explorar Azure Monitor para Azure Cosmos DB (versão prévia)

Azure Monitor para Azure Cosmos DB (versão prévia) fornece uma visão do desempenho geral, falhas, capacidade e integridade operacional de todos os seus Azure Cosmos DB recursos em uma experiência interativa unificada. Este artigo o ajudará a entender os benefícios dessa nova experiência de monitoramento e como você pode modificar e adaptar a experiência para atender às necessidades exclusivas de sua organização.   

## <a name="introduction"></a>Introdução

Antes de mergulhar na experiência, você deve entender como ele apresenta e visualiza informações. 

Ele fornece:

* **Na perspectiva da escala** de seus recursos de Azure Cosmos DB em todas as suas assinaturas em um único local, com a capacidade de fazer o escopo seletivo somente para as assinaturas e os recursos que você está interessado em avaliar.

* **Análise de busca detalhada** de um recurso específico do Azure CosmosDB para ajudar a diagnosticar problemas ou executar uma análise detalhada por categoria-utilização, falhas, capacidade e operações. A seleção de qualquer uma dessas opções fornece uma visão detalhada das métricas de Azure Cosmos DB relevantes.  

* **Personalizável** – essa experiência é criada sobre Azure monitor modelos de pasta de trabalho, permitindo que você altere quais métricas são exibidas, modifique ou defina os limites que se alinham com seus limites e, em seguida, salve em uma pasta de trabalho personalizada. Os gráficos nas pastas de trabalho podem ser fixados nos painéis do Azure.  

Esse recurso não exige que você habilite ou configure nada, essas métricas de Azure Cosmos DB são coletadas por padrão.

>[!NOTE]
>Não há nenhum encargo para acessar esse recurso e você só será cobrado pelos recursos essenciais Azure Monitor que você configurar ou habilitar, conforme descrito na página de [detalhes de preços de Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) .


## <a name="accessing-azure-monitor-for-azure-cosmos-db"></a>Acessando Azure Monitor para Azure Cosmos DB

Para exibir a utilização e o desempenho de suas contas de armazenamento em todas as suas assinaturas, execute as etapas a seguir.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Procure **Monitor** e selecione **Monitor**.

    ![Caixa de pesquisa com a palavra "Monitor" e uma lista suspensa que diz aos serviços "Monitor" com uma imagem de estilo velocímetro](./media/cosmosdb-insights-overview/search-monitor.png)

3. Selecione **Cosmos dB (versão prévia)** .

    ![Captura de tela da pasta de trabalho de visão geral Cosmos DB](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>Visão geral

Em **visão geral**, a tabela exibe métricas de Azure Cosmos DB interativas. Você pode filtrar os resultados com base nas opções selecionadas nas seguintes listas suspensas:

* **Assinaturas-somente** as assinaturas que têm um recurso Azure Cosmos DB são listadas.  

* **Cosmos DB** -você pode selecionar todos, um subconjunto ou recurso de Azure Cosmos DB único.

* **Intervalo de tempo** – por padrão, exibe as últimas 4 horas de informações com base nas seleções correspondentes feitas.

O bloco contador sob as listas suspensas acumula o número total de Azure Cosmos DB recursos estão nas assinaturas selecionadas. Há codificação de cor condicional ou calor para colunas na pasta de trabalho que relatam métricas de transação. A cor mais profunda tem o valor mais alto e uma cor mais clara é baseada nos valores mais baixos. 

A seleção de uma seta suspensa ao lado de um dos Azure Cosmos DB recursos revelará uma análise das métricas de desempenho no nível de contêiner do banco de dados individual:

![Lista suspensa expandida revelando contêineres de banco de dados individuais e divisão de desempenho associada](./media/cosmosdb-insights-overview/container-view.png)

Selecionar o nome do recurso de Azure Cosmos DB realçado em azul levará você à **visão geral** padrão para a conta de Azure Cosmos DB associada. 

### <a name="failures"></a>Falhas

Selecione **falhas** na parte superior da página e a parte de **falhas** do modelo da pasta de trabalho é aberta. Ele mostra o total de solicitações com a distribuição de respostas que compõem essas solicitações:

![Captura de tela de falhas com divisão por tipo de solicitação HTTP](./media/cosmosdb-insights-overview/failures.png)

| Código      |  Descrição       | 
|-----------|:--------------------|
| `200 OK`  | Uma das seguintes operações REST foi bem-sucedida: </br>-OBTER em um recurso. </br> -PUT em um recurso. </br> -POST em um recurso. </br> -POST em um recurso de procedimento armazenado para executar o procedimento armazenado.|
| `201 Created` | Uma operação POST para criar um recurso foi bem-sucedida. |
| `404 Not Found` | A operação está tentando agir em um recurso que não existe mais. Por exemplo, o recurso pode já ter sido excluído. |

Para obter uma lista completa de códigos de status, consulte o [artigo Azure Cosmos DB código de status http](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

### <a name="capacity"></a>Capacidade

Selecione a **capacidade** na parte superior da página e a parte da **capacidade** do modelo da pasta de trabalho será aberta. Ele mostra quantos documentos você tem, o crescimento do documento ao longo do tempo, o uso de dados e a quantidade total de armazenamento disponível que você deixou.  Isso pode ser usado para ajudar a identificar possíveis problemas de armazenamento e de utilização de dados.

![Pasta de trabalho de capacidade](./media/cosmosdb-insights-overview/capacity.png) 

Assim como acontece com a pasta de trabalho Visão geral, a seleção da lista suspensa ao lado de um recurso de Azure Cosmos DB na coluna **assinatura** revelará uma divisão pelos contêineres individuais que compõem o banco de dados.

### <a name="operations"></a>Operations 

Selecione **operações** na parte superior da página e a parte de **operações** do modelo da pasta de trabalho será aberta. Ele oferece a capacidade de ver suas solicitações divididas pelo tipo de solicitações feitas. 

Portanto, no exemplo abaixo, você verá que `eastus-billingint` está predominantemente recebendo solicitações de leitura, mas com um pequeno número de solicitações de Upsert e de criação. Enquanto `westeurope-billingint` é somente leitura de uma perspectiva de solicitação, pelo menos nas últimas quatro horas em que a pasta de trabalho está atualmente com escopo por meio de seu parâmetro de intervalo de tempo.

![Pasta de trabalho de operações](./media/cosmosdb-insights-overview/operation.png) 

## <a name="pin-export-and-expand"></a>Fixar, exportar e expandir

Você pode fixar qualquer uma das seções de métrica em um [painel do Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) selecionando o ícone de pino na parte superior direita da seção.

![Exemplo de PIN da seção de métrica para painel](./media/cosmosdb-insights-overview/pin.png)

Para exportar os dados para o formato do Excel, selecione o ícone de seta para baixo à esquerda do ícone de pino.

![Ícone exportar pasta de trabalho](./media/cosmosdb-insights-overview/export.png)

Para expandir ou recolher todas as exibições suspensas na pasta de trabalho, selecione o ícone de expansão à esquerda do ícone de exportação:

![Ícone expandir pasta de trabalho](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db-preview"></a>Personalizar Azure Monitor para Azure Cosmos DB (versão prévia)

Como essa experiência é criada sobre os modelos de pasta de trabalho Azure Monitor, você tem a capacidade de **personalizar** > **Editar** e **salvar** uma cópia da sua versão modificada em uma pasta de trabalho personalizada. 

![Personalizar barra](./media/cosmosdb-insights-overview/customize.png)

As pastas de trabalho são salvas em um grupo de recursos, na seção **meus relatórios** , que é particular para você ou na seção **relatórios compartilhados** , que é acessível a todos com acesso ao grupo de recursos. Depois de salvar a pasta de trabalho personalizada, você precisa ir para a Galeria de pastas de trabalho para iniciá-la.

![Iniciar Galeria de pastas de trabalho na barra de comandos](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>Passos seguintes

* Configure [alertas de métrica](../platform/alerts-metric.md) e [notificações de integridade do serviço](../../service-health/alerts-activity-log-service-notifications.md) para configurar alertas automatizados para auxiliar na detecção de problemas.

* Conheça os cenários em que as pastas de trabalho foram projetadas para dar suporte, como criar novos e personalizar relatórios existentes e muito mais examinando [criar relatórios interativos com pastas de trabalho do Azure monitor](../app/usage-workbooks.md).
