---
title: Ver trabalhos de Azure Stream Analytics em Estúdio Visual
description: Aprenda a visualizar, iniciar e parar, testar ligações, verificar resultados e exportar os seus trabalhos de Azure Stream Analytics usando o Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: ad35ed342dfd40b98d61919749479ec5612cdd4f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75369631"
---
# <a name="use-visual-studio-to-view-azure-stream-analytics-jobs"></a>Use o Estúdio Visual para ver empregos da Azure Stream Analytics

As ferramentas Azure Stream Analytics para o Estúdio Visual facilitam aos desenvolvedores a gestão dos seus trabalhos de Stream Analytics diretamente do IDE. Com ferramentas Azure Stream Analytics, pode:
- [Criar novos empregos](stream-analytics-quick-create-vs.md)
- Iniciar, parar e [monitorizar os empregos](stream-analytics-monitor-jobs-use-vs.md)
- Verifique os resultados do trabalho
- Exportar empregos existentes para um projeto
- Testar ligações de entrada e saída
- [Executar consultas localmente](stream-analytics-vs-tools-local-run.md)

Saiba como [instalar ferramentas Azure Stream Analytics para Estúdio Visual](stream-analytics-tools-for-visual-studio-install.md).

## <a name="explore-the-job-view"></a>Explore a vista de trabalho

Você pode usar a vista de trabalho para interagir com os trabalhos da Azure Stream Analytics do Visual Studio.

### <a name="open-the-job-view"></a>Abra a vista de trabalho

1. No **Server Explorer,** selecione **trabalhos de Análise stream** e, em seguida, selecione **Refresh**. O seu trabalho deve aparecer sob **os trabalhos da Stream Analytics.**

    ![Lista de exploradores de servidores Stream Analytics](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-list-jobs-01.png)

2. Expanda o seu nó de trabalho e clique duas vezes no nó **de Job View** para abrir uma vista de trabalho.
    
   ![Nó de trabalho expandido](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-job-view-01.png)

### <a name="start-and-stop-jobs"></a>Iniciar e parar empregos

Os trabalhos da Azure Stream Analytics podem ser totalmente geridos a partir da vista de trabalho no Visual Studio. Utilize os comandos para iniciar, parar ou apagar um trabalho.
    
   ![Controlos de trabalho stream Analytics](./media/stream-analytics-vs-tools/azure-stream-analytics-job-view-controls.png)

## <a name="check-job-results"></a>Verifique os resultados do trabalho

As ferramentas Stream Analytics para o Estúdio Visual suportam atualmente a pré-visualização de saída para o armazenamento de Lagos De Dados Azure e armazenamento de blob. Para visualizar o resultado, basta clicar duas vezes no nó de saída do diagrama de trabalho em Vista de **Trabalho** e introduzir as credenciais apropriadas.

   ![Saída de blob de trabalho stream Analytics](./media/stream-analytics-vs-tools/stream-analytics-blob-preview.png)

## <a name="export-jobs-to-a-project"></a>Exportação de empregos para um projeto

Há duas maneiras de exportar um emprego existente para um projeto.

1. No **Server Explorer,** sob o nó Stream Analytics Jobs, clique no nó de trabalho. Selecione **Export to New Stream Analytics Project**.
    
   ![Exportação de emprego para projeto](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-01.png)
    
    O projeto gerado aparece no **Solution Explorer.**
    
   ![Explorador de soluções](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-02.png)

2. Na vista de trabalho, selecione **Generate Project**.
    
   ![Gerar projeto a partir da vista de emprego](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="test-connections"></a>Ligações de teste

As ligações de entrada e saída podem ser testadas a partir da Vista de **Trabalho** selecionando uma opção a partir do dropdown de Ligação de **Teste.**

   ![Desistência da ligação de teste](./media/stream-analytics-vs-tools/stream-analytics-test-connection-dropdown.png)

Os resultados da **Ligação de Teste** são apresentados na janela **saída.**

   ![Resultados da ligação de teste](./media/stream-analytics-vs-tools/stream-analytics-test-connection-results.png)

## <a name="next-steps"></a>Passos seguintes

* [Monitorize e gerea postos de trabalho da Azure Stream Analytics utilizando o Visual Studio](stream-analytics-monitor-jobs-use-vs.md)
* [Quickstart: Criar um trabalho de Stream Analytics usando o Estúdio Visual](stream-analytics-quick-create-vs.md)
* [Tutorial: Implementar uma tarefa do Azure Stream Analytics com CI/CD através do Azure Pipelines](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Integrar e desenvolver continuamente as ferramentas do Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)
