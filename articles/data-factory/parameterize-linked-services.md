---
title: Parametrizar serviços ligados na Azure Data Factory
description: Saiba como parametrizar os serviços ligados na Azure Data Factory e passar valores dinâmicos no tempo de execução.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/18/2020
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 85689661e7f0d170cd88edde8985f46285e679c6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84987786"
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
