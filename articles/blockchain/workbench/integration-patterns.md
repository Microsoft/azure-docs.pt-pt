---
title: Padrões de integração do contrato inteligente no Azure Blockchain Workbench
description: Visão geral dos padrões de integração do contrato inteligente na visualização do Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: b3e7ff18dddeaeb1a42d69b9ed57d163410fbfa1
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329228"
---
# <a name="smart-contract-integration-patterns"></a>Padrões de integração de contratos inteligentes

Os contratos inteligentes geralmente representam um fluxo de trabalho de negócios que precisa ser integrado a dispositivos e sistemas externos.

Os requisitos desses fluxos de trabalho incluem a necessidade de iniciar transações em um razão distribuído que inclua dados de um sistema externo, serviço ou dispositivo. Eles também precisam ter sistemas externos reagir a eventos provenientes de contratos inteligentes em um razão distribuído.

A API REST e a integração de mensagens enviam transações de sistemas externos para contratos inteligentes incluídos em um aplicativo do Azure Blockchain Workbench. Ele também envia notificações de eventos para sistemas externos com base em alterações que ocorrem em um aplicativo.

Para cenários de integração de dados, o Azure Blockchain Workbench inclui um conjunto de modos de exibição de bancos de dados que mesclam uma combinação de informações transacionais de Blockchain e metadados sobre aplicativos e contratos inteligentes.

Além disso, alguns cenários, como aqueles relacionados à cadeia de fornecimento ou à mídia, também podem exigir a integração de documentos. Embora o Azure Blockchain Workbench não forneça chamadas à API para lidar com documentos diretamente, os documentos podem ser incorporados a um aplicativo Blockchain. Esta seção também inclui esse padrão.

Esta seção inclui os padrões identificados para implementar cada um desses tipos de integrações em suas soluções de ponta a ponta.

## <a name="rest-api-based-integration"></a>Integração baseada na API REST

Os recursos dentro do aplicativo Web gerado pelo Azure Blockchain Workbench são expostos por meio da API REST. Os recursos incluem o carregamento do Azure Blockchain Workbench, a configuração e a administração de aplicativos, o envio de transações para um razão distribuído e a consulta de metadados do aplicativo e dados do razão.

A API REST é usada principalmente para clientes interativos, como aplicativos Web, móveis e bot.

Esta seção analisa os padrões que se concentram nos aspectos da API REST que enviam transações para um razão distribuído e padrões que consultam dados sobre transações do do *banco de dados SQL do* Azure Blockchain Workbench.

### <a name="sending-transactions-to-a-distributed-ledger-from-an-external-system"></a>Enviando transações para um razão distribuído de um sistema externo

A API REST do Azure Blockchain Workbench envia solicitações autenticadas para executar transações em um razão distribuído.

![Enviando transações para um razão distribuído](./media/integration-patterns/send-transactions-ledger.png)

A execução de transações ocorre usando o processo descrito anteriormente, em que:

-   O aplicativo externo é autenticado no Azure Active Directory provisionado como parte da implantação do Azure Blockchain Workbench.
-   Usuários autorizados recebem um token de portador que pode ser enviado com solicitações para a API.
-   Os aplicativos externos fazem chamadas para a API REST usando o token de portador.
-   A API REST empacota a solicitação como uma mensagem e a envia para o barramento de serviço. A partir daqui, ele é recuperado, assinado e enviado para o razão distribuído apropriado.
-   A API REST faz uma solicitação para o BD SQL do Azure Blockchain Workbench para registrar a solicitação e estabelecer o status de provisionamento atual.
-   O BD SQL retorna o status de provisionamento e a chamada à API retorna a ID para o aplicativo externo que o chamou.

### <a name="querying-blockchain-workbench-metadata-and-distributed-ledger-transactions"></a>Consultando metadados do Blockchain Workbench e transações do razão distribuídas

A API REST do Azure Blockchain Workbench envia solicitações autenticadas para detalhes da consulta relacionados à execução do contrato inteligente em um razão distribuído.

![Consultando metadados](./media/integration-patterns/querying-metadata.png)

A consulta ocorre usando o processo descrito anteriormente, em que:

