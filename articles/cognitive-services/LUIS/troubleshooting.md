---
title: Perguntas frequentes (FAQ) - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém respostas para perguntas mais frequentes sobre a compreensão de idiomas (LUIS).
author: diberry
manager: nitinme
ms.custom: seodec18
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: a2472064720af0a25568a2f173b971898b1f2e25
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219711"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Compreensão da linguagem Perguntas Frequentes (FAQ)

Este artigo contém respostas para perguntas mais frequentes sobre a compreensão de idiomas (LUIS).

## <a name="whats-new"></a>Novidades

[Saiba mais](whats-new.md) sobre o que há de novo na Compreensão da Língua (LUIS).

<a name="luis-authoring"></a>

## <a name="authoring"></a>Criação

### <a name="what-are-the-luis-best-practices"></a>Quais são as práticas recomendadas do LUIS?
Comece com o [Ciclo de Autoria,](luis-concept-app-iteration.md)depois leia as [melhores práticas.](luis-concept-best-practices.md)

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>O que é a melhor maneira de começar a criar meu aplicativo em LUIS?

A melhor forma de construir a sua aplicação é através de um [processo incremental.](luis-concept-app-iteration.md)

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>O que é uma boa prática para modelar dos objetivos do meu aplicativo? Deve criar objetivos mais genéricos ou mais específicos?

Escolha os objetivos que não são tão gerais como para ser sobrepostos, mas não tão específica que ela torna difícil para o LUIS distinguir entre os objetivos semelhantes. Criar discriminative objetivos específicos é uma das práticas recomendadas para LUIS de modelagem.

### <a name="is-it-important-to-train-the-none-intent"></a>É importante preparar a intenção None?

Sim, é bom treinar a sua intenção **de "Nenhum"** com mais expressões à medida que adiciona mais rótulos a outras intenções. Uma boa proporção é de 1 ou 2 rótulos adicionados a **Nenhum** por cada 10 etiquetas adicionadas a uma intenção. Este rácio aumenta o poder discriminative do LUIS.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Como pode corrigir os erros de ortografia em expressões com?

