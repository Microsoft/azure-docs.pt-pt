---
title: Como adicionar conjuntos de dados de referência ao seu ambiente - Azure Time Series Insights Microsoft Docs
description: Este artigo descreve como adicionar um conjunto de dados de referência para aumentar os dados no seu ambiente Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: d80d97a609aa3a464b9b114439fe7f4058e287c3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001835"
---
# <a name="create-a-reference-data-set-for-your-azure-time-series-insights-gen1-environment-using-the-azure-portal"></a>Crie um conjunto de dados de referência para o seu ambiente Azure Time Series Insights Gen1 utilizando o portal Azure

> [!CAUTION]
> Este é um artigo da Gen1.

Este artigo descreve como adicionar um conjunto de dados de referência ao ambiente Azure Time Series Insights. Os dados de referência são úteis para se juntar aos seus dados de origem para aumentar os valores.

Um Conjunto de Dados de Referência é uma coleção de itens que aumentam os eventos a partir da sua fonte de evento. O motor de entrada de Ingress Azure Time Series Insights junta-se a cada evento a partir da sua fonte de evento com a correspondente linha de dados no seu conjunto de dados de referência. Esse evento aumentado fica então disponível para consulta. Esta junção baseia-se na coluna(s) da Chave Primária definida no seu conjunto de dados de referência.

Os dados de referência não são unidos retroativamente. Assim, apenas os dados atuais e futuros de entrada são combinados e unidos à data de referência definida, uma vez configurados e carregados.

## <a name="video"></a>Vídeo

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Saiba mais sobre o modelo de dados de referência da Time Series Insight.</br>

