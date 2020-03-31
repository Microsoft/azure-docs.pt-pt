---
title: Como adicionar conjuntos de dados de referência ao seu ambiente - Azure Time Series Insights [ Microsoft Docs
description: Este artigo descreve como adicionar um conjunto de dados de referência para aumentar dados no seu ambiente DeInsights da Série De Tempo Azure.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18~~~~
ms.openlocfilehash: 79628ed44753577023464ef6208027e1b7996d30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087257"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Crie um conjunto de dados de referência para o seu ambiente Time Series Insights usando o portal Azure

Este artigo descreve como adicionar um conjunto de dados de referência ao seu ambiente DeInsights da Série De Tempo Azure. Os dados de referência são úteis para se juntar aos seus dados de origem para aumentar os valores.

Um Conjunto de Dados de Referência é uma coleção de itens que aumentam os eventos a partir da sua fonte de evento. O motor de entrada Time Series Insights junta-se a cada evento a partir da fonte do seu evento com a linha de dados correspondente no seu conjunto de dados de referência. Esse evento aumentado fica então disponível para consulta. Esta adesão baseia-se nas colunas chave primárias definidas no seu conjunto de dados de referência.

Os dados de referência não são acompanhados retroativamente. Assim, apenas os dados de ingresso atuais e futuros são combinados e unidos à data de referência definida, uma vez configurado e carregado.

## <a name="video"></a>Vídeo

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Conheça o modelo de dados de referência da Time Series Insight.</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>Adicione um conjunto de dados de referência

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. Localize o ambiente atual da Série De Tempo Azure Insights. Selecione **Todos os recursos** no menu do lado esquerdo do portal Azure. Selecione o seu ambiente do Time Series Insights.

1. Selecione a página **'Visão Geral'.** Expanda a secção **Essentials** perto do topo da página para localizar o URL do **explorador time series insights** e abrir o link.  

   [![Expandir secção Essencial](media/add-reference-data-set/essentials.png)](media/add-reference-data-set/essentials.png#lightbox)

   Veja o explorador para o seu ambiente Time Series Insights.

1. Expanda o seletor de ambiente no explorador time series Insights. Escolha o ambiente ativo. Selecione o ícone de dados de referência na parte superior direita na página do explorador.

   [![Adicionar dados de referência](media/add-reference-data-set/tsi-select-environment-and-data-icons.png)](media/add-reference-data-set/tsi-select-environment-and-data-icons.png#lightbox)

1. Selecione o **+ Adicione um** botão de conjunto de dados para começar a adicionar um novo conjunto de dados.

   [![Adicionar conjunto de dados](media/add-reference-data-set/tsi-add-a-reference-data-set.png)](media/add-reference-data-set/tsi-add-a-reference-data-set.png#lightbox)

1. Na nova página de conjunto de dados de **referência,** escolha o formato dos dados:

   - Escolha **CSV** para dados delimitados de vírem. A primeira fila é tratada como uma linha de cabeçalho.
   - Escolha o **Array JSON** para obter dados formatados de observação de objetos JavaScript (JSON).

   [![Escolha o formato de dados.](media/add-reference-data-set/tsi-select-data-upload-option.png)](media/add-reference-data-set/tsi-select-data-upload-option.png#lightbox)

1. Fornecer os dados, utilizando um dos dois métodos:

   - Cola os dados ao editor de texto. Em seguida, selecione o botão de dados de **referência Parse.**
   - Selecione Escolher o botão **'Ficheiro'** para adicionar dados a partir de um ficheiro de texto local.

   Por exemplo, dados CSV de pasta: [ ![Dados CSV colados](media/add-reference-data-set/select-csv-and-enter-data.png)](media/add-reference-data-set/select-csv-and-enter-data.png#lightbox)

   Por exemplo, colar dados de matriz JSON: [ ![Dados da Paste JSON](media/add-reference-data-set/select-json-option-and-enter-data.png)](media/add-reference-data-set/select-json-option-and-enter-data.png#lightbox)

   Se houver um erro a analisar os valores dos dados, o erro aparece `CSV parsing error, no rows extracted`a vermelho na parte inferior da página, como .

1. Uma vez analisados com sucesso os dados, é mostrada uma grelha de dados que exibe as colunas e linhas que representam os dados. Reveja a grelha de dados para garantir a correção.

   [![Rever dados de referência](media/add-reference-data-set/review-displayed-data-grid.png)](media/add-reference-data-set/review-displayed-data-grid.png#lightbox)

1. Reveja cada coluna para compreender o tipo de dados assumido e altere o tipo de dados se necessário.  Selecione o símbolo do **#** tipo de dados na rubrica da coluna: para o dobro (dados numéricos), **T/ F** para boolean, ou **Abc** para corda.

   [![Escolha os tipos de dados nas rubricas da coluna.](media/add-reference-data-set/select-column-types.png)](media/add-reference-data-set/select-column-types.png#lightbox)

1. Mude o nome dos cabeçalhos da coluna, se necessário. O nome da coluna chave é necessário para se juntar à propriedade correspondente na sua fonte de evento. 

   > [!IMPORTANT]
   > Certifique-se de que os nomes das colunas de dados de referência correspondem exatamente ao nome do evento aos seus dados de entrada, incluindo a sensibilidade a casos. Os nomes de colunas não-chave são utilizados para aumentar os dados de entrada com os valores de dados de referência correspondentes.

1. Digite um valor no **campo Filter as linhas...** para rever linhas específicas conforme necessário. O filtro é útil para rever os dados, mas não é aplicado no momento do upload dos dados.

1. Nomeie o conjunto de dados, preenchendo o campo de **nomedo** conjunto de dados acima da grelha de dados.

    [![Diga o nome do conjunto de dados.](media/add-reference-data-set/enter-reference-data-set-name.png)](media/add-reference-data-set/enter-reference-data-set-name.png#lightbox)

1. Forneça a coluna **Chave Primária** no conjunto de dados, selecionando a queda acima da grelha de dados.

    [![Selecione as colunas de teclas.](media/add-reference-data-set/select-primary-key-column.png)](media/add-reference-data-set/select-primary-key-column.png#lightbox)

    **(Opcional)** Selecione o **+** botão para adicionar uma coluna de teclas secundária, como uma chave primária composta. Se precisar de desfazer a seleção, escolha o valor vazio a partir da queda para baixo para remover a chave secundária.

1. Para fazer o upload dos dados, selecione o botão **'Upload'** de linhas.

    [![Faça upload de linhas e confirme os dados.](media/add-reference-data-set/confirm-upload-reference-data.png)](media/add-reference-data-set/confirm-upload-reference-data.png#lightbox)

    A página confirma o upload completo e mostra a mensagem **com sucesso no conjunto de dados .**

    > [!WARNING]
    > Colunas ou propriedades partilhadas entre conjuntos de dados de referência apresentarão um erro de upload de nome de **propriedade Duplicate.** O erro não impedirá o carregamento bem sucedido dos conjuntos de dados de referência. Pode ser removido combinando linhas que partilham o nome de propriedade duplicado.

1. Selecione **Adicionar uma linha,** linhas de importação a **granel,** ou **adicionar uma coluna** para adicionar mais valores de dados de referência, conforme necessário.

    [![Adicione uma linha, linhas de importação a granel, ou adicione uma coluna.](media/add-reference-data-set/add-row-or-bulk-upload.png)](media/add-reference-data-set/add-row-or-bulk-upload.png#lightbox)

   > [!IMPORTANT]
   > Qualquer linha que partilhe uma chave única com outra linha terá as suas colunas ultrapassadas pela última linha adicionadas que partilham essa chave única.

   > [!NOTE]
   > As linhas adicionadas **não** precisam de ser *retangulares* - podem ter menos, maiores ou diferentes colunas das outras entradas no conjunto de dados de referência.

## <a name="next-steps"></a>Passos seguintes

* [Gerir dados de referência](time-series-insights-manage-reference-data-csharp.md) através de programação.

* Para obter a referência completa da API, leia o documento [da API de Dados de Referência.](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api)
