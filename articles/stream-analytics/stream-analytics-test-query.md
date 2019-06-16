---
title: Testar uma tarefa Azure Stream Analytics com dados de exemplo
description: Este artigo descreve como utilizar o portal do Azure para testar uma tarefa do Azure Stream Analytics, a entrada de exemplo e carregar dados de exemplo.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: fca76b632e9bcc27ed762886eaea696a5696ad3f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60761947"
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>Testar uma consulta do Stream Analytics com dados de exemplo

Ao utilizar o Azure Stream Analytics, pode carregar consultas de dados e teste de exemplo no portal do Azure sem iniciar ou parar uma tarefa.

## <a name="upload-sample-data-and-test-the-query"></a>Carregar dados de exemplo e testar a consulta

1. Inicie sessão no Portal do Azure. 

2. Localize a tarefa de Stream Analytics existente e selecioná-lo.

3. Sobre o Stream Analytics tarefa em página, o **topologia da tarefa** cabeçalho, selecione **consulta** para abrir a janela do editor de consulta. 

4. Para testar a consulta com dados de entrada de exemplo, com o botão direito em qualquer uma das suas entradas.  Em seguida, selecione **carregar dados de exemplo do ficheiro**. Os dados devem ser serializados no JSON, CSV ou AVRO. Entrada de exemplo tem de ser codificada em UTF-8 e não comprimida. Delimitador de vírgula (,) só é suportada para a testar entrada CSV no portal.

    ![editor de consultas de teste de consulta do Stream analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. Depois do carregamento estiver concluído, selecione **testar** para testar esta consulta contra os dados de exemplo que forneceu.

    ![dados de exemplo de teste do editor de consulta do Stream analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. Se precisar do resultado do teste para uso posterior, é apresentado o resultado da consulta no browser com uma ligação para os resultados da transferência. 

7. Iterativamente, modifique a consulta e testá-lo novamente para ver como o resultado é alterado.

   ![Saída de exemplo do editor de consulta do Stream Analytics](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   Quando utiliza várias saídas numa consulta, os resultados são mostrados nas guias separados e pode alternar facilmente entre eles.

8. Depois de verificar os resultados mostrados no navegador, **guardar** sua consulta. Em seguida, **iniciar** a tarefa e deixá-lo a processar os eventos de entrada.

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Referência do idioma de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
