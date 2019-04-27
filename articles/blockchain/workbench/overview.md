---
title: Descrição geral do Azure Blockchain Workbench
description: Descrição geral do Azure Blockchain Workbench e respetivas funcionalidades.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 01/14/2019
ms.topic: overview
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 58fd09726f05ba442c66387ecbd6cfad37f598e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60896163"
---
# <a name="what-is-azure-blockchain-workbench"></a>O que é o Azure Blockchain Workbench?

O Azure Blockchain Workbench é uma coleção de funcionalidades e serviços do Azure desenvolvida para o ajudar a criar e implementar aplicações blockchain com vista a partilhar dados e processos de negócio com outras organizações. O Azure Blockchain Workbench fornece as bases de infraestrutura para a criação de aplicações blockchain, o que permite aos programadores concentrarem-se na criação da lógica de negócio e de contratos inteligentes. Também simplifica a criação de aplicações blockchain graças à integração de várias funcionalidades e serviços do Azure que ajudam a automatizar as tarefas de desenvolvimento comuns.

## <a name="create-blockchain-applications"></a>Criar aplicações blockchain

Com o Blockchain Workbench, pode definir as aplicações blockchain a utilizar a configuração e da escrita de código para contratos inteligentes. Pode impulsionar o desenvolvimento das aplicações blockchain e focar-se na definição de contratos e na escrita de lógica de negócio em vez de dedicar-se à criação e configuração de serviços de suporte.

## <a name="manage-applications-and-users"></a>Gerir aplicações e utilizadores

O Azure Blockchain Workbench disponibiliza uma aplicação Web e APIs REST para a gestão de utilizadores e aplicações blockchain. Os administradores do Blockchain Workbench podem gerir o acesso à aplicação e atribuir os seus utilizadores a funções da aplicação. Os utilizadores do Azure AD são mapeados automaticamente para membros na aplicação.

## <a name="integrate-blockchain-with-applications"></a>Integrar o blockchain com as aplicações

Pode utilizar as APIs baseadas em mensagens e as APIs REST do Blockchain Workbench para promover a integração com os sistemas existentes. As APIs fornecem uma interface para permitir a substituição ou a utilização de várias tecnologias de registo distribuído, armazenamento e ofertas de bases de dados.

O Blockchain Workbench pode transformar as mensagens enviadas para a respetiva API baseada em mensagens de modo a criar transações num formato esperado pela API nativa do blockchain em causa.  O Workbench pode assinar e encaminhar transações para o blockchain adequado. 

O Workbench encaminha automaticamente os eventos para o Service Bus e o Event Grid para enviar mensagens aos consumidores a jusante. Os programadores podem integrar qualquer um destes sistemas de mensagens para promover as transações e analisar os resultados.

## <a name="deploy-a-blockchain-network"></a>Implementar uma rede blockchain

O Azure Blockchain Workbench simplifica a configuração da rede blockchain em consórcio sob a forma de uma solução pré-configurada com um modelo da solução Azure Resource Manager. O modelo oferece uma implementação simplificada que implementa todos os componentes necessários à execução de um consórcio. Atualmente, o Blockchain Workbench suporta Ethereum.

## <a name="use-active-directory-login"></a>Utilizar o início de sessão do Active Directory

Com os protocolos de blockchain existentes, as identidades de blockchain são representadas como um endereço na rede. O Azure Blockchain Workbench abstrai a identidade de blockchain ao associá-la a uma identidade do Active Directory, o que simplifica a criação de aplicações empresariais com identidades do Active Directory.

## <a name="synchronize-on-chain-data-with-off-chain-storage"></a>Sincronizar dados dentro da cadeia com armazenamento fora da cadeia

O Azure Blockchain Workbench facilita a análise dos dados e eventos blockchain ao sincronizar automaticamente os dados no blockchain com o armazenamento fora da cadeia. Em vez de extrair os dados diretamente a partir do blockchain, pode consultar os sistemas de base de dados fora da cadeia, como o SQL Server. Os utilizadores finais que executam tarefas de análise de dados não precisam de ter conhecimentos específicos sobre blockchain. 

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Arquitetura do Azure Blockchain Workbench](architecture.md)
