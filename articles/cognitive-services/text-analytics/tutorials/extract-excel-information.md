---
title: Extrair informações no Excel usando Análise de Texto e Automatizar Energia
titleSuffix: Azure Cognitive Services
description: Saiba como extrair o texto do Excel sem ter de escrever código, utilizando a Análise de Texto e Automatizar a Energia.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/27/2019
ms.author: aahi
ms.openlocfilehash: 197d28b2ac3d94b6639a6611b2919bdeb2b182e2
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359906"
---
# <a name="extract-information-in-excel-using-text-analytics-and-power-automate"></a>Extrair informações no Excel usando Análise de Texto e Automatizar Energia 

Neste tutorial, irá criar um fluxo power automate para extrair texto numa folha de cálculo do Excel sem ter de escrever código. 

Este fluxo levará uma folha de cálculo de problemas relatados sobre um complexo de apartamentos, e classificá-los-á em duas categorias: canalização e outras. Também extrairá os nomes e números de telefone dos inquilinos que os enviaram. Por último, o fluxo anexará esta informação à folha excel. 

Neste tutorial, irá aprender a:

> [!div class="checklist"]
> * Use o Power Automamate para criar um fluxo
> * Carregar dados do Excel do OneDrive para negócios
> * Extrair texto do Excel e enviá-lo para a API de Análise de Texto 
> * Utilize as informações da API para atualizar uma folha excel.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Microsoft Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/cognitive-services/) ou [inscreva-se .](https://portal.azure.com/)
- Um recurso de Análise de Texto. Se não tiver um, pode [criar um no portal Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) e usar o nível livre para completar este tutorial.
- A [chave e o ponto final](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) que foi gerado para si durante a inscrição.
- Uma folha de cálculo contendo problemas com inquilinos. Os dados de exemplo são fornecidos no GitHub
- Microsoft 365, com OneDrive para negócios.

## <a name="add-the-excel-file-to-onedrive-for-business"></a>Adicione o ficheiro Excel ao OneDrive para negócios

Descarregue o ficheiro Excel exemplo do [GitHub.](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx) Este ficheiro deve ser armazenado na sua conta OneDrive for Business.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Exemplos do ficheiro Excel.":::

As questões são relatadas em texto cru. Utilizaremos o Reconhecimento de Entidade Nomeada da API para extrair o nome da pessoa e o número de telefone. Em seguida, o fluxo procurará a palavra "canalização" na descrição para categorizar as questões. 

## <a name="create-a-new-power-automate-workflow"></a>Criar um novo fluxo de trabalho power automamate

Vá ao [site Power Automamate](https://preview.flow.microsoft.com/)e faça login. Em seguida, clique em **Criar** e **Programar fluxo**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="O ecrã de criação de fluxo.":::


Na **página de fluxo programado para construir,** inicialize o seu fluxo com os seguintes campos:

|Campo |Valor  |
|---------|---------|
|**Nome do fluxo**     | **Revisão agendada** ou outro nome.         |
|**A iniciar**     |  Insira a data e hora atuais.       |
|**Repita todos os**     | **Uma hora**        |

## <a name="add-variables-to-the-flow"></a>Adicione variáveis ao fluxo

> [!NOTE]
> Se quiser ver uma imagem do fluxo completo, pode descarregá-la a partir do [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/TextAnalytics/flow-diagrams). 

Crie variáveis que representem a informação que será adicionada ao ficheiro Excel. Clique em **New Step** e procure a **variável Initialize.** Faça isto quatro vezes, para criar quatro variáveis.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="Inicializar variáveis.":::

Adicione as seguintes informações às variáveis que criou. Representam as colunas do ficheiro Excel. Se alguma variável for colapsada, pode clicar nelas para expandi-las.

| Ação |Nome   | Tipo | Valor |
|---------|---------|---|---|
| Inicializar variável | var_person | String | Pessoa |
| Inicializar variável 2 | var_phone | String | Phone_Number |
| Inicializar variável 3 | var_plumbing | String | canalização |
| Inicializar variável 4 | var_other | String | other | 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="informação contida nas variáveis de fluxo":::

## <a name="read-the-excel-file"></a>Leia o ficheiro excel

Clique em **New Step** e **digite Excel,** em seguida, selecione **Lista de linhas presentes numa tabela** a partir da lista de ações.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="adicionar linhas excel.":::

Adicione o ficheiro Excel ao fluxo preenchendo os campos nesta ação. Este tutorial requer que o ficheiro tenha sido enviado para o OneDrive para negócios.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="preencher linhas excel":::

Clique em **New Step** e adicione um Apply a **cada ação.**

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="adicionar um comando de aplicação.":::

Clique em **Selecionar uma saída do passo anterior.** Na caixa de conteúdo dinâmica que aparece, selecione **valor**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="Selecione a saída do ficheiro Excel.":::

## <a name="send-a-request-to-the-text-analytics-api"></a>Envie um pedido à API de Análise de Texto

Se ainda não o fez, tem de criar um [recurso Text Analytics](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) no portal Azure.

### <a name="create-a-text-analytics-connection"></a>Criar uma ligação text Analytics

No **Aplicar a cada um,** clique **Em Adicionar uma ação**. Aceda à chave e página de **ponto final** do seu recurso Text Analytics no portal Azure e obtenha a chave e o ponto final para o seu recurso Text Analytics.

No seu fluxo, insira as seguintes informações para criar uma nova ligação Text Analytics.

> [!NOTE]
> Se já criou uma ligação Text Analytics e pretende alterar os seus dados de ligação, clique na elipse no canto superior direito e clique **em + Adicionar nova ligação**.

| Campo           | Valor                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| Nome da Ligação | Um nome para a ligação ao seu recurso Text Analytics. Por exemplo, `TAforPowerAutomate`. |
| Chave da conta     | A chave para o seu recurso Text Analytics.                                                                                   |
| Site URL        | O ponto final para o seu recurso Text Analytics.                                                       |

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::

## <a name="extract-the-excel-content"></a>Extrair o conteúdo do excel 

Após a criação da ligação, procure por **Text Analytics** e selecione **Entidades.** Isto irá extrair informações da coluna de descrição da questão.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="Adicionar Entidades de Análise de Texto.":::

Clique no campo **Texto** e **selecione Descrição** a partir das janelas de conteúdo dinâmico que aparecem. `en`Insira para a Linguagem. (Clique em Mostrar opções avançadas se não vir linguagem)

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="Adicione as definições de Análise de Texto.":::


## <a name="extract-the-person-name"></a>Extrair o nome da pessoa

Em seguida, encontraremos o tipo de entidade pessoa na saída Text Analytics. Dentro do **Apply a cada,** clique **Em Adicionar uma ação,** e crie outra **Aplicar a cada ação.** Clique dentro da caixa de texto e selecione **Entidades** na janela Conteúdo Dinâmico que aparece.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo. 2":::

Dentro da nova ação **Aplicar a cada 2 ação,** clique em Adicionar uma **ação** e adicione um controlo **de Condição.**

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo. 3":::

Na janela 'Estado', clique na primeira caixa de texto. Na janela de conteúdo dinâmico, procure por **Tipo de Entidades** e selecione-o.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo. 4":::

Certifique-se de que a segunda caixa está definida **para ser igual a**. Em seguida, selecione a terceira caixa e procure `var_person` na janela de conteúdo Dinâmico. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo. 5":::

Na condição **"Se sim",** digite o Excel e, em seguida, selecione **Update a Row**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo. 6":::

Introduza a informação do Excel e atualize os campos **Coluna-chave,** **Valor chave** e Nome **de Pessoa.** Isto anexará o nome detetado pela API à folha Excel. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo. 7":::

## <a name="get-the-phone-number"></a>Obtenha o número de telefone

Minimize a **Aplicação a cada ação de 2** clicando no nome. Em seguida, adicione outro **Aplicar a cada ação,** como antes. será nomeado **Aplicar a cada 3**. Selecione a caixa de texto e adicione **as entidades** como saída para esta ação. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-3.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo. 8":::

Dentro **Aplicar a cada 3** , adicionar um controlo de **condição.** Será nomeado **Condição 2.** Na primeira caixa de texto, procure e adicione **o Tipo de Entidades** a partir da janela de conteúdo Dinâmico. Certifique-se de que a caixa central está definida para ser **igual a**. Em seguida, na caixa de texto certa, `var_phone` insira. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo. 9":::

Na condição **"Se sim",** adicione **uma atualização de uma ação de linha.** Em seguida, insira a informação como fizemos acima, para a coluna de números de telefone da folha excel. Isto irá anexar o número de telefone detetado pela API à folha Excel. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo. 10":::


## <a name="get-the-plumbing-issues"></a>Pegue os problemas de canalização

Minimizar **Aplicar a cada 3** clicando no nome. Em seguida, crie outro **Aplicar a cada um** na ação dos pais. Selecione a caixa de texto e adicione **Entidades** como saída para esta ação a partir da janela de conteúdo Dinâmico. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-4.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo. 11":::


Em seguida, o fluxo verificará se a descrição do número de emissão da linha de tabela Excel contém a palavra "canalização". Se sim, adicionará "canalização" na coluna IssueType. Se não, entraremos em "outros".

Dentro da **Aplicação a cada ação de 4,** adicione um Controlo **de Condições.** Será nomeado **Condição 3.** Na primeira caixa de texto, procure e adicione **Descrição** a partir do ficheiro Excel, utilizando a janela de conteúdo Dinâmico. Certifique-se de que a caixa central diz **que contém**. Em seguida, na caixa de texto certa, encontre e selecione `var_plumbing` . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo. 12":::


Na condição **'Se sim',** clique em **Adicionar uma ação** e selecione Update a **row**. Em seguida, insira a informação como antes. Na coluna 'Emitir', selecione `var_plumbing` . Isto aplicará uma etiqueta de "canalização" à linha.

No **Se não houver** condição, clique em **Adicionar uma ação** e selecione Update a **row**. Em seguida, insira a informação como antes. Na coluna 'Emitir', selecione `var_other` . Isto aplicará uma etiqueta "outra" à linha.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo. 13":::

## <a name="test-the-workflow"></a>Testar o fluxo de trabalho

No canto superior direito do ecrã, clique em **Guardar** e, em seguida, **Test**. Selecione  **I'll executar a ação do gatilho**. Clique **em Guardar & Teste,** Executar o **fluxo** e, em seguida, **fazer**.

O ficheiro Excel será atualizado na sua conta OneDrive. Vai parecer o que está abaixo.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="A folha de cálculo atualizada do excel.":::

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Explore mais soluções](../text-analytics-user-scenarios.md)
