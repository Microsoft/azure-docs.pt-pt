---
title: Perguntas frequentes (FAQ) - LUIS
description: Este artigo contém respostas a perguntas frequentes sobre compreensão da linguagem (LUIS).
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: diberry
ms.openlocfilehash: 235eba7f80778b8a60ba880616cf80f2c14ccba1
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382191"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Compreensão da linguagem Perguntas Frequentes (FAQ)

Este artigo contém respostas a perguntas frequentes sobre compreensão da linguagem (LUIS).

## <a name="whats-new"></a>Novidades

[Saiba mais](whats-new.md) sobre o que há de novo na Compreensão da Língua (LUIS).

<a name="luis-authoring"></a>

## <a name="authoring"></a>Criação

### <a name="what-are-the-luis-best-practices"></a>Quais são as melhores práticas do LUIS?
Comece com o [Ciclo de Autoria,](luis-concept-app-iteration.md)depois leia as [melhores práticas.](luis-concept-best-practices.md)

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Qual é a melhor maneira de começar a construir a minha app no LUIS?

A melhor forma de construir a sua aplicação é através de um [processo incremental.](luis-concept-app-iteration.md)

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Qual é uma boa prática para modelar as intenções da minha app? Devo criar intenções mais específicas ou mais genéricas?

Escolha intenções que não sejam tão gerais que se sobreponham, mas não tão específicas que dificultem a distinção entre intenções semelhantes. Criar intenções específicas discriminatórias é uma das melhores práticas para a modelação LUIS.

### <a name="is-it-important-to-train-the-none-intent"></a>É importante treinar a intenção de Ninguém?

Sim, é bom treinar a sua intenção **de "Nenhum"** com mais expressões à medida que adiciona mais rótulos a outras intenções. Uma boa proporção é de 1 ou 2 rótulos adicionados a **Nenhum** por cada 10 etiquetas adicionadas a uma intenção. Este rácio aumenta o poder discriminatório do LUIS.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Como posso corrigir erros ortográficos em declarações?

