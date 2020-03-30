---
title: Arquitetura do Azure Blockchain Workbench
description: Visão geral da arquitetura de pré-visualização da bancada azure blockchain e dos seus componentes.
ms.date: 09/05/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: aa972e8ae486d181f0c48df72ec89c925c940451
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74324902"
---
# <a name="azure-blockchain-workbench-architecture"></a>Arquitetura do Azure Blockchain Workbench

A Pré-visualização da bancada de trabalho Azure Blockchain simplifica o desenvolvimento da aplicação blockchain, fornecendo uma solução utilizando vários componentes Do Azure. Blockchain Workbench pode ser implantado usando um modelo de solução no Mercado Azure. O modelo permite-lhe escolher módulos e componentes para implementar, incluindo a pilha blockchain, tipo de aplicação do cliente e suporte para integração ioT. Uma vez implementado, o Blockchain Workbench fornece acesso a uma aplicação web, aplicação iOS e aplicativo Android.

![Arquitetura blockchain workbench](./media/architecture/architecture.png)

## <a name="identity-and-authentication"></a>Identidade e autenticação

Utilizando a Blockchain Workbench, um consórcio pode federar as suas identidades empresariais usando o Azure Ative Directory (Azure AD). A workbench gera novas contas de utilizadores para identidades em cadeia com as identidades da empresa armazenadas em Azure AD. O mapeamento de identidade facilita o login autenticado às APIs e aplicações do cliente e utiliza as políticas de autenticação das organizações. A Workbench também fornece a capacidade de associar identidades empresariais a funções específicas dentro de um determinado contrato inteligente. Além disso, a Workbench também fornece um mecanismo para identificar as ações que essas funções podem tomar e a que horas.

Após a implementação da Blockchain Workbench, os utilizadores interagem com a Bancada de Trabalho blockchain, quer através das aplicações do cliente, da API do cliente baseado em REST, quer da API de Mensagens. Em todos os casos, as interações devem ser autenticadas, seja através do Diretório Ativo Azure (Azure AD) ou de credenciais específicas do dispositivo.

Os utilizadores federam as suas identidades para um consórcio Azure AD enviando um convite por e-mail aos participantes no seu endereço de e-mail. Ao iniciar sessão, estes utilizadores são autenticados utilizando o nome, senha e políticas. Por exemplo, a autenticação de dois fatores da sua organização.

O Azure AD é utilizado para gerir todos os utilizadores que tenham acesso à Bancada de Trabalho blockchain. Cada dispositivo que se liga a um contrato inteligente também está associado ao Azure AD.

A Azure AD também é usada para atribuir utilizadores a um grupo especial de administradores. Os utilizadores associados ao grupo de administradores têm acesso a direitos e ações no âmbito da Bancada de Trabalho blockchain, incluindo a implementação de contratos e a autorização para que um utilizador aceda a um contrato. Os utilizadores fora deste grupo não têm acesso a ações de administrador.

## <a name="client-applications"></a>Aplicações de cliente

A Workbench fornece aplicações de clientes geradas automaticamente para aplicações web e móveis (iOS, Android), que podem ser usadas para validar, testar e visualizar aplicações blockchain. A interface de aplicação é gerada dinamicamente com base em metadados de contratos inteligentes e pode acomodar qualquer caso de utilização. As aplicações do cliente fornecem uma extremidade frontal virada para o utilizador para as aplicações blockchain completas geradas pela Blockchain Workbench. As aplicações de clientes autenticam os utilizadores através do Azure Ative Directory (Azure AD) e apresentam uma experiência de utilizador adaptada ao contexto empresarial do contrato inteligente. A experiência do utilizador permite a criação de novos casos de contratos inteligentes por parte de indivíduos autorizados e, em seguida, apresenta a capacidade de executar certos tipos de transações em pontos apropriados no processo de negócio que o contrato inteligente representa.

Na aplicação web, os utilizadores autorizados podem aceder à Consola de Administrador. A consola está disponível para os utilizadores do grupo Administrator em Azure AD e fornece acesso à seguinte funcionalidade:

* A Implementação da Microsoft forneceu contratos inteligentes para cenários populares. Por exemplo, um cenário de transferência de ativos.
* Faça upload e implemente os seus próprios contratos inteligentes.
* Atribuir ao utilizador o acesso ao contrato inteligente no contexto de uma função específica.

Para mais informações, consulte as aplicações de clientes da equipa de trabalho [da Azure Blockchain no GitHub](https://github.com/Azure-Samples/blockchain-devkit/tree/master/connect/mobile).

## <a name="gateway-service-api"></a>Serviço gateway API

Blockchain Workbench inclui um serviço de gateway baseado em REST API. Ao escrever para um blockchain, a API gera e entrega mensagens a um corretor de eventos. Quando os dados são solicitados pela API, as consultas são enviadas para a base de dados SQL fora da cadeia. A base de dados SQL contém uma réplica de dados e metadados em cadeia que fornece informações de contexto e configuração para contratos inteligentes suportados. As consultas devolvem os dados necessários da réplica fora da cadeia num formato informado pelos metadados para o contrato.

Os desenvolvedores podem aceder ao serviço de gateway API para construir ou integrar soluções blockchain sem depender de aplicações de clientes blockchain Workbench.

> [!NOTE]
> Para permitir o acesso autenticado à API, duas aplicações de clientes estão registadas no Diretório Ativo Azure. O Azure Ative Directory requer registos de aplicação distintos de cada tipo de aplicação (nativo e web). 

## <a name="message-broker-for-incoming-messages"></a>Corretor de mensagens para mensagens de entrada

Os desenvolvedores que queiram enviar mensagens diretamente para a Bancada de Trabalho blockchain podem enviar mensagens diretamente para service bus. Por exemplo, as mensagens API poderiam ser utilizadas para a integração sistema-sistema ou dispositivos IoT.

## <a name="message-broker-for-downstream-consumers"></a>Corretor de mensagens para consumidores a jusante

Durante o ciclo de vida da aplicação, ocorrem eventos. Os eventos podem ser desencadeados pela API gateway ou no livro de registo. As notificações do evento podem iniciar código a jusante com base no evento.

Blockchain Workbench implementa automaticamente dois tipos de consumidores de eventos. Um consumidor é desencadeado por eventos blockchain para povoar a loja SQL fora da cadeia. O outro consumidor é capturar metadados para eventos gerados pela API relacionados com o upload e armazenamento de documentos.

## <a name="message-consumers"></a>Consumidores de mensagens

 Os consumidores de mensagens levam mensagens do Service Bus. O modelo de eventos subjacente aos consumidores de mensagens permite a extensão de serviços e sistemas adicionais. Por exemplo, pode adicionar suporte para povoar cosmosDB ou avaliar mensagens usando o Azure Streaming Analytics. As seguintes secções descrevem a mensagem que os consumidores incluem na bancada blockchain.

### <a name="distributed-ledger-consumer"></a>Consumidor de contabilidade distribuído

As mensagens distribuídas da tecnologia ledger (DLT) contêm os metadados para as transações a serem escritas na blockchain. O consumidor recupera as mensagens e empurra os dados para um construtor de transações, signatário e router.

### <a name="database-consumer"></a>Consumidor de base de dados

O consumidor de base de dados pega em mensagens do Service Bus e empurra os dados para uma base de dados anexada, como a base de dados SQL.

### <a name="storage-consumer"></a>Consumidor de armazenamento

O consumidor de armazenamento pega em mensagens do Service Bus e empurra os dados para um armazenamento anexo. Por exemplo, armazenar documentos de hashed no Armazenamento Azure.

## <a name="transaction-builder-and-signer"></a>Construtor de transações e signatário

Se uma mensagem no corretor de mensagens de entrada tiver de ser escrita para a blockchain, será processada pelo consumidor DLT. O consumidor DLT é um serviço, que recupera a mensagem que contém metadados para uma transação desejada para executar e, em seguida, envia a informação para o *construtor de transações e signatário*. O *construtor de transações e o signatário* montam uma transação blockchain com base nos dados e no destino blockchain pretendido. Uma vez montada, a transação é assinada. As chaves privadas estão armazenadas no Cofre de Chaves Azure.

 Blockchain Workbench recupera a chave privada apropriada do Key Vault e assina a transação fora do Key Vault. Uma vez assinada, a transação é enviada para routers de transações e livros de contabilidade.

## <a name="transaction-routers-and-ledgers"></a>Routers de transação e livros de contabilidade

Os routers de transações e os livros de contabilidade tomam transações assinadas e encaminham-nas para a blockchain apropriada. Atualmente, a Blockchain Workbench apoia a Ethereum como a sua blockchain alvo.

## <a name="dlt-watcher"></a>Observador DLT

Um observador de tecnologia de contabilidade distribuído (DLT) monitoriza eventos que ocorrem em cadeias de blocos ligadas à bancada de trabalho blockchain.
Os eventos refletem informações relevantes para indivíduos e sistemas. Por exemplo, a criação de novos casos de contrato, a execução de transações e as mudanças de Estado. Os eventos são capturados e enviados para o corretor de mensagens de saída, para que possam ser consumidos pelos consumidores a jusante.

Por exemplo, o consumidor SQL monitoriza eventos, consome-os e povoa a base de dados SQL com os valores incluídos. A cópia permite a recriação de uma réplica de dados em cadeia numa loja fora da cadeia.

## <a name="azure-sql-database"></a>Base de dados SQL do Azure

A base de dados Azure SQL anexa da Blockchain Workbench armazena definições de contratos, metadados de configuração e uma réplica acessível a SQL de dados armazenados na blockchain. Estes dados podem ser facilmente consultados, visualizados ou analisados acedendo diretamente à base de dados. Os desenvolvedores e outros utilizadores podem usar a base de dados para reportagens, análises ou outras integrações centradas em dados. Por exemplo, os utilizadores podem visualizar dados de transação usando o Power BI.

Este armazenamento fora da cadeia fornece a capacidade de as organizações empresariais consultarem dados em SQL e não num livro-razão blockchain. Além disso, padronizando um esquema padrão agnóstico de pilhas de tecnologia blockchain, o armazenamento fora da cadeia permite a reutilização de relatórios e outros artefactos através de projetos, cenários e organizações.

## <a name="azure-storage"></a>Storage do Azure

O Azure Storage é utilizado para armazenar contratos e metadados associados a contratos.

Desde ordens de compra e contas de embarque, imagens usadas nas notícias e imagens médicas, até vídeos originários de um contínuo, incluindo câmaras de corpo da polícia e grandes filmes, os documentos desempenham um papel em muitos cenários centrados em blockchain. Os documentos não são adequados para colocar diretamente na blockchain.

Blockchain Workbench suporta a capacidade de adicionar documentos ou outros conteúdos de mídia com lógica de negócio blockchain. Um hash do conteúdo do documento ou dos meios de comunicação é armazenado na blockchain e o conteúdo real do documento ou dos meios de comunicação é armazenado no Armazenamento Azure. As informações de transação associadas são entregues ao corretor de mensagens de entrada, embalados, assinados e encaminhados para a blockchain. Este processo desencadeia eventos, que são partilhados através do corretor de mensagens de saída. O SQL DB consome esta informação e envia-a para o DB para posterior consulta. Os sistemas a jusante também poderiam consumir estes eventos para agir em caso de adequação.

## <a name="monitoring"></a>Monitorização

A workbench fornece registo de aplicações utilizando insights de aplicação e monitor azure. Application Insights é usado para armazenar todas as informações registadas a partir da Bancada de Trabalho blockchain e inclui erros, avisos e operações bem sucedidas. Os Insights de Aplicação podem ser usados pelos desenvolvedores para depurar problemas com a Bancada de Trabalho blockchain. 

O Azure Monitor fornece informações sobre a saúde da rede blockchain. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Implementar o Azure Blockchain Workbench](../../blockchain-workbench/blockchain-workbench-deploy.md)