---
title: Como usar teclas de autoria e tempo de execução - LUIS
description: Quando utiliza pela primeira vez a Compreensão linguística (LUIS), não precisa de criar uma chave de autoria. Quando pretende publicar a aplicação e, em seguida, utilizar o seu ponto final de tempo de execução, tem de criar e atribuir a chave de tempo de execução à aplicação.
services: cognitive-services
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: c566e8fe56d19856f5a577e472929b7610497d7c
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344463"
---
# <a name="create-luis-resources"></a>Criar recursos LUIS

A autoria e os recursos de execução fornecem autenticação à sua app LUIS e ponto final de previsão.

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>

Ao entrar no portal LUIS, pode optar por continuar com:

* uma [chave de teste](#trial-key) livre - fornecendo a autoria e algumas consultas de ponto final de previsão.
* um recurso [de autoria Azure LUIS.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)

<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Inscreva-se no portal LUIS e comece a autorizar

1. Inscreva-se no [portal LUIS](https://www.luis.ai) e concorde com os termos de utilização.
1. Inicie a sua aplicação LUIS escolhendo que tipo de chave de autoria LUIS gostaria de usar: chave de teste gratuito ou nova chave de autoria Azure LUIS.

    ![Escolha um tipo de recurso de autoria de compreensão linguística](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Quando terminar o processo de seleção de recursos, [crie uma nova aplicação.](luis-how-to-start-new-app.md#create-new-app-in-luis)

## <a name="trial-key"></a>Chave do julgamento

A chave de teste (arranque) está prevista para si. É usado como a sua chave de autenticação para consultar o tempo de execução do ponto final de previsão, até 1000 consultas por mês.

É visível tanto na página **De Definições** do Utilizador como nas páginas **de recursos do Manage -> Azure** no portal LUIS.

Quando estiver pronto para publicar o seu ponto final de previsão, [crie](#create-luis-resources) e [atribua](#assign-a-resource-to-an-app) teclas de tempo de execução de autoria e previsão, para substituir a funcionalidade de chave de arranque.

<a name="create-resources-in-the-azure-portal"></a>


[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="create-resources-in-azure-cli"></a>Criar recursos em Azure CLI

Utilize o [CLI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para criar cada recurso individualmente.

`kind`Recurso:

* Autoria:`LUIS.Authoring`
* Previsão:`LUIS`

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

## <a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>Atribuir um recurso de autoria no portal LUIS para todas as aplicações

Pode atribuir um recurso de autoria para uma única aplicação ou para todas as aplicações no LUIS. O procedimento a seguir atribui todas as aplicações a um único recurso de autoria.

1. Inscreva-se no [portal LUIS.](https://www.luis.ai)
1. Na barra de navegação superior, para a extrema direita, selecione a sua conta de utilizador e, em seguida, selecione **Definições**.
1. Na página **Definições** do Utilizador, selecione **Adicionar o recurso de autoria** e, em seguida, selecione um recurso de autoria existente. Selecione **Guardar**.

## <a name="assign-a-resource-to-an-app"></a>Atribuir um recurso a uma aplicação

Pode atribuir um único recurso, autoria ou previsão de tempo de funcionamento, a uma aplicação com o seguinte procedimento.

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e, em seguida, selecione uma aplicação da lista **de aplicações My.**
1. Navegue para a página **de recursos Manage-> Azure.**

    ![Selecione os recursos Manage-> Azure no portal LUIS para atribuir um recurso à app.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Selecione o separador de recursos de Previsão ou autoria e, em seguida, selecione o **recurso de previsão Adicionar** ou Adicione o botão de recurso de **autoria.**
1. Selecione os campos no formulário para encontrar o recurso correto e, em seguida, **selecione Guardar**.

### <a name="assign-runtime-resource-without-using-luis-portal"></a>Atribuir recurso de tempo de execução sem usar o portal LUIS

Para fins de automatização, como um pipeline CI/CD, pode querer automatizar a atribuição de um recurso de runtime LUIS a uma aplicação LUIS. Para isso, é necessário executar os seguintes passos:

1. Obtenha um token Azure Resource Manager deste [site.](https://resources.azure.com/api/token?plaintext=true) Este token expira assim usá-lo imediatamente. O pedido devolve um token do Gestor de Recursos Azure.

    ![Solicite ao Azure Resource Manager token e receba o token do Gestor de Recursos Azure](./media/luis-manage-keys/get-arm-token.png)

1. Utilize o token para solicitar os recursos de execução LUIS através de subscrições, a partir das [contas API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c)get LUIS, a que a sua conta de utilizador tem acesso.

    Esta API POST requer as seguintes definições:

    |Cabeçalho|Valor|
    |--|--|
    |`Authorization`|O valor `Authorization` `Bearer {token}` de. Note que o valor simbólico deve ser precedido pela palavra `Bearer` e por um espaço.|
    |`Ocp-Apim-Subscription-Key`|A sua chave de autoria.|

    Esta API devolve uma série de objetos JSON das suas assinaturas LUIS, incluindo ID de subscrição, grupo de recursos e nome de recurso, devolvidos como nome de conta. Encontre o único item na matriz que é o recurso LUIS para atribuir à app LUIS.

1. Atribua o token ao recurso LUIS com as [contas Desatribuição de Azure a uma API de aplicação.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515)

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

## <a name="reset-authoring-key"></a>Chave de autoria de reset

**Para [a autoria de](luis-migration-authoring.md) aplicações migradas de recursos**: se a sua chave de autoria estiver comprometida, reinicie a chave no portal Azure na página **Keys** para esse recurso de autoria.

**Para aplicações que ainda não migraram:** a chave é reposta em todas as suas aplicações no portal LUIS. Se autorize as suas aplicações através das APIs de autoria, tem de alterar o valor da Chave de Subscrição Ocp-Apim para a nova chave.

## <a name="regenerate-azure-key"></a>Chave Azure regenerada

Regenerar as chaves Azure do portal Azure, na página **Keys.**

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

## <a name="next-steps"></a>Próximos passos

* Aprenda [a usar versões](luis-how-to-manage-versions.md) para controlar o ciclo de vida da sua aplicação.
* Compreenda os conceitos, incluindo o [recurso de autoria](luis-concept-keys.md#authoring-key) e [os contribuintes](luis-concept-keys.md#contributions-from-other-authors) nesse recurso.
* Saiba [como criar](luis-how-to-azure-subscription.md) recursos de autoria e tempo de execução
* Migrar para o novo [recurso de autoria](luis-migration-authoring.md)
