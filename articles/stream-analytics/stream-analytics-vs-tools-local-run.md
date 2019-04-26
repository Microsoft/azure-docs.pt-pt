---
title: Testar consultas do Azure Stream Analytics localmente com o Visual Studio
description: Este artigo descreve como testar consultas localmente com o Azure Stream Analytics Tools para Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: 1b86085a76f5ff87147db9dbd0a584784f5e4a2e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60308489"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio"></a>Testar consultas do Stream Analytics localmente com o Visual Studio

Pode utilizar ferramentas do Azure Stream Analytics para Visual Studio para testar as suas tarefas do Stream Analytics localmente com dados de exemplo.

Utilize esta opção [guia de introdução](stream-analytics-quick-create-vs.md) para saber como criar uma tarefa do Stream Analytics com o Visual Studio.

## <a name="test-your-query"></a>Testar a consulta

No seu projeto do Azure Stream Analytics, faça duplo clique **Script.asaql** para abrir o script no editor. É possível compilar a consulta para ver se existem erros de sintaxe. O editor de consultas oferece suporte a IntelliSense, sintaxe colorida e um marcador de erro.

![Editor de consultas](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="add-local-input"></a>Adicionar a entrada local

Para validar a sua consulta em relação a dados estáticos locais, clique com botão direito de entrada e selecione **adicionar a entrada local**.
   
![Adicionar a entrada local](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-01.png)
   
Na janela de pop-up, selecione os dados de exemplo do seu caminho local e **guardar**.
   
![Adicionar a entrada local](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-02.png)
   
Um ficheiro denominado **local_EntryStream.json** é adicionado automaticamente à sua pasta de entradas.
   
![Lista de ficheiros da pasta de entrada local](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-03.png)
   
Selecione **executar localmente** no editor de consultas. Ou pode pressionar F5.
   
![Executar localmente](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-01.png)
   
A saída pode ser visualizada num formato de tabela diretamente a partir do Visual Studio.

![Saída em formato de tabela](./media/stream-analytics-vs-tools-local-run/stream-analytics-for-vs-local-result.png)

Pode encontrar o caminho de saída a partir da saída da consola. Prima qualquer tecla para abrir a pasta de resultado.
   
![Execução local](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-02.png)
   
Verificar os resultados na pasta local.
   
![Resultado da pasta local](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-03.png)
   

### <a name="sample-input"></a>Entrada de exemplo
Também pode recolher dados de entrada de exemplo a partir de origens de entrada para um ficheiro local. O ficheiro de configuração de entrada com o botão direito e selecione **dados de exemplo**. 

![Dados de Exemplo](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-01.png)

Pode apenas os dados de exemplo de transmissão em fluxo dos Hubs de eventos ou os Hubs IoT. Não são suportadas outras origens de entrada. Na caixa de diálogo pop-up, preencha o caminho local para guardar os dados de exemplo e selecione **exemplo**.

![Configuração de dados de exemplo](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-02.png)
 
Pode ver o progresso no **saída** janela. 

![Saída de dados de exemplo](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-03.png)

## <a name="next-steps"></a>Passos Seguintes

* [Use o Visual Studio para ver tarefas do Azure Stream Analytics](stream-analytics-vs-tools.md)
* [Quickstart: Criar uma tarefa do Stream Analytics com o Visual Studio](stream-analytics-quick-create-vs.md)
* [Tutorial: Implementar uma tarefa do Azure Stream Analytics com CI/CD com o Azure DevOps](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Integrar e desenvolver continuamente as ferramentas do Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)