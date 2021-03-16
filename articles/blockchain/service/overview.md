---
title: Visão geral do Serviço Azure Blockchain
description: Visão geral do Serviço Azure Blockchain
ms.date: 03/15/2021
ms.topic: overview
ms.reviewer: patricka
ms.openlocfilehash: 035e2c1df5912f291ad241ade25361f9cc098b22
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573630"
---
# <a name="what-is-azure-blockchain-service"></a>O que é o Azure Blockchain Service?

O Azure Blockchain Service é um serviço de contabilidade totalmente gerido que dá aos utilizadores a capacidade de crescer e operar redes blockchain em escala em Azure. Ao fornecer um controlo unificado tanto para a gestão de infraestruturas como para a governação da rede blockchain, o Serviço Azure Blockchain fornece:

* Simples implantação e operações de rede
* Gestão de consórcios incorporados
* Desenvolver contratos inteligentes com ferramentas de desenvolvimento familiares

O Azure Blockchain Service foi concebido para suportar vários protocolos de contabilidade. Atualmente, fornece suporte para o livro-razão Ethereum [Quorum](https://www.goquorum.com/) utilizando o mecanismo de consenso [de Tolerância à Falha Bizantina de Istambul (IBFT).](https://docs.goquorum.consensys.net/en/stable/Concepts/Consensus/IBFT/)

Estas funcionalidades não exigem praticamente qualquer ação de administração e são todas fornecidas sem custos adicionais. Você pode focar-se no desenvolvimento de aplicações e lógica de negócio em vez de alocar tempo e recursos para gerir máquinas virtuais e infraestruturas. Além disso, pode continuar a desenvolver a sua aplicação com as ferramentas de código aberto e plataforma à sua escolha para entregar as suas soluções sem ter de aprender novas competências.

## <a name="network-deployment-and-operations"></a>Implementação e operações de rede

A implementação do Serviço Azure Blockchain é feita através do portal Azure CLI ou através do código Visual Studio utilizando a extensão Azure Blockchain. A implementação é simplificada, incluindo o fornecimento de nós de transação e validação, redes virtuais Azure para isolamento de segurança, bem como armazenamento gerido pelo serviço.  Além disso, ao implementar um novo membro blockchain, os utilizadores também criam, ou se juntam, um consórcio.  Os consórcios permitem que várias partes em diferentes subscrições do Azure possam comunicar-se de forma segura numa blockchain partilhada.  Esta implementação simplificada reduz a implementação da rede blockchain de dias para minutos.

### <a name="performance-and-service-tiers"></a>Níveis de desempenho e serviço

O Azure Blockchain Service oferece dois níveis de serviço: *Básico* e *Standard*. Cada nível oferece diferentes desempenhos e capacidades para apoiar o desenvolvimento leve e testar cargas de trabalho até implementações de blockchain de produção massivamente dimensionada. Utilize o nível *básico* para desenvolvimento, teste e prova de conceitos. Utilize o nível *Standard* para implantações de nível de produção. Ambos os níveis incluem pelo menos um nó de transação, e um nó validador (Básico) ou dois nós validadores (Standard). 

![Escalões de preço](./media/overview/pricing-tiers.png)

Além de oferecer dois nós validadores, o nível *Standard* fornece dois *vCores* para cada transação e nó validador, enquanto o nível *Básico* oferece uma configuração de 1 vCore.  Ao oferecer 2 vCores para transação e nódoas validadores, 1 vCore pode ser dedicado ao livro de contabilidade Qurum, enquanto os restantes 1 vCore podem ser utilizados para outros serviços relacionados com infraestruturas, garantindo o melhor desempenho para cargas de trabalho blockchain de produção. Para obter mais informações sobre os detalhes dos preços, consulte [os preços do Serviço Azure Blockchain](https://azure.microsoft.com/pricing/details/blockchain-service).

### <a name="security-and-maintenance"></a>Segurança e manutenção

Depois de providenciar o seu primeiro membro blockchain, tem a capacidade de adicionar nós de transação adicionais ao seu membro.  Por predefinição, os nós de transação são protegidos através das regras de firewall e requerem configuração para acesso.  Além disso, todos os nós de transação encriptam dados em movimento via TLS.  Existem várias opções para garantir o acesso ao nó de transação, incluindo regras de firewall, autenticação básica, chaves de acesso e integração do Azure Ative Directory. Para obter mais informações, consulte [os nós de transação de configuração](configure-transaction-nodes.md) e [configuure acesso ao Diretório Ativo Azure](configure-aad.md).

Como um serviço gerido, o Azure Blockchain Service garante que os nós do seu membro blockchain são corrigidos com as mais recentes atualizações de software de software de anfitrião e de software de contabilidade, configuradas para alta disponibilidade (apenas de nível padrão), eliminando grande parte dos DevOps necessários para os tradicionais nós de blockchain IaaS.  Para obter mais informações sobre patching e atualizações, consulte as [versões de contabilidade do Azure Blockchain Service suportadas](ledger-versions.md).

### <a name="monitoring-and-logging"></a>Monitorização e registos

Além disso, o Azure Blockchain Service fornece métricas ricas através do Azure Monitor Service, fornecendo informações sobre o CPU, memória e armazenamento dos nós.  O Azure Monitor também fornece informações úteis sobre a atividade da rede blockchain, tais como transações e blocos minados, profundidade de fila de transações e conexões ativas.  As métricas podem ser personalizadas para fornecer visualizações sobre os insights que são importantes para a sua aplicação blockchain.  Além disso, os limiares podem ser definidos através de alertas que permitem aos utilizadores desencadear ações como o envio de um e-mail ou mensagem de texto, executar uma App Lógica, Função Azure ou enviar para um webhook personalizado.

![A captura de ecrãs mostra monitorização, com valores para blocos, transações, transações pendentes e pedidos tratados.](./media/overview/metrics.png)

Através do Azure Log Analytics, os utilizadores podem visualizar registos relacionados com o livro de contabilidade do Quorum, ou outras informações importantes, tais como tentativas de ligações aos nós de transação.

## <a name="built-in-consortium-management"></a>Gestão de consórcios incorporados

Ao implementar o seu primeiro membro blockchain, ou se junta ou cria um novo consórcio.  Um consórcio é um grupo lógico usado para gerir a governação e conectividade entre membros blockchain que transacionam num processo multipartidária.  O Azure Blockchain Service fornece controlos de governação incorporados através de contratos inteligentes pré-definidos, que determinam que ações os membros do consórcio podem tomar.  Estes controlos de governação podem ser personalizados conforme necessário pelo administrador do consórcio. Quando cria um novo consórcio, o seu membro blockchain é o administrador padrão do consórcio, permitindo a capacidade de convidar outras partes para se juntarem ao seu consórcio.  Só pode aderir a um consórcio se tiver sido convidado anteriormente.  Ao aderir a um consórcio, o seu membro blockchain está sujeito aos controlos de governação implementados pelo administrador do consórcio.

![Gestão do consórcio](./media/overview/consortium.png)

As ações de gestão do consórcio, tais como a adição e remoção de membros de um consórcio, podem ser acedidas através da PowerShell e de uma API REST. Pode gerir programáticamente um consórcio usando interfaces comuns em vez de modificar e submeter contratos inteligentes baseados na solidez. Para mais informações, consulte [a gestão do consórcio.](consortium.md)

## <a name="develop-using-familiar-development-tools"></a>Desenvolver com ferramentas de desenvolvimento familiares

Com base no livro-razão Quorum Ethereum de origem aberta, pode desenvolver aplicações para o Azure Blockchain Service da mesma forma que para as aplicações Ethereum existentes. Trabalhando com os principais parceiros do setor, a extensão do Azure Blockchain Blockchain Development Kit Visual Studio Code permite que os desenvolvedores aproveitem ferramentas familiares como a Truffle Suite para construir contratos inteligentes. Utilizando a extensão do Código do Estúdio Visual Azure Blockchain, pode criar ou ligar-se a um consórcio existente para que possa construir e implementar os seus contratos inteligentes todos a partir de um IDE. Para obter mais informações, consulte [o Kit de Desenvolvimento blockchain do Azure no mercado do Código VS](https://aka.ms/vscodebcextension) e no guia do kit de desenvolvimento do [Azure Blockchain.](https://aka.ms/vscodebcextensionwiki)

## <a name="publish-blockchain-data"></a>Publicar dados de blockchain

O Gestor de Dados blockchain para o Azure Blockchain Service captura, transforma e entrega dados de transações do Serviço Azure Blockchain a tópicos de grelha de eventos Azure que proporcionam uma integração fiável e escalável de livros blockchain com serviços Azure. Pode utilizar o Blockchain Data Manager para integrar aplicações off-chain e lojas de dados. Para mais informações, consulte [o Gestor de Dados blockchain para o Serviço Azure Blockchain](data-manager.md).

## <a name="support-and-feedback"></a>Suporte e comentários

Para notícias da Azure Blockchain, visite o [blog Azure Blockchain](https://azure.microsoft.com/blog/topics/blockchain/) para se manter atualizado sobre ofertas de serviços blockchain e informações da equipa de engenharia Azure Blockchain.

Para fornecer feedback do produto ou para solicitar novas funcionalidades, publicar ou votar por uma ideia através do [fórum de feedback Azure para blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Suporte da comunidade

Envolva-se com engenheiros da Microsoft e especialistas da comunidade Azure Blockchain.

* [Microsoft Q&Uma página de perguntas para o Serviço Azure Blockchain](/answers/topics/azure-blockchain-service.html)
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-service)

## <a name="next-steps"></a>Passos seguintes

Para começar, experimente um quickstart ou descubra mais detalhes destes recursos.
* [Crie um membro blockchain usando o portal Azure](create-member.md) ou [crie um membro blockchain usando O Azure CLI](create-member-cli.md)
* Siga o caminho microsoft Learn [Começar com o desenvolvimento do blockchain](/learn/paths/ethereum-blockchain-development)
* Assista à [série do Principiante para blockchain](https://channel9.msdn.com/Series/Beginners-Series-to-Blockchain)
* Para comparações de custos e calculadoras, consulte [a página de preços](https://azure.microsoft.com/pricing/details/blockchain-service)
* Construa a sua primeira aplicação utilizando o [Kit de Desenvolvimento blockchain Azure](https://github.com/Azure-Samples/blockchain-devkit)
* [Guia de utilizadores](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki) da extensão Azure Blockchain VSCode
