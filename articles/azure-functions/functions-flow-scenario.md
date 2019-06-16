---
title: Chamar uma função do Azure a partir do Microsoft Flow | Documentos da Microsoft
description: Criar um conector personalizado, em seguida, chamar uma função com esse conector.
services: functions
keywords: aplicações na cloud, cloud processos de negócio de serviços, Microsoft Flow, aplicativo de negócios
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: glenga
ms.reviewer: sunayv
ms.custom: ''
ms.openlocfilehash: d3e777b5611dec382dc4eaaac5ec1594abcdab31
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65787678"
---
# <a name="call-a-function-from-microsoft-flow"></a>Chamar uma função a partir do Microsoft Flow

[Microsoft Flow](https://flow.microsoft.com/) torna mais fácil automatizar fluxos de trabalho e processos empresariais entre as aplicações favoritas e serviços. Desenvolvedores profissionais que pode utilizar as funções do Azure para estender os recursos do Microsoft Flow, enquanto os construtores de fluxo dos detalhes técnicos de proteção.

Criar um fluxo neste tópico, com base num cenário de manutenção para turbines de vento. Este tópico mostra-lhe como chamar a função que definiu nas [criar uma definição de OpenAPI para uma função](functions-openapi-definition.md). A função determina se uma reparação de emergência numa turbina eólica é rentável. Se é rentável, o fluxo envia um e-mail para a reparação de recomendar.

Para informações sobre como chamar a mesma função a partir do PowerApps, veja [chamar uma função a partir do PowerApps](functions-powerapps-scenario.md).

Neste tópico, ficará a saber como:

> [!div class="checklist"]
> * Crie uma lista no SharePoint.
> * Exporte uma definição de API.
> * Adicione uma ligação à API.
> * Crie um fluxo para enviar correio eletrónico se uma reparação é rentável.
> * Execute o fluxo.

[!INCLUDE [functions-openapi-note](../../includes/functions-openapi-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

+ Um ativo [conta do Microsoft Flow](https://flow.microsoft.com/documentation/sign-up-sign-in/) com o mesmo sinal em credenciais da sua conta do Azure. 
+ SharePoint, o que utilizar como uma origem de dados para este fluxo. Inscreva-se [uma versão de avaliação do Office 365](https://signup.microsoft.com/Signup?OfferId=467eab54-127b-42d3-b046-3844b860bebf&dl=O365_BUSINESS_PREMIUM&ali=1) se ainda não tiver o SharePoint.
+ Concluir o tutorial [criar uma definição de OpenAPI para uma função](functions-openapi-definition.md).

## <a name="create-a-sharepoint-list"></a>Criar uma lista do SharePoint
Começar através da criação de uma lista que utilizar como uma origem de dados para o fluxo. A lista tem as seguintes colunas.

| Coluna de lista     | Tipo de Dados           | Notas                                    |
|-----------------|---------------------|------------------------------------------|
| **Título**           | Uma linha de texto | Nome da turbina                      |
| **LastServiceDate** | Date                |                                          |
| **MaxOutput**       | Number              | Saída da turbina, em KwH            |
| **ServiceRequired** | Sim/Não              |                                          |
| **EstimatedEffort** | Number              | Tempo estimado para o reparo, em horas |

1. No seu site do SharePoint, clique ou toque **New**, em seguida, **lista**.

    ![Criar lista do SharePoint nova](./media/functions-flow-scenario/new-list.png)

2. Introduza o nome `Turbines`, em seguida, clique ou toque **criar**.

    ![Especificar o nome da lista nova](./media/functions-flow-scenario/create-list.png)

    O **Turbines** lista é criada, com o padrão **Title** campo.

    ![Lista de turbines](./media/functions-flow-scenario/initial-list.png)

3. Clique ou toque em ![ícone de novo item](./media/functions-flow-scenario/icon-new.png) , em seguida, **data**.

    ![Adicionar campo de uma linha de texto](./media/functions-flow-scenario/add-column.png)

4. Introduza o nome `LastServiceDate`, em seguida, clique ou toque **criar**.

    ![Criar coluna LastServiceDate](./media/functions-flow-scenario/date-column.png)

5. Repita os dois últimos passos para as outras três colunas na lista:

    1. **Number** > "MaxOutput"

    2. **Yes/No** > "ServiceRequired"

    3. **Number** > "EstimatedEffort"

Isso é tudo por agora – deve ter uma lista vazia que se parece com a imagem seguinte. Adicionar dados à lista depois de criar o fluxo.

![Lista vazia](media/functions-flow-scenario/empty-list.png)

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>Adicionar uma ligação à API
A API personalizada (também conhecido como um conector personalizado) está disponível no Microsoft Flow, mas tem de estabelecer uma ligação para a API antes de poder utilizá-lo num fluxo.

1. Na [flow.microsoft.com](https://flow.microsoft.com), clique no ícone de engrenagem (no canto superior direito), em seguida, clique em **ligações**.

    ![Ligações de fluxo](media/functions-flow-scenario/flow-connections.png)

1. Clique em **criar ligação**, desloque para baixo até o **reparação na turbina** conector e clique no mesmo.

    ![Criar ligação](media/functions-flow-scenario/create-connection.png)

1. Introduza a chave de API e clique em **criar ligação**.

    ![Introduza a chave de API e criar](media/functions-flow-scenario/api-key.png)

> [!NOTE]
> Se partilhar o seu fluxo com outras pessoas, cada pessoa que funciona em ou utiliza o fluxo também tem de introduzir a chave de API para ligar à API. Este comportamento poderá mudar no futuro e atualizaremos este tópico para refletir a alteração.


## <a name="create-a-flow"></a>Criar um fluxo

Agora, está pronto para criar um fluxo que utiliza o conector personalizado e a lista do SharePoint que criou.

### <a name="add-a-trigger-and-specify-a-condition"></a>Adicionar um acionador e especificar uma condição

Primeiro criar um fluxo do zero (sem um modelo) e adicione um *acionador* que é acionado quando é criado um item na lista do SharePoint. Em seguida, adicione uma *condição* para determinar o que acontece em seguida.

1. Na [flow.microsoft.com](https://flow.microsoft.com), clique em **meus fluxos**, em seguida, **criar do zero**.

    ![Criar do zero](media/functions-flow-scenario/create-from-blank.png)

2. Clique no acionador do SharePoint **quando um item é criado**.

    ![Escolher um acionador](media/functions-flow-scenario/choose-trigger.png)

    Se ainda não tiver iniciado no SharePoint, será solicitado a fazê-lo.

3. Para **endereço do Site**, introduza o nome de site do SharePoint e para **nome da lista**, introduza a lista que contém os dados de turbina.

    ![Escolher um acionador](media/functions-flow-scenario/site-list.png)

4. Clique em **novo passo**, em seguida, **adicionar uma condição**.

    ![Adicionar uma condição](media/functions-flow-scenario/add-condition.png)

    Microsoft Flow adiciona duas ramificações para o fluxo: **Se Sim** e **se não**. Adicione passos para um ou ambos os ramos depois de definir a condição que pretende corresponder.

    ![Ramos de condição](media/functions-flow-scenario/condition-branches.png)

5. Sobre o **condição** cartão, clique na primeira caixa, em seguida, selecione **ServiceRequired** do **conteúdo dinâmico** caixa de diálogo.

    ![Selecione o campo para a condição](media/functions-flow-scenario/condition1-field.png)

6. Introduza um valor de `True` para a condição.

    ![Introduza verdadeiro para a condição](media/functions-flow-scenario/condition1-yes.png)

    O valor é apresentado como `Yes` ou `No` no SharePoint lista, mas ele é armazenado como uma *booleano*, seja `True` ou `False`. 

7. Clique em **criar fluxo** na parte superior da página. Certifique-se de que clique em **fluxo de atualização** periodicamente.

Para todos os itens de criar na lista, o fluxo verifica se o **ServiceRequired** campo é definido como `Yes`, em seguida, vai para o **se Sim** ramo ou o **se não** ramificar como apropriado. Para economizar tempo, neste tópico só é especificar ações para o **em caso afirmativo** ramo.

### <a name="add-the-custom-connector"></a>Adicionar o conector personalizado

Agora, adicione o conector personalizado que chama a função no Azure. Adicionar o conector personalizado para o fluxo, tal como um conector padrão. 

1. Na **se Sim** ramo, clique em **adicionar uma ação**.

    ![Adicionar uma ação](media/functions-flow-scenario/condition1-yes-add-action.png)

2. Na **escolher uma ação** caixa de diálogo, procure `Turbine Repair`, em seguida, selecione a ação **reparação na turbina - calcula os custos**.

    ![Escolher uma ação](media/functions-flow-scenario/choose-turbine-repair.png)

    A imagem seguinte mostra o cartão que é adicionado ao fluxo. Os campos e descrições vêm da definição de OpenAPI para o conector.

    ![Calcula as predefinições de custos](media/functions-flow-scenario/calculates-costs-default.png)

3. Sobre o **calcula os custos** cartão, utilize o **conteúdo dinâmico** caixa de diálogo para selecionar as entradas para a função. Microsoft Flow mostra campos numéricos, mas não o campo de data, porque a definição de OpenAPI Especifica que **horas** e **capacidade** sejam numéricos.

    Para **horas**, selecione **EstimatedEffort**e para **capacidade**, selecione **MaxOutput**.

    ![Escolher uma ação](media/functions-flow-scenario/calculates-costs-fields.png)

     Agora é adicionar outra condição com base no resultado da função.

4. Na parte inferior a **se Sim** ramo, clique em **mais**, em seguida, **adicionar uma condição**.

    ![Adicionar uma condição](media/functions-flow-scenario/condition2-add.png)

5. Sobre o **condição 2** cartão, clique na primeira caixa, em seguida, selecione **mensagem** do **conteúdo dinâmico** caixa de diálogo.

    ![Selecione o campo para a condição](media/functions-flow-scenario/condition2-field.png)

6. Introduza um valor de `Yes`. O fluxo vai para o próximo **se Sim** ramo ou **se não** ramo baseia-se a mensagem devolvida pela função é yes (marca o reparo) ou (não faça a reparação). 

    ![Introduza yes de condição](media/functions-flow-scenario/condition2-yes.png)

O fluxo deverá agora ser semelhante à imagem seguinte.

![Introduza yes de condição](media/functions-flow-scenario/flow-checkpoint1.png)

### <a name="send-email-based-on-function-results"></a>Enviar um e-mail com base nos resultados de função

Neste ponto no fluxo, a função devolveu um **mensagem** valor de `Yes` ou `No` da função, bem como outras informações sobre os custos e o potencial de receita. Na **se Sim** ramo da segunda condição, enviará uma mensagem de e-mail, mas pode fazer qualquer número de coisas, como o Write back para a lista do SharePoint ou a partir de um [processo de aprovação](https://flow.microsoft.com/documentation/modern-approvals/).

1. Na **se Sim** ramo da segunda condição, clique em **adicionar uma ação**.

    ![Adicionar uma ação](media/functions-flow-scenario/condition2-yes-add-action.png)

2. Na **escolher uma ação** caixa de diálogo, procure `email`, em seguida, selecione uma ação de enviar um e-mail com base no sistema de e-mail a utilizar (no Outlook neste caso).

    ![Envio de Outlook uma mensagem de e-mail](media/functions-flow-scenario/outlook-send-email.png)

3. Sobre o **enviar um e-mail** cartão, compor uma mensagem de e-mail. Introduza um nome válido na sua organização para o **para** campo. Na imagem abaixo pode ver os outros campos são uma combinação de texto e tokens a partir da **conteúdo dinâmico** caixa de diálogo. 

    ![Campos de correio eletrónico](media/functions-flow-scenario/email-fields.png)

    O **Title** token vem da lista do SharePoint, e **CostToFix** e **RevenueOpportunity** são devolvidas pela função.

    O fluxo concluído deve ter um aspeto semelhante à imagem seguinte (deixamos de lado o primeiro **se não** ramo para economizar espaço).

    ![Fluxo completo](media/functions-flow-scenario/complete-flow.png)

4. Clique em **fluxo de atualização** na parte superior da página, em seguida, clique em **feito**.

## <a name="run-the-flow"></a>Executar o fluxo

Agora que o fluxo está concluído, adicionar uma linha à lista do SharePoint e ver como responde.

1. Voltar à lista do SharePoint e clique em **edição rápida**.

    ![Edição rápida](media/functions-flow-scenario/quick-edit.png)

2. Introduza os seguintes valores na grade de edição.

    | Coluna de lista     | Value           |
    |-----------------|---------------------|
    | **Título**           | Turbina 60 |
    | **LastServiceDate** | 08/04/2017 |
    | **MaxOutput**       | 2500 |
    | **ServiceRequired** | Sim |
    | **EstimatedEffort** | 10 |

3. Clique em **Concluído**.

    ![Edição rápida concluída](media/functions-flow-scenario/quick-edit-done.png)

    Quando adiciona o item, aciona o fluxo, que lhe dar uma olhada no seguinte.

4. Na [flow.microsoft.com](https://flow.microsoft.com), clique em **meus fluxos**, em seguida, clique em fluxo que criou.

    ![Meus fluxos](media/functions-flow-scenario/my-flows.png)

5. Sob **histórico de EXECUÇÕES**, clique em execução do fluxo.

    ![Histórico de execuções](media/functions-flow-scenario/run-history.png)

    Se a execução foi concluída com êxito, pode rever as operações de fluxo na página seguinte. Se a execução falhou por qualquer motivo, a página seguinte fornece informações de resolução de problemas.

6. Expanda os cartões para ver o que ocorreu durante o fluxo. Por exemplo, expanda o **calcula os custos** cartão para ver as entradas e saídas da função. 

    ![Calcula os custos entradas e saídas](media/functions-flow-scenario/calculates-costs-outputs.png)

7. Verifique a conta de e-mail da pessoa que especificou no **para** campo a **enviar um e-mail** cartão. O e-mail enviado a partir do fluxo deve ter um aspeto semelhante à imagem seguinte.

    ![E-mail do Flow](media/functions-flow-scenario/flow-email.png)

    Pode ver como os tokens foram substituídos com os valores corretos da lista do SharePoint e a função.

## <a name="next-steps"></a>Passos Seguintes
Neste tópico, ficou a saber como:

> [!div class="checklist"]
> * Crie uma lista no SharePoint.
> * Exporte uma definição de API.
> * Adicione uma ligação à API.
> * Crie um fluxo para enviar correio eletrónico se uma reparação é rentável.
> * Execute o fluxo.

Para saber mais sobre o Microsoft Flow, veja [introdução ao Microsoft Flow](https://flow.microsoft.com/documentation/getting-started/).

Para saber mais sobre outros cenários interessantes que utilizam as funções do Azure, veja [chamar uma função a partir do PowerApps](functions-powerapps-scenario.md) e [criar uma função que se integra no Azure Logic Apps](functions-twitter-email.md).
