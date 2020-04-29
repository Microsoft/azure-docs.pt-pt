---
title: Recursos Azure - QnA Maker
description: O QnA Maker utiliza várias fontes Azure, cada uma com um propósito diferente. Compreender como são usados individualmente permite-lhe planear e selecionar o nível de preços correto ou saber quando alterar o seu nível de preços. Compreender como são usados em combinação permite-lhe encontrar e corrigir problemas quando ocorrem.
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 581029d2372f7a2ef704dcf02f266b66440aa246
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80873910"
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

Normalmente existem três parâmetros que deve considerar:

* **A entrada que precisa do serviço:**
    * Selecione o Plano de Aplicações apropriado para o seu serviço de [Aplicações](https://azure.microsoft.com/pricing/details/app-service/plans/) com base nas suas necessidades. Pode [escalar para cima](https://docs.microsoft.com/azure/app-service/manage-scale-up) ou para baixo na App.
    * Isto também deve influenciar a sua seleção Azure **Cognitive Search** SKU, consulte mais detalhes [aqui](https://docs.microsoft.com/azure/search/search-sku-tier). Além disso, poderá ter de ajustar a [capacidade](../../../search/search-capacity-planning.md) de Pesquisa Cognitiva com réplicas.

* **Tamanho e número de bases de conhecimento**: Escolha o SKU de pesquisa [azure](https://azure.microsoft.com/pricing/details/search/) apropriado para o seu cenário. Normalmente, decide-se o número de bases de conhecimento que precisa com base no número de diferentes domínios sujeitos. Uma vez que o domínio sujeito (para uma única língua) deve estar numa base de conhecimento.

    Pode publicar bases de conhecimento N-1 num determinado nível, onde N é o índice máximo permitido no nível. Verifique também o tamanho máximo e o número de documentos permitidos por nível.

    Por exemplo, se o seu nível tiver 15 índices permitidos, pode publicar 14 bases de conhecimento (1 índice por base de conhecimento publicada). O décimo quinto índice é utilizado para todas as bases de conhecimento para autoria e teste.

* **Número de documentos como fontes**: O SKU gratuito do serviço de gestão QnA Maker limita o número de documentos que pode gerir através do portal e das APIs a 3 (de 1 MB de tamanho cada). O SKU padrão não tem limites para o número de documentos que pode gerir. Veja mais detalhes [aqui.](https://aka.ms/qnamaker-pricing)

A tabela que se segue dá-lhe algumas diretrizes de alto nível.

|                        | Gestão de Fabricantes qna | Serviço de Aplicações | Azure Cognitive Search | Limitações                      |
| ---------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| Experimentação        | SKU grátis             | Tier Livre   | Tier Livre    | Publicar Até 2 KBs, tamanho de 50 MB  |
| Ambiente de v/teste   | SKU Standard         | Partilhado      | Básico        | Publicar até 14 KBs, 2 GB tamanho    |
| Ambiente de Produção | SKU Standard         | Básico       | Standard     | Publicar até 49 KBs, tamanho de 25 GB |

## <a name="recommended-settings"></a>Definições recomendadas

|QPS alvo | Serviço de Aplicações | Azure Cognitive Search |
| -------------------- | ----------- | ------------ |
| 3             | S1, 1 Instância   | S1, 1 Instância    |
| 50         | S3, 10 Instâncias       | S1, 12 Instâncias         |
| 80         | S3, 10 Instâncias      |  S3, 12 Instâncias  |
| 100         | P3V2, 10 Instâncias  | S3, 12 Instâncias, 3 Divisórias   |
| 200 a 250         | P3V2, 20 Instâncias | S3, 12 Instâncias, 3 Divisórias    |

## <a name="when-to-change-a-pricing-tier"></a>Quando mudar um nível de preços

|Atualizar|Razão|
|--|--|
|[Upgrade](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) QnA Maker gestão SKU|Você quer ter mais pares QnA ou fontes de documento sintetizados na sua base de conhecimento.|
|[Upgrade](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) Serviço de Aplicações SKU e verifique o nível de pesquisa cognitiva e [crie réplicas](../../../search/search-capacity-planning.md) de Pesquisa Cognitiva|A sua base de conhecimento precisa de servir mais pedidos da sua aplicação de cliente, como um chat bot.|
|[Upgrade](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Serviço de Pesquisa Cognitiva Azure|Planeias ter muitas bases de conhecimento.|

Obtenha as últimas atualizações de tempo de execução [atualizando o seu Serviço de Aplicações no portal Azure](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates).

## <a name="resource-naming-considerations"></a>Considerações de nomeação de recursos

O nome de recurso para o recurso `qna-westus-f0-b`QnA Maker, como, também é usado para nomear os outros recursos.

O portal Azure cria janela que permite criar um recurso QnA Maker e selecionar os níveis de preços para os outros recursos.

> [!div class="mx-imgBorder"]
> ![Screenshot do portal Azure para a criação de recursos da QnA Maker](../media/concept-azure-resource/create-blade.png)

Após a criação dos recursos, têm o mesmo nome, com exceção do recurso opcional Application Insights, que publica caracteres para o nome.

> [!div class="mx-imgBorder"]
> ![Screenshot da listagem de recursos do portal Azure](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> Crie um novo grupo de recursos quando criar um recurso QnA Maker. Isso permite-lhe ver todos os recursos associados ao recurso QnA Maker ao pesquisar pelo grupo de recursos.

> [!TIP]
> Utilize uma convenção de nomeação para indicar níveis de preços em nome do recurso ou do grupo de recursos. Quando recebe erros na criação de uma nova base de conhecimento, ou na adição de novos documentos, o limite de preços da Pesquisa Cognitiva é uma questão comum.

## <a name="resource-purposes"></a>Fins de recursos

Cada recurso Azure criado com o QnA Maker tem um propósito específico:

* Recurso QnA Maker
* Recurso de pesquisa cognitiva
* Serviço de Aplicações
* Serviço de plano de aplicações
* Serviço de Insights de Aplicação


### <a name="cognitive-search-resource"></a>Recurso de pesquisa cognitiva

O recurso [de Pesquisa Cognitiva](../../../search/index.yml) é utilizado para:

* Armazenar os pares QnA
* Fornecer o ranking inicial (ranking #1) dos pares QnA em tempo de execução

#### <a name="index-usage"></a>Utilização do índice

O recurso mantém um índice para agir como o índice de teste e os restantes índices correlacionarem-se com uma base de conhecimento publicada cada.

Um recurso com preços para manter 15 índices, terá 14 bases de conhecimento publicadas, e um índice é usado para testar todas as bases de conhecimento. Este índice de teste é dividido pela base de conhecimento de modo a que uma consulta utilizando o painel de teste interativo utilize o índice de teste, mas apenas retorno os resultados da divisória específica associada à base de conhecimentos específica.

#### <a name="language-usage"></a>Uso da linguagem

A primeira base de conhecimento criada no recurso QnA Maker é usada para determinar o conjunto de linguagem _única_ para o recurso de Pesquisa Cognitiva e todos os seus índices. Você só pode ter _um conjunto de idiomas_ para um serviço QnA Maker.

### <a name="qna-maker-resource"></a>Recurso QnA Maker

O recurso QnA Maker fornece acesso às APIs de autoria e publicação, bem como ao processamento de linguagem natural (NLP) com base na segunda camada de ranking (ranking #2) dos pares QnA em tempo de execução.

O segundo ranking aplica filtros inteligentes que podem incluir metadados e solicitações de seguimento.

#### <a name="qna-maker-resource-configuration-settings"></a>Definições de configuração de recursos do Fabricante QnA

Quando se cria uma nova base de conhecimentos no [portal QnA Maker,](https://qnamaker.ai)a definição de **Idioma** é a única definição que é aplicada ao nível dos recursos. Seleciona o idioma quando cria a primeira base de conhecimentos para o recurso.

### <a name="app-service-and-app-service-plan"></a>Serviço de aplicativos e plano de serviço de aplicativos

O [serviço app](../../../app-service/index.yml) é utilizado pela sua aplicação cliente para aceder às bases de conhecimento publicadas através do ponto final do prazo de execução.

Para consultar a base de conhecimento publicada, todas as bases de conhecimento publicadas utilizam o mesmo ponto final de URL, mas especificam o ID da base de **conhecimento** dentro da rota.

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[Application Insights](../../../azure-monitor/app/app-insights-overview.md) é usado para recolher registos de chat e telemetria. Reveja as consultas comuns de [Kusto](../how-to/get-analytics-knowledge-base.md) para obter informações sobre o seu serviço.

## <a name="share-services-with-qna-maker"></a>Partilhar serviços com a QnA Maker

A QnA Maker cria vários recursos Azure. Para reduzir a gestão e beneficiar da partilha de custos, use a seguinte tabela para entender o que pode e não pode partilhar:

|Serviço|Partilhado|Razão|
|--|--|--|
|Serviços Cognitivos|X|Não é possível por design|
|Plano do App Service|✔|Espaço fixo em disco atribuído a um plano de Serviço de Aplicações. Se outras aplicações que partilham o mesmo plano de App Service usarem espaço significativo em disco, a instância do QnAMaker App Service encontrará problemas.|
|Serviço de Aplicações|X|Não é possível por design|
|Application Insights|✔|Pode ser partilhado|
|Serviço de pesquisa|✔|1. `testkb` é um nome reservado para o serviço QnAMaker; não pode ser usado por outros.<br>2. O mapa do sinónimo `synonym-map` pelo nome está reservado para o serviço QnAMaker.<br>3. O número de bases de conhecimento publicadas é limitado pelo nível de serviço de pesquisa. Se houver índices gratuitos disponíveis, outros serviços podem utilizá-los.|

### <a name="using-a-single-cognitive-search-service"></a>Usando um único serviço de pesquisa cognitiva

Se criar um serviço QnA e as suas dependências (como search) através do portal, é criado um serviço de Pesquisa para si e ligado ao serviço QnA Maker. Depois de criados estes recursos, pode atualizar a definição do Serviço de Aplicações para utilizar um serviço de Pesquisa anteriormente existente e remover o que acabou de criar.

Aprenda [a configurar o](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) QnA Maker para usar um recurso de Serviço Cognitivo diferente daquele criado como parte do processo de criação de recursos qnA Maker.

## <a name="management-service-region"></a>Região de serviçode gestão

O serviço de gestão da QnA Maker é utilizado apenas para o portal QnA Maker e para o processamento inicial de dados. Este serviço está disponível apenas na região **oeste dos EUA.** Não são armazenados dados de clientes neste serviço da West US.

## <a name="keys-in-qna-maker"></a>Chaves no Fabricante qna

O seu serviço QnA Maker lida com dois tipos de chaves: chaves de **autor** e **chaves finais** de consulta utilizadas com o tempo de execução hospedado no serviço app.

Se procura a sua chave de **subscrição,** [a terminologia mudou.](#subscription-keys)

Utilize estas chaves ao fazer pedidos ao serviço através de APIs.

![Gestão de chaves](../media/qnamaker-how-to-key-management/key-management.png)

|Nome|Localização|Objetivo|
|--|--|--|
|Chave de autoria|[Portal do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|Estas chaves são utilizadas para aceder às APIs do serviço de [gestão QnA Maker](https://go.microsoft.com/fwlink/?linkid=2092179). Estas APIs permitem-lhe editar as perguntas e respostas na sua base de conhecimentos e publicar a sua base de conhecimento. Estas chaves são criadas quando cria um novo serviço QnA Maker.<br><br>Encontre estas chaves no recurso dos **Serviços Cognitivos** na página **Keys.**|
|Chave final de consulta|[Portal do Criador de FAQ](https://www.qnamaker.ai)|Estas teclas são usadas para consultar o ponto final da base de conhecimento publicado para obter uma resposta para uma pergunta do utilizador. Normalmente utiliza este ponto final de consulta no seu chat bot ou no código de aplicação do cliente que se conecta ao serviço QnA Maker. Estas chaves são criadas quando publica a sua base de conhecimento qnA Maker.<br><br>Encontre estas teclas na página de definições do **Serviço.** Encontre esta página no menu do utilizador no canto superior direito da página no menu suspenso.|

### <a name="subscription-keys"></a>Chaves de subscrição

Os termos de autoria e consulta final são termos corretivos. O termo anterior era **a chave de subscrição.** Se vir outra documentação referindo-se às teclas de subscrição, estas são equivalentes a chaves finais de autoria e consulta (utilizadas no tempo de execução).

Deve saber qual é a chave de acesso, gestão da base de conhecimentos ou consulta da base de conhecimentos, para saber qual a chave que precisa de encontrar.

## <a name="next-steps"></a>Passos seguintes

* Conheça a base de [conhecimento](knowledge-base.md) do QnA Maker
* Compreender um ciclo de [vida base](development-lifecycle-knowledge-base.md) de conhecimento
* Rever [limites](../limits.md) de base de serviço e conhecimento

