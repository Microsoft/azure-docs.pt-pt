---
title: Padrões inteligentes de integração de contratos - Bancada de Trabalho Azure Blockchain
description: Visão geral dos padrões inteligentes de integração de contratos na Pré-visualização da bancada de trabalho azure Blockchain.
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: mmercuri
ms.openlocfilehash: f9626edd5bd655e3de5d0f9648041faf832e3b84
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74325975"
---
# <a name="smart-contract-integration-patterns"></a>Padrões de integração de contratos inteligentes

Os contratos inteligentes representam frequentemente um fluxo de trabalho empresarial que precisa de se integrar com sistemas e dispositivos externos.

Os requisitos destes fluxos de trabalho incluem a necessidade de iniciar transações num livro-razão distribuído que inclua dados de um sistema externo, serviço ou dispositivo. Precisam também de que os sistemas externos reajam a eventos originários de contratos inteligentes num livro-razão distribuído.

A Rest API e a integração de mensagens enviam transações de sistemas externos para contratos inteligentes incluídos numa aplicação Azure Blockchain Workbench. Também envia notificações de eventos a sistemas externos com base em alterações que ocorrem dentro de uma aplicação.

Para cenários de integração de dados, a Azure Blockchain Workbench inclui um conjunto de visualizações de bases de dados que fundem uma combinação de dados transacionais da blockchain e meta-dados sobre aplicações e contratos inteligentes.

Além disso, alguns cenários, como os relacionados com a cadeia de fornecimento ou os meios de comunicação, podem também exigir a integração de documentos. Embora a Bancada de Trabalho da Azure Blockchain não forneça chamadas API para o manuseamento de documentos diretamente, os documentos podem ser incorporados numa aplicação blockchain. Esta secção também inclui este padrão.

Esta secção inclui os padrões identificados para implementar cada um destes tipos de integrações na sua extremidade para as soluções finais.

## <a name="rest-api-based-integration"></a>Integração baseada em REST API

As capacidades dentro da aplicação web gerada pela Azure Blockchain São expostas através da API REST. As capacidades incluem o upload, configuração e administração de aplicações da Bancada Azure Blockchain, o envio de transações para um livro-razão distribuído e a consulta de metadados de aplicações e dados de contabilidade.

A API REST é usada principalmente para clientes interativos, tais como aplicações web, mobile e bot.

Esta secção analisa padrões focados nos aspetos da API REST que enviam transações para um livro-razão distribuído e padrões que consultam dados sobre transações da base de dados SQL da cadeia SQL da *Azure* Blockchain Workbench.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Envio de transações para um livro-razão distribuído a partir de um sistema externo

A API da bancada de trabalho Azure Blockchain REST envia pedidos autenticados para executar transações num livro-razão distribuído.

![Envio de transações para um livro-razão distribuído](./media/integration-patterns/send-transactions-ledger.png)

A execução de transações ocorre utilizando o processo descrito anteriormente, onde:

-   A aplicação externa autentica-se no Diretório Ativo Azure, provisionado como parte da implantação da bancada azure Blockchain.
-   Os utilizadores autorizados recebem um símbolo do portador que pode ser enviado com pedidos à API.
-   Aplicações externas fazem chamadas para a API REST usando o token portador.
-   A Rest API embala o pedido como mensagem e envia-o para o Ônibus de Serviço. A partir daqui é recuperado, assinado e enviado para o livro de contabilidade distribuído apropriado.
-   A REST API faz um pedido à bancada de trabalho Azure Blockchain SQL DB para registar o pedido e estabelecer o estado de provisionamento atual.
-   O SQL DB devolve o estado de provisionamento e a chamada da API devolve o ID à aplicação externa que lhe chamou.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Consulta de metadados da bancada de trabalho blockchain e transações de livros distribuídos

A API da bancada de trabalho azure Blockchain REST envia pedidos autenticados para consulta de detalhes relacionados com a execução inteligente de contratos num livro-razão distribuído.

![Consulta de metadados](./media/integration-patterns/querying-metadata.png)

A consulta ocorre utilizando o processo descrito anteriormente, onde:

