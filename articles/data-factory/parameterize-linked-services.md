---
title: Parametrizar serviços ligados na Azure Data Factory
description: Saiba como parametrizar os serviços ligados na Azure Data Factory e passar valores dinâmicos no tempo de execução.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/21/2020
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: b3aadab1b4af80f98c57a279b69606a02846e996
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2020
ms.locfileid: "88716848"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Parametrizar serviços ligados na Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Agora pode parametrizar um serviço ligado e passar valores dinâmicos no tempo de execução. Por exemplo, se pretender ligar-se a diferentes bases de dados no mesmo servidor lógico SQL, pode agora parametrizar o nome da base de dados na definição de serviço ligada. Isto impede-o de criar um serviço ligado para cada base de dados no servidor lógico SQL. Também é possível parametrizar outras propriedades na definição de serviço ligada - por exemplo, *o nome do utilizador.*

Pode utilizar a UI da Fábrica de Dados no portal Azure ou uma interface de programação para parametrizar os serviços ligados.

> [!TIP]
> Recomendamos que não parametrize palavras-passe ou segredos. Em vez disso, guarde todas as cordas de conexão no Cofre da Chave Azure e parametize o *Nome Secreto*.

Para uma introdução e demonstração de sete minutos desta funcionalidade, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Arquivos de dados suportados

Neste momento, a parametrização do serviço ligado é suportada na UI da Fábrica de Dados para as seguintes lojas de dados. Para todas as outras lojas de dados, pode parametrizar o serviço ligado selecionando o ícone **Código** no separador **Ligações** e utilizando o editor JSON.

- Amazon Redshift
- Azure Cosmos DB (SQL API)
- Base de Dados do Azure para MySQL
- Base de Dados SQL do Azure
- Azure Synapse Analytics (anteriormente SQL DW)
- MySQL
- Oracle
- SQL Server
- HTTP genérico
- REST genérico

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
