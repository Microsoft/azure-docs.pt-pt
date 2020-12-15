---
title: Recursos Azure - QnA Maker
description: O Fabricante QnA usa várias fontes Azure, cada uma com um propósito diferente. Compreender como são usados individualmente permite-lhe planear e selecionar o nível de preços correto ou saber quando alterar o seu nível de preços. Compreender como são usados em combinação permite-lhe encontrar e corrigir problemas quando ocorrem.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: e2c71e028225c22ab0355ff4cda77dacadf94095
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97512050"
---
# <a name="azure-resources-for-qna-maker"></a>Recursos Azure para O Criador QNA

O Fabricante QnA usa várias fontes Azure, cada uma com um propósito diferente. Compreender como são usados individualmente permite-lhe planear e selecionar o nível de preços correto ou saber quando alterar o seu nível de preços. Compreender como são usados _em combinação_ permite-lhe encontrar e corrigir problemas quando ocorrem.

## <a name="resource-planning"></a>Planeamento de recursos

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

Quando desenvolve uma base de conhecimento QnA Maker, na fase do protótipo, é comum ter um único recurso QnA Maker tanto para testes como para produção.

Quando se entra na fase de desenvolvimento do projeto, deve considerar:

* Quantas línguas o seu sistema de base de conhecimento vai manter?
* Em quantas regiões precisa da sua base de conhecimentos para estar disponível?
* Quantos documentos em cada domínio o seu sistema irá conter?

Planeia ter um único recurso QnA Maker que possua todas as bases de conhecimento que tenham a mesma língua, a mesma região e a mesma combinação de domínios sujeitos.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

Quando desenvolve uma base de conhecimento gerida pela QnA Maker, na fase do protótipo, é comum ter um único recurso gerido pela QnA Maker tanto para testar como para a produção.

Quando se entra na fase de desenvolvimento do projeto, deve considerar:

* Quantas línguas o seu sistema de base de conhecimento vai manter?
* Em quantas regiões precisa da sua base de conhecimentos para estar disponível?
* Quantos documentos em cada domínio o seu sistema irá conter?

---

## <a name="pricing-tier-considerations"></a>Considerações de nível de preços

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

Normalmente, há três parâmetros que deve considerar:

