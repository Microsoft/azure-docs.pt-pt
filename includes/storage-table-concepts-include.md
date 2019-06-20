---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 042aedf1a043cd89d74ff099554642d38a3c7dd3
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184006"
---
## <a name="what-is-table-storage"></a>O que é o armazenamento de Tabelas
O armazenamento de Tabelas do Azure armazena grandes quantidades de dados estruturados. O serviço é um arquivo de dados NoSQL que aceita chamadas autenticadas de dentro e de fora da nuvem do Azure. As tabelas do Azure são ideais para armazenar dados estruturados não relacionais. Utilizações comuns do Armazenamento de Tabelas:

* Armazenamento de TBs de dados estruturados com capacidade para servirem aplicações de dimensionamento da Web
* Armazenamento de conjuntos de dados que não precisam de associações complexas, chaves externas ou procedimentos armazenados e podem ser desnormalizadas para acesso rápido
* Consulta rápida de dados com um índice em cluster
* Acesso a dados com o protocolo OData e consultas LINQ com Bibliotecas de .NET do Serviço de Dados WCF

Pode utilizar o armazenamento de Tabelas para armazenar e consultar conjuntos enormes de dados estruturados não relacionais. As suas tabelas serão dimensionadas à medida que aumenta a procura.

## <a name="table-storage-concepts"></a>Conceitos de armazenamento de tabelas
O armazenamento de tabelas contém os seguintes componentes:

![diagrama de componente de armazenamento de tabelas][Table1]

* **Formato de URL:** Contas de armazenamento de tabelas do Azure, utilize este formato: `http://<storage account>.table.core.windows.net/<table>`

  As Contas da API de Tabela do Azure Cosmos DB utilizam este formato: `http://<storage account>.table.cosmosdb.azure.com/<table>`  

  Pode também endereçar diretamente as tabelas do Azure utilizando este endereço com o protocolo OData. Para obter mais informações, veja [OData.org][OData.org].
* **Contas:** Todos os acessos ao armazenamento do Azure é feito através de uma conta de armazenamento. Veja [Metas de Desempenho e Escalabilidade do Storage do Azure](../articles/storage/common/storage-scalability-targets.md) para obter detalhes acerca da capacidade das contas de armazenamento. 

    Todos os acessos ao Azure Cosmos DB são realizados através de uma conta de API de Tabela. Veja [Criar uma conta da API de Tabela](../articles/cosmos-db/create-table-dotnet.md#create-a-database-account) para mais detalhes sobre a criação de uma conta de API de Tabela.
* **tabela**: Uma tabela é uma coleção de entidades. As tabelas não impõem um esquema a entidades, o que significa que uma única tabela pode conter entidades que tenham conjuntos diferentes de propriedades.  
* **Entidade**: Uma entidade é um conjunto de propriedades, semelhantes a uma linha de base de dados. Uma entidade no Armazenamento do Azure pode ter até 1 MB de tamanho. Uma entidade no Azure Cosmos DB pode ter até 2 MB de tamanho.
* **Propriedades**: Uma propriedade é um par nome / valor. Cada entidade pode incluir até 252 propriedades para armazenar dados. Cada entidade tem também três propriedades do sistema que especificam uma chave de partição, uma chave de linha e um carimbo de data/hora. As entidades com a mesma chave de partição podem ser consultadas mais rapidamente e inseridas/atualizadas em operações atómicas. A chave de linha de uma entidade é o seu identificador exclusivo dentro de uma partição.

Para obter detalhes sobre a nomenclatura das tabelas e das propriedades, veja [Noções Básicas sobre o Modelo de Dados do Serviço Tabela](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

[Table1]: ./media/storage-table-concepts-include/table1.png
[OData.org]: http://www.odata.org/
