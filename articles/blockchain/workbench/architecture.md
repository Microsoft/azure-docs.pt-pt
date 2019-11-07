---
title: Arquitetura de visualização do Azure Blockchain Workbench
description: Visão geral da arquitetura de visualização do Azure Blockchain Workbench e seus componentes.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: conceptual
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 4613d441fd0d363654073d4832de19139a7781e7
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579728"
---
# <a name="azure-blockchain-workbench-preview-architecture"></a>Arquitetura de visualização do Azure Blockchain Workbench

A visualização do Azure Blockchain Workbench simplifica o desenvolvimento de aplicativos Blockchain fornecendo uma solução usando vários componentes do Azure. O Blockchain Workbench pode ser implantado usando um modelo de solução no Azure Marketplace. O modelo permite que você escolha módulos e componentes para implantar, incluindo blockchain Stack, tipo de aplicativo cliente e suporte para a integração de IoT. Depois de implantado, o Blockchain Workbench fornece acesso a um aplicativo Web, aplicativo iOS e aplicativo Android.

![Arquitetura do Blockchain Workbench](./media/architecture/architecture.png)

## <a name="identity-and-authentication"></a>Identidade e autenticação

Usando o Blockchain Workbench, um consórcio pode federar suas identidades empresariais usando o Azure Active Directory (AD do Azure). O Workbench gera novas contas de usuário para identidades em cadeia com as identidades corporativas armazenadas no Azure AD. O mapeamento de identidade facilita o logon autenticado em aplicativos e APIs de cliente e usa as políticas de autenticação das organizações. O Workbench também fornece a capacidade de associar identidades corporativas a funções específicas em um determinado contrato inteligente. Além disso, o Workbench também fornece um mecanismo para identificar as ações que essas funções podem tomar e em que hora.

Após a implantação do Blockchain Workbench, os usuários interagem com o Blockchain Workbench por meio dos aplicativos cliente, da API do cliente baseada em REST ou da API de mensagens. Em todos os casos, as interações devem ser autenticadas, seja via Azure Active Directory (Azure AD) ou credenciais específicas do dispositivo.

Os usuários federam suas identidades a um consórcio do Azure AD enviando um convite por email aos participantes em seu endereço de email. Ao fazer logon, esses usuários são autenticados usando o nome, a senha e as políticas. Por exemplo, a autenticação de dois fatores de sua organização.

O Azure AD é usado para gerenciar todos os usuários que têm acesso ao Blockchain Workbench. Cada dispositivo que se conecta a um contrato inteligente também está associado ao Azure AD.

O AD do Azure também é usado para atribuir usuários a um grupo de administradores especial. Os usuários associados ao grupo de administradores recebem acesso a direitos e ações no Blockchain Workbench, incluindo a implantação de contratos e a concessão de permissões a um usuário para acessar um contrato. Os usuários fora deste grupo não têm acesso às ações do administrador.

## <a name="client-applications"></a>Aplicações de cliente

O Workbench fornece aplicativos cliente gerados automaticamente para Web e móveis (iOS, Android), que podem ser usados para validar, testar e exibir aplicativos blockchain. A interface do aplicativo é gerada dinamicamente com base nos metadados do contrato inteligente e pode acomodar qualquer caso de uso. Os aplicativos cliente entregam um front-end voltado ao usuário para os aplicativos blockchain completos gerados pelo Blockchain Workbench. Os aplicativos cliente autenticam usuários via Azure Active Directory (Azure AD) e, em seguida, apresentam uma experiência do usuário adaptada ao contexto de negócios do contrato inteligente. A experiência do usuário permite a criação de novas instâncias de contrato inteligente por indivíduos autorizados e, em seguida, apresenta a capacidade de executar determinados tipos de transações em pontos apropriados no processo comercial que o contrato inteligente representa.

No aplicativo Web, os usuários autorizados podem acessar o Console do Administrador. O console está disponível para usuários no grupo administrador no Azure AD e fornece acesso à seguinte funcionalidade:

