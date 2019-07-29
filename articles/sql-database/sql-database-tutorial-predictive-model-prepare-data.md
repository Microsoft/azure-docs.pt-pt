---
title: 'Tutorial: Preparar dados para treinar um modelo de previsão em R'
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Na parte um desta série de tutoriais de três partes, você irá preparar os dados de um banco de dado SQL do Azure para treinar um modelo de previsão em R com o banco de dados SQL do Azure Serviços de Machine Learning (versão prévia).
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
ms.openlocfilehash: c1271d5b63fa796fe44b7a40c364953464a87539
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596663"
---
# <a name="tutorial-prepare-data-to-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Tutorial: Preparar dados para treinar um modelo de previsão em R com o banco de dados SQL do Azure Serviços de Machine Learning (versão prévia)

Na parte um desta série de tutoriais de três partes, você importará e preparará os dados de um Azure SQL Database usando o R. Posteriormente nesta série, você usará esses dados para treinar e implantar um modelo de aprendizado de máquina preditiva em R com o banco de dados SQL do Azure Serviços de Machine Learning (versão prévia).

Para esta série de tutoriais, imagine que você tenha um negócio de aluguel de esqui e queira prever o número de locações que você terá em uma data futura. Essas informações ajudarão você a tornar suas ações, sua equipe e suas instalações prontas.

Nas partes um e dois desta série, você desenvolverá alguns scripts do R no RStudio para preparar seus dados e treinar um modelo de aprendizado de máquina. Em seguida, na parte três, você executará esses scripts R dentro de um banco de dados SQL usando procedimentos armazenados.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Importar um banco de dados de exemplo para um banco de dados SQL do Azure usando o R
> * Carregar os dados do banco de dados SQL do Azure em um quadro de data do R
> * Prepare os dados em R identificando algumas colunas como categóricas

Na [parte dois](sql-database-tutorial-predictive-model-build-compare.md), você aprenderá a criar e treinar vários modelos de aprendizado de máquina em R e, em seguida, escolher o mais preciso.

Na [terceira parte](sql-database-tutorial-predictive-model-deploy.md), você aprenderá a armazenar o modelo em um banco de dados e, em seguida, criar procedimentos armazenados dos scripts R desenvolvidos nas partes um e dois. Os procedimentos armazenados serão executados em um banco de dados SQL para fazer previsões com base em um novo dado.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure-se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/) antes de começar.

* Servidor de banco de dados SQL do Azure com Serviços de Machine Learning habilitado-durante a visualização pública, a Microsoft integrará você e habilitará o aprendizado de máquina para seus bancos de dados novos ou existentes. Siga as etapas em [inscrever-se para a versão prévia](sql-database-machine-learning-services-overview.md#signup).

* Pacote RevoScaleR – consulte [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) para obter opções para instalar esse pacote localmente.

* R IDE-este tutorial usa o [RStudio desktop](https://www.rstudio.com/products/rstudio/download/).

* Ferramenta de consulta SQL – este tutorial pressupõe que você esteja usando [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) ou o [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="import-the-sample-database"></a>Importar o banco de dados de exemplo

O conjunto de dados de exemplo usado neste tutorial foi salvo em um arquivo de backup do banco de dados **. bacpac** para você baixar e usar.

1. Baixe o arquivo [TutorialDB. bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bacpac).

1. Siga as instruções em [importar um arquivo BACPAC para criar um banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-import)usando estes detalhes:

   * Importar do arquivo **TutorialDB. bacpac** que você baixou
   * Durante a visualização pública, escolha a configuração **Gen5/vCore** para o novo banco de dados
   * Nomeie o novo banco de dados "TutorialDB"

## <a name="load-the-data-into-a-data-frame"></a>Carregar os dados em um quadro de dados

Para usar os dados em R, você carregará os dados do banco de dados SQL do Azure em um quadro`rentaldata`de dado ().

Crie um novo arquivo RScript no RStudio e execute o script a seguir. Substitua **Server**, **UID**e **pwd** pelas suas próprias informações de conexão.

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

Você deverá ver resultados semelhantes ao seguinte.

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

Neste banco de dados de exemplo, a maior parte da preparação já foi feita, mas você fará mais uma preparação aqui.
Use o script R a seguir para identificar três colunas como *categorias* , alterando os tipos de dados para *fator*.

```r
#Changing the three factor columns to factor types
rentaldata$Holiday <- factor(rentaldata$Holiday);
rentaldata$Snow    <- factor(rentaldata$Snow);
rentaldata$WeekDay <- factor(rentaldata$WeekDay);

#Visualize the dataset after the change
str(rentaldata);
```

Você deverá ver resultados semelhantes ao seguinte.

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

Os dados agora estão preparados para treinamento.

## <a name="clean-up-resources"></a>Limpar recursos

Se você não continuar com este tutorial, exclua o banco de dados TutorialDB do seu servidor de banco de dados SQL do Azure.

No portal do Azure, siga estas etapas:

1. No menu à esquerda na portal do Azure, selecione **todos os recursos** ou bancos de **dados SQL**.
1. No campo **Filtrar por nome...** , insira **TutorialDB**e selecione sua assinatura.
1. Selecione o banco de dados TutorialDB.
1. Na página **Descrição geral**, selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Na parte um desta série de tutoriais, você concluiu estas etapas:

* Importar um banco de dados de exemplo para um banco de dados SQL do Azure usando o R
* Carregar os dados do banco de dados SQL do Azure em um quadro de data do R
* Prepare os dados em R identificando algumas colunas como categóricas

Para criar um modelo de aprendizado de máquina que usa dados do banco de TutorialDB, siga a parte dois desta série de tutoriais:

> [!div class="nextstepaction"]
> [Tutorial: Criar um modelo de previsão em R com o banco de dados SQL do Azure Serviços de Machine Learning (versão prévia)](sql-database-tutorial-predictive-model-build-compare.md)
