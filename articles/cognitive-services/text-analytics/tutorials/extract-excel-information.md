---
title: Extrair informações em Excel usando Análise de Texto e Automatização de Energia
titleSuffix: Azure Cognitive Services
description: Saiba como extrair texto Excel sem ter de escrever código, utilizando O Texto Analytics e Power Automate.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/27/2019
ms.author: aahi
ms.openlocfilehash: fd70fe14d3765fb7c21b92f62b4d73564176baa2
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201190"
---
# <a name="extract-information-in-excel-using-text-analytics-and-power-automate"></a>Extrair informações em Excel usando Análise de Texto e Automatização de Energia 

Neste tutorial, você vai criar um fluxo power automate para extrair texto numa folha de cálculo excel sem ter que escrever código. 

Este fluxo terá uma folha de cálculo de questões relatadas sobre um complexo de apartamentos, e classificá-los-ão em duas categorias: canalização e outras. Também extrairá os nomes e números de telefone dos inquilinos que os enviaram. Por último, o fluxo anexará esta informação à folha excel. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Use power automate para criar um fluxo
> * Upload de dados do Excel do OneDrive para negócios
> * Extrair texto do Excel e enviá-lo para a API de Análise de Texto 
> * Utilize as informações da API para atualizar uma folha excel.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Microsoft Azure. [Inicie uma avaliação gratuita](https://azure.microsoft.com/free/) ou [inicie sessão](https://portal.azure.com/).
- Um recurso de Análise de Texto. Se não tiver um, pode [criar um no portal Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) e utilizar o nível livre para completar este tutorial.
- A [chave e o ponto final](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) que foi gerado para si durante a inscrição.
- Uma folha de cálculo contendo problemas com inquilinos. Dados de exemplo são fornecidos no GitHub
- Escritório 365, com OneDrive para negócios.

## <a name="add-the-excel-file-to-onedrive-for-business"></a>Adicione o ficheiro Excel ao OneDrive para negócios

Descarregue o exemplo Excel file from [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx). Este ficheiro deve ser armazenado na sua conta OneDrive para negócios.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Exemplos do ficheiro Excel.":::

As questões são relatadas em texto bruto. Usaremos o Reconhecimento de Entidade Smalítica para extrair o nome da pessoa e o número de telefone. Em seguida, o fluxo procurará a palavra "canalização" na descrição para categorizar as questões. 

## <a name="create-a-new-power-automate-workflow"></a>Criar um novo fluxo de trabalho Power Automate

Vá ao [site Power Automate](https://preview.flow.microsoft.com/)e faça login. Em seguida, clique em **Criar** e **Programar o fluxo**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="O ecrã da criação de fluxo.":::


Na Página de **fluxo seletiva,** inicialize o seu fluxo com os seguintes campos:

|Campo |Valor  |
|---------|---------|
|**Nome do fluxo**     | **Revisão agendada** ou outro nome.         |
|**Início**     |  Insira a data e hora atuais.       |
|**Repita cada**     | **1 hora**        |

## <a name="add-variables-to-the-flow"></a>Adicione variáveis ao fluxo

> [!NOTE]
> Se quiser ver uma imagem do fluxo completo, pode descarregá-la a partir do [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/TextAnalytics/flow-diagrams). 

Crie variáveis que representem a informação que será adicionada ao ficheiro Excel. Clique em **Novo Passo** e procure a **variável Inicialize**. Faça isto quatro vezes, para criar quatro variáveis.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="Inicializar variáveis.":::

Adicione as seguintes informações às variáveis que criou. Representam as colunas do ficheiro Excel. Se alguma variável for colapsada, pode clicar nelas para as expandir.

| Ação |Nome   | Tipo | Valor |
|---------|---------|---|---|
| Inicializar variável | var_person | String | Pessoa |
| Inicializar variável 2 | var_phone | String | Phone_Number |
| Inicializar variável 3 | var_plumbing | String | canalização |
| Inicializar a variável 4 | var_other | String | outro | 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="informação contida nas variáveis de fluxo":::

## <a name="read-the-excel-file"></a>Leia o ficheiro excel

Clique em **New Step** e digite **Excel,** em seguida, selecione **listas presentes numa tabela** da lista de ações.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="adicionar linhas de excel.":::

Adicione o ficheiro Excel ao fluxo preenchendo os campos nesta ação. Este tutorial requer que o ficheiro tenha sido enviado para o OneDrive para negócios.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="adicionar linhas de excel.":::

Clique em **New Step** e adicione um Apply **a cada** ação.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="adicionar um comando de aplicação.":::

Clique em **Selecionar uma saída do passo anterior**. Na caixa de conteúdo Dinâmica que aparece, selecione **o valor**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="Selecione a saída a partir do ficheiro excel.":::

## <a name="send-a-request-to-the-text-analytics-api"></a>Envie um pedido para a API de Análise de Texto

Se ainda não o fez, precisa de criar um [recurso Text Analytics](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) no portal Azure.

### <a name="create-a-text-analytics-connection"></a>Criar uma ligação de Análise de Texto

No **Aplicar a cada um,** clique **em Adicionar uma ação**. Vá à página de **chave e ponto final** do seu recurso Text Analytics no portal Azure e obtenha a chave e o ponto final para o seu recurso Text Analytics.

No seu fluxo, introduza as seguintes informações para criar uma nova ligação Text Analytics.

> [!NOTE]
> Se já criou uma ligação Text Analytics e pretende alterar os seus dados de ligação, clique na elipse no canto superior direito e clique **em + Adicione nova ligação**.

| Campo           | Valor                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| Nome da Ligação | Um nome para a ligação ao seu recurso Text Analytics. Por exemplo, `TAforPowerAutomate`. |
| Chave de conta     | A chave para o seu recurso Text Analytics.                                                                                   |
| Site URL        | O ponto final para o seu recurso Text Analytics.                                                       |

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::

## <a name="extract-the-excel-content"></a>Extrair o conteúdo excel 

Depois da ligação ser criada, procure **por Análise** de Texto e selecione **Entidades**. Isto extrairá informações da coluna de descrição do problema.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::

Clique no campo **texto** e selecione **Descrição** das janelas de conteúdo dinâmica que aparecem. Insira `en` para linguagem. (Clique em Mostrar opções avançadas se não vir o Idioma)

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::


## <a name="extract-the-person-name"></a>Extrair o nome da pessoa

Em seguida, encontraremos o tipo de entidade pessoa na saída text Analytics. Dentro do **Aplicar a cada um,** clique **Em adicionar uma ação,** e criar outra **Aplicação a cada** ação. Clique no interior da caixa de texto e selecione **Entidades** na janela Conteúdo Dinâmico que aparece.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::

Dentro da ação recém-criada **Aplicar a cada 2** ação, clique Em adicionar uma **ação**, e adicione um controlo **de condição.**

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::

Na janela Condição, clique na primeira caixa de texto. Na janela de conteúdo Dinâmico, procure **Entidades Tipo** e selecione-a.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::

Certifique-se de que a segunda caixa está definida **é igual a**. Em seguida, selecione a terceira caixa e procure `var_person` na janela de conteúdo Dinâmico. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::

Na condição **Se sim,** digite em Excel e, em seguida, selecione **Atualizar uma Linha**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::

Introduza as informações do Excel e atualize os campos **'Chave** **Column,'Key Value** e **PersonName.** Isto anexará o nome detetado pela API à folha Excel. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::

## <a name="get-the-phone-number"></a>Obtenha o número de telefone

Minimize a **Aplicação a cada 2** açãoclicando o nome. Em seguida, adicione outra **Aplicar a cada** ação, como antes. será nomeado **Aplicar a cada 3**. Selecione a caixa de texto e adicione **as entidades** como saída para esta ação. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-3.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::

Dentro **Aplicar a cada 3**, adicione um controlo de **condição.** Será nomeado **Condição 2**. Na primeira caixa de texto, procure e adicione **Entidades Tipo** a partir da janela de conteúdo Dinâmico. Certifique-se de que a caixa central está definida **é igual a**. Em seguida, na caixa de texto certa, introduza `var_phone`. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::

Na condição **Se sim,** adicione uma **atualização de** linha. Em seguida, insira a informação como fizemos acima, para a coluna de números de telefone da folha excel. Isto anexará o número de telefone detetado pela API à folha Excel. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::


## <a name="get-the-plumbing-issues"></a>Obtenha os problemas de canalização

**Minimizar Aplicar a cada 3** clicando no nome. Em seguida, crie outra **Aplicação para cada um** na ação dos pais. Selecione a caixa de texto e adicione **As Entidades** como a saída para esta ação a partir da janela de conteúdo Dinâmico. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-4.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::


Em seguida, o fluxo verificará se a descrição do problema da tabela Excel contém a palavra "canalização". Se sim, adicionará "canalização" na coluna IssueType. Se não, entraremos em "outro".

No interior do **Aplicar a cada 4** ação, adicione um Controlo de **Condições.** Chamar-se-á **Condição 3**. Na primeira caixa de texto, procure e adicione **descrição** do ficheiro Excel, utilizando a janela de conteúdo Dinâmica. Certifique-se de que a caixa central diz **que contém**. Em seguida, na caixa de texto certa, encontre e selecione `var_plumbing`. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::


Na condição **Se sim,** clique em **Adicionar uma ação**, e selecione Atualizar uma **linha**. Em seguida, insira a informação como antes. Na coluna IssueType, selecione `var_plumbing`. Isto aplicará uma etiqueta de "canalização" na linha.

No **Caso não exista** qualquer condição, clique em **Adicionar uma ação**, e selecione Atualizar uma **linha**. Em seguida, insira a informação como antes. Na coluna IssueType, selecione `var_other`. Isto aplicará uma etiqueta "outra" na fila.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="Adicione credenciais de Análise de Texto ao seu fluxo.":::

## <a name="test-the-workflow"></a>Testar o fluxo de trabalho

No canto superior direito do ecrã, clique em **Guardar**, em **seguida, testar**. Selecione **vou executar a ação do gatilho.** Clique em **Guardar e Testar,** **executar o fluxo,** em **seguida, pronto**.

O ficheiro Excel será atualizado na sua conta OneDrive. Vai parecer o que se segue.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="A folha de cálculo de excel atualizada.":::

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Explore mais soluções](../text-analytics-user-scenarios.md)
