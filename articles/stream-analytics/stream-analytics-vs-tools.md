---
title: Ver trabalhos do Azure Stream Analytics no Estúdio Visual
description: Aprenda a ver, iniciar e parar, testar ligações, verificar resultados e exportar os seus trabalhos Azure Stream Analytics usando o Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 07/10/2018
ms.openlocfilehash: 43b145f5a37ea4209947e54cb276e16ff55c1da1
ms.sourcegitcommit: e0ec3c06206ebd79195d12009fd21349de4a995d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97682814"
---
# <a name="use-visual-studio-to-view-azure-stream-analytics-jobs"></a>Use o Estúdio Visual para ver os trabalhos do Azure Stream Analytics

As ferramentas Azure Stream Analytics para o Visual Studio facilitam aos desenvolvedores a gestão dos seus trabalhos stream Analytics diretamente do IDE. Com ferramentas Azure Stream Analytics, pode:
- [Criar novos empregos](stream-analytics-quick-create-vs.md)
- Começar, parar e [monitorizar os trabalhos](stream-analytics-monitor-jobs-use-vs.md)
- Ver resultados do trabalho
- Exportar postos de trabalho existentes para um projeto
- Testar ligações de entrada e saída
- [Executar consultas localmente](stream-analytics-vs-tools-local-run.md)

Saiba como [instalar ferramentas Azure Stream Analytics para o Visual Studio](stream-analytics-tools-for-visual-studio-install.md).

## <a name="explore-the-job-view"></a>Explore a vista de emprego

Você pode usar a vista de trabalho para interagir com os trabalhos da Azure Stream Analytics do Visual Studio.

### <a name="open-the-job-view"></a>Abra a vista de emprego

1. No **Server Explorer**, selecione stream analytics **e,** em seguida, selecione **Refresh**. O seu trabalho deve aparecer no âmbito **de trabalhos da Stream Analytics.**

    ![Lista de exploradores de servidores de streaming Analytics](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-list-jobs-01.png)

2. Expanda o nó de trabalho e clique duas vezes no nó **'Job View'** para abrir uma vista de emprego.
    
   ![Nó de emprego expandido](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-job-view-01.png)

### <a name="start-and-stop-jobs"></a>Iniciar e parar os trabalhos

Os trabalhos do Azure Stream Analytics podem ser geridos na íntegra a partir da vista de emprego no Visual Studio. Utilize os controlos para iniciar, parar ou apagar um trabalho.
    
   ![Controlos de emprego stream Analytics](./media/stream-analytics-vs-tools/azure-stream-analytics-job-view-controls.png)

## <a name="check-job-results"></a>Ver resultados do trabalho

As ferramentas Stream Analytics para Visual Studio suportam atualmente a pré-visualização da saída para o armazenamento de Azure Data Lake e para o armazenamento de bolhas. Para ver o resultado, basta clicar duas vezes no nó de saída do diagrama de trabalho em **Job View** e introduzir as credenciais apropriadas.

   ![Stream Analytics produto blob de trabalho](./media/stream-analytics-vs-tools/stream-analytics-blob-preview.png)

## <a name="export-jobs-to-a-project"></a>Exportar postos de trabalho para um projeto

Há duas maneiras de exportar um emprego existente para um projeto.

1. No **Server Explorer**, sob o nó Stream Analytics Jobs, clique com o nó de trabalho à direita. Selecione **Export to New Stream Analytics Project**.
    
   ![Trabalho de exportação para projeto](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-01.png)
    
    O projeto gerado aparece no **Solution Explorer.**
    
   ![Explorador de soluções](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-02.png)

2. Na visão de emprego, **selecione Gerar Projeto**.
    
   ![Gerar projeto a partir da vista de emprego](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="test-connections"></a>Ligações de teste

As ligações de entrada e saída podem ser testadas a partir do **Ponto de Vista de Trabalho** selecionando uma opção a partir do **dropdown de ligação** de teste.

   ![Queda da ligação de teste](./media/stream-analytics-vs-tools/stream-analytics-test-connection-dropdown.png)

Os resultados **da Ligação de Teste** são apresentados na janela de **saída.**

   ![Resultados da ligação de teste](./media/stream-analytics-vs-tools/stream-analytics-test-connection-results.png)

## <a name="next-steps"></a>Próximos passos

* [Monitorize e gere os trabalhos do Azure Stream Analytics utilizando o Visual Studio](stream-analytics-monitor-jobs-use-vs.md)
* [Quickstart: Criar um trabalho stream analytics usando o Visual Studio](stream-analytics-quick-create-vs.md)
* [Integrar e desenvolver continuamente as ferramentas do Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)
