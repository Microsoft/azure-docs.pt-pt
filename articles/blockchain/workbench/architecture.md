---
title: Arquitetura do Azure Blockchain Workbench
description: Descrição geral da arquitetura de Azure Blockchain Workbench e seus componentes.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 83c5e1405c402a1c6c98f9dbcaaf74891eb75e6d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61437777"
---
# <a name="azure-blockchain-workbench-architecture"></a>Arquitetura do Azure Blockchain Workbench

O Azure Blockchain Workbench simplifica o desenvolvimento de aplicações de blockchain ao fornecer uma solução com vários componentes do Azure. Blockchain Workbench pode ser implementado com um modelo de solução no Azure Marketplace. O modelo permite-lhe escolher módulos e componentes para implementar, incluindo a pilha de blockchain, tipo de aplicação de cliente e suporte para a integração de IoT. Uma vez implantado, o Blockchain Workbench fornece acesso a uma aplicação web, a aplicação para iOS e a aplicação Android.

![Arquitetura de Blockchain Workbench](./media/architecture/architecture.png)

## <a name="identity-and-authentication"></a>Identidade e autenticação

Utilizar o Blockchain Workbench, um consórcio pode federar suas identidades de empresa com o Azure Active Directory (Azure AD). Bancada de trabalho gera novas contas de utilizador de identidades na cadeia com as identidades da empresa armazenadas no Azure AD. O mapeamento de identidades facilita o início de sessão autenticado para aplicações e APIs de cliente e utiliza as políticas de autenticação das organizações. Bancada de trabalho também fornece a capacidade de associar as identidades de empresa para funções específicas dentro de um determinado contrato inteligente. Além disso, Bancada de trabalho também fornece um mecanismo para identificar as ações essas funções podem tirar e a que hora.

Depois de Blockchain Workbench ser implementado, os usuários interagem com o Blockchain Workbench por meio dos aplicativos cliente, API de cliente baseada em REST ou API de mensagens. Em todos os casos, as interações tem de ser autenticadas, através do Azure Active Directory (Azure AD) ou credenciais específicas do dispositivo.

Os usuários federar suas identidades para um consórcio do Azure AD ao enviar um convite por e-mail aos participantes no respetivo endereço de e-mail. Quando iniciar sessão, estes utilizadores são autenticados com o nome, a palavra-passe e a políticas. Por exemplo, autenticação de dois fatores de sua organização.

Azure AD é utilizado para gerir todos os utilizadores que têm acesso ao Blockchain Workbench. Cada dispositivo a ligar a um contrato de inteligente também está associado com o Azure AD.

Azure AD também é utilizado para atribuir utilizadores a um grupo de administrador especiais. Os utilizadores associados ao grupo de administrador são concedidos acesso aos direitos e ações dentro de Blockchain Workbench, incluindo a implementação de contratos e conceder permissões a um utilizador para aceder a um contrato. Os utilizadores fora deste grupo não tem acesso a ações de administrador.

## <a name="client-applications"></a>Aplicações de cliente

Bancada de trabalho fornece aos aplicativos de cliente gerado automaticamente para a web e dispositivos móveis (iOS, Android), que podem ser utilizado para validar, testar e ver aplicações de blockchain. A interface de aplicativo é gerada dinamicamente com base nos metadados de contrato inteligente e consegue acomodar qualquer caso de utilização. As aplicações cliente fornecem um front-end de destinada ao utilizador para as aplicações de blockchain completa geradas pela Bancada de trabalho de Blockchain. Aplicativos cliente autenticar usuários via Azure Active Directory (Azure AD) e, em seguida, apresentam uma experiência de utilizador personalizada para o contexto empresarial do contrato inteligente. A experiência do usuário permite a criação de novas instâncias de contrato inteligente indivíduos autorizados e, em seguida, apresenta a capacidade de executar determinados tipos de transações em momentos apropriados no processo de negócios que representa o contrato inteligente.

No aplicativo da web, os usuários autorizados possam acessar o Console do administrador. A consola está disponível para utilizadores no grupo de administrador no Azure AD e fornece acesso para a seguinte funcionalidade:

