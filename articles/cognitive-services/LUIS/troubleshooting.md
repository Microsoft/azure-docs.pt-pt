---
title: Perguntas frequentes – LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém respostas para perguntas frequentes sobre o Reconhecimento vocal (LUIS).
author: diberry
manager: nitinme
ms.custom: seodec18
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 6a17993d7bc4ff54b3d55fa5b5bb141463896e32
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488700"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Perguntas frequentes sobre o Reconhecimento vocal (FAQ)

Este artigo contém respostas para perguntas frequentes sobre o Reconhecimento vocal (LUIS).

## <a name="whats-new"></a>Novidades

[Saiba mais](whats-new.md) sobre as novidades do reconhecimento vocal.

<a name="luis-authoring"></a>

## <a name="authoring"></a>Criação

### <a name="what-are-the-luis-best-practices"></a>Quais são as práticas recomendadas do LUIS?
Comece com o [ciclo de criação](luis-concept-app-iteration.md)e leia as [práticas recomendadas](luis-concept-best-practices.md).

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Qual é a melhor maneira de começar a criar meu aplicativo no LUIS?

A melhor maneira de criar seu aplicativo é por meio de um [processo incremental](luis-concept-app-iteration.md).

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>O que é uma boa prática para modelar as intenções do meu aplicativo? Devo criar tentativas mais específicas ou mais genéricas?

Escolha as intenções que não são tão gerais quanto serem sobrepostas, mas não tão específica que torna difícil para LUIS distinguir entre tentativas semelhantes. A criação de intenções específicas discriminadas é uma das práticas recomendadas para a modelagem LUIS.

### <a name="is-it-important-to-train-the-none-intent"></a>É importante treinar a intenção de nenhum?

Sim, é bom treinar sua intenção de **nenhum** com mais declarações à medida que você adiciona mais rótulos a outras intenções. Uma boa taxa é 1 ou 2 rótulos adicionados a **nenhum** para cada 10 rótulos adicionados a uma intenção. Essa taxa impulsiona a potência discriminada do LUIS.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Como posso corrigir erros de ortografia no declarações?

