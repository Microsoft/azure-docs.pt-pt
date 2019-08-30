---
title: Exibir Azure Stream Analytics trabalhos no Visual Studio
description: Este artigo descreve como exibir e gerenciar Azure Stream Analytics trabalhos no Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: ae532ed19c2273e43aa739e84d5a68cadb717b86
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173015"
---
# <a name="use-visual-studio-to-view-azure-stream-analytics-jobs"></a>Usar o Visual Studio para exibir Azure Stream Analytics trabalhos

As ferramentas de Azure Stream Analytics para o Visual Studio facilitam para os desenvolvedores o gerenciamento de seus trabalhos de Stream Analytics diretamente do IDE. Com as ferramentas de Azure Stream Analytics, você pode:
- [Criar novos trabalhos](stream-analytics-quick-create-vs.md)
- Iniciar, parar e [monitorar trabalhos](stream-analytics-monitor-jobs-use-vs.md)
- Verificar resultados do trabalho
- Exportar trabalhos existentes para um projeto
- Testar conexões de entrada e saída
- [Executar consultas localmente](stream-analytics-vs-tools-local-run.md)

Saiba como [instalar o Azure Stream Analytics Tools para Visual Studio](stream-analytics-tools-for-visual-studio-install.md).

## <a name="explore-the-job-view"></a>Explorar a exibição do trabalho

Você pode usar a exibição de trabalho para interagir com Azure Stream Analytics trabalhos do Visual Studio.

### <a name="open-the-job-view"></a>Abrir a exibição do trabalho

1. Em **Gerenciador de servidores**, selecione **Stream Analytics trabalhos** e, em seguida, selecione **Atualizar**. Seu trabalho deve aparecer em **trabalhos de Stream Analytics**.

    ![Lista do Gerenciador de servidores Stream Analytics](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-list-jobs-01.png)



2. Expanda o nó do trabalho e clique duas vezes no nó **exibição do trabalho** para abrir um modo de exibição de trabalho.
    
   ![Nó de trabalho expandido](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-job-view-01.png)

### <a name="start-and-stop-jobs"></a>Iniciar e parar trabalhos

Azure Stream Analytics trabalhos podem ser totalmente gerenciados na exibição de trabalho no Visual Studio. Use os controles para iniciar, parar ou excluir um trabalho.
    
   ![Stream Analytics controles de trabalho](./media/stream-analytics-vs-tools/azure-stream-analytics-job-view-controls.png)


## <a name="check-job-results"></a>Verificar resultados do trabalho

O Stream Analytics Tools para Visual Studio atualmente dá suporte à visualização de saída para Azure Data Lake Storage e armazenamento de BLOBs. Para exibir o resultado, basta clicar duas vezes no nó de saída do diagrama de trabalho no **modo de exibição de trabalho** e inserir as credenciais apropriadas.

   ![Saída Stream Analytics blob de trabalho](./media/stream-analytics-vs-tools/stream-analytics-blob-preview.png)


## <a name="export-jobs-to-a-project"></a>Exportar trabalhos para um projeto

Há duas maneiras de exportar um trabalho existente para um projeto do.

1. No **Gerenciador de servidores**, no nó trabalhos do Stream Analytics, clique com o botão direito do mouse no nó do trabalho. Selecione **exportar para novo Stream Analytics projeto**.
    
   ![Exportar trabalho para o projeto](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-01.png)
    
    O projeto gerado aparece no **Gerenciador de soluções**.
    
   ![Gerenciador de soluções](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-02.png)

2. Na exibição de trabalho, selecione **gerar projeto**.
    
   ![Gerar projeto do modo de exibição de trabalho](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="test-connections"></a>Testar conexões

As conexões de entrada e saída podem ser testadas na **exibição de trabalho** selecionando uma opção na lista suspensa **testar conexão** .

   ![Lista suspensa de conexão de teste](./media/stream-analytics-vs-tools/stream-analytics-test-connection-dropdown.png)

Os resultados de **conexão de teste** são exibidos na janela **saída** .

   ![Testar resultados da conexão](./media/stream-analytics-vs-tools/stream-analytics-test-connection-results.png)

## <a name="next-steps"></a>Passos seguintes

* [Monitorar e gerenciar trabalhos de Azure Stream Analytics usando o Visual Studio](stream-analytics-monitor-jobs-use-vs.md)
* [Quickstart: Criar um trabalho de Stream Analytics usando o Visual Studio](stream-analytics-quick-create-vs.md)
* [Tutorial: Implantar um trabalho de Azure Stream Analytics com CI/CD usando Azure Pipelines](stream-analytics-tools-visual-studio-cicd-vsts.md)
* [Integrar e desenvolver continuamente as ferramentas do Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)
