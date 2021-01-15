---
title: Parametrizar serviços ligados na Azure Data Factory
description: Saiba como parametrizar os serviços ligados na Azure Data Factory e passar valores dinâmicos no tempo de execução.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2021
author: dcstwh
ms.author: weetok
manager: anandsub
ms.openlocfilehash: e463328df195b5a91db8ce272d138f980d000e79
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2021
ms.locfileid: "98232068"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Parametrizar serviços ligados na Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Agora pode parametrizar um serviço ligado e passar valores dinâmicos no tempo de execução. Por exemplo, se pretender ligar-se a diferentes bases de dados no mesmo servidor lógico SQL, pode agora parametrizar o nome da base de dados na definição de serviço ligada. Isto impede-o de criar um serviço ligado para cada base de dados no servidor lógico SQL. Também é possível parametrizar outras propriedades na definição de serviço ligada - por exemplo, *o nome do utilizador.*

Pode utilizar a UI da Fábrica de Dados no portal Azure ou uma interface de programação para parametrizar os serviços ligados.

> [!TIP]
> Recomendamos que não parametrize palavras-passe ou segredos. Em vez disso, guarde todas as cordas de conexão no Cofre da Chave Azure e parametize o *Nome Secreto*.

Para uma introdução e demonstração de sete minutos desta funcionalidade, veja o seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-linked-service-types"></a>Tipos de serviço ligados suportados

Pode parametrizar qualquer tipo de serviço ligado.
Ao autorizar o serviço ligado na UI, a Data Factory fornece experiência de parametrização incorporada para os seguintes tipos de serviços ligados. Na lâmina de criação/edição de serviços ligados, pode encontrar opções para novos parâmetros e adicionar conteúdo dinâmico.

- Amazon Redshift
- Amazon S3
- Azure Cosmos DB (SQL API)
- Base de Dados do Azure para MySQL
- Azure Databricks
- Azure Key Vault
- Base de Dados SQL do Azure
- Instância Gerida do SQL do Azure
- Azure Synapse Analytics 
- MySQL
- Oracle
- SQL Server
- HTTP genérico
- REST genérico

Para outros tipos de serviços ligados que não estejam na lista acima, pode parametrizar o serviço ligado editando o JSON na UI:

- Na criação/edição de serviços ligados - > expandir "Advanced" na parte inferior -> verificar "Especificar conteúdo dinâmico no formato JSON" -> especificar a carga útil do serviço ligado JSON. 
- Ou, depois de criar um serviço ligado sem parametrização, no [centro de gestão](author-visually.md#management-hub) -> serviços Linked -> encontrar o serviço ligado específico -> clique em "Code" (botão {} " para editar o JSON. 

Consulte a [amostra JSON](#json) para adicionar ` parameters` secção para definir parâmetros e referenciar o parâmetro utilizando ` @{linkedService().paraName} ` .

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
