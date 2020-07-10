---
title: Recursos Azure - QnA Maker
description: O Fabricante QnA usa várias fontes Azure, cada uma com um propósito diferente. Compreender como são usados individualmente permite-lhe planear e selecionar o nível de preços correto ou saber quando alterar o seu nível de preços. Compreender como são usados em combinação permite-lhe encontrar e corrigir problemas quando ocorrem.
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 7d6598510ae75a76e0a4fcaff59297b4c724ba78
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171079"
---
# <a name="azure-resources-for-qna-maker"></a>Recursos Azure para O Criador QNA

O Fabricante QnA usa várias fontes Azure, cada uma com um propósito diferente. Compreender como são usados individualmente permite-lhe planear e selecionar o nível de preços correto ou saber quando alterar o seu nível de preços. Compreender como são usados _em combinação_ permite-lhe encontrar e corrigir problemas quando ocorrem.

## <a name="resource-planning"></a>Planeamento de recursos

Quando desenvolve uma base de conhecimento QnA Maker, na fase do protótipo, é comum ter um único recurso QnA Maker tanto para testes como para produção.

Quando se entra na fase de desenvolvimento do projeto, deve considerar:

* quantas línguas o seu sistema de base de conhecimento vai manter
* quantas regiões precisa da sua base de conhecimentos para estar disponível em/a partir
* quantos documentos em cada domínio o seu sistema irá conter

Planeia ter um único recurso QnA Maker que possua todas as bases de conhecimento que tenham a mesma língua, a mesma região e a mesma combinação de domínios sujeitos.

## <a name="pricing-tier-considerations"></a>Considerações de nível de preços

Normalmente, há três parâmetros que deve considerar:

* **A produção que precisa do serviço:**
    * Selecione o [Plano de Aplicações](https://azure.microsoft.com/pricing/details/app-service/plans/) apropriado para o seu serviço de Aplicações com base nas suas necessidades. Pode [escalar para cima](https://docs.microsoft.com/azure/app-service/manage-scale-up) ou para baixo na App.
    * Isto também deve influenciar a sua seleção Azure **Cognitive Search** SKU, ver mais detalhes [aqui.](https://docs.microsoft.com/azure/search/search-sku-tier) Além disso, poderá ser necessário ajustar a [capacidade de](../../../search/search-capacity-planning.md) Pesquisa Cognitiva com réplicas.

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

## <a name="recommended-settings"></a>Definições recomendadas

|QPS-alvo | Serviço de Aplicações | Azure Cognitive Search |
| -------------------- | ----------- | ------------ |
| 3             | S1, 1 Instância   | S1, 1 Instância    |
| 50         | S3, 10 Instâncias       | S1, 12 Instâncias         |
| 80         | S3, 10 Instâncias      |  S3, 12 Instâncias  |
| 100         | P3V2, 10 Instâncias  | S3, 12 Instâncias, 3 Partições   |
| 200 a 250         | P3V2, 20 Instâncias | S3, 12 Instâncias, 3 Partições    |

## <a name="when-to-change-a-pricing-tier"></a>Quando alterar um nível de preços

|Atualizar|Razão|
|--|--|
|[Upgrade](../How-to/set-up-qnamaker-service-azure.md#upgrade-qna-maker-sku) Gestão do Fabricante QNA SKU|Você quer ter mais pares QnA ou fontes de documento na sua base de conhecimento.|
|[Upgrade](../How-to/set-up-qnamaker-service-azure.md#upgrade-app-service) Serviço de Aplicações SKU e verificar nível de Pesquisa Cognitiva e [criar réplicas de Pesquisa Cognitiva](../../../search/search-capacity-planning.md)|A sua base de conhecimentos precisa de servir mais pedidos da sua aplicação de clientes, como um chat bot.|
|[Upgrade](../How-to/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service) Serviço de Pesquisa Cognitiva Azure|Planeia ter muitas bases de conhecimento.|

Obtenha as últimas atualizações de tempo de execução [atualizando o seu Serviço de Aplicações no portal Azure.](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)

## <a name="resource-naming-considerations"></a>Considerações de nomeação de recursos

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

## <a name="resource-purposes"></a>Finalidades de recursos

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
|Chave de autoria|[Portal do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)|Estas chaves são utilizadas para aceder às APIs do [serviço de gestão QnA Maker](https://go.microsoft.com/fwlink/?linkid=2092179). Estas APIs permitem-lhe editar as perguntas e respostas na sua base de conhecimento, e publicar a sua base de conhecimentos. Estas teclas são criadas quando cria um novo serviço QnA Maker.<br><br>Encontre estas chaves no recurso **serviços cognitivos** na página **Chaves.**|
|Chave de ponto final de consulta|[Portal do Criador de FAQ](https://www.qnamaker.ai)|Estas teclas são usadas para consultar o ponto final da base de conhecimento publicado para obter uma resposta para uma questão do utilizador. Normalmente, utiliza este ponto final de consulta no seu chat bot ou no código de aplicação do cliente que se conecta ao serviço QnA Maker. Estas chaves são criadas quando publica a sua base de conhecimento qnA Maker.<br><br>Encontre estas chaves na página **de definições de Serviço.** Encontre esta página a partir do menu do utilizador no canto superior direito da página no menu suspenso.|

### <a name="subscription-keys"></a>Chaves de subscrição

Os termos de autoria e chave de ponto final de consulta são termos corretivos. O termo anterior era **a chave de subscrição.** Se vir outra documentação referente às teclas de subscrição, estas são equivalentes às teclas de ponto final de autoria e consulta (utilizadas no tempo de execução).

Você deve saber qual a chave é o acesso, gestão de base de conhecimento ou consulta de base de conhecimento, para saber que chave você precisa encontrar.

## <a name="recommended-settings-for-network-isolation"></a>Configurações recomendadas para isolamento de rede

* Proteger o Recurso de Serviço Cognitivo do acesso público [configurando a rede virtual.](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks?tabs=portal)
* Proteja o Serviço de Aplicações (QnA Runtime) do acesso público:
    * Permitir o tráfego apenas a partir de IPs do Serviço Cognitivo. Estes já estão incluídos na Tag de Serviço "CognitiveServicesManagement". Isto é necessário para que as APIs de Autoria (Create/Update KB) invoquem o serviço de aplicações e atualizem o serviço de Pesquisa Azure em conformidade.
    * Certifique-se de que também permite outros pontos de entrada como o serviço Bot, portal QnA Maker (pode ser a sua rede) etc. para previsão de acesso a API "GenerateAnswer".
    * Confira [mais informações sobre etiquetas de serviço.](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

## <a name="next-steps"></a>Passos seguintes

* Conheça a base de [conhecimentos](knowledge-base.md) da QnA Maker
* Compreender um [ciclo de vida base de conhecimento](development-lifecycle-knowledge-base.md)
* Rever [limites](../limits.md) de base de serviço e conhecimento

