---
title: Padrões inteligentes de integração de contratos - Azure Blockchain Workbench
description: Visão geral dos padrões inteligentes de integração de contratos na pré-visualização do workbench Azure Blockchain.
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: mmercuri
ms.openlocfilehash: dae63e16356e825d3be31380df1648749e59d8bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015507"
---
# <a name="smart-contract-integration-patterns"></a>Padrões de integração de contratos inteligentes

Os contratos inteligentes representam frequentemente um fluxo de trabalho de negócios que precisa de se integrar com sistemas e dispositivos externos.

Os requisitos destes fluxos de trabalho incluem a necessidade de iniciar transações num livro-razão distribuído que inclua dados de um sistema, serviço ou dispositivo externos. Precisam também de que os sistemas externos reajam a eventos originários de contratos inteligentes num livro-razão distribuído.

A API REST e a integração de mensagens envia transações de sistemas externos para contratos inteligentes incluídos numa aplicação Azure Blockchain Workbench. Também envia notificações de eventos para sistemas externos com base em alterações que ocorrem dentro de uma aplicação.

Para cenários de integração de dados, o Azure Blockchain Workbench inclui um conjunto de pontos de vista de base de dados que combinam uma combinação de dados transacionais da blockchain e meta-dados sobre aplicações e contratos inteligentes.

Além disso, alguns cenários, como os relacionados com a cadeia de fornecimento ou os meios de comunicação social, podem também exigir a integração de documentos. Embora a Azure Blockchain Workbench não forneça chamadas de API para manusear documentos diretamente, os documentos podem ser incorporados numa aplicação blockchain. Esta secção também inclui este padrão.

Esta secção inclui os padrões identificados para implementar cada um destes tipos de integrações no seu final para soluções finais.

## <a name="rest-api-based-integration"></a>REST Integração baseada em API

As capacidades dentro da aplicação web gerada pela Azure Blockchain Workbench são expostas através da API REST. As capacidades incluem o upload, configuração e administração de aplicações do Azure Blockchain, o envio de transações para um livro de contabilidade distribuído e a consulta de metadados de aplicação e dados de contabilidade.

A API REST é utilizada principalmente para clientes interativos, tais como aplicações web, mobile e bot.

Esta secção analisa padrões focados nos aspetos da API REST que enviam transações para um livro de contabilidade distribuído e padrões que consultam dados sobre transações a partir da base de dados *off chain* da Azure Blockchain Workbench.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Envio de transações para um livro de contabilidade distribuído a partir de um sistema externo

A Azure Blockchain Workbench REST API envia pedidos autenticados para executar transações num livro-razão distribuído.

![Envio de transações para um livro-razão distribuído](./media/integration-patterns/send-transactions-ledger.png)

A execução de transações ocorre utilizando o processo anteriormente descrito, onde:

-   A aplicação externa autentica-se no Diretório Ativo Azure, abastado no âmbito da implantação da Azure Blockchain Workbench.
-   Os utilizadores autorizados recebem um token ao portador que pode ser enviado com pedidos à API.
-   As aplicações externas fazem chamadas para a API REST utilizando o token do portador.
-   A API REST embala o pedido como mensagem e envia-o para o Autocarro de Serviço. A partir daqui é recuperado, assinado e enviado para o livro de contabilidade distribuído apropriado.
-   A API REST faz um pedido à Azure Blockchain Workbench SQL DB para registar o pedido e estabelecer o estado atual de provisionamento.
-   O SQL DB devolve o estado de provisionamento e a chamada da API devolve o ID à aplicação externa que o chamou.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Consulta de metadados blockchain Workbench e transações de livros distribuídos

A Azure Blockchain Workbench REST API envia pedidos autenticados para consultar detalhes relacionados com a execução de contrato inteligente num livro de contabilidade distribuído.

![Consulta de metadados](./media/integration-patterns/querying-metadata.png)

A consulta ocorre utilizando o processo descrito anteriormente, onde:

1. A aplicação externa autentica-se no Diretório Ativo Azure, abastado no âmbito da implantação da Azure Blockchain Workbench.
2. Os utilizadores autorizados recebem um token ao portador que pode ser enviado com pedidos à API.
3. As aplicações externas fazem chamadas para a API REST utilizando o token do portador.
4. A API REST consulta os dados do pedido do SQL DB e devolve-os ao cliente.