1. A aplicação externa autentica-se no Diretório Ativo Azure, provisionado como parte da implantação da bancada azure Blockchain.
2. Os utilizadores autorizados recebem um símbolo do portador que pode ser enviado com pedidos à API.
3. Aplicações externas fazem chamadas para a API REST usando o token portador.
4. A API REST consulta os dados para o pedido do SQL DB e devolve-os ao cliente.

## <a name="messaging-integration"></a>Integração de mensagens

A integração de mensagens facilita a interação com sistemas, serviços e dispositivos onde um inserimento interativo não é possível ou desejável. A integração de mensagens centra-se em dois tipos de mensagens: mensagens que solicitam transações sejam executadas num livro-razão distribuído, e eventos expostos por esse livro quando as transações ocorreram.

A integração de mensagens centra-se na execução e monitorização de transações relacionadas com a criação de utilizadores, criação de contratos e execução de transações em contratos e é usada principalmente por sistemas back-end *sem cabeça.*

Esta secção analisa padrões focados nos aspetos da API baseada na mensagem que enviam transações para um livro-razão distribuído e padrões que representam mensagens de eventoexpostas pelo livro-razão distribuído subjacente.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Entrega de eventos de ida de um contrato inteligente para um consumidor de eventos 

Neste cenário, ocorre um evento dentro de um contrato inteligente, por exemplo, uma alteração de Estado ou a execução de um tipo específico de transação. Este evento é transmitido através de uma Rede de Eventos para consumidores a jusante, e esses consumidores tomam as medidas apropriadas.

Um exemplo deste cenário é que quando uma transação ocorre, um consumidor seria alertado e poderia tomar medidas, tais como o registo da informação num Db SQL ou no Serviço Comum de Dados. Este cenário é o mesmo padrão que a Workbench segue para povoar a sua *cadeia* off SQL DB.

Outra seria se um contrato inteligente transitasse para um determinado estado, por exemplo, quando um contrato entra num *OutOfCompliance*. Quando esta mudança de estado acontecer, pode desencadear um alerta para ser enviado para o telemóvel de um administrador.

![Entrega de eventos de ida](./media/integration-patterns/one-way-event-delivery.png)

Este cenário ocorre utilizando o processo descrito anteriormente, onde:

-   O contrato inteligente transita para um estado novo e envia um evento para o livro de contabilidade.
-   O livro-razão recebe e entrega o evento à Bancada de Trabalho Azure Blockchain.
-   A Bancada de Trabalho Azure Blockchain é subscrita a eventos do livro-razão e recebe o evento.
-   A Azure Blockchain Workbench publica o evento aos subscritores da Grelha de Eventos.
-   Os sistemas externos são subscritos à Grelha de Eventos, consomem a mensagem e tomam as medidas apropriadas.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Entrega de uma mensagem de um sistema externo para um contrato inteligente

Há também um cenário que flui da direção oposta. Neste caso, um evento é gerado por um sensor ou um sistema externo e os dados desse evento devem ser enviados para um contrato inteligente.

Um exemplo comum é a entrega de dados dos mercados financeiros, por exemplo, dos preços das mercadorias, ações ou obrigações a um contrato inteligente.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Entrega direta de uma bancada azure blockchain no formato esperado

Algumas aplicações são construídas para integrar com a Bancada de Trabalho Azure Blockchain e geram diretamente e enviam mensagens nos formatos esperados.

![Entrega direta](./media/integration-patterns/direct-delivery.png)

Esta entrega ocorre utilizando o processo descrito anteriormente, onde:

-   Um evento ocorre num sistema externo que desencadeia a criação de uma mensagem para a Bancada de Trabalho Azure Blockchain.
-   O sistema externo tem código escrito para criar esta mensagem num formato conhecido e envia-a diretamente para o Bus de Serviço.
-   A Bancada de Trabalho Azure Blockchain é subscrita a eventos do Bus de Serviço e recupera a mensagem.
-   A Azure Blockchain Workbench inicia uma chamada para o livro de contabilidade, enviando dados do sistema externo para um contrato específico.
-   Após a receção da mensagem, o contrato transita para um estado novo.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Entrega de uma mensagem num formato desconhecido da bancada Azure Blockchain