* **A produção que precisa do serviço:**
    * Selecione o [Plano de Aplicações](https://azure.microsoft.com/pricing/details/app-service/plans/) apropriado para o seu serviço de Aplicações com base nas suas necessidades. Pode [escalar para cima](../../../app-service/manage-scale-up.md) ou para baixo na App.
    * Isto também deve influenciar a sua seleção Azure **Cognitive Search** SKU, ver mais detalhes [aqui.](../../../search/search-sku-tier.md) Além disso, poderá ser necessário ajustar a [capacidade de](../../../search/search-capacity-planning.md) Pesquisa Cognitiva com réplicas.

* **Tamanho e número de bases de conhecimento**: Escolha o [SKU de pesquisa Azure](https://azure.microsoft.com/pricing/details/search/) apropriado para o seu cenário. Normalmente, você decide o número de bases de conhecimento que precisa com base no número de diferentes domínios de assunto. Uma vez que o domínio do assunto (para uma única língua) deve estar numa base de conhecimento.

    Pode publicar bases de conhecimento N-1 num determinado nível, onde N é o índice máximo permitido no nível. Verifique também o tamanho máximo e o número de documentos permitidos por nível.

    Por exemplo, se o seu nível tiver 15 índices permitidos, pode publicar 14 bases de conhecimento (1 índice por base de conhecimentos publicados). O décimo quinto índice é utilizado para todas as bases de conhecimento para a autoria e teste.

* **Número de documentos como fontes**: O SKU gratuito do serviço de gestão QnA Maker limita o número de documentos que pode gerir através do portal e das APIs a 3 (de tamanho de 1 MB cada). O SKU padrão não tem limites para o número de documentos que pode gerir. Veja mais detalhes [aqui.](https://aka.ms/qnamaker-pricing)

A tabela a seguir dá-lhe algumas diretrizes de alto nível.

|                            | Gestão do Fabricante QNA | Serviço de Aplicações | Azure Cognitive Search | Limitações                      |
| -------------------------- | -------------------- | ----------- | ------------ | -------------------------------- |
| **Experimentação**        | SKU grátis             | Nível Gratuito   | Nível Gratuito    | Publicar até 2 KBs, tamanho de 50 MB  |
| **Ambiente dev/Teste**   | SKU Standard         | Partilhado      | Básico        | Publicar até 14 KBs, tamanho de 2 GB    |
| **Ambiente de Produção** | SKU Standard         | Básica       | Standard     | Publicar até 49 KBs, tamanho de 25 GB |

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

Normalmente, há três parâmetros que deve considerar:

* **A produção que precisa do serviço:**
    * QnA Maker gerido (Preview) é um serviço gratuito, e a produção está atualmente limitada a 10 TPS tanto para APIs de gestão como para APIs de previsão.
    * Isto também deve influenciar a sua seleção Azure **Cognitive Search** SKU, ver mais detalhes [aqui.](../../../search/search-sku-tier.md) Além disso, poderá ser necessário ajustar a [capacidade de](../../../search/search-capacity-planning.md) Pesquisa Cognitiva com réplicas.

* **Tamanho e número de bases de conhecimento**: Escolha o [SKU de pesquisa Azure](https://azure.microsoft.com/pricing/details/search/) apropriado para o seu cenário. Normalmente, você decide o número de bases de conhecimento que precisa com base no número de diferentes domínios de assunto. Uma vez que o domínio do assunto (para uma única língua) deve estar numa base de conhecimento.

    Com o QnA Maker gerido (Preview) tem a opção de configurar o seu serviço QnA Maker para KBs num único idioma ou em vários idiomas. Pode fazer esta seleção quando criar a primeira base de conhecimento no seu serviço QnA Maker gerido (Preview).

    ![O QnA Maker geriu (Preview) a seleção de base de conhecimento multilingue](../media/concept-plan-your-knowledge-base/qnamaker-v2-select-multilanguage-knowledge-base.png)

    Pode publicar bases de conhecimento N-1 de uma única língua ou bases de conhecimento N/2 de diferentes línguas num determinado nível, onde N é o índice máximo permitido no nível. Verifique também o tamanho máximo e o número de documentos permitidos por nível.

    Por exemplo, se o seu nível tiver 15 índices permitidos, pode publicar 14 bases de conhecimento da mesma língua (1 índice por base de conhecimento publicado). O décimo quinto índice é utilizado para todas as bases de conhecimento para a autoria e teste. Se optar por ter bases de conhecimento em diferentes línguas, só pode publicar 7 bases de conhecimento.

* **Número de documentos como fontes**: QnA Maker gerido (Preview) é um serviço gratuito, e não há limites para o número de documentos que pode adicionar como fontes. Veja mais detalhes [aqui.](https://aka.ms/qnamaker-pricing)

A tabela a seguir dá-lhe algumas diretrizes de alto nível.

|                            |Azure Cognitive Search | Limitações                      |
| -------------------------- |------------ | -------------------------------- |
| **Experimentação**        |Nível Gratuito    | Publicar até 2 KBs, tamanho de 50 MB  |
| **Ambiente dev/Teste**   |Básico        | Publicar até 14 KBs, tamanho de 2 GB    |
| **Ambiente de Produção** |Standard     | Publicar até 49 KBs, tamanho de 25 GB |

---

## <a name="recommended-settings"></a>Definições recomendadas

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

|QPS-alvo | Serviço de Aplicações | Azure Cognitive Search |
| -------------------- | ----------- | ------------ |
| 3             | S1, 1 Réplica   | S1, 1 Réplica    |
| 50         | S3, 10 Réplicas       | S1, 12 Réplicas         |
| 80         | S3, 10 Réplicas      |  S3, 12 Réplicas  |
| 100         | P3V2, 10 Réplicas  | S3, 12 Réplicas, 3 Divisórias   |
| 200 a 250         | P3V2, 20 Réplicas | S3, 12 Réplicas, 3 Divisórias    |

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

A QnA Maker gerida é um serviço gratuito, e a produção está atualmente limitada a 10 transações por segundo tanto para APIs de gestão como para APIs de previsão. Para direcionar 10 transações por segundo para o seu serviço recomendamos o S1 (1 exemplo) SKU da Azure Cognitive Search.

---

## <a name="when-to-change-a-pricing-tier"></a>Quando alterar um nível de preços

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

|Atualizar|Razão|
|--|--|
|[Upgrade](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) Gestão do Fabricante QNA SKU|Você quer ter mais pares QnA ou fontes de documento na sua base de conhecimento.|
|[Upgrade](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) Serviço de Aplicações SKU e verificar nível de Pesquisa Cognitiva e [criar réplicas de Pesquisa Cognitiva](../../../search/search-capacity-planning.md)|A sua base de conhecimentos precisa de servir mais pedidos da sua aplicação de clientes, como um chat bot.|
|[Upgrade](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Serviço de Pesquisa Cognitiva Azure|Planeia ter muitas bases de conhecimento.|

Obtenha as últimas atualizações de tempo de execução [atualizando o seu Serviço de Aplicações no portal Azure.](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

[Upgrade](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Serviço de Pesquisa Cognitiva Azure quando planeia ter muitas bases de conhecimento.

---

## <a name="resource-naming-considerations"></a>Considerações de nomeação de recursos

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

O nome de recurso para o recurso QnA Maker, `qna-westus-f0-b` como, também é usado para nomear os outros recursos.

O portal Azure cria uma janela que permite criar um recurso QnA Maker e selecionar os níveis de preços para os outros recursos.

> [!div class="mx-imgBorder"]
> ![Screenshot do portal Azure para a criação de recursos da QnA Maker](../media/concept-azure-resource/create-blade.png)

Após a criação dos recursos, têm o mesmo nome, com exceção do recurso opcional Application Insights, que publica caracteres para o nome.

> [!div class="mx-imgBorder"]
> ![Screenshot da listagem de recursos do portal Azure](../media/concept-azure-resource/all-azure-resources-created-qnamaker.png)

> [!TIP]
> Crie um novo grupo de recursos quando criar um recurso QnA Maker. Isso permite-lhe ver todos os recursos associados ao recurso QnA Maker ao pesquisar por grupo de recursos.

> [!TIP]
> Utilize uma convenção de nomeação para indicar os níveis de preços dentro do nome do recurso ou do grupo de recursos. Quando recebe erros de criar uma nova base de conhecimento, ou de adicionar novos documentos, o limite de preços de Pesquisa Cognitiva é uma questão comum.

### <a name="resource-purposes"></a>Finalidades de recursos

Cada recurso Azure criado com a QnA Maker tem um propósito específico:

* Recurso do Fabricante QnA
* Recurso de Pesquisa Cognitiva
* Serviço de Aplicações
* Serviço de Plano de Aplicações
* Serviço de Insights de Aplicações


### <a name="cognitive-search-resource"></a>Recurso de Pesquisa Cognitiva

O recurso [de Pesquisa Cognitiva](../../../search/index.yml) é utilizado para:

* Armazenar os pares QnA
* Fornecer o ranking inicial (ranking #1) dos pares QnA em tempo de execução

#### <a name="index-usage"></a>Utilização de índices

O recurso mantém um índice para funcionar como o índice de teste e os índices restantes correlacionam-se com uma base de conhecimento publicada cada.

Um recurso com preços a preços de 15 índices, terá 14 bases de conhecimento publicadas, e um índice é usado para testar todas as bases de conhecimento. Este índice de ensaio é dividido pela base de conhecimento de modo a que uma consulta utilizando o painel de ensaio interativo utilize o índice de teste, mas apenas os resultados de retorno da partição específica associada à base de conhecimento específica.

#### <a name="language-usage"></a>Uso da linguagem

A primeira base de conhecimento criada no recurso QnA Maker é usada para determinar o conjunto de linguagens _únicas_ para o recurso De Pesquisa Cognitiva e todos os seus índices. Só pode ter _um conjunto de idiomas_ para um serviço QnA Maker.

### <a name="qna-maker-resource"></a>Recurso do Fabricante QnA

O recurso QnA Maker fornece acesso às APIs de autoria e publicação, bem como à camada de segunda classificação baseada em linguagem natural (NLP) (ranker #2) dos pares QnA em tempo de execução.

O segundo ranking aplica filtros inteligentes que podem incluir metadados e pedidos de seguimento.

#### <a name="qna-maker-resource-configuration-settings"></a>Definições de configuração de recursos do QnA Maker

Quando cria uma nova base de conhecimento no [portal QnA Maker,](https://qnamaker.ai)a definição **de idioma** é a única definição que é aplicada ao nível dos recursos. Selecione o idioma quando criar a primeira base de conhecimento para o recurso.

### <a name="app-service-and-app-service-plan"></a>Serviço de aplicações e plano de serviço de aplicações

O [serviço App](../../../app-service/index.yml) é utilizado pela aplicação do seu cliente para aceder às bases de conhecimento publicadas através do ponto final de tempo de execução.

Para consultar a base de conhecimentos publicada, todas as bases de conhecimento publicadas utilizam o mesmo ponto final URL, mas especificam o **ID da base** de conhecimento dentro da rota.

`{RuntimeEndpoint}/qnamaker/knowledgebases/{kbId}/generateAnswer`

### <a name="application-insights"></a>Application Insights

[Application Insights](../../../azure-monitor/app/app-insights-overview.md) é usado para recolher registos de chat e telemetria. Reveja as [consultas comuns de Kusto](../how-to/get-analytics-knowledge-base.md) para obter informações sobre o seu serviço.

## <a name="share-services-with-qna-maker"></a>Partilhar serviços com a QnA Maker

A QnA Maker cria vários recursos Azure. Para reduzir a gestão e beneficiar da partilha de custos, use a seguinte tabela para entender o que pode e não pode partilhar:

|Serviço|Partilhar|Razão|
|--|--|--|
|Serviços Cognitivos|X|Não é possível por design|
|Plano do Serviço de Aplicações|✔|Espaço fixo em disco atribuído para um plano de Serviço de Aplicações. Se outras aplicações que partilham o mesmo plano de Serviço de Aplicações utilizarem espaço significativo em disco, a instância do Serviço de Aplicações QnAMaker irá encontrar problemas.|
|Serviço de Aplicações|X|Não é possível por design|
|Application Insights|✔|Pode ser partilhado|
|Serviço de pesquisa|✔|1. `testkb` é um nome reservado para o serviço QnAMaker; não pode ser utilizado por outros.<br>2. O mapa synonym com o nome `synonym-map` está reservado para o serviço QnAMaker.<br>3. O número de bases de conhecimento publicadas é limitado pelo nível de serviço de pesquisa. Se houver índices gratuitos disponíveis, outros serviços podem usá-los.|

### <a name="using-a-single-cognitive-search-service"></a>Usando um único serviço de Pesquisa Cognitiva

Se criar um serviço QnA e as suas dependências (como procurar) através do portal, é criado um serviço de Pesquisa para si e ligado ao serviço QnA Maker. Após a criação destes recursos, pode atualizar a definição de Serviço de Aplicações para utilizar um serviço de Pesquisa anteriormente existente e remover o que acabou de criar.

Aprenda [a configurar o](../How-To/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) QnA Maker para usar um recurso de Serviço Cognitivo diferente daquele criado como parte do processo de criação de recursos do QnA Maker.

## <a name="management-service-region"></a>Região de serviços de gestão

O serviço de gestão da QnA Maker é utilizado apenas para o portal QnA Maker e para o processamento inicial de dados. Este serviço está disponível apenas na região **oeste dos EUA.** Nenhum dado do cliente é armazenado neste serviço dos EUA.

## <a name="keys-in-qna-maker"></a>Chaves no Fabricante QnA

O seu serviço QnA Maker trata de dois tipos de chaves: chaves de **autoria** e **teclas de ponto final de consulta utilizadas** com o tempo de execução alojado no serviço App.

Se procura a sua **chave de subscrição,** [a terminologia mudou.](#subscription-keys)

Utilize estas teclas ao escrutinar os pedidos ao serviço através de APIs.

![Gestão de chaves](../media/qnamaker-how-to-key-management/key-management.png)

|Nome|Localização|Objetivo|
|--|--|--|
|Chave de autoria|[Portal do Azure](https://azure.microsoft.com/free/cognitive-services/)|Estas chaves são utilizadas para aceder às APIs do [serviço de gestão QnA Maker](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase). Estas APIs permitem-lhe editar as perguntas e respostas na sua base de conhecimento, e publicar a sua base de conhecimentos. Estas teclas são criadas quando cria um novo serviço QnA Maker.<br><br>Encontre estas chaves no recurso **serviços cognitivos** na página **Chaves.**|
|Chave de ponto final de consulta|[Portal do Criador de FAQ](https://www.qnamaker.ai)|Estas teclas são usadas para consultar o ponto final da base de conhecimento publicado para obter uma resposta para uma questão do utilizador. Normalmente, utiliza este ponto final de consulta no seu chat bot ou no código de aplicação do cliente que se conecta ao serviço QnA Maker. Estas chaves são criadas quando publica a sua base de conhecimento qnA Maker.<br><br>Encontre estas chaves na página **de definições de Serviço.** Encontre esta página a partir do menu do utilizador no canto superior direito da página no menu suspenso.|

### <a name="subscription-keys"></a>Chaves de subscrição

Os termos de autoria e chave de ponto final de consulta são termos corretivos. O termo anterior era **a chave de subscrição.** Se vir outra documentação referente às teclas de subscrição, estas são equivalentes às teclas de ponto final de autoria e consulta (utilizadas no tempo de execução).

Você deve saber qual a chave é o acesso, gestão de base de conhecimento ou consulta de base de conhecimento, para saber que chave você precisa encontrar.

### <a name="recommended-settings-for-network-isolation"></a>Configurações recomendadas para isolamento de rede

* Proteger o Recurso de Serviço Cognitivo do acesso público [configurando a rede virtual.](../../cognitive-services-virtual-networks.md?tabs=portal)
* Proteja o Serviço de Aplicações (QnA Runtime) do acesso público:
    * Permitir o tráfego apenas a partir de IPs do Serviço Cognitivo. Estes já estão incluídos na Tag de Serviço "CognitiveServicesManagement". Isto é necessário para que as APIs de Autoria (Create/Update KB) invoquem o serviço de aplicações e atualizem o serviço de Pesquisa Azure em conformidade.
    * Certifique-se de que também permite outros pontos de entrada como o serviço Bot, portal QnA Maker (pode ser a sua rede) etc. para previsão de acesso a API "GenerateAnswer".
    * Confira [mais informações sobre etiquetas de serviço.](../../../virtual-network/service-tags-overview.md)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

O nome de recurso para o recurso gerido (Preview) do QnA Maker, `qna-westus-f0-b` como, também é usado para nomear os outros recursos.

A janela de criação do portal Azure permite-lhe criar um recurso gerido (Preview) do QnA Maker e selecionar os níveis de preços para os outros recursos.

> [!div class="mx-imgBorder"]
> ![Screenshot do portal Azure para a criação de recursos (Preview) do QnA Maker ](../media/qnamaker-how-to-setup-service/enter-qnamaker-v2-info.png) Depois de criados os recursos, eles têm o mesmo nome.

> [!div class="mx-imgBorder"]
> ![Screenshot do recurso do portal Azure que lista o QnA Maker gerido (Preview)](../media/qnamaker-how-to-setup-service/resources-created-v2.png)
> [!TIP]
> Crie um novo grupo de recursos quando criar um recurso QnA Maker. Isso permite-lhe ver todos os recursos associados ao recurso gerido (Preview) do QnA Maker ao pesquisar por grupo de recursos.
> [!TIP]
> Utilize uma convenção de nomeação para indicar os níveis de preços dentro do nome do recurso ou do grupo de recursos. Quando recebe erros de criar uma nova base de conhecimento, ou de adicionar novos documentos, o limite de preços de Pesquisa Cognitiva é uma questão comum.

### <a name="resource-purposes"></a>Finalidades de recursos

Cada recurso Azure criado com qnA Maker gerido (Preview) tem um propósito específico:

* Recurso do Fabricante QnA
* Recurso de Pesquisa Cognitiva

### <a name="azure-cognitive-search-resource"></a>Recurso de Pesquisa Cognitiva Azure

O recurso [de Pesquisa Cognitiva](../../../search/index.yml) é utilizado para:

* Armazenar os pares QnA
* Fornecer o ranking inicial (ranking #1) dos pares QnA em tempo de execução

#### <a name="index-usage"></a>Utilização de índices

Pode publicar bases de conhecimento N-1 de uma única língua ou bases de conhecimento N/2 de diferentes línguas num determinado nível, onde N é o índice máximo permitido no nível de Pesquisa Cognitiva Azure. Verifique também o tamanho máximo e o número de documentos permitidos por nível.

Por exemplo, se o seu nível tiver 15 índices permitidos, pode publicar 14 bases de conhecimento da mesma língua (1 índice por base de conhecimento publicado). O décimo quinto índice é utilizado para todas as bases de conhecimento para a autoria e teste. Se optar por ter bases de conhecimento em diferentes línguas, só pode publicar 7 bases de conhecimento.

#### <a name="language-usage"></a>Uso da linguagem

Com o QnA Maker gerido (Preview) tem a opção de configurar o seu serviço QnA Maker para bases de conhecimento num único idioma ou em vários idiomas. Faça esta escolha durante a criação da primeira base de conhecimento no seu serviço QnA Maker. Veja [aqui](#pricing-tier-considerations) como permitir a definição de linguagem por base de conhecimento.

### <a name="qna-maker-resource"></a>Recurso do Fabricante QnA

O recurso gerido pelo QnA Maker (Preview) fornece acesso às APIs de autoria e publicação, acolhe o tempo de funcionamento do ranking, bem como fornece telemetria.

## <a name="region-support"></a>Suporte de região

Na QnA Maker gerido (Preview) tanto a gestão como os serviços de previsão são co-localizados na mesma região. Atualmente QnA Maker gerido (Preview) está disponível em **South Central EUA, Europa do Norte e Austrália Leste.**

### <a name="keys-in-qna-maker-managed-preview"></a>Chaves em QnA Maker geridas (Pré-visualização)

O seu serviço QnA Maker geriu (Preview) com dois tipos de chaves: chaves de **autoria** e **chaves de Pesquisa Cognitiva Azure utilizadas** para aceder ao serviço na subscrição do cliente.

Se procura a sua **chave de subscrição,** [a terminologia mudou.](#subscription-keys)

Utilize estas teclas ao escrutinar os pedidos ao serviço através de APIs.

![Pré-visualização gerida pela gestão de chaves](../media/qnamaker-how-to-key-management/qnamaker-v2-key-management.png)

|Nome|Localização|Objetivo|
|--|--|--|
|Chave de autoria|[Portal do Azure](https://azure.microsoft.com/free/cognitive-services/)|Estas chaves são utilizadas para aceder às APIs do [serviço de gestão QnA Maker](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase). Estas APIs permitem-lhe editar as perguntas e respostas na sua base de conhecimento, e publicar a sua base de conhecimentos. Estas teclas são criadas quando cria um novo serviço QnA Maker.<br><br>Encontre estas chaves no recurso **serviços cognitivos** na página **Chaves.**|
|Chave de administração de pesquisa cognitiva Azure|[Portal do Azure](../../../search/search-security-api-keys.md)|Estas teclas são utilizadas para comunicar com o serviço de pesquisa cognitiva Azure implantado na assinatura Azure do utilizador. Quando associa uma pesquisa cognitiva Azure ao serviço gerido (Preview) do QnA Maker, a tecla de administração é automaticamente transmitida para o serviço QnA Maker. <br><br>Pode encontrar estas chaves no recurso **Azure Cognitive Search** na página **Keys.**|

### <a name="subscription-keys"></a>Chaves de subscrição

Os termos de autoria e chave de ponto final de consulta são termos corretivos. O termo anterior era **a chave de subscrição.** Se vir outra documentação referente às teclas de subscrição, estas são equivalentes às teclas de ponto final de autoria e consulta (utilizadas no tempo de execução).

Você deve saber qual a chave é o acesso, gestão de base de conhecimento ou consulta de base de conhecimento, para saber que chave você precisa encontrar.

### <a name="recommended-settings-for-network-isolation"></a>Configurações recomendadas para isolamento de rede 

Proteger o Recurso de Serviço Cognitivo do acesso público [configurando a rede virtual.](../../cognitive-services-virtual-networks.md?tabs=portal)

---

## <a name="next-steps"></a>Passos seguintes

* Conheça a base de [conhecimentos](../index.yml) da QnA Maker
* Compreender um [ciclo de vida base de conhecimento](development-lifecycle-knowledge-base.md)
* Rever [limites](../limits.md) de base de serviço e conhecimento