## <a name="messaging-integration"></a>Integração de mensagens

A integração de mensagens facilita a interação com sistemas, serviços e dispositivos onde um sinal interativo não é possível ou desejável. A integração de mensagens centra-se em dois tipos de mensagens: mensagens que solicitam transações são executadas num livro-razão distribuído e eventos expostos por esse livro de registos quando as transações ocorreram.

A integração de mensagens centra-se na execução e monitorização de transações relacionadas com a criação de utilizadores, criação de contratos e execução de transações em contratos e é usada principalmente por sistemas sem *cabeça.*

Esta secção analisa padrões focados nos aspetos da API baseada em mensagens que enviam transações para um livro de contabilidade distribuído e padrões que representam mensagens de evento expostas pelo livro-razão distribuído subjacente.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Entrega de eventos unidirecciona de um contrato inteligente para um consumidor de eventos 

Neste cenário, um evento ocorre dentro de um contrato inteligente, por exemplo, uma alteração do Estado ou a execução de um tipo específico de transação. Este evento é transmitido através de uma Grade de Eventos para consumidores a jusante, e esses consumidores tomam as medidas apropriadas.

Um exemplo deste cenário é que, quando ocorre uma transação, um consumidor seria alertado e poderia tomar medidas, tais como registar a informação num SQL DB ou no Serviço Comum de Dados. Este cenário é o mesmo padrão que a Workbench segue para povoar a sua *cadeia off chain* SQL DB.

Outro seria se um contrato inteligente transite para um determinado estado, por exemplo, quando um contrato entra num *OutOfCompliance*. Quando esta mudança de estado acontecer, pode desencadear um alerta para ser enviado para o telemóvel de um administrador.

![Entrega de eventos unidirecciona](./media/integration-patterns/one-way-event-delivery.png)

Este cenário ocorre utilizando o processo descrito anteriormente, onde:

-   O contrato inteligente transita para um estado novo e envia um evento para o livro de contabilidade.
-   O livro-razão recebe e entrega o evento à Azure Blockchain Workbench.
-   A azure Blockchain Workbench é subscrita a eventos do livro de contabilidade e recebe o evento.
-   A Azure Blockchain Workbench publica o evento aos subscritores na Grelha de Eventos.
-   Os sistemas externos são subscritos na Grelha de Eventos, consomem a mensagem e tomam as ações apropriadas.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Entrega de um evento unidirecciona de uma mensagem de um sistema externo a um contrato inteligente

Há também um cenário que flui da direção oposta. Neste caso, um evento é gerado por um sensor ou um sistema externo e os dados desse evento devem ser enviados para um contrato inteligente.

Um exemplo comum é a entrega de dados dos mercados financeiros, por exemplo, os preços das mercadorias, ações ou obrigações a um contrato inteligente.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Entrega direta de uma bancada Azure Blockchain no formato esperado

Algumas aplicações são construídas para integrar com a Azure Blockchain Workbench e geram e enviam mensagens diretamente nos formatos esperados.

![Entrega direta](./media/integration-patterns/direct-delivery.png)

Esta entrega ocorre utilizando o processo anteriormente descrito, onde:

-   Um evento ocorre num sistema externo que desencadeia a criação de uma mensagem para a Azure Blockchain Workbench.
-   O sistema externo tem código escrito para criar esta mensagem num formato conhecido e envia-a diretamente para o Service Bus.
-   A azure Blockchain Workbench é subscrita a eventos do Service Bus e recupera a mensagem.
-   A Azure Blockchain Workbench inicia uma chamada para o livro de contabilidade, enviando dados do sistema externo para um contrato específico.
-   Ao receber a mensagem, o contrato transita para um novo Estado.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Entrega de uma mensagem num formato desconhecido da Azure Blockchain Workbench

Alguns sistemas não podem ser modificados para entregar mensagens nos formatos padrão utilizados pela Azure Blockchain Workbench. Nestes casos, os mecanismos existentes e os formatos de mensagens destes sistemas podem frequentemente ser utilizados. Especificamente, os tipos de mensagens nativas destes sistemas podem ser transformados usando Apps lógicas, funções Azure ou outro código personalizado para mapear para um dos formatos de mensagens padrão esperados.

