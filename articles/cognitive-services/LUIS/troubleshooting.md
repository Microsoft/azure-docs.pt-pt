---
title: Perguntas frequentes (FAQ) - LUIS
description: Este artigo contém respostas a perguntas frequentes sobre compreensão de linguagem (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: troubleshooting
ms.date: 05/06/2020
ms.openlocfilehash: b5e25e9ed25ced96d38994928bcb6275ce79420f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612801"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Perguntas Frequentes (FAQ) do Language Understanding 

Este artigo contém respostas a perguntas frequentes sobre compreensão de linguagem (LUIS).

## <a name="whats-new"></a>Novidades

[Saiba mais](whats-new.md) sobre as novidades em Compreensão de Línguas (LUIS).

<a name="luis-authoring"></a>

## <a name="authoring"></a>Criação

### <a name="what-are-the-luis-best-practices"></a>Quais são as melhores práticas do LUIS?
Comece com o [Ciclo de Autoria,](luis-concept-app-iteration.md)depois leia as [melhores práticas.](luis-concept-best-practices.md)

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Qual é a melhor maneira de começar a construir a minha app no LUIS?

A melhor forma de construir a sua app é através de um [processo incremental.](luis-concept-app-iteration.md)

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>O que é uma boa prática para modelar as intenções da minha app? Devo criar intenções mais específicas ou mais genéricas?

Escolha intenções que não sejam tão gerais que se sobreponham, mas não tão específicas que dificultam a distinção entre intenções semelhantes. Criar intenções discriminatórias específicas é uma das melhores práticas para a modelação DO LUIS.

### <a name="is-it-important-to-train-the-none-intent"></a>É importante treinar a intenção de Nenhum?

Sim, é bom treinar a sua intenção **de Zero** com mais expressões à medida que adiciona mais rótulos a outras intenções. Uma boa relação é 1 ou 2 rótulos adicionados a **Nenhum** por cada 10 rótulos adicionados a uma intenção. Este rácio impulsiona o poder discriminatório do LUIS.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Como posso corrigir erros ortográficos em expressões?

Consulte o tutorial da [API V7 da Verificação Ortográfica de Bing.](luis-tutorial-bing-spellcheck.md) A LUIS impõe limites impostos pela Bing Spell Check API V7.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Como edito a minha aplicação LUIS programáticamente?
Para editar a sua aplicação LUIS programáticamente, utilize [a API autoria.](https://go.microsoft.com/fwlink/?linkid=2092087) Consulte [a Call LUIS autoria da API](./get-started-get-model-rest-apis.md) e [construa uma aplicação LUIS programáticamente utilizando Node.js](./luis-tutorial-node-import-utterances-csv.md) por exemplo de como chamar a API de Autoria. A API autora requer que utilize uma [chave de autoria](luis-how-to-azure-subscription.md#azure-resources-for-luis) em vez de uma chave de ponto final. A autoria programática permite até 1.000.000 chamadas por mês e cinco transações por segundo. Para obter mais informações sobre as teclas que utiliza com o LUIS, consulte [as teclas Manage](./luis-how-to-azure-subscription.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Onde está a funcionalidade Padrão que proporcionava a correspondência regular da expressão?
A **função Padrão** anterior é atualmente depreciada, substituída por **[Padrões.](luis-concept-patterns.md)**

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Como uso uma entidade para retirar os dados corretos?
Consulte [entidades](luis-concept-entity-types.md) e [extração de dados.](luis-concept-data-extraction.md)

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>As variações de um exemplo de expressão devem incluir pontuação?
Utilize uma das seguintes soluções:
* Ignore [a pontuação](luis-reference-application-settings.md#punctuation-normalization)
* Adicione as diferentes variações como exemplo de declarações à intenção
* Adicione o padrão da expressão do exemplo com a [sintaxe para ignorar](luis-concept-patterns.md#pattern-syntax) a pontuação.

### <a name="does-luis-currently-support-cortana"></a>A LUIS apoia atualmente a Cortana?

As aplicações pré-construídas cortana foram depreciadas em 2017. Já não são apoiados.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Como transfira a propriedade de uma aplicação LUIS?
Para transferir uma aplicação LUIS para uma subscrição diferente do Azure, exporte a app LUIS e importe-a usando uma nova conta. Atualize o ID da aplicação LUIS na aplicação do cliente que a chama. A nova aplicação pode devolver pontuações ligeiramente diferentes da app original.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>Uma entidade pré-construída é marcada numa expressão de exemplo em vez da minha entidade personalizada. Como é que eu arranjo isto?

No portal LUIS, pode rotular texto para a entidade exata que está interessado em extrair. Se o portal LUIS não mostrar a previsão correta da entidade, poderá ter de adicionar mais expressões e rotular a entidade dentro do texto ou adicionar uma funcionalidade.

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Tentei importar uma aplicação ou ficheiro de versão, mas tive um erro, o que aconteceu?

Leia mais sobre [erros de importação de versão.](luis-how-to-manage-versions.md#import-errors)

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>Colaboração e contribuição

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-azure-role-based-access-control-azure-rbac"></a>Como posso dar aos colaboradores acesso ao LUIS com Azure Ative Directory (Azure AD) ou controlo de acesso baseado em funções Azure (Azure RBAC)?

Consulte [os recursos do Azure Ative Directory](luis-how-to-collaborate.md#azure-active-directory-resources)  e o utilizador inquilino do [Azure Ative Directory](luis-how-to-collaborate.md#azure-active-directory-tenant-user) para aprender a dar acesso aos colaboradores.

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Ponto final

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>Recebi um código de estado de erro HTTP 403. Como posso corrigi-lo?

Obtém 403 e 429 códigos de estado de erro quando excede as transações por segundo ou transações mensais para o seu nível de preços. Aumente o seu nível de preços ou utilize [recipientes](luis-container-howto.md)de compreensão linguística .

Quando utilizar todas essas consultas gratuitas de 1000 pontos finais ou exceder a quota mensal de transações do seu nível de preços, recebe um código de estado de erro HTTP 403.

Para corrigir este erro, tem de [alterar o seu nível de preços](luis-how-to-azure-subscription.md#change-the-pricing-tier) para um nível mais elevado ou criar um novo [recurso](get-started-portal-deploy-app.md#create-the-endpoint-resource) e [atribuí-lo à sua aplicação.](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal)

As soluções para este erro incluem:

* No [portal Azure](https://portal.azure.com), no seu recurso de Compreensão linguística, no **nível de preços de gestão de recursos ->**, altere o seu nível de preços para um nível de TPS mais elevado. Não precisa de fazer nada no portal de Compreensão de Idiomas se o seu recurso já estiver atribuído à sua aplicação de Compreensão de Idiomas.
*  Se o seu uso exceder o nível de preços mais elevado, adicione mais recursos de Compreensão linguística com um equilibrador de carga à sua frente. O [recipiente de compreensão de linguagem](luis-container-howto.md) com Kubernetes ou Docker Compose pode ajudar com isso.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>Recebi um código de estado de erro HTTP 429. Como posso corrigi-lo?

Obtém 403 e 429 códigos de estado de erro quando excede as transações por segundo ou transações mensais para o seu nível de preços. Aumente o seu nível de preços ou utilize [recipientes](luis-container-howto.md)de compreensão linguística .

Este código de estado é devolvido quando as suas transações por segundo excedem o seu nível de preços.

As soluções incluem:

* Pode [aumentar o seu nível de preços,](luis-how-to-azure-subscription.md#change-the-pricing-tier)se não estiver no nível mais alto.
* Se o seu uso exceder o nível de preços mais elevado, adicione mais recursos de Compreensão linguística com um equilibrador de carga à sua frente. O [recipiente de compreensão de linguagem](luis-container-howto.md) com Kubernetes ou Docker Compose pode ajudar com isso.
* Pode limitar os pedidos de candidatura ao seu cliente com uma [política de relemisão](/azure/architecture/best-practices/transient-faults#general-guidelines) que implementa quando obtém este código de estado.

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>A minha consulta de fim de dia deu resultados inesperados. O que devo fazer?

Os resultados inesperados da previsão de consulta baseiam-se no estado do modelo publicado. Para corrigir o modelo, poderá ter de alterar o modelo, treinar e publicar novamente.

A correção do modelo começa com [uma aprendizagem ativa.](luis-how-to-review-endpoint-utterances.md)

Pode remover a formação não determinística atualizando as definições de versão de [aplicação API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) para utilizar todos os dados de formação.

Reveja as [melhores práticas](luis-concept-best-practices.md) para outras dicas.

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Porque é que o LUIS acrescenta espaços à consulta em torno ou no meio das palavras?
LUIS [tokeniza](luis-glossary.md#token) a expressão baseada na [cultura.](luis-language-support.md#tokenization) Tanto o valor original como o valor simbólico estão disponíveis para [a extração de dados.](luis-concept-data-extraction.md#tokenized-entity-returned)

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Como posso criar e atribuir uma chave de ponta LUIS?
[Crie a chave de ponto final](luis-how-to-azure-subscription.md) em Azure para o seu nível de [serviço.](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) [Atribua a chave](luis-how-to-azure-subscription.md) na página **[Recursos Azure.](luis-how-to-azure-subscription.md)** Não existe uma API correspondente para esta ação. Em seguida, tem de alterar o pedido HTTP para o ponto final para [utilizar a nova tecla de ponto final](luis-how-to-azure-subscription.md).

### <a name="how-do-i-interpret-luis-scores"></a>Como interpreto as pontuações do LUIS?
O seu sistema deve utilizar a maior intenção de pontuação, independentemente do seu valor. Por exemplo, uma pontuação abaixo de 0,5 (menos de 50%) não significa necessariamente que LUIS tem pouca confiança. Fornecer mais dados de treino pode ajudar a aumentar a [pontuação](luis-concept-prediction-score.md) da intenção mais provável.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Porque é que não vejo os meus pontos finais no painel de instrumentos da minha aplicação?
Os acessos finais totais no Dashboard da sua aplicação são atualizados periodicamente, mas as métricas associadas à sua chave de ponta DE LUIS no portal Azure são atualizadas com mais frequência.

Se não vir os pontos finais atualizados no Painel de Instrumentos, inscreva-se no portal Azure e encontre o recurso associado à sua tecla de ponto final LUIS e abra **métricas** para selecionar a métrica **De Chamadas Totais.** Se a tecla de ponto final for utilizada para mais de uma aplicação LUIS, a métrica no portal Azure mostra o número agregado de chamadas de todas as aplicações LUIS que a utilizam.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Existe um comando PowerShell que chega à quota de ponto final?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pode utilizar um comando PowerShell para ver a quota de ponto final:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
```

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>A minha aplicação LUIS estava a funcionar ontem, mas hoje estou a ter 403 erros. Não mudei a aplicação. Como posso corrigi-lo?
Siga estas [instruções](#how-do-i-create-and-assign-a-luis-endpoint-key) para criar uma chave de ponta LUIS e atribuí-la à aplicação. Em seguida, tem de alterar o pedido HTTP da aplicação do cliente para o ponto final para [utilizar a nova chave de ponto final](luis-how-to-azure-subscription.md). Se criou um novo recurso numa região diferente, altere também a região do pedido do cliente HTTP.

### <a name="how-do-i-secure-my-luis-endpoint"></a>Como posso assegurar o meu ponto final LUIS?
Ver [Assegurar o ponto final.](luis-how-to-azure-subscription.md#securing-the-endpoint)

## <a name="working-within-luis-limits"></a>Trabalhar dentro dos limites luis

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Qual é o número máximo de intenções e entidades que uma app LUIS pode suportar?
Veja a referência [dos limites.](luis-limits.md)

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Quero construir uma app LUIS com mais do que o número máximo de intenções. O que devo fazer?

Consulte [as melhores práticas para as intenções.](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents)

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Quero construir uma app na LUIS com mais do que o número máximo de entidades. O que devo fazer?

Ver [Melhores Práticas para entidades](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Quais são os limites do número e tamanho das listas de frases?
Para o comprimento máximo de uma lista de [frases,](./luis-concept-feature.md)consulte a referência [de limites.](luis-limits.md)

### <a name="what-are-the-limits-on-example-utterances"></a>Quais são os limites das expressões de exemplo?
Veja a referência [dos limites.](luis-limits.md)

## <a name="testing-and-training"></a>Teste e formação

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Vejo alguns erros no painel de testes do lote para alguns dos modelos da minha aplicação. Como posso resolver este problema?

Os erros indicam que existe alguma discrepância entre as suas etiquetas e as previsões dos seus modelos. Para resolver o problema, faça uma ou ambas as seguintes tarefas:
* Para ajudar o LUIS a melhorar a discriminação entre as intenções, adicione mais rótulos.
* Para ajudar o LUIS a aprender mais rapidamente, adicione funcionalidades de lista de frases que introduzam vocabulário específico de domínio.

Consulte o tutorial [de testes](./luis-how-to-batch-test.md) do Lote.

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Quando uma aplicação é exportada então reimportada para uma nova app (com um novo ID de aplicação), as pontuações de previsão do LUIS são diferentes. Porque é que isto acontece?

Ver [Diferenças de Previsão entre cópias da mesma aplicação.](luis-concept-prediction-score.md#review-intents-with-similar-scores)

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Algumas declarações vão para a intenção errada depois de eu ter feito alterações na minha aplicação. A questão parece desaparecer ao acaso. Como posso corrigi-lo?

Ver [Comboio com todos os dados.](luis-how-to-train.md#train-with-all-data)

## <a name="app-publishing"></a>Publicação de aplicativos

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Qual é a iD do inquilino na janela "Adicionar uma chave à sua aplicação"?
Em Azure, um inquilino representa o cliente ou organização que está associado a um serviço. Encontre o seu ID do seu inquilino no portal Azure na caixa de ID do **Diretório** selecionando propriedades de gestão **de diretório azure**  >    >  ative.

![ID do inquilino no portal Azure](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Por que há mais chaves de ponto final atribuídas à minha app do que eu atribuí?
Cada aplicação LUIS tem a chave de autoria/arranque na lista de pontos finais como uma conveniência. Esta chave permite apenas alguns toques de ponto final para que possa experimentar o LUIS.

Se a sua aplicação existisse antes de o LUIS estar geralmente disponível (GA), as teclas de ponto final LUIS na sua subscrição são atribuídas automaticamente. Isto foi feito para facilitar a migração da AG. Quaisquer novas teclas de ponto final LUIS no portal Azure _não_ são automaticamente atribuídas ao LUIS.

## <a name="key-management"></a>Gestão de chaves

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Como é que eu sei de que chave preciso, onde a consigo e o que faço com ela?

Consulte [as teclas de ponto final de autoria e previsão de consulta no LUIS](luis-how-to-azure-subscription.md) para saber mais sobre as diferenças entre a chave de autoria e a chave de tempo de execução de previsão.

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Tenho um erro em não ter quota. Como posso corrigi-lo?

Consulte, Corrija o código de estado [HTTP 403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) e [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) para saber mais.

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>Preciso de lidar com mais consultas de ponta. Como posso fazê-lo?

Consulte, Corrija o código de estado [HTTP 403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) e [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) para saber mais.

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>Criei uma chave de autoria, mas não está a aparecer no portal LUIS. O que aconteceu?

As teclas de autoria estão disponíveis no portal LUIS após [migrar para a experiência-chave de autoria](luis-migration-authoring.md).

## <a name="app-management"></a>Gestão de aplicações

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Como faço o download de um registo de declarações de utilizadores?
Por padrão, a sua aplicação LUIS regista declarações dos utilizadores. Para descarregar um registo de declarações que os utilizadores enviam para a sua aplicação LUIS, vá às **Minhas Apps** e selecione a aplicação. Na barra de ferramentas contextual, selecione **Registos de ponto final de exportação**. O registo é formatado como um ficheiro de valor separado em vírgula (CSV).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Como posso desativar o registo de expressões?
Pode desativar o registo de declarações do utilizador definindo `log=false` no URL Endpoint que a sua aplicação do cliente utiliza para consultar o LUIS. No entanto, desligar o registo desativa a capacidade da sua app LUIS de sugerir expressões ou melhorar o desempenho que se baseia na [aprendizagem ativa.](luis-concept-review-endpoint-utterances.md#what-is-active-learning) Se você definir `log=false` por causa de preocupações de privacidade de dados, você não pode baixar um registo dessas declarações de usuários de LUIS ou usar essas expressões para melhorar a sua app.

O registo madeireira é o único armazenamento de expressões.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Por que não quero todas as minhas declarações de ponto final registadas?
Se estiver a utilizar o seu registo para análise de previsão, não capture as expressões de teste no seu registo.

## <a name="data-management"></a>Gestão de dados

### <a name="can-i-delete-data-from-luis"></a>Posso apagar dados do LUIS?

* Pode sempre apagar palavras de exemplo usadas para treinar o LUIS. Se eliminar um exemplo da sua aplicação LUIS, este é removido do serviço web LUIS e não está disponível para exportação.
* Pode eliminar as declarações da lista de declarações de utilizador que a LUIS sugere na página de comentários de ponto final do Ponto final do **Ponto de Avaliação.** A eliminação de declarações desta lista impede que sejam sugeridas, mas não as apaga dos registos.
* Se eliminar uma conta, todas as aplicações são eliminadas, juntamente com as suas palavras de exemplo e registos. Os dados são retidos nos servidores durante 60 dias antes de serem eliminados permanentemente.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Como é que a Microsoft gere os dados que envio ao LUIS?

O [Trust Center](https://www.microsoft.com/trustcenter) explica os nossos compromissos e as suas opções de gestão de dados e acesso nos Serviços Azure.

## <a name="language-and-translation-support"></a>Suporte à língua e à tradução

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Tenho uma aplicação num idioma e quero criar uma aplicação paralela noutro idioma. Qual é a maneira mais fácil de o fazer?
1. Exporte a sua aplicação.
2. Traduza as expressões rotuladas no ficheiro JSON da aplicação exportada para o idioma-alvo.
3. Pode ter de alterar os nomes das intenções e entidades ou deixá-los como estão.
4. Finalmente, importe a app para ter uma app LUIS no idioma alvo.

## <a name="app-notification"></a>Notificação de aplicativos

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Por que recebi um e-mail a dizer que estou quase sem quota?
A sua tecla de autoria/arranque só é permitida 1000 consultas de ponto final por mês. Crie uma chave de ponto final LUIS (gratuita ou paga) e use essa chave ao fazer consultas de ponto final. Se estiver a fazer consultas de ponto final a partir de um bot ou de outra aplicação de cliente, tem de alterar a chave de ponta DO LUIS.

## <a name="bots"></a>Bots

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>O meu bot LUIS não está a funcionar. O que posso fazer?

A primeira questão é isolar se a questão está relacionada com o LUIS ou acontece fora do middleware LUIS.

#### <a name="resolve-issue-in-luis"></a>Resolver problema no LUIS
Passe a mesma expressão para LUIS a partir do [ponto final do LUIS.](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) Se receber um erro, resolva o problema no LUIS até que o erro não seja mais devolvido. Os erros comuns incluem:

* `Out of call volume quota. Quota will be replenished in <time>.` - Esta questão indica que tem de mudar de uma chave de autoria para uma [chave de ponto final](luis-how-to-azure-subscription.md) ou precisa de alterar os [níveis](luis-how-to-azure-subscription.md#change-the-pricing-tier)de serviço .

#### <a name="resolve-issue-in-azure-bot-service"></a>Resolver problema no Azure Bot Service

Se estiver a utilizar o Serviço Azure Bot e o problema é que o **Teste no Web Chat** retorna, verifique os seus `Sorry, my bot code is having an issue` registos:

1. No portal Azure, para o seu bot, a partir da secção de **gestão bot,** selecione **Build**.
1. Abra o editor de código online.
1. Na parte superior, barra de navegação azul, selecione o nome bot (o segundo item à direita).
1. Na lista de drop-down resultante, selecione **Open Kudu Console**.
1. Selecione **'Registar'** e, em seguida, selecione **'Aplicação'.** Reveja todos os ficheiros de registo. Se não vir o erro na pasta de aplicação, reveja todos os ficheiros de registo em **Ficheiros de Registo**.
1. Lembre-se de reconstruir o seu projeto se estiver a utilizar uma linguagem compilada como C#.

> [!Tip]
> A consola também pode instalar pacotes.

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Resolva o problema enquanto depura na máquina local com a Bot Framework.

Para saber mais sobre a depuragem local de um bot, consulte [Debug a bot.](/azure/bot-service/bot-service-debug-bot)

## <a name="integrating-luis"></a>Integração DO LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Onde é criada a minha app LUIS durante o processo de subscrição de bot de aplicação web Azure?
Se selecionar um modelo LUIS e selecionar o botão **Selecione** no painel de modelos, o painel do lado esquerdo muda para incluir o tipo de modelo e pergunta em que região criar o modelo LUIS. O processo de bot de aplicação web não cria uma subscrição LUIS no entanto.

![Luis modelo web app bot região](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Que regiões luis apoiam o discurso do Quadro bot?
[A escorva da fala](/bot-framework/bot-service-manage-speech-priming) só é suportada para aplicações LUIS no caso central (EUA).

## <a name="api-programming-strategies"></a>Estratégias de Programação da API

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Como posso, programáticamente, obter a região LUIS de um recurso?

Utilize a amostra LUIS para encontrar a [região](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) programáticamente utilizando C# ou Node.Js.

## <a name="luis-service"></a>Serviço LUIS

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>A Compreensão linguística (LUIS) está disponível no local ou em nuvem privada?

Sim, pode utilizar o [recipiente](luis-container-howto.md) LUIS para estes cenários se tiver a conectividade necessária para a utilização do medidor.

## <a name="migrating-to-the-next-version"></a>Migrando para a próxima versão

### <a name="how-do-i-migrate-to-preview-v3-api"></a>Como posso migrar para pré-visualização V3 API?

Ver [API v2 para v3 Guia de migração para apps LUIS](luis-migration-api-v3.md)

## <a name="build-2019-conference-announcements"></a>Construir anúncios da Conferência 2019

As seguintes funcionalidades foram lançadas na Conferência Build 2019:

* [Pré-visualização do guia de migração da API V3](luis-migration-api-v3.md)
* [Painel de análise melhorado](luis-how-to-use-dashboard.md)
* [Domínios pré-construídos melhorados](luis-reference-prebuilt-domains.md)
* [Entidades de lista dinâmica](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Entidades externas](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

Vídeos:

* [Como usar a Azure Conversational IA para escalar o seu negócio para a próxima geração](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o LUIS, consulte os seguintes recursos:
* [Perguntas de Stack Overflow marcadas com LUIS](https://stackoverflow.com/questions/tagged/luis)
* [Microsoft Q&Uma página de perguntas para msdn language understanding intelligent services (LUIS)](/answers/topics/azure-language-understanding.html)