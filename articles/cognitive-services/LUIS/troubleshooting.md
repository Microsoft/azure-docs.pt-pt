---
title: Perguntas frequentes – LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém respostas para perguntas mais frequentes sobre a compreensão de idiomas (LUIS).
author: diberry
manager: nitinme
ms.custom: seodec18
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: 3907a244bc2d85e7225f94b15150298fd80a032f
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382346"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Perguntas frequentes sobre o Reconhecimento vocal (FAQ)

Este artigo contém respostas para perguntas mais frequentes sobre a compreensão de idiomas (LUIS).

## <a name="whats-new"></a>Novidades

[Saiba mais](whats-new.md) sobre as novidades do reconhecimento vocal.

<a name="luis-authoring"></a>

## <a name="authoring"></a>Criação de conteúdos

### <a name="what-are-the-luis-best-practices"></a>Quais são as práticas recomendadas do LUIS?
Começar com o [ciclo de criação](luis-concept-app-iteration.md), em seguida, leia o [melhores práticas](luis-concept-best-practices.md).

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>O que é a melhor maneira de começar a criar meu aplicativo em LUIS?

A melhor forma de criar a sua aplicação é por meio de um [processo incremental](luis-concept-app-iteration.md).

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>O que é uma boa prática para modelar dos objetivos do meu aplicativo? Deve criar objetivos mais genéricos ou mais específicos?

Escolha os objetivos que não são tão gerais como para ser sobrepostos, mas não tão específica que ela torna difícil para o LUIS distinguir entre os objetivos semelhantes. Criar discriminative objetivos específicos é uma das práticas recomendadas para LUIS de modelagem.

### <a name="is-it-important-to-train-the-none-intent"></a>É importante preparar a intenção None?

Sim, é bom preparar sua **None** intenção com mais expressões com à medida que adiciona mais etiquetas para outros objetivos. Uma boa razão é 1 ou 2 etiquetas adicionadas ao **None** para todas as etiquetas de 10 adicionadas a um objetivo. Este rácio aumenta o poder discriminative do LUIS.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Como pode corrigir os erros de ortografia em expressões com?

