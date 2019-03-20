---
title: Como adicionar um conjunto de dados de referência para o seu ambiente do Azure Time Series Insights | Documentos da Microsoft
description: Este artigo descreve como adicionar um conjunto de dados de referência para aumentar os dados no seu ambiente do Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/15/2018
ms.custom: seodec18
ms.openlocfilehash: f5cc06ba1a326d262c1437a941306b4d4a648260
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58101232"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Criar um conjunto de dados de referência para o seu ambiente do Time Series Insights com o portal do Azure

Este artigo descreve como adicionar um conjunto de dados de referência para o seu ambiente do Azure Time Series Insights. Dados de referência são útil para associar a sua origem de dados para aumentar os valores.

Um conjunto de dados de referência é uma coleção de itens que aumentam os eventos da origem de evento. O mecanismo de entrada do Time Series Insights une cada evento da origem de evento com a linha de dados correspondente no seu conjunto de dados de referência. Esse evento aumentado fica então disponível para consulta. Esta associação baseia-se as colunas de chave primária definidas no seu conjunto de dados de referência.

Dados de referência não estão associados retroativamente. Isso significa que apenas os dados de entrada atuais e futuras são correspondidos e associados ao conjunto de datas de referência, assim que ele foi configurado e carregado.

## <a name="video"></a>Vídeo: 

### <a name="in-this-video-we-cover-time-series-insights-reference-data-modelbr"></a>Neste vídeo, vamos abordar o modelo de dados de referência do Time Series Insights.</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>Adicionar um conjunto de dados de referência

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Localize o seu ambiente do Time Series Insights existente. Clique em **todos os recursos** no menu no lado esquerdo do portal do Azure. Selecione o seu ambiente do Time Series Insights.

3. Selecione o **descrição geral** página. Localize a **URL de Explorador do Time Series Insights** e abrir a ligação.  

   Ver o Explorador para o seu ambiente do TSI.

4. Expanda o Seletor de ambiente no Explorador de TSI. Escolha o ambiente do Active Directory. Selecione o ícone de dados de referência no canto superior direito na página do explorer.

   ![Adicionar dados de referência](media/add-reference-data-set/add_reference_data.png)

5. Selecione o **+ adicionar um conjunto de dados** botão para começar a adicionar um novo conjunto de dados.

   ![Adicionar conjunto de dados](media/add-reference-data-set/add_data_set.png)

6. Sobre o **novo conjunto de dados de referência** página, escolha o formato dos dados: 
   - Escolher **CSV** para dados delimitada por vírgulas. A primeira linha é tratada como uma linha de cabeçalho. 
   - Escolher **matriz JSON** para javascript os dados serão formatados (JSON object notation).

   ![Escolha o formato de dados.](media/add-reference-data-set/add_data.png)

7. Fornece os dados, através de um dos dois métodos:
   - Cole os dados no editor de texto. Em seguida, selecione **dados de referência de análise** botão.
   - Selecione **Escolher ficheiro** botão para adicionar dados a partir de um ficheiro de texto local. 

   Por exemplo, cole os dados CSV: ![Dados colados de CSV](media/add-reference-data-set/csv_data_pasted.png)

   Por exemplo, cole os dados de matriz JSON: ![Cole os dados JSON](media/add-reference-data-set/json_data_pasted.png)

   Se existir um erro ao analisar os valores de dados, o erro é apresentado a vermelho na parte inferior da página, tal como `CSV parsing error, no rows extracted`.

8. Depois dos dados são analisados com êxito, uma grade de dados é apresentada a apresentar as colunas e linhas que representam os dados.  Reveja a grade de dados para garantir a correção.

   ![Adicionar dados de referência](media/add-reference-data-set/parse_data.png)

9. Reveja cada coluna para ver o tipo de dados, pressupõe-se e alterar o tipo de dados, se necessário.  Selecione o símbolo de tipo de dados no cabeçalho de coluna: **#** para double (numéricos dados), **T | F** para boolean, ou **Abc** para a cadeia de caracteres.

   ![Escolha os tipos de dados os cabeçalhos de coluna.](media/add-reference-data-set/choose_datatypes.png)

10. Se for necessário, mude o nome os cabeçalhos de coluna. O nome de coluna de chave é necessário para associar à propriedade correspondente na origem de evento. Certifique-se de que os nomes de coluna chave de dados de referência correspondem exatamente ao nome do evento para seus dados de entrada, incluindo sensibilidade. Os nomes de coluna sem chave são utilizados para aumentar os dados de entrada com os valores de dados de referência correspondente.

11. Clique em **adicionar uma linha** ou **adicionar uma coluna** para adicionar mais valores de dados de referência, conforme necessário.

12. Escreva um valor no **filtrar as linhas...**  campo para rever linhas específicas, conforme necessário. O filtro é útil para analisar dados, mas não se aplica ao carregar os dados.
 
13. Nomeie o conjunto de dados, preenchendo os **nome do conjunto de dados** campo acima da grelha de dados.

    ![Nome do conjunto de dados.](media/add-reference-data-set/name_reference_dataset.png)

14. Forneça o **chave primária** coluna no conjunto de dados, ao selecionar a lista pendente acima da grelha de dados.

    ![Selecione as colunas chave.](media/add-reference-data-set/set_primary_key.png)

    Opcionalmente, selecione o **+** botão para adicionar uma coluna chave secundária, como uma chave primária composta. Se precisar de anular a seleção, escolha o valor vazio na lista suspensa para remover a chave secundária.

15. Para carregar os dados, selecione o **carregue linhas** botão.

    ![Carregar](media/add-reference-data-set/upload_rows.png)

    A página confirma a conclusão de carregar e exibir a mensagem **carregado com êxito o conjunto de dados**.

## <a name="next-steps"></a>Passos Seguintes
* [Gerir dados de referência](time-series-insights-manage-reference-data-csharp.md) através de programação.
* Para obter a referência completa de API, consulte o documento [API de dados de referência](/rest/api/time-series-insights/ga-reference-data-api).
