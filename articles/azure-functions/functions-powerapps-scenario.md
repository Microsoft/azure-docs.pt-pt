---
title: Chamar uma função a partir do PowerApps | Documentos da Microsoft
description: Criar um conector personalizado, em seguida, chamar uma função com esse conector.
services: functions
keywords: aplicações na cloud, cloud processos de negócio de serviços, o PowerApps, aplicativo de negócios
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: glenga
ms.reviewer: sunayv
ms.custom: ''
ms.openlocfilehash: 26f6502f63b39d3f1ecf8dfeb09c8df4daa63b68
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65786120"
---
# <a name="call-a-function-from-powerapps"></a>Chamar uma função a partir do PowerApps
O [PowerApps](https://powerapps.microsoft.com) plataforma foi concebida para os especialistas em negócios criar aplicações sem código de aplicação tradicional. Desenvolvedores profissionais que pode utilizar as funções do Azure para expandir as funcionalidades do PowerApps, ao PowerApps criadores de aplicativos dos detalhes técnicos de proteção.

Criar uma aplicação neste tópico, com base num cenário de manutenção para turbines de vento. Este tópico mostra-lhe como chamar a função que definiu nas [criar uma definição de OpenAPI para uma função](functions-openapi-definition.md). A função determina se uma reparação de emergência numa turbina eólica é rentável.

![Aplicação concluída no PowerApps](media/functions-powerapps-scenario/finished-app.png)

Para informações sobre como chamar a mesma função a partir do Microsoft Flow, veja [chamar uma função a partir do Microsoft Flow](functions-flow-scenario.md).

Neste tópico, ficará a saber como:

> [!div class="checklist"]
> * Prepare os dados de exemplo no Excel.
> * Exporte uma definição de API.
> * Adicione uma ligação à API.
> * Criar uma aplicação e adicionar origens de dados.
> * Adicione controlos para ver os dados na aplicação.
> * Adicione controlos para chamar a função e exibir dados.
> * Execute a aplicação para determinar se uma reparação é rentável.

[!INCLUDE [functions-openapi-note](../../includes/functions-openapi-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

+ Um ativo [conta do PowerApps](https://docs.microsoft.com/powerapps/maker/signup-for-powerapps) com o mesmo sinal em credenciais da sua conta do Azure. 
+ Excel e o [ficheiro de Excel de exemplo](https://procsi.blob.core.windows.net/docs/turbine-data.xlsx) que irá utilizar como origem de dados para a sua aplicação.
+ Concluir o tutorial [criar uma definição de OpenAPI para uma função](functions-openapi-definition.md).

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>Adicionar uma ligação à API
A API personalizada (também conhecido como um conector personalizado) está disponível no PowerApps, mas tem de estabelecer uma ligação para a API antes de poder utilizá-lo numa aplicação.

1. Na [web.powerapps.com](https://web.powerapps.com), clique em **ligações**.

    ![Ligações do PowerApps](media/functions-powerapps-scenario/powerapps-connections.png)

1. Clique em **nova ligação**, desloque para baixo até o **reparação na turbina** conector e clique no mesmo.

    ![Nova ligação](media/functions-powerapps-scenario/new-connection.png)

1. Introduza a chave de API e clique em **criar**.

    ![Criar ligação](media/functions-powerapps-scenario/create-connection.png)

> [!NOTE]
> Se partilhar a sua aplicação com outras pessoas, cada pessoa que funciona em ou utiliza a aplicação também tem de introduzir a chave de API para ligar à API. Este comportamento poderá mudar no futuro e atualizaremos este tópico para refletir a alteração.

## <a name="create-an-app-and-add-data-sources"></a>Criar uma aplicação e adicionar origens de dados
Agora, está pronto para criar a aplicação no PowerApps e adicionar os dados do Excel e a API personalizada como origens de dados para a aplicação.

1. Em [web.powerapps.com](https://web.powerapps.com), escolha **Começar com aplicação em branco** > ![Phone app icon](media/functions-powerapps-scenario/icon-phone-app.png) (telemóvel) > **Tornar esta aplicação**.

    ![Comece do zero - aplicação de telefone](media/functions-powerapps-scenario/create-phone-app.png)

    A aplicação abre no PowerApps Studio para a web. A imagem seguinte mostra as diferentes partes do PowerApps Studio.

    ![PowerApps Studio](media/functions-powerapps-scenario/powerapps-studio.png)

    **(A) barra de navegação à esquerda**, onde verá uma exibição hierárquica de todos os controles em cada ecrã

    **(B) painel meio**, que mostra o ecrã que está a trabalhar no

    **Painel direito (C)** , onde define opções como o layout e origens de dados

    **(D) propriedade** na lista pendente, onde pode selecionar as propriedades que as fórmulas se aplicam a

    **(E) barra fórmulas**, onde adiciona fórmulas (como no Excel) que definem o comportamento da aplicação
    
    **(F) a faixa de opções**, onde pode adicionar controlos e personalizar elementos de estrutura

1. Adicione o ficheiro do Excel como uma origem de dados.

    Os dados que irá importar é semelhante ao seguinte:

    ![Dados do Excel para importar](media/functions-powerapps-scenario/excel-table.png)

    1. Na tela da aplicação, escolha **ligar aos dados**.

    1. Sobre o **dados** painel, clique em **adicionar dados estáticos à sua aplicação**.

        ![Adicionar origem de dados](media/functions-powerapps-scenario/add-static-data.png)

        Normalmente ler e escrever dados a partir de uma origem externa, mas estiver a adicionar os dados do Excel como dados estáticos porque este é um exemplo.

    1. Navegue até ao ficheiro do Excel que guardou, selecione o **Turbines** da tabela e clique em **Connect**.

        ![Adicionar origem de dados](media/functions-powerapps-scenario/choose-table.png)


1. Adicione a API personalizada como origem de dados.

    1. Sobre o **dados** painel, clique em **Adicionar origem de dados**.

    1. Clique em **reparação na turbina**.

        ![Conector de reparação turbina](media/functions-powerapps-scenario/turbine-connector.png)

## <a name="add-controls-to-view-data-in-the-app"></a>Adicionar controlos para ver os dados na aplicação
Agora que as origens de dados estão disponíveis na aplicação, adicione um ecrã para a sua aplicação para que pode ver os dados de turbina.

1. Sobre o **home page** separador, clique em **novo ecrã** > **ecrã de lista**.

    ![Ecrã de lista](media/functions-powerapps-scenario/list-screen.png)

    PowerApps adiciona um ecrã que contém um *galeria* que exibe itens e outros controles que permitem a pesquisa, classificação e filtragem.

1. Alterar a barra de título para `Turbine Repair`e redimensione a galeria, portanto, não há espaço para mais controlos sob a mesma.

    ![Alterar o título e redimensione a Galeria](media/functions-powerapps-scenario/gallery-title.png)

1. Com a Galeria selecionada, no painel da direita, em **propriedades**, clique em **CustomGallerySample**.

    ![Origem de dados de alteração](media/functions-powerapps-scenario/change-data-source.png)

1. Na **dados** painel, selecione **Turbines** da lista.

    ![Selecionar origem de dados](media/functions-powerapps-scenario/select-data-source.png)

    O conjunto de dados não contém uma imagem, por isso, em seguida, alterar o esquema para se adequar melhor os dados. 

1. Ainda na **dados** painel, altere **Layout** para **título, subtítulo e corpo**.

    ![Esquema de Galeria de alteração](media/functions-powerapps-scenario/change-layout.png)

1. Como o último passo da **dados** painel, alterar os campos que são apresentados na galeria.

    ![Alterar os campos da Galeria](media/functions-powerapps-scenario/change-fields.png)
    
    + **Body1** = LastServiceDate
    + **Subtitle2** = ServiceRequired
    + **Title2** Title 

1. Com a Galeria selecionada, defina o **TemplateFill** propriedade como a fórmula seguinte: `If(ThisItem.IsSelected, Orange, White)`.

    ![Fórmula de preenchimento do modelo](media/functions-powerapps-scenario/formula-fill.png)

    Agora é mais fácil ver qual o item de galeria estiver selecionado.

    ![Item selecionado](media/functions-powerapps-scenario/selected-item.png)

1. Não é necessário o ecrã original na aplicação. No painel da esquerda, Paire o rato sobre **Screen1**, clique em **...** , e **eliminar**.

    ![Eliminar ecrã](media/functions-powerapps-scenario/delete-screen.png)

1. Clique em **ficheiro**e dê o nome da aplicação. Clique em **salvar** no menu da esquerda, em seguida, clique em **guardar** no canto inferior direito.

Há muita outra formatação, que faria normalmente num aplicativo de produção, mas vamos passar para a parte importante para este cenário - chamando a função.

## <a name="add-controls-to-call-the-function-and-display-data"></a>Adicionar controlos para chamar a função e exibir dados
Tem uma aplicação que apresenta dados de resumos para cada turbina, portanto, agora é hora de adicionar controles que chamar a função que criou e exibir os dados que são devolvidos. Acessa a função com base na forma que lhe um nome na definição OpenAPI; Neste caso é `TurbineRepair.CalculateCosts()`.

1. No Friso, sobre o **inserir** separador, clique em **botão**. Em seguida, no mesmo separador, clique em **etiqueta**

    ![Inserir botão e etiqueta](media/functions-powerapps-scenario/insert-controls.png)

1. Arraste o botão e a etiqueta para baixo da Galeria e redimensione a etiqueta. 

1. Selecione o texto do botão e altere-o para `Calculate costs`. A aplicação deve ter um aspeto semelhante à imagem seguinte.

    ![Aplicação com o botão](media/functions-powerapps-scenario/move-button-label.png)

1. Selecione o botão e introduza a seguinte fórmula para o botão **OnSelect** propriedade.

    ```
    If (BrowseGallery1.Selected.ServiceRequired="Yes", ClearCollect(DetermineRepair, TurbineRepair.CalculateCosts({hours: BrowseGallery1.Selected.EstimatedEffort, capacity: BrowseGallery1.Selected.MaxOutput})))
    ```
    Esta fórmula é executado quando o botão for clicado, e faz o seguinte se o item de galeria selecionada tem um **ServiceRequired** valor de `Yes`:

    + Limpa a *recolha* `DetermineRepair` para remover dados de chamadas anteriores. Uma coleção é uma variável em tabela.

    + Atribui à coleção os dados retornados, chamando a função `TurbineRepair.CalculateCosts()`. 
    
        Os valores passados para a função são provenientes do **EstimatedEffort** e **MaxOutput** campos para o item selecionado na galeria. Estes campos não são apresentados na galeria, mas eles permanecerão disponíveis para utilização em fórmulas.

1. Selecione a etiqueta e introduza a seguinte fórmula para a etiqueta **texto** propriedade.

    ```
    "Repair decision: " & First(DetermineRepair).message & " | Cost: " & First(DetermineRepair).costToFix & " | Revenue: " & First(DetermineRepair).revenueOpportunity
    ```
    Esta fórmula utiliza a `First()` para acessar a linha do primeira (e única) do `DetermineRepair` coleção. Em seguida, apresenta os três valores que a função devolve: `message`, `costToFix`, e `revenueOpportunity`. Estes valores são em branco antes da aplicação é executada pela primeira vez.

    A aplicação concluída deve ter um aspeto semelhante à imagem seguinte.

    ![Aplicação concluída antes de executar](media/functions-powerapps-scenario/finished-app-before-run.png)


## <a name="run-the-app"></a>Executar a aplicação
Tem uma aplicação completa! Agora é hora de executá-lo e ver que a função chama-se em ação.

1. No canto superior direito do PowerApps Studio, clique no botão de execução: ![Botão de execução da aplicação](media/functions-powerapps-scenario/f5-arrow-sm.png).

1. Selecione uma turbina com um valor de `Yes` para **ServiceRequired**, em seguida, clique nas **calcular os custos** botão. Deverá ver um resultado semelhante à imagem seguinte.

    ![Aplicação concluída no PowerApps](media/functions-powerapps-scenario/finished-app.png)

1. Experimente outras turbines para ver o que é retornado pela função cada vez.

## <a name="next-steps"></a>Passos Seguintes
Neste tópico, ficou a saber como:

> [!div class="checklist"]
> * Prepare os dados de exemplo no Excel.
> * Exporte uma definição de API.
> * Adicione uma ligação à API.
> * Criar uma aplicação e adicionar origens de dados.
> * Adicione controlos para ver os dados na aplicação.
> * Adicionar controlos para chamar a função e exibir dados
> * Execute a aplicação para determinar se uma reparação é rentável.

Para saber mais sobre o PowerApps, veja [introdução ao PowerApps](https://powerapps.microsoft.com/tutorials/getting-started/).

Para saber mais sobre outros cenários interessantes que utilizam as funções do Azure, veja [chamar uma função a partir do Microsoft Flow](functions-flow-scenario.md) e [criar uma função que se integra no Azure Logic Apps](functions-twitter-email.md).