Consulte o tutorial [API V7](luis-tutorial-bing-spellcheck.md) do Bing Spell. LUIS impõe limites impostos pelo Bing ortográfica verificar a API V7.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Como faço para editar a minha aplicação LUIS por meio de programação?
Para editar a sua aplicação LUIS programáticamente, utilize a [API de Autoria.](https://go.microsoft.com/fwlink/?linkid=2092087) Consulte call [LUIS autor a API](./get-started-get-model-rest-apis.md) e [construa uma app LUIS programáticamente usando Node.js](./luis-tutorial-node-import-utterances-csv.md) para exemplos de como chamar a API autora. A API de autoria requer que utilize uma chave de [autor e](luis-concept-keys.md#azure-resources-for-luis) não uma chave de ponto final. A criação programática permite até 1 000 000 chamadas por mês e cinco transações por segundo. Para obter mais informações sobre as chaves que utiliza com o LUIS, consulte [Gerir chaves](./luis-concept-keys.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Qual é a funcionalidade padrão que fornecia a expressão regular que corresponda?
A **funcionalidade Padrão** anterior é atualmente depreciada, substituída por **[Padrões](luis-concept-patterns.md)** .

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Como utilizo uma entidade para extrair os dados corretos?
Consulte [entidades](luis-concept-entity-types.md) e extração de [dados.](luis-concept-data-extraction.md)

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Variações de uma expressão de exemplo devem incluir a pontuação?
Ou adicione as diferentes variações como declarações de exemplo à intenção ou adicione o padrão da expressão do exemplo com a [sintaxe para ignorar](luis-concept-patterns.md#pattern-syntax) a pontuação.

### <a name="does-luis-currently-support-cortana"></a>O LUIS suporta atualmente Cortana?

Cortana criados previamente as aplicações foram preteridas em 2017. Já não são suportadas.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Como transferir a propriedade de uma aplicação do LUIS?
Para transferir uma aplicação do LUIS para uma subscrição do Azure diferente, exportar a aplicação do LUIS e importe-o utilizando uma conta nova. Atualize o ID da aplicação LUIS no aplicativo cliente que chama-lo. A nova aplicação poderá devolver as LUIS ligeiramente diferente pontuações da aplicação original.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>Uma entidade pré-construída é marcada num exemplo em vez da minha entidade personalizada. Como posso resolver isto? 

No portal LUIS, pode rotular texto para a entidade exata que está interessado em extrair. Se o portal LUIS não mostrar a previsão correta da entidade, poderá ter de adicionar mais expressões e rotular a entidade dentro do texto ou adicionar um descritor (como uma funcionalidade). 

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Tentei importar uma aplicação ou ficheiro de versão, mas tenho um erro, o que aconteceu? 

Leia mais sobre erros de importação de [versão](luis-how-to-manage-versions.md#import-errors).

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>Colaborando e contribuindo

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-role-based-access-control-rbac"></a>Como dou aos colaboradores acesso a LUIS com O Diretório Ativo Azure (Azure AD) ou controlo de acesso baseado em funções (RBAC)?

Consulte [os recursos do Azure Ative Directory](luis-how-to-collaborate.md#azure-active-directory-resources) e o utilizador do inquilino do [Azure Ative Directory](luis-how-to-collaborate.md#azure-active-directory-tenant-user) para aprender a dar acesso aos colaboradores. 

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Ponto Final

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>Recebi um código de estado de erro HTTP 403. Como posso corrigi-lo?

Obtém-se 403 e 429 códigos de estado de erro quando excede as transações por segundo ou transações por mês para o seu nível de preços. Aumente o seu nível de preços ou utilize [recipientes](luis-container-howto.md)de compreensão linguística .

Quando utiliza todas essas consultas de ponto final gratuitas ou excede a quota mensal de transações do seu nível de preços, recebe um código de estado de erro HTTP 403. 

Para corrigir este erro, tem de [alterar o seu nível](luis-how-to-azure-subscription.md#change-pricing-tier) de preços para um nível mais elevado ou [criar um novo recurso](get-started-portal-deploy-app.md#create-the-endpoint-resource) e [atribuí-lo à sua aplicação](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

As soluções para este erro incluem:

* No [portal Azure](https://portal.azure.com), sobre o seu recurso de Compreensão de Línguas, na Gestão de **Recursos -> Nível**de Preços , altere o seu nível de preços para um nível de TPS mais elevado. Não precisa de fazer nada no portal de Compreensão da Linguagem se o seu recurso já estiver atribuído à sua aplicação de Compreensão de Línguas.
*  Se o seu uso exceder o nível de preços mais elevado, adicione mais recursos de Compreensão da Linguagem com um equilibrante de carga à sua frente. O [recipiente de compreensão da linguagem](luis-container-howto.md) com Kubernetes ou Docker Compose pode ajudar com isso.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>Recebi um código de estado de erro HTTP 429. Como posso corrigi-lo?

Obtém-se 403 e 429 códigos de estado de erro quando excede as transações por segundo ou transações por mês para o seu nível de preços. Aumente o seu nível de preços ou utilize [recipientes](luis-container-howto.md)de compreensão linguística .

Este código de estado é devolvido quando as suas transações por segundo excedem o seu nível de preços.  

As soluções incluem:

* Pode [aumentar o seu nível](luis-how-to-azure-subscription.md#change-pricing-tier)de preços, se não estiver no nível mais alto.
* Se o seu uso exceder o nível de preços mais elevado, adicione mais recursos de Compreensão da Linguagem com um equilibrante de carga à sua frente. O [recipiente de compreensão da linguagem](luis-container-howto.md) com Kubernetes ou Docker Compose pode ajudar com isso.
* Pode fazer um gate dos pedidos de pedido do seu cliente com uma política de [retry](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) que se implementa quando obtém este código de estado. 

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>Minha consulta de ponto final devolvidos resultados inesperados. O que devo fazer?

Resultados da predição de consulta inesperada baseiam-se sobre o estado do modelo publicado. Para corrigir o modelo, poderá ter de alterar o modelo, treinar e publicar novamente. 

A correção do modelo começa com [uma aprendizagem ativa.](luis-how-to-review-endpoint-utterances.md)

Pode remover a formação não determinística atualizando as definições da [versão de aplicação API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) de forma a utilizar todos os dados de formação.

Reveja as [melhores práticas](luis-concept-best-practices.md) para outras dicas. 

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Porquê o LUIS adiciona espaços para a consulta em torno ou no meio de palavras?
LUIS [tokeniza](luis-glossary.md#token) a expressão baseada na [cultura.](luis-language-support.md#tokenization) Tanto o valor original como o valor simbólico estão disponíveis para extração de [dados.](luis-concept-data-extraction.md#tokenized-entity-returned)

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Como criar e atribuir um LUIS chave de ponto final?
[Crie a chave de ponta](luis-how-to-azure-subscription.md) em Azure para o seu nível de [serviço.](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) [Atribua a chave](luis-how-to-azure-subscription.md) na página dos **[Recursos Azure.](luis-how-to-azure-subscription.md)** Não há nenhuma API correspondente para esta ação. Em seguida, tem de alterar o pedido HTTP para o ponto final para [utilizar a nova tecla de ponto final](luis-concept-keys.md).

### <a name="how-do-i-interpret-luis-scores"></a>Como posso interpretar as pontuações de LUIS?
O sistema deve utilizar a intenção de classificação mais elevada, independentemente de seu valor. Por exemplo, uma pontuação inferior a 0,5 (menos de 50%) não significa necessariamente que o LUIS tem confiança baixa. Fornecer mais dados de formação pode ajudar a aumentar a [pontuação](luis-concept-prediction-score.md) da intenção mais provável.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Por que motivo não vejo minhas acertos de ponto final no Dashboard da minha aplicação?
As visitas de ponto de extremidade total no Dashboard da sua aplicação são atualizadas periodicamente, mas as métricas associadas à sua chave de ponto final de LUIS no portal do Azure são atualizadas com mais frequência.

Se não vir os hits de ponto final atualizados no Dashboard, inscreva-se no portal Azure e encontre o recurso associado à sua chave de ponto final LUIS e abra **métricas** para selecionar a métrica **Total Call.** Se a chave de ponto final é utilizada para mais do que uma aplicação do LUIS, a métrica no portal do Azure mostra o número agregado de chamadas de todas as aplicações de LUIS utilização-lo.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Há algum comando PowerShell a chegar à quota de ponto final?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pode utilizar um comando PowerShell para ver a quota de ponto final:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
``` 

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>A minha aplicação LUIS estava trabalhando ontem, mas hoje em dia, eu tenho 403 erros. Eu não alterou a aplicação. Como posso corrigi-lo?
Siga estas [instruções](#how-do-i-create-and-assign-a-luis-endpoint-key) para criar uma chave de ponto final LUIS e atribua-a à aplicação. Em seguida, tem de alterar o pedido HTTP da aplicação cliente para o ponto final para [utilizar a nova chave de ponto final](luis-concept-keys.md). Se criou um novo recurso numa região diferente, mude também a região do pedido de cliente HTTP.

### <a name="how-do-i-secure-my-luis-endpoint"></a>Como posso proteger meu ponto de final do LUIS?
Ver [Fixar o ponto final](luis-concept-keys.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>Trabalhar dentro dos limites de LUIS

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>O que é o número máximo de intenções e entidades que pode oferecer suporte a uma aplicação do LUIS?
Veja a referência dos [limites.](luis-boundaries.md)

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Quero criar uma aplicação de LUIS com mais do que o número máximo de objetivos. O que devo fazer?

Consulte [as melhores práticas para intenções.](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents)

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Quero criar uma aplicação no LUIS com mais do que o número máximo de entidades. O que devo fazer?

Ver [As Melhores Práticas para entidades](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Apresenta uma lista quais são os limites no número e tamanho de frase?
Para o comprimento máximo de uma lista de [frases,](./luis-concept-feature.md)consulte a referência dos [limites.](luis-boundaries.md)

### <a name="what-are-the-limits-on-example-utterances"></a>Quais são os limites em expressões de exemplo?
Veja a referência dos [limites.](luis-boundaries.md)

## <a name="testing-and-training"></a>Teste e treinamento

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Posso ver alguns erros no lote painel de teste para alguns dos modelos em meu aplicativo. Como posso solucionar esse problema?

Os erros indicam que não há alguma discrepância entre as etiquetas e as previsões de seus modelos. Para resolver o problema, efetue uma ou ambas das seguintes tarefas:
* Para ajudar a melhorar discrimination entre os objetivos do LUIS, adicione mais etiquetas.
* Para ajudar a LUIS aprende mais depressa, adicione funcionalidades de lista de frase que apresentam o vocabulário específicas de domínio.

Consulte o tutorial de teste do [Lote.](luis-tutorial-batch-testing.md)

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Quando uma aplicação é exportada, em seguida, novamente importada para uma nova aplicação (com um novo ID de aplicação), as pontuações de predição de LUIS são diferentes. Por que isso acontece?

Ver Diferenças de [Previsão entre cópias da mesma aplicação](luis-concept-prediction-score.md#review-intents-with-similar-scores).

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Algumas expressões com até a intenção de errado depois que fiz alterações para a minha aplicação. O problema parece sumir aleatoriamente. Como posso corrigi-lo? 

Ver [Comboio com todos os dados](luis-how-to-train.md#train-with-all-data).

## <a name="app-publishing"></a>Publicação de aplicações

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>O que é o ID de inquilino na janela "Adicionar uma chave à sua aplicação"?
No Azure, um inquilino representa o cliente ou de uma organização que está associada um serviço. Encontre o seu ID de inquilino no portal Azure na caixa de ID do **Diretório** selecionando **o Diretório Ativo Azure** > **Gerir** > **Propriedades**.

![ID do inquilino no portal do Azure](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Por que há mais chaves de ponto final atribuídas à minha aplicação que atribuí?
Cada aplicação LUIS tem a chave de criação/starter na lista de ponto de extremidade como uma conveniência. Esta chave permite apenas alguns cliques de ponto final para que pode experimentar o LUIS.  

Se a sua aplicação existia antes do LUIS foi em disponibilidade geral (GA), as chaves de ponto final do LUIS na sua subscrição são atribuídas automaticamente. Isso foi feito para facilitar a migração de disponibilidade geral. Quaisquer novas chaves de ponto final LUIS no portal Azure _não_ são automaticamente atribuídas à LUIS.

## <a name="key-management"></a>Gestão de chaves

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Como sei que chave preciso, onde a consigo, e o que faço com ela? 

Consulte [as chaves finais de](luis-concept-keys.md) autor e previsão de consulta no LUIS para aprender sobre as diferenças entre a chave de autor e a chave de tempo de execução de previsão. 

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Tenho um erro em ficar fora da quota. Como posso corrigi-lo? 

Consulte, Fixe o código de estado [HTTP 403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) e [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) para saber mais.

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>Preciso de mais consultas finais. Como faço isto? 

Consulte, Fixe o código de estado [HTTP 403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) e [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) para saber mais.

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>Criei uma chave de autor, mas não está a aparecer no portal luis. O que aconteceu?

As chaves de autoria estão disponíveis no portal LUIS após [a migração para a experiência-chave de autoria.](luis-migration-authoring.md)  

## <a name="app-management"></a>Gestão de aplicações

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Como posso transferir um log de expressões de utilizador?
Por predefinição, a sua aplicação LUIS regista expressões com dos utilizadores. Para descarregar um registo de expressões que os utilizadores enviam para a sua aplicação LUIS, vá a **My Apps**e selecione a aplicação. Na barra de ferramentas contextual, selecione **Registos de Ponto final de Exportação**. O registo é formatado como um ficheiro de valores separados por vírgulas (CSV).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Como posso desativar o registo de expressões com?
Pode desativar o registo de declarações do utilizador definindo `log=false` no URL endpoint que a sua aplicação de cliente utiliza para consultar o LUIS. No entanto, desligar o registo desativa a capacidade da sua app LUIS de sugerir expressões ou melhorar o desempenho baseado na [aprendizagem ativa.](luis-concept-review-endpoint-utterances.md#what-is-active-learning) Se definir `log=false` devido a preocupações de privacidade de dados, não pode descarregar um registo dessas declarações de utilizador da LUIS ou usar essas expressões para melhorar a sua aplicação.

O registo é o armazenamento apenas de expressões.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Por que não quero que todos os meus expressões com ponto final com sessão iniciadas?
Se estiver a utilizar o início de sessão para análise de previsão, não capture expressões de teste no seu registo.

## <a name="data-management"></a>Gestão de dados

### <a name="can-i-delete-data-from-luis"></a>Pode eliminar dados do LUIS?

* Pode sempre eliminar as expressões de exemplo usadas para treinar o LUIS. Se eliminar uma expressão de exemplo da sua aplicação LUIS, ele é removido do serviço web LUIS e não está disponível para exportação.
* Pode eliminar as declarações da lista de declarações do utilizador que a LUIS sugere na página de declarações de ponto final da **Revisão.** A eliminar expressões com desta lista evita que sejam sendo sugerida, mas não a eliminá-los a partir de registos.
* Se eliminar uma conta, todas as aplicações são eliminadas, juntamente com os seus registos e as expressões de exemplo. Os dados são mantidos nos servidores durante 60 dias antes de ser eliminado permanentemente.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Como a Microsoft gere dados de que envio para o LUIS?

O [Trust Center](https://www.microsoft.com/trustcenter) explica os nossos compromissos e as suas opções de gestão de dados e acesso nos Serviços Azure.

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

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>O meu robô LUIS não está a funcionar. O que posso fazer?

A primeira questão é isolar se a questão está relacionada com luis ou se acontece fora do middleware LUIS. 

#### <a name="resolve-issue-in-luis"></a>Resolver problema no LUIS
Passe a mesma expressão para LUIS a partir do ponto final do [LUIS.](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) Se receber um erro, resolva o problema no LUIS até que o erro não seja devolvido. Erros comuns incluem:

* `Out of call volume quota. Quota will be replenished in <time>.` - Este problema indica que ou precisa de mudar de uma chave de autor para uma [chave de ponto final](luis-how-to-azure-subscription.md) ou precisa de mudar os níveis de [serviço](luis-how-to-azure-subscription.md#change-pricing-tier). 

#### <a name="resolve-issue-in-azure-bot-service"></a>Resolver problema no Serviço Bot Azure

Se estiver a utilizar o Serviço Bot Azure e o problema é que o **Teste no Chat web** retorna `Sorry, my bot code is having an issue`, verifique os seus registos:

1. No portal Azure, para o seu bot, a partir da secção de **gestão bot,** selecione **Build**.
1. Abra o editor de código online. 
1. Na parte superior, barra de navegação azul, selecione o nome bot (o segundo item à direita).
1. Na lista de abandono resultante, selecione **Open Kudu Console**.
1. Selecione **Ficheiros**e, em seguida, selecione **A Aplicação**. Reveja todos os ficheiros de registo. Se não vir o erro na pasta de aplicação, reveja todos os ficheiros de registo em **Ficheiros .** 
1. Lembre-se de reconstruir o seu projeto se C#estiver a usar uma linguagem compilada como .

> [!Tip] 
> A consola também pode instalar pacotes. 

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Resolva o problema enquanto depura na máquina local com a Bot Framework. 

Para saber mais sobre a depuração local de um bot, consulte [Debug um bot](https://docs.microsoft.com/azure/bot-service/bot-service-debug-bot?view=azure-bot-service-4.0).

## <a name="integrating-luis"></a>Integrar o LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Em que a minha aplicação LUIS é criada durante o processo de subscrição de bot de aplicação web do Azure?
Se selecionar um modelo LUIS e selecionar o botão **Select** no painel do modelo, o painel do lado esquerdo muda para incluir o tipo de modelo e pergunta em que região criar o modelo LUIS. O processo de bot de aplicação web não cria, no entanto, uma subscrição do LUIS.

![Região do LUIS modelo web app bot](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Que regiões do LUIS suportam priming de voz de Bot Framework?
[A preparação](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) de discursos só é suportada para aplicações LUIS no caso central (EUA).

## <a name="api-programming-strategies"></a>Estratégias de Programação da API

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Como posso obter programáticamente a região do LUIS de um recurso? 

Utilize a amostra LUIS para encontrar C# a [região](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) programáticamente utilizando ou nó.Js. 

## <a name="luis-service"></a>Serviço LUIS

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>Compreensão de idiomas (LUIS) disponível no local ou na nuvem privada?

Sim, pode utilizar o [recipiente](luis-container-howto.md) LUIS para estes cenários se tiver a conectividade necessária para a utilização do contador. 

## <a name="migrating-to-the-next-version"></a>Migrando para a próxima versão

### <a name="how-do-i-migrate-to-preview-v3-api"></a>Como migrapara pré-visualização da V3 API? 

Consulte [a API v2 para v3 Guia de migração para apps LUIS](luis-migration-api-v3.md)

## <a name="build-2019-conference-announcements"></a>Construir anúncios da Conferência 2019

As seguintes características foram lançadas na Conferência Build 2019:

* [Pré-visualização do guia de migração V3 API](luis-migration-api-v3.md)
* [Painel de análise melhorado](luis-how-to-use-dashboard.md)
* [Domínios pré-construídos melhorados](luis-reference-prebuilt-domains.md) 
* [Entidades de lista dinâmica](luis-migration-api-v3.md#dynamic-lists-passed-in-at-prediction-time)
* [Entidades externas](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

Vídeos:

* [Como usar a IA Conversacional Azure para escalar o seu negócio para a próxima geração](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o LUIS, consulte os seguintes recursos:
* [Stack Overflow perguntas marcadas com LUIS](https://stackoverflow.com/questions/tagged/luis)
* [Fórum de Compreensão de Linguagem MSDN (LUIS)](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS)
