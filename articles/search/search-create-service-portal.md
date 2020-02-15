---
title: 'Quickstart: Criar um serviço de pesquisa no portal'
titleSuffix: Azure Cognitive Search
description: Neste portal, aprenda a criar um recurso azure de pesquisa cognitiva no portal Azure. Escolha grupos de recursos, regiões e SKU ou nível de preços.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/10/2020
ms.openlocfilehash: 3bc3edcd0e75d8f6e3e4d6f9b200032909318040
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209363"
---
# <a name="quickstart-create-an-azure-cognitive-search-service-in-the-portal"></a>Quickstart: Criar um serviço de Pesquisa Cognitiva Azure no portal

A Azure Cognitive Search é um recurso autónomo usado para ligar uma experiência de pesquisa a aplicações personalizadas. A Azure Cognitive Search integra-se facilmente com outros serviços Azure, com aplicações em servidores de rede, ou com software a funcionar noutras plataformas da nuvem.

Neste artigo, aprenda a criar um recurso no [portal Azure.](https://portal.azure.com/)

[GIF animado ![](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

Prefere o PowerShell? Utilize o [modelo de serviço](https://azure.microsoft.com/resources/templates/101-azure-search-create/) do Azure Resource Manager. Para obter ajuda para começar, consulte [Manage Azure Cognitive Search com PowerShell](search-manage-powershell.md).

## <a name="subscribe-free-or-paid"></a>Subscrever (gratuito ou pago)

[Abra uma conta do Azure gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) e utilize créditos gratuitos para experimentar serviços do Azure pagos. Depois de gastar os créditos, mantenha a conta e continue a utilizar os serviços do Azure gratuitos, como Web sites. O seu cartão de crédito não será cobrado, a menos que altere explicitamente as suas definições e peça para ser cobrado.

Em alternativa, [ative os benefícios do subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Uma subscrição do MSDN dá-lhe créditos todos os meses que pode utilizar em serviços pagos do Azure. 

## <a name="find-azure-cognitive-search"></a>Encontre pesquisa cognitiva azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).
2. Clique no sinal de mais ("+ Criar Recurso") no canto superior esquerdo.
3. Utilize a barra de pesquisa para encontrar "Azure Cognitive Search" ou navegue para o recurso através da **Web** > **Azure Cognitive Search**.

![Criar um recurso no portal](./media/search-create-service-portal/find-search3.png "Criar um recurso no portal")

## <a name="choose-a-subscription"></a>Escolha uma subscrição

Se tiver mais de uma subscrição, escolha uma para o seu serviço de pesquisa.

## <a name="set-a-resource-group"></a>Definir um grupo de recursos

Um grupo de recursos é um recipiente que detém recursos relacionados para a sua solução Azure. É necessário para o serviço de pesquisa. Também é útil para gerir todos os recursos, incluindo os custos. Um grupo de recursos pode consistir num serviço, ou em vários serviços utilizados em conjunto. Por exemplo, se estiver a usar a Pesquisa Cognitiva Azure para indexar uma base de dados Azure Cosmos DB, poderá fazer de ambos os serviços parte do mesmo grupo de recursos para fins de gestão. 

Se não estiver a combinar recursos num único grupo, ou se os grupos de recursos existentes estiverem cheios de recursos utilizados em soluções não relacionadas, crie um novo grupo de recursos apenas para o seu recurso de Pesquisa Cognitiva Azure. 

![Criar um novo grupo de recursos](./media/search-create-service-portal/new-resource-group.png "Criar um novo grupo de recursos")

Com o tempo, pode rastrear os custos atuais e projetados ou pode ver os encargos com recursos individuais. A imagem que se segue mostra o tipo de informação de custos que pode esperar ver quando combina vários recursos num só grupo.

![Gerir custos ao nível do grupo de recursos](./media/search-create-service-portal/resource-group-cost-management.png "Gerir custos ao nível do grupo de recursos")

> [!TIP]
> Os grupos de recursos simplificam a limpeza porque eliminar um grupo elimina todos os serviços que lhe são atribuídos. Para projetos de protótipo que utilizam múltiplos serviços, colocá-los a todos no mesmo grupo de recursos facilita a limpeza depois de o projeto terminar.

## <a name="name-the-service"></a>Nomeie o serviço

Em detalhes, forneça um nome de serviço no campo **URL.** O nome faz parte do ponto final do URL contra o qual são emitidas chamadas API: `https://your-service-name.search.windows.net`. Por exemplo, se quiser que o ponto final seja `https://myservice.search.windows.net`, entraria `myservice`.

Requisitos do nome do serviço:

* Tem de ser exclusivo no espaço de nomes search.windows.net
* Deve ter entre 2 e 60 caracteres de comprimento
* Deve utilizar letras minúsculas, dígitos ou traços ("-")
* Não utilize traços ("-") nos primeiros 2 caracteres ou como último personagem único
* Você pode não usar traços consecutivos ("--") em qualquer lugar

> [!TIP]
> Se pensa que vai utilizar vários serviços, recomendamos que inclua a região (ou localização) no nome do serviço como convenção de nomeação. Os serviços dentro da mesma região podem trocar dados gratuitamente, por isso, se a Pesquisa Cognitiva Azure estiver no Oeste dos EUA, e tiver outros serviços também nos EUA Ocidentais, um nome como `mysearchservice-westus` pode poupar-lhe uma viagem à página de propriedades ao decidir como combinar ou anexar recursos.

## <a name="choose-a-location"></a>Escolha um local

Como serviço Azure, a Azure Cognitive Search pode ser hospedada em datacenters em todo o mundo. A lista de regiões apoiadas pode ser consultada na página de [preços](https://azure.microsoft.com/pricing/details/search/). 

Pode minimizar ou evitar taxas de largura de banda, escolhendo o mesmo local para vários serviços. Por exemplo, se estiver a indexar dados fornecidos por outro serviço Azure (armazenamento Azure, Azure Cosmos DB, Base de Dados Azure SQL), criar o seu serviço de Pesquisa Cognitiva Azure na mesma região evita taxas de largura de banda (não existem encargos para dados de saída quando os serviços estão na mesma região).

Se estiver a usar o enriquecimento de IA, crie o seu serviço de pesquisa na mesma região que os Serviços Cognitivos. *A co-localização dos Serviços cognitivos de pesquisa e cognitivo seletiva azure na mesma região é um requisito para o enriquecimento de IA.*

> [!Note]
> A Índia Central está atualmente indisponível para novos serviços. Para serviços já na Índia Central, você pode escalar sem restrições, e seu serviço é totalmente suportado nessa região. A restrição nesta região é temporária e limitada apenas a novos serviços. Removeremos esta nota quando a restrição deixar de se aplicar.

## <a name="choose-a-pricing-tier-sku"></a>Escolha um nível de preços (SKU)

A Pesquisa [Cognitiva Azure é atualmente oferecida em vários níveis](https://azure.microsoft.com/pricing/details/search/)de preços : Grátis, Básico ou Standard. Cada escalão tem a sua própria [capacidade e limites](search-limits-quotas-capacity.md). Veja [Escolher um escalão de preço ou SKU](search-sku-tier.md) para obter orientações.

Basic e Standard são as escolhas mais comuns para cargas de trabalho de produção, mas a maioria dos clientes começa com o serviço Free. As principais diferenças entre os níveis são o tamanho e a velocidade da divisória, e limites no número de objetos que pode criar.

Lembre-se, um nível de preços não pode ser alterado uma vez que o serviço é criado. Se precisar de um nível mais alto ou inferior, terá de recriar o serviço.

## <a name="create-your-service"></a>Criar o serviço

Depois de fornecer as inputs necessárias, vá em frente e crie o serviço. 

![Reveja e crie o serviço](./media/search-create-service-portal/new-service3.png "Reveja e crie o serviço")

O seu serviço está implantado dentro de minutos. Pode monitorizar o progresso através de notificações do Azure. Considere fixar o serviço no seu painel de instrumentos para facilitar o acesso no futuro.

![Monitore e pin o serviço](./media/search-create-service-portal/monitor-notifications.png "Monitore e pin o serviço")

## <a name="get-a-key-and-url-endpoint"></a>Obtenha uma chave e ponto final de URL

A menos que esteja a utilizar o portal, o acesso programático ao seu novo serviço requer que forneça o ponto final do URL e uma chave api de autenticação.

1. Na página **'Overview',** localize e copie o ponto final do URL no lado direito da página.

2. Na página **Keys,** copie qualquer uma das teclas de administração (são equivalentes). As chaves api-chave de administrador são necessárias para criar, atualizar e apagar objetos no seu serviço. Em contraste, as teclas de consulta fornecem acesso de leitura ao conteúdo do índice.

   ![Página de visão geral do serviço com ponto final de URL](./media/search-create-service-portal/get-url-key.png "Finalpoint URL e outros detalhes do serviço")

Não são necessários um ponto final e uma chave para tarefas baseadas em portal. O portal já está ligado ao seu recurso azure Cognitive Search com direitos de administração. Para um portal de passagem, comece com [Quickstart: Crie um índice](search-get-started-portal.md)de Pesquisa Cognitiva Azure no portal .

## <a name="scale-your-service"></a>Dimensionar o serviço

Depois de o serviço ser aprovisionado, pode dimensioná-lo para satisfazer as suas necessidades. Se escolheu o nível Standard para o seu serviço de Pesquisa Cognitiva Azure, pode escalar o seu serviço em duas dimensões: réplicas e divisórias. Se tiver escolhido o escalão Básico, apenas pode adicionar réplicas. Se tiver aprovisionado o serviço gratuito, o dimensionamento não está disponível.

As ***partições*** permitem ao serviço armazenar e pesquisar mais documentos.

As ***réplicas*** permitem ao serviço processar uma carga maior de consultas de pesquisa.

A adição de recursos aumenta a sua fatura mensal. A [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) pode ajudá-lo a compreender as ramificações de faturação da adição de recursos. Lembre-se de que pode ajustar os recursos com base na carga. Por exemplo, pode aumentar os recursos para criar um índice inicial completo e, em seguida, reduzir recursos mais tarde para um nível mais adequado para a indexação incremental.

> [!Important]
> Um serviço tem de ter [2 réplicas para SLA só de leitura e 3 réplicas para SLA de leitura/escrita](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Aceda à página do serviço de pesquisa no portal do Azure.
2. No painel de navegação esquerdo, selecione **Definições** > **Dimensionar**.
3. Utilize a barra de deslize para adicionar recursos de qualquer tipo.

![Adicionar capacidade](./media/search-create-service-portal/settings-scale.png "Adicionar capacidade através de réplicas e divisórias")

> [!Note]
> O armazenamento por divisória e a velocidade aumentam em níveis mais altos. Para mais informações, consulte [a capacidade e os limites.](search-limits-quotas-capacity.md)

## <a name="when-to-add-a-second-service"></a>Quando adicionar um segundo serviço

A maioria dos clientes utiliza apenas um serviço prestado a um nível que fornece o [equilíbrio certo de recursos.](search-sku-tier.md) Um serviço pode alojar vários índices, sujeitos aos [limites máximos do escalão que selecionar](search-capacity-planning.md), com os índices isolados uns dos outros. Na Pesquisa Cognitiva Azure, os pedidos só podem ser direcionados para um índice, minimizando a possibilidade de recuperação acidental ou intencional de dados de outros índices no mesmo serviço.

Embora a maioria dos clientes utilize apenas um serviço, a redundância de serviços pode ser necessária se os requisitos operacionais incluírem o seguinte:

* Recuperação após desastre (falha do datacenter). A Pesquisa Cognitiva Azure não fornece falhas instantâneas em caso de paragem. Para obter recomendações e orientações, veja [Administração de serviços](search-manage.md).
* A investigação de modelação de multi-inquilinos determinou que a conceção ideal é ter serviços adicionais. Para obter mais informações, veja [Conceber para multi-inquilinos](search-modeling-multitenant-saas-applications.md).
* Para aplicações implementadas globalmente, você pode exigir uma instância de Pesquisa Cognitiva Azure em várias regiões para minimizar a latência do tráfego internacional da sua aplicação.

> [!NOTE]
> Na Pesquisa Cognitiva Azure, não é possível segregar operações de indexação e consulta; assim, nunca criaria vários serviços para cargas de trabalho segregadas. Um índice é sempre consultado no serviço em que foi criado (não pode criar um índice num serviço e copiá-lo para outro).

Não é necessário um segundo serviço para elevada disponibilidade. A elevada disponibilidade para consultas é alcançada ao utilizar 2 ou mais réplicas no mesmo serviço. As atualizações de réplicas são sequenciais, o que significa que pelo menos uma está operacional quando uma atualização de serviço é lançada. Para mais informações sobre o tempo de antena, consulte [os Acordos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Passos seguintes

Depois de fornecer um serviço, pode continuar no portal para criar o seu primeiro índice.

> [!div class="nextstepaction"]
> [Quickstart: Criar um índice de pesquisa cognitiva Azure no portal](search-get-started-portal.md)