Consulte o tutorial [API V7](luis-tutorial-bing-spellcheck.md) do Bing Spell. LUIS impõe limites impostos por Bing Spell Check API V7.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Como edição programática da minha aplicação LUIS?
Para editar a sua aplicação LUIS programáticamente, utilize a [API de Autoria.](https://go.microsoft.com/fwlink/?linkid=2092087) Consulte call [LUIS autor a API](./get-started-get-model-rest-apis.md) e [construa uma app LUIS programáticamente usando Node.js](./luis-tutorial-node-import-utterances-csv.md) para exemplos de como chamar a API autora. A API de autoria requer que utilize uma chave de [autor e](luis-concept-keys.md#azure-resources-for-luis) não uma chave de ponto final. A autoria programática permite até 1.000.000 chamadas por mês e cinco transações por segundo. Para obter mais informações sobre as chaves que utiliza com o LUIS, consulte [Gerir chaves](./luis-concept-keys.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Onde está a função Padrão que forneceu correspondência de expressão regular?
A **funcionalidade Padrão** anterior é atualmente depreciada, substituída por **[Padrões](luis-concept-patterns.md)**.

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Como uso uma entidade para retirar os dados corretos?
Consulte [entidades](luis-concept-entity-types.md) e extração de [dados.](luis-concept-data-extraction.md)

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>As variações de um exemplo incluem pontuação?
Utilize uma das seguintes soluções:
* Ignorar [a pontuação](luis-reference-application-settings.md#punctuation-normalization)
* Adicione as diferentes variações como declarações de exemplo à intenção
* Adicione o padrão da expressão do exemplo com a [sintaxe para ignorar](luis-concept-patterns.md#pattern-syntax) a pontuação.

### <a name="does-luis-currently-support-cortana"></a>A LUIS apoia atualmente a Cortana?

As aplicações pré-construídas da Cortana foram depreciadas em 2017. Já não são apoiados.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Como posso transferir a propriedade de uma aplicação LUIS?
Para transferir uma app LUIS para uma subscrição azure diferente, exportar a app LUIS e importá-la usando uma nova conta. Atualize o ID da aplicação LUIS na aplicação do cliente que a chama. A nova aplicação poderá devolver pontuações DE LUIS ligeiramente diferentes da aplicação original.

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

* No [portal Azure](https://portal.azure.com), sobre o seu recurso de compreensão de linguagem, no nível de Gestão de **Recursos -> ,** altere o seu nível de preços para um nível de TPS mais elevado. Não precisa de fazer nada no portal de Compreensão da Linguagem se o seu recurso já estiver atribuído à sua aplicação de Compreensão de Línguas.
*  Se o seu uso exceder o nível de preços mais elevado, adicione mais recursos de Compreensão da Linguagem com um equilibrante de carga à sua frente. O [recipiente de compreensão da linguagem](luis-container-howto.md) com Kubernetes ou Docker Compose pode ajudar com isso.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>Recebi um código de estado de erro HTTP 429. Como posso corrigi-lo?

Obtém-se 403 e 429 códigos de estado de erro quando excede as transações por segundo ou transações por mês para o seu nível de preços. Aumente o seu nível de preços ou utilize [recipientes](luis-container-howto.md)de compreensão linguística .

Este código de estado é devolvido quando as suas transações por segundo excedem o seu nível de preços.

As soluções incluem:

* Pode [aumentar o seu nível](luis-how-to-azure-subscription.md#change-pricing-tier)de preços, se não estiver no nível mais alto.
* Se o seu uso exceder o nível de preços mais elevado, adicione mais recursos de Compreensão da Linguagem com um equilibrante de carga à sua frente. O [recipiente de compreensão da linguagem](luis-container-howto.md) com Kubernetes ou Docker Compose pode ajudar com isso.
* Pode fazer um gate dos pedidos de pedido do seu cliente com uma política de [retry](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) que se implementa quando obtém este código de estado.

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>A minha consulta final devolveu resultados inesperados. O que devo fazer?

Os resultados inesperados da previsão de consulta baseiam-se no estado do modelo publicado. Para corrigir o modelo, poderá ter de alterar o modelo, treinar e publicar novamente.

A correção do modelo começa com [uma aprendizagem ativa.](luis-how-to-review-endpoint-utterances.md)

Pode remover a formação não determinística atualizando as definições da [versão de aplicação API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) de forma a utilizar todos os dados de formação.

Reveja as [melhores práticas](luis-concept-best-practices.md) para outras dicas.

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Porque é que o LUIS adiciona espaços à consulta ao redor ou no meio das palavras?
LUIS [tokeniza](luis-glossary.md#token) a expressão baseada na [cultura.](luis-language-support.md#tokenization) Tanto o valor original como o valor simbólico estão disponíveis para extração de [dados.](luis-concept-data-extraction.md#tokenized-entity-returned)

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Como posso criar e atribuir uma chave de ponto final LUIS?
[Crie a chave de ponta](luis-how-to-azure-subscription.md) em Azure para o seu nível de [serviço.](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) [Atribua a chave](luis-how-to-azure-subscription.md) na página dos **[Recursos Azure.](luis-how-to-azure-subscription.md)** Não existe uma API correspondente para esta ação. Em seguida, tem de alterar o pedido HTTP para o ponto final para [utilizar a nova tecla de ponto final](luis-concept-keys.md).

### <a name="how-do-i-interpret-luis-scores"></a>Como interpreto as notas do LUIS?
O seu sistema deve usar a maior intenção de pontuação, independentemente do seu valor. Por exemplo, uma pontuação abaixo de 0,5 (menos de 50%) não significa necessariamente que LUIS tem baixa confiança. Fornecer mais dados de formação pode ajudar a aumentar a [pontuação](luis-concept-prediction-score.md) da intenção mais provável.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Porque não vejo os meus sucessos no Dashboard da minha aplicação?
Os sucessos totais no Dashboard da sua aplicação são atualizados periodicamente, mas as métricas associadas à sua chave de ponto final LUIS no portal Azure são atualizadas com mais frequência.

Se não vir os hits de ponto final atualizados no Dashboard, inscreva-se no portal Azure e encontre o recurso associado à sua chave de ponto final LUIS e abra **métricas** para selecionar a métrica **Total Call.** Se a chave de ponto final for utilizada para mais do que uma aplicação LUIS, a métrica no portal Azure mostra o número agregado de chamadas de todas as aplicações da LUIS que a utilizam.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Há algum comando PowerShell a chegar à quota de ponto final?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pode utilizar um comando PowerShell para ver a quota de ponto final:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
```

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>A minha aplicação luis estava a funcionar ontem, mas hoje estou a ter 403 erros. Não mudei a aplicação. Como posso corrigi-lo?
Siga estas [instruções](#how-do-i-create-and-assign-a-luis-endpoint-key) para criar uma chave de ponto final LUIS e atribua-a à aplicação. Em seguida, tem de alterar o pedido HTTP da aplicação cliente para o ponto final para [utilizar a nova chave de ponto final](luis-concept-keys.md). Se criou um novo recurso numa região diferente, mude também a região do pedido de cliente HTTP.

### <a name="how-do-i-secure-my-luis-endpoint"></a>Como posso garantir o meu ponto final luis?
Ver [Fixar o ponto final](luis-concept-keys.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>Trabalhar dentro dos limites luis

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Qual é o número máximo de intenções e entidades que uma app LUIS pode suportar?
Veja a referência dos [limites.](luis-boundaries.md)

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Quero construir uma app LUIS com mais do que o número máximo de intenções. O que devo fazer?

Consulte [as melhores práticas para intenções.](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents)

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Quero construir uma app no LUIS com mais do que o número máximo de entidades. O que devo fazer?

Ver [As Melhores Práticas para entidades](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Quais são os limites do número e tamanho das listas de frases?
Para o comprimento máximo de uma lista de [frases,](./luis-concept-feature.md)consulte a referência dos [limites.](luis-boundaries.md)

### <a name="what-are-the-limits-on-example-utterances"></a>Quais são os limites das declarações de exemplo?
Veja a referência dos [limites.](luis-boundaries.md)

## <a name="testing-and-training"></a>Teste e formação

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Vejo alguns erros no painel de testes de lote para alguns dos modelos da minha aplicação. Como posso resolver este problema?

Os erros indicam que existe alguma discrepância entre as etiquetas e as previsões dos seus modelos. Para resolver o problema, faça uma ou ambas as seguintes tarefas:
* Para ajudar o LUIS a melhorar a discriminação entre as intenções, adicione mais rótulos.
* Para ajudar o LUIS a aprender mais rapidamente, adicione funcionalidades da lista de frases que introduzem vocabulário específico de domínio.

Consulte o tutorial de teste do [Lote.](luis-tutorial-batch-testing.md)

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Quando uma aplicação é exportada então reimportada para uma nova app (com uma nova app ID), as pontuações de previsão luis são diferentes. Porque é que isto acontece?

Ver Diferenças de [Previsão entre cópias da mesma aplicação](luis-concept-prediction-score.md#review-intents-with-similar-scores).

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Algumas declarações vão para a intenção errada depois de eu ter feito alterações na minha aplicação. O assunto parece desaparecer aleatoriamente. Como posso corrigi-lo?

Ver [Comboio com todos os dados](luis-how-to-train.md#train-with-all-data).

## <a name="app-publishing"></a>Publicação de aplicativos

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Qual é a identificação do inquilino na janela "Adicione uma chave à sua aplicação"?
Em Azure, um inquilino representa o cliente ou organização que está associado a um serviço. Encontre o seu ID de inquilino no portal Azure na caixa de ID do **Diretório** selecionando propriedades de**gestão** > de **diretórios** > ativos azure **.**

![ID do inquilino no portal Azure](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Por que há mais chaves de ponta atribuídas à minha aplicação do que eu atribuí?
Cada aplicação LUIS tem a chave de autoria/arranque na lista de pontofinal como uma conveniência. Esta chave permite apenas alguns golpes finais para que possa experimentar O LUIS.

Se a sua aplicação existisse antes de a LUIS estar geralmente disponível (GA), as chaves de ponto final LUIS na sua subscrição são atribuídas automaticamente. Isto foi feito para facilitar a migração da AG. Quaisquer novas chaves de ponto final LUIS no portal Azure _não_ são automaticamente atribuídas à LUIS.

## <a name="key-management"></a>Gestão de chaves

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Como sei que chave preciso, onde a consigo, e o que faço com ela?

Consulte [as chaves finais de](luis-concept-keys.md) autor e previsão de consulta no LUIS para aprender sobre as diferenças entre a chave de autor e a chave de tempo de execução de previsão.

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Tenho um erro em ficar fora da quota. Como posso corrigi-lo?

Consulte, Fixe o código de estado [HTTP 403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) e [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) para saber mais.

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>Preciso de mais consultas finais. Como posso fazê-lo?

Consulte, Fixe o código de estado [HTTP 403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) e [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) para saber mais.

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>Criei uma chave de autor, mas não está a aparecer no portal luis. O que aconteceu?

As chaves de autoria estão disponíveis no portal LUIS após [a migração para a experiência-chave de autoria.](luis-migration-authoring.md)

## <a name="app-management"></a>Gestão de aplicações

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Como faço o download de um registo de declarações de utilizadores?
Por predefinição, a sua aplicação LUIS regista declarações dos utilizadores. Para descarregar um registo de expressões que os utilizadores enviam para a sua aplicação LUIS, vá a **My Apps**e selecione a aplicação. Na barra de ferramentas contextual, selecione **Registos de Ponto final de Exportação**. O registo é formatado como um ficheiro de valor separado de vírina (CSV).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Como posso desativar o registo de expressões?
Pode desativar o registo de declarações do utilizador definindo `log=false` no URL endpoint que a sua aplicação de cliente utiliza para consulta LUIS. No entanto, desligar o registo desativa a capacidade da sua app LUIS de sugerir expressões ou melhorar o desempenho baseado na [aprendizagem ativa.](luis-concept-review-endpoint-utterances.md#what-is-active-learning) Se definir `log=false` por motivos de privacidade de dados, não pode descarregar um registo dessas declarações de utilizador da LUIS ou usar essas expressões para melhorar a sua aplicação.

A exploração madeireira é o único armazenamento de expressões.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Por que não quero todas as minhas declarações finais registadas?
Se estiver a utilizar o seu registo para análise de previsão, não capture as declarações de teste no seu registo.

## <a name="data-management"></a>Gestão de dados

### <a name="can-i-delete-data-from-luis"></a>Posso apagar dados do LUIS?

* Pode sempre apagar as declarações de exemplo utilizadas para a formação do LUIS. Se eliminar um exemplo da sua aplicação LUIS, é removido do serviço web LUIS e não está disponível para exportação.
* Pode eliminar as declarações da lista de declarações do utilizador que a LUIS sugere na página de declarações de ponto final da **Revisão.** Eliminar as declarações desta lista impede que sejam sugeridas, mas não as apaga dos registos.
* Se eliminar uma conta, todas as aplicações são eliminadas, juntamente com as suas expressões e registos de exemplo. Os dados são retidos nos servidores durante 60 dias antes de ser eliminado permanentemente.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Como é que a Microsoft gere os dados que envio para o LUIS?

O [Trust Center](https://www.microsoft.com/trustcenter) explica os nossos compromissos e as suas opções de gestão de dados e acesso nos Serviços Azure.

## <a name="language-and-translation-support"></a>Suporte à língua e à tradução

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Tenho uma aplicação num idioma e quero criar uma aplicação paralela noutro idioma. Qual é a maneira mais fácil de fazê-lo?
1. Exporte a sua aplicação.
2. Traduza as palavras rotuladas no ficheiro JSON da aplicação exportada para o idioma-alvo.
3. Pode ser necessário alterar os nomes das intenções e entidades ou deixá-los como estão.
4. Por fim, importe a app para ter uma app LUIS no idioma-alvo.

## <a name="app-notification"></a>Notificação de aplicativos

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Por que recebi um e-mail dizendo que estou quase sem quota?
A sua chave de autoria/arranque só é permitida 1000 consultas de ponto final por mês. Crie uma chave de ponto final LUIS (grátis ou paga) e use essa tecla ao fazer consultas de ponto final. Se estiver a fazer consultas finais a partir de um bot ou de outra aplicação de cliente, tem de alterar a chave de ponto final da LUIS.

## <a name="bots"></a>Bots

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>O meu robô LUIS não está a funcionar. O que posso fazer?

A primeira questão é isolar se a questão está relacionada com luis ou se acontece fora do middleware LUIS.

#### <a name="resolve-issue-in-luis"></a>Resolver problema no LUIS
Passe a mesma expressão para LUIS a partir do ponto final do [LUIS.](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) Se receber um erro, resolva o problema no LUIS até que o erro não seja devolvido. Os erros comuns incluem:

* `Out of call volume quota. Quota will be replenished in <time>.`- Este problema indica que ou precisa de mudar de uma chave de autor para uma [chave de ponto final](luis-how-to-azure-subscription.md) ou precisa de mudar os níveis de [serviço](luis-how-to-azure-subscription.md#change-pricing-tier).

#### <a name="resolve-issue-in-azure-bot-service"></a>Resolver problema no Serviço Bot Azure

Se estiver a utilizar o Serviço Bot Azure e o `Sorry, my bot code is having an issue`problema é que o Teste no Chat **web** retorna, verifique os seus registos:

1. No portal Azure, para o seu bot, a partir da secção de **gestão bot,** selecione **Build**.
1. Abra o editor de código online.
1. Na parte superior, barra de navegação azul, selecione o nome bot (o segundo item à direita).
1. Na lista de abandono resultante, selecione **Open Kudu Console**.
1. Selecione **Ficheiros**e, em seguida, selecione **A Aplicação**. Reveja todos os ficheiros de registo. Se não vir o erro na pasta de aplicação, reveja todos os ficheiros de registo em **Ficheiros .**
1. Lembre-se de reconstruir o seu projeto se estiver a usar uma linguagem compilada como C#.

> [!Tip]
> A consola também pode instalar pacotes.

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Resolva o problema enquanto depura na máquina local com a Bot Framework.

Para saber mais sobre a depuração local de um bot, consulte [Debug um bot](https://docs.microsoft.com/azure/bot-service/bot-service-debug-bot?view=azure-bot-service-4.0).

## <a name="integrating-luis"></a>Integração LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Onde está a minha aplicação LUIS criada durante o processo de subscrição de bot web web da Web Web?
Se selecionar um modelo LUIS e selecionar o botão **Select** no painel do modelo, o painel do lado esquerdo muda para incluir o tipo de modelo e pergunta em que região criar o modelo LUIS. No entanto, o processo de bot da aplicação web não cria uma subscrição LUIS.

![Região bot de aplicativo de modelo LUIS](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Que regiões do LUIS apoiam a preparação do discurso do Bot Framework?
[A preparação](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) de discursos só é suportada para aplicações LUIS no caso central (EUA).

## <a name="api-programming-strategies"></a>Estratégias de Programação da API

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Como posso obter programáticamente a região do LUIS de um recurso?

Utilize a amostra LUIS para encontrar a [região](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) programáticamente utilizando C# ou Node.Js.

## <a name="luis-service"></a>Serviço LUIS

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>A Compreensão da Linguagem (LUIS) está disponível no local ou em nuvem privada?

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