Alguns sistemas não podem ser modificados para entregar mensagens nos formatos padrão utilizados pela Bancada de Trabalho Azure Blockchain. Nestes casos, os mecanismos existentes e os formatos de mensagens destes sistemas podem frequentemente ser utilizados. Especificamente, os tipos de mensagens nativas destes sistemas podem ser transformados usando Aplicações Lógicas, Funções Azure ou outro código personalizado para mapear para um dos formatos de mensagens padrão esperados.

![Formato de mensagem desconhecida](./media/integration-patterns/unknown-message-format.png)

Isto ocorre utilizando o processo descrito anteriormente, onde:

-   Um evento ocorre num sistema externo que desencadeia a criação de uma mensagem.
-   Uma Aplicação Lógica ou código personalizado é usado para receber essa mensagem e transformá-la numa mensagem formada padrão de Azure Blockchain.
-   A Aplicação Lógica envia a mensagem transformada diretamente para o Ônibus de Serviço.
-   A Bancada de Trabalho Azure Blockchain é subscrita a eventos do Bus de Serviço e recupera a mensagem.
-   A Azure Blockchain Workbench inicia uma chamada para o livro de contabilidade, enviando dados do sistema externo para uma função específica do contrato.
-   A função executa e tipicamente modifica o estado. A mudança de Estado avança o fluxo de trabalho empresarial refletido no contrato inteligente, permitindo que outras funções sejam agora executadas conforme apropriado.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Controlo de transição para um processo externo e aguardar conclusão

Há cenários em que um contrato inteligente deve parar a execução interna e passar para um processo externo. Esse processo externo completaria então, enviaria uma mensagem para o contrato inteligente, e a execução continuaria dentro do contrato inteligente.

#### <a name="transition-to-the-external-process"></a>Transição para o processo externo

Este padrão é normalmente implementado utilizando a seguinte abordagem:

-   O contrato inteligente transita para um estado específico. Neste estado, não podem ser executadas nenhuma ou um número limitado de funções até que um sistema externo tome uma ação desejada.
-   A mudança de estado surge como um evento para um consumidor a jusante.
-   O consumidor a jusante recebe o evento e desencadeia a execução de código externo.