Consulte o tutorial da [API V7 verificação ortográfica do Bing](luis-tutorial-bing-spellcheck.md) . O LUIS impõe os limites impostos pela API de Verificação Ortográfica do Bing v7.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Como fazer editar meu aplicativo LUIS de forma programática?
Para editar seu aplicativo LUIS de forma programática, use a [API de criação](https://go.microsoft.com/fwlink/?linkid=2092087). Consulte [chamar a API de criação do Luis](./luis-quickstart-node-add-utterance.md) e [COMPILAR um aplicativo Luis programaticamente usando node. js](./luis-tutorial-node-import-utterances-csv.md) para obter exemplos de como chamar a API de criação. A API de criação requer que você use uma [chave de criação](luis-concept-keys.md#azure-resources-for-luis) em vez de uma chave de ponto de extremidade. A criação programática permite até 1 milhão chamadas por mês e cinco transações por segundo. Para obter mais informações sobre as chaves que você usa com o LUIS, consulte [gerenciar chaves](./luis-concept-keys.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Onde está o recurso padrão que fornece correspondência de expressão regular?
O **recurso de padrão** anterior está atualmente preterido, substituído por **[padrões](luis-concept-patterns.md)** .

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Como fazer usar uma entidade para extrair os dados corretos?
Confira [entidades](luis-concept-entity-types.md) e [extração de dados](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Se as variações de um exemplo expressão incluem Pontuação?
Adicione as diferentes variações como exemplo declarações à intenção ou adicione o padrão do expressão de exemplo com a [sintaxe para ignorar](luis-concept-patterns.md#pattern-syntax) a pontuação.

### <a name="does-luis-currently-support-cortana"></a>O LUIS atualmente dá suporte à Cortana?

Aplicativos predefinidos da Cortana foram preteridos em 2017. Eles não têm mais suporte.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Como fazer transferir a propriedade de um aplicativo LUIS?
Para transferir um aplicativo LUIS para uma assinatura diferente do Azure, exporte o aplicativo LUIS e importe-o usando uma nova conta. Atualize a ID do aplicativo LUIS no aplicativo cliente que a chama. O novo aplicativo pode retornar pontuações de LUIS ligeiramente diferentes do aplicativo original.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>Uma entidade predefinida é marcada em um exemplo de expressão em vez de minha entidade personalizada. Como fazer corrigir isso? 

No portal do LUIS, você pode rotular o texto para a entidade exata que você está interessado em extrair. Se o portal do LUIS não estiver mostrando a previsão de entidade correta, talvez seja necessário adicionar mais declarações e rotular a entidade dentro do texto ou adicionar um descritor (como um recurso). 

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Tentei importar um arquivo de aplicativo ou de versão, mas recebi um erro, o que aconteceu? 

Leia mais sobre [erros de importação de versão](luis-how-to-manage-versions.md#import-errors) e [erros de importação de aplicativo](luis-how-to-start-new-app.md#import-errors).

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>Colaborando e contribuindo

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-role-based-access-control-rbac"></a>Como fazer dar aos colaboradores acesso ao LUIS com o Azure Active Directory (Azure AD) ou o RBAC (controle de acesso baseado em função)?

Consulte [Azure Active Directory recursos](luis-how-to-collaborate.md#azure-active-directory-resources) e [Azure Active Directory usuário do locatário](luis-how-to-collaborate.md#azure-active-directory-tenant-user) para saber como dar acesso aos colaboradores. 

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Ponto Final

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>Recebi um código de status de erro HTTP 403. Como fazer corrigi-lo?

Você obtém códigos de status de erro 403 e 429 ao exceder as transações por segundo ou transações por mês para o tipo de preço. Aumente seu tipo de preço ou use [contêineres](luis-container-howto.md)de reconhecimento vocal.

Quando você usar todas essas consultas de ponto de extremidade 1000 gratuitas ou exceder a cota de transações mensais do seu tipo de preço, você receberá um código de status de erro HTTP 403. 

Para corrigir esse erro, você precisa [alterar seu tipo de preço](luis-how-to-azure-subscription.md#change-pricing-tier) para uma camada superior ou [criar um novo recurso](get-started-portal-deploy-app.md#create-the-endpoint-resource) e [atribuí-lo ao seu aplicativo](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

As soluções para esse erro incluem:

* No [portal do Azure](https://portal.azure.com), em seu recurso de reconhecimento vocal, no **tipo de preço gerenciamento de recursos->** , altere o tipo de preço para uma camada mais alta do TPS. Você não precisa fazer nada no portal de Reconhecimento vocal se o recurso já estiver atribuído ao seu aplicativo Reconhecimento vocal.
*  Se seu uso exceder o tipo de preço mais alto, adicione mais recursos Reconhecimento vocal com um balanceador de carga na frente deles. O [contêiner reconhecimento vocal](luis-container-howto.md) com Kubernetes ou Docker Compose pode ajudar com isso.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>Recebi um código de status de erro HTTP 429. Como fazer corrigi-lo?

Você obtém códigos de status de erro 403 e 429 ao exceder as transações por segundo ou transações por mês para o tipo de preço. Aumente seu tipo de preço ou use [contêineres](luis-container-howto.md)de reconhecimento vocal.

Esse código de status é retornado quando suas transações por segundo excedem seu tipo de preço.  

As soluções incluem:

* Você pode [aumentar seu tipo de preço](luis-how-to-azure-subscription.md#change-pricing-tier), se não estiver na camada mais alta.
* Se seu uso exceder o tipo de preço mais alto, adicione mais recursos Reconhecimento vocal com um balanceador de carga na frente deles. O [contêiner reconhecimento vocal](luis-container-howto.md) com Kubernetes ou Docker Compose pode ajudar com isso.
* Você pode portar suas solicitações de aplicativo cliente com uma [política de repetição](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) que você mesmo implementa quando obtém esse código de status. 

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>Minha consulta de ponto de extremidade retornou resultados inesperados. O que devo fazer?

Resultados de previsão de consulta inesperados são baseados no estado do modelo publicado. Para corrigir o modelo, talvez seja necessário alterar o modelo, treinar e publicar novamente. 

A correção do modelo começa com o [aprendizado ativo](luis-how-to-review-endpoint-utterances.md).

Você pode remover o treinamento não determinístico atualizando a [API de configurações de versão do aplicativo](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) para usar todos os dados de treinamento.

Examine as [práticas recomendadas](luis-concept-best-practices.md) para obter outras dicas. 

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Por que o LUIS adiciona espaços à consulta ou no meio de palavras?
LUIS [cria tokens](luis-glossary.md#token) expressão com base na [cultura](luis-language-support.md#tokenization). O valor original e o valor de token estão disponíveis para [extração de dados](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Como fazer criar e atribuir uma chave de ponto de extremidade LUIS?
[Crie a chave do ponto de extremidade](luis-how-to-azure-subscription.md) no Azure para seu nível de [serviço](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) . [Atribua a chave](luis-how-to-azure-subscription.md) na página de **[recursos do Azure](luis-how-to-azure-subscription.md)** . Não há uma API correspondente para esta ação. Em seguida, você deve alterar a solicitação HTTP para o ponto de extremidade para [usar a nova chave de ponto de extremidade](luis-concept-keys.md).

### <a name="how-do-i-interpret-luis-scores"></a>Como fazer interpretar pontuações de LUIS?
O sistema deve usar a tentativa de pontuação mais alta, independentemente de seu valor. Por exemplo, uma pontuação abaixo de 0,5 (menos de 50%) Não significa necessariamente que LUIS tem pouca confiança. Fornecer mais dados de treinamento pode ajudar a aumentar a [Pontuação](luis-concept-prediction-score.md) da intenção mais provável.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Por que não vejo meus acertos de ponto de extremidade no painel do meu aplicativo?
Os acertos totais de ponto de extremidade no painel do aplicativo são atualizados periodicamente, mas as métricas associadas à sua chave de ponto de extremidade LUIS no portal do Azure são atualizadas com mais frequência.

Se você não vir os acertos de ponto de extremidade atualizados no painel, entre no portal do Azure, localize o recurso associado à sua chave de ponto de extremidade LUIS e abra as **métricas** para selecionar a métrica **total de chamadas** . Se a chave do ponto de extremidade for usada para mais de um aplicativo LUIS, a métrica no portal do Azure mostrará o número agregado de chamadas de todos os aplicativos LUIS que o utilizam.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Há um comando do PowerShell que obtém a cota do ponto de extremidade?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Você pode usar um comando do PowerShell para ver a cota do ponto de extremidade:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
``` 

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>Meu aplicativo LUIS estava funcionando ontem, mas hoje estou recebendo erros 403. Eu não alterei o aplicativo. Como fazer corrigi-lo?
Siga estas [instruções](#how-do-i-create-and-assign-a-luis-endpoint-key) para criar uma chave de ponto de extremidade Luis e atribuí-la ao aplicativo. Em seguida, você deve alterar a solicitação HTTP do aplicativo cliente para o ponto de extremidade para [usar a nova chave de ponto de extremidade](luis-concept-keys.md). Se você criou um novo recurso em uma região diferente, altere também a região da solicitação de cliente HTTP.

### <a name="how-do-i-secure-my-luis-endpoint"></a>Como fazer proteger meu ponto de extremidade LUIS?
Consulte [protegendo o ponto de extremidade](luis-concept-keys.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>Trabalhando em limites de LUIS

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Qual é o número máximo de tentativas e entidades às quais um aplicativo LUIS pode dar suporte?
Consulte a referência de [limites](luis-boundaries.md) .

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Quero criar um aplicativo LUIS com mais do que o número máximo de tentativas. O que devo fazer?

Consulte [práticas recomendadas para tentativas](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Quero criar um aplicativo em LUIS com mais do que o número máximo de entidades. O que devo fazer?

Veja [as práticas recomendadas para entidades](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Quais são os limites do número e do tamanho das listas de frases?
Para obter o comprimento máximo de uma [lista de frases](./luis-concept-feature.md), consulte a referência de [limites](luis-boundaries.md) .

### <a name="what-are-the-limits-on-example-utterances"></a>Quais são os limites do exemplo declarações?
Consulte a referência de [limites](luis-boundaries.md) .

## <a name="testing-and-training"></a>Teste e treinamento

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Vejo alguns erros no painel de teste do lote para alguns dos modelos em meu aplicativo. Como posso resolver esse problema?

Os erros indicam que há alguma discrepância entre seus rótulos e as previsões de seus modelos. Para resolver o problema, execute uma ou ambas as tarefas a seguir:
* Para ajudar a LUIS a melhorar a discriminação entre as intenções, adicione mais rótulos.
* Para ajudar a LUIS a aprender mais rapidamente, adicione recursos de lista de frases que introduzem vocabulário específico de domínio.

Consulte o tutorial de [teste em lote](luis-tutorial-batch-testing.md) .

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Quando um aplicativo é exportado e, em seguida, reimportado para um novo aplicativo (com uma nova ID de aplicativo), as pontuações de previsão de LUIS são diferentes. Por que isso acontece?

Consulte [diferenças de previsão entre cópias do mesmo aplicativo](luis-concept-prediction-score.md#review-intents-with-similar-scores).

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Alguns declarações vão para a intenção errada depois de fazer alterações no meu aplicativo. O problema parece desaparecer aleatoriamente. Como fazer corrigi-lo? 

Confira [treinar com todos os dados](luis-how-to-train.md#train-with-all-data).

## <a name="app-publishing"></a>Publicação de aplicativo

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Qual é a ID de locatário na janela "adicionar uma chave ao seu aplicativo"?
No Azure, um locatário representa o cliente ou a organização que está associada a um serviço. Localize sua ID de locatário no portal do Azure na caixa **ID de diretório** selecionando **Azure Active Directory** > **gerenciar** > **Propriedades**.

![ID do locatário no portal do Azure](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Por que há mais chaves de ponto de extremidade atribuídas ao meu aplicativo do que eu atribuí?
Cada aplicativo LUIS tem a chave de criação/iniciante na lista de pontos de extremidade como uma conveniência. Essa chave permite apenas algumas ocorrências de ponto de extremidade para que você possa experimentar o LUIS.  

Se seu aplicativo existia antes de o LUIS ter sido disponibilizado (GA), as chaves de ponto de extremidade LUIS em sua assinatura serão atribuídas automaticamente. Isso foi feito para facilitar a migração do GA. Todas as novas chaves de ponto de extremidade LUIS no portal do Azure _não_ são atribuídas automaticamente a Luis.

## <a name="key-management"></a>Gestão de chaves

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Como fazer saber qual é a chave de que preciso, onde posso obtê-la e o que faço com ela? 

Consulte [criação e consulta de chaves de ponto de extremidade de previsão em Luis](luis-concept-keys.md) para saber mais sobre as diferenças entre a chave de criação e a chave de tempo de execução de previsão. 

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Recebi um erro de ausência de cota. Como fazer corrigi-lo? 

Consulte corrigir o código de status HTTP [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) e [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) para saber mais.

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>Preciso lidar com mais consultas de ponto de extremidade. Como fazer fazer isso? 

Consulte corrigir o código de status HTTP [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) e [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) para saber mais.

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>Criei uma chave de criação, mas ela não está sendo exibida no portal do LUIS. O que aconteceu?

As chaves de criação estão disponíveis no portal do LUIS após [a migração para a experiência de chave de criação](luis-migration-authoring.md).  

## <a name="app-management"></a>Gerenciamento de aplicativos

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Como fazer baixar um log do usuário declarações?
Por padrão, seu aplicativo LUIS registra declarações de usuários. Para baixar um log de declarações que os usuários enviam para seu aplicativo LUIS, vá para **meus aplicativos**e selecione o aplicativo. Na barra de ferramentas contextual, selecione **Exportar logs de ponto de extremidade**. O log é formatado como um arquivo CSV (valores separados por vírgula).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Como posso desabilitar o registro em log de declarações?
Você pode desativar o registro em log do usuário declarações definindo `log=false` na URL do ponto de extremidade que seu aplicativo cliente usa para consultar o LUIS. No entanto, desativar o registro desabilita a capacidade do seu aplicativo LUIS de sugerir declarações ou melhorar o desempenho com base no [aprendizado ativo](luis-concept-review-endpoint-utterances.md#what-is-active-learning). Se você definir `log=false` devido a questões de privacidade de dados, não poderá baixar um registro desses usuários declarações de LUIS ou usar esses declarações para melhorar seu aplicativo.

O registro em log é o único armazenamento de declarações.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Por que não quero que todos os meus pontos de extremidade declarações registrados?
Se você estiver usando seu log para análise de previsão, não Capture declarações de teste em seu log.

## <a name="data-management"></a>Gestão de dados

### <a name="can-i-delete-data-from-luis"></a>Posso excluir dados do LUIS?

* Você sempre pode excluir o exemplo declarações usado para treinar o LUIS. Se você excluir um exemplo de expressão de seu aplicativo LUIS, ele será removido do serviço Web LUIS e não estará disponível para exportação.
* Você pode excluir declarações da lista de usuários declarações que o LUIS sugere na página **revisar declarações do ponto de extremidade** . Excluir declarações dessa lista impede que eles sejam sugeridos, mas não os exclui dos logs.
* Se você excluir uma conta, todos os aplicativos serão excluídos, juntamente com seus declarações de exemplo e logs. Os dados são retidos nos servidores por 60 dias antes de serem excluídos permanentemente.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Como a Microsoft gerencia dados que envio para o LUIS?

A [central de confiabilidade](https://www.microsoft.com/trustcenter) explica nossos compromissos e suas opções de gerenciamento de dados e acesso nos serviços do Azure.

## <a name="language-and-translation-support"></a>Suporte à linguagem e tradução

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Tenho um aplicativo em uma linguagem e desejamos criar um aplicativo paralelo em outro idioma. Qual é a maneira mais fácil de fazer isso?
1. Exporte seu aplicativo.
2. Traduza o declarações rotulado no arquivo JSON do aplicativo exportado para o idioma de destino.
3. Talvez seja necessário alterar os nomes das intenções e entidades ou deixá-las como estão.
4. Por fim, importe o aplicativo para ter um aplicativo LUIS no idioma de destino.

## <a name="app-notification"></a>Notificação do aplicativo

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Por que eu recebi um email dizendo que estou quase sem cota?
Sua chave de criação/início é permitida apenas 1000 consultas de ponto de extremidade por mês. Crie uma chave de ponto de extremidade LUIS (gratuita ou paga) e use essa chave ao fazer consultas de ponto de extremidade. Se estiver fazendo consultas de ponto de extremidade de um bot ou outro aplicativo cliente, você precisará alterar a chave do ponto de extremidade LUIS.

## <a name="bots"></a>Bots

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>Meu bot LUIS não está funcionando. O que devo fazer?

O primeiro problema é isolar se o problema está relacionado a LUIS ou acontece fora do middleware LUIS. 

#### <a name="resolve-issue-in-luis"></a>Resolver o problema em LUIS
Passe o mesmo expressão para LUIS do [ponto de extremidade Luis](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint). Se você receber um erro, resolva o problema em LUIS até que o erro não seja mais retornado. Os erros comuns incluem:

* `Out of call volume quota. Quota will be replenished in <time>.`-esse problema indica que você precisa alterar de uma chave de criação para uma [chave de ponto de extremidade](luis-how-to-azure-subscription.md) ou você precisa alterar as camadas de [serviço](luis-how-to-azure-subscription.md#change-pricing-tier). 

#### <a name="resolve-issue-in-azure-bot-service"></a>Resolver problema no serviço de bot do Azure

Se você estiver usando o serviço de bot do Azure e o problema é que o **teste no Web Chat** retorna `Sorry, my bot code is having an issue`, verifique os logs:

1. Na portal do Azure, para o bot, na seção **Gerenciamento de bot** , selecione **Compilar**.
1. Abra o editor de código online. 
1. Na parte superior, barra de navegação azul, selecione o nome do bot (o segundo item à direita).
1. Na lista suspensa resultante, selecione **Abrir console do kudu**.
1. Selecione **LogFiles**e, em seguida, selecione **aplicativo**. Examine todos os arquivos de log. Se você não vir o erro na pasta do aplicativo, examine todos os arquivos de log em **LogFiles**. 
1. Lembre-se de recompilar seu projeto se você estiver usando uma linguagem C#compilada, como.

> [!Tip] 
> O console também pode instalar pacotes. 

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Resolva o problema durante a depuração no computador local com o bot Framework. 

Para saber mais sobre a depuração local de um bot, consulte [depurar um bot](https://docs.microsoft.com/azure/bot-service/bot-service-debug-bot?view=azure-bot-service-4.0).

## <a name="integrating-luis"></a>Integração do LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Onde meu aplicativo LUIS é criado durante o processo de assinatura de bot do aplicativo Web do Azure?
Se você selecionar um modelo LUIS e selecionar o botão **selecionar** no painel modelo, o painel do lado esquerdo será alterado para incluir o tipo de modelo e perguntará em qual região criar o modelo Luis. No entanto, o processo de bot do aplicativo Web não cria uma assinatura do LUIS.

![Região de bot do aplicativo Web do modelo LUIS](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Quais regiões do LUIS dão suporte à espriming de fala do bot Framework?
Só há suporte para a [priming de fala](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) em aplicativos Luis na instância central (EUA).

## <a name="api-programming-strategies"></a>Estratégias de programação de API

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Como fazer obter de forma programática a região LUIS de um recurso? 

Use o exemplo LUIS para [localizar a região](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) programaticamente usando C# o ou o Node. js. 

## <a name="luis-service"></a>Serviço LUIS

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>O é Reconhecimento vocal (LUIS) disponível localmente ou na nuvem privada?

Sim, você pode usar o [contêiner](luis-container-howto.md) Luis para esses cenários se tiver a conectividade necessária para medir o uso. 

## <a name="migrating-to-the-next-version"></a>Migrando para a próxima versão

### <a name="how-do-i-migrate-to-preview-v3-api"></a>Como fazer migrar para a API da visualização v3? 

Consulte o [Guia de migração da API v2 para V3 para aplicativos Luis](luis-migration-api-v3.md)

## <a name="build-2019-conference-announcements"></a>Crie anúncios de conferência 2019

Os seguintes recursos foram lançados na conferência Build 2019:

* [Visualização do guia de migração da API v3](luis-migration-api-v3.md)
* [Painel analítico aprimorado](luis-how-to-use-dashboard.md)
* [Domínios predefinidos aprimorados](luis-reference-prebuilt-domains.md) 
* [Entidades de lista dinâmica](luis-migration-api-v3.md#dynamic-lists-passed-in-at-prediction-time)
* [Entidades externas](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

Vídeos:

* [Como usar o AI de conversa do Azure para dimensionar sua empresa para a próxima geração](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o LUIS, consulte os seguintes recursos:
* [Stack Overflow perguntas marcadas com LUIS](https://stackoverflow.com/questions/tagged/luis)
* [Fórum do LUIS (serviços inteligentes) do MSDN Reconhecimento vocal](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS)