---
title: 'Tutorial: Prepare dados para treinar um modelo preditivo em R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Na primeira parte desta série tutorial em três partes, irá preparar os dados de uma base de dados Azure SQL para formar um modelo preditivo em R com o Azure SQL Database Machine Learning Services (pré-visualização).
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
ms.date: 07/26/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a82467a097c50314e8f26f4a5cc4507f867ad504
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053772"
---
# <a name="tutorial-prepare-data-to-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Prepare dados para treinar um modelo preditivo em R com serviços de machine learning de base de dados Azure SQL (pré-visualização)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Na primeira parte desta série tutorial em três partes, você vai importar e preparar dados de uma base de dados Azure SQL usando R. Mais tarde nesta série, utilizará estes dados para treinar e implementar um modelo de aprendizagem automática preditiva em R com o Azure SQL Database Machine Learning Services (pré-visualização).

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

Para esta série tutorial, imagine que possui um negócio de aluguer de esqui e quer prever o número de alugueres que terá numa data futura. Esta informação irá ajudá-lo a preparar as suas ações, pessoal e instalações.

Nas partes um e dois desta série, desenvolverá alguns scripts R no RStudio para preparar os seus dados e treinar um modelo de aprendizagem automática. Depois, na terceira parte, executarás os scripts R dentro de uma base de dados SQL usando procedimentos armazenados.

Neste artigo, aprenderá a:

> [!div class="checklist"]
>
> * Importar uma base de dados de amostras numa base de dados Azure SQL utilizando R
> * Carregue os dados da base de dados Azure SQL num quadro de dados R
> * Prepare os dados em R identificando algumas colunas como categóricas

Na [segunda parte,](predictive-model-build-compare-tutorial.md)aprenderá a criar e treinar vários modelos de aprendizagem automática em R, e depois escolherá o mais preciso.

Na [terceira parte,](predictive-model-deploy-tutorial.md)aprenderá a armazenar o modelo numa base de dados e, em seguida, criará procedimentos armazenados a partir dos scripts R que desenvolveu nas partes um e dois. Os procedimentos armazenados serão executados numa base de dados SQL para fazer previsões com base em novos dados.

## <a name="prerequisites"></a>Pré-requisitos

* Subscrição Azure - Se não tiver uma subscrição Azure, [crie uma conta](https://azure.microsoft.com/free/) antes de começar.

* [Base de dados Azure SQL com Serviços de Aprendizagem Automática (com R)](machine-learning-services-overview.md) ativados.

* Pacote RevoScaleR - Consulte [o RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) para obter opções para instalar este pacote localmente.

* R IDE - Este tutorial utiliza [o RStudio Desktop](https://www.rstudio.com/products/rstudio/download/).

* Ferramenta de consulta SQL - Este tutorial assume que está a usar o [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) ou [o SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Importar a base de dados da amostra

O conjunto de dados da amostra utilizado neste tutorial foi guardado num ficheiro de backup da base de dados **.bacpac** para que possa descarregar e utilizar.

1. Faça o download do ficheiro [TutorialDB.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bacpac).

1. Siga as instruções em Importar um ficheiro BACPAC para criar uma base de [dados Azure SQL,](https://docs.microsoft.com/azure/sql-database/sql-database-import)utilizando estes detalhes:

   * Importar a partir do ficheiro **TutorialDB.bacpac** que descarregou
   * Durante a pré-visualização pública, escolha a configuração **Gen5/vCore** para a nova base de dados
   * Nomeie a nova base de dados "TutorialDB"

## <a name="load-the-data-into-a-data-frame"></a>Carregue os dados num quadro de dados

Para utilizar os dados em R, irá carregar os dados da base de dados Azure SQL num quadro de dados `rentaldata` ().

Crie um novo ficheiro RScript no RStudio e execute o seguinte script. Substitua o **Servidor,** **o UID**e o **PWD** com as suas próprias informações de ligação.

```r
#Define the connection string to connect to the TutorialDB database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Logical SQL server>",
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

Deve ver resultados semelhantes aos seguintes.

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

Nesta base de dados de amostras, a maior parte da preparação já foi feita, mas você fará mais uma preparação aqui.
Utilize o seguinte script R para identificar três colunas como *categorias,* alterando os tipos de dados para *fator*.

```r
#Changing the three factor columns to factor types
rentaldata$Holiday <- factor(rentaldata$Holiday);
rentaldata$Snow    <- factor(rentaldata$Snow);
rentaldata$WeekDay <- factor(rentaldata$WeekDay);

#Visualize the dataset after the change
str(rentaldata);
```

Deve ver resultados semelhantes aos seguintes.

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

Os dados estão agora preparados para o treino.

## <a name="clean-up-resources"></a>Limpar recursos

Se não vai continuar com este tutorial, elimine a base de dados tutorialDB do seu servidor.

A partir do portal Azure, siga estes passos:

1. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** ou bases de dados **SQL**.
1. No **campo Filter por nome...** introduza **tutorialDB,** e selecione a sua subscrição.
1. Selecione a sua base de dados TutorialDB.
1. Na página **Descrição geral**, selecione **Eliminar**.

## <a name="next-steps"></a>Próximos passos

Na primeira parte desta série tutorial, completou estes passos:

* Importar uma base de dados de amostras numa base de dados Azure SQL utilizando R
* Carregue os dados da base de dados Azure SQL num quadro de dados R
* Prepare os dados em R identificando algumas colunas como categóricas

Para criar um modelo de machine learning que utilize dados da base de dados tutorialDB, siga a segunda parte desta série tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Criar um modelo preditivo em R com Serviços de Machine Learning de Base de Dados Azure SQL (pré-visualização)](predictive-model-build-compare-tutorial.md)
