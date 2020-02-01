---
title: Recursos Azure - QnA Maker
description: O QnA Maker utiliza várias fontes Azure, cada uma com um propósito diferente. Compreender como são usados individualmente permite-lhe planear e selecionar o nível de preços correto ou saber quando alterar o seu nível de preços. Compreender como são usados em combinação permite-lhe encontrar e corrigir problemas quando ocorrem.
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 77aced459c7731a2cac432538cfc66ed9ce83f9d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902078"
---
# <a name="azure-resources-for-qna-maker"></a>Recursos azure para O Fabricante qnA

O QnA Maker utiliza várias fontes Azure, cada uma com um propósito diferente. Compreender como são usados individualmente permite-lhe planear e selecionar o nível de preços correto ou saber quando alterar o seu nível de preços. Compreender como são usados _em combinação_ permite-lhe encontrar e corrigir problemas quando ocorrem.

## <a name="resource-planning"></a>Planeamento de recursos

Quando se desenvolve pela primeira vez uma base de conhecimentos qnA Maker, na fase do protótipo, é comum ter um único recurso QnA Maker tanto para testes como para produção.

Quando se entra na fase de desenvolvimento do projeto, deve considerar:

* quantas línguas o seu sistema base de conhecimento vai manter
* quantas regiões precisa da sua base de conhecimento para estar disponível dentro/a partir
* quantos documentos em cada domínio o seu sistema vai manter

Planeie ter um único recurso QnA Maker manter todas as bases de conhecimento que tenham a mesma língua, a mesma região e a mesma combinação de domínio sujeito.

## <a name="pricing-tier-considerations"></a>Considerações de nível de preços

Normalmente, existem três parâmetros, que precisa considerar:

