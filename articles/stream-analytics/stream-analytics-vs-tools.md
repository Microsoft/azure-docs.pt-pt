---
title: Ver tarefas do Azure Stream Analytics no Visual Studio
description: Este artigo descreve como ver tarefas do Stream Analytics no Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: 1c7133801eb0d95616cacf501162e6cee3da7c80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61477907"
---
# <a name="use-visual-studio-to-view-azure-stream-analytics-jobs"></a>Use o Visual Studio para ver tarefas do Azure Stream Analytics

Ferramentas do Stream Analytics do Azure para Visual Studio torna mais fácil para os desenvolvedores gerenciem seus trabalhos do Stream Analytics diretamente do IDE. Com as ferramentas do Azure Stream Analytics, pode:
- [Criar novas tarefas](stream-analytics-quick-create-vs.md)
- Iniciar, parar, e [monitorizar tarefas](stream-analytics-monitor-jobs-use-vs.md)
- Resultados da verificação de tarefa
- Exportar tarefas existentes para um projeto
- Ligações de entrada e saídas de teste
- [Executar consultas localmente](stream-analytics-vs-tools-local-run.md)

Saiba como [instalar as ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-tools-for-visual-studio-install.md).

## <a name="explore-the-job-view"></a>Explore a vista de tarefas

Pode utilizar a vista de tarefas para interagir com tarefas do Azure Stream Analytics a partir do Visual Studio.

### <a name="open-the-job-view"></a>Abra a vista de tarefas

1. Na **Explorador de servidores**, selecione **as tarefas do Stream Analytics** e, em seguida, selecione **atualizar**. A tarefa deve aparecer sob **tarefas do Stream Analytics**.

    ![Lista de Explorador de servidores do Stream Analytics](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-list-jobs-01.png)



2. Expanda o nó de trabalho e faça duplo clique no **vista de tarefas** o nó para abrir uma vista de tarefas.
    
   ![Nó de trabalho expandido](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-job-view-01.png)

### <a name="start-and-stop-jobs"></a>Iniciar e parar tarefas

Tarefas do Azure Stream Analytics podem ser totalmente geridas a partir da vista de tarefas no Visual Studio. Utilize os controlos para iniciar, parar ou eliminar uma tarefa.
    
   ![Controles de tarefa do Stream Analytics](./media/stream-analytics-vs-tools/azure-stream-analytics-job-view-controls.png)


## <a name="check-job-results"></a>Resultados da verificação de tarefa

Ferramentas de análise do Stream para o Visual Studio suporta atualmente a pré-visualização de saída para armazenamento de BLOBs e de armazenamento do Azure Data Lake. Para ver o resultado, apenas clique duas vezes o nó de saída do diagrama de tarefas no **vista de tarefas** e introduza as credenciais apropriadas.

   ![Saída de blob de tarefa do Stream Analytics](./media/stream-analytics-vs-tools/stream-analytics-blob-preview.png)


## <a name="export-jobs-to-a-project"></a>Tarefas de exportação para um projeto

Existem duas formas, pode exportar uma tarefa existente para um projeto.

1. Na **Explorador de servidores**, sob o nó de tarefas do Stream Analytics, clique com botão direito no nó de trabalho. Selecione **exportar para o novo projeto do Stream Analytics**.
    
   ![Exportar tarefa ao projeto](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-01.png)
    
    O projeto gerado aparece na **Explorador de soluções**.
    
   ![Explorador de soluções](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-02.png)

2. Na vista de tarefas, selecione **projeto gerar**.
    
   ![Gerar o projeto a partir da vista de tarefas](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="test-connections"></a>Ligações de teste

Ligações de entrada e saídas podem ser testadas do **vista de tarefas** ao selecionar uma opção da **Testar ligação** lista pendente.

   ![Lista pendente de ligação de teste](./media/stream-analytics-vs-tools/stream-analytics-test-connection-dropdown.png)

O **Testar ligação** os resultados são apresentados sob a **saída** janela.

   ![Resultados do teste de ligação](./media/stream-analytics-vs-tools/stream-analytics-test-connection-results.png)

## <a name="next-steps"></a>Passos Seguintes

* [Monitorizar e gerir tarefas de Azure Stream Analytics com o Visual Studio](stream-analytics-monitor-jobs-use-vs.md)
* [Quickstart: Criar uma tarefa do Stream Analytics com o Visual Studio](stream-analytics-quick-create-vs.md)
* [Tutorial: Implementar uma tarefa do Azure Stream Analytics com CI/CD com Pipelines do Azure](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Integrar e desenvolver continuamente as ferramentas do Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)
