---
title: Criar um serviço de Azure Search no portal-Azure Search
description: Provisionar um recurso de Azure Search no portal do Azure. Escolha grupos de recursos, regiões e a SKU ou tipo de preço.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 08/09/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 0649fea0b598ffaaaf2611c9d1324174105ee5d4
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68931544"
---
# <a name="create-an-azure-search-service-in-the-portal"></a>Criar um serviço do Azure Search no portal

Azure Search é um recurso autônomo usado para conectar uma experiência de pesquisa em aplicativos personalizados. Embora Azure Search se integre facilmente com outros serviços do Azure, você também pode usá-lo como um componente autônomo, ou integrá-lo a aplicativos em servidores de rede ou com software em execução em outras plataformas de nuvem.

Neste artigo, saiba como criar um recurso de Azure Search no [portal do Azure](https://portal.azure.com/).

[![GIF animado](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

Prefere o PowerShell? Utilize o [modelo de serviço](https://azure.microsoft.com/resources/templates/101-azure-search-create/) do Azure Resource Manager. Para obter ajuda com a introdução, consulte [gerenciar o Azure Search com o PowerShell](search-manage-powershell.md).

## <a name="subscribe-free-or-paid"></a>Subscrever (gratuito ou pago)

[Abra uma conta do Azure gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) e utilize créditos gratuitos para experimentar serviços do Azure pagos. Depois de gastar os créditos, mantenha a conta e continue a utilizar os serviços do Azure gratuitos, como Web sites. O seu cartão de crédito não será cobrado, a menos que altere explicitamente as suas definições e peça para ser cobrado.

Em alternativa, [ative os benefícios do subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Uma subscrição do MSDN dá-lhe créditos todos os meses que pode utilizar em serviços pagos do Azure. 

## <a name="find-azure-search"></a>Localizar o Azure Search

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Clique no sinal de adição ("+ criar recurso") no canto superior esquerdo.
3. Use a barra de pesquisa para localizar "Azure Search" ou navegue até o recurso por meio da**Azure Search** **da Web** > .

![Navegar para um recurso de Azure Search](./media/search-create-service-portal/find-search3.png "Caminho de navegação para Azure Search")

## <a name="select-a-subscription"></a>Selecione uma subscrição

Se tiver mais do que uma subscrição, escolha uma que tenha também serviços de armazenamento de dados ou ficheiros. Azure Search pode detectar automaticamente o armazenamento de tabelas e BLOBs do Azure, banco de dados SQL [](search-indexer-overview.md)e Azure Cosmos DB para indexação por meio de indexadores, mas somente para serviços na mesma assinatura.

## <a name="set-a-resource-group"></a>Definir um grupo de recursos

Um grupo de recursos é necessário e é útil para gerenciar todos os recursos, incluindo o gerenciamento de custos. Um grupo de recursos pode consistir em um serviço ou em vários serviços usados juntos. Por exemplo, se você estiver usando Azure Search para indexar um banco de dados de Azure Cosmos DB, poderá fazer com que ambos os serviços façam parte do mesmo grupo de recursos para fins de gerenciamento. 

Se você não estiver combinando recursos em um único grupo ou se os grupos de recursos existentes estiverem preenchidos com recursos usados em soluções não relacionadas, crie um novo grupo de recursos apenas para o recurso de Azure Search. 

Ao usar o serviço, você pode acompanhar os custos atuais e projetados em todas as distâncias (conforme mostrado na captura de tela) ou rolar para baixo para exibir os encargos de recursos individuais.

![Gerenciar custos no nível do grupo de recursos](./media/search-create-service-portal/resource-group-cost-management.png "Gerenciar custos no nível do grupo de recursos")

> [!TIP]
> A eliminação de um grupo de recursos também elimina os serviços no mesmo. Para projetos de protótipo que utilizam múltiplos serviços, colocá-los a todos no mesmo grupo de recursos facilita a limpeza depois de o projeto terminar.

## <a name="name-the-service"></a>Nomear o serviço

Em detalhes da instância, forneça um nome de serviço no campo **URL** . O nome faz parte do ponto de extremidade da URL para o qual as chamadas `https://your-service-name.search.windows.net`à API são emitidas:. Por exemplo, se você quiser que o ponto de `https://myservice.search.windows.net`extremidade seja, você `myservice`deve inserir.

Requisitos do nome do serviço:

* Tem de ser exclusivo no espaço de nomes search.windows.net
* Ter 2 e 60 carateres
* Utilize letras minúsculas, números ou hífenes ("-")
* Evite hífenes ("-") nos primeiro 2 carateres ou como último caráter único
* Sem hífenes consecutivos ("-") em qualquer parte

> [!TIP]
> Se você acha que usará vários serviços, recomendamos incluir a região (ou o local) no nome do serviço como uma Convenção de nomenclatura. Os serviços na mesma região podem trocar dados sem custos, portanto, se Azure Search estiver no oeste dos EUA, e se você tiver outros serviços também no oeste dos EUA, `mysearchservice-westus` um nome como pode poupar uma viagem à página de propriedades ao decidir como combinar ou anexar recursos.

## <a name="choose-a-location"></a>Escolher uma localização

Como um serviço do Azure, o Azure Search pode ser alojado em datacenters em todo o mundo. A lista de regiões com suporte pode ser encontrada na [página de preços](https://azure.microsoft.com/pricing/details/search/). 

Você pode minimizar ou evitar encargos de largura de banda escolhendo o mesmo local para vários serviços. Por exemplo, se você estiver indexando os dados fornecidos por outro serviço do Azure (armazenamento do Azure, Azure Cosmos DB, Azure SQL Database), a criação de seu serviço de Azure Search na mesma região evita cobranças de largura de banda (não há encargos para dados de saída quando os serviços estão em a mesma região).

Além disso, se você estiver usando os aprimoramentos de AI de pesquisa cognitiva, crie seu serviço na mesma região que o recurso de serviços cognitivas. A colocalização *de Azure Search e serviços cognitivas na mesma região é um requisito para o enriquecimento de ia*.

> [!Note]
> A Índia central não está disponível no momento para novos serviços. Para serviços que já estão na Índia central, você pode escalar verticalmente sem restrições e o serviço tem suporte total nessa região. A restrição nessa região é temporária e limitada apenas a novos serviços. Removeremos esta observação quando a restrição não se aplicar mais.

## <a name="choose-a-pricing-tier-sku"></a>Escolher um tipo de preço (SKU)

O [Azure Search atualmente é oferecido em vários tipos de preço](https://azure.microsoft.com/pricing/details/search/): Gratuito, básico ou Standard. Cada escalão tem a sua própria [capacidade e limites](search-limits-quotas-capacity.md). Veja [Escolher um escalão de preço ou SKU](search-sku-tier.md) para obter orientações.

Básico e standard são as opções mais comuns para cargas de trabalho de produção, mas a maioria dos clientes começa com o serviço gratuito. As principais diferenças entre as camadas são o tamanho e a velocidade da partição e os limites do número de objetos que você pode criar.

Lembre-se de que um tipo de preço não pode ser alterado depois que o serviço é criado. Se precisar de um escalão superior ou inferior mais tarde, terá de voltar a criar o serviço.

## <a name="create-your-service"></a>Criar o serviço

Depois de fornecer as entradas necessárias, vá em frente e crie o serviço. 

![Examinar e criar o serviço](./media/search-create-service-portal/new-service3.png "Examinar e criar o serviço")

Seu serviço é implantado em minutos, que você pode monitorar por meio de notificações do Azure. Considere fixar o serviço em seu painel para facilitar o acesso no futuro.

![Monitorar e fixar o serviço](./media/search-create-service-portal/monitor-notifications.png "Monitorar e fixar o serviço")

## <a name="get-a-key-and-url-endpoint"></a>Obter um ponto de extremidade de chave e URL

A menos que você esteja usando o portal, o acesso programático ao seu novo serviço requer que você forneça o ponto de extremidade da URL e uma chave de API de autenticação.

1. Na página Visão geral do serviço, localize e copie o ponto de extremidade da URL no lado direito da página.

2. No painel de navegação esquerdo, selecione **chaves** e, em seguida, copie uma das chaves de administração (elas são equivalentes). API de administração-as chaves são necessárias para criar, atualizar e excluir objetos em seu serviço.

   ![Página Visão geral do serviço com ponto de extremidade de URL](./media/search-create-service-portal/get-url-key.png "Ponto de extremidade de URL e outros detalhes do serviço")

Um ponto de extremidade e uma chave não são necessários para tarefas baseadas no Portal. O portal já está vinculado ao recurso de Azure Search com direitos de administrador. Para obter uma explicação do portal, [comece com o início rápido: Crie um índice de Azure Search no portal](search-get-started-portal.md).

## <a name="scale-your-service"></a>Dimensionar o serviço

Depois de o serviço ser aprovisionado, pode dimensioná-lo para satisfazer as suas necessidades. Se você escolher a camada Standard para seu serviço de Azure Search, poderá dimensionar seu serviço em duas dimensões: réplicas e partições. Se tiver escolhido o escalão Básico, apenas pode adicionar réplicas. Se tiver aprovisionado o serviço gratuito, o dimensionamento não está disponível.

As ***partições*** permitem ao serviço armazenar e pesquisar mais documentos.

As ***réplicas*** permitem ao serviço processar uma carga maior de consultas de pesquisa.

A adição de recursos aumenta a sua fatura mensal. A [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) pode ajudá-lo a compreender as ramificações de faturação da adição de recursos. Lembre-se de que pode ajustar os recursos com base na carga. Por exemplo, pode aumentar os recursos para criar um índice inicial completo e, em seguida, reduzir recursos mais tarde para um nível mais adequado para a indexação incremental.

> [!Important]
> Um serviço tem de ter [2 réplicas para SLA só de leitura e 3 réplicas para SLA de leitura/escrita](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Aceda à página do serviço de pesquisa no portal do Azure.
2. No painel de navegação esquerdo, selecione **Definições** > **Dimensionar**.
3. Utilize a barra de deslize para adicionar recursos de qualquer tipo.

![Adicionar capacidade](./media/search-create-service-portal/settings-scale.png "Adicionar capacidade por meio de réplicas e partições")

> [!Note]
> O armazenamento por partição e a velocidade aumentam em camadas mais altas. Para obter mais informações, consulte [capacidade e limites](search-limits-quotas-capacity.md).

## <a name="when-to-add-a-second-service"></a>Quando adicionar um segundo serviço

A maioria dos clientes usa apenas um serviço provisionado em uma camada que fornece o [equilíbrio certo de recursos](search-sku-tier.md). Um serviço pode alojar vários índices, sujeitos aos [limites máximos do escalão que selecionar](search-capacity-planning.md), com os índices isolados uns dos outros. No Azure Search, os pedidos só podem ser direcionados para um índice, o que minimiza a possibilidade de obtenção de dados acidental ou intencional a partir de outros índices no mesmo serviço.

Embora a maioria dos clientes utilize apenas um serviço, a redundância de serviços pode ser necessária se os requisitos operacionais incluírem o seguinte:

* Recuperação após desastre (falha do datacenter). O Azure Search não fornece ativação pós-falha instantânea na eventualidade de indisponibilidade. Para obter recomendações e orientações, veja [Administração de serviços](search-manage.md).
* A investigação de modelação de multi-inquilinos determinou que a conceção ideal é ter serviços adicionais. Para obter mais informações, veja [Conceber para multi-inquilinos](search-modeling-multitenant-saas-applications.md).
* Para aplicações implementadas globalmente, pode necessitar de uma instância do Azure Search em várias regiões para minimizar a latência do tráfego internacional da sua aplicação.

> [!NOTE]
> No Azure Search, não é possível separar as operações de indexação e consulta; Portanto, você nunca criaria vários serviços para cargas de trabalho segregas. Um índice é sempre consultado no serviço em que foi criado (não pode criar um índice num serviço e copiá-lo para outro).

Não é necessário um segundo serviço para elevada disponibilidade. A elevada disponibilidade para consultas é alcançada ao utilizar 2 ou mais réplicas no mesmo serviço. As atualizações de réplicas são sequenciais, o que significa que pelo menos uma está operacional quando é implementada uma atualização de serviço. Para obter mais informações sobre o tempo de atividade, veja [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Passos seguintes

Depois de provisionar um serviço de Azure Search, você pode continuar no portal para criar seu primeiro índice.

> [!div class="nextstepaction"]
> [Quickstart: Criar um índice de Azure Search no portal](search-get-started-portal.md)
