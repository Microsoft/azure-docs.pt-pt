---
title: Instalar as Ferramentas do Azure Data Lake para Visual Studio
description: Este artigo descreve como instalar o Ferramentas do Azure Data Lake para Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: ad8a6992-02c7-47d4-a108-62fc5a0777a3
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 08f0456b6a8b663f0af68fbb179de14b9e3acf79
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914053"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Instalar as Ferramentas do Data Lake para Visual Studio

Saiba como usar o Visual Studio para criar contas de Azure Data Lake Analytics. Você pode definir trabalhos no [U-SQL](data-lake-analytics-u-sql-get-started.md) e enviar trabalhos para o serviço de data Lake Analytics. Para mais informações sobre a Data Lake Analytics, consulte [Descrição geral da Análise do Azure Data Lake](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Visual Studio**: Todas as edições, exceto Express, têm suporte.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* Versão 2.7.1 ou superior do **Microsoft Azure SDK para .NET**. Instale-a através do [instalador de plataforma Web](https://www.microsoft.com/web/downloads/platform.aspx).
* A conta do **Data Lake Analytics**. Para criar uma conta, consulte [Introdução ao Azure Data Lake Analytics com o portal do Azure](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio-2017-or-visual-studio-2019"></a>Instalar o Ferramentas do Azure Data Lake para Visual Studio 2017 ou o Visual Studio 2019

Ferramentas do Azure Data Lake para Visual Studio tem suporte no Visual Studio 2017 15,3 ou posterior. A ferramenta faz parte das cargas de trabalho de **processamento e armazenamento de dados** e de **desenvolvimento do Azure** . Ative uma destas duas cargas de trabalho como parte da sua instalação do Visual Studio.

Habilite o **armazenamento de dados e** a carga de trabalho de processamento, conforme mostrado:

![Habilitar o armazenamento de dados e o processamento de carga de trabalho](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-01.png)

Habilite a carga de trabalho de **desenvolvimento do Azure** , conforme mostrado:

![Selecionar carga de trabalho de desenvolvimento do Azure](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Instalar as Ferramentas do Azure Data Lake para Visual Studio 2013 e 2015

Baixe e instale o [Microsoft Azure data Lake e as ferramentas de Stream Analytics para Visual Studio ](https://aka.ms/adltoolsvs). Após a instalação, o Visual Studio tem as seguintes alterações:

* O nó do **Server Explorer** > **Azure** contém um nó do **Data Lake Analytics**.
* O menu **Ferramentas** inclui um item do **Data Lake**.

## <a name="next-steps"></a>Passos Seguintes

* Para registrar em log informações de diagnóstico, consulte [acessando logs de diagnóstico para Azure data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Para ver uma consulta mais complexa, consulte [Analisar registos de site através da Análise do Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
* Para usar o modo de exibição de execução de vértice, consulte [usar o modo de exibição de execução de vértice em ferramentas de data Lake para Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
