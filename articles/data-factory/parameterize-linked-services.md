---
title: Parametrizar os serviços ligados no Azure Data Factory | Documentos da Microsoft
description: Saiba como parametrizar serviços ligados no Azure Data Factory e passar valores dinâmicos em tempo de execução.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/18/2018
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: 0239c53f98fba201b6d70e1e2212eea36134e30d
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57574632"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Parametrizar os serviços ligados no Azure Data Factory

Agora pode parametrizar um serviço ligado e passar valores dinâmicos em tempo de execução. Por exemplo, se pretender ligar a bases de dados diferentes no mesmo servidor de base de dados do Azure SQL, agora é possível parametrizar o nome de base de dados na definição do serviço ligado. Isso evita a necessidade de criar um serviço ligado para cada base de dados no servidor de base de dados SQL do Azure. É possível parametrizar outras propriedades na definição do serviço ligado, também - por exemplo, *nome de utilizador.*

Pode utilizar a IU do Data Factory no Portal do Azure ou uma interface de programação parametrizar serviços ligados.

> [!TIP]
> Recomendamos que não para parametrizar senhas ou segredos. Store em vez disso, todas as cadeias de ligação no Azure Key Vault e parametrizar os *nome do segredo*.

Para obter uma introdução de sete minutos e demonstração desta funcionalidade, veja o vídeo seguinte:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Armazena os dados suportados

Neste momento, a parametrização de serviço ligado é suportada na IU do Data Factory no portal do Azure para os seguintes arquivos de dados. Para todos os outros arquivos de dados, é possível parametrizar o serviço ligado ao selecionar o **código** ícone na **ligações** guia e utilizar o editor de JSON.
- Base de Dados SQL do Azure
- Armazém de Dados SQL do Azure
- SQL Server
- Oracle
- Cosmos DB
- Amazon Redshift
- MySQL
- Base de Dados do Azure para MySQL

## <a name="data-factory-ui"></a>IU do Data Factory

![Adicionar conteúdo dinâmico para a definição de serviço ligado](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![Criar um novo parâmetro](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "value": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
                "type": "SecureString"
            }
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
