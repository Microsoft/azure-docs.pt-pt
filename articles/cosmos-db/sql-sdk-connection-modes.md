---
title: Modos de conectividade Azure Cosmos DL SDK
description: Conheça os diferentes modos de conectividade disponíveis nos SDKs Azure Cosmos DB SQL.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: maquaran
ms.custom: devx-track-dotnet, contperfq2
ms.openlocfilehash: 7901ad4f67f6f3243a2d3b8dc25733ac68b612db
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608372"
---
# <a name="azure-cosmos-db-sql-sdk-connectivity-modes"></a>Modos de conectividade Azure Cosmos DL SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A forma como um cliente se conecta à Azure Cosmos DB tem implicações importantes no desempenho, especialmente para a latência observada do lado do cliente. A Azure Cosmos DB oferece um modelo de programação RESTful simples e aberto sobre HTTPS chamado modo gateway. Além disso, oferece um protocolo TCP eficiente, que também é RESTful no seu modelo de comunicação e utiliza TLS para autenticação inicial e encriptação de tráfego, chamado modo direto.

## <a name="available-connectivity-modes"></a>Modos de conectividade disponíveis

Os dois modos de conectividade disponíveis são:

  * Modo de gateway
      
    O modo Gateway é suportado em todas as plataformas SDK. Se a sua aplicação for executado dentro de uma rede corporativa com restrições rígidas de firewall, o modo gateway é a melhor escolha porque utiliza a porta HTTPS padrão e um único ponto final DNS. A troca de desempenho, no entanto, é que o modo gateway envolve um salto de rede adicional cada vez que os dados são lidos ou escritos para Azure Cosmos DB. Recomendamos também o modo de ligação de gateway quando executar aplicações em ambientes com um número limitado de ligações à tomada.

    Quando utilizar o SDK em Funções Azure, nomeadamente no [plano de Consumo,](../azure-functions/functions-scale.md#consumption-plan)esteja atento aos [atuais limites de ligações](../azure-functions/manage-connections.md).

  * Modo direto

    O modo direto suporta a conectividade através do protocolo TCP e oferece um melhor desempenho porque há menos lúpulo de rede. A aplicação liga-se diretamente às réplicas de backend. Atualmente, o modo direto apenas é suportado nas plataformas .NET e Java SDK.
     
:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="Os modos de conectividade Azure Cosmos DB" border="false":::

Estes modos de conectividade condicionam essencialmente a rota que os pedidos do plano de dados - leituras e escritas de documentos - levam da sua máquina cliente para divisórias no back-end Azure Cosmos DB. O modo direto é a opção preferida para o melhor desempenho - permite ao seu cliente abrir ligações TCP diretamente às divisórias no back-end Azure Cosmos e enviar pedidos *diretamente* sem intermediário. Em contraste, no modo Gateway, os pedidos feitos pelo seu cliente são encaminhados para um servidor chamado "Gateway" na extremidade frontal do Azure Cosmos DB, que por sua vez os fãs apagam os seus pedidos para a(s) partição(s) apropriada no DB DB Azure Cosmos.

## <a name="service-port-ranges"></a>Gamas porta de serviço

Quando utilizar o modo direto, para além das portas gateway, é necessário garantir que a autonomia entre 10000 e 20000 está aberta porque a Azure Cosmos DB utiliza portas TCP dinâmicas. Quando utilizar o modo direto nos [pontos finais privados,](./how-to-configure-private-endpoints.md)deve estar aberta toda a gama de portas TCP de 0 a 65535. Se estas portas não estiverem abertas e tentar utilizar o protocolo TCP, poderá receber um erro 503 Serviço Indisponível.

O quadro a seguir apresenta um resumo dos modos de conectividade disponíveis para várias APIs e as portas de serviço utilizadas para cada API:

|Modo de ligação  |Protocolo apoiado  |SDKs apoiados  |Porta API/Serviço  |
|---------|---------|---------|---------|
|Gateway  |   HTTPS    |  Todos os SDKs    |   SQL (443), MongoDB (10250, 10255, 10256), Tabela (443), Cassandra (10350), Gráfico (443) <br> A porta 10250 mapeia para um API API API AZure Cosmos padrão para a instância MongoDB sem geo-replicação. Enquanto as portas 10255 e 10256 mapeam para o caso que tem geo-replicação.   |
|Direct    |     TCP    |  .NET SDK Java SDK    | Quando utilizar pontos finais públicos/de serviço: portas na gama 10000-20000<br>Quando utilizar pontos finais privados: portas na gama 0 a 65535 |

## <a name="next-steps"></a>Passos seguintes

Para otimizações específicas do desempenho da plataforma SDK:

* [.NET V2 SDK dicas de desempenho](performance-tips.md)

* [.Net V3 SDK dicas de desempenho](performance-tips-dotnet-sdk-v3-sql.md)
 
* [Dicas de desempenho java V4 SDK](performance-tips-java-sdk-v4-sql.md)