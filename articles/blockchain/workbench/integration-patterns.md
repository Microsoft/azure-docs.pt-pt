---
title: Padrões de integração do contrato inteligentes no Azure Blockchain Workbench
description: Descrição geral dos padrões de integração de contrato inteligente no Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/09/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: bd53ae3346882cf20ae7464548fa9ef2c0329f05
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65957020"
---
# <a name="smart-contract-integration-patterns"></a>Padrões de integração do contrato inteligente

Contratos inteligentes, muitas vezes, representam um fluxo de trabalho de empresas que precisam para integrar com sistemas externos e dispositivos.

Os requisitos desses fluxos de trabalho incluem a necessidade de iniciar transações num livro razão distribuído que incluem dados a partir de um sistema externo, o serviço ou o dispositivo. Eles também precisam ter reagir a eventos provenientes de contratos inteligentes num livro razão distribuído de sistemas externos.

A REST API e a integração de mensagens envia transações de sistemas externos para contratos inteligentes incluídos num aplicativo do Azure Blockchain Workbench. Também envia notificações de eventos para os sistemas externos com base nas alterações que ocorrem dentro de um aplicativo.

Para cenários de integração de dados, o Azure Blockchain Workbench inclui um conjunto de vistas de base de dados que uma combinação de dados transacionais do blockchain e metadados sobre as aplicações e contratos inteligentes de intercalação.

Além disso, alguns cenários, tais como aqueles relacionados a fornecer suporte de dados, ou de cadeia também podem exigir a integração de documentos. Enquanto o Azure Blockchain Workbench não fornece a chamadas de API para a manipulação de documentos diretamente, os documentos podem ser incorporados num aplicativo de blockchain. Esta secção também inclui esse padrão.

Esta secção inclui os padrões identificados para a implementação de cada um desses tipos de integrações nas suas soluções de ponta a ponta.

## <a name="rest-api-based-integration"></a>Integração com base na API do REST

Capacidades da aplicação web do Azure Blockchain Workbench gerado são expostas através da API REST. As capacidades incluem o carregamento do Azure Blockchain Workbench, a configuração e a administração de aplicações, enviando as transações para um livro razão distribuído e a consulta dos dados de metadados e contábeis da aplicação.

A API REST é usada principalmente para clientes interativos, como web, móveis e aplicações de bot.

Esta secção analisa padrões centradas nos aspetos da REST API que enviam transações para um livro razão distribuído e padrões esses dados de consulta sobre transações do Azure Blockchain Workbench *desativado cadeia* base de dados SQL.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Enviando as transações para um livro razão distribuído de um sistema externo

A API de REST do Azure Blockchain Workbench envia pedidos autenticados para executar transações num livro razão distribuído.

![Enviando as transações para um livro razão distribuído](./media/integration-patterns/send-transactions-ledger.png)

Executar transações ocorre usando o processo descrito anteriormente, onde:

-   O aplicativo externo autentica para o Azure Active Directory aprovisionada como parte da implementação do Azure Blockchain Workbench.
-   Os utilizadores autorizados recebem um token de portador que pode ser enviado com pedidos para a API.
-   Aplicativos externos fazem chamadas para a API de REST com o token de portador.
-   A API REST de pacotes o pedido como uma mensagem e envia-o para o Service Bus. Aqui é obtida, assinado e enviado para o livro razão distribuído apropriado.
-   A API REST faz um pedido para o Blockchain Workbench BD SQL do Azure para registar o pedido e estabelecer o atual estado de aprovisionamento.
-   A BD SQL devolve o estado de aprovisionamento e a chamada de API devolve o ID para a aplicação externa que o chamou.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Consultar os metadados de Blockchain Workbench e transações do razão distribuído

A API de REST do Azure Blockchain Workbench envia pedidos autenticados para detalhes de consulta relacionados com a execução de contrato inteligentes num livro razão distribuído.

![Consultar metadados](./media/integration-patterns/querying-metadata.png)

Consultar ocorre usando o processo descrito anteriormente, onde:

1. O aplicativo externo autentica para o Azure Active Directory aprovisionada como parte da implementação do Azure Blockchain Workbench.
2. Os utilizadores autorizados recebem um token de portador que pode ser enviado com pedidos para a API.
3. Aplicativos externos fazem chamadas para a API de REST com o token de portador.
4. A API REST consulta os dados para o pedido da BD SQL e a devolve para o cliente.

## <a name="messaging-integration"></a>Integração de mensagens

Integração de mensagens facilita a interação com sistemas, serviços e dispositivos em que um interativo início de sessão não é possível ou desejável. Integração de mensagens se concentra em dois tipos de mensagens: mensagens de solicitação de transações ser executado num livro razão distribuído e eventos expostos por essa razão, quando efetuada transações.

Integração de mensagens se concentra na execução e monitorização de transações relacionadas com a criação do utilizador, a criação de contrato e execução de transações sobre contratos e é utilizado principalmente pelo *sem periféricos* sistemas back-end.

Esta secção analisa padrões centradas nos aspetos de padrões que representam mensagens de eventos expostas pelo livro razão distribuído subjacente e API baseada em mensagens que enviam transações para um livro razão distribuído.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Entrega de eventos unidirecional de um contrato inteligente para um consumidor de eventos 

Neste cenário, ocorre um evento dentro de um contrato inteligente, por exemplo, uma alteração de estado ou a execução de um tipo específico de transação. Este evento é de difusão por meio de uma grelha de eventos para os consumidores de downstream e esses clientes, em seguida, executar as ações apropriadas.

Um exemplo deste cenário é que, quando ocorre uma transação, um consumidor poderia ser alertado e poderia levar a ação, tal como gravar as informações numa BD SQL ou o Common Data Service. Este cenário é o mesmo padrão que se segue Bancada de trabalho para preencher sua *desativado cadeia* BD SQL.

Outra seria se um contrato inteligente faz a transição para um estado específico, por exemplo, quando um contrato entra num *OutOfCompliance*. Quando esta alteração de estado ocorre, ele poderia acionar um alerta para serem enviados para o telemóvel de um administrador.

![Entrega de eventos unidirecional](./media/integration-patterns/one-way-event-delivery.png)

Este cenário ocorre usando o processo descrito anteriormente, onde:

-   O contrato inteligente faz a transição para um novo Estado e envia um evento ao razão.
-   A razão recebe e entrega o evento para o Azure Blockchain Workbench.
-   O Azure Blockchain Workbench está subscrita em eventos da razão e recebe o evento.
-   O Azure Blockchain Workbench publica o evento, aos subscritores do Event Grid.
-   Sistemas externos estão a subscrever o Event Grid, consumir a mensagem e tomar as ações adequadas.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Entrega de eventos unidirecional de uma mensagem de um sistema externo para um contrato inteligente

Também é um cenário que flui na direção oposta. Neste caso, um evento é gerado por um sensor ou um sistema externo e os dados desse evento devem ser enviados para um contrato inteligente.

Um exemplo comum é a entrega de dados a partir de mercados financeiros, por exemplo, os preços dos produtos, Estoque ou obrigações, para um contrato inteligente.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Entrega direta de um Azure Blockchain Workbench no formato esperado

Alguns aplicativos são criados para integrar com o Azure Blockchain Workbench e diretamente gera e enviar mensagens nos formatos esperados.

![Entrega direta](./media/integration-patterns/direct-delivery.png)

Este entrega ocorre usando o processo descrito anteriormente, onde:

-   Um evento ocorre num sistema externo que aciona a criação de uma mensagem para o Azure Blockchain Workbench.
-   O sistema externo tem código escrito para criar esta mensagem num formato conhecido e envia-os diretamente para o Service Bus.
-   O Azure Blockchain Workbench está subscrita em eventos do Service Bus e obtém a mensagem.
-   O Azure Blockchain Workbench inicia uma chamada ao razão, enviar dados a partir do sistema externo para um contrato específico.
-   Ao receber a mensagem, o contrato faz a transição para um novo Estado.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Entrega de uma mensagem num formato desconhecido para o Azure Blockchain Workbench

