---
title: 'Tutorial: Preparar dados para preparar um modelo preditivo em R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Na primeira parte desta série de tutoriais de três partes, irá preparar os dados a partir de uma base de dados SQL do Azure para formar um modelo preditivo em R com os serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 05/02/2019
ms.openlocfilehash: aa9c41ee34a50ab9b1409357bfe7d123166601bf
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978737"
---
# <a name="tutorial-prepare-data-to-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Preparar dados para preparar um modelo preditivo em R com os serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização)

Na primeira parte desta série de tutoriais de três partes, irá preparar os dados a partir de uma base de dados SQL do Azure para formar um modelo preditivo em R com os serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização).

Para esta série de tutoriais, imagine que é proprietário de uma empresa de aluguer de esqui e pretende prever o número de alugueres que terá uma data futura. Essas informações ajudarão a se preparar seu estoque, funcionários e instalações.

Neste artigo, ficará a saber como:

> [!div class="checklist"]
> * Importar uma base de dados de exemplo para uma base de dados SQL do Azure
> * Carregar os dados da base de dados SQL do Azure para um quadro de dados através da linguagem R
> * Preparar os dados por meio da identificação algumas colunas como categóricos

Na [parte dois](sql-database-tutorial-predictive-model-build-compare.md), ficará a saber como criar e utilizar vários modelos e, em seguida, escolher a mais precisos.

Na [parte três](sql-database-tutorial-predictive-model-deploy.md), irá aprender a armazenar o modelo numa base de dados e, em seguida, crie um procedimento armazenado que pode realizar predições com base nos dados de novo.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição do Azure - se não tiver uma subscrição do Azure, [criar uma conta](https://azure.microsoft.com/free/) antes de começar.

* Servidor de base de dados SQL do Azure com o Machine Learning Services habilitado - durante a pré-visualização pública, será de Microsoft carregar e ativar machine learning para seus bancos de dados novos ou existentes. Siga os passos em [Inscreva-se na pré-visualização](sql-database-machine-learning-services-overview.md#signup).

* Pacote de RevoScaleR - consulte [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) para conhecer as opções instalar este pacote localmente.

* Este tutorial utiliza o IDE de R - [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* Ferramenta de consulta SQL - este tutorial pressupõe que está a utilizar [Estúdio de dados do Azure](https://docs.microsoft.com/sql/azure-data-studio/what-is) ou [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Importar a base de dados de exemplo

O conjunto de dados de exemplo utilizado neste tutorial foi guardado para uma **. bacpac** ficheiro de cópia de segurança da base de dados que pode transferir e utilizar.

1. Transfira o ficheiro [TutorialDB.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bacpac).

1. Siga as indicações [importar um ficheiro BACPAC para criar uma base de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-import), com estes detalhes:

   * Importar a partir da **TutorialDB.bacpac** arquivo que baixou
   * Durante a pré-visualização pública, escolha o **vcore Gen5/da** configuração para a nova base de dados
   * Nomeie a nova base de dados "TutorialDB"

## <a name="load-the-data-into-a-data-frame"></a>Carregar os dados para um quadro de dados

Para utilizar os dados em R, irá carregar os dados da base de dados SQL do Azure para um quadro de dados (`rentaldata`).

Criar um novo ficheiro de RScript no r Studio e execute o seguinte script. Substitua **servidor**, **UID**, e **PWD** pelas suas informações de ligação.

```r
#Define the connection string to connect to the TutorialDB database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
               "; Database=TutorialDB",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");

#Get the data from the table
SQL_rentaldata <- RxSqlServerData(table = "dbo.rental_data", connectionString = connStr, returnDataFrame = TRUE);

#Import the data into a data frame
rentaldata <- rxImport(SQL_rentaldata);

#Take a look at the structure of the data and the top rows
head(rentaldata);
str(rentaldata);
```

Deverá ver resultados semelhantes ao seguinte.

```results
   Year  Month  Day  RentalCount  WeekDay  Holiday  Snow
1  2014    1     20      445         2        1      0
2  2014    2     13       40         5        0      0
3  2013    3     10      456         1        0      0
4  2014    3     31       38         2        0      0
5  2014    4     24       23         5        0      0
6  2015    2     11       42         4        0      0
'data.frame':       453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : num  2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : int  1 0 0 0 0 0 0 0 0 0 ...
$ Snow       : num  0 0 0 0 0 0 0 0 0 0 ...
```

## <a name="prepare-the-data"></a>Preparar os dados

Nesta base de dados de exemplo, a maioria da preparação já foi feito, mas fará uma preparação mais aqui.
Utilize o seguinte script de R para identificar três colunas como *categorias* alterando os tipos de dados para *fator*.

```r
#Changing the three factor columns to factor types
rentaldata$Holiday <- factor(rentaldata$Holiday);
rentaldata$Snow    <- factor(rentaldata$Snow);
rentaldata$WeekDay <- factor(rentaldata$WeekDay);

#Visualize the dataset after the change
str(rentaldata);
```

Deverá ver resultados semelhantes ao seguinte.

```results
data.frame':      453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : Factor w/ 7 levels "1","2","3","4",..: 2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : Factor w/ 2 levels "0","1": 2 1 1 1 1 1 1 1 1 1 ...
$ Snow       : Factor w/ 2 levels "0","1": 1 1 1 1 1 1 1 1 1 1 ...
```

Os dados está agora preparados para treinamento.

## <a name="clean-up-resources"></a>Limpar recursos

Se não pretender continuar com este tutorial, elimine a base de dados TutorialDB do seu servidor de base de dados do Azure SQL.

No portal do Azure, siga estes passos:

1. No menu esquerdo no portal do Azure, selecione **todos os recursos** ou **bases de dados SQL**.
1. Na **filtrar por nome...**  , insira **TutorialDB**e selecione a sua subscrição.
1. Selecione a sua base de dados TutorialDB.
1. Na página **Descrição geral**, selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Na primeira parte desta série de tutoriais, concluiu estes passos:

* Importar um ficheiro de cópia de segurança da base de dados para uma base de dados SQL do Azure
* Carregar os dados da base de dados SQL do Azure para um quadro de dados através da linguagem R
* Preparar os dados por meio da identificação algumas colunas como categorias

Para criar um modelo que utiliza dados da base de dados TutorialDB machine learning, siga a parte dois desta série de tutoriais:

> [!div class="nextstepaction"]
> [Tutorial: Criar um modelo preditivo em R com os serviços de aprendizagem de máquina de base de dados de SQL do Azure (pré-visualização)](sql-database-tutorial-predictive-model-build-compare.md)
