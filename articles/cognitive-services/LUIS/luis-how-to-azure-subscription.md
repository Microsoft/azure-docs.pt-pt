---
title: Como utilizar as chaves de autoria e tempo de funcionamento - LUIS
description: Quando utiliza pela primeira vez a Compreensão da Linguagem (LUIS), não precisa de criar uma chave de autor. Quando pretende publicar a aplicação, em seguida, use o seu ponto final de prazo de execução, você precisa criar e atribuir a chave de tempo de execução para a app.
services: cognitive-services
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: d9235b6ef1c7cddbfbbd36f8382439d781af6d5f
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101030"
---
# <a name="create-luis-resources"></a>Criar recursos LUIS

Os recursos de autoria e tempo de execução fornecem autenticação à sua app LUIS e ponto final de previsão.

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>

Quando iniciar sessão no portal LUIS, pode optar por continuar com:

* uma [chave de teste](#trial-key) gratuita - fornecendo autoria e algumas consultas finais de previsão.
* um recurso de autoria Azure [LUIS.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)

<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Inscreva-se no portal LUIS e comece a autoria

1. Inscreva-se no [portal LUIS](https://www.luis.ai) e concorde com os termos de utilização.
1. Inicie a sua aplicação LUIS escolhendo o tipo de chave de autor LUIS que gostaria de usar: chave de teste gratuito, ou nova chave de autor do Azure LUIS.

    ![Escolha um tipo de recurso de autor de compreensão linguística](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Quando terminar o seu processo de seleção de recursos, [crie uma nova app.](luis-how-to-start-new-app.md#create-new-app-in-luis)

## <a name="trial-key"></a>Chave do julgamento

A chave do ensaio (arranque) está prevista para si. É usado como chave de autenticação para consulta do prazo de execução do ponto final da previsão, até 1000 consultas por mês.

É visível tanto na página de **Definições** do Utilizador como nas páginas de **recursos do Manage -> Azure** no portal LUIS.

Quando estiver pronto para publicar o seu ponto final de previsão, [crie](#create-luis-resources) e [atribua](#assign-a-resource-to-an-app) chaves de execução de autoria e previsão, para substituir a funcionalidade da chave de arranque.

<a name="create-resources-in-the-azure-portal"></a>


[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="create-resources-in-azure-cli"></a>Criar recursos no Azure CLI

Utilize o [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para criar cada recurso individualmente.

Recurso: `kind`

* Autoria:`LUIS.Authoring`
* Previsão:`LUIS`

1. Inscreva-se no Azure CLI:

    ```azurecli
    az login
    ```

    Isto abre um navegador para permitir selecionar a conta correta e fornecer autenticação.

1. Criar um **recurso de autoria LUIS**, de espécie `LUIS.Authoring` `my-resource-group` , `westus` nomeado `my-luis-authoring-resource` no grupo de recursos _existente_ nomeado para a região.

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Criar um recurso de ponto `LUIS`final `my-luis-prediction-resource` de **previsão LUIS**, de `westus` espécie , nomeado no grupo de recursos _existente_ nomeado `my-resource-group` para a região. Se quiser uma entrada superior à do `F0` `S0`nível livre, mude para . Saiba mais sobre os níveis de [preços e a entrada.](luis-limits.md#key-limits)

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > Estas chaves **não** são utilizadas pelo portal LUIS até serem atribuídas no portal LUIS sobre os **recursos 'Gerir -> Azure.**

## <a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>Atribuir um recurso de autoria no portal LUIS para todas as aplicações

Pode atribuir um recurso de autoria para uma única aplicação ou para todas as aplicações em LUIS. O procedimento seguinte atribui todas as aplicações a um único recurso de autoria.

1. Inscreva-se no [portal LUIS.](https://www.luis.ai)
1. Na barra de navegação superior, à extrema-direita, selecione a sua conta de utilizador e, em seguida, selecione **Definições**.
1. Na página **Definições** do Utilizador, selecione **Adicionar recurso de autor** e, em seguida, selecione um recurso de autor existente. Selecione **Guardar**.

## <a name="assign-a-resource-to-an-app"></a>Atribuir um recurso a uma aplicação

Pode atribuir um único recurso, autoria ou previsão de prazo final, a uma aplicação com o seguinte procedimento.

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e, em seguida, selecione uma aplicação da lista **de aplicações My.**
1. Navegue para a página de **recursos Do > Azure.**

    ![Selecione os recursos Manage -> Azure no portal LUIS para atribuir um recurso à app.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Selecione o separador de recursos de previsão ou autor e, em seguida, selecione o **recurso de previsão Adicionar** ou adicionar o botão de recurso **autor.**
1. Selecione os campos no formulário para encontrar o recurso correto e, em seguida, **selecione Guardar**.

### <a name="assign-runtime-resource-without-using-luis-portal"></a>Atribuir recurso de tempo de execução sem usar o portal LUIS

Para fins de automação, como um pipeline CI/CD, pode querer automatizar a atribuição de um recurso de tempo de execução LUIS a uma aplicação LUIS. Para isso, é necessário realizar os seguintes passos:

1. Obtenha um token Do Gestor de Recursos Azure deste [site](https://resources.azure.com/api/token?plaintext=true). Este token expira, por isso use-o imediatamente. O pedido devolve um token do Gestor de Recursos Azure.

    ![Solicite ficha do Gestor de Recursos Azure e receba ficha do Gestor de Recursos Azure](./media/luis-manage-keys/get-arm-token.png)

1. Utilize o símbolo para solicitar os recursos de tempo de execução da LUIS através de subscrições, a partir das [contas Get LUIS azure API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), a que a sua conta de utilizador tem acesso.

    Esta API POST requer as seguintes definições:

    |Cabeçalho|Valor|
    |--|--|
    |`Authorization`|O valor `Authorization` `Bearer {token}`de é. Note que o valor simbólico deve `Bearer` ser precedido pela palavra e por um espaço.|
    |`Ocp-Apim-Subscription-Key`|A sua chave de autoria.|

    Este API devolve uma série de objetos JSON das suas subscrições LUIS, incluindo ID de subscrição, grupo de recursos e nome de recursos, devolvidos como nome de conta. Encontre o único item na matriz que é o recurso LUIS para atribuir à app LUIS.

1. Atribuir o símbolo ao recurso LUIS com as contas do API da Atribuição de um API de [aplicação.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515)

    Esta API POST requer as seguintes definições:

    |Tipo|Definição|Valor|
    |--|--|--|
    |Cabeçalho|`Authorization`|O valor `Authorization` `Bearer {token}`de é. Note que o valor simbólico deve `Bearer` ser precedido pela palavra e por um espaço.|
    |Cabeçalho|`Ocp-Apim-Subscription-Key`|A sua chave de autoria.|
    |Cabeçalho|`Content-type`|`application/json`|
    |Corda de consulta|`appid`|Pelo ID de aplicação do LUIS.
    |Corpo||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"Grupo de Recursos": "grupo de recursos-2",<br>"Nome de conta": "luis-uswest-S0-2"}|

    Quando esta API for bem sucedida, devolve um estatuto criado em 201.

## <a name="unassign-resource"></a>Recurso de não atribuir

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e, em seguida, selecione uma aplicação da lista **de aplicações My.**
1. Navegue para a página de **recursos Do > Azure.**
1. Selecione o separador de recursos de Previsão ou Autor e, em seguida, selecione o botão **de recurso Unassign** para o recurso.

Quando não atribua um recurso, não é eliminado do Azure. Só não está ligado ao LUIS.

## <a name="reset-authoring-key"></a>Redefinir a chave de autor

**Para a autoria de aplicações [migradas](luis-migration-authoring.md) **por recursos : se a sua chave de autor estiver comprometida, reponha a chave no portal Azure na página **Keys** para esse recurso de autoria.

**Para aplicações que ainda não migraram**: a chave é redefinida em todas as suas aplicações no portal LUIS. Se possuir as suas aplicações através das APIs de autoria, terá de alterar o valor da Chave de Assinatura Ocp-Apim para a nova chave.

## <a name="regenerate-azure-key"></a>Chave De regeneração de Azure

Regenerar as teclas Azure do portal Azure, na página **Keys.**

## <a name="delete-account"></a>Eliminar conta

Consulte o [armazenamento e remoção de dados](luis-concept-data-storage.md#accounts) para obter informações sobre quais os dados eliminados quando eliminar a sua conta.

## <a name="change-pricing-tier"></a>Alterar escalão de preço

1.  Em [Azure,](https://portal.azure.com)encontre a sua assinatura LUIS. Selecione a subscrição LUIS.
    ![Encontre a sua subscrição LUIS](./media/luis-usage-tiers/find.png)
1.  Selecione **o nível** de preços para ver os níveis de preços disponíveis.
    ![Ver níveis de preços](./media/luis-usage-tiers/subscription.png)
1.  Selecione o nível de preços e selecione **Selecione** para guardar a sua alteração.
    ![Altere o seu nível de pagamento LUIS](./media/luis-usage-tiers/plans.png)
1.  Quando a mudança de preços estiver completa, uma janela pop-up verifica o novo nível de preços.
    ![Verifique o seu nível de pagamento LUIS](./media/luis-usage-tiers/updated.png)
1. Lembre-se de [atribuir esta chave de ponto final](#assign-a-resource-to-an-app) na página **Publicar** e usá-la em todas as consultas de ponto final.

## <a name="viewing-azure-resource-metrics"></a>Visualização das métricas de recursos do Azure

### <a name="viewing-azure-resource-summary-usage"></a>Visualização do uso do resumo do recurso Azure
Pode ver informações de utilização da LUIS em Azure. A página **de visão geral** mostra informações sumárias recentes, incluindo chamadas e erros. Se fizer um pedido de ponto final luis, consulte imediatamente a **página 'Overview',** deixe até cinco minutos para que a utilização apareça.

![Visualização do uso do resumo](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Personalização de gráficos de utilização de recursos Azure
As métricas fornecem uma visão mais detalhada dos dados.

![Métricas padrão](./media/luis-usage-tiers/metrics-default.png)

Pode configurar os seus gráficos de métricas para o período de tempo e o tipo métrico.

![Métricas personalizadas](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Alerta de limiar de transações totais
Se quiser saber quando atingiu um determinado limiar de transação, por exemplo, 10.000 transações, pode criar um alerta.

![Alertas predefinidos](./media/luis-usage-tiers/alert-default.png)

Adicione um alerta métrico para a métrica total das **chamadas** durante um determinado período de tempo. Adicione endereços de e-mail de todas as pessoas que devem receber o alerta. Adicione webhooks para todos os sistemas que devem receber o alerta. Também pode executar uma aplicação lógica quando o alerta é desencadeado.

## <a name="next-steps"></a>Passos seguintes

* Aprenda [a usar versões](luis-how-to-manage-versions.md) para controlar o seu ciclo de vida da aplicação.
* Compreenda os conceitos, incluindo o [recurso de autoria](luis-concept-keys.md#authoring-key) e os [contribuintes](luis-concept-keys.md#contributions-from-other-authors) desse recurso.
* Aprenda [a criar](luis-how-to-azure-subscription.md) recursos de autoria e tempo de funcionamento
* Migrar para o novo [recurso de autoria](luis-migration-authoring.md)
