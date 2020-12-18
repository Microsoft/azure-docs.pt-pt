---
title: Copiar ou apoiar trabalhos de Azure Stream Analytics
description: Este artigo descreve como copiar ou apoiar um trabalho do Azure Stream Analytics.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/11/2019
ms.openlocfilehash: 67e28e8c5092f2b52a3a34053f81d8a00afb24ed
ms.sourcegitcommit: e0ec3c06206ebd79195d12009fd21349de4a995d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97683226"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>Copiar ou apoiar trabalhos de Azure Stream Analytics

Pode copiar ou fazer cópias de segurança dos seus trabalhos Azure Stream Analytics usando Visual Studio Code ou Visual Studio. Copiar um trabalho para outra região não copia o último tempo de saída. Portanto, não pode utilizar [**A última**](./start-job.md#start-options) opção interrompida ao iniciar o trabalho copiado.

## <a name="before-you-begin"></a>Antes de começar
* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

* Inicie sessão no [portal do Azure](https://portal.azure.com/).

* Instale [a extensão Azure Stream Analytics para](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension) ferramentas Visual Studio Code ou [Azure Stream Analytics para o Visual Studio](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension).  

## <a name="visual-studio-code"></a>Visual Studio Code

1. Clique no ícone **Azure** na Barra de Atividade do Código do Estúdio Visual e, em seguida, expanda o nó **Stream Analytics.** Os seus trabalhos devem aparecer sob as suas assinaturas.

   ![Explorador de analítica de fluxo aberto](./media/vscode-explore-jobs/open-explorer.png)

2. Para exportar um emprego para um projeto local, localize o trabalho que deseja exportar no **Stream Analytics Explorer** em Visual Studio Code. Em seguida, selecione uma pasta para o seu projeto.

    ![Localizar trabalho asa em Código de Estúdio Visual](./media/vscode-explore-jobs/export-job.png)

    O projeto é exportado para a pasta que seleciona e adicionado ao seu espaço de trabalho atual.

3. Para publicar o trabalho noutra região ou cópia de segurança utilizando outro nome, **selecione Selecione das suas subscrições para publicar** no editor de consulta \* (.asaql) e siga as instruções.

    ![Publicar para Azure em Código de Estúdio Visual](./media/quick-create-visual-studio-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. Siga a [exportação de um trabalho Azure Stream Analytics implantado para uma instrução do projeto](./stream-analytics-vs-tools.md#export-jobs-to-a-project).

2. Abra o \* ficheiro .asaql no Editor de Consulta, **selecione Submeter-se ao Azure** no editor de scripts e siga as instruções para publicar o trabalho para outra região ou cópia de segurança usando um novo nome.

## <a name="next-steps"></a>Próximos passos

* [Quickstart: Criar um trabalho stream analytics usando Código de Estúdio Visual](quick-create-visual-studio-code.md)
* [Quickstart: Criar um trabalho stream analytics usando o Visual Studio](stream-analytics-quick-create-vs.md)