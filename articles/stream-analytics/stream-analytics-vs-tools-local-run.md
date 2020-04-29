---
title: Test Azure Stream Analytics consultas localmente em Estúdio Visual
description: Este artigo descreve como testar consultas localmente com ferramentas de análise de Fluxo Azure para estúdio visual.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: 34c8555356d5c0142d7b677c8119fe66806ab064
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76834914"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio"></a>Test Stream Analytics consultas localmente com Estúdio Visual

Pode utilizar ferramentas Azure Stream Analytics para o Estúdio Visual para testar os seus trabalhos de Streaming Analytics localmente com dados de amostra ou [dados ao vivo.](stream-analytics-live-data-local-testing.md) 

Use este [Quickstart](stream-analytics-quick-create-vs.md) para aprender a criar um trabalho de Stream Analytics usando o Visual Studio.

## <a name="test-your-query"></a>Testar a consulta

No seu projeto Azure Stream Analytics, clique duas vezes no **Script.asaql** para abrir o script no editor. Pode compilar a consulta para ver se existem erros de sintaxe. O editor de consulta suporta intelliSense, coloração de sintaxe e um marcador de erro.

![Editor de consultas](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="add-local-input"></a>Adicione a entrada local

Para validar a sua consulta com dados estáticos locais, clique na entrada e **selecione Adicionar entrada local**.
   
![Adicione a entrada local](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-01.png)
   
Na janela pop-up, selecione dados da amostra do seu caminho local e **guarde**.
   
![Adicione a entrada local](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-02.png)
   
Um ficheiro chamado **local_EntryStream.json** é adicionado automaticamente à pasta de entrada.
   
![Lista de ficheiros de pasta de entrada local](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-03.png)
   
Selecione **Executar Localmente** no editor de consulta. Ou pode premir F5.
   
![Correr localmente](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-01.png)
   
A saída pode ser visualizada em formato de tabela diretamente do Visual Studio.

![Saída em formato de mesa](./media/stream-analytics-vs-tools-local-run/stream-analytics-for-vs-local-result.png)

Pode encontrar o caminho de saída a partir da saída da consola. Pressione qualquer tecla para abrir a pasta de resultados.
   
![Execução local](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-02.png)
   
Verifique os resultados na pasta local.
   
![Resultado da pasta local](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-03.png)
   

### <a name="sample-input"></a>Entrada da amostra
Também pode recolher dados de entrada de amostras das suas fontes de entrada para um ficheiro local. Clique no ficheiro de configuração da entrada e selecione Dados da **Amostra**. 

![Dados de Exemplo](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-01.png)

Só é possível provar o streaming de dados a partir de Centros de Eventos ou Hubs IoT. Outras fontes de entrada não são apoiadas. Na caixa de diálogo pop-up, preencha o caminho local para guardar os dados da amostra e selecione **Sample**.

![Configuração de dados da amostra](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-02.png)
 
Pode ver o progresso na janela **output.** 

![Saída de dados da amostra](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-03.png)

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Criar um trabalho de Stream Analytics usando o Estúdio Visual](stream-analytics-quick-create-vs.md)
* [Use o Estúdio Visual para ver empregos da Azure Stream Analytics](stream-analytics-vs-tools.md)
* [Teste dados ao vivo localmente utilizando ferramentas Azure Stream Analytics para Estúdio Visual (Pré-visualização)](stream-analytics-live-data-local-testing.md)
* [Tutorial: Implemente um trabalho de Análise de Fluxo Saléis Azure com CI/CD utilizando O Azure DevOps](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Integrar e desenvolver continuamente as ferramentas do Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)