1. O aplicativo externo é autenticado no Azure Active Directory provisionado como parte da implantação do Azure Blockchain Workbench.
2. Usuários autorizados recebem um token de portador que pode ser enviado com solicitações para a API.
3. Os aplicativos externos fazem chamadas para a API REST usando o token de portador.
4. A API REST consulta os dados para a solicitação do BD SQL e retorna-os para o cliente.

## <a name="messaging-integration"></a>Integração de mensagens

A integração de mensagens facilita a interação com sistemas, serviços e dispositivos em que uma entrada interativa não é possível ou desejável. A integração de mensagens se concentra em dois tipos de mensagens: as mensagens que solicitam transações são executadas em um razão distribuído e eventos expostos por esse razão quando as transações ocorreram.

A integração de mensagens concentra-se na execução e no monitoramento de transações relacionadas à criação de usuário, criação de contrato e execução de transações em contratos e é usada principalmente por sistemas de back-end sem *periféricos* .

Esta seção analisa os padrões focados nos aspectos da API baseada em mensagem que enviam transações para um razão distribuído e padrões que representam mensagens de evento expostas pelo razão distribuído subjacente.

### <a name="one-way-event-delivery-from-a-smart-contract-to-an-event-consumer"></a>Entrega de evento unidirecional de um contrato inteligente para um consumidor de evento 

Nesse cenário, um evento ocorre dentro de um contrato inteligente, por exemplo, uma alteração de estado ou a execução de um tipo específico de transação. Esse evento é transmitido por meio de uma grade de eventos para consumidores de downstream, e esses consumidores tomam as ações apropriadas.

Um exemplo desse cenário é que quando uma transação ocorre, um consumidor seria alertado e poderia executar uma ação, como gravar as informações em um banco de dados SQL ou o Common Data Service. Esse cenário é o mesmo padrão que o Workbench segue para popular seu banco de BD do SQL de *cadeia* .

Outra seria se um contrato inteligente fizer uma transição para um estado específico, por exemplo, quando um contrato entrar em um *OutOfCompliance*. Quando essa alteração de estado ocorre, ela pode disparar um alerta para ser enviado ao telefone celular de um administrador.

![Entrega de evento unidirecional](./media/integration-patterns/one-way-event-delivery.png)

Esse cenário ocorre usando o processo descrito anteriormente, em que:

-   O contrato inteligente faz a transição para um novo estado e envia um evento para o razão.
-   O razão recebe e entrega o evento para o Azure Blockchain Workbench.
-   O Azure Blockchain Workbench é inscrito em eventos do razão e recebe o evento.
-   O Azure Blockchain Workbench publica o evento nos assinantes na grade de eventos.
-   Os sistemas externos são assinados na grade de eventos, consomem a mensagem e tomam as ações apropriadas.

## <a name="one-way-event-delivery-of-a-message-from-an-external-system-to-a-smart-contract"></a>Entrega de evento unidirecional de uma mensagem de um sistema externo para um contrato inteligente

Há também um cenário que flui da direção oposta. Nesse caso, um evento é gerado por um sensor ou um sistema externo e os dados desse evento devem ser enviados a um contrato inteligente.

Um exemplo comum é a entrega de dados de mercados financeiros, por exemplo, preços de mercadorias, ações ou títulos, para um contrato inteligente.

### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Entrega direta de um Azure Blockchain Workbench no formato esperado

Alguns aplicativos são criados para integração com o Azure Blockchain Workbench e gera e envia mensagens diretamente nos formatos esperados.

![Entrega direta](./media/integration-patterns/direct-delivery.png)

Essa entrega ocorre usando o processo descrito anteriormente, em que:

-   Um evento ocorre em um sistema externo que dispara a criação de uma mensagem para o Azure Blockchain Workbench.
-   O sistema externo tem código escrito para criar essa mensagem em um formato conhecido e a envia diretamente para o barramento de serviço.
-   O Azure Blockchain Workbench está inscrito em eventos do barramento de serviço e recupera a mensagem.
-   O Azure Blockchain Workbench inicia uma chamada para o razão, enviando dados do sistema externo para um contrato específico.
-   Após o recebimento da mensagem, o contrato faz a transição para um novo estado.

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Entrega de uma mensagem em um formato desconhecido para o Azure Blockchain Workbench

