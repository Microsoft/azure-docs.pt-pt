---
title: Computação de base de dados sem servidor com funções Azure Cosmos DB e Azure
description: Saiba como o Azure Cosmos DB e o Azure Functions podem ser usados em conjunto para criar aplicações de computação sem servidor orientadas por eventos.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2019
ms.author: sngun
ms.openlocfilehash: 0167dc0b1cbf8cf3b95995645ef24548a05c4343
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538651"
---
# <a name="serverless-database-computing-using-azure-cosmos-db-and-azure-functions"></a>Computação de base de dados sem servidor usando funções Azure Cosmos DB e Azure

A computação sem servidor tem tudo a ver com a capacidade de se focar em peças lógicas individuais que são repetíveis e apátridas. Estas peças não requerem gestão de infraestruturas e consomem recursos apenas durante os segundos, ou milissegundos, que correm. No cerne do movimento de computação sem servidor estão as funções, que são disponibilizadas no ecossistema Azure por [Azure Functions](https://azure.microsoft.com/services/functions). Para saber mais sobre outros ambientes de execução sem servidor em Azure, consulte sem servidor na página [Azure.](https://azure.microsoft.com/solutions/serverless/) 

Com a integração nativa entre as [funções Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) e Azure, pode criar gatilhos de base de dados, encadernações de entrada e encadernações de saída diretamente da sua conta DB Azure Cosmos. Utilizando as Funções Azure e o Azure Cosmos DB, pode criar e implementar aplicações sem servidor orientadas para eventos com acesso de baixa latência a dados ricos para uma base de utilizadores global.

## <a name="overview"></a>Descrição geral

As funções Azure Cosmos DB e Azure permitem-lhe integrar as suas bases de dados e aplicações sem servidor das seguintes formas:

* Crie um **gatilho de funções Azure orientado por eventos para Cosmos DB** . Este gatilho baseia-se em fluxos [de alimentação](change-feed.md) de mudança para monitorizar o seu contentor Azure Cosmos para alterações. Quando são efetuadas alterações num recipiente, o fluxo de alimentação de alteração é enviado para o gatilho, que invoca a Função Azure.
* Alternativamente, ligue uma Função Azure a um recipiente Azure Cosmos utilizando uma **ligação de entrada** . As ligações de entrada lêem os dados de um recipiente quando uma função é executada.
* Ligue uma função a um recipiente Azure Cosmos utilizando uma **ligação de saída** . As ligações de saída escrevem dados para um recipiente quando uma função termina.

> [!NOTE]
> Atualmente, o gatilho das funções Azure, as ligações de entrada e as ligações de saída para Cosmos DB são suportadas apenas para utilização com o API SQL. Para todas as outras APIs DB Azure Cosmos, deverá aceder à base de dados a partir da sua função utilizando o cliente estático para a sua API.


O seguinte diagrama ilustra cada uma destas três integrações: 

:::image type="content" source="./media/serverless-computing-database/cosmos-db-azure-functions-integration.png" alt-text="Como integram as funções Azure Cosmos DB e Azure" border="false":::

O gatilho das funções Azure, a ligação de entrada e a ligação de saída para a Azure Cosmos DB podem ser usadas nas seguintes combinações:

* Um gatilho de Funções Azure para Cosmos DB pode ser usado com uma ligação de saída para um recipiente Azure Cosmos diferente. Depois de uma função executar uma ação sobre um item no feed de alteração, pode escrevê-lo para outro recipiente (escrevê-lo para o mesmo recipiente de onde veio criaria efetivamente um laço recursivo). Ou, você pode usar um gatilho Azure Functions para Cosmos DB para migrar eficazmente todos os itens alterados de um recipiente para um recipiente diferente, com o uso de uma ligação de saída.
* As ligações de entrada e as ligações de saída para Azure Cosmos DB podem ser usadas na mesma Função Azure. Isto funciona bem nos casos em que pretende encontrar determinados dados com a ligação de entrada, modificá-lo na Função Azure e, em seguida, guardá-lo para o mesmo recipiente ou um recipiente diferente, após a modificação.
* Uma ligação de entrada a um recipiente Azure Cosmos pode ser usada na mesma função que um gatilho de Funções Azure para Cosmos DB, e pode ser usado com ou sem uma ligação de saída também. Pode utilizar esta combinação para aplicar informações de câmbio atualizadas (puxadas com uma ligação de entrada a um recipiente de câmbio) para alterar o feed de novas encomendas no seu serviço de carrinhos de compras. O total do carrinho de compras atualizado, com a conversão da moeda atual aplicada, pode ser escrito num terceiro recipiente utilizando uma ligação de saída.

## <a name="use-cases"></a>Casos de utilização

Os seguintes casos de utilização demonstram algumas formas de aproveitar ao máximo os seus dados DB do Azure Cosmos - ligando os seus dados às Funções Azure orientadas por eventos.

### <a name="iot-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>Caso de utilização IoT - Azure Functions trigger e ligação de saída para Cosmos DB

Nas implementações de IoT, pode invocar uma função quando a luz do motor de verificação é exibida num carro conectado.

**Implementação:** Use um gatilho de funções Azure e ligação de saída para Cosmos DB

1. Um **gatilho Azure Functions para Cosmos DB** é usado para desencadear eventos relacionados com alertas de carros, como a luz do motor de verificação que acende num carro conectado.
2. Quando a luz do motor de verificação chega, os dados do sensor são enviados para a Azure Cosmos DB.
3. A Azure Cosmos DB cria ou atualiza novos documentos de dados de sensores, então essas alterações são transmitidas para o gatilho de Funções Azure para Cosmos DB.
4. O gatilho é invocado em todas as alterações de dados para a recolha de dados do sensor, uma vez que todas as alterações são transmitidas através do feed de alteração.
5. É utilizada uma condição limiar na função para enviar os dados do sensor para o departamento de garantia.
6. Se a temperatura também for superior a um determinado valor, também é enviado um alerta ao proprietário.
7. A **ligação de saída** na função atualiza o registo do carro em outro contentor Azure Cosmos para armazenar informações sobre o evento do motor de verificação.

A imagem a seguir mostra o código escrito no portal Azure para este gatilho.

:::image type="content" source="./media/serverless-computing-database/cosmos-db-trigger-portal.png" alt-text="Como integram as funções Azure Cosmos DB e Azure":::

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Caso de utilização financeira - Gatilho temporizador e encadernação de entrada

Nas implementações financeiras, pode invocar uma função quando um saldo de conta bancária se enquadra num determinado montante.

**Implementação:** Um gatilho temporizador com uma ligação de entrada Azure Cosmos DB

1. Utilizando um [gatilho temporizador,](../azure-functions/functions-bindings-timer.md)pode obter a informação do saldo da conta bancária armazenada num recipiente Azure Cosmos em intervalos cronometrado utilizando uma **ligação de entrada** .
2. Se o saldo estiver abaixo do limiar de saldo baixo definido pelo utilizador, então acompanhe uma ação da Função Azure.
3. A imução de saída pode ser uma [integração SendGrid](../azure-functions/functions-bindings-sendgrid.md) que envia um e-mail de uma conta de serviço para os endereços de e-mail identificados para cada uma das contas de baixo saldo.

As imagens que se seguem mostram o código no portal Azure para este cenário.

:::image type="content" source="./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png" alt-text="Como integram as funções Azure Cosmos DB e Azure":::

:::image type="content" source="./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png" alt-text="Como integram as funções Azure Cosmos DB e Azure":::

### <a name="gaming-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>Caso de uso de jogos - Azure Functions trigger e ligação de saída para Cosmos DB 

Nos jogos, quando um novo utilizador é criado, pode pesquisar outros utilizadores que possam conhecê-los utilizando a [API API AZure Cosmos DB Gremlin](graph-introduction.md). Em seguida, pode escrever os resultados para uma base de dados Azure Cosmos DB ou SQL para uma fácil recuperação.

**Implementação:** Use um gatilho de funções Azure e ligação de saída para Cosmos DB

1. Utilizando uma [base de dados de gráficos](graph-introduction.md) DB da Azure Cosmos para armazenar todos os utilizadores, pode criar uma nova função com um gatilho de Funções Azure para Cosmos DB. 
2. Sempre que um novo utilizador é inserido, a função é invocada e, em seguida, o resultado é armazenado com uma **ligação de saída** .
3. A função consulta a base de dados do gráfico para procurar todos os utilizadores que estejam diretamente relacionados com o novo utilizador e retorne esse conjunto de dados à função.
4. Estes dados são então armazenados num DB Azure Cosmos que pode ser facilmente recuperado por qualquer aplicação frontal que mostre ao novo utilizador os seus amigos conectados.

### <a name="retail-use-case---multiple-functions"></a>Caso de uso a retalho - Múltiplas funções

Nas implementações de retalho, quando um utilizador adiciona um item ao seu cesto, tem agora a flexibilidade para criar e invocar funções para componentes de gasodutos de negócios opcionais.

**Implementação:** Várias funções de Azure para Cosmos DB ouvir um recipiente

1. Pode criar várias Funções Azure adicionando gatilhos de Funções Azure para Cosmos DB a cada um - todos os quais ouvem o mesmo feed de alteração de dados do carrinho de compras. Note que quando várias funções ouvem o mesmo feed de alteração, é necessária uma nova coleção de locação para cada função. Para obter mais informações sobre as coleções de arrendamento, consulte [a biblioteca do processador Change Feed](change-feed-processor.md).
2. Sempre que um novo item é adicionado a um carrinho de compras dos utilizadores, cada função é invocada independentemente pelo feed de mudança do recipiente do carrinho de compras.
   * Uma função pode utilizar o conteúdo do cesto atual para alterar o visor de outros itens em que o utilizador possa estar interessado.
   * Outra função pode atualizar os totais de inventário.
   * Outra função pode enviar informações do cliente para determinados produtos para o departamento de marketing, que lhes envia um carteiro promocional. 

     Qualquer departamento pode criar um Azure Functions for Cosmos DB ouvindo o feed de mudança, e certifique-se de que eles não atrasarão eventos críticos de processamento de encomendas no processo.

Em todos estes casos de utilização, como a função desacou a própria app, não precisa de girar sempre novas aplicações. Em vez disso, as Funções Azure giram as funções individuais para completar processos discretos conforme necessário.

## <a name="tooling"></a>Ferramentas

A integração nativa entre Azure Cosmos DB e Azure Functions está disponível no portal Azure e no Visual Studio 2019.

* No portal Azure Functions, pode criar um gatilho. Para obter instruções de arranque rápido, consulte [Criar um gatilho de funções Azure para Cosmos DB no portal Azure](../azure-functions/functions-create-cosmos-db-triggered-function.md).
* No portal DB Azure Cosmos, pode adicionar um gatilho de Funções Azure para Cosmos DB a uma aplicação Azure Function existente no mesmo grupo de recursos.
* No Visual Studio 2019, pode criar o gatilho utilizando as [Ferramentas de Funções Azure:](../azure-functions/functions-develop-vs.md)

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Porquê escolher a integração de Azure Functions para computação sem servidor?

As Funções Azure proporcionam a capacidade de criar unidades de trabalho escaláveis, ou concisas peças de lógica que podem ser executadas a pedido, sem fornecer ou gerir infraestruturas. Ao utilizar as Funções Azure, não é preciso criar uma aplicação completa para responder a alterações na base de dados do Azure Cosmos, podendo criar pequenas funções reutilizáveis para tarefas específicas. Além disso, também pode utilizar dados DB do Azure Cosmos como entrada ou saída para uma Função Azure em resposta a eventos como um pedido HTTP ou um gatilho cronometrado.

Azure Cosmos DB é a base de dados recomendada para a sua arquitetura de computação sem servidor pelas seguintes razões:

* **Acesso instantâneo a todos os seus dados** : Tem acesso granular a todos os valores armazenados porque a Azure Cosmos DB [indexa automaticamente](index-policy.md) todos os dados por padrão, e disponibiliza esses índices imediatamente. Isto significa que é capaz de consultar, atualizar e adicionar novos itens à sua base de dados e ter acesso instantâneo através de Funções Azure.

* **Schemaless.** A azure Cosmos DB é sem esquemas - por isso é únicamente capaz de lidar com qualquer saída de dados de uma Função Azure. Esta abordagem de "lidar com qualquer coisa" torna simples criar uma variedade de Funções que todas as saídas para Azure Cosmos DB.

* **Produção escalável.** A produção pode ser escalada para cima e para baixo instantaneamente em Azure Cosmos DB. Se tiver centenas ou milhares de Funções consultando e escrevendo para o mesmo recipiente, pode escalar o seu [RU/s](request-units.md) para manusear a carga. Todas as funções podem funcionar em paralelo utilizando os seus RU/s atribuídos e os seus dados são garantidos como [consistentes.](consistency-levels.md)

* **Replicação global.** Pode replicar os dados do Azure Cosmos DB [em todo o mundo](distribute-data-globally.md) para reduzir a latência, geolocalização dos seus dados mais próximos de onde os seus utilizadores estão. Tal como acontece com todas as consultas de DB da Azure Cosmos, os dados dos gatilhos orientados para o evento são lidos com dados do Azure Cosmos DB mais próximo do utilizador.

Se pretende integrar-se com as Funções Azure para armazenar dados e não necessitar de uma indexação profunda ou se necessitar de armazenar anexos e ficheiros de mídia, o [gatilho de armazenamento Azure Blob](../azure-functions/functions-bindings-storage-blob.md) pode ser uma opção melhor.

Benefícios das Funções Azure: 

* **Orientado para o evento.** As Funções Azure são orientadas para o evento e podem ouvir um feed de mudança a partir de Azure Cosmos DB. Isto significa que não precisas de criar lógica sonora, apenas ficas atento às mudanças que estás a ouvir. 

* **Sem limites.** As funções executam em paralelo e o serviço gira quantos precisar. Define os parâmetros.

* **Bom para tarefas rápidas.** O serviço gira novas instâncias de funções sempre que um evento dispara e fecha-as assim que a função termina. Só pagas o tempo que as tuas funções estão a funcionar.

Se não tem a certeza se o Flow, As Aplicações Lógicas, as Funções Azure ou webJobs são os melhores para a sua implementação, consulte [Escolha entre Flow, Logic Apps, Functions e WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="next-steps"></a>Passos seguintes

Agora vamos ligar as funções Azure Cosmos DB e Azure de verdade: 

* [Criar um gatilho de funções Azure para Cosmos DB no portal Azure](../azure-functions/functions-create-cosmos-db-triggered-function.md)
* [Criar um acionador HTTP das Funções do Azure com um enlace de entrada do Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb.md?tabs=csharp)
* [Azure Cosmos DB liga e dispara](../azure-functions/functions-bindings-cosmosdb-v2.md)
