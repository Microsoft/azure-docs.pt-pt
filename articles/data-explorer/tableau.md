---
title: Utilize o conector Azure Data Explorer ODBC para visualizar os dados do Tableau
description: Neste artigo, aprende-se a utilizar uma ligação de conectividade de base de dados aberta (ODBC) à ligação Azure Data Explorer para visualizar dados com tableau.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 903daf450800a7f060899d736c2b31920c1b51f6
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562450"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Visualizar dados do Azure Data Explorer em Tableau

 [Tableau](https://www.tableau.com/) é uma plataforma de análise visual para inteligência empresarial. Para ligar ao Azure Data Explorer a partir de Tableau e trazer dados de um cluster de amostras, utilize o controlador de conectividade de base de dados aberta do Servidor SQL (ODBC). 

## <a name="prerequisites"></a>Pré-requisitos

Precisa do seguinte para completar este artigo:

* [Ligue-se ao Azure Data Explorer com o ODBC](connect-odbc.md) utilizando o controlador SQL Server ODBC, para ligar ao Azure Data Explorer a partir de Tableau. 

* Tableau Desktop, versão completa ou [experimental.](https://www.tableau.com/products/desktop/download)

* Um cluster que inclui os dados da amostra StormEvents. Para mais informações, consulte [Criar um cluster e base de dados do Azure Data Explorer e](create-cluster-database-portal.md) ingerir dados de [amostras no Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Visualizar dados em Tableau 

Assim que terminar de configurar a ODBC, pode trazer dados da amostra para o Tableau.

1. No Tableau Desktop, no menu esquerdo, selecione **Outras Bases de Dados (ODBC)** .

    ![Entrar em contacto com o ODBC](media/tableau/connect-odbc.png)

1. Para **dSN,** selecione a fonte de dados criada para o ODBC e, em seguida, selecione **Iniciar**sessão .

    ![Sign-in da ODBC](media/tableau/odbc-sign-in.png)

1. Para **a Base de Dados,** selecione a base de dados do seu cluster de amostras, como *testDatabase*. Para **Schema**, selecione *dbo*, e para **tabela**, selecione a tabela de amostras *StormEvents.*

    ![Selecione base de dados e tabela](media/tableau/select-database-table.png)

1. Tableau mostra agora o esquema para os dados da amostra. Selecione **Update Now** para trazer os dados para tableau.

    ![Atualizar dados](media/tableau/update-data.png)

    Quando os dados são importados, o Tableau mostra linhas de dados semelhantes à seguinte imagem.

    ![Conjunto de resultados](media/tableau/result-set.png)

1. Agora pode criar visualizações em Tableau com base nos dados que trouxe do Azure Data Explorer. Para mais informações, consulte [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>Passos seguintes

* [Escrever consultas do Azure Data Explorer](write-queries.md)