Alguns sistemas não podem ser modificados para entregar mensagens nos formatos padrão utilizados pelo Azure Blockchain Workbench. Nesses casos, a mecanismos existentes e a mensagem formatos a partir desses sistemas, muitas vezes, podem ser utilizados. Especificamente, os tipos de mensagem nativa destes sistemas podem ser transformados com Logic Apps, funções do Azure ou outros códigos personalizados para mapear para um padrão esperados de formatos de mensagens.

![Formato de mensagem desconhecida](./media/integration-patterns/unknown-message-format.png)

Isso ocorre usando o processo descrito anteriormente, onde:

-   Um evento ocorre num sistema externo que aciona a criação de uma mensagem.
-   Uma aplicação lógica ou o código personalizado é utilizado para receber essa mensagem e transformá-los numa mensagem formatada padrão do Azure Blockchain Workbench.
-   A aplicação lógica envia a mensagem transformada diretamente para o Service Bus.
-   O Azure Blockchain Workbench está subscrita em eventos do Service Bus e obtém a mensagem.
-   O Azure Blockchain Workbench inicia uma chamada ao razão, enviar dados de sistema externo para uma função específica no contrato.
-   A função é executada e normalmente modifica o estado. A alteração de estado, avance para o fluxo de trabalho do negócio refletido no contrato de inteligente, permitindo que outras funções para ser executado agora conforme apropriado.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Fazendo a transição de controle para um externo processar e aguardar a conclusão

Existem cenários onde um contrato inteligente tem de parar a execução interna e mão fora de um processo externo. Que processo externo, em seguida, precisaria completar, envie uma mensagem para o contrato inteligente e a execução, em seguida, iria continuar dentro do contrato inteligente.

#### <a name="transition-to-the-external-process"></a>Transição para o processo externo

Este padrão é normalmente implementado com a seguinte abordagem:

-   As transições de contrato inteligente para um estado específico. Neste estado, de qualquer não ou um número limitado de funções pode ser executado até que um sistema externo demora a ação desejada.
-   A alteração de estado é mostrada como um evento para um consumidor de downstream.
-   O consumidor downstream recebe o evento e aciona a execução de código externos.