Alguns sistemas não podem ser modificados para entregar mensagens nos formatos padrão usados pelo Azure Blockchain Workbench. Nesses casos, os mecanismos existentes e os formatos de mensagem desses sistemas podem ser usados com frequência. Especificamente, os tipos de mensagens nativas desses sistemas podem ser transformados usando aplicativos lógicos, Azure Functions ou outro código personalizado para mapear para um dos formatos de mensagens padrão esperados.

![Formato de mensagem desconhecido](./media/integration-patterns/unknown-message-format.png)

Isso ocorre usando o processo descrito anteriormente, em que:

-   Um evento ocorre em um sistema externo que dispara a criação de uma mensagem.
-   Um aplicativo lógico ou código personalizado é usado para receber essa mensagem e transformá-la em uma mensagem formatada padrão do Azure Blockchain Workbench.
-   O aplicativo lógico envia a mensagem transformada diretamente para o barramento de serviço.
-   O Azure Blockchain Workbench está inscrito em eventos do barramento de serviço e recupera a mensagem.
-   O Azure Blockchain Workbench inicia uma chamada para o razão, enviando dados do sistema externo para uma função específica no contrato.
-   A função é executada e normalmente modifica o estado. A alteração do estado avança para o fluxo de trabalho de negócios refletido no contrato inteligente, permitindo que outras funções agora sejam executadas conforme apropriado.

### <a name="transitioning-control-to-an-external-process-and-await-completion"></a>Transição de controle para um processo externo e aguardar conclusão

Há cenários em que um contrato inteligente deve parar a execução interna e entregar a um processo externo. Esse processo externo seria concluído, enviar uma mensagem para o contrato inteligente e a execução continuaria no contrato inteligente.

#### <a name="transition-to-the-external-process"></a>Transição para o processo externo

Esse padrão normalmente é implementado usando a seguinte abordagem:

-   O contrato inteligente faz a transição para um estado específico. Nesse estado, nenhum ou um número limitado de funções pode ser executado até que um sistema externo execute uma ação desejada.
-   A alteração do estado é retonada como um evento para um consumidor downstream.
-   O consumidor downstream recebe o evento e dispara a execução de código externo.

![Controle de transição para processo externo](./media/integration-patterns/transition-external-process.png)

#### <a name="return-of-control-from-the-smart-contract"></a>Retorno do controle do contrato inteligente

Dependendo da capacidade de personalizar o sistema externo, ele pode ou não ser capaz de entregar mensagens em um dos formatos padrão esperados pelo Azure Blockchain Workbench. Com base na capacidade dos sistemas externos de gerar uma dessas mensagens, determine quais dos dois caminhos de retorno a seguir são tirados.

##### <a name="direct-delivery-of-an-azure-blockchain-workbench-in-the-expected-format"></a>Entrega direta de um Azure Blockchain Workbench no formato esperado

![](./media/integration-patterns/direct-delivery.png)

Nesse modelo, a comunicação com o contrato e a alteração de estado subsequente ocorre após o processo anterior em que-

-   Ao atingir a conclusão ou uma etapa específica na execução do código externo, um evento é enviado para o barramento de serviço conectado ao Azure Blockchain Workbench.

-   Para sistemas que não podem ser adaptados diretamente para gravar uma mensagem que esteja de acordo com as expectativas da API, ela é transformada.

-   O conteúdo da mensagem é empacotado e enviado a uma função específica no contrato inteligente. Essa entrega é feita em nome do usuário associado ao sistema externo.

-   A função é executada e normalmente modifica o estado. A alteração do estado avança para o fluxo de trabalho de negócios refletido no contrato inteligente, permitindo que outras funções agora sejam executadas conforme apropriado.

### 

### <a name="delivery-of-a-message-in-a-format-unknown-to-azure-blockchain-workbench"></a>Entrega de uma mensagem em um formato desconhecido para o Azure Blockchain Workbench

![Formato de mensagem desconhecido](./media/integration-patterns/unknown-message-format.png)

Nesse modelo em que uma mensagem em um formato padrão não pode ser enviada diretamente, a comunicação com o contrato e a alteração de estado subsequente ocorre após o processo anterior, em que:

