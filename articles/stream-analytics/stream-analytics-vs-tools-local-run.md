---
title: Teste Azure Stream Analytics consultas localmente em Visual Studio
description: Este artigo descreve como testar consultas localmente com Azure Stream Analytics Tools for Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 07/10/2018
ms.openlocfilehash: aa1d8ff0743399d96436f33426ebc4c4ab4a29f6
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045642"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio"></a>Teste Stream Analytics consultas localmente com Visual Studio

Pode utilizar ferramentas Azure Stream Analytics para o Visual Studio para testar os seus trabalhos stream Analytics localmente com dados de amostra ou [dados ao vivo.](stream-analytics-live-data-local-testing.md) 

Utilize este [Quickstart](stream-analytics-quick-create-vs.md) para aprender a criar um trabalho stream Analytics utilizando o Visual Studio.

## <a name="test-your-query"></a>Testar a consulta

No seu projeto Azure Stream Analytics, clique duas vezes em **Script.asaql** para abrir o script no editor. Pode compilar a consulta para ver se existem erros de sintaxe. O editor de consulta suporta IntelliSense, coloração de sintaxe e um marcador de erro.

![Editor de consultas](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="add-local-input"></a>Adicionar entrada local

Para validar a sua consulta contra dados estáticos locais, clique com o botão direito na entrada e **selecione Adicionar a entrada local**.
   
![Adicionar entrada local](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-01.png)
   
Na janela pop-up, selecione os dados da amostra do seu caminho local e **guarde**.
   
![Adicionar entrada local](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-02.png)
   
Um ficheiro com o nome **local_EntryStream.jsligado** é adicionado automaticamente à pasta das entradas.
   
![Lista de ficheiros de pasta de entrada local](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-03.png)
   
**Selecione Executar Localmente** no editor de consulta. Ou pode pressionar F5.
   
![Executar Localmente](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-01.png)
   
A saída pode ser visualizada num formato de mesa diretamente do Visual Studio.

![Saída em formato de mesa](./media/stream-analytics-vs-tools-local-run/stream-analytics-for-vs-local-result.png)

Pode encontrar o caminho de saída a partir da saída da consola. Prima qualquer tecla para abrir a pasta de resultados.
   
![Execução local](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-02.png)
   
Verifique os resultados na pasta local.
   
![Resultado da pasta local](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-03.png)
   

### <a name="sample-input"></a>Entrada de amostra
Também pode recolher dados de entrada de amostras das suas fontes de entrada para um ficheiro local. Clique com o botão direito no ficheiro de configuração de entrada e selecione **Dados de Amostra**. 

![Dados de Exemplo](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-01.png)

Só é possível amostrar o streaming de dados a partir de Centros de Eventos ou IoT Hubs. Outras fontes de entrada não são suportadas. Na caixa de diálogo pop-up, preencha o caminho local para guardar os dados da amostra e selecione **Sample**.

![Configuração de dados de amostra](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-02.png)
 
Pode ver o progresso na janela **de saída.** 

![Saída de dados da amostra](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-03.png)

## <a name="next-steps"></a>Próximos passos

* [Quickstart: Criar um trabalho stream analytics usando o Visual Studio](stream-analytics-quick-create-vs.md)
* [Use o Estúdio Visual para ver os trabalhos do Azure Stream Analytics](stream-analytics-vs-tools.md)
* [Teste dados ao vivo localmente usando ferramentas Azure Stream Analytics para Estúdio Visual (Pré-visualização)](stream-analytics-live-data-local-testing.md)
* [Tutorial: Implementar um trabalho de Azure Stream Analytics com CI/CD usando Azure DevOps](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Integrar e desenvolver continuamente as ferramentas do Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)
