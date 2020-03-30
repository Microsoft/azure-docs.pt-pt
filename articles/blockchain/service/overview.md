---
title: Visão geral do Serviço Azure Blockchain
description: Visão geral do Serviço Azure Blockchain
ms.date: 03/12/2020
ms.topic: overview
ms.reviewer: ravastra
ms.openlocfilehash: b9f5deb501fb93327fa5d5cfcfd5bb583ed6135e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79205077"
---
# <a name="what-is-azure-blockchain-service"></a>O que é o Azure Blockchain Service?

O Azure Blockchain Service é um serviço de contabilidade totalmente gerido que permite aos utilizadores a capacidade de crescer e operar redes blockchain em escala no Azure. Ao fornecer um controlo unificado tanto para a gestão de infraestruturas como para a governação da rede blockchain, o Azure Blockchain Service fornece:

* Simples implantação e operações de rede
* Gestão de consórcio incorporado
* Desenvolver contratos inteligentes com ferramentas de desenvolvimento familiares

O Azure Blockchain Service foi concebido para suportar vários protocolos de contabilidade. Atualmente, fornece apoio ao livro-razão ethereum [Quorum](https://www.goquorum.com/) utilizando o mecanismo de consenso [IBFT.](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus)

Estas funcionalidades não exigem praticamente qualquer ação de administração e são todas fornecidas sem custos adicionais. Você pode focar-se no desenvolvimento de aplicações e lógica de negócio, em vez de alocar tempo e recursos para gerir máquinas virtuais e infraestruturas. Além disso, pode continuar a desenvolver a sua aplicação com as ferramentas de código aberto e plataforma da sua escolha para oferecer as suas soluções sem ter de aprender novas competências.

## <a name="network-deployment-and-operations"></a>Implementação e operações de rede

A implementação do Serviço Azure Blockchain é feita através do portal Azure, Azure CLI, ou através do código Visual Studio utilizando a extensão Azure Blockchain. A implantação é simplificada, incluindo o fornecimento de nós de transação e validador, redes virtuais Azure para isolamento de segurança, bem como armazenamento gerido pelo serviço.  Além disso, ao implementar um novo membro blockchain, os utilizadores também criam, ou juntam-se, um consórcio.  Os consórcios permitem que várias partes em diferentes subscrições do Azure possam comunicar-se de forma segura numa blockchain partilhada.  Esta implementação simplificada reduz a implantação da rede blockchain de dias para minutos.

### <a name="performance-and-service-tiers"></a>Níveis de desempenho e serviço

O Azure Blockchain Service oferece dois níveis de serviço: *Básico* e *Standard.* Cada nível oferece diferentes desempenhos e capacidades para suportar o desenvolvimento leve e testar cargas de trabalho até implementações de blockchain de produção massivamente dimensionadas. Utilize o nível *Básico* para desenvolvimento, teste e prova de conceitos. Utilize o nível *Standard* para implementações de qualidade de produção. Ambos os níveis incluem pelo menos um nó de transação, e um nó validante (Básico) ou dois nós validadores (Standard). 

![Escalões de preço](./media/overview/pricing-tiers.png)

Além de oferecer dois nós validadores, o nível *Standard* fornece 2 *vCores* para cada transação e nó validante, enquanto o nível Basic oferece uma configuração de 1 vCore.  Ao oferecer 2 vCores para transações e nós validadores, 1 vCore pode ser dedicado ao livro de contabilidade Quorum enquanto os restantes 1 vCore podem ser usados para outros serviços relacionados com a infraestrutura, garantindo um desempenho ótimo para as cargas de trabalho de blockchain de produção. Para obter mais informações sobre os detalhes dos preços, consulte os preços do [Serviço Azure Blockchain](https://azure.microsoft.com/pricing/details/blockchain-service).

### <a name="security-and-maintenance"></a>Segurança e manutenção

Depois de fornecer o seu primeiro membro blockchain, tem a capacidade de adicionar nós de transação adicionais ao seu membro.  Por predefinição, os nós de transação são protegidos através de regras de firewall e requerem configuração para acesso.  Além disso, todos os nós de transação encriptam dados em movimento via TLS.  Existem várias opções para garantir o acesso ao nó de transações, incluindo regras de firewall, autenticação básica, chaves de acesso e integração do Diretório Ativo Azure. Para mais informações, consulte os nós de [transação e](configure-transaction-nodes.md) configure o [acesso ao Diretório Ativo Azure](configure-aad.md).

Como um serviço gerido, o Azure Blockchain Service garante que os nós do seu membro blockchain são remendados com o mais recente sistema operativo de anfitrião e atualizações de pilhas de software ledger, configuradas para alta disponibilidade (apenas nível Standard), eliminando grande parte dos DevOps necessário para os tradicionais nódosos de blockchain IaaS.  Para obter mais informações sobre patching e atualizações, consulte [as versões de contabilidade do Azure Blockchain Service suportadas](ledger-versions.md).

### <a name="monitoring-and-logging"></a>Monitorização e registos

Além disso, o Azure Blockchain Service fornece métricas ricas através do Serviço De Monitor Azure, fornecendo informações sobre o CPU, memória e armazenamento dos nós.  O Azure Monitor também fornece informações úteis sobre a atividade da rede blockchain, tais como transações e blocos minados, profundidade de fila de transações e ligações ativas.  As métricas podem ser personalizadas para fornecer vistas sobre os insights que são importantes para a sua aplicação blockchain.  Além disso, os limiares podem ser definidos através de alertas que permitem aos utilizadores desencadear ações como o envio de um e-mail ou mensagem de texto, executar uma Aplicação Lógica, Função Azure ou enviar para um webhook definido sob medida.

![Métricas](./media/overview/metrics.png)

Através do Azure Log Analytics, os utilizadores podem visualizar registos relacionados com o livro de contabilidade quorum, ou outras informações importantes, tais como tentativas de ligações aos nós de transação.

## <a name="built-in-consortium-management"></a>Gestão de consórcio incorporado

Ao implementar o seu primeiro membro blockchain, você quer se juntar ou criar um novo consórcio.  Um consórcio é um grupo lógico usado para gerir a governação e conectividade entre membros blockchain que transacionam num processo multipartidário.  O Azure Blockchain Service fornece controlos de governação incorporados através de contratos inteligentes pré-definidos, que determinam que ações os membros do consórcio podem tomar.  Estes controlos de governação podem ser personalizados conforme necessário pelo administrador do consórcio. Ao criar um novo consórcio, o seu membro blockchain é o administrador padrão do consórcio, permitindo a capacidade de convidar outras partes a aderir ao seu consórcio.  Só pode entrar num consórcio se tiver sido previamente convidado.  Ao aderir a um consórcio, o seu membro blockchain está sujeito aos controlos de governação implementados pelo administrador do consórcio.

![Gestão do consórcio](./media/overview/consortium.png)

As ações de gestão do consórcio, tais como a adição e remoção de membros de um consórcio, podem ser acedidas através da PowerShell e de uma API REST. Pode gerir programáticamente um consórcio utilizando interfaces comuns em vez de modificar e submeter contratos inteligentes baseados na solidez. Para mais informações, consulte a [gestão do consórcio.](consortium.md)

## <a name="develop-using-familiar-development-tools"></a>Desenvolver com ferramentas de desenvolvimento familiares

Com base no livro de contabilidade quorum Ethereum de código aberto, pode desenvolver aplicações para o Azure Blockchain Service da mesma forma que você para aplicações Ethereum existentes. Trabalhando com os principais parceiros do setor, a extensão do Código visual do Kit de Desenvolvimento do Kit de Desenvolvimento de Blockchain azure permite que os desenvolvedores aproveitem ferramentas familiares como a Truffle Suite para construir contratos inteligentes. Utilizando a extensão do Kit de Desenvolvimento Azure Blockchain, os desenvolvedores podem criar, ou ligar e ao consórcio existente para que possa construir e implementar os seus contratos inteligentes todos a partir de um IDE. Utilizando a extensão do Código do Estúdio Visual Azure Blockchain, pode criar ou ligar-se a um consórcio existente para que possa construir e implementar os seus contratos inteligentes a partir de um IDE. Para mais informações, consulte o Kit de [Desenvolvimento Azure Blockchain no mercado do Código VS](https://aka.ms/vscodebcextension) e o guia de utilizador do Kit de Desenvolvimento [Azure Blockchain.](https://aka.ms/vscodebcextensionwiki)

## <a name="publish-blockchain-data"></a>Publicar dados blockchain

O Blockchain Data Manager do Azure Blockchain Service captura, transforma e entrega dados de transações do Serviço Azure Blockchain aos Tópicos da Grelha de Eventos Do Evento Azure, proporcionando uma integração fiável e escalável do livro de blockchain com os serviços Azure. Pode utilizar o Blockchain Data Manager para integrar aplicações fora da cadeia e lojas de dados. Para mais informações, consulte [blockchain Data Manager para o Serviço Blockchain Azure](data-manager.md).

## <a name="support-and-feedback"></a>Suporte e comentários

Para notícias azure Blockchain, visite o [blog Azure Blockchain](https://azure.microsoft.com/blog/topics/blockchain/) para se manter atualizado sobre as ofertas de serviços blockchain e informações da equipa de engenharia Azure Blockchain.

Para fornecer feedback do produto ou para solicitar novas funcionalidades, poste de voto para uma ideia através do fórum de [feedback Azure para blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Suporte da comunidade

Envolva-se com engenheiros da Microsoft e especialistas da comunidade Azure Blockchain.

* [Fórum Azure Blockchain MSDN](https://social.msdn.microsoft.com/Forums/home?forum=azureblockchain)
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/AzureBlockchainService)

## <a name="next-steps"></a>Passos seguintes

Para começar, tente um arranque rápido ou descubra mais detalhes destes recursos.
* [Crie um membro blockchain usando o portal Azure](create-member.md) ou [crie um membro blockchain usando o Azure CLI](create-member-cli.md)
* Para comparações de custos e calculadoras, consulte a [página de preços](https://azure.microsoft.com/pricing/details/blockchain-service).
* Construa a sua primeira aplicação usando o Kit de [Desenvolvimento Azure Blockchain](https://github.com/Azure-Samples/blockchain-devkit)
* [Guia de utilizador](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki) de extensão VSCode Azure Blockchain
