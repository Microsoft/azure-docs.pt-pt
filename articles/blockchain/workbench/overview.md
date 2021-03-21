---
title: Visão geral da pré-visualização da bancada de trabalho de Azure Blockchain
description: Visão geral da pré-visualização da bancada de trabalho Azure Blockchain e das suas capacidades.
ms.date: 05/22/2020
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: fbd6be3907dbd10b003d065dfb14031a0e378478
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87003157"
---
# <a name="what-is-azure-blockchain-workbench"></a>O que é o Azure Blockchain Workbench?

A Azure Blockchain Workbench Preview é uma coleção de serviços e capacidades da Azure projetadas para ajudá-lo a criar e implementar aplicações blockchain para partilhar processos de negócio e dados com outras organizações. O Azure Blockchain Workbench fornece as bases de infraestrutura para a criação de aplicações blockchain, o que permite aos programadores concentrarem-se na criação da lógica de negócio e de contratos inteligentes. Também simplifica a criação de aplicações blockchain graças à integração de várias funcionalidades e serviços do Azure que ajudam a automatizar as tarefas de desenvolvimento comuns.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="create-blockchain-applications"></a>Criar aplicações blockchain

Com o Blockchain Workbench, pode definir as aplicações blockchain a utilizar a configuração e da escrita de código para contratos inteligentes. Pode impulsionar o desenvolvimento das aplicações blockchain e focar-se na definição de contratos e na escrita de lógica de negócio em vez de dedicar-se à criação e configuração de serviços de suporte.

## <a name="manage-applications-and-users"></a>Gerir aplicações e utilizadores

O Azure Blockchain Workbench disponibiliza uma aplicação Web e APIs REST para a gestão de utilizadores e aplicações blockchain. Os administradores do Blockchain Workbench podem gerir o acesso à aplicação e atribuir os seus utilizadores a funções da aplicação. Os utilizadores do Azure AD são mapeados automaticamente para membros na aplicação.

## <a name="integrate-blockchain-with-applications"></a>Integrar o blockchain com as aplicações

Pode utilizar as APIs baseadas em mensagens e as APIs REST do Blockchain Workbench para promover a integração com os sistemas existentes. As APIs fornecem uma interface para permitir a substituição ou a utilização de várias tecnologias de registo distribuído, armazenamento e ofertas de bases de dados.

Blockchain Workbench pode transformar mensagens enviadas para a sua API baseada em mensagens para construir transações num formato esperado pela API nativa da blockchain.  O Workbench pode assinar e encaminhar transações para o blockchain adequado. 

O Workbench encaminha automaticamente os eventos para o Service Bus e o Event Grid para enviar mensagens aos consumidores a jusante. Os programadores podem integrar qualquer um destes sistemas de mensagens para promover as transações e analisar os resultados.

## <a name="deploy-a-blockchain-network"></a>Implementar uma rede blockchain

O Azure Blockchain Workbench simplifica a configuração da rede blockchain em consórcio sob a forma de uma solução pré-configurada com um modelo da solução Azure Resource Manager. O modelo oferece uma implementação simplificada que implementa todos os componentes necessários à execução de um consórcio. Atualmente, o Blockchain Workbench suporta Ethereum.

## <a name="use-active-directory"></a>Utilizar o Active Directory

Com os protocolos de blockchain existentes, as identidades de blockchain são representadas como um endereço na rede. O Azure Blockchain Workbench abstrai a identidade de blockchain ao associá-la a uma identidade do Active Directory, o que simplifica a criação de aplicações empresariais com identidades do Active Directory.

## <a name="synchronize-on-chain-data-with-off-chain-storage"></a>Sincronizar dados dentro da cadeia com armazenamento fora da cadeia

O Azure Blockchain Workbench facilita a análise dos dados e eventos blockchain ao sincronizar automaticamente os dados no blockchain com o armazenamento fora da cadeia. Em vez de extrair os dados diretamente a partir do blockchain, pode consultar os sistemas de base de dados fora da cadeia, como o SQL Server. A experiência blockchain não é necessária para utilizadores finais que estão a fazer tarefas de análise de dados.

## <a name="support-and-feedback"></a>Suporte e comentários

Para notícias da Azure Blockchain, visite o [blog Azure Blockchain](https://azure.microsoft.com/blog/topics/blockchain/) para se manter atualizado sobre ofertas de serviços blockchain e informações da equipa de engenharia Azure Blockchain.

Para fornecer feedback do produto ou para solicitar novas funcionalidades, publicar ou votar por uma ideia através do [fórum de feedback Azure para blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Suporte da comunidade

Envolva-se com engenheiros da Microsoft e especialistas da comunidade Azure Blockchain.

* [Microsoft Q&Uma página de perguntas para a bancada de trabalho do Azure Blockchain](/answers/topics/azure-blockchain-workbench.html)
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Arquitetura do Azure Blockchain Workbench](architecture.md)
