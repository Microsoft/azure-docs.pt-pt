---
title: Parametrizar serviços ligados na Fábrica de Dados azure
description: Saiba como parametrizar serviços ligados na Azure Data Factory e passar valores dinâmicos no tempo de funcionao.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/18/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: acc7284eb607d20ca1d62b478d802be56048bc6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75440101"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Parametrizar serviços ligados na Fábrica de Dados azure

Agora pode parametrizar um serviço ligado e passar valores dinâmicos no tempo de execução. Por exemplo, se pretender ligar-se a diferentes bases de dados no mesmo servidor de base de dados Azure SQL, pode agora parametrizar o nome da base de dados na definição de serviço ligada. Isto impede que tenha de criar um serviço ligado para cada base de dados no servidor de base de dados Azure SQL. Também pode parametificar outras propriedades na definição de serviço ligada - por exemplo, *o nome do utilizador.*

Pode utilizar a Data Factory UI no portal Azure ou uma interface de programação para parametrizar serviços ligados.

> [!TIP]
> Recomendamos que não parametilize senhas ou segredos. Guarde todas as cordas de ligação no Cofre de Chaves Azure e parametilize o *Nome Secreto*.

Para uma introdução de sete minutos e demonstração desta funcionalidade, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Arquivos de dados suportados

Neste momento, a parametrização de serviços ligados é suportada na Data Factory UI no portal Azure para as seguintes lojas de dados. Para todas as outras lojas de dados, pode parametrizar o serviço ligado selecionando o ícone **Código** no separador **Ligações** e utilizando o editor JSON.
- Base de Dados SQL do Azure
- Azure SQL Data Warehouse
- SQL Server
- Oracle
- Cosmos DB
- Amazon Redshift
- MySQL
- Base de Dados do Azure para MySQL

## <a name="data-factory-ui"></a>IU do Data Factory

![Adicione conteúdo dinâmico à definição de Serviço Linked](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![Criar um novo parâmetro](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
