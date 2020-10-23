---
title: Instalar as Ferramentas do Azure Data Lake para Visual Studio
description: Este artigo descreve como instalar ferramentas do Lago de Dados Azure para o Estúdio Visual.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 08/30/2019
ms.openlocfilehash: cb0183ae229c328588a31c2c0549e7e93fd19b78
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221048"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Instalar as Ferramentas do Data Lake para Visual Studio

Aprenda a usar o Visual Studio para criar contas Azure Data Lake Analytics. Você pode definir empregos em [U-SQL](data-lake-analytics-u-sql-get-started.md) e submeter empregos ao serviço Data Lake Analytics. Para mais informações sobre a Data Lake Analytics, consulte [Descrição geral da Análise do Azure Data Lake](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* **Visual Studio**: são suportadas todas as edições exceto a Express.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* Versão 2.7.1 ou superior do **Microsoft Azure SDK para .NET**. Instale-a através do [instalador de plataforma Web](https://www.microsoft.com/web/downloads/platform.aspx).
* Uma conta **data lake analytics.** Para criar uma conta, consulte [Introdução ao Azure Data Lake Analytics com o portal do Azure](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio-2017-or-visual-studio-2019"></a>Instalar ferramentas do Lago de Dados Azure para o Visual Studio 2017 ou Visual Studio 2019

Azure Data Lake Tools for Visual Studio é suportado no Visual Studio 2017 15.3 ou mais tarde. A ferramenta faz parte das cargas de trabalho de armazenamento e processamento de **dados** e **de desenvolvimento do Azure.** Ative uma destas duas cargas de trabalho como parte da sua instalação do Visual Studio.

Ativar a carga de trabalho **de armazenamento e processamento de dados,** conforme mostrado:

![Ativar a carga de trabalho de armazenamento e processamento de dados](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-01.png)

Ativar a carga de trabalho de desenvolvimento do **Azure,** tal como mostrado:

![Selecione a carga de trabalho de desenvolvimento do Azure](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Instalar as Ferramentas do Azure Data Lake para Visual Studio 2013 e 2015

Descarregue e instale ferramentas de análise do [Microsoft Azure Data Lake e stream analytics para o Estúdio Visual. ](https://aka.ms/adltoolsvs) Após a instalação, o Visual Studio tem as seguintes alterações:

* O **nó**  >  **Azure** do Explorador do Servidor contém um nó **Data Lake Analytics.**
* O menu **Ferramentas** inclui um item do **Data Lake**.

## <a name="next-steps"></a>Passos seguintes

* Para registar informações de diagnóstico, consulte [aceder aos registos de diagnósticos do Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Para ver uma consulta mais complexa, consulte [Analisar registos de site através da Análise do Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
* Para utilizar a vista de execução do vértice, consulte [a vista de execução do vertex em ferramentas do lago de dados para estúdio visual](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
