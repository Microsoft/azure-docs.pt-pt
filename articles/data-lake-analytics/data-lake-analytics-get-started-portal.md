---
title: Criar & Azure Data Lake Analytics de consulta-portal do Azure
description: Utilize o portal do Azure para criar uma conta do Azure Data Lake Analytics e submeta uma tarefa de U-SQL.
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: b1584d16-e0d2-4019-ad1f-f04be8c5b430
ms.topic: conceptual
ms.date: 03/21/2017
ms.openlocfilehash: 46da3750e4d0ac78c5fd9df91ae37670e541302d
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315764"
---
# <a name="get-started-with-azure-data-lake-analytics-using-the-azure-portal"></a>Introdução ao Azure Data Lake Analytics com o portal do Azure
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Este artigo descreve como utilizar o portal do Azure para criar contas do Azure Data Lake Analytics, definir tarefas em [U-SQL](data-lake-analytics-u-sql-get-started.md) e submeter tarefas para o serviço Data Lake Analytics.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter uma **subscrição do Azure**. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-analytics-account"></a>Criar uma conta de Data Lake Analytics

Agora, você criará um Data Lake Analytics e uma conta de Azure Data Lake Storage Gen1 ao mesmo tempo.  Este passo é simples e leva apenas cerca de 60 segundos para concluir.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Clique em **Criar um recurso** >  **Dados + Análise** > **Data Lake Analytics**.
3. Selecione os valores para os seguintes itens:
   * **Nome**: Nomeie sua conta de Data Lake Analytics (apenas letras minúsculas e números permitidos).
   * **Subscrição**: Escolha a assinatura do Azure usada para a conta de análise.
   * **Grupo de Recursos**. Selecione um Grupo de Recursos do Azure existente ou crie um novo.
   * **Localização**. Selecione um centro de dados do Azure para a conta do Data Lake Analytics.
   * **Data Lake Storage Gen1**: Siga as instruções para criar uma nova conta de Data Lake Storage Gen1 ou selecione uma existente. 
4. Opcionalmente, selecione o escalão de preços para a sua conta do Data Lake Analytics.
5. Clique em **Criar**. 


## <a name="your-first-u-sql-script"></a>O seu primeiro script U-SQL

O texto apresentado em seguida é um script U-SQL muito simples. Tudo o que ele faz é definir um pequeno conjunto de pequenos dentro do script e, em seguida, gravar esse conjunto de um para `/data.csv`a conta de data Lake Storage Gen1 padrão como um arquivo chamado.

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

## <a name="submit-a-u-sql-job"></a>Submeter uma tarefa de U-SQL

1. Na conta do Data Lake Analytics, selecione **Nova Tarefa**.
2. Cole o texto do script U-SQL acima. Nome da tarefa. 
3. Selecione o botão **Submeter** para iniciar a tarefa.   
4. Monitorize o **Estado** da tarefa e aguarde até que o estado da tarefa se altere para **Com Êxito**.
5. Selecione o separador **Dados** e selecione o separador **Saídas**. Selecione o ficheiro de saída com o nome `data.csv` e veja os dados de saída.

## <a name="see-also"></a>Consulte também

* Para começar a desenvolver aplicações U-SQL, consulte [Desenvolver scripts SQL-U, utilizando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Para saber mais sobre U-SQL, consulte [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para tarefas de gestão, veja [Manage Azure Data Lake Analytics using Azure portal (Gerir o Azure Data Lake Analytics com o Portal do Azure)](data-lake-analytics-manage-use-portal.md).