![Formato de mensagem desconhecido](./media/integration-patterns/unknown-message-format.png)

Isto ocorre utilizando o processo descrito anteriormente, onde:

-   Um evento ocorre num sistema externo que desencadeia a criação de uma mensagem.
-   Uma Aplicação lógica ou código personalizado é usado para receber essa mensagem e transformá-la numa mensagem formatada padrão Azure Blockchain Workbench.
-   A Aplicação Lógica envia a mensagem transformada diretamente para o Service Bus.
-   A azure Blockchain Workbench é subscrita a eventos do Service Bus e recupera a mensagem.
-   A Azure Blockchain Workbench inicia uma chamada para o livro de contabilidade, enviando dados do sistema externo para uma função específica no contrato.
-   A função executa e normalmente modifica o estado. A mudança de Estado avança com o fluxo de trabalho do negócio refletido no contrato inteligente, permitindo que outras funções sejam agora executadas conforme apropriado.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Transição do controlo para um processo externo e aguarda conclusão

Há cenários em que um contrato inteligente deve parar a execução interna e passar para um processo externo. Esse processo externo seria então concluído, enviaria uma mensagem para o contrato inteligente, e a execução continuaria dentro do contrato inteligente.

#### <a name="transition-to-the-external-process"></a>Transição para o processo externo

Este padrão é tipicamente implementado usando a seguinte abordagem:

-   O contrato inteligente transita para um estado específico. Neste estado, não ou um número limitado de funções podem ser executadas até que um sistema externo tome uma ação desejada.
-   A mudança de estado surge como um evento para um consumidor a jusante.
-   O consumidor a jusante recebe o evento e desencadeia a execução de código externo.

