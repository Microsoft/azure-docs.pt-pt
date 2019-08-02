---
title: Como adicionar um conjunto de dados de referência ao seu ambiente de Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como adicionar um conjunto de dados de referência para aumentar os dados em seu ambiente de Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/26/2019
ms.custom: seodec18
ms.openlocfilehash: 5b48a41f025ef06c69e6de126e0a64ad359ce09a
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68666370"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Criar um conjunto de dados de referência para seu ambiente de Time Series Insights usando o portal do Azure

Este artigo descreve como adicionar um conjunto de dados de referência ao seu ambiente de Azure Time Series Insights. Os dados de referência são úteis para unir seus dados de origem para aumentar os valores.

Um conjunto de dados de referência é uma coleção de itens que aumentam os eventos da origem do evento. Time Series Insights mecanismo de entrada une cada evento da origem do evento com a linha de dados correspondente no seu conjunto de dados de referência. Esse evento aumentado fica então disponível para consulta. Essa junção se baseia nas colunas de chave primária definidas no seu conjunto de dados de referência.

Os dados de referência não estão ingressados retroativamente. Assim, somente os dados de entrada atuais e futuros são correspondidos e associados à data de referência definida, depois que ele tiver sido configurado e carregado.

## <a name="video"></a>Vídeo

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Saiba mais sobre o modelo de dados de referência do time Series Insight.</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>Adicionar um conjunto de dados de referência

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Localize o seu ambiente do Time Series Insights existente. Selecione **todos os recursos** no menu no lado esquerdo do portal do Azure. Selecione o seu ambiente do Time Series Insights.

1. Selecione a página **visão geral** . Localize a **URL do time Series insights Explorer** e abra o link.  

   Exiba o Gerenciador para seu ambiente de TSI.

1. Expanda o seletor de ambiente no Gerenciador de TSI. Escolha o ambiente ativo. Selecione o ícone dados de referência no canto superior direito na página do Gerenciador.

   [![Adicionar dados de referência](media/add-reference-data-set/add-reference-data.png)](media/add-reference-data-set/add-reference-data.png#lightbox)

1. Selecione o botão **+ Adicionar um conjunto de dados** para começar a adicionar um novo conjunto de dados.

   [![Adicionar conjunto de dados](media/add-reference-data-set/add-data-set.png)](media/add-reference-data-set/add-data-set.png#lightbox)

1. Na página **novo conjunto de dados de referência** , escolha o formato dos dados:
   - Escolha **CSV** para dados delimitados por vírgula. A primeira linha é tratada como uma linha de cabeçalho.
   - Escolha a **matriz JSON** para dados formatados do JSON (JavaScript Object Notation).

   [![Escolha o formato de dados.](media/add-reference-data-set/add-data.png)](media/add-reference-data-set/add-data.png#lightbox)

1. Forneça os dados usando um dos dois métodos:
   - Cole os dados no editor de texto. Em seguida, selecione o botão **analisar dados de referência** .
   - Selecione o botão **escolher arquivo** para adicionar dados de um arquivo de texto local.

   Por exemplo, Cole dados CSV: [![Dados CSV colados](media/add-reference-data-set/csv-data-pasted.png)](media/add-reference-data-set/csv-data-pasted.png#lightbox)

   Por exemplo, Cole os dados da matriz JSON: [![Colar dados JSON](media/add-reference-data-set/json-data-pasted.png)](media/add-reference-data-set/json-data-pasted.png#lightbox)

   Se houver um erro ao analisar os valores de dados, o erro aparecerá em vermelho na parte inferior da página, como `CSV parsing error, no rows extracted`.

1. Depois que os dados são analisados com êxito, uma grade de dados é mostrada exibindo as colunas e as linhas que representam os dados.  Examine a grade de dados para garantir a exatidão.

   [![Adicionar dados de referência](media/add-reference-data-set/parse-data.png)](media/add-reference-data-set/parse-data.png#lightbox)

1. Examine cada coluna para ver o tipo de dados assumido e altere o tipo de dados, se necessário.  Selecione o símbolo de tipo de dados no título da **#** coluna: para duplo (dados numéricos), **T | F** para booliano ou **ABC** para cadeia de caracteres.

   [![Escolha os tipos de dados nos títulos das colunas.](media/add-reference-data-set/choose-datatypes.png)](media/add-reference-data-set/choose-datatypes.png#lightbox)

1. Renomeie os cabeçalhos de coluna, se necessário. O nome da coluna de chave é necessário para ingressar na propriedade correspondente na origem do evento. Verifique se os nomes da coluna da chave de dados de referência correspondem exatamente ao nome do evento para os dados de entrada, incluindo a diferenciação de maiúsculas e minúsculas. Os nomes de coluna não chave são usados para aumentar os dados de entrada com os valores de dados de referência correspondentes.

1. Selecione **Adicionar uma linha** ou **Adicionar uma coluna** para adicionar mais valores de dados de referência, conforme necessário.

1. Digite um valor no campo **filtrar as linhas...** para revisar linhas específicas conforme necessário. O filtro é útil para revisar dados, mas não é aplicado ao carregar os dados.

1. Nomeie o conjunto de dados, preenchendo o campo **nome do conjunto de dados** acima da grade de dados.

    [![Nomeie o conjunto de dados.](media/add-reference-data-set/name-reference-dataset.png)](media/add-reference-data-set/name-reference-dataset.png#lightbox)

1. Forneça a coluna de **chave primária** no conjunto de dados, selecionando a lista suspensa acima da grade de dados.

    [![Selecione as colunas de chave.](media/add-reference-data-set/set-primary-key.png)](media/add-reference-data-set/set-primary-key.png#lightbox)

    Opcionalmente, selecione o **+** botão para adicionar uma coluna de chave secundária, como uma chave primária composta. Se você precisar desfazer a seleção, escolha o valor vazio na lista suspensa para remover a chave secundária.

1. Para carregar os dados, selecione o botão **carregar linhas** .

    [![Carregar](media/add-reference-data-set/upload-rows.png)](media/add-reference-data-set/upload-rows.png#lightbox)

    A página confirma o carregamento concluído e exibe o conjunto de mensagens **carregado com êxito**.

## <a name="next-steps"></a>Passos Seguintes

* [Gerir dados de referência](time-series-insights-manage-reference-data-csharp.md) através de programação.

* Para obter a referência completa de API, consulte o documento [API de dados de referência](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api).
