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
ms.date: 09/09/2019
ms.author: diberry
ms.openlocfilehash: 1fb57a7c6cc694c56667d589eae39442ee9e82ac
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984383"
---
# <a name="using-authoring-and-runtime-resource-keys"></a>Usando chaves de recurso de criação e tempo de execução

Os recursos de criação e tempo de execução fornecem autenticação para seu aplicativo LUIS e ponto de extremidade de previsão.

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>

Ao entrar no portal do LUIS, você pode optar por continuar com:

* uma [chave de avaliação](#trial-key) gratuita-fornecendo criação e algumas consultas de ponto de extremidade de previsão.
* um novo recurso de criação do Azure LUIS – crie um novo recurso. Isso não é o mesmo que um recurso de ponto de extremidade de previsão. 


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

1. Inicie sessão no [portal do Azure](https://azure.microsoft.com/free/). 
1. Selecione **+ Criar um recurso**.
1. Na caixa de pesquisa, introduza `Language understanding`.
1. Selecione **Criar** para iniciar o processo de criação. 
1. Selecione **ambos** para criar uma chave de tempo de execução de criação de ponto de extremidade de previsão. 
1. Insira as informações necessárias para criar o recurso e, em seguida, selecione **criar** para concluir o processo.

    ![Criar o recurso de reconhecimento de idioma](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

    |Name|Objetivo|
    |--|--|
    |Nome do recurso| Um nome personalizado que você escolhe, usado como parte da URL para suas consultas de ponto de extremidade de criação e previsão.|
    |Nome da subscrição| a assinatura que será cobrada para o recurso.|
    |Resource group| Um nome de grupo de recursos personalizado que você escolhe ou cria. Os grupos de recursos permitem que você agrupe recursos do Azure para acesso e gerenciamento na mesma região.|
    |Local de criação|A região associada ao seu modelo.|
    |Tipo de preço de criação|O tipo de preço determina a transação máxima por segundo e mês.|
    |Local do tempo de execução|A região associada ao tempo de execução do ponto de extremidade de previsão publicado.|
    |Tipo de preço de tempo de execução|O tipo de preço determina a transação máxima por segundo e mês.|

    Depois que ambos os recursos forem criados, atribua os recursos no portal do LUIS.

## <a name="create-resources-in-azure-cli"></a>Criar recursos no CLI do Azure

Use o [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para criar cada recurso individualmente. 

Recurso `kind`:

* Criação`LUIS.Authoring`
* Previsão`LUIS` 

1. Entre no CLI do Azure:

    ```console
    az login
    ```

    Isso abre um navegador para permitir que você selecione a conta correta e forneça a autenticação.

1. Crie um **recurso de criação de Luis**, de `LUIS.Authoring`tipo, `my-luis-authoring-resource` nomeado no grupo de recursos existente `my-resource-group` chamado para `westus` a região. 

    ```console
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Crie um **recurso de ponto de extremidade de previsão Luis** `LUIS`, de `my-luis-prediction-resource` tipo, nomeado no grupo de `my-resource-group` recursos existente `westus` denominado para a região. Se você quiser uma taxa de transferência mais alta do que a `F0` camada `S0`gratuita, altere para. Saiba mais sobre os [tipos de preço e a taxa de transferência](luis-boundaries.md#key-limits).

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

1. Obter um Gestor de recursos do Azure token nesta [Web site](https://resources.azure.com/api/token?plaintext=true). Este token expirar então, utilizá-lo imediatamente. O pedido devolve um token do Azure Resource Manager.

    ![Solicitar token Azure Resource Manager e receber token de Azure Resource Manager](./media/luis-manage-keys/get-arm-token.png)

1. Use o token para solicitar os recursos de tempo de execução LUIS entre assinaturas, da [API Get Luis Azure accounts](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), à qual sua conta de usuário tem acesso. 

    Esta API de publicação requer as seguintes definições:

    |Cabeçalho|Valor|
    |--|--|
    |`Authorization`|O valor de `Authorization` é `Bearer {token}`. Tenha em atenção que o valor do token têm de ser precedido pela palavra `Bearer` e um espaço.| 
    |`Ocp-Apim-Subscription-Key`|Sua chave de criação.|

    Esta API devolve uma matriz de objetos JSON das suas subscrições de LUIS, incluindo o ID de subscrição, o grupo de recursos e o nome de recurso, retornado como o nome da conta. Encontre um item na matriz que é o recurso de LUIS para atribuir à aplicação LUIS. 

1. Atribuir o token para o recurso de LUIS com o [atribuir um LUIS contas do azure a uma aplicação](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API. 

    Esta API de publicação requer as seguintes definições:

    |Tipo|Definição|Valor|
    |--|--|--|
    |Cabeçalho|`Authorization`|O valor de `Authorization` é `Bearer {token}`. Tenha em atenção que o valor do token têm de ser precedido pela palavra `Bearer` e um espaço.|
    |Cabeçalho|`Ocp-Apim-Subscription-Key`|Sua chave de criação.|
    |Cabeçalho|`Content-type`|`application/json`|
    |Cadeia de consulta|`appid`|Pelo ID de aplicação do LUIS. 
    |Corpo||{"AzureSubscriptionId": "ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup 2",<br>"AccountName": "luis-uswest-S0-2"}|

    Quando esta API for bem sucedida, ele retorna um status de criado 201. 

## <a name="unassign-resource"></a>Anular a atribuição de recursos

1. Entre no portal do [Luis](https://www.luis.ai)e selecione um aplicativo na lista **meus aplicativos** .
1. Navegue até a página **gerenciar > recursos do Azure** .
1. Selecione a guia recurso de previsão ou criação e, em seguida, selecione o botão **desatribuir recurso** para o recurso. 

Quando você cancelar a atribuição de um recurso, ele não será excluído do Azure. Só é desassociado do LUIS. 

## <a name="reset-authoring-key"></a>Repor a chave de criação

**Para a criação de aplicativos [migrados](luis-migration-authoring.md)** : se a sua chave de criação estiver comprometida, redefina a chave no portal do Azure na página **chaves** desse recurso de criação. 

**Para aplicativos que ainda não foram migrados**: a chave é redefinida em todos os seus aplicativos no portal do Luis. Se você criar seus aplicativos por meio de APIs de criação, precisará alterar o valor de OCP-APIM-Subscription-Key para a nova chave.

## <a name="regenerate-azure-key"></a>Regenerar chave do Azure

Gere novamente as chaves do Azure no portal do Azure, na página **chaves** .

## <a name="delete-account"></a>Eliminar conta

Ver [armazenamento de dados e remoção](luis-concept-data-storage.md#accounts) para obter informações sobre quais dados são eliminados quando eliminar a sua conta.

## <a name="change-pricing-tier"></a>Alterar escalão de preço

1.  Na [Azure](https://portal.azure.com), localize a sua subscrição do LUIS. Selecione a assinatura LUIS.
    ![Localize a sua subscrição do LUIS](./media/luis-usage-tiers/find.png)
1.  Selecione **tipo de preço** para ver os tipos de preço disponíveis. 
    ![Vista de escalões de preço](./media/luis-usage-tiers/subscription.png)
1.  Selecione o tipo de preço e selecione **selecionar** para salvar a alteração. 
    ![Alterar o escalão de pagamento do LUIS](./media/luis-usage-tiers/plans.png)
1.  Quando a alteração de preços estiver concluída, uma janela de pop-up verifica se o novo escalão de preços. 
    ![Verifique se o seu escalão de pagamento do LUIS](./media/luis-usage-tiers/updated.png)
1. Não se esqueça de [atribuir esta chave de ponto final](#assign-a-resource-to-an-app) sobre o **publicar** página e utilizá-lo em todas as consultas de ponto final. 

## <a name="viewing-azure-resource-metrics"></a>Exibindo métricas de recursos do Azure

### <a name="viewing-azure-resource-summary-usage"></a>Exibindo o uso do Resumo de recursos do Azure
Pode ver informações de utilização do LUIS no Azure. O **descrição geral** página mostra informações de resumo recentes, incluindo chamadas e erros. Se fizer um LUIS pedido de ponto final, em seguida, imediatamente Assista a **página de descrição geral**, aguarde cinco minutos para a utilização a aparecer.

![Ver utilização de resumo](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Personalizando gráficos de uso de recursos do Azure
Metrics fornece uma visão mais detalhada sobre os dados.

![Métricas predefinidas](./media/luis-usage-tiers/metrics-default.png)

Pode configurar seus gráficos de métricas para o período de tempo e o tipo de métrica. 

![Métricas personalizadas](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Alerta do limiar de total de transações
Se quiser saber quando atingiu um certo limite da transação, por exemplo 10 000 transações, pode criar um alerta. 

![Alertas de predefinição](./media/luis-usage-tiers/alert-default.png)

Adicionar um alerta de métrica para o **total de chamadas** métrica durante um determinado período de tempo. Adicione endereços de e-mail de todas as pessoas que devem receber o alerta. Adicione webhooks para todos os sistemas que devem receber o alerta. Também pode executar uma aplicação lógica quando o alerta é acionado. 

## <a name="next-steps"></a>Passos Seguintes

* Saiba [como usar versões](luis-how-to-manage-versions.md) para controlar o ciclo de vida do aplicativo.
* Entenda os conceitos, incluindo o [recurso de criação](luis-concept-keys.md#authoring-key) e os [colaboradores](luis-concept-keys.md#contributions-from-other-authors) nesse recurso.
* Saiba [como criar](luis-how-to-azure-subscription.md) recursos de criação e tempo de execução
* Migrar para o novo [recurso de criação](luis-migration-authoring.md) 
