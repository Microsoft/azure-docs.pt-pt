---
title: Como usar teclas de autoria e tempo de execução - LUIS
description: Quando utiliza pela primeira vez a Compreensão linguística (LUIS), não precisa de criar uma chave de autoria. Quando pretende publicar a aplicação e, em seguida, utilizar o seu ponto final de tempo de execução, tem de criar e atribuir a chave de tempo de execução à aplicação.
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: adc0c253648ed7ae869a20b92c42e7f6478501b7
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151585"
---
# <a name="create-luis-resources"></a>Criar recursos LUIS

Os recursos de prazo de previsão de autoria e consulta fornecem autenticação à sua app LUIS e ponto final de previsão.

<a name="azure-resources-for-luis"></a>
<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

## <a name="luis-resources"></a>Recursos LUIS

A LUIS permite três tipos de recursos Azure e um recurso não-Azure:

|Recurso|Objetivo|Serviço cognitivo `kind`|Serviço cognitivo `type`|
|--|--|--|--|
|Recurso de autoria|Permite-lhe criar, gerir, treinar, testar e publicar as suas aplicações. [Crie um recurso de autoria LUIS](luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal) se pretender autoriar aplicações LUIS programáticas ou a partir do Portal LUIS. Primeiro, tem de [migrar a sua conta LUIS](luis-migration-authoring.md#what-is-migration) para poder ligar os seus recursos Azure à sua aplicação. Pode controlar permissões ao recurso de autoria atribuindo as pessoas ao [papel de contribuinte](#contributions-from-other-authors). <br><br> Existe um nível de avialável para o recurso de autoria LUIS:<br> * **Recurso gratuito de autoria F0** que lhe dá 1M Transações de autoria gratuitas e 1000 pedidos de previsão de teste gratuito mensalmente. |`LUIS.Authoring`|`Cognitive Services`|
|Recurso de previsão| Depois de publicar a sua aplicação LUIS, utilize o recurso/chave de previsão para consultar pedidos de ponto final de previsão. Crie um recurso de previsão LUIS antes que a sua aplicação do cliente solicite previsões para além dos 1.000 pedidos fornecidos pela Autoria ou pelo recurso inicial. <br><br> Existem dois níveis de avialável para o recurso de previsão:<br> * **Recurso gratuito de previsão de F0** que lhe dá 10.000 pedidos de previsão gratuita mensalmente<br> * **Recurso de previsão padrão S0** que é o nível pago. [Saiba mais sobre os detalhes dos preços](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)|`LUIS`|`Cognitive Services`|
|Recurso inicial/experimental|Permite-lhe criar, gerir, treinar, testar e publicar as suas aplicações. Isto é criado por surdo se escolher a opção de recurso inicial enquanto se inscreve pela primeira vez no tp LUIS. No entanto, a chave de arranque acabará por ser depreciada e todos os utilizadores de LUIS terão de [migrar as suas contas](luis-migration-authoring.md#what-is-migration) e ligar as suas aplicações LUIS a um recurso de autoria. Este recurso não lhe dá permissões para o controlo de acesso baseado em funções Azure, como o recurso de autoria. <br><br> Tal como o recurso de autoria, o recurso inicial dá-lhe 1M transações de autoria gratuitas e 1000 pedidos de previsão de teste gratuitos.|-|Não é um recurso Azure|
|[Chave de recursos multi-serviço do Serviço Cognitivo](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Pedidos de previsão de consulta partilhados com LUIS e outros Serviços Cognitivos apoiados.|`CognitiveServices`|`Cognitive Services`|


> [!Note]
> Existem dois tipos de recursos F0 (free tier) que a LUIS fornece. Um para a autoria de transações e outro para transações de previsão. Se estiver a ficar sem quota gratuita para transações de previsão, certifique-se de que está de facto a utilizar o recurso de previsão F0 que lhe dá 10.000 transações gratuitas mensais e não o recurso de autoria que lhe dá 1000 transações de previsão mensais.

Quando o processo de criação de recursos Azure estiver concluído, [atribua o recurso](#assign-a-resource-to-an-app) à app no portal LUIS.

É importante para o autor de apps LUIS em [regiões](luis-reference-regions.md#publishing-regions) onde você quer publicar e consultar.

## <a name="resource-ownership"></a>Propriedade de recursos

Um recurso Azure, como um LUIS, é propriedade da subscrição que contém o recurso.

Para transferir a propriedade de um recurso, pode:
* Transferir [propriedade](../../cost-management-billing/manage/billing-subscription-transfer.md) da sua subscrição
* Exporte a app LUIS como um ficheiro e, em seguida, importe a app numa subscrição diferente. A exportação está disponível na página **my apps** no portal LUIS.


## <a name="resource-limits"></a>Limites de recursos

### <a name="authoring-key-creation-limits"></a>Autoria de limites-chave de criação

Pode criar até 10 chaves de autoria por região por subscrição.

Ver [Limites-Chave](luis-limits.md#key-limits) e [Regiões de Azure.](luis-reference-regions.md)

As regiões editoriais são diferentes das regiões de autoria. Certifique-se de criar uma aplicação na região de autoria correspondente à região editorial que pretende que a sua aplicação ao cliente seja localizada.

### <a name="key-usage-limit-errors"></a>Erros-limite de utilização chave

Os limites de utilização baseiam-se no nível de preços.

Se exceder a quota de transações por segundo (TPS), receberá um erro 429 de HTTP. Se exceder a quota de transações por mês (TPS), receberá um erro 403 de HTTP.


### <a name="reset-authoring-key"></a>Chave de autoria de reset

Para a autoria de aplicações [migradas de recursos:](luis-migration-authoring.md) se a sua chave de autoria estiver comprometida, reinicie a chave no portal Azure na página **Keys** para esse recurso de autoria.

Para aplicações que ainda não migraram: a chave é reposta em todas as suas aplicações no portal LUIS. Se autorize as suas aplicações através das APIs de autoria, tem de alterar o valor da Chave de Subscrição Ocp-Apim para a nova chave.

### <a name="regenerate-azure-key"></a>Chave Azure regenerada

Regenerar as chaves Azure do portal Azure, na página **Keys.**


<a name="securing-the-endpoint"></a>

## <a name="app-ownership-access-and-security"></a>Propriedade, acesso e segurança de aplicativos

Uma aplicação é definida pelos seus recursos Azure, que é determinada pela subscrição do proprietário.

Pode mover a sua aplicação LUIS. Utilize os seguintes recursos documentais no portal Azure ou no Azure CLI:

* [Mover app entre recursos de autoria LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Mover recurso para novo grupo de recursos ou subscrição](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Mover recurso dentro da mesma subscrição ou em todas as subscrições](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


### <a name="contributions-from-other-authors"></a>Contribuições de outros autores

Para a autoria de aplicações [migradas de recursos:](luis-migration-authoring.md) _os contribuintes_ são geridos no portal Azure para o recurso de autoria, utilizando a página **access control (IAM).** Saiba [como adicionar um utilizador,](luis-how-to-collaborate.md)utilizando o endereço de e-mail do colaborador e o papel do _contribuinte._

Para apps que ainda não migraram: todos os _colaboradores_ são geridos no portal LUIS a partir da página **Manage -> Colaboradores.**

### <a name="query-prediction-access-for-private-and-public-apps"></a>Acesso de previsão de consulta para aplicações privadas e públicas

Para uma aplicação **privada,** o acesso de previsão de consulta está disponível para proprietários e colaboradores. Para uma aplicação **pública,** o acesso ao tempo de execução está disponível para todos os que têm o seu próprio recurso de tempo de execução Azure [Cognitive Service](../cognitive-services-apis-create-account.md) ou [LUIS,](#create-resources-in-the-azure-portal) e tem o ID da aplicação pública.

Atualmente, não existe um catálogo de aplicações públicas.

### <a name="authoring-permissions-and-access"></a>Permissões de autoria e acesso
O acesso à aplicação a partir do portal [LUIS](luis-reference-regions.md#luis-website) ou das [APIs de autoria](https://go.microsoft.com/fwlink/?linkid=2092087) é controlado pelo recurso de autoria Azure.

O proprietário e todos os colaboradores têm acesso ao autor da app.

|O acesso à autoria inclui|Notas|
|--|--|
|Adicione ou remova as teclas do ponto final||
|Versão exportadora||
|Registos de pontos finais de exportação||
|Versão importadora||
|Tornar a aplicação pública|Quando uma aplicação é pública, qualquer pessoa com uma chave de autoria ou ponto final pode consultar a aplicação.|
|Alterar modelo|
|Publicar|
|Rever declarações de ponto final para [aprendizagem ativa](luis-how-to-review-endpoint-utterances.md)|
|Preparar|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Previsão de acesso ao tempo de execução do ponto final

O acesso à consulta do ponto final de previsão é controlado por uma definição na página **'Informações de Aplicação'** na secção **'Gerir'.**

|[Ponto final privado](#runtime-security-for-private-apps)|[Ponto final público](#runtime-security-for-public-apps)|
|:--|:--|
|Disponível para proprietário e contribuintes|Disponível para proprietário, contribuintes e qualquer outra pessoa que conheça o ID da aplicação|

Pode controlar quem vê a sua chave de tempo de execução LUIS chamando-a num ambiente servidor-servidor. Se estiver a utilizar o LUIS a partir de um bot, a ligação entre o bot e o LUIS já está segura. Se estiver a ligar diretamente para o ponto final LUIS, deverá criar uma API do lado do servidor (tal como uma [função](https://azure.microsoft.com/services/functions/)Azure) com acesso controlado (como [AAD).](https://azure.microsoft.com/services/active-directory/) Quando a API do lado do servidor for chamada e autenticada e a autorização for verificada, passe a chamada para o LUIS. Embora esta estratégia não impeça ataques man-in-the-middle, ele obstaculiza a sua CHAVE e URL de ponto final dos seus utilizadores, permite-lhe rastrear o acesso, e permite-lhe adicionar registo de resposta de ponto final (como [Insights de Aplicação).](https://azure.microsoft.com/services/application-insights/)

### <a name="runtime-security-for-private-apps"></a>Segurança de tempo de execução para aplicativos privados

O tempo de execução de uma aplicação privada só está disponível para o seguinte:

|Chave e utilizador|Explicação|
|--|--|
|Chave de autoria do proprietário| Até 1000 pontos finais|
|Chaves de autoria colaborador/colaboradora| Até 1000 pontos finais|
|Qualquer chave atribuída à LUIS por um autor ou colaborador/colaborador|Baseado no nível de utilização chave|

### <a name="runtime-security-for-public-apps"></a>Segurança de tempo de execução para aplicações públicas

Uma vez configurada uma aplicação como pública, _qualquer_ chave de autoria válida luis ou chave de ponto final LUIS pode consultar a sua app, desde que a chave não tenha usado toda a quota de ponto final.

Um utilizador que não seja proprietário ou colaborador, só pode aceder ao tempo de execução de uma aplicação pública se for dado o ID da aplicação. A LUIS não tem _um mercado_ público ou outra forma de procurar uma aplicação pública.

Uma aplicação pública é publicada em todas as regiões para que um utilizador com uma chave de recursos LUIS baseada na região possa aceder à aplicação em qualquer região que esteja associada à chave de recursos.


### <a name="securing-the-query-prediction-endpoint"></a>Assegurar o ponto final de previsão de consulta

Pode controlar quem pode ver a tecla de ponto final de previsão LUIS, chamando-a num ambiente servidor-servidor. Se estiver a utilizar o LUIS a partir de um bot, a ligação entre o bot e o LUIS já está segura. Se estiver a ligar diretamente para o ponto final LUIS, deverá criar uma API do lado do servidor (tal como uma [função](https://azure.microsoft.com/services/functions/)Azure) com acesso controlado (como [AAD).](https://azure.microsoft.com/services/active-directory/) Quando a API do lado do servidor for chamada e a autenticação e a autorização forem verificadas, passe a chamada para o LUIS. Embora esta estratégia não impeça ataques man-in-the-middle, ele obstaculiza o seu ponto final dos seus utilizadores, permite-lhe rastrear o acesso, e permite-lhe adicionar registo de resposta de ponto final (como [Insights de Aplicação).](https://azure.microsoft.com/services/application-insights/)

<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Inscreva-se no portal LUIS e comece a autorizar

1. Inscreva-se no [portal LUIS](https://www.luis.ai) e concorde com os termos de utilização.
1. Inicie a sua aplicação LUIS escolhendo a sua chave de autoria Azure LUIS.

   ![Escolha um tipo de recurso de autoria de compreensão linguística](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Quando terminar o processo de seleção de recursos, [crie uma nova aplicação.](luis-how-to-start-new-app.md#create-new-app-in-luis)


<a name="create-azure-resources"></a>
<a name="create-resources-in-the-azure-portal"></a>

[!INCLUDE [Create LUIS resource in Azure portal](includes/create-luis-resource.md)]

### <a name="create-resources-in-azure-cli"></a>Criar recursos em Azure CLI

Utilize o [CLI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para criar cada recurso individualmente.

`kind`Recurso:

* Autoria: `LUIS.Authoring`
* Previsão: `LUIS`

1. Inscreva-se no Azure CLI:

    ```azurecli
    az login
    ```

    Isto abre um browser para permitir que você selecione a conta correta e forneça a autenticação.

1. Criar um recurso de **autoria LUIS,** de `LUIS.Authoring` espécie, nomeado `my-luis-authoring-resource` no grupo de recursos _existente_ nomeado para `my-resource-group` a `westus` região.

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Criar um recurso de ponto final de **previsão LUIS,** de `LUIS` espécie, nomeado `my-luis-prediction-resource` no grupo de recursos _existente_ nomeado para `my-resource-group` a `westus` região. Se quiser uma produção superior à do nível livre, mude `F0` para `S0` . Saiba mais sobre [os níveis de preços e a produção.](luis-limits.md#key-limits)

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > Estas teclas **não** são utilizadas pelo portal LUIS até que sejam atribuídas no portal LUIS sobre os **recursos Manage -> Azure**.

<a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>

### <a name="assign-resource-in-the-luis-portal"></a>Atribuir recurso no portal LUIS

Pode atribuir um recurso de autoria para uma única aplicação ou para todas as aplicações no LUIS. O procedimento a seguir atribui todas as aplicações a um único recurso de autoria.

1. Inscreva-se no [portal LUIS.](https://www.luis.ai)
1. Na barra de navegação superior, para a extrema direita, selecione a sua conta de utilizador e, em seguida, selecione **Definições**.
1. Na página **Definições** do Utilizador, selecione **Adicionar o recurso de autoria** e, em seguida, selecione um recurso de autoria existente. Selecione **Guardar**.

## <a name="assign-a-resource-to-an-app"></a>Atribuir um recurso a uma aplicação

Por favor, note que se não tiver uma subscrição Azure, não poderá atribuir ou criar um novo recurso. Você terá que primeiro ir e criar um [Azure Free Trial](https://azure.microsoft.com/en-us/free/) em seguida, voltar ao LUIS para criar um novo recurso a partir do portal.

Pode atribuir ou criar um recurso de autoria ou de previsão a uma aplicação com o seguinte procedimento:

1. Inscreva-se no [portal LUIS,](https://www.luis.ai)em seguida, selecione uma aplicação da lista **de aplicações My**
1. Navegue para a página **de recursos Do Azure Gerir ->**

    ![Selecione os recursos Manage-> Azure no portal LUIS para atribuir um recurso à app.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Selecione o separador de recursos de Previsão ou Autoria e, em seguida, selecione o **recurso de previsão adicionar** ou adicionar o botão de recurso de **autoria**
1. Selecione os campos no formulário para encontrar o recurso correto e, em seguida, **selecione Guardar**
1. Se não tiver um recurso de exisite, pode criar um selecionando "Criar um novo recurso LUIS?" do fundo da janela


### <a name="assign-query-prediction-runtime-resource-without-using-luis-portal"></a>Atribuir recurso de previsão de consulta sem usar o portal LUIS

Para fins de automatização, como um pipeline CI/CD, pode querer automatizar a atribuição de um recurso de runtime LUIS a uma aplicação LUIS. Para isso, é necessário executar os seguintes passos:

1. Obtenha um token Azure Resource Manager deste [site.](https://resources.azure.com/api/token?plaintext=true) Este token expira assim usá-lo imediatamente. O pedido devolve um token do Gestor de Recursos Azure.

    ![Solicite ao Azure Resource Manager token e receba o token do Gestor de Recursos Azure](./media/luis-manage-keys/get-arm-token.png)

1. Utilize o token para solicitar os recursos de execução LUIS através de subscrições, a partir das [contas Get LUIS Azure API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), a que a sua conta de utilizador tem acesso.

    Esta API POST requer as seguintes definições:

    |Cabeçalho|Valor|
    |--|--|
    |`Authorization`|O valor `Authorization` `Bearer {token}` de. Note que o valor simbólico deve ser precedido pela palavra `Bearer` e por um espaço.|
    |`Ocp-Apim-Subscription-Key`|A sua chave de autoria.|

    Esta API devolve uma série de objetos JSON das suas assinaturas LUIS, incluindo ID de subscrição, grupo de recursos e nome de recurso, devolvidos como nome de conta. Encontre o único item na matriz que é o recurso LUIS para atribuir à app LUIS.

1. Atribua o token ao recurso LUIS com as [contas Atribuição de Uma Azure LUIS a uma API de aplicação.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515)

    Esta API POST requer as seguintes definições:

    |Tipo|Definição|Valor|
    |--|--|--|
    |Cabeçalho|`Authorization`|O valor `Authorization` `Bearer {token}` de. Note que o valor simbólico deve ser precedido pela palavra `Bearer` e por um espaço.|
    |Cabeçalho|`Ocp-Apim-Subscription-Key`|A sua chave de autoria.|
    |Cabeçalho|`Content-type`|`application/json`|
    |Consulta|`appid`|Pelo ID de aplicação do LUIS.
    |Corpo||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"Grupo de Recursos": "grupo de recursos-2",<br>"Nome da conta": "luis-uswest-S0-2"}|

    Quando esta API tiver sucesso, devolve um 201 - estatuto criado.

## <a name="unassign-resource"></a>Recurso de unassign

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e, em seguida, selecione uma aplicação da lista **de aplicações My.**
1. Navegue para a página **de recursos Manage-> Azure.**
1. Selecione o separador de recursos de Previsão ou autoria e, em seguida, selecione o botão **de recurso Design** para o recurso.

Quando não se indemeça um recurso, este não é apagado do Azure. Só está desvinculado do LUIS.


## <a name="delete-account"></a>Eliminar conta

Consulte [o armazenamento de dados e a remoção](luis-concept-data-storage.md#accounts) para obter informações sobre os dados que são eliminados quando elimina a sua conta.

## <a name="change-pricing-tier"></a>Alterar escalão de preço

1.  Em [Azure,](https://portal.azure.com)encontre a sua assinatura LUIS. Selecione a assinatura LUIS.
    ![Encontre a sua assinatura LUIS](./media/luis-usage-tiers/find.png)
1.  Selecione **o nível de preços** para ver os níveis de preços disponíveis.
    ![Ver níveis de preços](./media/luis-usage-tiers/subscription.png)
1.  Selecione o nível de preços e **selecione Selecione** para guardar a sua alteração.
    ![Altere o seu nível de pagamento LUIS](./media/luis-usage-tiers/plans.png)
1.  Quando a alteração dos preços estiver completa, uma janela pop-up verifica o novo nível de preços.
    ![Verifique o seu nível de pagamento LUIS](./media/luis-usage-tiers/updated.png)
1. Lembre-se de [atribuir esta chave de ponto final](#assign-a-resource-to-an-app) na página **Publicar** e usá-la em todas as consultas de ponto final.

## <a name="viewing-azure-resource-metrics"></a>Visualização das métricas de recursos do Azure

### <a name="viewing-azure-resource-summary-usage"></a>Visualização do uso do resumo do recurso Azure
Pode ver informações de utilização do LUIS em Azure. A **página 'Visão Geral'** mostra informações sumárias recentes, incluindo chamadas e erros. Se esborecir um pedido de ponto final LUIS, então assista imediatamente à **página 'Vista Geral',** deixe até cinco minutos para que a utilização apareça.

![Visualização do uso do resumo](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Personalizar gráficos de utilização de recursos Azure
As métricas fornecem uma visão mais detalhada dos dados.

![Métricas padrão](./media/luis-usage-tiers/metrics-default.png)

Pode configurar as suas tabelas de métricas para o período de tempo e o tipo métrico.

![Métricas personalizadas](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Alerta de limiar total de transações
Se quiser saber quando atingiu um determinado limiar de transação, por exemplo 10.000 transações, pode criar um alerta.

![Alertas predefinidos](./media/luis-usage-tiers/alert-default.png)

Adicione um alerta métrico para a métrica total das **chamadas** durante um determinado período de tempo. Adicione endereços de e-mail de todas as pessoas que devem receber o alerta. Adicione webhooks para todos os sistemas que devem receber o alerta. Também pode executar uma aplicação lógica quando o alerta é desencadeado.

## <a name="next-steps"></a>Passos seguintes

* Aprenda [a usar versões](luis-how-to-manage-versions.md) para controlar o ciclo de vida da sua aplicação.
* Migrar para o novo [recurso de autoria](luis-migration-authoring.md)