> [!VIDEO <https://www.youtube.com/embed/Z0NuWQUMv1o>]

## <a name="add-a-reference-data-set"></a>Adicione um conjunto de dados de referência

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Localize o ambiente existente da Série de TempoS Azure Insights. Selecione **Todos os recursos** no menu do lado esquerdo do portal Azure. Selecione o seu ambiente Azure Time Series Insights.

1. Selecione a página **Descrição geral**. Expanda a secção **Essentials** perto do topo da página para localizar o URL do **Time Series Insights Explorer** e abrir o link.  

   [![Expandir a secção Essencial](media/add-reference-data-set/essentials.png)](media/add-reference-data-set/essentials.png#lightbox)

   Consulte o Explorer para o seu ambiente Azure Time Series Insights.

1. Expandir o seletor de ambiente no Azure Time Series Insights Explorer. Escolha o ambiente ativo. Selecione o ícone de dados de referência na parte superior direita na página Explorer.

   [![Adicionar dados de referência](media/add-reference-data-set/tsi-select-environment-and-data-icons.png)](media/add-reference-data-set/tsi-select-environment-and-data-icons.png#lightbox)

1. Selecione o **botão + Conjunto de dados** para começar a adicionar um novo conjunto de dados.

   [![Adicionar conjunto de dados](media/add-reference-data-set/tsi-add-a-reference-data-set.png)](media/add-reference-data-set/tsi-add-a-reference-data-set.png#lightbox)

1. Na nova página **de conjunto de dados** de referência, escolha o formato dos dados:

   - Escolha **CSV** para dados delimitados em vírgula. A primeira linha é tratada como uma linha de cabeçalho.
   - Escolha **o Conjunto JSON** para dados formatados de notação de objetos JavaScript (JSON).

   [![Escolha o formato de dados.](media/add-reference-data-set/tsi-select-data-upload-option.png)](media/add-reference-data-set/tsi-select-data-upload-option.png#lightbox)

1. Forneça os dados, utilizando um dos dois métodos:

   - Cole os dados no editor de texto. Em seguida, selecione o botão **de dados de referência parse.**
   - Selecione Escolher o botão **'Escolha'** para adicionar dados a partir de um ficheiro de texto local.

   Por exemplo, colar dados de CSV: [ ![ Dados de CSV colados](media/add-reference-data-set/select-csv-and-enter-data.png)](media/add-reference-data-set/select-csv-and-enter-data.png#lightbox)

   Por exemplo, cole os dados da matriz JSON: [ ![ Colar dados JSON](media/add-reference-data-set/select-json-option-and-enter-data.png)](media/add-reference-data-set/select-json-option-and-enter-data.png#lightbox)

   Se houver um erro de análise dos valores dos dados, o erro aparece a vermelho na parte inferior da página, tal como `CSV parsing error, no rows extracted` .

1. Uma vez analisados os dados com sucesso, é mostrada uma grelha de dados que apresenta as colunas e linhas que representam os dados. Reveja a grelha de dados para garantir a correção.

   [![Rever dados de referência](media/add-reference-data-set/review-displayed-data-grid.png)](media/add-reference-data-set/review-displayed-data-grid.png#lightbox)

1. Reveja cada coluna para entender o tipo de dados assumido e altere o tipo de dados se necessário.  Selecione o símbolo do tipo de dados no título da coluna: **#** para duplo (dados numéricos), **T/ F** para boolean, ou **Abc** para cordas.

   [![Escolha tipos de dados nas rubricas da coluna.](media/add-reference-data-set/select-column-types.png)](media/add-reference-data-set/select-column-types.png#lightbox)

1. Mude o nome dos cabeçalhos da coluna, se necessário. O nome da coluna-chave é necessário para se juntar à propriedade correspondente na sua fonte de evento.

   > [!IMPORTANT]
   > Certifique-se de que os nomes da coluna de referência correspondem exatamente ao nome do evento com os dados de entrada, incluindo a sensibilidade ao caso. Os nomes das colunas não-chave são utilizados para aumentar os dados de entrada com os valores de dados de referência correspondentes.

1. Digite um valor no **Filtro das linhas...** campo para rever linhas específicas conforme necessário. O filtro é útil para rever dados, mas não é aplicado ao carregar os dados.

1. Nomeie o conjunto de dados, preenchendo o campo de **nomes de conjunto de dados** acima da grelha de dados.

    [![Nomeie o conjunto de dados.](media/add-reference-data-set/enter-reference-data-set-name.png)](media/add-reference-data-set/enter-reference-data-set-name.png#lightbox)

1. Forneça a coluna **Chave Primária** no conjunto de dados, selecionando a queda acima da grelha de dados.

    [![Selecione a coluna(s) das chaves.](media/add-reference-data-set/select-primary-key-column.png)](media/add-reference-data-set/select-primary-key-column.png#lightbox)

    **(Opcional)** Selecione o **+** botão para adicionar uma coluna de chave secundária, como uma chave primária composta. Se precisar de desfazer a seleção, escolha o valor vazio da queda para remover a tecla secundária.

1. Para fazer o upload dos dados, selecione o botão **'Carregar linhas'.**

    [![Faça upload de linhas e confirme os dados.](media/add-reference-data-set/confirm-upload-reference-data.png)](media/add-reference-data-set/confirm-upload-reference-data.png#lightbox)

    A página confirma o upload concluído e exibe a mensagem **com sucesso no conjunto de dados carregados**.

    > [!WARNING]
    > Colunas ou propriedades partilhadas entre conjuntos de dados de referência apresentarão um erro de upload **de nome de propriedade duplicado.** O erro não impedirá o upload bem sucedido dos conjuntos de dados de referência. Pode ser removido combinando linhas que partilham o nome de propriedade duplicado.

1. **Selecione Adicione uma linha**, **Linhas de importação a granel,** ou **Adicione uma coluna** para adicionar mais valores de dados de referência, conforme necessário.

    [![Adicione uma linha, linhas de importação a granel, ou Adicione uma coluna.](media/add-reference-data-set/add-row-or-bulk-upload.png)](media/add-reference-data-set/add-row-or-bulk-upload.png#lightbox)

   > [!IMPORTANT]
   > Qualquer linha que partilhe uma chave única com outra linha terá as suas colunas superadas pela última linha adicionada que partilha essa chave única.

   > [!NOTE]
   > As linhas adicionadas **não** precisam de ser *retangulares* - podem ter menos, maiores ou diferentes colunas das outras entradas no conjunto de dados de referência.

## <a name="next-steps"></a>Passos seguintes

- [Gerir dados de referência](time-series-insights-manage-reference-data-csharp.md) através de programação.

- Para obter a referência completa da API, leia o documento [API de Dados de Referência.](/rest/api/time-series-insights/gen1-reference-data-api)