* Implante os contratos inteligentes fornecidos pela Microsoft para cenários populares. Por exemplo, um cenário de transferência de ativos.
* Carregue e implante seus próprios contratos inteligentes.
* Atribua um acesso de usuário ao contrato inteligente no contexto de uma função específica.

Para obter mais informações, consulte os [aplicativos cliente de exemplo do Azure Blockchain Workbench no GitHub](https://github.com/Azure-Samples/blockchain-devkit/tree/master/connect/mobile).

## <a name="gateway-service-api"></a>API de serviço do gateway

O Blockchain Workbench inclui uma API de serviço de gateway baseada em REST. Ao gravar em um blockchain, a API gera e entrega mensagens para um agente de eventos. Quando os dados são solicitados pela API, consultas são enviadas para o banco de dados SQL fora da cadeia. O banco de dados SQL contém uma réplica de metadados on-Chain e Metadata que fornece informações de contexto e configuração para contratos inteligentes com suporte. As consultas retornam os dados necessários da réplica fora da cadeia em um formato informado pelos metadados para o contrato.

Os desenvolvedores podem acessar a API de serviço do gateway para criar ou integrar soluções blockchain sem depender de aplicativos cliente do Blockchain Workbench.

> [!NOTE]
> Para habilitar o acesso autenticado à API, dois aplicativos cliente são registrados em Azure Active Directory. Azure Active Directory requer registros de aplicativo distintos de cada tipo de aplicativo (nativo e Web). 

## <a name="message-broker-for-incoming-messages"></a>Agente de mensagem para mensagens de entrada

Os desenvolvedores que desejam enviar mensagens diretamente para o Blockchain Workbench podem enviar mensagens diretamente para o barramento de serviço. Por exemplo, a API de mensagens pode ser usada para integração de sistema para sistema ou dispositivos IoT.

## <a name="message-broker-for-downstream-consumers"></a>Agente de mensagem para consumidores downstream

Durante o ciclo de vida do aplicativo, ocorrem eventos. Os eventos podem ser disparados pela API do gateway ou no razão. As notificações de eventos podem iniciar o código downstream com base no evento.

O Blockchain Workbench implanta automaticamente dois tipos de consumidores de eventos. Um consumidor é disparado por eventos blockchain para preencher a loja do SQL fora da cadeia. O outro consumidor é capturar metadados para eventos gerados pela API relacionada ao carregamento e armazenamento de documentos.

## <a name="message-consumers"></a>Consumidores de mensagens

 Os consumidores de mensagens tomam mensagens do barramento de serviço. O modelo subjacente de eventos para consumidores de mensagens permite extensões de serviços e sistemas adicionais. Por exemplo, você pode adicionar suporte para popular CosmosDB ou avaliar mensagens usando o Azure streaming Analytics. As seções a seguir descrevem os consumidores de mensagens incluídos no Blockchain Workbench.

### <a name="distributed-ledger-consumer"></a>Consumidor do razão distribuído

As mensagens de DLT (Distributed Ledger Technology) contêm os metadados das transações a serem gravadas no blockchain. O consumidor recupera as mensagens e envia os dados por push para um construtor de transações, um signatário e um roteador.

### <a name="database-consumer"></a>Consumidor de banco de dados

O consumidor de banco de dados recebe as mensagens do barramento de serviço e envia-os por push para um banco de dado anexado, como o SQL Database.

### <a name="storage-consumer"></a>Consumidor de armazenamento

O consumidor de armazenamento recebe mensagens do barramento de serviço e envia os dados por push para um armazenamento anexado. Por exemplo, armazenar documentos com hash no armazenamento do Azure.

## <a name="transaction-builder-and-signer"></a>Construtor de transações e assinante

Se uma mensagem no agente de mensagem de entrada precisar ser gravada no blockchain, ela será processada pelo consumidor DLT. O consumidor DLT é um serviço, que recupera a mensagem que contém os metadados de uma transação desejada para execução e, em seguida, envia as informações para o *Construtor de transações e o assinante*. O *Construtor de transações e o assinante* monta uma transação blockchain com base nos dados e no destino de blockchain desejado. Uma vez montado, a transação é assinada. As chaves privadas são armazenadas em Azure Key Vault.

 O Blockchain Workbench recupera a chave privada apropriada de Key Vault e assina a transação fora do Key Vault. Depois de assinado, a transação é enviada para roteadores de transação e razões.

## <a name="transaction-routers-and-ledgers"></a>Roteadores de transação e razões

Roteadores de transação e razões tomam transações assinadas e as encaminham para o blockchain apropriado. Atualmente, o Blockchain Workbench dá suporte a Ethereum como seu Blockchain de destino.

## <a name="dlt-watcher"></a>Inspetor de DLT

Um inspetor de DLT (Distributed Ledger Technology) monitora os eventos que ocorrem em cadeias de bloco anexadas ao Blockchain Workbench.
Eventos refletem informações relevantes para indivíduos e sistemas. Por exemplo, a criação de novas instâncias de contrato, execução de transações e alterações de estado. Os eventos são capturados e enviados para o agente de mensagens de saída, para que possam ser consumidos por consumidores de downstream.

Por exemplo, o consumidor do SQL monitora eventos, os consome e popula o banco de dados SQL com os valores incluídos. A cópia permite a recriação de uma réplica de dados na cadeia em um repositório fora da cadeia.

## <a name="azure-sql-database"></a>Base de dados SQL do Azure

O banco de dados SQL do Azure anexado ao Blockchain Workbench armazena definições de contrato, metadados de configuração e uma réplica acessível por SQL de data armazenados no Blockchain. Esses dados podem ser consultados, visualizados ou analisados com facilidade acessando diretamente o Database. Os desenvolvedores e outros usuários podem usar o banco de dados para relatórios, análises ou outras integrações centradas no data. Por exemplo, os usuários podem visualizar dados de transação usando Power BI.

Esse armazenamento fora da cadeia fornece a capacidade para que as organizações empresariais consultem dados em SQL em vez de em uma contabilidade blockchain. Além disso, ao padronizar um esquema padrão que é independente das pilhas de tecnologia blockchain, o armazenamento fora da cadeia permite a reutilização de relatórios e outros artefatos entre projetos, cenários e organizações.

## <a name="azure-storage"></a>Storage do Azure

O armazenamento do Azure é usado para armazenar contratos e metadados associados a contratos.

Desde os pedidos de compra e as notas de embarque até as imagens usadas nas notícias e fotos médicas, no caso de vídeos provenientes de uma série de continuidade, incluindo câmeras de corpo de polícia e imagens de movimento importantes, os documentos desempenham uma função em muitos cenários centrados em blockchain. Os documentos não são apropriados para serem colocados diretamente no blockchain.

O Blockchain Workbench dá suporte à capacidade de adicionar documentos ou outros conteúdos de mídia com a lógica de negócios do Blockchain. Um hash do documento ou conteúdo de mídia é armazenado no blockchain e o documento real ou conteúdo de mídia é armazenado no armazenamento do Azure. As informações de transação associadas são entregues ao agente de mensagem de entrada, empacotado, assinado e roteado para o blockchain. Esse processo dispara eventos, que são compartilhados por meio do agente de mensagens de saída. O banco de dados SQL consome essas informações e as envia para o BD para consulta posterior. Os sistemas downstream também podem consumir esses eventos para agir conforme apropriado.

## <a name="monitoring"></a>Monitorização

O Workbench fornece o log de aplicativos usando Application Insights e Azure Monitor. Application Insights é usado para armazenar todas as informações registradas do Blockchain Workbench e inclui erros, avisos e operações bem-sucedidas. Application Insights pode ser usado por desenvolvedores para depurar problemas com o Blockchain Workbench. 

Azure Monitor fornece informações sobre a integridade da rede blockchain. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Implementar o Azure Blockchain Workbench](../../blockchain-workbench/blockchain-workbench-deploy.md)