![Controlo de transição para o processo externo](./media/integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Devolução do controlo do contrato inteligente

Dependendo da capacidade de personalizar o sistema externo, pode ou não ser capaz de entregar mensagens num dos formatos padrão que a Azure Blockchain Workbench espera. Com base na capacidade dos sistemas externos de gerar uma destas mensagens, determina qual dos dois caminhos de retorno seguintes é tomado.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Entrega direta de uma bancada azure blockchain no formato esperado

![](./media/integration-patterns/direct-delivery.png)

Neste modelo, a comunicação ao contrato e posterior alteração do Estado ocorre na sequência do processo anterior em que -

-   Ao atingir a conclusão ou um marco específico na execução de código externo, um evento é enviado para o Bus de Serviço ligado à Bancada de Trabalho Azure Blockchain.

-   Para sistemas que não podem ser adaptados diretamente para escrever uma mensagem que se conforme com as expectativas da API, é transformada.

-   O conteúdo da mensagem é embalado e enviado para uma função específica no contrato inteligente. Esta entrega é feita em nome do utilizador associado ao sistema externo.

-   A função executa e tipicamente modifica o estado. A mudança de Estado avança o fluxo de trabalho empresarial refletido no contrato inteligente, permitindo que outras funções sejam agora executadas conforme apropriado.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Entrega de uma mensagem num formato desconhecido da bancada Azure Blockchain

![Formato de mensagem desconhecida](./media/integration-patterns/unknown-message-format.png)

Neste modelo em que uma mensagem num formato padrão não pode ser enviada diretamente, a comunicação ao contrato e posterior alteração do Estado ocorre na sequência do processo anterior em que:

1.  Ao atingir a conclusão ou um marco específico na execução de código externo, um evento é enviado para o Bus de Serviço ligado à Bancada de Trabalho Azure Blockchain.
2.  Uma Aplicação Lógica ou código personalizado é usado para receber essa mensagem e transformá-la numa mensagem formada padrão de Azure Blockchain.
3.  A Aplicação Lógica envia a mensagem transformada diretamente para o Ônibus de Serviço.
4.  A Bancada de Trabalho Azure Blockchain é subscrita a eventos do Bus de Serviço e recupera a mensagem.
5.  A Azure Blockchain Workbench inicia uma chamada para o livro de contabilidade, enviando dados do sistema externo para um contrato específico.
6. O conteúdo da mensagem é embalado e enviado para uma função específica no contrato inteligente. Esta entrega é feita em nome do utilizador associado ao sistema externo.
7.  A função executa e tipicamente modifica o estado. A mudança de Estado avança o fluxo de trabalho empresarial refletido no contrato inteligente, permitindo que outras funções sejam agora executadas conforme apropriado.

## <a name="iot-integration"></a>Integração ioT

Um cenário comum de integração é a inclusão de dados de telemetria recuperados de sensores num contrato inteligente. Com base em dados entregues por sensores, os contratos inteligentes poderiam tomar medidas informadas e alterar o estado do contrato.

Por exemplo, se um camião que entrega o medicamento tiver a sua temperatura elevada para 110 graus, pode ter impacto na eficácia do medicamento e pode causar um problema de segurança pública se não for detetado e removido da cadeia de abastecimento. Se um condutor acelerar o seu carro a 160 km/h, as informações resultantes do sensor podem desencadear o cancelamento do seguro pelo seu segurador. Se o carro fosse um carro alugado, os dados do GPS poderiam indicar quando o condutor saiu de uma geografia coberta pelo seu contrato de aluguer e cobrar uma penalidade.

O desafio é que estes sensores possam estar a fornecer dados constantemente e não é apropriado enviar todos estes dados para um contrato inteligente. Uma abordagem típica é limitar o número de mensagens enviadas para a blockchain enquanto entrega todas as mensagens a uma loja secundária. Por exemplo, entregar mensagens recebidas apenas com intervalo fixo, por exemplo, uma vez por hora, e quando um valor contido cai fora de um intervalo acordado para um contrato inteligente. A verificação de valores que não sejam tolerâncias, assegura que os dados relevantes para a lógica empresarial dos contratos são recebidos e executados. Verificar o valor ao intervalo confirma que o sensor ainda está a reportar. Todos os dados são enviados para uma loja secundária de relatórios para permitir relatórios mais amplos, análises e machine learning. Por exemplo, ao obter leituras de sensores para GPS pode não ser necessário a cada minuto para um contrato inteligente, eles podem fornecer dados interessantes para serem usados em relatórios ou rotas de mapeamento.

Na plataforma Azure, a integração com dispositivos é normalmente feita com o IoT Hub. O IoT Hub fornece o encaminhamento de mensagens com base no conteúdo, e permite o tipo de funcionalidade descrito anteriormente.

![Mensagens IoT](./media/integration-patterns/iot.png)

O processo retrata um padrão:

-   Um dispositivo comunica diretamente ou através de uma porta de entrada de campo para o IoT Hub.
-   O IoT Hub recebe as mensagens e avalia as mensagens contra as rotas estabelecidas que verificam o conteúdo da mensagem, por exemplo. *O sensor reporta uma temperatura superior a 50 graus?*
-   O IoT Hub envia mensagens que satisfazem os critérios para um ônibus de serviço definido para a rota.
-   Uma Aplicação Lógica ou outro código ouve o Ônibus de serviço que o IoT Hub estabeleceu para a rota.
-   A Aplicação Lógica ou outro código recupera e transforma a mensagem num formato conhecido.
-   A mensagem transformada, agora em formato padrão, é enviada para o Bus de Serviço para a Bancada de Trabalho Azure Blockchain.
-   A Bancada de Trabalho Azure Blockchain é subscrita a eventos do Bus de Serviço e recupera a mensagem.
-   A Azure Blockchain Workbench inicia uma chamada para o livro de contabilidade, enviando dados do sistema externo para um contrato específico.
-   Após a receção da mensagem, o contrato avalia os dados e pode alterar o Estado com base no resultado dessa avaliação, por exemplo, para uma temperatura elevada, alterar o Estado para *Fora de Conformidade*.

## <a name="data-integration"></a>Integração de dados

Além do REST e da API baseada em mensagens, a Azure Blockchain Workbench também fornece acesso a um SQL DB povoado com meta-dados de aplicação e contrato, bem como dados transacionais de livros de contabilidade distribuídos.

![Integração de dados](./media/integration-patterns/data-integration.png)

A integração de dados é bem conhecida:

-   A Azure Blockchain Workbench armazena metadados sobre aplicações, fluxos de trabalho, contratos e transações como parte do seu comportamento normal de operação.
-   Sistemas ou ferramentas externos fornecem um ou mais diálogos para facilitar a recolha de informações sobre a base de dados, tais como nome do servidor de base de dados, nome da base de dados, tipo de autenticação, credenciais de login e que pontos de vista de base de dados a utilizar.
-   As consultas são escritas contra as vistas da base de dados SQL para facilitar o consumo a jusante por sistemas externos, serviços, relatórios, ferramentas de desenvolvimento e ferramentas de produtividade da empresa.

## <a name="storage-integration"></a>Integração de armazenamento

Muitos cenários podem exigir a necessidade de incorporar ficheiros attáveis. Por várias razões, é inapropriado colocar ficheiros numa blockchain. Em vez disso, uma abordagem comum é executar um haxixe criptográfico (por exemplo, SHA-256) contra um ficheiro e partilhar que haxixe em um livro-razão distribuído. Realizar o hash novamente em qualquer momento futuro deve devolver o mesmo resultado. Se o ficheiro for modificado, mesmo que apenas um pixel seja modificado numa imagem, o haxixe devolve um valor diferente.

![Integração de armazenamento](./media/integration-patterns/storage-integration.png)

O padrão pode ser implementado onde:

-   Um sistema externo persiste num sistema de armazenamento, como o Armazenamento Azure.
-   Um haxixe é gerado com o ficheiro ou o ficheiro e metadados associados, como um identificador para o proprietário, o URL onde o ficheiro está localizado, etc.
-   O haxixe e quaisquer metadados são enviados para uma função num contrato inteligente, como *FileAdded*
-   No futuro, o ficheiro e os metadados podem voltar a ser hashed e comparados com os valores armazenados no livro de contabilidade.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>Pré-requisitos para a implementação de padrões de integração utilizando o REST e as APIs de mensagem

Para facilitar a capacidade de um sistema externo ou dispositivo interagir com o contrato inteligente utilizando o REST ou a mensagem API, deve ocorrer o seguinte -

1. No Diretório Ativo Azure para o consórcio, é criada uma conta que representa o sistema ou dispositivo externo.
2. Um ou mais contratos inteligentes apropriados para a sua aplicação Azure Blockchain Workbench têm funções definidas para aceitar os eventos do seu sistema externo ou dispositivo.
3. O ficheiro de configuração da aplicação para o seu contrato inteligente contém a função, que o sistema ou dispositivo é atribuído.
4. O ficheiro de configuração da aplicação para o seu contrato inteligente identifica em que afirma que esta função é chamada pela função definida.
5. O ficheiro de configuração da Aplicação e os seus contratos inteligentes são enviados para a bancada de trabalho da Azure Blockchain.

Uma vez que a aplicação é enviada, a conta azure Ative Directy para o sistema externo é atribuída ao contrato e à função associada.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Testar fluxos de integração de sistemas externos antes da escrita código de integração 

Integrar-se com sistemas externos é um requisito fundamental de muitos cenários. É desejável poder validar o design inteligente de contratos antes ou em paralelo com o desenvolvimento do código para integrar com sistemas externos.

A utilização do Azure Ative Directory (Azure AD) pode acelerar consideravelmente a produtividade e o tempo de valorização do desenvolvedor. Especificamente, a integração de códigos com um sistema externo pode demorar um período de tempo não trivial. Ao utilizar o Azure AD e a geração automática de UX pela Azure Blockchain Workbench, pode permitir que os desenvolvedores inscrevam-se na Bancada de Trabalho blockchain como sistema externo e povoam valores do sistema externo através do UX. Pode desenvolver e validar rapidamente ideias numa prova de ambiente conceptual antes de ser escrito código de integração para os sistemas externos.