* **O débito precisa do serviço**: selecione o adequado [plano de aplicação](https://azure.microsoft.com/pricing/details/app-service/plans/) para o serviço de aplicações com base nas suas necessidades. Pode [aumentar verticalmente](https://docs.microsoft.com/azure/app-service/manage-scale-up) ou reduzir verticalmente a aplicação. Isto também deve influenciar a sua seleção Azure Cognitive Search SKU, consulte mais detalhes [aqui](https://docs.microsoft.com/azure/search/search-sku-tier).

* **Tamanho e o número de bases de dados de conhecimento**: escolha apropriado [SKU de pesquisa do Azure](https://azure.microsoft.com/pricing/details/search/) para o seu cenário. Normalmente, decide-se o número de bases de conhecimento que precisa com base no número de diferentes domínios sujeitos. Uma vez que o domínio sujeito (para uma única língua) deve estar numa base de conhecimento.

    Pode publicar o bases de dados de conhecimento de n-1 numa camada específica, onde N é os índices máximos permitidos na camada. Também pode verificar o tamanho máximo e o número de documentos permitidas por camada.

    Por exemplo, se sua camada tiver 15 índices permitidos, você poderá publicar 14 bases de dados de conhecimento (1 índice por base de dados de conhecimento publicado). O décimo quinto índice é utilizado para todas as bases de conhecimento para autoria e teste.

* **Número de documentos como fontes**: O SKU gratuito do serviço de gestão do QnA Maker limita o número de documentos, pode gerenciar através do portal e as APIs para 3 (de 1 MB de tamanho de cada). O padrão de SKU não tem limites ao número de documentos, que pode gerir. Ver mais detalhes [aqui](https://aka.ms/qnamaker-pricing).

A tabela seguinte fornece algumas diretrizes de alto nível.

|                        | Gestão do QnA Maker | Serviço de Aplicações | Pesquisa Cognitiva do Azure | Limitações                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Experimentação        | SKU gratuito             | Escalão Gratuito   | Escalão Gratuito    | Publicar até 2 KBs, tamanho de 50 MB  |
| Ambiente de desenvolvimento/teste   | SKU Standard         | Partilhado      | Basic        | Publicar até 14 KBs, tamanho de 2 GB    |
| Ambiente de produção | SKU Standard         | Basic       | Padrão     | Publicar até 49 KBs, 25 GB de tamanho |

## <a name="when-to-change-a-pricing-tier"></a>Quando mudar um nível de preços

|Atualização|Razão|
|--|--|
|[Atualizar](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) o SKU de gerenciamento de QnA Maker|Você quer ter mais conjuntos qnA ou fontes de documento na sua base de conhecimento.|
|[Atualizar](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) o SKU do serviço de aplicativo|Sua base de dados de conhecimento precisa atender a mais solicitações do aplicativo cliente, como um bot de chat.|
|[Atualizar](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) o Serviço de Pesquisa Cognitiva do Azure|Você planeja ter muitas bases de dados de conhecimento.|

Obtenha as últimas atualizações de tempo de execução [atualizando o seu Serviço de Aplicações no portal Azure](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

## <a name="resource-naming-considerations"></a>Considerações de nomeação de recursos

O nome de recurso para o recurso QnA Maker, como `qna-westus-f0-b`, também é usado para nomear os outros recursos.

O portal Azure cria janela que permite criar um recurso QnA Maker e selecionar os níveis de preços para os outros recursos.

> [!div class="mx-imgBorder"]
> ![Screenshot do portal Azure para a criação de recursos da QnA Maker](../media/concept-azure-resource/create-blade.png)

Após a criação dos recursos, têm o mesmo nome, com exceção do recurso opcional Application Insights, que publica caracteres para o nome.

> [!div class="mx-imgBorder"]
> ![Screenshot da](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png) de listagem de recursos do portal Azure

> [!TIP]
> Crie um novo grupo de recursos quando criar um recurso QnA Maker. Isso permite-lhe ver todos os recursos associados ao recurso QnA Maker ao pesquisar pelo grupo de recursos.

> [!TIP]
> Utilize uma convenção de nomeação para indicar níveis de preços em nome do recurso ou do grupo de recursos. Quando recebe erros na criação de uma nova base de conhecimento, ou na adição de novos documentos, o limite de preços da Pesquisa Cognitiva é uma questão comum.

## <a name="resource-purposes"></a>Fins de recursos

Cada recurso Azure criado com o QnA Maker tem um propósito específico:

* QnA Maker recurso
* Recurso de pesquisa cognitiva
* Serviço de Aplicações
* Serviço de plano de aplicações
* Serviço de Insights de Aplicação


### <a name="cognitive-search-resource"></a>Recurso de pesquisa cognitiva

O recurso [de Pesquisa Cognitiva](../../../search/index.yml) é utilizado para:

* Armazenar os conjuntos QnA
* Fornecer o ranking inicial (ranking #1) dos conjuntos qnA em tempo de execução

#### <a name="index-usage"></a>Utilização do índice

O recurso mantém um índice para agir como o índice de teste e os restantes índices correlacionarem-se com uma base de conhecimento publicada cada.

Um recurso com preços para manter 15 índices, terá 14 bases de conhecimento publicadas, e um índice é usado para testar todas as bases de conhecimento. Este índice de teste é dividido pela base de conhecimento de modo a que uma consulta utilizando o painel de teste interativo utilize o índice de teste, mas apenas retorno os resultados da divisória específica associada à base de conhecimentos específica.

#### <a name="language-usage"></a>Uso da linguagem

A primeira base de conhecimento criada no recurso QnA Maker é usada para determinar o conjunto de linguagem _única_ para o recurso de Pesquisa Cognitiva e todos os seus índices. Você só pode ter _um conjunto de idiomas_ para um serviço QnA Maker.

### <a name="qna-maker-resource"></a>QnA Maker recurso

O recurso QnA Maker fornece acesso às APIs de autoria e publicação, bem como ao processamento de linguagem natural (NLP) com base na segunda camada de ranking (ranking #2) dos conjuntos QnA em tempo de execução.

O segundo ranking aplica filtros inteligentes que podem incluir metadados e solicitações de seguimento.

#### <a name="qna-maker-resource-configuration-settings"></a>Definições de configuração de recursos do Fabricante QnA

Quando se cria uma nova base de conhecimentos no [portal QnA Maker,](https://qnamaker.ai)a definição de **Idioma** é a única definição que é aplicada ao nível dos recursos. Seleciona o idioma quando cria a primeira base de conhecimentos para o recurso. 

### <a name="app-service-and-app-service-plan"></a>Serviço de aplicativos e plano de serviço de aplicativos

O [serviço app](../../../app-service/index.yml) é utilizado pela sua aplicação cliente para aceder às bases de conhecimento publicadas através do ponto final do prazo de execução.

Para consultar a base de conhecimento publicada, todas as bases de conhecimento publicadas utilizam o mesmo ponto final de URL, mas especificam o ID da base de **conhecimento** dentro da rota.

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Estatísticas das Aplicações

[Application Insights](../../../azure-monitor/app/app-insights-overview.md) é usado para recolher registos de chat e telemetria. Reveja as consultas comuns de [Kusto](../how-to/get-analytics-knowledge-base.md) para obter informações sobre o seu serviço.

## <a name="share-services-with-qna-maker"></a>Partilhar serviços com a QnA Maker

QnA Maker cria vários recursos do Azure. Para reduzir o gerenciamento e beneficiar-se do compartilhamento de custos, use a tabela a seguir para entender o que você pode e não consegue compartilhar:

|Serviço|Partilhar|Razão|
|--|--|--|
|Serviços Cognitivos|X|Não é possível por design|
|Plano do App Service|✔|Espaço em disco fixo alocado para um plano do serviço de aplicativo. Se outros aplicativos que compartilham o mesmo plano do serviço de aplicativo usarem um espaço em disco significativo, a instância do serviço de aplicativo do QnAMaker encontrará problemas.|
|Serviço de Aplicações|X|Não é possível por design|
|Estatísticas das Aplicações|✔|Pode ser compartilhado|
|Serviço de pesquisa|✔|1. `testkb` é um nome reservado para o serviço QnAMaker; Ele não pode ser usado por outras pessoas.<br>2. o mapa de sinônimos pelo nome `synonym-map` é reservado para o serviço QnAMaker.<br>3. o número de bases de dados de conhecimento publicadas é limitado pela camada de serviço de pesquisa. Se houver índices livres disponíveis, outros serviços poderão usá-los.|

### <a name="using-a-single-cognitive-search-service"></a>Usando um único serviço de pesquisa cognitiva

Se você criar um serviço QnA e suas dependências (como pesquisa) por meio do portal, um serviço de pesquisa será criado para você e vinculado ao serviço de QnA Maker. Depois que esses recursos forem criados, você poderá atualizar a configuração do serviço de aplicativo para usar um serviço de pesquisa existente anteriormente e remover o que você acabou de criar.

Aprenda [a configurar o](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) QnA Maker para usar um recurso de Serviço Cognitivo diferente daquele criado como parte do processo de criação de recursos qnA Maker.

## <a name="management-service-region"></a>Região do serviço de gerenciamento

O serviço de gerenciamento do QnA Maker é usado somente para o portal de QnA Maker e para o processamento de dados inicial. Este serviço está disponível apenas na região **oeste dos EUA.** Nenhum dado do cliente é armazenado neste serviço oeste dos EUA.

## <a name="keys-in-qna-maker"></a>Chaves no Fabricante qna

O seu serviço QnA Maker lida com dois tipos de chaves: chaves de **autor** e **chaves finais** de consulta utilizadas com o tempo de execução hospedado no serviço app.

Se procura a sua chave de **subscrição,** [a terminologia mudou.](#subscription-keys)

Utilize estas chaves ao fazer pedidos ao serviço através de APIs.

![Gestão de chaves](../media/qnamaker-how-to-key-management/key-management.png)

|Nome|Localização|Finalidade|
|--|--|--|
|Chave de criação|[Portal do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|Essas chaves são usadas para acessar as [APIs do serviço de gerenciamento de QnA Maker](https://go.microsoft.com/fwlink/?linkid=2092179). Essas APIs permitem que você edite as perguntas e respostas em sua base de dados de conhecimento e publique sua base de dados de conhecimento. Essas chaves são criadas quando você cria um novo serviço de QnA Maker.<br><br>Localize essas chaves no recurso **Serviços cognitivas** na página **chaves** .|
|Chave final de consulta|[Portal de QnA Maker](https://www.qnamaker.ai)|Estas teclas são usadas para consultar o ponto final da base de conhecimento publicado para obter uma resposta para uma pergunta do utilizador. Normalmente utiliza este ponto final de consulta no seu chat bot ou no código de aplicação do cliente que se conecta ao serviço QnA Maker. Essas chaves são criadas quando você publica sua base de dados de conhecimento QnA Maker.<br><br>Localize essas chaves na página **configurações de serviço** . Localize essa página no menu do usuário no canto superior direito da página no menu suspenso.|

### <a name="subscription-keys"></a>Chaves de subscrição

Os termos de autoria e consulta final são termos corretivos. O termo anterior era **a chave de subscrição.** Se vir outra documentação referindo-se às teclas de subscrição, estas são equivalentes a chaves finais de autoria e consulta (utilizadas no tempo de execução).

Deve saber qual é a chave de acesso, gestão da base de conhecimentos ou consulta da base de conhecimentos, para saber qual a chave que precisa de encontrar.

## <a name="next-steps"></a>Passos seguintes

* Conheça a base de [conhecimento](knowledge-base.md) do QnA Maker
* Compreender um ciclo de [vida base](development-lifecycle-knowledge-base.md) de conhecimento
* Rever [limites](../limits.md) de base de serviço e conhecimento

