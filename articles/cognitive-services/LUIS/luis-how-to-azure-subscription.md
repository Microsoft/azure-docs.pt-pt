---
title: Como utilizar as chaves de autoria e tempo de funcionamento - LUIS
titleSuffix: Azure Cognitive Services
description: Quando utiliza pela primeira vez a Compreensão da Linguagem (LUIS), não precisa de criar uma chave de autor. Quando pretende publicar a aplicação, em seguida, use o seu ponto final de prazo de execução, você precisa criar e atribuir a chave de tempo de execução para a app.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: a5140da32eb6fce03131a42bfa90e71e64552431
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78668451"
---
# <a name="using-authoring-and-runtime-resource-keys"></a>Utilização de chaves de recursos de autoria e tempo de execução

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

É visível tanto na página de **Definições** do Utilizador como nas páginas **de recursos Do Manage &> Azure** no portal LUIS. 

Quando estiver pronto para publicar o seu ponto final de previsão, crie e atribua chaves de execução de autoria e previsão, para substituir a funcionalidade da chave de arranque. 

## <a name="create-resources-in-the-azure-portal"></a>Criar recursos no portal Azure

1. Utilize [este link](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) para abrir o portal Azure para a criação de recursos.
1. Selecione **Ambos** para criar uma chave de execução de ponto final de previsão. 
1. Introduza as informações necessárias para criar o recurso e, em seguida, selecione **Criar** para terminar o processo.

    ![Criar o recurso de compreensão da linguagem](./media/luis-how-to-azure-subscription/create-resource-in-azure.png)

    |Nome|Objetivo|
    |--|--|
    |Nome do recurso| Um nome personalizado que escolher, usado como parte do URL para as suas consultas de autor e ponto final de previsão.|
    |Nome da subscrição| a subscrição que será faturada para o recurso.|
    |Grupo de recursos| Um nome de grupo de recursos personalizado que você escolhe ou cria. Os grupos de recursos permitem-lhe agrupar os recursos do Azure para acesso e gestão na mesma região.|
    |Localização de autoria|A região associada ao seu modelo.|
    |Nível de preços de autoria|O nível de preços determina a transação máxima por segundo e mês.|
    |Localização do tempo de execução|A região associada ao seu tempo de execução de previsão publicada.|
    |Nível de preços de tempo de execução|O nível de preços determina a transação máxima por segundo e mês.|

    Uma vez criados os dois recursos, atribua os recursos no portal LUIS.

## <a name="create-resources-in-azure-cli"></a>Criar recursos no Azure CLI