* Implemente o Microsoft fornecido contratos inteligentes para cenários populares. Por exemplo, um cenário de transferência ativo.
* Carregar e implementar seus próprios contratos inteligentes.
* Atribua um utilizador acesso ao contrato de inteligente no contexto de uma função específica.

Para obter mais informações, consulte a [aplicações de cliente do Azure Blockchain Workbench exemplo no GitHub](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-development-kit/connect/mobile/blockchain-workbench/workbench-client).

## <a name="gateway-service-api"></a>API de serviço de gateway

Blockchain Workbench inclui uma API de serviço de gateway baseada em REST. Ao escrever um blockchain, a API gera e entrega mensagens para um mediador de eventos. Quando dados são solicitados pela API, as consultas são enviadas para a base de dados do SQL de fora da cadeia. A base de dados SQL contém uma réplica dos dados na cadeia e metadados que fornece informações de contexto e a configuração para contratos inteligentes suportados. Consultas devolvem os dados necessários a partir da réplica de fora da cadeia em formato informado pelos metadados para o contrato.

Os desenvolvedores podem acessar a API de serviço de gateway para criar ou integrar soluções de blockchain sem depender de aplicações de cliente de Blockchain Workbench.

> [!NOTE]
> Para ativar o acesso autenticado à API, as duas aplicações de cliente são registadas no Azure Active Directory. O Azure Active Directory requer registos de aplicação distintos de cada tipo de aplicação (nativas e web). 

## <a name="message-broker-for-incoming-messages"></a>Mediador de mensagens para mensagens de entrada

Os desenvolvedores que deseja enviar mensagens diretamente para o Blockchain Workbench podem enviar mensagens diretamente para o Service Bus. Por exemplo, a API de mensagens poderia ser utilizada para a integração do sistema para sistema ou de dispositivos IoT.

## <a name="message-broker-for-downstream-consumers"></a>Mediador de mensagens para os consumidores de downstream

Durante o ciclo de vida do aplicativo, os eventos ocorrem. Eventos podem ser acionados pela API de Gateway ou no razão. Notificações de eventos podem iniciar com base no evento de código downstream.

Blockchain Workbench implementa automaticamente a dois tipos de consumidores de eventos. Um consumidor é acionado por eventos de blockchain para preencher o arquivo SQL de fora da cadeia. O outro tipo de consumidor é capturar metadados para eventos gerados pela API relacionados com o carregamento e o armazenamento de documentos.

## <a name="message-consumers"></a>Consumidores de mensagens

 Consumidores de mensagens demorar mensagens do Service Bus. Permite que o modelo subjacente de eventos para os consumidores de mensagem para extensões de sistemas e serviços adicionais. Por exemplo, poderia adicionar suporte para preencher o cosmos DB ou avaliar mensagens usando a análise de transmissão em fluxo do Azure. As secções seguintes descrevem os consumidores de mensagem incluídos na bancada de trabalho de Blockchain.

### <a name="distributed-ledger-consumer"></a>Consumidor do livro razão distribuído

As mensagens de tecnologia (DLT) do livro razão distribuído contêm os metadados para transações de escrita do blockchain. O consumidor obtém as mensagens e envia os dados para um construtor de transação, o signatário e o roteador.

### <a name="database-consumer"></a>Consumidor de base de dados

O consumidor de base de dados recebe mensagens do Service Bus e envia os dados para uma base de dados anexado, por exemplo, a base de dados SQL.

### <a name="storage-consumer"></a>Consumidor de armazenamento

O consumidor de armazenamento usa mensagens do Service Bus e envia dados para um armazenamento anexado. Por exemplo, armazenamento de documentos com hash no armazenamento do Azure.

## <a name="transaction-builder-and-signer"></a>Construtor de transação e signatário

