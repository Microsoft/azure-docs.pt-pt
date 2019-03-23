---
title: Perguntas Mais Frequentes (FAQ)
titleSuffix: Azure Cognitive Services
description: Este artigo contém respostas para perguntas mais frequentes sobre a compreensão de idiomas (LUIS).
author: diberry
manager: nitinme
ms.custom: seodec18
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/21/2019
ms.author: diberry
ms.openlocfilehash: aed7d4fedd4781eac8c127744e5fe93fb054b99d
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369722"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Compreensão de idiomas perguntas mais frequentes sobre (FAQ)

Este artigo contém respostas para perguntas mais frequentes sobre a compreensão de idiomas (LUIS).

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
Para editar a sua aplicação do LUIS programaticamente, utilize o [criação API](https://aka.ms/luis-authoring-apis). Ver [LUIS chamar a API de criação](./luis-quickstart-node-add-utterance.md) e [criar uma aplicação do LUIS por meio de programação com node. js](./luis-tutorial-node-import-utterances-csv.md) para obter exemplos de como chamar a API de criação. A API de criação é necessário utilizar um [chave de criação](luis-concept-keys.md#authoring-key) em vez de uma chave de ponto final. A criação programática permite até 1 000 000 chamadas por mês e cinco transações por segundo. Para obter mais informações sobre as chaves a utilizar com os LUIS, consulte [gerir chaves](./luis-concept-keys.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Qual é a funcionalidade padrão que fornecia a expressão regular que corresponda?
Anterior **funcionalidade de padrão** atualmente é preterido, substituídos pelos  **[padrões](luis-concept-patterns.md)**.

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Como utilizo uma entidade para extrair os dados corretos?
Ver [entidades](luis-concept-entity-types.md) e [extração de dados](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Variações de uma expressão de exemplo devem incluir a pontuação?
Adicione as diferentes variações como expressões de exemplo para a intenção ou adicionar o padrão de expressão de exemplo com o [sintaxe para ignorar](luis-concept-patterns.md#pattern-syntax) a pontuação.

### <a name="does-luis-currently-support-cortana"></a>O LUIS suporta atualmente Cortana?

Cortana criados previamente as aplicações foram preteridas em 2017. Já não são suportadas.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Como transferir a propriedade de uma aplicação do LUIS?
Para transferir uma aplicação do LUIS para uma subscrição do Azure diferente, exportar a aplicação do LUIS e importe-o utilizando uma conta nova. Atualize o ID da aplicação LUIS no aplicativo cliente que chama-lo. A nova aplicação poderá devolver as LUIS ligeiramente diferente pontuações da aplicação original.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>Uma entidade pré-criados é identificada numa expressão de exemplo em vez de minha entidade personalizada. Como posso corrigir isto? 

Ver [entidades previamente concebidas de resolução de problemas](luis-concept-entity-types.md#troubleshooting-prebuilt-entities).

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Tentei importar um ficheiro de aplicação ou versão, mas eu obtive um erro, o que aconteceu? 

Leia mais sobre [erros de importação de versão](luis-how-to-manage-versions.md#import-errors) e [erros de importação de aplicativos](luis-how-to-start-new-app.md#import-errors).

<a name="luis-collaborating"></a>

## <a name="collaborating"></a>Colaborar

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-role-based-access-control-rbac"></a>Como posso conceder acesso de colaboradores para LUIS com o Azure Active Directory (Azure AD) ou o controlo de acesso baseado em funções (RBAC)?

Ver [recursos do Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-resources) e [utilizador de inquilino do Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-tenant-user) para aprender a conceder acesso de colaboradores. 

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Ponto Final

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>Minha consulta de ponto final devolvidos resultados inesperados. O que devo fazer?

Resultados da predição de consulta inesperada baseiam-se sobre o estado do modelo publicado. Para corrigir o modelo, pode precisar de alterar o modelo, formar e publicar novamente. 

Como corrigir o modelo começa com [aprendizagem ativa](luis-how-to-review-endoint-utt.md).

Pode remover o treinamento determinística ao atualizar o [aplicação API de definições de versão](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) para utilizar todos os dados de treinamento.

Reveja os [melhores práticas](luis-concept-best-practices.md) para outras sugestões. 

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Porquê o LUIS adiciona espaços para a consulta em torno ou no meio de palavras?
LUIS [divide](luis-glossary.md#token) a expressão com base na [cultura](luis-language-support.md#tokenization). O valor original e o valor tokenized estão disponíveis para [extração de dados](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Como criar e atribuir um LUIS chave de ponto final?
[Criar a chave de ponto final](luis-how-to-azure-subscription.md) no Azure para a sua [serviço](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) nível. [Atribuir a chave](luis-how-to-azure-subscription.md) sobre o **[chaves e os pontos finais](luis-how-to-azure-subscription.md)** página. Não há nenhuma API correspondente para esta ação. Em seguida, tem de alterar o pedido HTTP para o ponto final para [utilizar a nova chave de ponto final](luis-concept-keys.md#use-endpoint-key-in-query).

### <a name="how-do-i-interpret-luis-scores"></a>Como posso interpretar as pontuações de LUIS?
O sistema deve utilizar a intenção de classificação mais elevada, independentemente de seu valor. Por exemplo, uma pontuação inferior a 0,5 (menos de 50%) não significa necessariamente que o LUIS tem confiança baixa. Para disponibilizar formação mais dados podem ajudar a aumentar a [pontuação](luis-concept-prediction-score.md) da intenção mais prováveis.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Por que motivo não vejo minhas acertos de ponto final no Dashboard da minha aplicação?
As visitas de ponto de extremidade total no Dashboard da sua aplicação são atualizadas periodicamente, mas as métricas associadas à sua chave de ponto final de LUIS no portal do Azure são atualizadas com mais frequência.

Se não vir os resultados de ponto final atualizadas no Dashboard, inicie sessão no portal do Azure e localize o recurso associado à sua chave de ponto final de LUIS e, abra **métricas** para selecionar o **Total de chamadas** métrica. Se a chave de ponto final é utilizada para mais do que uma aplicação do LUIS, a métrica no portal do Azure mostra o número agregado de chamadas de todas as aplicações de LUIS utilização-lo.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Existe um comando obter para a quota de ponto final de PowerShell?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pode utilizar um comando do PowerShell para ver a quota de ponto final:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
``` 

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>A minha aplicação LUIS estava trabalhando ontem, mas hoje em dia, eu tenho 403 erros. Eu não alterou a aplicação. Como posso corrigi-lo?
A seguir a [instruções](#how-do-i-create-and-assign-a-luis-endpoint-key) nas perguntas frequentes seguintes para criar uma chave de ponto final do LUIS e atribuí-la para a aplicação. Em seguida, tem de alterar o pedido HTTP para o ponto final para [utilizar a nova chave de ponto final](luis-concept-keys.md#use-endpoint-key-in-query).

### <a name="how-do-i-secure-my-luis-endpoint"></a>Como posso proteger meu ponto de final do LUIS?
Ver [proteger o ponto final](luis-concept-security.md#securing-the-endpoint).

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

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Como posso saber que preciso, onde posso obtê-lo, de chave e o que posso fazer com ele? 

Ver [criação e a consulta a chaves do ponto final de predição em LUIS](luis-concept-keys.md) para saber mais sobre as diferenças entre o [chave de criação](luis-how-to-account-settings.md) e o [chave de predição de ponto final](luis-how-to-azure-subscription.md). 

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Recebi um erro sobre a ser fora da quota. Como posso corrigi-lo? 

Ver, [como corrigir erros de fora da quota, quando a chave excede a utilização do escalão de preço](luis-how-to-azure-subscription.md##how-to-fix-out-of-quota-errors-when-the-key-exceeds-pricing-tier-usage) para saber mais.

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>Preciso de lidar com mais consultas de ponto final. Como fazer isso? 

Ver, [como corrigir erros de fora da quota, quando a chave excede a utilização do escalão de preço](luis-how-to-azure-subscription.md##how-to-fix-out-of-quota-errors-when-the-key-exceeds-pricing-tier-usage) para saber mais.



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

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>Meu bot de LUIS não está a funcionar. O que posso fazer?

O primeiro problema é isolar se o problema está relacionado com a LUIS ou acontece fora o middleware de LUIS. 

#### <a name="resolve-issue-in-luis"></a>Resolver problema do LUIS
Passar a mesma expressão para LUIS a partir do [ponto final de LUIS](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance). Se receber um erro, resolva o problema em LUIS até que já não é devolvido o erro. Erros comuns incluem:

* `Out of call volume quota. Quota will be replenished in <time>.` -Este problema indica a necessidade de alterar a partir de uma chave de criação para um [chave de ponto final](luis-how-to-azure-subscription.md) ou precisar de alterar [escalões de serviço](luis-how-to-azure-subscription.md#change-pricing-tier). 

#### <a name="resolve-issue-in-azure-bot-service"></a>Resolver o problema no Azure Bot Service

Se estiver a utilizar o Azure Bot Service e o problema é que o **teste na Web Chat** devolve `Sorry, my bot code is having an issue`, verifique os registos:

1. No portal do Azure, para o bot, do **gestão de Bot** secção, selecione **criar**.
1. Abra o editor de código online. 
1. Na barra de navegação superior, azul, selecione o nome do bot (o segundo item para a direita).
1. Na lista resultante de lista pendente, selecione **abra a consola Kudu**.
1. Selecione **LogFiles**, em seguida, selecione **aplicação**. Reveja todos os ficheiros de registo. Se não vir o erro na pasta da aplicação, reveja todos os ficheiros de registo em **LogFiles**. 
1. Lembre-se reconstruir o projeto, se estiver a utilizar uma linguagem compilada, como C#.

> [!Tip] 
> A consola também pode instalar pacotes. 

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Resolva o problema durante a depuração na máquina local com o Bot Framework. 

Para saber mais sobre a depuração local de um bot, veja [depurar um bot](https://docs.microsoft.com/azure/bot-service/bot-service-debug-bot?view=azure-bot-service-4.0).

## <a name="integrating-luis"></a>Integrar o LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Em que a minha aplicação LUIS é criada durante o processo de subscrição de bot de aplicação web do Azure?
Se selecionar um modelo do LUIS e selecionar a **selecione** botão no painel de modelo, o painel da esquerda é alterado para incluir o tipo de modelo e pede-lhe em que região para criar o modelo do LUIS. O processo de bot de aplicação web não cria, no entanto, uma subscrição do LUIS.

![Região do LUIS modelo web app bot](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Que regiões do LUIS suportam priming de voz de Bot Framework?
[Priming de voz](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) apenas é suportada para aplicações de LUIS na instância da central (E.U.A.).

## <a name="api-programming-strategies"></a>Estratégias de programação de API

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Como posso obter programaticamente a região de LUIS de um recurso? 

Utilize o exemplo de LUIS [encontrar região](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) forma programática com C# ou node. js. 

## <a name="luis-service"></a>Serviço de LUIS

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>Compreensão de idiomas (LUIS) disponível no local ou na nuvem privada?

Sim, pode utilizar o LUIS [contentor](luis-container-howto.md) para estes cenários, se tiver a conectividade necessária para medir a utilização. 

### <a name="at-the-build-2018-conference-i-heard-about-a-language-understanding-feature-or-demo-but-i-dont-remember-what-it-was-called"></a>Na conferência Build 2018, ouvi sobre um recurso de compreensão de idiomas ou uma demonstração, mas eu não se lembrar de que foi chamado?

As seguintes funcionalidades foram lançadas na conferência Build 2018:

|Nome|Conteúdo|
|--|--|
|Aprimoramentos|[Expressão regular](luis-concept-data-extraction.md##regular-expression-entity-data) entidade e [frases chave](luis-concept-data-extraction.md#key-phrase-extraction-entity-data) entidade
|Padrões|Padrões [conceito](luis-concept-patterns.md), [tutorial](luis-tutorial-pattern.md), [procedimentos](luis-how-to-model-intent-pattern.md)<br>[Patterns.Any](luis-concept-entity-types.md) incluindo do conceito de entidade [lista explícita](luis-concept-patterns.md#explicit-lists) para exceções<br>[Funções](luis-concept-roles.md) conceito|
|Integrações|[Análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) a integração do [análise de sentimentos](luis-how-to-publish-app.md#enable-sentiment-analysis)<br>[Voz](https://docs.microsoft.com/azure/cognitive-services/speech) a integração do priming de voz em conjunto com [SDK de voz](https://aka.ms/SpeechSDK)|
|Ferramenta de distribuição|Parte [BotBuilder-tools](https://github.com/Microsoft/botbuilder-tools), linha de comandos de expedição [ferramenta](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps) para combinar vários LUIS e QnA Maker aplicações numa única aplicação LUIS para melhor reconhecimento da intenção de um Bot

Criação adicionais [as rotas API](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/authoring-routes.md) foram incluídos.

Vídeos:
* [Azure Friday na compilação 2018: Serviços cognitivos - idiomas (LUIS)](https://channel9.msdn.com/Shows/Azure-Friday/At-Build-2018-Cognitive-Services-Language-LUIS/player)
* [Compilação de 2018 IA Mostrar - o que há de novo com o serviço de compreensão de idiomas](https://channel9.msdn.com/Shows/AI-Show/Whats-New-with-Language-Understanding-Service-LUIS/player)
* [Sessão do Build 2018 - Inteligência de bots, Capacidades de Voz e melhores práticas do NLU](https://channel9.msdn.com/events/Build/2018/BRK3208)
* [Compilação de 2018 - atualizações de LUIS](https://channel9.msdn.com/events/Build/2018/THR3118/player)

Projetos:
* [Bot de contoso Cafe](https://github.com/botbuilderbuild2018/build2018demo) demonstração - código-fonte no GitHub

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre o LUIS, consulte os seguintes recursos:
* [Perguntas de estouro de pilha etiquetadas com os LUIS](https://stackoverflow.com/questions/tagged/luis)
* [Fórum de (LUIS) de serviços de compreensão inteligentes de idiomas do MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS)
