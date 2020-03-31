---
title: Instalar as Ferramentas do Azure Data Lake para Visual Studio
description: Este artigo descreve como instalar ferramentas de lago de dados Azure para estúdio visual.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 08f0456b6a8b663f0af68fbb179de14b9e3acf79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70914053"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Instalar as Ferramentas do Data Lake para Visual Studio

Aprenda a usar o Visual Studio para criar contas Azure Data Lake Analytics. Pode definir empregos na [U-SQL](data-lake-analytics-u-sql-get-started.md) e submeter empregos ao serviço Data Lake Analytics. Para mais informações sobre a Data Lake Analytics, consulte [Descrição geral da Análise do Azure Data Lake](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Visual Studio**: são suportadas todas as edições exceto a Express.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* Versão 2.7.1 ou superior do **Microsoft Azure SDK para .NET**. Instale-a através do [instalador de plataforma Web](https://www.microsoft.com/web/downloads/platform.aspx).
* Uma conta **data lake analytics.** Para criar uma conta, consulte [Introdução ao Azure Data Lake Analytics com o portal do Azure](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio-2017-or-visual-studio-2019"></a>Instale ferramentas azure data lake para estúdio visual 2017 ou Estúdio Visual 2019

O Azure Data Lake Tools for Visual Studio é suportado no Visual Studio 2017 15.3 ou mais tarde. A ferramenta faz parte do **armazenamento e processamento** de dados e das cargas de trabalho de desenvolvimento do **Azure.** Ative uma destas duas cargas de trabalho como parte da sua instalação do Visual Studio.

Ativar a carga de trabalho de armazenamento e processamento de **dados,** como mostrado:

![Ativar a carga de trabalho de armazenamento e processamento de dados](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-01.png)

Ativar a carga de trabalho de desenvolvimento do **Azure,** como mostrado:

![Selecione carga de trabalho de desenvolvimento do Azure](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Instalar as Ferramentas do Azure Data Lake para Visual Studio 2013 e 2015

Descarregue e [instale ferramentas do Microsoft Azure Data Lake e Stream Analytics para estúdio visual ](https://aka.ms/adltoolsvs). Após a instalação, o Visual Studio tem as seguintes alterações:

* O nó **Server Explorer** > **Azure** contém um nó de **Analytics data Lake** Analytics.
* O menu **Ferramentas** inclui um item do **Data Lake**.

## <a name="next-steps"></a>Passos seguintes

* Para registar informações de diagnóstico, consulte aceder aos registos de [diagnóstico para o Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Para ver uma consulta mais complexa, consulte [Analisar registos de site através da Análise do Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
* Para utilizar a vista de execução do vértice, consulte Use a vista de [execução vertex em ferramentas](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)de data lake para estúdio visual .
