---
title: Parametrizar serviços vinculados no Azure Data Factory
description: Saiba como parametrizar os serviços vinculados em Azure Data Factory e passar valores dinâmicos em tempo de execução.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/18/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 0d8418d846d26d4104718df6d0fc66d264ef4a54
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74918836"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Parametrizar serviços vinculados no Azure Data Factory

Agora você pode parametrizar um serviço vinculado e passar valores dinâmicos em tempo de execução. Por exemplo, se você quiser se conectar a bancos de dados diferentes no mesmo servidor de banco de dados SQL do Azure, agora você pode parametrizar o nome do banco de dados na definição de serviço vinculado. Isso impede que você precise criar um serviço vinculado para cada banco de dados no servidor de banco de dados SQL do Azure. Você pode parametrizar outras propriedades na definição de serviço vinculado também, por exemplo, *nome de usuário.*

Você pode usar a interface do usuário do Data Factory no portal do Azure ou uma interface de programação para parametrizar os serviços vinculados.

> [!TIP]
> Recomendamos não parametrizar senhas ou segredos. Armazene todas as cadeias de conexão em Azure Key Vault em vez disso e parametrizar o *nome do segredo*.

Para uma introdução e uma demonstração desse recurso de sete minutos, Assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Arquivos de dados suportados

Neste momento, há suporte para a parametrização do serviço vinculado na interface do usuário do Data Factory no portal do Azure para os repositórios de dados a seguir. Para todos os outros armazenamentos de dados, você pode parametrizar o serviço vinculado selecionando o ícone de **código** na guia **conexões** e usando o editor de JSON.
- Base de Dados SQL do Azure
- Armazém de Dados SQL do Azure
- SQL Server
- Oracle
- Cosmos DB
- Amazon Redshift
- MySQL
- Base de Dados do Azure para MySQL

## <a name="data-factory-ui"></a>IU do Data Factory

![Adicionar conteúdo dinâmico à definição de serviço vinculado](media/parameterize-linked-services/parameterize-linked-services-image1.png)

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
