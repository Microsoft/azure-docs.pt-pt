---
title: Criar um serviço de pesquisa no portal
titleSuffix: Azure Cognitive Search
description: Saiba como configurar um recurso de Pesquisa Cognitiva Azure no portal Azure. Escolha grupos de recursos, regiões e sKU ou nível de preços.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2021
ms.openlocfilehash: 83e206a5fd7b34da0b0ac8590d5271a554855d3e
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580755"
---
# <a name="create-an-azure-cognitive-search-service-in-the-portal"></a>Criar um serviço Azure Cognitive Search no portal

[A Azure Cognitive Search](search-what-is-azure-search.md) é um recurso Azure usado para adicionar uma experiência completa de pesquisa de texto a aplicações personalizadas. Pode integrá-lo facilmente com outros serviços Azure que fornecem dados ou processamento adicional, com aplicações em servidores de rede ou com software em execução em outras plataformas de nuvem.

Pode criar um serviço de pesquisa através do [portal Azure,](https://portal.azure.com/)que está abrangido por este artigo. Também pode utilizar [a Azure PowerShell](search-manage-powershell.md), [Azure CLI,](/cli/azure/search)a [Management REST API](/rest/api/searchmanagement/)ou um [modelo de serviço Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-azure-search-create/).

[![GIF animado](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

## <a name="before-you-start"></a>Antes de começar

As seguintes propriedades de serviço são fixadas durante o tempo de vida do serviço - alterar qualquer uma delas requer um novo serviço. Por serem fixas, considere as implicações de utilização à medida que preenche cada propriedade:

+ O nome do serviço torna-se parte do ponto final do URL[(dicas de revisão](#name-the-service) para nomes de serviços úteis).
+ [O nível de](search-sku-tier.md) serviço afeta a faturação e estabelece um limite ascendente de capacidade. Algumas funcionalidades não estão disponíveis no nível livre.
+ A região de serviços pode determinar a disponibilidade de certos cenários. Se necessitar de [funcionalidades](search-security-overview.md) de alta segurança ou [enriquecimento de IA,](cognitive-search-concept-intro.md)terá de criar a Azure Cognitive Search na mesma região que outros serviços, ou em regiões que fornecem a funcionalidade em questão. 

## <a name="subscribe-free-or-paid"></a>Subscrever (gratuito ou pago)

[Abra uma conta do Azure gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) e utilize créditos gratuitos para experimentar serviços do Azure pagos. Depois de gastar os créditos, mantenha a conta e continue a utilizar os serviços do Azure gratuitos, como Web sites. O seu cartão de crédito não será cobrado, a menos que altere explicitamente as suas definições e peça para ser cobrado.

Em alternativa, [ative os benefícios do subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Uma subscrição do MSDN dá-lhe créditos todos os meses que pode utilizar em serviços pagos do Azure. 

## <a name="find-azure-cognitive-search"></a>Encontre a pesquisa cognitiva de Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Clique no sinal de mais **("+ Criar Recurso"**) no canto superior esquerdo.

1. Utilize a barra de pesquisa para encontrar "Azure Cognitive Search" ou navegue para o recurso através da **Web**  >  **Azure Cognitive Search**.

:::image type="content" source="media/search-create-service-portal/find-search3.png" alt-text="Criar um recurso no portal" border="false":::

## <a name="choose-a-subscription"></a>Escolha uma subscrição

Se tiver mais de uma subscrição, escolha uma para o seu serviço de pesquisa. Se estiver a implementar [uma dupla encriptação](search-security-overview.md#double-encryption) ou outras funcionalidades que dependam de identidades de serviço geridas, escolha a mesma subscrição que a utilizada para o Azure Key Vault ou outros serviços para os quais são utilizadas identidades geridas.

## <a name="set-a-resource-group"></a>Definir um grupo de recursos

Um grupo de recursos é um contentor que detém recursos relacionados para a sua solução Azure. É necessário para o serviço de pesquisa. É também útil para gerir todos os recursos, incluindo custos. Um grupo de recursos pode consistir de um serviço, ou vários serviços usados em conjunto. Por exemplo, se estiver a utilizar a Azure Cognitive Search para indexar uma base de dados DB do Azure Cosmos, poderá fazer de ambos os serviços parte do mesmo grupo de recursos para fins de gestão. 

Se não estiver a combinar recursos num único grupo, ou se os grupos de recursos existentes estiverem repletos de recursos utilizados em soluções não relacionadas, crie um novo grupo de recursos apenas para o seu recurso Azure Cognitive Search. 

:::image type="content" source="media/search-create-service-portal/new-resource-group.png" alt-text="Criar um novo grupo de recursos" border="false":::

Com o tempo, você pode rastrear os custos atuais e projetados all-up ou você pode ver os custos para recursos individuais. A imagem que se segue mostra o tipo de informação de custos que pode esperar ver quando combina vários recursos num só grupo.

:::image type="content" source="media/search-create-service-portal/resource-group-cost-management.png" alt-text="Gerir os custos ao nível do grupo de recursos" border="false":::

> [!TIP]
> Os grupos de recursos simplificam a limpeza porque a eliminação de um grupo elimina todos os serviços dentro dele. Para projetos de protótipo que utilizam múltiplos serviços, colocá-los a todos no mesmo grupo de recursos facilita a limpeza depois de o projeto terminar.

## <a name="name-the-service"></a>Nomeie o serviço

Em caso de detalhes, forneça um nome de serviço no campo **URL.** O nome faz parte do ponto final do URL contra o qual são emitidas chamadas API: `https://your-service-name.search.windows.net` . Por exemplo, se quiser que o ponto final `https://myservice.search.windows.net` seja, entrará `myservice` .

Requisitos do nome do serviço:

+ Tem de ser exclusivo no espaço de nomes search.windows.net
+ Deve ter entre 2 e 60 caracteres de comprimento
+ Deve utilizar letras minúsculas, dígitos ou traços ("-")
+ Não utilize traços ("-") nos primeiros 2 caracteres ou como o último personagem único
+ Não pode usar traços consecutivos ("--") em qualquer lugar

> [!TIP]
> Se você acha que você vai usar vários serviços, recomendamos incluir a região (ou localização) no nome de serviço como uma convenção de nomeação. Os serviços dentro da mesma região podem trocar dados gratuitamente, por isso, se a Azure Cognitive Search estiver nos EUA Ocidentais, e tiver outros serviços também nos EUA ocidentais, um nome como `mysearchservice-westus` pode poupar-lhe uma viagem à página de propriedades ao decidir como combinar ou anexar recursos.

## <a name="choose-a-location"></a>Escolha uma localização

A Azure Cognitive Search está disponível na maioria das regiões, conforme documentado nos [Produtos disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=search) Geralmente, se estiver a utilizar vários serviços Azure, escolha uma região que também esteja hospedando os seus dados ou serviço de aplicação. Ao fazê-lo, minimiza ou anula os encargos de largura de banda para os dados de saída (não há encargos para os dados de saída quando os serviços estão na mesma região).

+ [O enriquecimento de](cognitive-search-concept-intro.md) IA requer que os Serviços Cognitivos estejam na mesma região física que a Pesquisa Cognitiva de Azure. Há apenas algumas regiões que não fornecem ambas. Os [Produtos disponíveis por](https://azure.microsoft.com/global-infrastructure/services/?products=search) página de região indicam dupla disponibilidade mostrando duas marcas de verificação empilhadas. Uma combinação indisponível tem uma marca de verificação em falta:

  :::image type="content" source="media/search-create-service-portal/region-availability.png" alt-text="Disponibilidade regional" border="true":::

+ Os requisitos de continuidade do negócio e recuperação de desastres (BCDR) devem ser cumpridos através da criação de múltiplos serviços de pesquisa em [pares regionais.](../best-practices-availability-paired-regions.md#azure-regional-pairs) Por exemplo, se estiver a operar na América do Norte, poderá escolher os EUA e o Oeste dos EUA, ou o Norte Central DOS EUA e o Centro Sul dos EUA, para cada serviço de pesquisa.

Algumas funcionalidades têm disponibilidade limitada com base em regiões. As restrições são delineadas na documentação do recurso:

+ [Dupla encriptação](search-security-overview.md#double-encryption)

+ ["Zonas de disponibilidade" em Escala para Desempenho](search-performance-optimization.md#availability-zones).

## <a name="choose-a-pricing-tier"></a>Escolha um nível de preços

A Azure Cognitive Search é atualmente oferecida em [vários níveis de preços](https://azure.microsoft.com/pricing/details/search/): Gratuito, Básico, Padrão ou Armazenamento Otimizado. Cada escalão tem a sua própria [capacidade e limites](search-limits-quotas-capacity.md). Consulte [Escolha um nível de preços](search-sku-tier.md) para obter orientação.

A Basic e a Standard são as escolhas mais comuns para cargas de trabalho de produção, mas a maioria dos clientes começa com o serviço Gratuito. As principais diferenças entre os níveis são o tamanho e a velocidade da partição, e os limites do número de objetos que pode criar.

Lembre-se, um nível de preços não pode ser alterado uma vez que o serviço é criado. Se precisar de um nível superior ou inferior, terá de recriar o serviço.

## <a name="create-your-service"></a>Criar o serviço

Depois de fornecer as entradas necessárias, vá em frente e crie o serviço. 

:::image type="content" source="media/search-create-service-portal/new-service3.png" alt-text="Rever e criar o serviço" border="false":::

O seu serviço é acionado dentro de minutos. Pode monitorizar o progresso através de notificações do Azure. Considere fixar o serviço no seu painel de instrumentos para facilitar o acesso no futuro.

:::image type="content" source="media/search-create-service-portal/monitor-notifications.png" alt-text="Monitorize e pine o serviço" border="false":::

## <a name="get-a-key-and-url-endpoint"></a>Obtenha uma chave e ponto final URL

A menos que esteja a utilizar o portal, o acesso programático ao seu novo serviço requer que forneça o ponto final url e uma chave api de autenticação.

1. Na página **'Vista Geral',** localize e copie o ponto final URL no lado direito da página.

1. Na página **Chaves,** copie qualquer uma das teclas de administração (são equivalentes). As teclas Admin api são necessárias para criar, atualizar e apagar objetos no seu serviço. Em contraste, as teclas de consulta fornecem acesso de leitura ao conteúdo do índice.

   :::image type="content" source="media/search-create-service-portal/get-url-key.png" alt-text="Página geral do serviço com ponto final de URL" border="false":::

Não são necessários pontos finais e chave para tarefas baseadas no portal. O portal já está ligado ao seu recurso Azure Cognitive Search com direitos de administração. Para uma passagem de portal, comece com [Quickstart: Crie um índice de Pesquisa Cognitiva Azure no portal](search-get-started-portal.md).

## <a name="scale-your-service"></a>Dimensionar o serviço

Depois de o serviço ser aprovisionado, pode dimensioná-lo para satisfazer as suas necessidades. Se escolheu o nível Standard para o seu serviço de Pesquisa Cognitiva Azure, pode escalar o seu serviço em duas dimensões: réplicas e divisórias. Se tiver escolhido o escalão Básico, apenas pode adicionar réplicas. Se tiver aprovisionado o serviço gratuito, o dimensionamento não está disponível.

As ***partições*** permitem ao serviço armazenar e pesquisar mais documentos.

As ***réplicas*** permitem ao serviço processar uma carga maior de consultas de pesquisa.

A adição de recursos aumenta a sua fatura mensal. A [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) pode ajudá-lo a compreender as ramificações de faturação da adição de recursos. Lembre-se de que pode ajustar os recursos com base na carga. Por exemplo, pode aumentar os recursos para criar um índice inicial completo e, em seguida, reduzir recursos mais tarde para um nível mais adequado para a indexação incremental.

> [!Important]
> Um serviço tem de ter [2 réplicas para SLA só de leitura e 3 réplicas para SLA de leitura/escrita](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Aceda à página do serviço de pesquisa no portal do Azure.
1. No painel de navegação à esquerda, selecione **Escala de Definições**  >  .
1. Utilize a barra de deslize para adicionar recursos de qualquer tipo.

:::image type="content" source="media/search-create-service-portal/settings-scale.png" alt-text="Adicione capacidade através de réplicas e divisórias" border="false":::

> [!Note]
> O armazenamento por partição e a velocidade aumentam em níveis mais altos. Para mais informações, consulte [a capacidade e os limites.](search-limits-quotas-capacity.md)

## <a name="when-to-add-a-second-service"></a>Quando adicionar um segundo serviço

A maioria dos clientes usa apenas um serviço prestado num nível que fornece o [equilíbrio de recursos adequado.](search-sku-tier.md) Um serviço pode alojar vários índices, sujeitos aos [limites máximos do escalão que selecionar](search-capacity-planning.md), com os índices isolados uns dos outros. Na Pesquisa Cognitiva do Azure, os pedidos só podem ser direcionados para um índice, minimizando a possibilidade de recuperação acidental ou intencional de dados de outros índices no mesmo serviço.

Embora a maioria dos clientes utilize apenas um serviço, a redundância de serviços pode ser necessária se os requisitos operacionais incluírem o seguinte:

+ [Continuidade do negócio e recuperação de desastres (BCDR)](../best-practices-availability-paired-regions.md). A Azure Cognitive Search não proporciona falha instantânea em caso de interrupção.

+ [As arquiteturas multi-arrendatários](search-modeling-multitenant-saas-applications.md) às vezes exigem dois ou mais serviços.

+ Aplicações implementadas globalmente podem exigir serviços de pesquisa em cada geografia para minimizar a latência.

> [!NOTE]
> Na Pesquisa Cognitiva Azure, não é possível segregar operações de indexação e consulta; assim, nunca criaria múltiplos serviços para cargas de trabalho segregadas. Um índice é sempre consultado no serviço em que foi criado (não pode criar um índice num serviço e copiá-lo para outro).

Não é necessário um segundo serviço para elevada disponibilidade. A elevada disponibilidade para consultas é alcançada ao utilizar 2 ou mais réplicas no mesmo serviço. As atualizações de réplicas são sequenciais, o que significa que pelo menos uma está operacional quando uma atualização de serviço é lançada. Para obter mais informações sobre o tempo de paragem, consulte [Os Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Passos seguintes

Depois de prestar um serviço, pode continuar no portal para criar o seu primeiro índice.

> [!div class="nextstepaction"]
> [Quickstart: Criar um índice de Pesquisa Cognitiva Azure no portal](search-get-started-portal.md)

Quer otimizar e economizar nos gastos na nuvem?

> [!div class="nextstepaction"]
> [Comece a analisar custos com a Gestão de Custos](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)