1.  Ao atingir a conclusão ou uma etapa específica na execução do código externo, um evento é enviado para o barramento de serviço conectado ao Azure Blockchain Workbench.
2.  Um aplicativo lógico ou código personalizado é usado para receber essa mensagem e transformá-la em uma mensagem formatada padrão do Azure Blockchain Workbench.
3.  O aplicativo lógico envia a mensagem transformada diretamente para o barramento de serviço.
4.  O Azure Blockchain Workbench está inscrito em eventos do barramento de serviço e recupera a mensagem.
5.  O Azure Blockchain Workbench inicia uma chamada para o razão, enviando dados do sistema externo para um contrato específico.
6. O conteúdo da mensagem é empacotado e enviado a uma função específica no contrato inteligente. Essa entrega é feita em nome do usuário associado ao sistema externo.
7.  A função é executada e normalmente modifica o estado. A alteração do estado avança para o fluxo de trabalho de negócios refletido no contrato inteligente, permitindo que outras funções agora sejam executadas conforme apropriado.

## <a name="iot-integration"></a>Integração de IoT

Um cenário de integração comum é a inclusão de dados de telemetria recuperados de sensores em um contrato inteligente. Com base nos dados entregues pelos sensores, os contratos inteligentes poderiam tomar medidas informadas e alterar o estado do contrato.

Por exemplo, se um caminhão entregando sua temperatura disparar a 110 graus, isso poderá afetar a eficiência da medicina e poderá causar um problema de segurança pública se não for detectado e removido da cadeia de suprimentos. Se um driver acelerasse seu carro a 100 milhas por hora, as informações de sensor resultantes podem disparar um cancelamento de seguro por seu provedor de seguros. Se o carro fosse um carro de aluguel, os dados de GPS poderiam indicar quando o driver saiu de uma geografia coberta por seu contrato de aluguel e encarregava uma penalidade.

O desafio é que esses sensores podem entregar dados em uma base constante e não é apropriado enviar todos esses dados para um contrato inteligente. Uma abordagem típica é limitar o número de mensagens enviadas ao blockchain enquanto entrega todas as mensagens a um armazenamento secundário. Por exemplo, entregue mensagens recebidas apenas em intervalos fixos, por exemplo, uma vez por hora e quando um valor contido cair fora de um intervalo acordado para um contrato inteligente. Verificando valores que estão fora das tolerâncias, garante que os dados relevantes para a lógica de negócios dos contratos sejam recebidos e executados. Verificar o valor no intervalo confirma que o sensor ainda está se comunicando. Todos os dados são enviados a um repositório de relatórios secundário para permitir relatórios, análises e aprendizado de máquina mais amplos. Por exemplo, embora a obtenção de leituras de sensor para GPS não seja necessária a cada minuto para um contrato inteligente, elas podem fornecer dados interessantes a serem usados em relatórios ou rotas de mapeamento.

Na plataforma Azure, a integração com dispositivos normalmente é feita com o Hub IoT. O Hub IoT fornece roteamento de mensagens com base no conteúdo e habilita o tipo de funcionalidade descrito anteriormente.

![Mensagens de IoT](./media/integration-patterns/iot.png)

O processo representa um padrão:

-   Um dispositivo se comunica diretamente ou por meio de um gateway de campo para o Hub IoT.
-   O Hub IoT recebe as mensagens e avalia as mensagens em relação às rotas estabelecidas para verificar o conteúdo da mensagem, por exemplo. *O sensor relata uma temperatura maior que 50 graus?*
-   O Hub IoT envia mensagens que atendem aos critérios para um barramento de serviço definido para a rota.
-   Um aplicativo lógico ou outro código ouve o barramento de serviço que o Hub IoT estabeleceu para a rota.
-   O aplicativo lógico ou outro código recupera e transforma a mensagem em um formato conhecido.
-   A mensagem transformada, agora em um formato padrão, é enviada para o Service Bus para o Azure Blockchain Workbench.
-   O Azure Blockchain Workbench está inscrito em eventos do barramento de serviço e recupera a mensagem.
-   O Azure Blockchain Workbench inicia uma chamada para o razão, enviando dados do sistema externo para um contrato específico.
-   Após o recebimento da mensagem, o contrato avalia os dados e pode alterar o estado com base no resultado dessa avaliação, por exemplo, para uma alta temperatura, alterar o estado para *fora de conformidade*.

## <a name="data-integration"></a>Integração de dados

Além da API baseada em REST e em mensagens, o Azure Blockchain Workbench também fornece acesso a um banco de dados SQL populado com metadados de aplicativo e contrato, bem como dados transacionais de razões distribuídas.

![Integração de dados](./media/integration-patterns/data-integration.png)

A integração de dados é bem conhecida:

-   O Azure Blockchain Workbench armazena metadados sobre aplicativos, fluxos de trabalho, contratos e transações como parte de seu comportamento operacional normal.
-   Os sistemas ou as ferramentas externas fornecem uma ou mais caixas de diálogo para facilitar a coleta de informações sobre o banco de dados, como nome do servidor de banco de dados, nome do banco de dados, tipo de autenticação, credenciais de logon e quais exibições de banco de dados utilizar.
-   As consultas são gravadas em exibições do banco de dados SQL para facilitar o consumo de downstream por sistemas externos, serviços, relatórios, ferramentas de desenvolvedor e ferramentas de produtividade corporativa.

## <a name="storage-integration"></a>Integração de armazenamento

Muitos cenários podem exigir a necessidade de incorporar arquivos atestados. Por vários motivos, não é apropriado colocar arquivos em um blockchain. Em vez disso, uma abordagem comum é executar um hash criptográfico (por exemplo, SHA-256) em um arquivo e compartilhar esse hash em um razão distribuído. Executar o hash novamente a qualquer momento futuro deve retornar o mesmo resultado. Se o arquivo for modificado, mesmo que apenas um pixel seja modificado em uma imagem, o hash retornará um valor diferente.

![Integração de armazenamento](./media/integration-patterns/storage-integration.png)

O padrão pode ser implementado em que:

-   Um sistema externo persiste um arquivo em um mecanismo de armazenamento, como o armazenamento do Azure.
-   Um hash é gerado com o arquivo ou o arquivo e os metadados associados, como um identificador para o proprietário, a URL em que o arquivo está localizado, etc.
-   O hash e todos os metadados são enviados para uma função em um contrato inteligente, como *Fileadicionad*
-   No futuro, o arquivo e os metadados podem ser codificados novamente e comparados com os valores armazenados no razão.

## <a name="prerequisites-for-implementing-integration-patterns-using-the-rest-and-message-apis"></a>Pré-requisitos para implementar padrões de integração usando as APIs REST e de mensagem

Para facilitar a capacidade de um dispositivo ou sistema externo interagir com o contrato inteligente usando a API REST ou de mensagem, deve ocorrer o seguinte:

1. No Azure Active Directory para o consórcio, é criada uma conta que representa o dispositivo ou sistema externo.
2. Um ou mais contratos inteligentes apropriados para seu aplicativo do Azure Blockchain Workbench têm funções definidas para aceitar os eventos do seu sistema ou dispositivo externo.
3. O arquivo de configuração de aplicativo para seu contrato inteligente contém a função, que o sistema ou dispositivo está atribuído.
4. O arquivo de configuração de aplicativo para seu contrato inteligente identifica em que Estados essa função é chamada pela função definida.
5. O arquivo de configuração do aplicativo e seus contratos inteligentes são carregados no Azure Blockchain Workbench.

Depois que o aplicativo for carregado, a conta de Azure Active Directory para o sistema externo será atribuída ao contrato e à função associada.

## <a name="testing-external-system-integration-flows-prior-to-writing-integration-code"></a>Testando fluxos de integração do sistema externo antes de escrever o código de integração 

A integração com sistemas externos é um requisito importante de muitos cenários. É desejável poder validar o design de um contrato inteligente antes ou em paralelo ao desenvolvimento de código para integração com sistemas externos.

O uso de Azure Active Directory (AD do Azure) pode acelerar significativamente a produtividade do desenvolvedor e o tempo de implantação. Especificamente, a integração de código com um sistema externo pode levar um período de tempo não trivial. Usando o Azure AD e a geração automática de UX pelo Azure Blockchain Workbench, você pode permitir que os desenvolvedores entrem no Blockchain Workbench como o sistema externo e populem valores do sistema externo por meio da UX. Você pode desenvolver e validar ideias rapidamente em um ambiente de prova de conceito antes que o código de integração seja escrito para os sistemas externos.
