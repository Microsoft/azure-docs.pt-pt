---
title: Criar um serviço Azure Search no portal - Azure Search
description: Aprovisione um recurso do Azure Search no portal do Azure. Escolha os grupos de recursos, regiões e SKU ou escalão de preço.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 07/09/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: d0d1dbb81f00f500f3eb95c605ed0c15c634f624
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706786"
---
# <a name="create-an-azure-search-service-in-the-portal"></a>Criar um serviço do Azure Search no portal

O Azure Search é um recurso de autónomo utilizado para serem incorporados numa experiência de pesquisa em aplicações personalizadas. Embora o Azure Search integra-se facilmente com outros serviços do Azure, também pode utilizá-lo como um componente autônomo ou integrá-lo com as aplicações em servidores de rede, ou com o software em execução noutras plataformas na cloud.

Neste artigo, saiba como criar um recurso do Azure Search no [portal do Azure](https://portal.azure.com/).

[![GIF animado](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

Prefere o PowerShell? Utilize o [modelo de serviço](https://azure.microsoft.com/resources/templates/101-azure-search-create/) do Azure Resource Manager. Para obter ajuda com a introdução, consulte [gerir o Azure Search com o PowerShell](search-manage-powershell.md).

## <a name="subscribe-free-or-paid"></a>Subscrever (gratuito ou pago)

[Abra uma conta do Azure gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) e utilize créditos gratuitos para experimentar serviços do Azure pagos. Depois de gastar os créditos, mantenha a conta e continue a utilizar os serviços do Azure gratuitos, como Web sites. O seu cartão de crédito não será cobrado, a menos que altere explicitamente as suas definições e peça para ser cobrado.

Em alternativa, [ative os benefícios do subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Uma subscrição do MSDN dá-lhe créditos todos os meses que pode utilizar em serviços pagos do Azure. 

## <a name="find-azure-search"></a>Localizar o Azure Search

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Clique no sinal ("+ Criar recurso") no canto superior esquerdo.
3. Utilize a barra de pesquisa para encontrar "Azure Search" ou navegue para o recurso através de **Web** > **Azure Search**.

![Navegue para um recurso do Azure Search](./media/search-create-service-portal/find-search3.png "caminho de navegação para o Azure Search")

## <a name="name-the-service-and-url-endpoint"></a>Atribuir um nome ao serviço e ao ponto final do URL

Um nome de serviço faz parte do ponto final do URL no qual são emitidas as chamadas de API: `https://your-service-name.search.windows.net`. Introduza o nome do serviço no campo **URL**.

Por exemplo, se pretender que o ponto final a ser `https://my-app-name-01.search.windows.net`, deve introduzir `my-app-name-01`.

Requisitos do nome do serviço:

* Tem de ser exclusivo no espaço de nomes search.windows.net
* Ter 2 e 60 carateres
* Utilize letras minúsculas, números ou hífenes ("-")
* Evite hífenes ("-") nos primeiro 2 carateres ou como último caráter único
* Sem hífenes consecutivos ("-") em qualquer parte

## <a name="select-a-subscription"></a>Selecionar uma subscrição

Se tiver mais do que uma subscrição, escolha uma que tenha também serviços de armazenamento de dados ou ficheiros. O Azure Search pode deteção automática de tabelas do Azure e BLOBs de armazenamento, base de dados SQL e do Azure Cosmos DB para indexação através de [ *indexadores*](search-indexer-overview.md), mas apenas para serviços na mesma subscrição.

## <a name="select-a-resource-group"></a>Selecionar um grupo de recursos

Um grupo de recursos é uma coleção de serviços e recursos do Azure utilizados em conjunto. Por exemplo, se estiver a utilizar o Azure Search para indexar uma base de dados SQL, ambos os serviços devem fazer parte do mesmo grupo de recursos.

Se não é a combinação de recursos num único grupo, ou se os grupos de recursos existentes são repletos de recursos utilizados em soluções não relacionadas, crie um novo grupo de recursos apenas para o seu recurso do Azure Search.

> [!TIP]
> A eliminação de um grupo de recursos também elimina os serviços no mesmo. Para projetos de protótipo que utilizam múltiplos serviços, colocá-los a todos no mesmo grupo de recursos facilita a limpeza depois de o projeto terminar.

## <a name="select-a-location"></a>Selecione uma localização

Como um serviço do Azure, o Azure Search pode ser alojado em datacenters em todo o mundo. A lista de regiões suportadas pode ser encontrada no [página de preços](https://azure.microsoft.com/pricing/details/search/). 

Se estiver indexando dados fornecidos pelo Azure outro serviço (o armazenamento do Azure, Azure Cosmos DB, base de dados do Azure SQL), recomendamos que crie o seu serviço de pesquisa do Azure na mesma região para evitar custos de largura de banda. Não existem custos para dados de saída quando os serviços estão na mesma região.

Se estiver a utilizar o possível de ia de pesquisa cognitiva, crie o seu serviço na mesma região que o recurso dos serviços cognitivos. *Localização conjunta de Azure Search e os serviços cognitivos na mesma região é um requisito para melhoria do IA*.

> [!Note]
> Índia central não está atualmente disponível para os novos serviços. Para os serviços já na Índia Central, pode aumentar verticalmente sem restrições e seu serviço é totalmente suportado nessa região. A restrição nesta região é temporário e limitado a apenas novos serviços. Vamos remover esta nota quando já não se aplica a restrição.

## <a name="select-a-pricing-tier-sku"></a>Selecionar um escalão de preço (SKU)

[O Azure Search está disponível em vários escalões de preços](https://azure.microsoft.com/pricing/details/search/): Gratuito, básico ou Standard. Cada escalão tem a sua própria [capacidade e limites](search-limits-quotas-capacity.md). Veja [Escolher um escalão de preço ou SKU](search-sku-tier.md) para obter orientações.

Normalmente, o escalão Standard é escolhido para cargas de trabalho de produção, embora a maioria dos clientes comece com o serviço Gratuito.

Um escalão de preço não pode ser alterado após a criação do serviço. Se precisar de um escalão superior ou inferior mais tarde, terá de voltar a criar o serviço.

## <a name="create-your-service"></a>Criar o serviço

Introduza as entradas necessárias para criar o serviço. 

![Reveja e criar o serviço](./media/search-create-service-portal/new-service3.png "revisão e criar o serviço")

O serviço é implementado numa questão de minutos, o que pode monitorar por meio de notificações do Azure. Considere a afixar o serviço ao seu dashboard para facilitar o acesso no futuro.

![Monitorizar e afixar o serviço](./media/search-create-service-portal/monitor-notifications.png "Monitor e o serviço de pin")

## <a name="get-a-key-and-url-endpoint"></a>Obter uma chave e o ponto final do URL

A menos que estiver a utilizar o portal, aceder ao seu novo serviço requer que forneça o ponto de final do URL e uma chave de api de autenticação.

1. Na página de descrição geral do serviço, localize e copie o ponto final do URL no lado direito da página.

2. No painel de navegação esquerdo, selecione **chaves** e, em seguida, copie uma das chaves de administração (ambos são equivalentes). Chaves de api são necessárias para criar, atualizar e eliminar objetos no seu serviço.

   ![Página de descrição geral do serviço com o ponto final do URL](./media/search-create-service-portal/get-url-key.png "ponto final do URL e outros detalhes de serviço")

Um ponto final e a chave não são necessários para tarefas com base no portal. O portal já está ligado ao seu recurso do Azure Search com direitos de administrador. Para obter instruções portal, comece com [início rápido: Criar um índice da Azure Search no portal do](search-get-started-portal.md).

## <a name="scale-your-service"></a>Dimensionar o serviço

Depois de o serviço ser aprovisionado, pode dimensioná-lo para satisfazer as suas necessidades. Se tiver escolhido o escalão Standard para o serviço Azure Search, pode dimensionar o serviço em duas dimensões: réplicas e partições. Se tiver escolhido o escalão Básico, apenas pode adicionar réplicas. Se tiver aprovisionado o serviço gratuito, o dimensionamento não está disponível.

As ***partições*** permitem ao serviço armazenar e pesquisar mais documentos.

As ***réplicas*** permitem ao serviço processar uma carga maior de consultas de pesquisa.

A adição de recursos aumenta a sua fatura mensal. A [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) pode ajudá-lo a compreender as ramificações de faturação da adição de recursos. Lembre-se de que pode ajustar os recursos com base na carga. Por exemplo, pode aumentar os recursos para criar um índice inicial completo e, em seguida, reduzir recursos mais tarde para um nível mais adequado para a indexação incremental.

> [!Important]
> Um serviço tem de ter [2 réplicas para SLA só de leitura e 3 réplicas para SLA de leitura/escrita](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Aceda à página do serviço de pesquisa no portal do Azure.
2. No painel de navegação esquerdo, selecione **Definições** > **Dimensionar**.
3. Utilize a barra de deslize para adicionar recursos de qualquer tipo.

![Adicionar capacidade](./media/search-create-service-portal/settings-scale.png "adicionar capacidade através de réplicas e partições")

> [!Note]
> Por partição aumentos de armazenamento e a velocidade em escalões superiores. Para obter mais informações, consulte [capacidade e limites](search-limits-quotas-capacity.md).

## <a name="when-to-add-a-second-service"></a>Quando adicionar um segundo serviço

A maioria dos clientes utilize apenas um serviço aprovisionado num escalão fornecer a [equilíbrio certo de recursos](search-sku-tier.md). Um serviço pode alojar vários índices, sujeitos aos [limites máximos do escalão que selecionar](search-capacity-planning.md), com os índices isolados uns dos outros. No Azure Search, os pedidos só podem ser direcionados para um índice, o que minimiza a possibilidade de obtenção de dados acidental ou intencional a partir de outros índices no mesmo serviço.

Embora a maioria dos clientes utilize apenas um serviço, a redundância de serviços pode ser necessária se os requisitos operacionais incluírem o seguinte:

* Recuperação após desastre (falha do datacenter). O Azure Search não fornece ativação pós-falha instantânea na eventualidade de indisponibilidade. Para obter recomendações e orientações, veja [Administração de serviços](search-manage.md).
* A investigação de modelação de multi-inquilinos determinou que a conceção ideal é ter serviços adicionais. Para obter mais informações, veja [Conceber para multi-inquilinos](search-modeling-multitenant-saas-applications.md).
* Para aplicações implementadas globalmente, pode necessitar de uma instância do Azure Search em várias regiões para minimizar a latência do tráfego internacional da sua aplicação.

> [!NOTE]
> No Azure Search, não pode segregar cargas de trabalho de indexação e consulta; assim, nunca criará vários serviços para cargas de trabalho segregadas. Um índice é sempre consultado no serviço em que foi criado (não pode criar um índice num serviço e copiá-lo para outro).

Não é necessário um segundo serviço para elevada disponibilidade. A elevada disponibilidade para consultas é alcançada ao utilizar 2 ou mais réplicas no mesmo serviço. As atualizações de réplicas são sequenciais, o que significa que pelo menos uma está operacional quando é implementada uma atualização de serviço. Para obter mais informações sobre o tempo de atividade, veja [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Passos Seguintes

Depois de aprovisionar um serviço Azure Search, pode continuar no portal para criar seu primeiro índice.

> [!div class="nextstepaction"]
> [Quickstart: Criar um índice da Azure Search no portal](search-get-started-portal.md)