Consulte a [Bing ortográfica verificar a API V7](luis-tutorial-bing-spellcheck.md) tutorial. LUIS impõe limites impostos pelo Bing ortográfica verificar a API V7.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Como faço para editar a minha aplicação LUIS por meio de programação?
Para editar a sua aplicação do LUIS programaticamente, utilize o [criação API](https://go.microsoft.com/fwlink/?linkid=2092087). Ver [LUIS chamar a API de criação](./luis-quickstart-node-add-utterance.md) e [criar uma aplicação do LUIS por meio de programação com node. js](./luis-tutorial-node-import-utterances-csv.md) para obter exemplos de como chamar a API de criação. A API de criação é necessário utilizar um [chave de criação](luis-concept-keys.md#authoring-key) em vez de uma chave de ponto final. A criação programática permite até 1 000 000 chamadas por mês e cinco transações por segundo. Para obter mais informações sobre as chaves a utilizar com os LUIS, consulte [gerir chaves](./luis-concept-keys.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Qual é a funcionalidade padrão que fornecia a expressão regular que corresponda?
Anterior **funcionalidade de padrão** atualmente é preterido, substituídos pelos  **[padrões](luis-concept-patterns.md)** .

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Como utilizo uma entidade para extrair os dados corretos?
Ver [entidades](luis-concept-entity-types.md) e [extração de dados](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Variações de uma expressão de exemplo devem incluir a pontuação?
Adicione as diferentes variações como expressões de exemplo para a intenção ou adicionar o padrão de expressão de exemplo com o [sintaxe para ignorar](luis-concept-patterns.md#pattern-syntax) a pontuação.

### <a name="does-luis-currently-support-cortana"></a>O LUIS suporta atualmente Cortana?

Cortana criados previamente as aplicações foram preteridas em 2017. Já não são suportadas.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Como transferir a propriedade de uma aplicação do LUIS?
Para transferir uma aplicação do LUIS para uma subscrição do Azure diferente, exportar a aplicação do LUIS e importe-o utilizando uma conta nova. Atualize o ID da aplicação LUIS no aplicativo cliente que chama-lo. A nova aplicação poderá devolver as LUIS ligeiramente diferente pontuações da aplicação original.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>Uma entidade predefinida é marcada em um exemplo de expressão em vez de minha entidade personalizada. Como fazer corrigir isso? 

Consulte [Solucionando problemas de entidades predefinidas](luis-concept-entity-types.md#troubleshooting-prebuilt-entities).

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Tentei importar um arquivo de aplicativo ou de versão, mas recebi um erro, o que aconteceu? 

Leia mais sobre [erros de importação de versão](luis-how-to-manage-versions.md#import-errors) e [erros de importação de aplicativo](luis-how-to-start-new-app.md#import-errors).

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>Colaborando e contribuindo

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-role-based-access-control-rbac"></a>Como fazer dar aos colaboradores acesso ao LUIS com o Azure Active Directory (Azure AD) ou o RBAC (controle de acesso baseado em função)?

Consulte [Azure Active Directory recursos](luis-how-to-collaborate.md#azure-active-directory-resources) e [Azure Active Directory usuário do locatário](luis-how-to-collaborate.md#azure-active-directory-tenant-user) para saber como dar acesso aos colaboradores. 

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Ponto Final

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>Recebi um código de status de erro HTTP 403. Como posso corrigi-lo?

Você obtém códigos de status de erro 403 e 429 ao exceder as transações por segundo ou transações por mês para o tipo de preço. Aumente seu tipo de preço ou use [contêineres](luis-container-howto.md)de reconhecimento vocal.

Quando você usar todas essas consultas de ponto de extremidade 1000 gratuitas ou exceder a cota de transações mensais do seu tipo de preço, você receberá um código de status de erro HTTP 403. 

Para corrigir esse erro, você precisa [alterar seu tipo de preço](luis-how-to-azure-subscription.md#change-pricing-tier) para uma camada superior ou [criar um novo recurso](get-started-portal-deploy-app.md#create-the-endpoint-resource) e [atribuí-lo ao seu aplicativo](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

As soluções para esse erro incluem:

* No [portal do Azure](https://portal.azure.com), em seu recurso de reconhecimento vocal, no **tipo de preço gerenciamento de recursos->** , altere o tipo de preço para uma camada mais alta do TPS. Você não precisa fazer nada no portal de Reconhecimento vocal se o recurso já estiver atribuído ao seu aplicativo Reconhecimento vocal.
*  Se seu uso exceder o tipo de preço mais alto, adicione mais recursos Reconhecimento vocal com um balanceador de carga na frente deles. O [contêiner reconhecimento vocal](luis-container-howto.md) com Kubernetes ou Docker Compose pode ajudar com isso.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>Recebi um código de status de erro HTTP 429. Como posso corrigi-lo?

Você obtém códigos de status de erro 403 e 429 ao exceder as transações por segundo ou transações por mês para o tipo de preço. Aumente seu tipo de preço ou use [contêineres](luis-container-howto.md)de reconhecimento vocal.

Esse código de status é retornado quando suas transações por segundo excedem seu tipo de preço.  

As soluções incluem:

* Você pode [aumentar seu tipo de preço](luis-how-to-azure-subscription.md#change-pricing-tier), se não estiver na camada mais alta.
* Se seu uso exceder o tipo de preço mais alto, adicione mais recursos Reconhecimento vocal com um balanceador de carga na frente deles. O [contêiner reconhecimento vocal](luis-container-howto.md) com Kubernetes ou Docker Compose pode ajudar com isso.
* Você pode portar suas solicitações de aplicativo cliente com uma [política de repetição](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) que você mesmo implementa quando obtém esse código de status. 

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>Minha consulta de ponto final devolvidos resultados inesperados. O que devo fazer?

Resultados da predição de consulta inesperada baseiam-se sobre o estado do modelo publicado. Para corrigir o modelo, talvez seja necessário alterar o modelo, treinar e publicar novamente. 

Como corrigir o modelo começa com [aprendizagem ativa](luis-how-to-review-endpoint-utterances.md).

Pode remover o treinamento determinística ao atualizar o [aplicação API de definições de versão](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) para utilizar todos os dados de treinamento.

Reveja os [melhores práticas](luis-concept-best-practices.md) para outras sugestões. 

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Porquê o LUIS adiciona espaços para a consulta em torno ou no meio de palavras?
LUIS [divide](luis-glossary.md#token) a expressão com base na [cultura](luis-language-support.md#tokenization). O valor original e o valor tokenized estão disponíveis para [extração de dados](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Como criar e atribuir um LUIS chave de ponto final?
[Criar a chave de ponto final](luis-how-to-azure-subscription.md) no Azure para a sua [serviço](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) nível. [Atribua a chave](luis-how-to-azure-subscription.md) na página de **[recursos do Azure](luis-how-to-azure-subscription.md)** . Não há nenhuma API correspondente para esta ação. Em seguida, tem de alterar o pedido HTTP para o ponto final para [utilizar a nova chave de ponto final](luis-concept-keys.md).

### <a name="how-do-i-interpret-luis-scores"></a>Como posso interpretar as pontuações de LUIS?
O sistema deve utilizar a intenção de classificação mais elevada, independentemente de seu valor. Por exemplo, uma pontuação inferior a 0,5 (menos de 50%) não significa necessariamente que o LUIS tem confiança baixa. Para disponibilizar formação mais dados podem ajudar a aumentar a [pontuação](luis-concept-prediction-score.md) da intenção mais prováveis.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Por que motivo não vejo minhas acertos de ponto final no Dashboard da minha aplicação?
As visitas de ponto de extremidade total no Dashboard da sua aplicação são atualizadas periodicamente, mas as métricas associadas à sua chave de ponto final de LUIS no portal do Azure são atualizadas com mais frequência.

Se você não vir os acertos de ponto de extremidade atualizados no painel, entre no portal do Azure, localize o recurso associado à sua chave de ponto de extremidade LUIS e abra as **métricas** para selecionar a métrica **total de chamadas** . Se a chave de ponto final é utilizada para mais do que uma aplicação do LUIS, a métrica no portal do Azure mostra o número agregado de chamadas de todas as aplicações de LUIS utilização-lo.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Há um comando do PowerShell que obtém a cota do ponto de extremidade?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Você pode usar um comando do PowerShell para ver a cota do ponto de extremidade:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
``` 

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>A minha aplicação LUIS estava trabalhando ontem, mas hoje em dia, eu tenho 403 erros. Eu não alterou a aplicação. Como posso corrigi-lo?
Siga estas [instruções](#how-do-i-create-and-assign-a-luis-endpoint-key) para criar uma chave de ponto de extremidade Luis e atribuí-la ao aplicativo. Em seguida, você deve alterar a solicitação HTTP do aplicativo cliente para o ponto de extremidade para [usar a nova chave de ponto de extremidade](luis-concept-keys.md). Se você criou um novo recurso em uma região diferente, altere também a região da solicitação de cliente HTTP.

### <a name="how-do-i-secure-my-luis-endpoint"></a>Como posso proteger meu ponto de final do LUIS?
Ver [proteger o ponto final](luis-concept-keys.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>Trabalhar dentro dos limites de LUIS

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>O que é o número máximo de intenções e entidades que pode oferecer suporte a uma aplicação do LUIS?
Consulte a [limites](luis-boundaries.md) referência.

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Quero criar uma aplicação de LUIS com mais do que o número máximo de objetivos. O que devo fazer?

Ver [melhores práticas para objetivos](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Quero criar uma aplicação no LUIS com mais do que o número máximo de entidades. O que devo fazer?

Consulte [melhores práticas para entidades](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Apresenta uma lista quais são os limites no número e tamanho de frase?
Para o comprimento máximo de um [lista de frase](./luis-concept-feature.md), consulte a [limites](luis-boundaries.md) referência.

### <a name="what-are-the-limits-on-example-utterances"></a>Quais são os limites em expressões de exemplo?
Consulte a [limites](luis-boundaries.md) referência.

## <a name="testing-and-training"></a>Teste e treinamento

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Posso ver alguns erros no lote painel de teste para alguns dos modelos em meu aplicativo. Como posso solucionar esse problema?

Os erros indicam que não há alguma discrepância entre as etiquetas e as previsões de seus modelos. Para resolver o problema, efetue uma ou ambas das seguintes tarefas:
* Para ajudar a melhorar discrimination entre os objetivos do LUIS, adicione mais etiquetas.
* Para ajudar a LUIS aprende mais depressa, adicione funcionalidades de lista de frase que apresentam o vocabulário específicas de domínio.

Consulte a [testes de Batch](luis-tutorial-batch-testing.md) tutorial.

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Quando uma aplicação é exportada, em seguida, novamente importada para uma nova aplicação (com um novo ID de aplicação), as pontuações de predição de LUIS são diferentes. Por que isso acontece?

Ver [diferenças de predição entre cópias da mesma aplicação](luis-concept-prediction-score.md#review-intents-with-similar-scores).

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Algumas expressões com até a intenção de errado depois que fiz alterações para a minha aplicação. O problema parece sumir aleatoriamente. Como posso corrigi-lo? 

Ver [Train com todos os dados](luis-how-to-train.md#train-with-all-data).

## <a name="app-publishing"></a>Publicação de aplicações

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>O que é o ID de inquilino na janela "Adicionar uma chave à sua aplicação"?
No Azure, um inquilino representa o cliente ou de uma organização que está associada um serviço. Encontrar o seu ID de inquilino no portal do Azure no **ID de diretório** caixa selecionando **Azure Active Directory** > **gerir**  >  **Propriedades**.

![ID do inquilino no portal do Azure](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Por que há mais chaves de ponto final atribuídas à minha aplicação que atribuí?
Cada aplicação LUIS tem a chave de criação/starter na lista de ponto de extremidade como uma conveniência. Esta chave permite apenas alguns cliques de ponto final para que pode experimentar o LUIS.  

Se a sua aplicação existia antes do LUIS foi em disponibilidade geral (GA), as chaves de ponto final do LUIS na sua subscrição são atribuídas automaticamente. Isso foi feito para facilitar a migração de disponibilidade geral. Quaisquer novas chaves de ponto final de LUIS no portal do Azure são _não_ atribuída automaticamente aos LUIS.

## <a name="key-management"></a>Gestão de chaves

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Como fazer saber qual é a chave de que preciso, onde posso obtê-la e o que faço com ela? 

Consulte [criação e consulta de chaves de ponto de extremidade de previsão em Luis](luis-concept-keys.md) para saber mais sobre as diferenças entre a chave de criação e a chave de tempo de execução de previsão. 

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Recebi um erro de ausência de cota. Como posso corrigi-lo? 

Consulte corrigir o código de status HTTP [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) e [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) para saber mais.

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>Preciso lidar com mais consultas de ponto de extremidade. Como fazer fazer isso? 

Consulte corrigir o código de status HTTP [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) e [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) para saber mais.

## <a name="app-management"></a>Gestão de aplicações

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Como posso transferir um log de expressões de utilizador?
Por predefinição, a sua aplicação LUIS regista expressões com dos utilizadores. Para transferir um log das expressões com que os utilizadores enviar para a sua aplicação LUIS, aceda a **as minhas aplicações**e selecione a aplicação. Na barra de ferramentas contextual, selecione **exportar registos de ponto final**. O registo é formatado como um ficheiro de valores separados por vírgulas (CSV).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Como posso desativar o registo de expressões com?
Pode desativar o registo de expressões do utilizador através da definição `log=false` no URL do ponto final que a aplicação cliente utiliza para consulta LUIS. No entanto, a desativação do registo desativa a capacidade da sua aplicação LUIS para sugerir discursos ou melhorar o desempenho com base no [aprendizagem ativa](luis-concept-review-endpoint-utterances.md#what-is-active-learning). Se definir `log=false` devido a problemas de privacidade de dados, não é possível transferir um registo dessas expressões de utilizador do LUIS ou utilizar essas expressões de com para melhorar a sua aplicação.

O registo é o armazenamento apenas de expressões.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Por que não quero que todos os meus expressões com ponto final com sessão iniciadas?
Se estiver a utilizar o início de sessão para análise de previsão, não capture expressões de teste no seu registo.

## <a name="data-management"></a>Gestão de dados

### <a name="can-i-delete-data-from-luis"></a>Pode eliminar dados do LUIS?

* Pode sempre eliminar as expressões de exemplo usadas para treinar o LUIS. Se eliminar uma expressão de exemplo da sua aplicação LUIS, ele é removido do serviço web LUIS e não está disponível para exportação.
* Pode eliminar expressões com na lista de expressões de utilizador que LUIS sugere no **rever expressões de ponto final** página. A eliminar expressões com desta lista evita que sejam sendo sugerida, mas não a eliminá-los a partir de registos.
* Se eliminar uma conta, todas as aplicações são eliminadas, juntamente com os seus registos e as expressões de exemplo. Os dados são mantidos nos servidores durante 60 dias antes de ser eliminado permanentemente.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Como a Microsoft gere dados de que envio para o LUIS?

O [Centro de fidedignidade](https://www.microsoft.com/trustcenter) explica nossos compromissos e as opções de gestão de dados e de acesso nos serviços do Azure.

## <a name="language-and-translation-support"></a>Suporte de idioma e de tradução

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Tenho uma aplicação numa linguagem e pretende criar uma aplicação paralela em outro idioma. O que é a maneira mais fácil de fazê-lo?
1. Exporte a sua aplicação.
2. Traduza as expressões etiquetadas no ficheiro JSON da aplicação exportado para o idioma de destino.
3. Poderá ter de alterar os nomes dos objetivos e entidades ou deixá-los como estão.
4. Por fim, importe a aplicação tenha uma aplicação do LUIS no idioma de destino.

## <a name="app-notification"></a>Notificação de aplicação

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Por que recebi uma mensagem de e-mail dizendo, estou quase fora da quota?
A chave de criação/starter só é permitida a 1000 consulta o ponto final de um mês. Crie uma chave de ponto final do LUIS (gratuita ou paga) e utilizar essa chave ao efetuar consultas de ponto final. Se estiver a efetuar consultas de ponto final de um bot ou outra aplicação de cliente, terá de alterar a chave de ponto final de LUIS lá.

## <a name="bots"></a>Bots

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>Meu bot LUIS não está funcionando. O que posso fazer?

O primeiro problema é isolar se o problema está relacionado a LUIS ou acontece fora do middleware LUIS. 

#### <a name="resolve-issue-in-luis"></a>Resolver o problema em LUIS
Passe o mesmo expressão para LUIS do [ponto de extremidade Luis](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint). Se você receber um erro, resolva o problema em LUIS até que o erro não seja mais retornado. Erros comuns incluem:

* `Out of call volume quota. Quota will be replenished in <time>.`-Esse problema indica que você precisa alterar de uma chave de criação para uma [chave de ponto de extremidade](luis-how-to-azure-subscription.md) ou precisa alterar as camadas de [serviço](luis-how-to-azure-subscription.md#change-pricing-tier). 

#### <a name="resolve-issue-in-azure-bot-service"></a>Resolver problema no serviço de bot do Azure

Se você estiver usando o serviço de bot do Azure e o problema for que o **teste no Web Chat** retorna `Sorry, my bot code is having an issue`, verifique seus logs:

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

## <a name="integrating-luis"></a>Integrar o LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Em que a minha aplicação LUIS é criada durante o processo de subscrição de bot de aplicação web do Azure?
Se selecionar um modelo do LUIS e selecionar a **selecione** botão no painel de modelo, o painel da esquerda é alterado para incluir o tipo de modelo e pede-lhe em que região para criar o modelo do LUIS. O processo de bot de aplicação web não cria, no entanto, uma subscrição do LUIS.

![Região do LUIS modelo web app bot](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Que regiões do LUIS suportam priming de voz de Bot Framework?
[Priming de voz](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) apenas é suportada para aplicações de LUIS na instância da central (E.U.A.).

## <a name="api-programming-strategies"></a>Estratégias de programação de API

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Como fazer obter de forma programática a região LUIS de um recurso? 

Use o exemplo LUIS para [localizar a região](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) programaticamente usando C# o ou o Node. js. 

## <a name="luis-service"></a>Serviço de LUIS

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>Compreensão de idiomas (LUIS) disponível no local ou na nuvem privada?

Sim, pode utilizar o LUIS [contentor](luis-container-howto.md) para estes cenários, se tiver a conectividade necessária para medir a utilização. 

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
* [Perguntas de estouro de pilha etiquetadas com os LUIS](https://stackoverflow.com/questions/tagged/luis)
* [Fórum de (LUIS) de serviços de compreensão inteligentes de idiomas do MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS)