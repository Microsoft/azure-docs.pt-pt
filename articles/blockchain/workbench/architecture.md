---
title: Arquitetura do Azure Blockchain Workbench
description: Visão geral da Azure Blockchain Workbench Preview arquitetura e seus componentes.
ms.date: 09/05/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 1fff70ef2eeb1dc27d33769fd48fe5923f56717b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021610"
---
# <a name="azure-blockchain-workbench-architecture"></a>Arquitetura do Azure Blockchain Workbench

A Azure Blockchain Workbench Preview simplifica o desenvolvimento da aplicação blockchain fornecendo uma solução utilizando vários componentes Azure. Blockchain Workbench pode ser implementado usando um modelo de solução no Azure Marketplace. O modelo permite-lhe escolher módulos e componentes para implementar, incluindo blockchain stack, tipo de aplicação do cliente e suporte para integração de IoT. Uma vez implementado, blockchain Workbench fornece acesso a uma aplicação web, iOS e aplicativo Android.

![Arquitetura blockchain Workbench](./media/architecture/architecture.png)

## <a name="identity-and-authentication"></a>Identidade e autenticação

Utilizando a Blockchain Workbench, um consórcio pode federar as suas identidades empresariais usando o Azure Ative Directory (Azure AD). Workbench gera novas contas de utilizador para identidades em cadeia com as identidades da empresa armazenadas em Azure AD. O mapeamento de identidade facilita o login autenticado para AS EA e aplicações do cliente e utiliza as políticas de autenticação das organizações. Workbench também fornece a capacidade de associar identidades empresariais a papéis específicos dentro de um determinado contrato inteligente. Além disso, workbench também fornece um mecanismo para identificar as ações que essas funções podem tomar e a que horas.

Após a implementação do Blockchain Workbench, os utilizadores interagem com o Blockchain Workbench através das aplicações do cliente, da API do cliente com sede em REST ou da API de Mensagens. Em todos os casos, as interações devem ser autenticadas, quer através do Azure Ative Directory (Azure AD) quer através de credenciais específicas do dispositivo.

Os utilizadores federam as suas identidades para um consórcio Azure AD, enviando um convite por e-mail aos participantes no seu endereço de e-mail. Ao iniciar sessão, estes utilizadores são autenticados utilizando o nome, palavra-passe e políticas. Por exemplo, a autenticação de dois fatores da sua organização.

O Azure AD é usado para gerir todos os utilizadores que tenham acesso ao Blockchain Workbench. Cada dispositivo que se conecta a um contrato inteligente também está associado ao Azure AD.

O Azure AD também é usado para atribuir utilizadores a um grupo de administradores especiais. Os utilizadores associados ao grupo de administradores têm acesso a direitos e ações dentro da Blockchain Workbench, incluindo a implementação de contratos e a concessão de permissões a um utilizador para aceder a um contrato. Os utilizadores fora deste grupo não têm acesso a ações de administrador.

## <a name="client-applications"></a>Aplicações de cliente

Workbench fornece aplicações de clientes geradas automaticamente para web e mobile (iOS, Android), que podem ser usadas para validar, testar e visualizar aplicações blockchain. A interface de aplicação é gerada dinamicamente com base em metadados de contrato inteligente e pode acomodar qualquer caso de utilização. As aplicações do cliente fornecem uma extremidade frontal voltada para o utilizador para as aplicações blockchain completas geradas pela Blockchain Workbench. As aplicações do cliente autenticam os utilizadores através do Azure Ative Directory (Azure AD) e depois apresentam uma experiência de utilizador adaptada ao contexto comercial do contrato inteligente. A experiência do utilizador permite a criação de novas instâncias de contrato inteligente por indivíduos autorizados e, em seguida, apresenta a capacidade de executar determinados tipos de transações em pontos apropriados no processo de negócio que o contrato inteligente representa.

Na aplicação web, os utilizadores autorizados podem aceder à Consola de Administrador. A consola está disponível para os utilizadores do grupo Administrador em Azure AD e fornece acesso à seguinte funcionalidade:

* Implementar a Microsoft forneceu contratos inteligentes para cenários populares. Por exemplo, um cenário de transferência de ativos.
* Carreque e implante os seus próprios contratos inteligentes.
* Atribua um acesso ao contrato inteligente no contexto de uma função específica.

Para obter mais informações, consulte as aplicações de [clientes da amostra de workbench Azure Blockchain no GitHub.](https://github.com/Azure-Samples/blockchain-devkit/tree/master/connect/mobile)

## <a name="gateway-service-api"></a>Serviço gateway API

Blockchain Workbench inclui um serviço de gateway baseado em REST API. Ao escrever para uma blockchain, a API gera e entrega mensagens a um corretor de eventos. Quando os dados são solicitados pela API, as consultas são enviadas para a base de dados off-chain. A base de dados contém uma réplica de dados e metadados em cadeia que fornece informações de contexto e configuração para contratos inteligentes suportados. As consultas devolvem os dados necessários da réplica off-chain num formato informado pelos metadados do contrato.

Os desenvolvedores podem aceder ao serviço gateway API para construir ou integrar soluções blockchain sem depender de aplicações de clientes blockchain Workbench.

> [!NOTE]
> Para permitir o acesso autenticado à API, estão registadas duas aplicações para clientes no Diretório Ativo Azure. O Azure Ative Directory requer registos de candidaturas distintos de cada tipo de aplicação (nativo e web).

## <a name="message-broker-for-incoming-messages"></a>Corretor de mensagens para mensagens recebidas

Os desenvolvedores que pretendam enviar mensagens diretamente para blockchain Workbench podem enviar mensagens diretamente para o Service Bus. Por exemplo, as mensagens API podem ser usadas para integração sistema-sistema ou dispositivos IoT.

## <a name="message-broker-for-downstream-consumers"></a>Corretor de mensagens para consumidores a jusante

Durante o ciclo de vida da aplicação, ocorrem eventos. Os eventos podem ser desencadeados pela Gateway API ou no livro-razão. As notificações do evento podem iniciar código a jusante com base no evento.

Blockchain Workbench implanta automaticamente dois tipos de consumidores de eventos. Um consumidor é desencadeado por eventos blockchain para povoar a loja SQL off-chain. O outro consumidor é capturar metadados para eventos gerados pela API relacionados com o upload e armazenamento de documentos.

## <a name="message-consumers"></a>Consumidores de mensagens

 Os consumidores de mensagens levam mensagens da Service Bus. O modelo de eventos subjacente aos consumidores de mensagens permite a extensão de serviços e sistemas adicionais. Por exemplo, pode adicionar suporte para povoar o CosmosDB ou avaliar mensagens usando o Azure Streaming Analytics. As seguintes secções descrevem a mensagem que os consumidores incluem na Blockchain Workbench.

### <a name="distributed-ledger-consumer"></a>Consumidor de livros de contabilidade distribuído

As mensagens de tecnologia de contabilidade distribuída (DLT) contêm os metadados para as transações a escrever para a blockchain. O consumidor recupera as mensagens e empurra os dados para um construtor de transações, signatário e router.

### <a name="database-consumer"></a>Consumidor de bases de dados

O consumidor da base de dados leva mensagens do Service Bus e empurra os dados para uma base de dados anexa, como uma base de dados na Base de Dados Azure SQL.

### <a name="storage-consumer"></a>Consumidor de armazenamento

O consumidor de armazenamento leva mensagens da Service Bus e empurra os dados para um armazenamento anexo. Por exemplo, armazenar documentos de haxixe no Armazenamento Azure.

## <a name="transaction-builder-and-signer"></a>Construtor de transações e signatário

Se uma mensagem no corretor de mensagens de entrada precisar de ser escrita para a blockchain, será processada pelo consumidor DLT. O consumidor DLT é um serviço que recupera a mensagem que contém metadados para uma transação pretendda a executar e, em seguida, envia a informação para o *construtor de transações e para* o signatário . O *construtor de transações e o signatário* reúne uma transação blockchain com base nos dados e no destino blockchain pretendido. Uma vez montada, a transação é assinada. As chaves privadas estão guardadas no Cofre da Chave Azure.

 Blockchain Workbench recupera a chave privada apropriada do Key Vault e assina a transação fora do Key Vault. Uma vez assinada, a transação é enviada para routers de transações e livros de contabilidade.

## <a name="transaction-routers-and-ledgers"></a>Routers e livros de transações

Os routers de transações e os livros de contabilidade fazem transações assinadas e encaminham-nas para a blockchain apropriada. Atualmente, blockchain Workbench suporta ethereum como o seu blockchain alvo.

## <a name="dlt-watcher"></a>Observador DLT

Um observador de livros de contabilidade distribuído (DLT) monitoriza eventos que ocorrem em cadeias de blocos anexadas à Bancada Blockchain.
Os eventos refletem informação relevante para indivíduos e sistemas. Por exemplo, a criação de novos contratos, a execução de transações e alterações do Estado. Os eventos são capturados e enviados para o corretor de mensagens de saída, para que possam ser consumidos pelos consumidores a jusante.

Por exemplo, o consumidor SQL monitoriza eventos, consome-os e povoa a base de dados com os valores incluídos. A cópia permite a recriação de uma réplica de dados em cadeia numa loja off-chain.

## <a name="azure-sql-database"></a>Base de Dados SQL do Azure

A base de dados anexa à Blockchain Workbench armazena definições de contrato, metadados de configuração e uma réplica acessível ao SQL de dados armazenados na blockchain. Estes dados podem ser facilmente consultados, visualizados ou analisados acedendo diretamente à base de dados. Os desenvolvedores e outros utilizadores podem utilizar a base de dados para reportar, análise ou outras integrações centradas em dados. Por exemplo, os utilizadores podem visualizar dados de transação usando o Power BI.

Este armazenamento off-chain fornece a capacidade para as organizações empresariais consultarem dados em SQL e não em um livro de blockchain. Além disso, ao normalizar um esquema padrão que é agnóstico de pilhas de tecnologia blockchain, o armazenamento off-chain permite a reutilização de relatórios e outros artefactos através de projetos, cenários e organizações.

## <a name="azure-storage"></a>Storage do Azure

O Azure Storage é utilizado para armazenar contratos e metadados associados a contratos.

Desde ordens de compra e notas de embarque, até imagens usadas nas notícias e imagens médicas, até vídeos originários de um contínuo, incluindo câmaras do corpo da polícia e grandes filmes, os documentos desempenham um papel em muitos cenários centrados em blockchain. Os documentos não são adequados para colocar diretamente na blockchain.

Blockchain Workbench suporta a capacidade de adicionar documentos ou outros conteúdos de mídia com lógica de negócio blockchain. Um haxixe do documento ou conteúdo de mídia é armazenado na blockchain e o documento real ou conteúdo de mídia é armazenado no Azure Storage. As informações de transação associadas são entregues ao corretor de mensagens de entrada, embalado, assinado e encaminhado para a blockchain. Este processo desencadeia eventos que são partilhados através do corretor de mensagens de saída. O SQL DB consome esta informação e envia-a para o DB para posterior consulta. Os sistemas a jusante também poderiam consumir estes eventos para agirem conforme apropriado.

## <a name="monitoring"></a>Monitorização

Workbench fornece registo de aplicações usando Insights de Aplicação e Monitor Azure. O Application Insights é utilizado para armazenar todas as informações registadas da Blockchain Workbench e inclui erros, avisos e operações bem sucedidas. Os Insights de Aplicação podem ser usados pelos desenvolvedores para depurar problemas com o Blockchain Workbench. 

O Azure Monitor fornece informações sobre a saúde da rede blockchain. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Implementar o Azure Blockchain Workbench](./deploy.md)