Se uma mensagem sobre o Mediador de mensagens de entrada tem de ser escrito para o blockchain, voltará a ser processada pelo consumidor DLT. O consumidor DLT é um serviço, que obtém a mensagem que contém metadados para uma transação pretendido executar e, em seguida, envia as informações para o *builder de transação e o signatário*. O *builder de transação e o signatário* monta uma transação de blockchain com base nos dados e o destino pretendido de blockchain. Assim que Reunimos, a transação está assinada. As chaves privadas são armazenadas no Azure Key Vault.

 Blockchain Workbench obtém a chave privada apropriada do Key Vault e inicia a transação fora do Cofre de chaves. Depois de iniciar sessão, a transação é enviada para os routers de transação e livros razão.

## <a name="transaction-routers-and-ledgers"></a>Routers de transação e livros razão

Routers de transação e livros razão tirar transações assinadas e roteá-los para o blockchain apropriado. Atualmente, o Blockchain Workbench suporta Ethereum como seu blockchain de destino.

## <a name="dlt-watcher"></a>Observador DLT

Um observador de tecnologia (DLT) do livro razão distribuído monitoriza os eventos que ocorrem em cadeias de bloco anexadas a Bancada de trabalho de Blockchain.
Eventos refletem informações relevantes para indivíduos e sistemas. Por exemplo, a criação de novas instâncias de contrato, a execução de transações e alterações de estado. Os eventos são capturados e enviados para o Mediador de mensagens de saída, eles podem ser consumidos pelos consumidores de downstream.

Por exemplo, o consumidor SQL monitora eventos, consome-los e preenche a base de dados SQL com valores incluídos. A cópia ativa recriar uma réplica dos dados na cadeia numa loja de fora da cadeia.

## <a name="azure-sql-database"></a>Base de dados SQL do Azure

A base de dados SQL do Azure anexado à Bancada de trabalho de Blockchain armazena as definições de contrato, metadados de configuração e uma réplica SQL acessível dos dados armazenados no blockchain. Estes dados podem facilmente ser consultados, visualizados ou analisados pelos acessar diretamente o banco de dados. Os desenvolvedores e outros utilizadores podem utilizar a base de dados para relatórios, análise ou outras integrações centrados em dados. Por exemplo, os utilizadores podem visualizar dados de transação com o Power BI.

Este armazenamento de fora da cadeia permite às organizações empresariais para consultar dados no SQL, em vez de num livro razão de blockchain. Além disso, ao uniformizar num esquema padrão que é independente das pilhas de tecnologia blockchain, o armazenamento de fora da cadeia permite a reutilização de relatórios e outros artefatos entre organizações, projetos e cenários.

## <a name="azure-storage"></a>Storage do Azure

Armazenamento do Azure é utilizado para armazenar os contratos e metadados associados contratos.

De ordens de compra e lading de listas, para imagens usadas nas notícias e imagens médicas, para vídeo provenientes de um continuum, incluindo câmaras de corpo de polícia e principais de movimento de imagens, documentos desempenham um papel em muitos cenários centrados em blockchain. Documentos não são adequados para colocar diretamente no blockchain.

Blockchain Workbench suporta a capacidade de adicionar documentos ou outros conteúdos de multimédia com lógica de negócio de blockchain. Um hash do conteúdo do documento ou suporte de dados é armazenado no blockchain e o documento real ou o conteúdo de mídia é armazenado no armazenamento do Azure. As informações de transações associados são fornecidas para o Mediador de mensagens de entrada, empacotadas, assinadas e encaminhadas para o blockchain. Este processo aciona eventos, que são partilhados, através do Mediador de mensagens de saída. A BD do SQL consome estas informações e envia-os para o banco de dados para consultar mais tarde. Sistemas de Downstream também podem consumir estes eventos para agir conforme adequado.

## <a name="monitoring"></a>Monitorização

Bancada de trabalho fornece o registo de aplicação com o Application Insights e o Azure Monitor. Application Insights é utilizado para armazenar todas as informações com sessão iniciada da bancada de trabalho de Blockchain e inclui as operações com êxito, avisos e erros. O Application Insights pode ser utilizado pelos desenvolvedores para depurar problemas com o Blockchain Workbench. 

O Azure Monitor fornece informações sobre o estado de funcionamento da rede de blockchain. 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Implementar o Azure Blockchain Workbench](../../blockchain-workbench/blockchain-workbench-deploy.md)