---
title: Como implementar personalizado de sincronização para otimizar para elevada disponibilidade e desempenho no Azure Cosmos DB
description: Aprenda a implementar personalizado de sincronização para otimizar para elevada disponibilidade e desempenho no Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 2/12/2019
ms.author: mjbrown
ms.openlocfilehash: 9033a7502919c8dc05053048272f3fa62f81b31d
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56119292"
---
# <a name="how-to-implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Como implementar personalizado de sincronização para otimizar para elevada disponibilidade e desempenho

O Azure Cosmos DB oferece cinco níveis de consistência bem definidos para a sua escolha para equilibrar o compromisso entre disponibilidade, desempenho e consistência. Consistência forte garante que os dados são replicados de forma síncrona e durabilidade persistentes em cada região que a conta do Cosmos é configurada com. No entanto, esta configuração fornece ao nível mais elevado de durabilidade impacta o desempenho e disponibilidade. Se um aplicativo quiser a durabilidade de dados de acordo com a necessidade de aplicativo sem comprometer a disponibilidade de controlo/relaxe, podem ser empregadas personalizado de sincronização na camada da aplicação para alcançar o nível desejado de durabilidade.

O diagrama abaixo descreve visualmente o modelo personalizado de sincronização.

![Personalizado de sincronização](./media/how-to-custom-synchronization/custom-synchronization.png)

Neste cenário um contentor do Cosmos é replicado globalmente em várias regiões espalhadas por vários continentes. Usando a consistência forte para todas as regiões este cenário iria afetar o desempenho. Para garantir um nível mais elevado de durabilidade dos dados sem comprometer a latência de escrita, o aplicativo pode usar dois clientes que partilham o mesmo token de sessão.

O primeiro cliente possa escrever dados para a região local (por exemplo, e.u.a. Centro-Oeste). O segundo cliente (por exemplo, nos EUA Leste) é um cliente de leitura usado para garantir que a sincronização. Por token de sessão do fluxo da resposta de escrita para a leitura seguinte, a leitura irá garantir que a sincronização de escritas, EUA Leste. O Azure Cosmos DB garante escritas são visualizadas pelo menos uma região e são garantidas para sobreviver a uma falha regional, se a região de escrita original fosse diminuir. Neste cenário, cada gravação é sincronizada EUA leste, reduzindo a latência de emprego de consistência forte em todas as regiões. Num cenário de vários mestre, onde escritas estão a ocorrer em cada região, esse modelo pode ser estendido para sincronizar em várias regiões em paralelo.

## <a name="configure-the-clients"></a>Configurar os clientes

O exemplo abaixo mostra a camada de acesso a dados instanciar dois clientes para esta finalidade.

```csharp
class MyDataAccessLayer
{
    private DocumentClient writeClient;
    private DocumentClient readClient;

    public async Task InitializeAsync(Uri accountEndpoint, string key)
    {
        ConnectionPolicy writeConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp,
            UseMultipleWriteLocations = true
        };
        writeConnectionPolicy.SetCurrentLocation(LocationNames.WestUS);

        ConnectionPolicy readConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp
        };
        readConnectionPolicy.SetCurrentLocation(LocationNames.EastUS);

        writeClient = new DocumentClient(accountEndpoint, key, writeConnectionPolicy);
        readClient = new DocumentClient(accountEndpoint, key, readConnectionPolicy, ConsistencyLevel.Session);

        await Task.WhenAll(new Task[]
        {
            writeClient.OpenAsync(),
            readClient.OpenAsync()
        });
    }
}
```

## <a name="implement-custom-synchronization"></a>Implementar a sincronização personalizada

Assim que os clientes são inicializados, aplicação pode realizar escritas para a região local (E.u.a. oeste) e pode forçar sincronizar as gravações na região E.U. a leste da seguinte forma.

```csharp
class MyDataAccessLayer
{
    public async Task CreateItem(string containerLink, Document document)
    {
        ResourceResponse<Document> response = await writeClient.CreateDocumentAsync(
            containerLink, document);

        await readClient.ReadDocumentAsync(response.Resource.SelfLink,
            new RequestOptions { SessionToken = response.SessionToken });
    }
}
```

Esse modelo pode ser estendido para sincronizar em várias regiões em paralelo.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a distribuição global e consistência no Azure Cosmos DB, leia os artigos seguintes:

* [Escolher o nível certo de consistência no Azure Cosmos DB](consistency-levels-choosing.md)

* [Compromissos de consistência, disponibilidade e desempenho no Azure Cosmos DB](consistency-levels-tradeoffs.md)

* [Como gerir a consistência no Azure Cosmos DB](how-to-manage-consistency.md)

* [Distribuição de criação de partições e os dados no Azure Cosmos DB](partition-data.md)
