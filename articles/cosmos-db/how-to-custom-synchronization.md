---
title: Como implementar personalizado de sincronização para otimizar para elevada disponibilidade e desempenho no Azure Cosmos DB
description: Aprenda a implementar personalizado de sincronização para otimizar para elevada disponibilidade e desempenho no Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: d948798f161eb36578cb679b6d96409917424fd4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61056145"
---
# <a name="how-to-implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Como implementar personalizado de sincronização para otimizar para elevada disponibilidade e desempenho

Azure Cosmos DB oferece [cinco níveis de consistência bem definidos](consistency-levels.md) à sua escolha de equilibrar o compromisso entre disponibilidade, desempenho e consistência. Consistência forte garante que os dados são replicados de forma síncrona e mantidos de maneira duradoura em cada região, onde a conta do Cosmos do Azure está disponível. Esta configuração, ao mesmo tempo, o nível mais elevado de durabilidade, é fornecido ao custo de desempenho e disponibilidade. Se um aplicativo quiser controle/relaxe a durabilidade de dados de acordo com o aplicativo precisa sem comprometer a disponibilidade, pode empregar *personalizado de sincronização* na camada da aplicação para alcançar o nível desejado de durabilidade.

O diagrama abaixo descreve visualmente o modelo personalizado de sincronização.

![Personalizado de sincronização](./media/how-to-custom-synchronization/custom-synchronization.png)

Neste cenário, um contentor do Cosmos do Azure é replicado globalmente em várias regiões espalhadas por vários continentes. Usando a consistência forte para todas as regiões este cenário iria afetar o desempenho. Para garantir um nível mais elevado de durabilidade dos dados sem comprometer a latência de escrita, o aplicativo pode usar dois clientes que partilham o mesmo [token de sessão](how-to-manage-consistency.md#utilize-session-tokens).

O primeiro cliente possa escrever dados para a região local (por exemplo, e.u.a. Centro-Oeste). O segundo cliente (por exemplo, nos EUA Leste) é um cliente de leitura usado para garantir que a sincronização. Por que flui o token de sessão da resposta de escrita para a leitura seguinte, a leitura irá garantir que a sincronização de escritas, EUA Leste. O Azure Cosmos DB garante escritas são visualizadas pelo menos uma região e são garantidas para sobreviver a uma falha regional, se a região de escrita original fosse diminuir. Neste cenário, cada gravação é sincronizada EUA leste, reduzindo a latência de emprego de consistência forte em todas as regiões. Num cenário de vários mestre, onde escritas estão a ocorrer em cada região, esse modelo pode ser estendido para sincronizar em várias regiões em paralelo.

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

## <a name="implement-custom-synchronization"></a>Implementar sincronização personalizada

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
