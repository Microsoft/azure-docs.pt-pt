---
title: Computação de base de dados sem servidores com funções Azure Cosmos DB e Azure
description: Saiba como as Funções Azure Cosmos DB e Azure podem ser usadas em conjunto para criar aplicações de computação sem servidores orientadas por eventos.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: sngun
ms.openlocfilehash: 70545020899b69508a4cedb0fd7cf5495c847104
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462450"
---
# <a name="serverless-database-computing-using-azure-cosmos-db-and-azure-functions"></a>Computação de base de dados sem servidores utilizando funções Azure Cosmos DB e Azure

A computação sem servidores tem tudo a ver com a capacidade de se focar em pedaços individuais de lógica que são repetíveis e apátridas. Estas peças não requerem gestão de infraestruturas e consomem recursos apenas durante os segundos, ou milissegundos, que correm para. No cerne do movimento de computação sem servidores estão as funções, que são disponibilizadas no ecossistema Azure pelas [Funções Azure.](https://azure.microsoft.com/services/functions) Para saber mais sobre outros ambientes de execução sem servidor estoirar em Azure, consulte o servidor na página [do Azure.](https://azure.microsoft.com/solutions/serverless/) 

Com a integração nativa entre as Funções [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) e Azure, pode criar gatilhos de base de dados, encadernações de entrada e encadernações de saída diretamente da sua conta Azure Cosmos DB. Utilizando funções Azure e Azure Cosmos DB, pode criar e implementar aplicações sem servidores orientadas por eventos com acesso de baixa latência a dados ricos para uma base de utilizadores global.

## <a name="overview"></a>Descrição geral

As Funções Azure Cosmos DB e Azure permitem-lhe integrar as suas bases de dados e aplicações sem servidores das seguintes formas:

* Crie um gatilho de **funções Azure orientado saqueadas por eventos para cosmos DB**. Este gatilho baseia-se em fluxos de [alimentação](change-feed.md) de mudança para monitorizar o seu recipiente Azure Cosmos para alterações. Quando são efetuadas alterações num recipiente, o fluxo de alimentação de alteração é enviado para o gatilho, que invoca a Função Azure.
* Alternativamente, ligue uma Função Azure a um recipiente Azure Cosmos utilizando uma **ligação**de entrada . As encadernações de entrada lêem os dados de um recipiente quando uma função executa.
* Ligue uma função a um recipiente Azure Cosmos utilizando uma **ligação**de saída . As encadernações de saída escrevem dados para um recipiente quando uma função completa.

> [!NOTE]
> Atualmente, o gatilho das Funções Azure, as encadernações de entrada e as encadernações de saída para o Cosmos DB são suportadas apenas com a API SQL. Para todas as outras APIs DB Da Azure Cosmos, deve aceder à base de dados a partir da sua função utilizando o cliente estático para a sua API.


O diagrama seguinte ilustra cada uma destas três integrações: 

![Como integram as funções Azure Cosmos DB e Azure](./media/serverless-computing-database/cosmos-db-azure-functions-integration.png)

O gatilho das Funções Azure, a ligação de entrada e a ligação de saída para o Azure Cosmos DB podem ser utilizados nas seguintes combinações:

* Um gatilho de funções Azure para Cosmos DB pode ser usado com uma ligação de saída a um recipiente Azure Cosmos diferente. Depois de uma função realizar uma ação num item no feed de mudança pode escrevê-lo noutro recipiente (escrevê-lo no mesmo recipiente de onde veio criaria efetivamente um laço recursivo). Ou, pode utilizar um gatilho de Funções Azure para o Cosmos DB para migrar eficazmente todos os itens alterados de um recipiente para um recipiente diferente, com a utilização de uma ligação de saída.
* As encadernações de entrada e encadernações de saída para o Azure Cosmos DB podem ser utilizadas na mesma Função Azure. Isto funciona bem nos casos em que pretende encontrar certos dados com a encadernação de entrada, modificá-los na Função Azure e, em seguida, guardá-los para o mesmo recipiente ou um recipiente diferente, após a modificação.
* Uma entrada de ligação a um recipiente Azure Cosmos pode ser usada na mesma função que um gatilho de Funções Azure para Cosmos DB, e pode ser usada com ou sem uma ligação de saída também. Pode utilizar esta combinação para aplicar informações atualizadas sobre a troca de divisas (puxadas com uma entrada vinculativa para um recipiente de câmbio) para a mudança de novas encomendas no seu serviço de carrinhos de compras. O total do carrinho de compras atualizado, com a conversão cambial atual aplicada, pode ser escrito a um terceiro recipiente utilizando uma encadernação de saída.

## <a name="use-cases"></a>Casos de utilização

Os seguintes casos de utilização demonstram algumas formas de aproveitar ao máximo os seus dados Azure Cosmos DB - ligando os seus dados às Funções Azure orientadas para eventos.

### <a name="iot-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>Caso de utilização de IoT - Azure Functions trigger and output binding for Cosmos DB

Nas implementações do IoT, pode invocar uma função quando a luz do motor de verificação é exibida num carro conectado.

**Implementação:** Utilize um gatilho de funções Azure e ligação de saída para Cosmos DB

1. Um **gatilho de Funções Azure para Cosmos DB** é usado para desencadear eventos relacionados com alertas de carros, como a luz do motor de verificação que acende num carro conectado.
2. Quando a luz do motor de verificação chegar, os dados do sensor são enviados para o Azure Cosmos DB.
3. O Azure Cosmos DB cria ou atualiza novos documentos de dados de sensores, então essas alterações são transmitidas para o gatilho das Funções Azure para cosmos DB.
4. O gatilho é invocado em todas as alterações de dados na recolha de dados do sensor, uma vez que todas as alterações são transmitidas através do feed de alteração.
5. Uma condição de limiar é usada na função para enviar os dados do sensor para o departamento de garantia.
6. Se a temperatura também for superior a um determinado valor, é também enviado um alerta ao proprietário.
7. A **ligação de saída** da função atualiza o registo do carro noutro contentor da Azure Cosmos para armazenar informações sobre o evento do motor de verificação.

A imagem que se segue mostra o código escrito no portal Azure para este gatilho.

![Criar um gatilho de Funções Azure para cosmos DB no portal Azure](./media/serverless-computing-database/cosmos-db-trigger-portal.png)

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Caso de utilização financeira - Gatilho temporizador e encadernação de entrada

Nas implementações financeiras, pode invocar uma função quando um saldo de conta bancária se situa abaixo de um determinado montante.

**Implementação:** Um gatilho temporizador com uma ligação de entrada Azure Cosmos DB

1. Utilizando um [gatilho temporizador,](../azure-functions/functions-bindings-timer.md)pode recuperar a informação do saldo da conta bancária armazenada num contentor Azure Cosmos em intervalos cronometrados utilizando uma **encadernação**de entrada .
2. Se o saldo estiver abaixo do limiar de saldo baixo definido pelo utilizador, acompanhe com uma ação da Função Azure.
3. A ligação de saída pode ser uma [integração SendGrid](../azure-functions/functions-bindings-sendgrid.md) que envia um e-mail de uma conta de serviço para os endereços de e-mail identificados para cada uma das contas de baixo saldo.

As seguintes imagens mostram o código no portal Azure para este cenário.

![Ficheiro Index.js para um gatilho do Temporizador para um cenário financeiro](./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png)

![Ficheiro Run.csx para um gatilho do Temporizador para um cenário financeiro](./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png)

### <a name="gaming-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>Caixa de utilização de jogos - Azure Functions gatilho e ligação de saída para Cosmos DB 

Nos jogos, quando um novo utilizador é criado, pode pesquisar outros utilizadores que os possam conhecer utilizando o [Azure Cosmos DB Gremlin API](graph-introduction.md). Em seguida, pode escrever os resultados para uma base de dados [Azure Cosmos DB SQL] para uma recuperação fácil.

**Implementação:** Utilize um gatilho de funções Azure e ligação de saída para Cosmos DB

1. Utilizando uma base de dados de [gráficos](graph-introduction.md) Azure Cosmos DB para armazenar todos os utilizadores, pode criar uma nova função com um gatilho de Funções Azure para cosmos DB. 
2. Sempre que um novo utilizador é inserido, a função é invocada e, em seguida, o resultado é armazenado com uma **encadernação de saída**.
3. A função questiona a base de dados do gráfico para procurar todos os utilizadores que estejam diretamente relacionados com o novo utilizador e devolve esse conjunto de dados à função.
4. Estes dados são então armazenados num Azure Cosmos DB que pode ser facilmente recuperado por qualquer aplicação frontal que mostre ao novo utilizador os seus amigos conectados.

### <a name="retail-use-case---multiple-functions"></a>Caixa de uso retalhista - Múltiplas funções

Nas implementações de retalho, quando um utilizador adiciona um item ao seu cabaz, tem agora a flexibilidade para criar e invocar funções para componentes opcionais de pipeline de negócios.

**Implementação:** Múltiplas Funções Azure disparam para Cosmos DB ouvir um recipiente

1. Pode criar múltiplas Funções Azure adicionando gatilhos de Funções Azure para Cosmos DB a cada um - todos os quais ouvem o mesmo feed de mudança de dados do carrinho de compras. Note que quando várias funções ouvem o mesmo feed de mudança, é necessária uma nova coleção de arrendamento para cada função. Para mais informações sobre as coleções de arrendamento, consulte [Understanding the Change Feed Processor library](change-feed-processor.md).
2. Sempre que um novo item é adicionado a um carrinho de compras dos utilizadores, cada função é invocada independentemente pela mudança de alimentos do recipiente do carrinho de compras.
   * Uma função pode utilizar o conteúdo do cabaz atual para alterar o visualização de outros itens em que o utilizador possa estar interessado.
   * Outra função pode atualizar os totais de inventário.
   * Outra função pode enviar informações do cliente para determinados produtos para o departamento de marketing, que lhes envia um carteiro promocional. 

     Qualquer departamento pode criar uma Funções Azure para Cosmos DB ouvindo o feed de mudança, e certifique-se de que eles não vão atrasar eventos de processamento de ordem crítica no processo.

Em todos estes casos de utilização, uma vez que a função desagregaa a própria app, não é preciso girar sempre novas instâncias de aplicações. Em vez disso, a Azure Functions gira funções individuais para completar processos discretos conforme necessário.

## <a name="tooling"></a>Ferramentas

A integração nativa entre a Azure Cosmos DB e a Azure Functions está disponível no portal Azure e no Visual Studio 2019.

* No portal Funções Azure, pode criar um gatilho. Para obter instruções de arranque rápido, consulte Criar um gatilho de [funções Azure para Cosmos DB no portal Azure](https://aka.ms/cosmosdbtriggerportalfunc).
* No portal Azure Cosmos DB, você pode adicionar um gatilho de Funções Azure para Cosmos DB a uma aplicação azure function existente no mesmo grupo de recursos.
* No Visual Studio 2019, pode criar o gatilho utilizando as [Ferramentas de Funções Azure:](../azure-functions/functions-develop-vs.md)

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Porquê escolher a integração de Funções Azure para computação sem servidores?

A Azure Functions oferece a capacidade de criar unidades de trabalho escaláveis, ou peças lógicas concisas que podem ser executadas a pedido, sem fornecer ou gerir infraestruturas. Ao utilizar funções Azure, não é preciso criar uma app completa para responder a alterações na sua base de dados Azure Cosmos, pode criar pequenas funções reutilizáveis para tarefas específicas. Além disso, também pode utilizar os dados do Azure Cosmos DB como entrada ou saída para uma Função Azure em resposta a eventos como pedidos HTTP ou um gatilho cronometrado.

Azure Cosmos DB é a base de dados recomendada para a sua arquitetura de computação sem servidores pelas seguintes razões:

* **Acesso instantâneo a todos os seus dados**: Tem acesso granular a todos os valores armazenados porque o Azure Cosmos DB [indexa automaticamente](index-policy.md) todos os dados por padrão, e disponibiliza esses índices imediatamente. Isto significa que é capaz de consultar, atualizar e adicionar novos itens à sua base de dados e ter acesso instantâneo através das Funções Azure.

* **Sem nada.** O Azure Cosmos DB é inútil - por isso é único capaz de lidar com qualquer saída de dados a partir de uma Função Azure. Esta abordagem "lidar com qualquer coisa" torna simples criar uma variedade de Funções que toda a produção para Azure Cosmos DB.

* **Entrada escalável.** A entrada pode ser dimensionada para cima e para baixo instantaneamente em Azure Cosmos DB. Se tiver centenas ou milhares de funções consultando e escrevendo para o mesmo recipiente, pode escalar o seu [RU/s](request-units.md) para lidar com a carga. Todas as funções podem funcionar em paralelo utilizando os seus RU/s atribuídos e os seus dados são garantidos como [consistentes](consistency-levels.md).

* **Replicação global.** Pode replicar dados do Azure Cosmos DB [em todo o mundo](distribute-data-globally.md) para reduzir a latência, geolocalização dos seus dados mais próximos de onde os seus utilizadores estão. Tal como acontece com todas as consultas da Azure Cosmos DB, os dados dos gatilhos orientados para o evento são lidos dados do Azure Cosmos DB mais próximo do utilizador.

Se procura integrar-se com as Funções Azure para armazenar dados e não necessitar de indexação profunda ou se precisar de armazenar anexos e ficheiros de mídia, o [gatilho de armazenamento De Blob Azure](../azure-functions/functions-bindings-storage-blob.md) pode ser uma opção melhor.

Benefícios das Funções Azure: 

* **Orientado para o evento.** As Funções Azure são orientadas para eventos e podem ouvir um feed de mudança do Azure Cosmos DB. Isto significa que não precisas de criar lógica de escuta, ficas atento às mudanças que estás a ouvir. 

* **Sem limites.** As funções executam em paralelo e o serviço gira quantas precisar. Definiu os parâmetros.

* **Bom para tarefas rápidas.** O serviço gira novas instâncias de funções sempre que um evento dispara e as fecha assim que a função termina. Só pagas pelo tempo que as tuas funções estão a funcionar.

Se não tem a certeza se o Flow, as Aplicações Lógicas, as Funções Azure ou webJobs são os melhores para a sua implementação, consulte [Escolha entre Fluxo, Aplicações Lógicas, Funções e WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="next-steps"></a>Passos seguintes

Agora vamos ligar as funções Azure Cosmos DB e Azure de verdade: 

* [Criar um gatilho de Funções Azure para cosmos DB no portal Azure](https://aka.ms/cosmosdbtriggerportalfunc)
* [Criar um gatilho DE funções Azure HTTP com uma ligação de entrada Azure Cosmos DB](https://aka.ms/cosmosdbinputbind)
* [Ligações e gatilhos da Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md)