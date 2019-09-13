---
title: Testar Azure Stream Analytics consultas localmente com o Visual Studio
description: Este artigo descreve como testar consultas localmente com o Azure Stream Analytics Tools para Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: 4ec427ad7af18f4f301e1e62ca21af643a0425aa
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914017"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio"></a>Testar Stream Analytics consultas localmente com o Visual Studio

Você pode usar as ferramentas do Azure Stream Analytics para Visual Studio para testar seus trabalhos do Stream Analytics localmente com dados de exemplo ou [dados dinâmicos](stream-analytics-live-data-local-testing.md). 

Use este guia de [início rápido](stream-analytics-quick-create-vs.md) para aprender a criar um trabalho de Stream Analytics usando o Visual Studio.

## <a name="test-your-query"></a>Testar a consulta

No projeto Azure Stream Analytics, clique duas vezes em **script. asaql** para abrir o script no editor. Você pode compilar a consulta para ver se há erros de sintaxe. O editor de consultas dá suporte ao IntelliSense, à coloração de sintaxe e a um marcador de erro.

![Editor de consultas](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="add-local-input"></a>Adicionar entrada local

Para validar sua consulta em relação aos dados estáticos locais, clique com o botão direito do mouse na entrada e selecione **Adicionar entrada local**.
   
![Adicionar entrada local](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-01.png)
   
Na janela pop-up, selecione dados de exemplo do caminho local e **salve**.
   
![Adicionar entrada local](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-02.png)
   
Um arquivo chamado **local_EntryStream. JSON** é adicionado automaticamente à sua pasta entradas.
   
![Lista de arquivos da pasta de entrada local](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-03.png)
   
Selecione **executar localmente** no editor de consultas. Ou você pode pressionar F5.
   
![Executar localmente](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-01.png)
   
A saída pode ser exibida em um formato de tabela diretamente do Visual Studio.

![Saída em formato de tabela](./media/stream-analytics-vs-tools-local-run/stream-analytics-for-vs-local-result.png)

Você pode encontrar o caminho de saída na saída do console. Pressione qualquer tecla para abrir a pasta de resultados.
   
![Execução local](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-02.png)
   
Verifique os resultados na pasta local.
   
![Resultado da pasta local](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-03.png)
   

### <a name="sample-input"></a>Entrada de exemplo
Você também pode coletar dados de entrada de exemplo de suas fontes de entrada para um arquivo local. Clique com o botão direito do mouse no arquivo de configuração de entrada e selecione **dados de exemplo**. 

![Dados de Exemplo](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-01.png)

Você só pode obter amostras de streaming de dados de hubs de eventos ou hubs IoT. Não há suporte para outras fontes de entrada. Na caixa de diálogo pop-up, preencha o caminho local para salvar os dados de exemplo e selecione **exemplo**.

![Configuração de dados de exemplo](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-02.png)
 
Você pode ver o progresso na janela de **saída** . 

![Saída de dados de exemplo](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-03.png)

## <a name="next-steps"></a>Passos Seguintes


* [Quickstart: Criar um trabalho de Stream Analytics usando o Visual Studio](stream-analytics-quick-create-vs.md)
* [Use o Visual Studio para ver tarefas do Azure Stream Analytics](stream-analytics-vs-tools.md)
* [Testar dados dinâmicos localmente usando o Azure Stream Analytics Tools para Visual Studio (visualização)](stream-analytics-live-data-local-testing.md)
* [Tutorial: Implantar um trabalho de Azure Stream Analytics com CI/CD usando o Azure DevOps](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Integrar e desenvolver continuamente as ferramentas do Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)