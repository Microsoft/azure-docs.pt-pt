---
title: Computação sem servidor da base de dados - as funções do Azure e o Azure Cosmos DB
description: Saiba como do Azure Cosmos DB e as funções do Azure podem ser utilizadas em conjunto para criar aplicações computação sem servidor condicionadas por eventos.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: sngun
ms.openlocfilehash: 4d259523d3f7fe7165d0ef4c8a5aac12bd7cd823
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58123781"
---
# <a name="serverless-database-computing-using-azure-cosmos-db-and-azure-functions"></a>Computação de base de dados sem servidor com o Azure Cosmos DB e as funções do Azure

Computação sem servidor é tudo sobre a capacidade de se concentrar em partes individuais de lógica que sejam repetíveis e sem monitoração de estado. Essas partes não exigem nenhuma gestão de infraestrutura e que consomem recursos apenas para os segundos ou milissegundos, são executados para. No núcleo do movimento de computação sem servidor são funções, que estão disponíveis no ecossistema do Azure por [as funções do Azure](https://azure.microsoft.com/services/functions). Para saber mais sobre outros ambientes de execução sem servidor no Azure ver [sem servidor no Azure](https://azure.microsoft.com/solutions/serverless/) página. 

Com a integração nativa entre [do Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) e as funções do Azure, pode criar os acionadores de base de dados, enlaces de entrada e ligações de saída diretamente a partir da sua conta do Azure Cosmos DB. Utilizar as funções do Azure e o Azure Cosmos DB, pode criar e implementar aplicações sem servidor condicionadas por eventos com acesso de baixa latência para dados avançados para uma base de usuários global.

## <a name="overview"></a>Descrição geral

O Azure Cosmos DB e as funções do Azure permitem-lhe integrar as suas aplicações sem servidor e de bases de dados das seguintes formas:

* Criar um condicionada por eventos **acionador do Azure Cosmos DB** numa função do Azure. Este acionador depende [feed de alterações](change-feed.md) fluxos para monitorizar o seu contentor do Azure Cosmos DB para que as alterações. Quando são efetuadas quaisquer alterações a um contentor, a fluxo de feed de alterações é enviada para o acionador que invoca a função do Azure.
* Em alternativa, vincular uma função do Azure para um contentor do Azure Cosmos DB com um **enlace de entrada**. Enlaces de entrada leem dados a partir de um contentor quando executa uma função.
* Ligar uma função a um contentor do Azure Cosmos DB com um **enlace de saída**. Enlaces de saída escrever dados para um contentor quando uma função é concluída.

> [!NOTE]
> Atualmente, o acionador do Azure Cosmos DB, enlaces de entrada e ligações de saída são suportadas para utilização com a API de SQL apenas. Para todas as outras APIs do Azure Cosmos DB, deve acessar a base de dados a partir da sua função com o cliente de estático para a sua API.


O diagrama seguinte ilustra cada uma destas três integrações: 

![Como integram o Azure Cosmos DB e as funções do Azure](./media/serverless-computing-database/cosmos-db-azure-functions-integration.png)

O acionador do Azure Cosmos DB, o enlace de entrada e o enlace de saída podem ser usados nas combinações seguintes:
* Um acionador do Azure Cosmos DB pode ser utilizado com um enlace de saída para um contentor diferente do Azure Cosmos DB. Depois de uma função executa uma ação num item no feed de alterações pode escrevê-lo para outro contêiner (gravando-o para o mesmo contentor que vieram com eficiência criaria um loop recursivo). Em alternativa, pode utilizar um acionador do Azure Cosmos DB para efetivamente migrar itens alterados tudo a partir de um contentor num contentor diferente, com o uso de um enlace de saída.
* Enlaces de entrada e de ligações de saída para o Azure Cosmos DB podem ser utilizadas na mesma função do Azure. Isso funciona bem em casos quando pretender localizar certos dados com o enlace de entrada, modificá-lo a função do Azure e, em seguida, guarde-o para o mesmo contentor ou um contentor diferente, após a modificação.
* Um enlace de entrada para um contentor do Azure Cosmos DB pode ser utilizado na mesma função como um acionador do Azure Cosmos DB e pode ser utilizado com ou sem uma saída de ligação também. Poderia usar essa combinação para aplicar as informações da moeda atualizadas ao exchange (obtidas com um enlace de entrada para um contentor do exchange) para o feed de alterações de novas encomendas no seu serviço de carrinho de compras. Total carrinho de compras atualizada, com a conversão de moeda atual aplicada, pode ser escrito para um terceiro contentor com um enlace de saída.

## <a name="use-cases"></a>Casos de utilização

Os casos de utilização seguintes demonstram algumas maneiras que pode fazer o máximo partido dos seus dados do Azure Cosmos DB - ao ligar os dados para as funções do Azure de condicionada por eventos.

### <a name="iot-use-case---azure-cosmos-db-trigger-and-output-binding"></a>IoT caso de utilização - acionador do Azure Cosmos DB e enlace de saída

Implementações do IoT, pode invocar uma função quando a luz de mecanismo de verificação é apresentada em carros ligados.

**Implementação:** Utilizar um acionador do Azure Cosmos DB e um enlace de saída

1. Uma **acionador do Azure Cosmos DB** é utilizado para acionar eventos relacionados com alertas de carro, por exemplo, a luz de mecanismo de verificação chegando carros ligados.
2. Diz respeito a luz de mecanismo de verificação, os dados de sensor são enviados para o Azure Cosmos DB.
3. O Azure Cosmos DB cria ou atualiza a novos documentos de dados de sensor, em seguida, essas alterações são transmitidas em fluxo para o acionador do Azure Cosmos DB.
4. O acionador é invocado em todas as alterações de dados para a recolha de dados de sensor, como todas as alterações são transmitidas em fluxo através do feed de alterações.
5. Uma condição de limiar é utilizada na função para enviar os dados de sensor para o departamento de garantias.
6. Se a temperatura também é através de um determinado valor, também é enviado um alerta para o proprietário.
7. O **enlace de saída** na função atualiza o registo de carro em outro contentor do Azure Cosmos DB para armazenar informações sobre o evento de motor de verificação.

A imagem seguinte mostra o código escrito no portal do Azure para este acionador.

![Criar um acionador do Azure Cosmos DB no portal do Azure](./media/serverless-computing-database/cosmos-db-trigger-portal.png)

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Financial caso de utilização - acionador de temporizador e enlace de entrada

Em implementações financeiras, é possível invocar uma função quando atingir um saldo de conta bancária num determinado período.

**Implementação:** Um acionador de temporizador com um enlace de entrada do Azure Cosmos DB

1. Utilizar um [acionador de temporizador](../azure-functions/functions-bindings-timer.md), pode obter as informações de saldo de conta bancária armazenadas num contentor do Azure Cosmos DB de intervalos de tempo com um **enlace de entrada**.
2. Se o saldo for inferior ao limiar de saldo baixo definido pelo utilizador, em seguida, acompanhe os uma ação da função do Azure.
3. O enlace de saída pode ser um [SendGrid integração](../azure-functions/functions-bindings-sendgrid.md) que envia um e-mail de uma conta de serviço para os endereços de e-mail identificados para cada uma das contas de saldo baixo.

As seguintes imagens mostram o código no portal do Azure para este cenário.

![Ficheiro Index js para um acionador de temporizador para um cenário financeiro](./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png)

![Ficheiro Run. csx para um acionador de temporizador para um cenário financeiro](./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png)

### <a name="gaming-use-case---azure-cosmos-db-trigger-and-output-binding"></a>Jogos caso de utilização - acionador do Azure Cosmos DB e enlace de saída

Em jogos, quando é criado um novo utilizador pode procurar outros utilizadores que podem conhecê-los utilizando o [API do Azure Cosmos DB Gremlin](graph-introduction.md). Em seguida, pode escrever os resultados para um [Azure Cosmos DB SQL da base de dados] para fácil obtenção.

**Implementação:** Utilizar um acionador do Azure Cosmos DB e um enlace de saída

1. Utilizar o Azure Cosmos DB [base de dados do gráfico](graph-introduction.md) para armazenar todos os utilizadores, pode criar uma nova função com um acionador do Azure Cosmos DB. 
2. Sempre que um novo utilizador é inserido, a função é invocada e, em seguida, o resultado é armazenado com uma **enlace de saída**.
3. A função de consulta a base de dados do gráfico para procurar todos os utilizadores que estão diretamente relacionados ao novo usuário e retorna esse conjunto de dados para a função.
4. Estes dados, em seguida, são armazenados no Azure Cosmos DB que, em seguida, podem ser facilmente obtidos por qualquer aplicação de front-end que mostra o novo utilizador amigos ligados.

### <a name="retail-use-case---multiple-functions"></a>Caso de utilização de varejo - várias funções

Em implementações de varejo, quando um utilizador adiciona um item para seus cesto agora tem a flexibilidade para criar e invocar funções para componentes de pipeline de negócios opcional.

**Implementação:** Múltiplos acionadores do Azure Cosmos DB a escuta de um contentor

1. Pode criar várias funções do Azure ao adicionar acionadores do Azure Cosmos DB a cada - todos os quais escutam ao mesmo alterar feed de dados do carrinho de compras. Tenha em atenção que, quando várias funções de ouvem o mesmo feed de alterações, uma nova coleção de concessão é necessária para cada função. Para obter mais informações sobre as coleções de concessão, consulte [Noções básicas sobre a biblioteca processador do Feed de alterações](change-feed-processor.md).
2. Sempre que um novo item é adicionado a um carrinho de compras de utilizadores, cada função de forma independente é invocada pela mudança de feed o contêiner de carrinho de compras.
   * Uma função pode utilizar o conteúdo do cesto atual para alterar a apresentação de outros itens que o utilizador poderá ter interesse em.
   * Outra função pode atualizar os totais de inventário.
   * Outra função pode enviar informações de clientes para determinados produtos para o departamento de marketing, que envia um mailer promocional. 

     Qualquer departamento pode criar um acionador do Azure Cosmos DB através da escuta para o feed de alterações e certifique-se de que eles não atrasar eventos de processamento de ordem crítica no processo.

Em todos esses casos de utilização, porque a função tem desacoplados aplicação em si, não precisa acelerar novas instâncias de aplicação, o tempo todo. Em vez disso, as funções do Azure acelera funções individuais para concluir processos distintos, conforme necessário.

## <a name="tooling"></a>Ferramentas

Integração nativa entre o Azure Cosmos DB e as funções do Azure está disponível no portal do Azure e no Visual Studio 2017.

* No portal das funções do Azure, pode criar um acionador do Azure Cosmos DB. Para obter instruções de início rápido, consulte [criar um acionador do Azure Cosmos DB no portal do Azure](https://aka.ms/cosmosdbtriggerportalfunc).
* No portal do Azure Cosmos DB, pode adicionar um acionador do Azure Cosmos DB a uma aplicação de função do Azure existente no mesmo grupo de recursos.
* No Visual Studio 2017, pode criar um acionador do Azure Cosmos DB utilizando a [ferramentas de funções do Azure para Visual Studio 2017](../azure-functions/functions-develop-vs.md):

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Por que escolher integração das funções do Azure para a computação sem servidor?

As funções do Azure fornece a capacidade de criar unidades escalonáveis de trabalho, ou partes concisos de lógica que podem ser executadas a pedido, sem aprovisionar ou gerir infraestrutura. Ao utilizar as funções do Azure, não precisa criar uma aplicação completa para responder às alterações na sua base de dados do Azure Cosmos DB, pode criar funções reutilizáveis do pequeno para tarefas específicas. Além disso, também pode utilizar dados do Azure Cosmos DB como entrada ou saída para uma função do Azure em resposta a eventos, como um HTTP pedidos ou um acionador de tempo.

O Azure Cosmos DB é a base de dados recomendada para a sua arquitetura de computação sem servidor pelos seguintes motivos:

* **Acesso imediato a todos os seus dados**: Tem acesso granular a cada valor armazenado porque do Azure Cosmos DB [indexa automaticamente](index-policy.md) todos os dados por predefinição e disponibiliza imediatamente desses índices. Isso significa que pode constantemente a consultar, atualizar e a adicionar novos itens à sua base de dados e ter acesso instantâneo através das funções do Azure.

* **Sem esquemas**. O Azure Cosmos DB é sem esquemas - é exclusivamente capaz de lidar com qualquer saída de dados de uma função do Azure. Essa abordagem de "lidar com qualquer coisa" torna simples para criar uma variedade de funções que toda a saída para o Azure Cosmos DB.

* **Débito dimensionável**. Taxa de transferência pode ser aumentada e diminuído instantaneamente no Azure Cosmos DB. Se tiver centenas ou milhares de funções de consulta e escrita ao mesmo contentor, pode dimensionar a sua [RU/s](request-units.md) para processar a carga. Todas as funções podem trabalhar em paralelo usando sua alocado RU/s e seus dados são garantidos que estará [consistente](consistency-levels.md).

* **Replicação global**. Pode replicar dados do Azure Cosmos DB [em todo o mundo](distribute-data-globally.md) para reduzir a latência, geo a localizar os dados mais próximos de onde estão os utilizadores. Como com todas as consultas do Azure Cosmos DB, dados a partir de acionadores desencadeados por eventos é ler dados a partir do Azure Cosmos DB mais próximo do utilizador.

Se estiver à procura para integrar com as funções do Azure para armazenar dados e não precisa de indexar profunda ou se tiver de armazenar anexos e arquivos de suporte de dados, o [acionador do armazenamento de Blobs do Azure](../azure-functions/functions-bindings-storage-blob.md) pode ser uma opção melhor.

Benefícios das funções do Azure: 

* **Condicionada por eventos**. As funções do Azure são condicionadas por eventos e podem escutar a uma alteração de feed do Azure Cosmos DB. Isso significa que não precisa de criar a lógica de escuta, apenas fique atento para que as alterações que estiver ouvindo. 

* **Sem limites de**. As funções executadas em paralelo e o serviço de rotações se tantos conforme necessário. Defina os parâmetros.

* **Bom para tarefas rápidas**. O serviço acelera novas instâncias de funções, sempre que um evento é acionado e fecha-las assim que a função for concluída. Paga apenas o tempo que as suas funções estão em execução.

Se não tiver a certeza se Flow, Logic Apps, as funções do Azure ou os WebJobs são recomendadas para a sua implementação, consulte [escolher entre Flow, Logic Apps, funções e WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="next-steps"></a>Passos Seguintes

Agora vamos ligar do Azure Cosmos DB e as funções do Azure verdadeira: 

* [Criar um acionador do Azure Cosmos DB no portal do Azure](https://aka.ms/cosmosdbtriggerportalfunc)
* [Criar um acionador de HTTP de funções do Azure com um enlace de entrada do Azure Cosmos DB](https://aka.ms/cosmosdbinputbind)
* [Acionadores e enlaces do Cosmos DB do Azure](../azure-functions/functions-bindings-cosmosdb.md)


 



