---
title: Utilização de teclas de autoria e tempo de execução - LUIS
description: Quando se usa o LUIS pela primeira vez, não é necessário criar uma chave de autoria. Quando pretende publicar a aplicação e, em seguida, utilizar o seu ponto final de tempo de execução, tem de criar e atribuir a chave de tempo de execução à aplicação.
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: a71c1a0df1a72e3831fa54a041539f62b38a0aca
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95999914"
---
# <a name="create-luis-resources"></a>Criar recursos LUIS

Os recursos de prazo de previsão de autoria e consulta fornecem autenticação à sua app de Compreensão linguística (LUIS) e ponto final de previsão.

<a name="azure-resources-for-luis"></a>
<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

## <a name="luis-resources"></a>Recursos LUIS

A LUIS permite três tipos de recursos Azure e um recurso não-Azure:

|Recurso|Objetivo|Serviço cognitivo `kind`|Serviço cognitivo `type`|
|--|--|--|--|
|Recurso de autoria|Permite-lhe criar, gerir, treinar, testar e publicar as suas aplicações. [Crie um recurso de autoria LUIS](luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal) se pretender autoriar aplicações LUIS programáticas ou a partir do portal LUIS. Tem de [migrar](luis-migration-authoring.md#what-is-migration) a sua conta LUIS antes de ligar os recursos de autoria do Azure à sua aplicação. Pode controlar as permissões ao recurso de autoria atribuindo às pessoas [o papel de contribuinte](#contributions-from-other-authors). <br><br> Um nível está disponível para o recurso de autoria LUIS:<br> <ul> <li>**Recurso de autoria gratuita de F0**, que lhe dá 1 milhão de transações de autoria gratuitas e 1.000 pedidos de previsão de testes gratuitos mensalmente. |`LUIS.Authoring`|`Cognitive Services`|
|Recurso de previsão| Depois de publicar a sua aplicação LUIS, utilize o recurso/chave de previsão para consultar pedidos de ponto final de previsão. Crie um recurso de previsão LUIS antes que a sua aplicação do cliente solicite previsões para além dos 1.000 pedidos fornecidos pelo recurso de autoria ou arranque. <br><br> Estão disponíveis dois níveis para o recurso de previsão:<br><ul> <li> **Recurso de previsão F0 gratuito**, que lhe dá 10.000 pedidos de previsão gratuita mensalmente.<br> <li> **Recurso de previsão padrão S0**, que é o nível pago. [Saiba mais sobre preços.](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)|`LUIS`|`Cognitive Services`|
|Recurso inicial/experimental|Permite-lhe criar, gerir, treinar, testar e publicar as suas aplicações. Este recurso é criado por padrão se escolher a opção de recurso inicial quando iniciar a sação pela primeira vez no LUIS. A chave de arranque acabará por ser depreciada. Todos os utilizadores de LUIS terão de [migrar as suas contas](luis-migration-authoring.md#what-is-migration) e ligar as suas aplicações LUIS a um recurso de autoria. Ao contrário do recurso de autoria, este recurso não lhe dá permissões para o controlo de acesso baseado em funções Azure. <br><br> Tal como o recurso de autoria, o recurso inicial dá-lhe 1 milhão de transações de autoria gratuitas e 1.000 pedidos de previsão de teste gratuitos.|-|Não é um recurso Azure.|
|[Chave de recursos multiserviços de serviços cognitivos](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Pedidos de previsão de consulta partilhados com LUIS e outros serviços cognitivos apoiados.|`CognitiveServices`|`Cognitive Services`|


> [!Note]
> A LUIS fornece dois tipos de recursos F0 (free tier): um para a autoria de transações e outro para transações de previsão. Se estiver a ficar sem quota gratuita para transações de previsão, certifique-se de que está a usar o recurso de previsão F0, que lhe dá 10.000 transações gratuitas mensais, e não o recurso de autoria, o que lhe dá 1.000 transações de previsão mensais.

Quando o processo de criação de recursos Azure estiver concluído, [atribua o recurso](#assign-a-resource-to-an-app) à app no portal LUIS.

> [!important]
> Você deve autor de aplicativos LUIS nas [regiões](luis-reference-regions.md#publishing-regions) onde você quer publicar e consultar.

## <a name="resource-ownership"></a>Propriedade de recursos

Um recurso Azure, como um recurso LUIS, é propriedade da subscrição que contém o recurso.

Para alterar a propriedade de um recurso, pode tomar uma destas ações:
* Transfira a [propriedade](../../cost-management-billing/manage/billing-subscription-transfer.md) da sua subscrição.
* Exporte a app LUIS como um ficheiro e, em seguida, importe a app numa subscrição diferente. A exportação está disponível na página **my apps** no portal LUIS.


## <a name="resource-limits"></a>Limites de recursos

### <a name="authoring-key-creation-limits"></a>Autoria de limites-chave de criação

Pode criar até 10 chaves de autoria por região, por subscrição.

Para mais informações, consulte [os limites-chave](luis-limits.md#key-limits) e [as regiões de Azure.](luis-reference-regions.md)

As regiões editoriais são diferentes das regiões de autoria. Certifique-se de criar uma aplicação na região de autoria que corresponda à região editorial onde pretende que a sua aplicação de cliente seja localizada.

### <a name="errors-for-key-usage-limits"></a>Erros para os limites de utilização chave

Os limites de utilização baseiam-se no nível de preços.

Se exceder a quota de transações por segundo (TPS), receberá um erro 429 de HTTP. Se exceder a sua quota de transação por mês (TPM), recebe um erro HTTP 403.


### <a name="reset-an-authoring-key"></a>Redefinir uma chave de autoria

Para aplicações de recursos de [autoria migradas:](luis-migration-authoring.md) Se a sua chave de autoria estiver comprometida, reinicie a chave no portal Azure, na página **Chaves** para o recurso de autoria.

Para aplicações que não tenham sido migradas: A chave é reposta em todas as suas aplicações no portal LUIS. Se autorize as suas aplicações através das APIs de autoria, tem de alterar o valor da `Ocp-Apim-Subscription-Key` nova chave.

### <a name="regenerate-an-azure-key"></a>Regenerar uma chave azul

Pode regenerar uma chave Azure a partir da página **Chaves** no portal Azure.


<a name="securing-the-endpoint"></a>

## <a name="app-ownership-access-and-security"></a>Propriedade, acesso e segurança de aplicativos

Uma aplicação é definida pelos seus recursos Azure, que são determinados pela subscrição do proprietário.

Pode mover a sua aplicação LUIS. Utilize os seguintes recursos para o ajudar a fazê-lo utilizando o portal Azure ou O Azure CLI:

* [Mover uma app entre recursos de autoria LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Mover um recurso para um novo grupo de recursos ou subscrição](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Mover um recurso dentro da mesma subscrição ou através de subscrições](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


### <a name="contributions-from-other-authors"></a>Contribuições de outros autores

Para aplicações [de recursos de autoria migradas:](luis-migration-authoring.md) Pode gerir _os contribuintes_ para um recurso de autoria no portal Azure utilizando a página **access control (IAM).** Saiba [como adicionar um utilizador](luis-how-to-collaborate.md) utilizando o endereço de e-mail do colaborador e o papel do contribuinte.

Para apps que ainda não migraram: Pode gerir todos os _colaboradores_ na página **Manage -> Colaboradores** no portal LUIS.

### <a name="query-prediction-access-for-private-and-public-apps"></a>Acesso de previsão de consulta para aplicações privadas e públicas

Para aplicações privadas, o acesso de previsão de consulta está disponível para proprietários e contribuintes. Para aplicações públicas, o acesso ao tempo de execução está disponível para utilizadores que tenham o seu próprio recurso de runtime Azure [Cognitive Service](../cognitive-services-apis-create-account.md) ou [LUIS](#create-resources-in-the-azure-portal) e o ID da aplicação pública.

Não existe atualmente um catálogo de aplicações públicas.

### <a name="authoring-permissions-and-access"></a>Permissões de autoria e acesso
O acesso a uma aplicação a partir do portal [LUIS](luis-reference-regions.md#luis-website) ou das [APIs de autoria](https://go.microsoft.com/fwlink/?linkid=2092087) é controlado pelo recurso de autoria Azure.

O proprietário e todos os colaboradores têm acesso ao autor da app.

|O acesso à autoria inclui:|Notas|
|--|--|
|Adicione ou remova as teclas do ponto final||
|Versão de exportação||
|Registos de pontos finais de exportação||
|Versão de importação||
|Tornar a aplicação pública|Quando uma aplicação é pública, qualquer pessoa que tenha uma chave de autoria ou ponto final pode consultar a app.|
|Alterar modelo|
|Publicar|
|Rever declarações de ponto final para [aprendizagem ativa](luis-how-to-review-endpoint-utterances.md)|
|Preparar|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Previsão de acesso ao tempo de execução do ponto final

O acesso para consulta do ponto final de previsão é controlado por uma definição na página **'Informações de aplicação'** na secção **'Gerir'.**

|[Ponto final privado](#runtime-security-for-private-apps)|[Ponto final público](#runtime-security-for-public-apps)|
|:--|:--|
|Disponível para proprietário e contribuintes|Disponível para proprietário, colaboradores e qualquer outra pessoa que conheça o ID da aplicação|

Pode controlar quem vê a sua chave de tempo de execução LUIS chamando-a num ambiente servidor-servidor. Se estiver a usar o LUIS de um bot, a ligação entre o bot e o LUIS já é mais segura. Se estiver a ligar diretamente para o ponto final LUIS, deverá criar uma API do lado do servidor (como uma [função](https://azure.microsoft.com/services/functions/)Azure) com acesso controlado (através de algo como [Azure AD).](https://azure.microsoft.com/services/active-directory/) Quando a API do lado do servidor for chamada e autenticada e a autorização for verificada, passe a chamada para o LUIS. Esta estratégia não impede ataques man-in-the-middle. Mas obstacule o URL da chave e ponto final dos seus utilizadores, permite-lhe rastrear o acesso e permitir-lhe adicionar registos de resposta de ponto final (como [o Application Insights).](https://azure.microsoft.com/services/application-insights/)

### <a name="runtime-security-for-private-apps"></a>Segurança de tempo de execução para aplicativos privados

O tempo de funcionação de uma aplicação privada está disponível apenas para as seguintes teclas:

|Chave e utilizador|Explicação|
|--|--|
|Chave de autoria do proprietário| Até 1.000 pontos finais|
|Chaves de autoria colaborador/colaboradora| Até 1.000 pontos finais|
|Qualquer chave atribuída à LUIS por um autor ou colaborador/colaborador|Baseado no nível de utilização chave|

### <a name="runtime-security-for-public-apps"></a>Segurança de tempo de execução para aplicações públicas

Quando a sua aplicação é configurada como pública, _qualquer_ chave de autoria válida luis ou chave de ponta final LUIS pode questioná-la, desde que a chave não tenha usado toda a quota de ponto final.

Um utilizador que não seja proprietário ou colaborador só pode aceder ao tempo de funcionação de uma aplicação pública se for dado o ID da aplicação. A LUIS não tem um mercado público ou qualquer outra forma de os utilizadores procurarem uma aplicação pública.

Uma aplicação pública é publicada em todas as regiões. Assim, um utilizador com uma chave de recursos LUIS baseada na região pode aceder à aplicação em qualquer região que esteja associada à chave de recursos.


### <a name="control-access-to-your-query-prediction-endpoint"></a>Controle o acesso ao ponto final de previsão de consulta

Pode controlar quem pode ver a tecla de ponto final de previsão LUIS, chamando-a num ambiente servidor-servidor. Se estiver a usar o LUIS de um bot, a ligação entre o bot e o LUIS já é mais segura. Se estiver a ligar diretamente para o ponto final LUIS, deverá criar uma API do lado do servidor (como uma [função](https://azure.microsoft.com/services/functions/)Azure) com acesso controlado (através de algo como [Azure AD).](https://azure.microsoft.com/services/active-directory/) Quando a API do lado do servidor for chamada e a autenticação e a autorização forem verificadas, passe a chamada para o LUIS. Esta estratégia não impede ataques man-in-the-middle. Mas obstacula o seu ponto final dos seus utilizadores, permite-lhe rastrear o acesso e permitir-lhe adicionar registo de resposta de ponto final (como [o Application Insights).](https://azure.microsoft.com/services/application-insights/)

<a name="starter-key"></a>

## <a name="sign-in-to-the-luis-portal-and-begin-authoring"></a>Inscreva-se no portal LUIS e comece a autorizar

1. Inscreva-se no [portal LUIS](https://www.luis.ai) e concorde com os termos de utilização.
1. Comece a ser a autoria da sua aplicação LUIS escolhendo a sua chave de autoria Azure LUIS:

   ![Screenshot que mostra o ecrã de boas-vindas.](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Quando terminar o processo de seleção de recursos, [crie uma nova aplicação.](luis-how-to-start-new-app.md#create-new-app-in-luis)


<a name="create-azure-resources"></a>
<a name="create-resources-in-the-azure-portal"></a>

[!INCLUDE [Create LUIS resource in Azure portal](includes/create-luis-resource.md)]

### <a name="create-resources-in-the-azure-cli"></a>Criar recursos no CLI Azure

Utilize o [CLI Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) para criar cada recurso individualmente.

`kind`Recurso:

* Autoria: `LUIS.Authoring`
* Previsão: `LUIS`

1. Inscreva-se no Azure CLI:

    ```azurecli
    az login
    ```

    Este comando abre um browser para que possa selecionar a conta correta e fornecer autenticação.

1. Crie um recurso de autoria `LUIS.Authoring` LUIS, denominado `my-luis-authoring-resource` . Crie-o no grupo de recursos _existente_ nomeado `my-resource-group` para a `westus` região.

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Crie um recurso de ponta de previsão LUIS, `LUIS` denominado `my-luis-prediction-resource` . Crie-o no grupo de recursos _existente_ nomeado `my-resource-group` para a `westus` região. Se quiser uma produção superior à que o nível livre proporciona, mude `F0` para `S0` . [Saiba mais sobre os níveis de preços e a produção.](luis-limits.md#key-limits)

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > Estas chaves não são utilizadas pelo portal LUIS até serem atribuídas na página **De Recursos**  >  **Geridores** no portal LUIS.

<a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>

### <a name="assign-resources-in-the-luis-portal"></a>Atribuir recursos no portal LUIS

Pode atribuir um recurso de autoria para uma única aplicação ou para todas as aplicações no LUIS. O procedimento a seguir atribui todas as aplicações a um único recurso de autoria.

1. Inscreva-se no [portal LUIS.](https://www.luis.ai)
1. No canto superior direito, selecione a sua conta de utilizador e, em seguida, selecione **Definições**.
1. Na página **Definições** do Utilizador, selecione **Adicionar o recurso de autoria** e, em seguida, selecione um recurso de autoria existente. Selecione **Guardar**.

## <a name="assign-a-resource-to-an-app"></a>Atribuir um recurso a uma aplicação

>[!NOTE]
>Se não tiver uma subscrição do Azure, não poderá atribuir ou criar um novo recurso. Terá de criar uma [conta gratuita Azure](https://azure.microsoft.com/en-us/free/) e depois regressar ao LUIS para criar um novo recurso a partir do portal.

Pode utilizar este procedimento para criar um recurso de autoria ou previsão ou atribuir um a uma aplicação: 

1. Inscreva-se no [portal LUIS.](https://www.luis.ai) Selecione uma aplicação da lista **de aplicações My.**
1. Ir para **gerir**  >  **recursos Azure:**

    ![Screenshot que mostra a página Azure Resources.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. No **separador de recursos de previsão** ou de **autoria,** selecione o **recurso de previsão Adicionar** ou Adicione o botão de recurso de **autoria.**
1. Utilize os campos no formulário para encontrar o recurso correto e, em seguida, **selecione Guardar**.
1. Se não tiver um recurso existente, pode criar um selecionando **Criar um novo recurso LUIS?**


### <a name="assign-a-query-prediction-runtime-resource-without-using-the-luis-portal"></a>Atribua um recurso de previsão de consulta sem utilizar o portal LUIS

Para processos automatizados como os oleodutos CI/CD, é melhor automatizar a atribuição de um recurso de execução LUIS a uma aplicação LUIS. Para tal, complete estes passos:

1. Obtenha um token Azure Resource Manager [deste site.](https://resources.azure.com/api/token?plaintext=true) Este símbolo expira, então use-o imediatamente. O pedido devolve um token do Gestor de Recursos Azure.

    ![Screenshot que mostra o site para solicitar um token Azure Resource Manager.](./media/luis-manage-keys/get-arm-token.png)

1. Use o token para solicitar os recursos de execução LUIS através de subscrições. Utilize a [conta Get LUIS Azure API,](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c)à qual a sua conta de utilizador tem acesso.

    Esta API POST requer os seguintes valores:

    |Cabeçalho|Valor|
    |--|--|
    |`Authorization`|O valor `Authorization` `Bearer {token}` de. O valor simbólico deve ser precedido pela palavra `Bearer` e por um espaço.|
    |`Ocp-Apim-Subscription-Key`|A sua chave de autoria.|

    A API devolve uma série de objetos JSON que representam as suas assinaturas LUIS. Os valores devolvidos incluem o ID de subscrição, o grupo de recursos e o nome do recurso, devolvidos como `AccountName` . Encontre o item na matriz que é o recurso LUIS que pretende atribuir à app LUIS.

1. Atribua o token ao recurso LUIS utilizando as [contas Atribuição de Azure a uma API de aplicação.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515)

    Esta API POST requer os seguintes valores:

    |Tipo|Definição|Valor|
    |--|--|--|
    |Cabeçalho|`Authorization`|O valor `Authorization` `Bearer {token}` de. O valor simbólico deve ser precedido pela palavra `Bearer` e por um espaço.|
    |Cabeçalho|`Ocp-Apim-Subscription-Key`|A sua chave de autoria.|
    |Cabeçalho|`Content-type`|`application/json`|
    |Consulta|`appid`|Pelo ID de aplicação do LUIS.
    |Corpo||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"Grupo de Recursos": "grupo de recursos-2",<br>"Nome da conta": "luis-uswest-S0-2"}|

    Quando esta API tiver sucesso, `201 - created status` regressa.

## <a name="unassign-a-resource"></a>Não assinar um recurso

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e, em seguida, selecione uma aplicação da lista **de aplicações My.**
1. Ir para **gerir** recursos  >  **Azure.**
1. No **separador de recursos de previsão** ou de **autoria,** selecione o botão **de recurso Design** para o recurso.

Quando se não dá a assinatura de um recurso, não é apagado do Azure. Só está desvinculado do LUIS.


## <a name="delete-an-account"></a>Eliminar uma conta

Consulte [o armazenamento de dados e a remoção](luis-concept-data-storage.md#accounts) para obter informações sobre os dados que são eliminados quando elimina a sua conta.

## <a name="change-the-pricing-tier"></a>Alterar o nível de preços

1.  No [portal Azure,](https://portal.azure.com)encontre e selecione a sua assinatura LUIS:

    ![Screenshot que mostra uma assinatura LUIS no portal Azure.](./media/luis-usage-tiers/find.png)
1.  Selecione **o nível de preços** para ver os níveis de preços disponíveis:

    ![Screenshot que mostra o item do menu de nível de preços.](./media/luis-usage-tiers/subscription.png)
1.  Selecione o nível de preços e, em seguida, clique **em Selecionar** para guardar o seu alterar:

    ![Screenshot que mostra como selecionar e guardar um nível de preços.](./media/luis-usage-tiers/plans.png)

    Quando a alteração dos preços estiver concluída, uma janela pop-up verifica a atualização do nível de preços:

    ![Screenshot da janela pop-up que verifica a atualização de preços.](./media/luis-usage-tiers/updated.png)
1. Lembre-se de [atribuir esta chave de ponto final](#assign-a-resource-to-an-app) na página **Publicar** e usá-la em todas as consultas de ponto final.

## <a name="view-azure-resource-metrics"></a>Ver métricas de recursos Azure

### <a name="view-a-summary-of-azure-resource-usage"></a>Ver um resumo da utilização do recurso Azure
Pode ver informações de utilização do LUIS no portal Azure. A página **'Visão Geral'** mostra um resumo, incluindo chamadas e erros recentes. Se fizer um pedido de ponto final LUIS, permita até cinco minutos para que a alteração apareça.

![Screenshot que mostra a página de visão geral.](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Personalizar gráficos de utilização de recursos Azure
A página **Métricas** fornece uma visão mais detalhada dos dados:

![Screenshot que mostra a página Métricas.](./media/luis-usage-tiers/metrics-default.png)

Pode configurar os seus gráficos de métricas para um período de tempo específico e tipo métrico:

![Screenshot que mostra um gráfico personalizado.](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Alerta de limiar total de transações
Se quiser saber quando atingir um determinado limiar de transação, por exemplo 10.000 transações, pode criar um alerta:

![Screenshot que mostra a página de regras alerta.](./media/luis-usage-tiers/alert-default.png)

Adicione um alerta métrico para a métrica total das **chamadas** durante um determinado período de tempo. Adicione endereços de e-mail de todas as pessoas que devem receber o alerta. Adicione webhooks para todos os sistemas que devem receber o alerta. Também pode executar uma aplicação lógica quando o alerta é desencadeado.

## <a name="next-steps"></a>Passos seguintes

* Aprenda [a usar versões](luis-how-to-manage-versions.md) para controlar o ciclo de vida da sua aplicação.
* Migrar para o novo [recurso de autoria.](luis-migration-authoring.md)
