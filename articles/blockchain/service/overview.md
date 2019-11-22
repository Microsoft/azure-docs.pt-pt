---
title: Visão geral do serviço Blockchain do Azure
description: Visão geral do serviço Blockchain do Azure
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/18/2019
ms.topic: overview
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 55c4c3f2c6a7856f3e7c0aea81ce013d3ff56f8b
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74284791"
---
# <a name="what-is-azure-blockchain-service"></a>O que é o Azure Blockchain Service?

O serviço Blockchain do Azure é um serviço de razão totalmente gerenciado que permite aos usuários aumentar e operar redes Blockchain em escala no Azure. Ao fornecer controle unificado para gerenciamento de infraestrutura, bem como governança de rede blockchain, o serviço Blockchain do Azure fornece:

* Implantação e operações de rede simples
* Gestão de consórcios incorporada
* Desenvolva contratos inteligentes com ferramentas de desenvolvimento familiares

O serviço Blockchain do Azure foi projetado para dar suporte a vários protocolos contábeis. Atualmente, ele fornece suporte para o razão de [Quorum](https://www.jpmorgan.com/Quorum) Ethereum usando o mecanismo de consenso [IBFT](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus) .

Estas funcionalidades não exigem praticamente qualquer ação de administração e são todas fornecidas sem custos adicionais. Você pode se concentrar no desenvolvimento de aplicativos e na lógica de negócios, em vez de alocar tempo e recursos para gerenciar máquinas virtuais e infraestrutura. Além disso, você pode continuar a desenvolver seu aplicativo com as ferramentas de software livre e a plataforma de sua escolha para fornecer suas soluções sem precisar aprender novas habilidades.

## <a name="network-deployment-and-operations"></a>Implantação e operações de rede

A implantação do serviço Blockchain do Azure é feita por meio do portal do Azure, CLI do Azure ou por meio do Visual Studio Code usando a extensão Blockchain do Azure. A implantação é simplificada, incluindo o provisionamento de nós de transação e validação, redes virtuais do Azure para o isolamento de segurança, bem como o armazenamento gerenciado por serviços.  Além disso, ao implantar um novo membro blockchain, os usuários também criam ou ingressam em um consórcio.  Os consórcios permitem que várias partes em diferentes assinaturas do Azure possam se comunicar com segurança umas com as outras em um blockchain compartilhado.  Essa implantação simplificada reduz a implantação de rede blockchain de dias para minutos.

### <a name="performance-and-service-tiers"></a>Camadas de desempenho e de serviço

O serviço Blockchain do Azure oferece duas camadas de serviço: *básico* e *Standard*. Cada camada oferece desempenho e recursos diferentes para dar suporte a cargas de trabalho de desenvolvimento e teste leves para implantações de blockchain de produção amplamente dimensionadas. Ambas as camadas incluem pelo menos um nó de transação e um nó de validador (básico) ou dois nós de validador (padrão).

![Escalões de preço](./media/overview/pricing-tiers.png)

Além de oferecer dois nós de validador, a camada *Standard* fornece 2 *vCores* para cada nó de transação e validador, enquanto a camada básica oferece uma configuração de 1 vCore.  Ao oferecer 2 vCores para nós de transação e validador, 1 vCore pode ser dedicado ao razão de quorum enquanto o 1 vCore restante pode ser usado para outros serviços relacionados à infraestrutura, garantindo o desempenho ideal para cargas de trabalho de produção blockchain. Para obter mais informações sobre os detalhes de preços, consulte [preços do serviço Blockchain do Azure](https://azure.microsoft.com/pricing/details/blockchain-service).

### <a name="security-and-maintenance"></a>Segurança e manutenção

Depois de provisionar seu primeiro membro blockchain, você tem a capacidade de adicionar nós de transação adicionais ao seu membro.  Por padrão, os nós de transação são protegidos por meio de regras de firewall e exigem a configuração de acesso.  Além disso, todos os nós de transação criptografam dados em movimento via TLS.  Existem várias opções para proteger o acesso ao nó da transação, incluindo regras de firewall, autenticação básica, chaves de acesso e integração de Azure Active Directory. Para obter mais informações, consulte [Configurar nós de transação](configure-transaction-nodes.md) e [Configurar o acesso de Azure Active Directory](configure-aad.md).

Como um serviço gerenciado, o serviço Blockchain do Azure garante que os nós de seus membros do Blockchain sejam corrigidos com as atualizações mais recentes do sistema operacional do host e da pilha de software do razão, configurados para alta disponibilidade (somente camada Standard), eliminando grande parte do DevOps necessário para nós blockchain de IaaS tradicionais.  Para obter mais informações sobre patches e atualizações, consulte [versões do razão do serviço Blockchain do Azure com suporte](ledger-versions.md).

### <a name="monitoring-and-logging"></a>Monitorização e registos

Além disso, o serviço Blockchain do Azure fornece métricas avançadas por meio do serviço de Azure Monitor fornecendo informações sobre a CPU, memória e uso de armazenamento dos nós.  Azure Monitor também fornece informações úteis sobre a atividade de rede blockchain, como transações e blocos minados, profundidade da fila de transações e conexões ativas.  As métricas podem ser personalizadas para fornecer exibições para as informações que são importantes para seu aplicativo blockchain.  Além disso, os limites podem ser definidos por meio de alertas, permitindo que os usuários disparem ações como enviar um email ou mensagem de texto, executar um aplicativo lógico, o Azure Function ou enviar para um webhook definido pelo personalizado.

![Métricas](./media/overview/metrics.png)

Por meio do Azure Log Analytics, os usuários podem exibir logs relacionados ao razão de quorum ou outras informações importantes, como tentativas de conexão com os nós de transação.

## <a name="built-in-consortium-management"></a>Gestão de consórcios incorporada

Ao implantar seu primeiro membro blockchain, você pode ingressar ou criar um novo consórcio.  Um consórcio é um grupo lógico usado para gerenciar a governança e a conectividade entre os membros do blockchain que estão em um processo de várias partes.  O serviço Blockchain do Azure fornece controles de governança internos por meio de contratos inteligentes predefinidos, que determinam quais ações os membros do Consórcio podem tomar.  Esses controles de governança podem ser personalizados conforme necessário pelo administrador do consórcio. Quando você cria um novo consórcio, seu membro do blockchain é o administrador padrão do consórcio, permitindo convidar outras partes para ingressar no consórcio.  Você só poderá ingressar em um consórcio se tiver sido convidado anteriormente.  Ao ingressar em um consórcio, seu membro blockchain está sujeito aos controles de governança colocados em vigor pelo administrador do consórcio.

![Gerenciamento de consórcio](./media/overview/consortium.png)

Ações de gerenciamento de consórcio, como adicionar e remover membros de um consórcio, podem ser acessadas por meio do PowerShell e de uma API REST. Você pode gerenciar programaticamente um consórcio usando interfaces comuns em vez de modificar e enviar contratos inteligentes baseados em solidez. Para obter mais informações, consulte [Gerenciamento de consórcio](consortium.md).

## <a name="develop-using-familiar-development-tools"></a>Desenvolver usando ferramentas de desenvolvimento familiares

Com base na contabilidade de Ethereum de quorum de software livre, você pode desenvolver aplicativos para o serviço Blockchain do Azure da mesma maneira como faz para aplicativos Ethereum existentes. Trabalhando com parceiros líderes do setor, a extensão de Visual Studio Code do kit de desenvolvimento do Azure Blockchain permite que os desenvolvedores aproveitem ferramentas familiares, como o Truffle Suite, para criar contratos inteligentes. Usando a extensão do kit de desenvolvimento do Azure Blockchain, os desenvolvedores podem criar ou conectar-se a um consórcio existente para que você possa criar e implantar seus contratos inteligentes de um IDE. Usando a extensão de Visual Studio Code do Blockchain do Azure, você pode criar ou se conectar a um consórcio existente para que você possa criar e implantar seus contratos inteligentes em um IDE. Para obter mais informações, consulte [Kit de desenvolvimento do Azure Blockchain no vs Code Marketplace](https://aka.ms/vscodebcextension) e [Guia do usuário do kit de desenvolvimento do Blockchain do Azure](https://aka.ms/vscodebcextensionwiki ).

## <a name="support-and-feedback"></a>Suporte e comentários

Precisa de ajuda ou tem comentários?

* Visite o [blog do Azure Blockchain](https://azure.microsoft.com/blog/topics/blockchain/), a [comunidade técnica da Microsoft e o](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain) [Fórum do Azure Blockchain](https://social.msdn.microsoft.com/Forums/home?forum=azureblockchain).
* Para enviar comentários ou pedir novas funcionalidades, crie uma entrada através do [UserVoice](https://feedback.azure.com/forums/921130-azure-blockchain-service).

## <a name="next-steps"></a>Passos seguintes

Para começar, experimente um início rápido ou descubra mais detalhes desses recursos.
* [Criar um membro do blockchain usando o portal do Azure](create-member.md) ou [criar um membro do blockchain usando CLI do Azure](create-member-cli.md)
* Para comparações de custo e calculadoras, consulte a [página de preços](https://azure.microsoft.com/pricing/details/blockchain-service).
* Crie seu primeiro aplicativo usando o [Kit de desenvolvimento Blockchain do Azure](https://github.com/Azure-Samples/blockchain-devkit)
* [Guia do usuário](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki) da extensão VSCode do Azure Blockchain
