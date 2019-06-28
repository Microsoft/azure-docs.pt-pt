---
title: Trabalhar com o biblioteca de processador no Azure Cosmos DB do feed de alterações
description: Biblioteca processador do feed usando a alteração do Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: d0faeba5278e23990a72c9d2dd3d7e18510bdf80
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342045"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Processador de feed de alterações no Azure Cosmos DB 

O [biblioteca processador do feed de alterações do Azure Cosmos DB](sql-api-sdk-dotnet-changefeed.md) ajuda-o a distribuir o processamento de eventos por vários consumidores. Esta biblioteca simplifica as alterações de leitura em partições e de vários threads trabalhando em paralelo.

O principal benefício da biblioteca de processador do feed de alterações é que não precisa de gerir cada partição e o token de continuação e não tem que consultar cada contentor manualmente.

A biblioteca de processador do feed de alterações simplifica as alterações de leitura em partições e de vários threads trabalhando em paralelo. Gere automaticamente as alterações de leitura por usando um mecanismo de concessão de partições. Como pode ver na imagem seguinte, se iniciar dois clientes que utilizam o biblioteca processador do feed de alterações, elas dividem o trabalho entre si. À medida que continua a aumentar o número de clientes, eles continuar dividindo o trabalho entre si.

![Usar as alterações do Azure Cosmos DB biblioteca processador do feed](./media/change-feed-processor/change-feed-output.png)

O cliente do esquerda foi iniciado primeiro e ele começou a monitorização de todas as partições, em seguida, o segundo cliente foi iniciado e, em seguida, a primeira desistir de algumas das concessões para segundo cliente. Esta é uma forma eficiente para distribuir trabalho entre computadores diferentes e clientes.

Se tiver duas funções de Azure sem servidor o mesmo contentor de monitorização e utilizar a concessão do mesmo, as duas funções poderão obter documentos diferentes consoante a forma como a biblioteca de processador decide processar as partições.

## <a name="implementing-the-change-feed-processor-library"></a>Implementar a alteração de biblioteca processador do feed

Existem quatro componentes principais de implementar o biblioteca processador do feed de alterações: 

1. **O contentor monitorizado:** O contentor monitorizado tem os dados a partir do qual o feed de alterações é gerado. Qualquer inserções e as alterações para o contentor monitorizado são refletidas no feed de alterações do contentor.

1. **O contentor da concessão:** O contentor da concessão coordena o feed de alterações em várias funções de trabalho de processamento. Um contentor separado é utilizado para armazenar as concessões com uma concessão por partição. É vantajoso para armazenar este contentor de concessão numa conta diferente com a região de escrita mais perto de onde está a executar o processador do feed de alterações. Um objeto de concessão contém os seguintes atributos:

   * Proprietário: Especifica o host que detém a concessão.

   * Continuação: Especifica a posição (token de continuação) na alteração do feed para uma determinada partição.

   * Timestamp: Hora da última concessão foi atualizada. o carimbo de hora pode ser usado para verificar se a concessão seja considerada expirada.

1. **O anfitrião do processador:** Cada anfitrião determina quantas partições para processar com base no número de outro instâncias de anfitriões tem concessões ativas.

   * Quando um anfitrião é iniciado, ele adquire concessões a balancear a carga de trabalho em todos os anfitriões. Um anfitrião renova periodicamente concessões, para que as concessões permanecem ativas.

   * Leia ao token de continuação última para a concessão para cada um pontos de verificação do anfitrião. Para garantir a segurança de simultaneidade, um anfitrião verifica a ETag para cada atualização de concessão. Também são suportadas outras estratégias de ponto de verificação.

   * Após o encerramento, um host libera concessões de todos os mas mantém as informações de continuação, para que ele pode continuá ler a partir do ponto de verificação armazenado mais tarde.

   Atualmente, o número de anfitriões não pode ser superior ao número de partições (concessões).

1. **Os consumidores:** Os consumidores, ou funções de trabalho, são os threads que executam o processamento de feed de alterações iniciado por cada anfitrião. Cada anfitrião do processador pode ter vários consumidores. Cada consumidor lê a alteração do feed da partição que está atribuída a e notifica o seu host de alterações e expirado concessões.

Para compreender melhor como essas quatro elementos de feed de alterações trabalho de processador em conjunto, vamos examinar um exemplo no diagrama seguinte. A coleção monitorizada Armazena os documentos e "Cidade" como a chave de partição. Podemos ver que a partição azul contém documentos com o campo "Cidade" de "A E" e assim por diante. Existem dois anfitriões, cada uma com dois os consumidores de leitura de quatro partições em paralelo. As setas mostram os consumidores de leitura a partir de um ponto específico no feed de alterações. A primeira partição, a azul mais escura representa as alterações não lidas enquanto o azul claro representa as alterações de leitura já no feed de alterações. Os anfitriões usam a coleção de concessão para armazenar um valor de "continuação" para controlar a posição atual de leitura para cada consumidor.

![Exemplo de processador do feed de alterações](./media/change-feed-processor/changefeedprocessor.png)

### <a name="change-feed-and-provisioned-throughput"></a>Feed de alterações e o débito aprovisionado

É-lhe cobrada RUs consumidos, uma vez que o movimento de dados e para os contentores de Cosmos sempre consome RUs. É-lhe cobrada RUs consumidos ao contentor de concessão.

## <a name="additional-resources"></a>Recursos adicionais

* [Biblioteca de processador do feed de alterações do Azure Cosmos DB](sql-api-sdk-dotnet-changefeed.md)
* [Pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [Exemplos adicionais no GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

## <a name="next-steps"></a>Passos Seguintes

Agora, pode avançar para saber mais sobre a alteração do feed nos seguintes artigos:

* [Descrição geral do feed de alterações](change-feed.md)
* [Feed de alterações de formas para ler](read-change-feed.md)
* [Usando a alteração do feed com as funções do Azure](change-feed-functions.md)
