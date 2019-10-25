---
title: Como usar as chaves de criação e tempo de execução-LUIS
titleSuffix: Azure Cognitive Services
description: Ao usar o Reconhecimento vocal (LUIS) pela primeira vez, você não precisa criar uma chave de criação. Quando você pretende publicar o aplicativo, use o ponto de extremidade do tempo de execução, você precisa criar e atribuir a chave de tempo de execução ao aplicativo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: acda549ffc03679de43b4e5956e65ccada766c15
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819964"
---
# <a name="using-authoring-and-runtime-resource-keys"></a>Usando chaves de recurso de criação e tempo de execução

Os recursos de criação e tempo de execução fornecem autenticação para seu aplicativo LUIS e ponto de extremidade de previsão.

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>

Ao entrar no portal do LUIS, você pode optar por continuar com:

* uma [chave de avaliação](#trial-key) gratuita-fornecendo criação e algumas consultas de ponto de extremidade de previsão.
* um recurso de [criação de Luis](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) do Azure. 


<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Entre no portal do LUIS e comece a criar

1. Entre no [portal do Luis](https://www.luis.ai) e concorde com os termos de uso.
1. Inicie seu aplicativo LUIS escolhendo o tipo de chave de criação do LUIS que você gostaria de usar: chave de avaliação gratuita ou nova chave de criação do Azure LUIS. 

    ![Escolha um tipo de Reconhecimento vocal recurso de criação](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Quando você terminar com o processo de seleção de recursos, [crie um novo aplicativo](luis-how-to-start-new-app.md#create-new-app-in-luis). 

## <a name="trial-key"></a>Chave de avaliação

A chave de avaliação (inicial) é fornecida para você. Ele é usado como sua chave de autenticação para consultar o tempo de execução de ponto de extremidade de previsão, até 1000 consultas por mês. 

Ele é visível na página de **configurações do usuário** e nas páginas de **recursos do Azure manage->** no portal do Luis. 

Quando você estiver pronto para publicar seu ponto de extremidade de previsão, crie e atribua chaves de criação e de tempo de execução de previsão para substituir a funcionalidade de chave inicial. 

## <a name="create-resources-in-the-azure-portal"></a>Criar recursos no portal do Azure

1. Use [este link](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) para abrir o portal do Azure em para a criação de recursos.
1. Selecione **ambos** para criar uma chave de tempo de execução de criação de ponto de extremidade de previsão. 
1. Insira as informações necessárias para criar o recurso e, em seguida, selecione **criar** para concluir o processo.

    ![Criar o recurso de reconhecimento de idioma](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

    |Nome|Finalidade|
    |--|--|
    |Nome do recurso| Um nome personalizado que você escolhe, usado como parte da URL para suas consultas de ponto de extremidade de criação e previsão.|
    |Nome da assinatura| a assinatura que será cobrada para o recurso.|
    |Grupo de recursos| Um nome de grupo de recursos personalizado que você escolhe ou cria. Os grupos de recursos permitem que você agrupe recursos do Azure para acesso e gerenciamento na mesma região.|
    |Local de criação|A região associada ao seu modelo.|
    |Tipo de preço de criação|O tipo de preço determina a transação máxima por segundo e mês.|
    |Local do tempo de execução|A região associada ao tempo de execução do ponto de extremidade de previsão publicado.|
    |Tipo de preço de tempo de execução|O tipo de preço determina a transação máxima por segundo e mês.|

    Depois que ambos os recursos forem criados, atribua os recursos no portal do LUIS.

## <a name="create-resources-in-azure-cli"></a>Criar recursos no CLI do Azure

Use o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para criar cada recurso individualmente. 

`kind`de recursos:

* Criação: `LUIS.Authoring`
* Previsão: `LUIS` 

1. Entre no CLI do Azure:

    ```console
    az login
    ```

    Isso abre um navegador para permitir que você selecione a conta correta e forneça a autenticação.

1. Crie um **recurso de criação de Luis**, do tipo `LUIS.Authoring`, chamado `my-luis-authoring-resource` no grupo de recursos _existente_ chamado `my-resource-group` para a região de `westus`. 

    ```console
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Crie um **recurso de ponto de extremidade de previsão Luis**, do tipo `LUIS`, chamado `my-luis-prediction-resource` no grupo de recursos _existente_ chamado `my-resource-group` para a região de `westus`. Se você quiser uma taxa de transferência mais alta do que a camada gratuita, altere `F0` para `S0`. Saiba mais sobre os [tipos de preço e a taxa de transferência](luis-boundaries.md#key-limits).

    ```console
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note] 
    > Essas chaves **não** são usadas pelo portal do Luis até que sejam atribuídas no portal do Luis nos **recursos do Azure gerenciar->** .

## <a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>Atribuir um recurso de criação no portal do LUIS para todos os aplicativos

Você pode atribuir um recurso de criação para um único aplicativo ou para todos os aplicativos no LUIS. O procedimento a seguir atribui todos os aplicativos a um único recurso de criação.

1. Entre no portal do [Luis](https://www.luis.ai).
1. Na barra de navegação superior, à extrema direita, selecione sua conta de usuário e, em seguida, selecione **configurações**.
1. Na página **configurações do usuário** , selecione **Adicionar recurso de criação** e, em seguida, selecione um recurso de criação existente. Selecione **Guardar**. 

## <a name="assign-a-resource-to-an-app"></a>Atribuir um recurso a um aplicativo

Você pode atribuir um único recurso, criação ou tempo de execução de ponto de extremidade de previsão a um aplicativo com o procedimento a seguir.

1. Entre no portal do [Luis](https://www.luis.ai)e selecione um aplicativo na lista **meus aplicativos** .
1. Navegue até a página **gerenciar > recursos do Azure** .

    ![Selecione Gerenciar-> recursos do Azure no portal do LUIS para atribuir um recurso ao aplicativo.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Selecione a guia Previsão ou recurso de criação e, em seguida, selecione o botão **Adicionar recurso de previsão** ou **Adicionar recurso de criação** . 
1. Selecione os campos no formulário para localizar o recurso correto e, em seguida, selecione **salvar**.  

### <a name="assign-runtime-resource-without-using-luis-portal"></a>Atribuir recurso de tempo de execução sem usar o portal do LUIS

Para fins de automação, como um pipeline de CI/CD, talvez você queira automatizar a atribuição de um recurso de tempo de execução LUIS para um aplicativo LUIS. Para fazer isso, você precisa executar as seguintes etapas:

1. Obtenha um Azure Resource Manager token deste [site](https://resources.azure.com/api/token?plaintext=true). Esse token expira, portanto, use-o imediatamente. A solicitação retorna um token Azure Resource Manager.

    ![Solicitar token Azure Resource Manager e receber token de Azure Resource Manager](./media/luis-manage-keys/get-arm-token.png)

1. Use o token para solicitar os recursos de tempo de execução LUIS entre assinaturas, da [API Get Luis Azure accounts](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), à qual sua conta de usuário tem acesso. 

    Essa API POST requer as seguintes configurações:

    |Cabeçalho|Valor|
    |--|--|
    |`Authorization`|O valor de `Authorization` é `Bearer {token}`. Observe que o valor do token deve ser precedido pela palavra `Bearer` e por um espaço.| 
    |`Ocp-Apim-Subscription-Key`|Sua chave de criação.|

    Essa API retorna uma matriz de objetos JSON de suas assinaturas do LUIS, incluindo a ID da assinatura, o grupo de recursos e o nome do recurso, retornados como nome da conta. Localize um item na matriz que é o recurso LUIS a ser atribuído ao aplicativo LUIS. 

1. Atribua o token ao recurso LUIS com [atribuir uma Luis contas do Azure a uma](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API de aplicativo. 

    Essa API POST requer as seguintes configurações:

    |Tipo|Definição|Valor|
    |--|--|--|
    |Cabeçalho|`Authorization`|O valor de `Authorization` é `Bearer {token}`. Observe que o valor do token deve ser precedido pela palavra `Bearer` e por um espaço.|
    |Cabeçalho|`Ocp-Apim-Subscription-Key`|Sua chave de criação.|
    |Cabeçalho|`Content-type`|`application/json`|
    |QueryString|`appid`|Pelo ID de aplicação do LUIS. 
    |Corpo||{"AzureSubscriptionId": "ddda2925-af7f-4b05-9BA1-2155c5fe8a8e",<br>"Resourcegroup": "resourcegroup-2",<br>"AccountName": "Luis-uswest-S0-2"}|

    Quando essa API é bem-sucedida, ela retorna um status de 201 criado. 

## <a name="unassign-resource"></a>Cancelar atribuição de recurso

1. Entre no portal do [Luis](https://www.luis.ai)e selecione um aplicativo na lista **meus aplicativos** .
1. Navegue até a página **gerenciar > recursos do Azure** .
1. Selecione a guia recurso de previsão ou criação e, em seguida, selecione o botão **desatribuir recurso** para o recurso. 

Quando você cancelar a atribuição de um recurso, ele não será excluído do Azure. Ele só é desvinculado de LUIS. 

## <a name="reset-authoring-key"></a>Redefinir chave de criação

**Para a criação de aplicativos [migrados](luis-migration-authoring.md)** : se a sua chave de criação estiver comprometida, redefina a chave no portal do Azure na página **chaves** desse recurso de criação. 

**Para aplicativos que ainda não foram migrados**: a chave é redefinida em todos os seus aplicativos no portal do Luis. Se você criar seus aplicativos por meio de APIs de criação, precisará alterar o valor de OCP-APIM-Subscription-Key para a nova chave.

## <a name="regenerate-azure-key"></a>Regenerar chave do Azure

Gere novamente as chaves do Azure no portal do Azure, na página **chaves** .

## <a name="delete-account"></a>Excluir conta

Consulte [armazenamento e remoção de dados](luis-concept-data-storage.md#accounts) para obter informações sobre quais dados são excluídos quando você exclui sua conta.

## <a name="change-pricing-tier"></a>Alterar escalão de preço

1.  No [Azure](https://portal.azure.com), encontre sua assinatura do Luis. Selecione a assinatura LUIS.
    ![encontrar sua assinatura do LUIS](./media/luis-usage-tiers/find.png)
1.  Selecione **tipo de preço** para ver os tipos de preço disponíveis. 
    ![exibir tipos de preço](./media/luis-usage-tiers/subscription.png)
1.  Selecione o tipo de preço e selecione **selecionar** para salvar a alteração. 
    ![alterar sua camada de pagamento do LUIS](./media/luis-usage-tiers/plans.png)
1.  Quando a alteração de preços for concluída, uma janela pop-up verificará o novo tipo de preço. 
    ![verificar sua camada de pagamento do LUIS](./media/luis-usage-tiers/updated.png)
1. Lembre-se de [atribuir essa chave de ponto de extremidade](#assign-a-resource-to-an-app) na página de **publicação** e usá-la em todas as consultas de ponto de extremidade. 

## <a name="viewing-azure-resource-metrics"></a>Exibindo métricas de recursos do Azure

### <a name="viewing-azure-resource-summary-usage"></a>Exibindo o uso do Resumo de recursos do Azure
Você pode exibir informações de uso do LUIS no Azure. A página **visão geral** mostra informações de resumo recentes, incluindo chamadas e erros. Se você fizer uma solicitação de ponto de extremidade LUIS, observe imediatamente a **página Visão geral**, aguarde até cinco minutos para que o uso apareça.

![Exibindo o uso de resumo](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Personalizando gráficos de uso de recursos do Azure
As métricas fornecem uma exibição mais detalhada dos dados.

![Métricas padrão](./media/luis-usage-tiers/metrics-default.png)

Você pode configurar seus gráficos de métricas para o período de tempo e o tipo de métrica. 

![Métricas personalizadas](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Alerta de limite de transações totais
Se você quiser saber quando atingiu um determinado limite de transação, por exemplo, 10.000 transações, você pode criar um alerta. 

![Alertas padrão](./media/luis-usage-tiers/alert-default.png)

Adicione um alerta de métrica para a métrica **total de chamadas** para um determinado período de tempo. Adicione endereços de email de todas as pessoas que devem receber o alerta. Adicione WebHooks para todos os sistemas que devem receber o alerta. Você também pode executar um aplicativo lógico quando o alerta é disparado. 

## <a name="next-steps"></a>Passos seguintes

* Saiba [como usar versões](luis-how-to-manage-versions.md) para controlar o ciclo de vida do aplicativo.
* Entenda os conceitos, incluindo o [recurso de criação](luis-concept-keys.md#authoring-key) e os [colaboradores](luis-concept-keys.md#contributions-from-other-authors) nesse recurso.
* Saiba [como criar](luis-how-to-azure-subscription.md) recursos de criação e tempo de execução
* Migrar para o novo [recurso de criação](luis-migration-authoring.md) 