![Controle de transição para o processo externo](./media/integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Retorno do controle do contrato inteligente

Consoante a capacidade de personalizar o sistema externo, pode ou não ser capaz de entrega de mensagens em um dos formatos padrão que espera de Azure Blockchain Workbench. Com base na capacidade de sistemas externos para gerar um dos seguintes mensagens de determinam qual dos dois seguintes devolver caminhos está a ser utilizado.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Entrega direta de um Azure Blockchain Workbench no formato esperado

![](./media/integration-patterns/direct-delivery.png)

Nesse modelo, a comunicação com o contrato e alteração de estado subseqüentes ocorre após o processo anterior em que -

-   Quando atingir a conclusão ou um marco específico na execução do código externo, é enviado um evento para o barramento de serviço ligado ao Azure Blockchain Workbench.

-   Para sistemas que não podem ser adaptados diretamente para gravar uma mensagem que está em conformidade com as expectativas da API, ela é transformada.

-   O conteúdo da mensagem é empacotado e enviado para uma função específica no contrato inteligente. Este entrega é efetuada em nome do utilizador associado ao sistema externo.

-   A função é executada e normalmente modifica o estado. A alteração de estado, avance para o fluxo de trabalho do negócio refletido no contrato de inteligente, permitindo que outras funções para ser executado agora conforme apropriado.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Entrega de uma mensagem num formato desconhecido para o Azure Blockchain Workbench

![Formato de mensagem desconhecida](./media/integration-patterns/unknown-message-format.png)

Neste modelo, em que uma mensagem num formato padrão não pode ser enviada diretamente, a comunicação com o contrato e alteração de estado subseqüentes ocorre após o anterior processar where:

1.  Quando atingir a conclusão ou um marco específico na execução do código externo, é enviado um evento para o barramento de serviço ligado ao Azure Blockchain Workbench.
2.  Uma aplicação lógica ou o código personalizado é utilizado para receber essa mensagem e transformá-los numa mensagem formatada padrão do Azure Blockchain Workbench.
3.  A aplicação lógica envia a mensagem transformada diretamente para o Service Bus.
4.  O Azure Blockchain Workbench está subscrita em eventos do Service Bus e obtém a mensagem.
5.  O Azure Blockchain Workbench inicia uma chamada ao razão, enviar dados a partir do sistema externo para um contrato específico.
6. O conteúdo da mensagem é empacotado e enviado para uma função específica no contrato inteligente. Este entrega é efetuada em nome do utilizador associado ao sistema externo.
7.  A função é executada e normalmente modifica o estado. A alteração de estado, avance para o fluxo de trabalho do negócio refletido no contrato de inteligente, permitindo que outras funções para ser executado agora conforme apropriado.

## <a name="iot-integration"></a>Integração de IoT

Um cenário de integração comum é a inclusão de dados de telemetria obtidos a partir de sensores num contrato inteligente. Com base nos dados fornecidos por sensores, contratos inteligentes podem tomar ações informadas e alterar o estado do contrato.

Por exemplo, se um camião fornecendo medicina tivesse a temperatura soar como graus de 110,-lo pode afetar a eficácia da medicina e pode provocar um problema de segurança pública se não for detetado e removida da cadeia de fornecimento. Se um driver acelerada carro para 100 milhas por hora, as informações do sensor resultante poderiam acionar um cancelamento de seguros pelo seu fornecedor de seguros. Se o carro era um carro de aluguel, os dados GPS poderiam indicar quando o driver correu fora de uma geografia abrangida pelo contrato de aluguel e cobrar uma penalidade.

O desafio é que esses sensores podem ser entregar dados de forma constante e não é adequado enviar todos esses dados para um contrato inteligente. Uma abordagem típica é limitar o número de mensagens enviadas para o blockchain ao mesmo tempo, todas as mensagens para um armazenamento secundário. Por exemplo, entregar as mensagens recebidas no apenas intervalo fixo, por exemplo, uma vez por hora, e quando um valor contido esteja fora um acordado após o intervalo para um contrato inteligente. A verificar os valores que estão fora das tolerâncias, garante que os dados relevantes para a lógica de negócios de contratos são recebidos e executados. Verificar o valor no intervalo confirma que o sensor ainda está a comunicar. Todos os dados é enviada para um arquivo de relatórios secundário para ativar a análise de relatório, mais abrangente e machine learning. Por exemplo, enquanto a obter as leituras dos sensores para GPS talvez não sejam necessária para um contrato inteligente a cada minuto, eles fornecem dados interessantes a ser utilizados em relatórios ou rotas de mapeamento.

Na plataforma do Azure, a integração com dispositivos normalmente é feita com o IoT Hub. IoT Hub fornece encaminhamento de mensagens com base no conteúdo e permite que o tipo de funcionalidade descrita anteriormente.

![Mensagens de IoT](./media/integration-patterns/iot.png)

O processo ilustra um padrão:

-   Um dispositivo comunica diretamente ou através de um gateway de campo para o IoT Hub.
-   O IoT Hub recebe as mensagens e avalia as mensagens de rotas estabelecidas que verificar o conteúdo da mensagem, por exemplo. *O sensor informa uma temperatura superior a 50 graus?*
-   O IoT Hub envia as mensagens que cumpram os critérios a um barramento de serviço definidos para a rota.
-   Uma aplicação lógica ou outros códigos escuta para o Service Bus que estabeleceu o IoT Hub para a rota.
-   A aplicação lógica ou outro código recupera e transformar a mensagem para um formato conhecido.
-   A mensagem transformada, agora num formato padrão, é enviada para o Service Bus para o Azure Blockchain Workbench.
-   O Azure Blockchain Workbench está subscrita em eventos do Service Bus e obtém a mensagem.
-   O Azure Blockchain Workbench inicia uma chamada ao razão, enviar dados a partir do sistema externo para um contrato específico.
-   Ao receber a mensagem, o contrato avalia os dados e pode alterar o estado com base no resultado da avaliação, por exemplo, para uma temperatura máxima, altere o estado para *fora de conformidade*.

## <a name="data-integration"></a>Integração de dados

Além de REST e API baseada em mensagens, o Azure Blockchain Workbench também fornece acesso para uma BD SQL preenchido com os metadados de aplicações e contrato, bem como os dados transacionais de livros razão distribuídos.

![Integração de dados](./media/integration-patterns/data-integration.png)

A integração de dados é bem conhecida:

-   O Azure Blockchain Workbench armazena os metadados sobre as aplicações, fluxos de trabalho, contratos e transações como parte de seu comportamento de funcionamento normal.
-   Sistemas externos ou ferramentas fornecem uma ou mais caixas de diálogo para facilitar a recolha de informações sobre a base de dados, como o nome do servidor de base de dados, o nome da base de dados, o tipo de autenticação, as credenciais de início de sessão e vistas de base de dados para utilizar.
-   As consultas são escritas em relação a modos de exibição do SQL da base de dados para facilitar a jusante consumo por sistemas externos, serviços, relatórios, ferramentas de programação e ferramentas de produtividade empresarial.

## <a name="storage-integration"></a>Integração de armazenamento

Muitos cenários podem exigir a necessidade de incorporar ficheiros attestable. Por vários motivos, que não é apropriado colocar ficheiros num blockchain. Em vez disso, uma abordagem comum é executar um hash criptográfico (por exemplo, SHA-256) num arquivo e compartilhar esse hash num livro razão distribuído. Efetuar o hash novamente em qualquer altura futura deverá devolver o mesmo resultado. Se o ficheiro é modificado, mesmo que apenas um pixel é modificado numa imagem, o hash devolve um valor diferente.

![Integração de armazenamento](./media/integration-patterns/storage-integration.png)

O padrão pode ser implementado em que:

-   Um sistema externo persiste um arquivo num mecanismo de armazenamento, como o armazenamento do Azure.
-   Um hash é gerado com o ficheiro ou o ficheiro e metadados associados, como um identificador para o proprietário, o URL em que o ficheiro está localizado, etc.
-   O hash e todos os metadados são enviados para uma função num contrato inteligente, como *FileAdded*
-   No futuro, o ficheiro e metadados podem ser protegido por hash novamente e comparados com os valores armazenados no razão.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>Pré-requisitos para a implementação de padrões de integração com o REST e APIs de mensagem

Para facilitar a capacidade para um sistema externo ou dispositivo de interagir com o contrato inteligente através de REST ou de uma mensagem de API, deve ocorrer o seguinte-

1. No Azure Active Directory para o consortium, é criada uma conta que representa o sistema externo ou dispositivo.
2. Um ou mais contratos inteligentes apropriados para a sua aplicação do Azure Blockchain Workbench tem funções definidas para aceitar os eventos do seu sistema externo ou dispositivo.
3. O ficheiro de configuração de aplicação para o seu contrato inteligente contém a função, o que o sistema ou o dispositivo é atribuído.
4. O ficheiro de configuração de aplicação para o seu contrato inteligente identifica no que afirma que esta função é chamada pela função de definidos.
5. O ficheiro de configuração do aplicativo e seus contratos inteligentes são carregados para o Azure Blockchain Workbench.

Depois do aplicativo é carregado, a conta do Azure Active Directory para o sistema externo é atribuída para o contrato e a função associada.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Teste de fluxos de integração de sistema externo antes de escrever código de integração 

A integração com sistemas externos é um requisito essencial de muitos cenários. É desejável para validar o design de contratos inteligentes anterior ou em paralelo para o desenvolvimento de código para integrar com sistemas externos.

A utilização do Azure Active Directory (Azure AD) pode acelerar bastante produtividade do desenvolvedor e a hora para o valor. Especificamente, a integração de código com um sistema externo pode demorar uma quantidade de tempo. Ao utilizar o Azure AD e a geração automática de experiência do Usuário pelo Azure Blockchain Workbench, pode permitir que os desenvolvedores a iniciar sessão na bancada de trabalho de Blockchain como o sistema externo e preencher os valores do sistema externo através da experiência do usuário. Pode desenvolver e validar idéias num ambiente de prova de conceito, antes do código de integração é escrito para os sistemas externos rapidamente.