Utilize o [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para criar cada recurso individualmente. 

`kind`de recursos:

* Autoria: `LUIS.Authoring`
* Previsão: `LUIS` 

1. Inscreva-se no Azure CLI:

    ```azurecli
    az login
    ```

    Isto abre um navegador para permitir selecionar a conta correta e fornecer autenticação.

1. Criar um **recurso de autoria LUIS**, tipo `LUIS.Authoring`, denominado `my-luis-authoring-resource` no grupo de recursos _existente_ chamado `my-resource-group` para a região `westus`. 

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Criar um recurso de ponto final de **previsão LUIS**, de tipo `LUIS`, nomeado `my-luis-prediction-resource` no grupo de recursos _existente_ chamado `my-resource-group` para a região `westus`. Se quiser uma entrada superior à do nível livre, mude `F0` para `S0`. Saiba mais sobre os níveis de [preços e a entrada.](luis-boundaries.md#key-limits)

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note] 
    > Estas chaves **não** são utilizadas pelo portal LUIS até serem atribuídas no portal LUIS sobre os **recursos Manage &> Azure**.

## <a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>Atribuir um recurso de autoria no portal LUIS para todas as aplicações

Pode atribuir um recurso de autoria para uma única aplicação ou para todas as aplicações em LUIS. O procedimento seguinte atribui todas as aplicações a um único recurso de autoria.

1. Inscreva-se no [portal LUIS.](https://www.luis.ai)
1. Na barra de navegação superior, à extrema-direita, selecione a sua conta de utilizador e, em seguida, selecione **Definições**.
1. Na página **Definições** do Utilizador, selecione **Adicionar recurso de autor** e, em seguida, selecione um recurso de autor existente. Selecione **Guardar**. 

## <a name="assign-a-resource-to-an-app"></a>Atribuir um recurso a uma aplicação

Pode atribuir um único recurso, autoria ou previsão de prazo final, a uma aplicação com o seguinte procedimento.

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e, em seguida, selecione uma aplicação da lista **de aplicações My.**
1. Navegue para a página **de recursos Manage &> Azure.**

    ![Selecione os recursos Manage -> Azure no portal LUIS para atribuir um recurso à app.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Selecione o separador de recursos de previsão ou autor e, em seguida, selecione o **recurso de previsão Adicionar** ou adicionar o botão de recurso **autor.** 
1. Selecione os campos no formulário para encontrar o recurso correto e, em seguida, **selecione Guardar**.  

### <a name="assign-runtime-resource-without-using-luis-portal"></a>Atribuir recurso de tempo de execução sem usar o portal LUIS

Para fins de automação, como um pipeline CI/CD, pode querer automatizar a atribuição de um recurso de tempo de execução LUIS a uma aplicação LUIS. Para isso, é necessário realizar os seguintes passos:

1. Obtenha um token Do Gestor de Recursos Azure deste [site](https://resources.azure.com/api/token?plaintext=true). Este token expirar então, utilizá-lo imediatamente. O pedido devolve um token do Azure Resource Manager.

    ![Solicite ficha do Gestor de Recursos Azure e receba ficha do Gestor de Recursos Azure](./media/luis-manage-keys/get-arm-token.png)

1. Utilize o símbolo para solicitar os recursos de tempo de execução da LUIS através de subscrições, a partir das [contas Get LUIS azure API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), a que a sua conta de utilizador tem acesso. 

    Esta API de publicação requer as seguintes definições:

    |Cabeçalho|Valor|
    |--|--|
    |`Authorization`|O valor do `Authorization` é `Bearer {token}`. Note que o valor simbólico deve ser precedido pela palavra `Bearer` e um espaço.| 
    |`Ocp-Apim-Subscription-Key`|A sua chave de autoria.|

    Esta API devolve uma matriz de objetos JSON das suas subscrições de LUIS, incluindo o ID de subscrição, o grupo de recursos e o nome de recurso, retornado como o nome da conta. Encontre um item na matriz que é o recurso de LUIS para atribuir à aplicação LUIS. 

1. Atribuir o símbolo ao recurso LUIS com as contas do API da Atribuição de um API de [aplicação.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) 

    Esta API de publicação requer as seguintes definições:

    |Tipo|Definição|Valor|
    |--|--|--|
    |Cabeçalho|`Authorization`|O valor do `Authorization` é `Bearer {token}`. Note que o valor simbólico deve ser precedido pela palavra `Bearer` e um espaço.|
    |Cabeçalho|`Ocp-Apim-Subscription-Key`|A sua chave de autoria.|
    |Cabeçalho|`Content-type`|`application/json`|
    |Cadeia de consulta|`appid`|Pelo ID de aplicação do LUIS. 
    |Corpo||{"AzureSubscriptionId": "ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup 2",<br>"AccountName": "luis-uswest-S0-2"}|

    Quando esta API for bem sucedida, ele retorna um status de criado 201. 

## <a name="unassign-resource"></a>Anular a atribuição de recursos

1. Inscreva-se no [portal LUIS](https://www.luis.ai)e, em seguida, selecione uma aplicação da lista **de aplicações My.**
1. Navegue para a página **de recursos Manage &> Azure.**
1. Selecione o separador de recursos de Previsão ou Autor e, em seguida, selecione o botão **de recurso Unassign** para o recurso. 

Quando não atribua um recurso, não é eliminado do Azure. Só é desassociado do LUIS. 

## <a name="reset-authoring-key"></a>Repor a chave de criação

**Para a autoria de aplicações [migradas](luis-migration-authoring.md)** por recursos : se a sua chave de autor estiver comprometida, reponha a chave no portal Azure na página **Keys** para esse recurso de autoria. 

**Para aplicações que ainda não migraram**: a chave é redefinida em todas as suas aplicações no portal LUIS. Se possuir as suas aplicações através das APIs de autoria, terá de alterar o valor da Chave de Assinatura Ocp-Apim para a nova chave.

## <a name="regenerate-azure-key"></a>Chave De regeneração de Azure

Regenerar as teclas Azure do portal Azure, na página **Keys.**

## <a name="delete-account"></a>Eliminar conta

Consulte o [armazenamento e remoção de dados](luis-concept-data-storage.md#accounts) para obter informações sobre quais os dados eliminados quando eliminar a sua conta.

## <a name="change-pricing-tier"></a>Alterar escalão de preço

1.  Em [Azure,](https://portal.azure.com)encontre a sua assinatura LUIS. Selecione a subscrição LUIS.
    ![Encontre o seu](./media/luis-usage-tiers/find.png) de subscrição LUIS
1.  Selecione **o nível** de preços para ver os níveis de preços disponíveis. 
    ![Ver os níveis de preços](./media/luis-usage-tiers/subscription.png)
1.  Selecione o nível de preços e selecione **Selecione** para guardar a sua alteração. 
    ![Alterar o seu](./media/luis-usage-tiers/plans.png) de nível de pagamento LUIS
1.  Quando a alteração de preços estiver concluída, uma janela de pop-up verifica se o novo escalão de preços. 
    ![Verifique o seu](./media/luis-usage-tiers/updated.png) de nível de pagamento LUIS
1. Lembre-se de [atribuir esta chave de ponto final](#assign-a-resource-to-an-app) na página **Publicar** e usá-la em todas as consultas de ponto final. 

## <a name="viewing-azure-resource-metrics"></a>Visualização das métricas de recursos do Azure

### <a name="viewing-azure-resource-summary-usage"></a>Visualização do uso do resumo do recurso Azure
Pode ver informações de utilização do LUIS no Azure. A página **de visão geral** mostra informações sumárias recentes, incluindo chamadas e erros. Se fizer um pedido de ponto final luis, consulte imediatamente a **página 'Overview',** deixe até cinco minutos para que a utilização apareça.

![Ver utilização de resumo](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Personalização de gráficos de utilização de recursos Azure
Metrics fornece uma visão mais detalhada sobre os dados.

![Métricas predefinidas](./media/luis-usage-tiers/metrics-default.png)

Pode configurar seus gráficos de métricas para o período de tempo e o tipo de métrica. 

![Métricas personalizadas](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Alerta do limiar de total de transações
Se quiser saber quando atingiu um certo limite da transação, por exemplo 10 000 transações, pode criar um alerta. 

![Alertas de predefinição](./media/luis-usage-tiers/alert-default.png)

Adicione um alerta métrico para a métrica total das **chamadas** durante um determinado período de tempo. Adicione endereços de e-mail de todas as pessoas que devem receber o alerta. Adicione webhooks para todos os sistemas que devem receber o alerta. Também pode executar uma aplicação lógica quando o alerta é acionado. 

## <a name="next-steps"></a>Passos seguintes

* Aprenda [a usar versões](luis-how-to-manage-versions.md) para controlar o seu ciclo de vida da aplicação.
* Compreenda os conceitos, incluindo o [recurso de autoria](luis-concept-keys.md#authoring-key) e os [contribuintes](luis-concept-keys.md#contributions-from-other-authors) desse recurso.
* Aprenda [a criar](luis-how-to-azure-subscription.md) recursos de autoria e tempo de funcionamento
* Migrar para o novo [recurso de autoria](luis-migration-authoring.md) 