![O diagrama mostra uma mudança de estado dentro do Contrato, fazendo com que um evento vá para o Livro Distribuído. Blockchain Workbench, em seguida, pega no evento e publica-o.](./media/integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Retorno do controlo do contrato inteligente

Dependendo da capacidade de personalizar o sistema externo, pode ou não ser capaz de entregar mensagens num dos formatos padrão que o Azure Blockchain Workbench espera. Com base na capacidade dos sistemas externos de gerar uma destas mensagens determinam qual dos dois caminhos de retorno seguintes é tomado.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Entrega direta de uma bancada Azure Blockchain no formato esperado

![O diagrama mostra uma mensagem A P I do Sistema Externo a ser captada pela Blockchain Workbench através do Service Bus. Blockchain Workbench envia então uma mensagem como uma transação para Distributed Ledger, em nome do agente. É passado para Contrato, onde causa uma mudança de estado.](./media/integration-patterns/direct-delivery.png)

Neste modelo, a comunicação ao contrato e posterior alteração do estado ocorre na sequência do processo anterior em que -

-   Ao alcançar a conclusão ou um marco específico na execução do código externo, um evento é enviado para o Service Bus ligado à Azure Blockchain Workbench.

-   Para sistemas que não podem ser adaptados diretamente para escrever uma mensagem que esteja em conformidade com as expectativas da API, é transformado.

-   O conteúdo da mensagem é embalado e enviado para uma função específica no contrato inteligente. Esta entrega é feita em nome do utilizador associado ao sistema externo.

-   A função executa e normalmente modifica o estado. A mudança de Estado avança com o fluxo de trabalho do negócio refletido no contrato inteligente, permitindo que outras funções sejam agora executadas conforme apropriado.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Entrega de uma mensagem num formato desconhecido da Azure Blockchain Workbench

![Formato de mensagem desconhecido](./media/integration-patterns/unknown-message-format.png)

Neste modelo em que uma mensagem num formato normalizado não pode ser enviada diretamente, a comunicação ao contrato e posterior alteração do estado ocorre na sequência do processo anterior em que:

1.  Ao alcançar a conclusão ou um marco específico na execução do código externo, um evento é enviado para o Service Bus ligado à Azure Blockchain Workbench.
2.  Uma Aplicação lógica ou código personalizado é usado para receber essa mensagem e transformá-la numa mensagem formatada padrão Azure Blockchain Workbench.
3.  A Aplicação Lógica envia a mensagem transformada diretamente para o Service Bus.
4.  A azure Blockchain Workbench é subscrita a eventos do Service Bus e recupera a mensagem.
5.  A Azure Blockchain Workbench inicia uma chamada para o livro de contabilidade, enviando dados do sistema externo para um contrato específico.
6. O conteúdo da mensagem é embalado e enviado para uma função específica no contrato inteligente. Esta entrega é feita em nome do utilizador associado ao sistema externo.
7.  A função executa e normalmente modifica o estado. A mudança de Estado avança com o fluxo de trabalho do negócio refletido no contrato inteligente, permitindo que outras funções sejam agora executadas conforme apropriado.

## <a name="iot-integration"></a>Integração ioT

Um cenário comum de integração é a inclusão de dados de telemetria recuperados de sensores num contrato inteligente. Com base em dados entregues por sensores, os contratos inteligentes poderiam tomar ações informadas e alterar o estado do contrato.

Por exemplo, se um camião que entrega medicamentos tiver a sua temperatura a subir até aos 110 graus, pode afetar a eficácia do medicamento e pode causar um problema de segurança pública se não for detetado e removido da cadeia de abastecimento. Se um condutor acelerasse o seu carro para 160 km/h, as informações dos sensores resultantes poderiam desencadear um cancelamento do seguro pelo seu segurador. Se o carro fosse alugado, os dados do GPS poderiam indicar quando o condutor saiu de uma geografia abrangida pelo seu contrato de aluguer e cobrar uma penalidade.

O desafio é que estes sensores possam estar a fornecer dados numa base constante e não é apropriado enviar todos estes dados para um contrato inteligente. Uma abordagem típica é limitar o número de mensagens enviadas para a blockchain enquanto entrega todas as mensagens a uma loja secundária. Por exemplo, entregar mensagens recebidas apenas a intervalo fixo, por exemplo, uma vez por hora, e quando um valor contido se encontra fora do intervalo acordado para um contrato inteligente. A verificação de valores que não são tolerados, assegura que os dados relevantes para a lógica de negócio dos contratos são recebidos e executados. Verificar o valor no intervalo confirma que o sensor ainda está a reportar. Todos os dados são enviados para uma loja de relatórios secundários para permitir relatórios, análises e machine learning mais amplos. Por exemplo, embora a obtenção de leituras de sensores para GPS possa não ser necessária a cada minuto para um contrato inteligente, eles podem fornecer dados interessantes para serem usados em relatórios ou rotas de mapeamento.

Na plataforma Azure, a integração com dispositivos é normalmente feita com o IoT Hub. O IoT Hub fornece o encaminhamento de mensagens com base no conteúdo e permite o tipo de funcionalidade descrita anteriormente.

![Mensagens IoT](./media/integration-patterns/iot.png)

O processo retrata um padrão:

-   Um dispositivo comunica diretamente ou através de uma porta de entrada de campo para o IoT Hub.
-   O IoT Hub recebe as mensagens e avalia as mensagens contra as rotas estabelecidas que verificam o conteúdo da mensagem, por exemplo. *O sensor reporta uma temperatura superior a 50 graus?*
-   O IoT Hub envia mensagens que satisfazem os critérios a um Autocarro de Serviço definido para a rota.
-   Uma Aplicação Lógica ou outro código ouve o Service Bus que o IoT Hub estabeleceu para a rota.
-   A Aplicação Lógica ou outro código recupera e transforma a mensagem num formato conhecido.
-   A mensagem transformada, agora num formato padrão, é enviada para o Service Bus para a Azure Blockchain Workbench.
-   A azure Blockchain Workbench é subscrita a eventos do Service Bus e recupera a mensagem.
-   A Azure Blockchain Workbench inicia uma chamada para o livro de contabilidade, enviando dados do sistema externo para um contrato específico.
-   Ao receber a mensagem, o contrato avalia os dados e pode alterar o estado com base no resultado dessa avaliação, por exemplo, para uma temperatura elevada, alterar o estado para *out of Compliance*.

## <a name="data-integration"></a>Integração de dados

Além da REST e da API baseada em mensagens, a Azure Blockchain Workbench também fornece acesso a um SQL DB povoado com met-dados de aplicação e contrato, bem como dados transacionais de livros distribuídos.

![Integração de dados](./media/integration-patterns/data-integration.png)

A integração de dados é bem conhecida:

-   A Azure Blockchain Workbench armazena metadados sobre aplicações, fluxos de trabalho, contratos e transações como parte do seu comportamento normal de funcionamento.
-   Sistemas ou ferramentas externos fornecem um ou mais diálogos para facilitar a recolha de informações sobre a base de dados, tais como nome do servidor de base de dados, nome da base de dados, tipo de autenticação, credenciais de login e quais as vistas da base de dados para utilizar.
-   As consultas são escritas contra pontos de vista de base de dados para facilitar o consumo a jusante por sistemas externos, serviços, relatórios, ferramentas de desenvolvimento e ferramentas de produtividade das empresas.

## <a name="storage-integration"></a>Integração de armazenamento

Muitos cenários podem exigir a necessidade de incorporar ficheiros atestáveis. Por várias razões, é inapropriado colocar ficheiros numa blockchain. Em vez disso, uma abordagem comum é executar um haxixe criptográfico (por exemplo, SHA-256) contra um ficheiro e partilhar esse haxixe num livro-razão distribuído. Executar o haxixe novamente em qualquer momento futuro deve devolver o mesmo resultado. Se o ficheiro for modificado, mesmo que apenas um pixel seja modificado numa imagem, o haxixe devolve um valor diferente.

![Integração de armazenamento](./media/integration-patterns/storage-integration.png)

O padrão pode ser implementado onde:

-   Um sistema externo persiste um ficheiro num mecanismo de armazenamento, como o Azure Storage.
-   Um haxixe é gerado com o ficheiro ou o ficheiro e metadados associados, como um identificador para o proprietário, o URL onde o ficheiro está localizado, etc.
-   O haxixe e quaisquer metadados são enviados para uma função num contrato inteligente, como *FileAdded*
-   No futuro, o ficheiro e os meta-dados podem ser novamente hashed e comparados com os valores armazenados no livro de contabilidade.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>Pré-requisitos para a implementação de padrões de integração utilizando as APIs REST e mensagem

Para facilitar a capacidade de um sistema ou dispositivo externo interagir com o contrato inteligente utilizando o REST ou a API de mensagem, deve ocorrer o seguinte -

1. No Diretório Ativo Azure para o consórcio, é criada uma conta que representa o sistema ou dispositivo externo.
2. Um ou mais contratos inteligentes apropriados para a sua aplicação Azure Blockchain Workbench têm funções definidas para aceitar os eventos do seu sistema ou dispositivo externo.
3. O ficheiro de configuração de aplicação para o seu contrato inteligente contém a função, que o sistema ou dispositivo é atribuído.
4. O ficheiro de configuração de aplicação para o seu contrato inteligente identifica em que estados esta função é chamada pela função definida.
5. O ficheiro de configuração de aplicação de aplicação e os seus contratos inteligentes são enviados para a Azure Blockchain Workbench.

Uma vez que a aplicação é carregada, a conta Azure Ative Directory para o sistema externo é atribuída ao contrato e à função associada.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Testar fluxos de integração do sistema externo antes do código de integração de escrita 

A integração com sistemas externos é um requisito fundamental de muitos cenários. É desejável poder validar o design de contrato inteligente antes ou em paralelo com o desenvolvimento do código para integrar com sistemas externos.

A utilização do Azure Ative Directory (Azure AD) pode acelerar consideravelmente a produtividade do desenvolvedor e o tempo para valorizar. Especificamente, a integração do código com um sistema externo pode demorar um tempo não trivial. Ao utilizar o Azure AD e a geração automática de UX by Azure Blockchain Workbench, pode permitir que os desenvolvedores inscrevam-se no Blockchain Workbench como o sistema externo e povoam valores do sistema externo através do UX. Pode desenvolver e validar rapidamente ideias numa prova de ambiente conceptual antes de ser escrito o código de integração para os sistemas externos.
