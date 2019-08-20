---
title: Computação de banco de dados sem servidor-Azure Functions e Azure Cosmos DB
description: Saiba como Azure Cosmos DB e Azure Functions podem ser usados juntos para criar aplicativos de computação sem servidor controlados por eventos.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: sngun
ms.openlocfilehash: e1014c710d892e45f09999db22b1f59c0bb36300
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614577"
---
# <a name="serverless-database-computing-using-azure-cosmos-db-and-azure-functions"></a>Computação de banco de dados sem servidor usando Azure Cosmos DB e Azure Functions

A computação sem servidor é tudo sobre a capacidade de se concentrar em partes individuais da lógica que podem ser repetidas e não monitoradas. Essas partes não exigem gerenciamento de infraestrutura e consomem recursos apenas por segundos, ou milissegundos, que são executadas para o. No núcleo da movimentação de computação sem servidor estão as funções, que são disponibilizadas no ecossistema do Azure por [Azure Functions](https://azure.microsoft.com/services/functions). Para saber mais sobre outros ambientes de execução sem servidor no Azure, confira a página sem [servidor no Azure](https://azure.microsoft.com/solutions/serverless/) . 

Com a integração nativa entre [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) e Azure functions, você pode criar gatilhos de banco de dados, associações de entrada e associações de saída diretamente da sua conta de Azure Cosmos DB. Usando Azure Functions e Azure Cosmos DB, você pode criar e implantar aplicativos sem servidor controlados por eventos com acesso de baixa latência a dados avançados para uma base de usuários global.

## <a name="overview"></a>Descrição geral

Azure Cosmos DB e Azure Functions permitem que você integre seus bancos de dados e aplicativos sem servidor das seguintes maneiras:

* Crie um **gatilho de Azure Functions controlado por evento para Cosmos DB**. Esse gatilho depende de fluxos de [feed de alterações](change-feed.md) para monitorar o contêiner Cosmos do Azure em busca de alterações. Quando qualquer alteração é feita em um contêiner, o fluxo do feed de alterações é enviado ao gatilho, que invoca a função do Azure.
* Como alternativa, associe uma função do Azure a um contêiner Cosmos do Azure usando uma **Associação de entrada**. As associações de entrada lêem dados de um contêiner quando uma função é executada.
* Associe uma função a um contêiner Cosmos do Azure usando uma **Associação de saída**. As associações de saída gravam dados em um contêiner quando uma função é concluída.

> [!NOTE]
> Atualmente, Azure Functions gatilho, associações de entrada e associações de saída para Cosmos DB têm suporte para uso somente com a API do SQL. Para todas as outras APIs de Azure Cosmos DB, você deve acessar o banco de dados de sua função usando o cliente estático para sua API.


O diagrama a seguir ilustra cada uma dessas três integrações: 

![Como Azure Cosmos DB e Azure Functions integrar](./media/serverless-computing-database/cosmos-db-azure-functions-integration.png)

O gatilho Azure Functions, a associação de entrada e a associação de saída para Azure Cosmos DB podem ser usados nas seguintes combinações:

* Um gatilho Azure Functions para Cosmos DB pode ser usado com uma associação de saída para um contêiner Cosmos do Azure diferente. Depois que uma função executa uma ação em um item no feed de alterações, você pode gravá-la em outro contêiner (gravá-la no mesmo contêiner em que ela veio criaria efetivamente um loop recursivo). Ou você pode usar um gatilho Azure Functions para Cosmos DB a fim de migrar efetivamente todos os itens alterados de um contêiner para um contêiner diferente, com o uso de uma associação de saída.
* Associações de entrada e associações de saída para Azure Cosmos DB podem ser usadas na mesma função do Azure. Isso funciona bem em casos em que você deseja localizar determinados dados com a associação de entrada, modificá-los na função do Azure e, em seguida, salvá-los no mesmo contêiner ou em um contêiner diferente, após a modificação.
* Uma associação de entrada para um contêiner Cosmos do Azure pode ser usada na mesma função que um gatilho Azure Functions para Cosmos DB e também pode ser usada com ou sem uma associação de saída. Você pode usar essa combinação para aplicar informações de troca de moedas atualizadas (recebidas com uma associação de entrada para um contêiner do Exchange) para o feed de alterações de novos pedidos em seu serviço de carrinho de compras. O total do carrinho de compras atualizado, com a conversão de moeda atual aplicada, pode ser gravado em um terceiro contêiner usando uma associação de saída.

## <a name="use-cases"></a>Casos de utilização

Os casos de uso a seguir demonstram algumas maneiras que você pode aproveitar ao máximo seus dados de Azure Cosmos DB-conectando seus dados a Azure Functions orientados por eventos.

### <a name="iot-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>Caso de uso de IoT-Azure Functions gatilho e Associação de saída para Cosmos DB

Em implementações de IoT, você pode invocar uma função quando a luz do mecanismo de verificação é exibida em um carro conectado.

**Implementação** Usar um gatilho Azure Functions e uma associação de saída para Cosmos DB

1. Um **gatilho Azure Functions para Cosmos DB** é usado para disparar eventos relacionados a alertas de carros, como a luz do mecanismo de verificação entrando em um carro conectado.
2. Quando a luz do mecanismo de verificação é exibida, os dados do sensor são enviados para Azure Cosmos DB.
3. Azure Cosmos DB cria ou atualiza novos documentos de dados de sensor, essas alterações são transmitidas para o gatilho de Azure Functions para Cosmos DB.
4. O gatilho é invocado em cada alteração de dados para a coleta de dados do sensor, pois todas as alterações são transmitidas por meio do feed de alterações.
5. Uma condição de limite é usada na função para enviar os dados do sensor para o departamento de garantia.
6. Se a temperatura também estiver acima de um determinado valor, um alerta também será enviado ao proprietário.
7. A **Associação de saída** na função atualiza o registro de carro em outro contêiner Cosmos do Azure para armazenar informações sobre o evento do mecanismo de verificação.

A imagem a seguir mostra o código escrito na portal do Azure para este gatilho.

![Criar um gatilho de Azure Functions para Cosmos DB no portal do Azure](./media/serverless-computing-database/cosmos-db-trigger-portal.png)

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Caso de uso financeiro-gatilho de temporizador e Associação de entrada

Em implementações financeiras, você pode invocar uma função quando um saldo de conta bancária se enquadrar em um determinado valor.

**Implementação** Um gatilho de temporizador com uma associação de entrada Azure Cosmos DB

1. Usando um [gatilho](../azure-functions/functions-bindings-timer.md)de temporizador, você pode recuperar as informações de saldo da conta bancária armazenadas em um contêiner Cosmos do Azure em intervalos de tempo usando uma **Associação de entrada**.
2. Se o saldo estiver abaixo do limite de saldo baixo definido pelo usuário, siga uma ação da função do Azure.
3. A associação de saída pode ser uma [integração SendGrid](../azure-functions/functions-bindings-sendgrid.md) que envia um email de uma conta de serviço para os endereços de email identificados para cada uma das contas de saldo baixo.

As imagens a seguir mostram o código no portal do Azure para este cenário.

![Arquivo index. js para um gatilho de temporizador para um cenário financeiro](./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png)

![Execute o arquivo. CSX para um gatilho de temporizador para um cenário financeiro](./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png)

### <a name="gaming-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>Caso de uso de jogos-Azure Functions gatilho e Associação de saída para Cosmos DB 

Em jogos, quando um novo usuário é criado, você pode pesquisar outros usuários que possam conhecê-los usando a [API do Azure Cosmos DB Gremlin](graph-introduction.md). Em seguida, você pode gravar os resultados em um [Azure Cosmos DB banco de dados SQL] para recuperação fácil.

**Implementação** Usar um gatilho Azure Functions e uma associação de saída para Cosmos DB

1. Usando um [banco de dados](graph-introduction.md) de Azure Cosmos DB Graph para armazenar todos os usuários, você pode criar uma nova função com um gatilho de Azure Functions para Cosmos DB. 
2. Sempre que um novo usuário é inserido, a função é invocada e, em seguida, o resultado é armazenado usando uma **Associação de saída**.
3. A função consulta o banco de dados de grafo para pesquisar todos os usuários que estão diretamente relacionados ao novo usuário e retorna esse DataSet para a função.
4. Esses dados são então armazenados em uma Azure Cosmos DB que pode ser recuperada facilmente por qualquer aplicativo front-end que mostre ao novo usuário seus amigos conectados.

### <a name="retail-use-case---multiple-functions"></a>Caso de uso de varejo – várias funções

Em implementações de varejo, quando um usuário adiciona um item à sua cesta, agora você tem a flexibilidade de criar e invocar funções para componentes opcionais de pipeline de negócios.

**Implementação** Vários gatilhos Azure Functions para Cosmos DB ouvindo um contêiner

1. Você pode criar vários Azure Functions adicionando Azure Functions gatilhos para Cosmos DB a cada um deles que escutam o mesmo feed de alterações dos dados do carrinho de compras. Observe que, quando várias funções ouvem o mesmo feed de alterações, uma nova coleção de concessão é necessária para cada função. Para obter mais informações sobre coleções de concessão, consulte [noções básicas sobre a biblioteca do processador do feed de alterações](change-feed-processor.md).
2. Sempre que um novo item é adicionado a um carrinho de compras de usuários, cada função é invocada independentemente pelo feed de alterações do contêiner de carrinho de compras.
   * Uma função pode usar o conteúdo da cesta atual para alterar a exibição de outros itens em que o usuário pode estar interessado.
   * Outra função pode atualizar os totais de inventário.
   * Outra função pode enviar informações do cliente para determinados produtos para o departamento de marketing, que os envia para um correio promocional. 

     Qualquer departamento pode criar um Azure Functions para Cosmos DB ouvindo o feed de alterações e ter certeza de que eles não atrasarão os eventos de processamento de pedidos críticos no processo.

Em todos esses casos de uso, como a função tem dissociado o próprio aplicativo, você não precisa criar novas instâncias de aplicativo o tempo todo. Em vez disso, Azure Functions gira as funções individuais para concluir processos discretos conforme necessário.

## <a name="tooling"></a>Ferramentas

A integração nativa entre Azure Cosmos DB e Azure Functions está disponível no portal do Azure e no Visual Studio 2019.

* No portal de Azure Functions, você pode criar um gatilho. Para obter instruções de início rápido, consulte [criar um gatilho de Azure Functions para Cosmos DB no portal do Azure](https://aka.ms/cosmosdbtriggerportalfunc).
* No portal de Azure Cosmos DB, você pode adicionar um gatilho de Azure Functions para Cosmos DB a um aplicativo de funções do Azure existente no mesmo grupo de recursos.
* No Visual Studio 2019, você pode criar o gatilho usando as [ferramentas de Azure Functions](../azure-functions/functions-develop-vs.md):

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Por que escolher a integração de Azure Functions para a computação sem servidor?

Azure Functions fornece a capacidade de criar unidades escalonáveis de trabalho ou partes concisas de lógica que podem ser executadas sob demanda, sem provisionamento ou gerenciamento de infraestrutura. Usando Azure Functions, você não precisa criar um aplicativo completo para responder às alterações no banco de dados Cosmos do Azure, você pode criar pequenas funções reutilizáveis para tarefas específicas. Além disso, você também pode usar Azure Cosmos DB dados como entrada ou saída para uma função do Azure em resposta ao evento, como solicitações HTTP ou um gatilho cronometrado.

Azure Cosmos DB é o banco de dados recomendado para sua arquitetura de computação sem servidor pelos seguintes motivos:

* **Acesso instantâneo a todos os seus dados**: Você tem acesso granular a cada valor armazenado porque Azure Cosmos DB [indexa automaticamente](index-policy.md) todos os dados por padrão e torna esses índices imediatamente disponíveis. Isso significa que você pode consultar, atualizar e adicionar novos itens constantemente ao seu banco de dados e ter acesso instantâneo por meio de Azure Functions.

* Sem **esquema**. Azure Cosmos DB não tem esquema e, portanto, é capaz de lidar exclusivamente com qualquer saída de dados de uma função do Azure. Essa abordagem "tratar qualquer coisa" torna simples criar uma variedade de funções que todas as saídas para Azure Cosmos DB.

* **Taxa de transferência escalonável**. A taxa de transferência pode ser aumentada e reduzida instantaneamente no Azure Cosmos DB. Se você tiver centenas ou milhares de funções consultando e gravando no mesmo contêiner, poderá escalar verticalmente seus [ru/s](request-units.md) para lidar com a carga. Todas as funções podem funcionar em paralelo usando as RU/s alocadas e seus dados são garantidos como [consistentes](consistency-levels.md).

* **Replicação global**. Você pode replicar dados [de Azure Cosmos DB em todo o mundo](distribute-data-globally.md) para reduzir a latência, localizando geograficamente seus dados mais próximos de onde estão os usuários. Assim como ocorre com todas as consultas de Azure Cosmos DB, os dados de gatilhos orientados a eventos são dados de leitura do Azure Cosmos DB mais próximo ao usuário.

Se você estiver procurando integrar com Azure Functions para armazenar dados e não precisar de indexação profunda ou se precisar armazenar anexos e arquivos de mídia, o [gatilho do armazenamento de BLOBs do Azure](../azure-functions/functions-bindings-storage-blob.md) poderá ser uma opção melhor.

Benefícios do Azure Functions: 

* **Controlado por evento**. Azure Functions são controladas por evento e podem escutar um feed de alterações de Azure Cosmos DB. Isso significa que você não precisa criar uma lógica de escuta. você só fica atento às alterações que está ouvindo. 

* **Sem limites**. As funções são executadas em paralelo e o serviço gira quantas forem necessárias. Você define os parâmetros.

* **Bom para tarefas rápidas**. O serviço gira novas instâncias de funções sempre que um evento é disparado e fecha-as assim que a função é concluída. Você só paga pelo tempo em que suas funções estão em execução.

Se você não tiver certeza se o Flow, os aplicativos lógicos, Azure Functions ou trabalhos Web são melhores para sua implementação, consulte [escolher entre fluxo, aplicativos lógicos, funções e trabalhos](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)Web.

## <a name="next-steps"></a>Passos Seguintes

Agora, vamos conectar Azure Cosmos DB e Azure Functions para o real: 

* [Criar um gatilho de Azure Functions para Cosmos DB no portal do Azure](https://aka.ms/cosmosdbtriggerportalfunc)
* [Criar um gatilho HTTP Azure Functions com uma associação de entrada de Azure Cosmos DB](https://aka.ms/cosmosdbinputbind)
* [Azure Cosmos DB associações e gatilhos](../azure-functions/functions-bindings-cosmosdb.md)


 



