---
title: Criar um serviço Azure Search no portal - Azure Search
description: Aprovisione um recurso do Azure Search no portal do Azure. Escolha os grupos de recursos, regiões e SKU ou escalão de preço.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 04/05/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: c48acf7e9074ac3c5a7d19765a9524a411fa26c8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59264034"
---
# <a name="create-an-azure-search-service-in-the-portal"></a>Criar um serviço do Azure Search no portal

O Azure Search é um recurso de autónomo utilizado para serem incorporados numa experiência de pesquisa em aplicações personalizadas. Embora o Azure Search integra-se facilmente com outros serviços do Azure, também pode utilizá-lo por si só, com as aplicações em servidores de rede, ou com o software em execução noutras plataformas na cloud.

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

## <a name="select-a-hosting-location"></a>Selecionar uma localização de alojamento

Como um serviço do Azure, o Azure Search pode ser alojado em datacenters em todo o mundo. [Os preços podem ser diferentes](https://azure.microsoft.com/pricing/details/search/) por geografia.

Se estiver indexando um conteúdo localizado no Azure service (o armazenamento do Azure, Azure Cosmos DB, base de dados do Azure SQL), crie o seu serviço de pesquisa do Azure na mesma região que seus dados para evitar custos de largura de banda. Não existem custos para dados de saída quando os serviços estão na mesma região.

## <a name="select-a-pricing-tier-sku"></a>Selecionar um escalão de preço (SKU)

[O Azure Search está disponível em vários escalões de preços](https://azure.microsoft.com/pricing/details/search/): Gratuito, básico ou Standard. Cada escalão tem a sua própria [capacidade e limites](search-limits-quotas-capacity.md). Veja [Escolher um escalão de preço ou SKU](search-sku-tier.md) para obter orientações.

Normalmente, o escalão Standard é escolhido para cargas de trabalho de produção, embora a maioria dos clientes comece com o serviço Gratuito.

Um escalão de preço não pode ser alterado após a criação do serviço. Se precisar de um escalão superior ou inferior mais tarde, terá de voltar a criar o serviço.

## <a name="create-your-service"></a>Criar o serviço

Não se esqueça de afixar o serviço ao dashboard para facilitar o acesso sempre que iniciar sessão.

![Afixar ao dashboard](./media/search-create-service-portal/new-service3.png "afixar o recurso ao seu dashboard para acesso conveniente")

## <a name="get-a-key-and-url-endpoint"></a>Obter uma chave e o ponto final do URL

Com algumas exceções, a utilização de seu novo serviço requer que forneça o ponto de final do URL e uma chave de api de autorização. Inícios rápidos, tutoriais, tal como [Explore pesquisa APIs REST do Azure (Postman)](search-fiddler.md) e [como utilizar o Azure Search no .NET](search-howto-dotnet-sdk.md), exemplos e código personalizado, tudo o precisa de um ponto final e a chave para ser executado no seu recurso em particular.

1. Na página de descrição geral do serviço, localize e copie o ponto final do URL no lado direito da página.

   ![Página de descrição geral do serviço com o ponto final do URL](./media/search-create-service-portal/url-endpoint.png "ponto final do URL e outros detalhes de serviço")

2. No painel de navegação esquerdo, selecione **chaves** e, em seguida, copie uma das chaves de administração (ambos são equivalentes). Chaves de api são necessárias para criar, atualizar e eliminar objetos no seu serviço.

   ![Chaves de página que mostra a chaves de primárias e secundárias](./media/search-create-service-portal/admin-api-keys.png "chaves de api para autorização")

Um ponto final e a chave não são necessários para tarefas com base no portal. O portal já está ligado ao seu recurso do Azure Search com direitos de administrador. Para um tutorial do portal, comece com [Tutorial: Importar, índice e a consulta no Azure Search](search-get-started-portal.md).

## <a name="scale-your-service"></a>Dimensionar o serviço

Pode demorar alguns minutos a criar um serviço (15 minutos ou mais consoante o escalão). Depois de o serviço ser aprovisionado, pode dimensioná-lo para satisfazer as suas necessidades. Uma vez que escolheu o escalão Standard para o seu serviço do Azure Search, pode dimensionar o serviço em duas dimensões: réplicas e partições. Se tiver escolhido o escalão Básico, apenas pode adicionar réplicas. Se tiver aprovisionado o serviço gratuito, o dimensionamento não está disponível.

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
> Cada escalão tem diferentes [limites](search-limits-quotas-capacity.md) para o número total de Unidades de Pesquisa permitido num único serviço (Réplicas * Partições = Total de Unidades de Pesquisa).

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
> [Tutorial: Importar dados, o índice e executar consultas no portal](search-get-started-portal.md)
