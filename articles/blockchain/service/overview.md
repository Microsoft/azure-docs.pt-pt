---
title: Descrição geral do Azure do serviço de Blockchain
description: Descrição geral do serviço do Azure Blockchain
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: e370916d420a7bc4cd16d021c69a2f8609093d30
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544706"
---
# <a name="what-is-azure-blockchain-service"></a>O que é o serviço do Azure Blockchain?

O serviço do Azure Blockchain é um serviço de contabilidade totalmente gerido que permite aos utilizadores a capacidade de aumentar e operar a redes de blockchain em escala no Azure. Ao fornecer controle unificada para o gerenciamento da infraestrutura, bem como blockchain governação de rede, o serviço do Azure Blockchain fornece:

* Rede simples de implantação e operações
* Gestão de consórcios incorporada
* Desenvolver contratos inteligentes com ferramentas de programação familiar

O serviço do Azure Blockchain foi concebido para suportar vários protocolos de contabilidade. Atualmente, ele fornece suporte para o Ethereum [quórum](https://www.jpmorgan.com/Quorum) contábeis utilizando o [IBFT](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus) mecanismo de consenso.

Estas funcionalidades não exigem praticamente qualquer ação de administração e são todas fornecidas sem custos adicionais. Pode se concentrar no desenvolvimento de aplicações e lógica de negócio em vez de alocar tempo e recursos para gerir máquinas virtuais e infraestruturas. Além disso, pode continuar a desenvolver a sua aplicação com a plataforma à sua escolha para fornecer as suas soluções sem ter de aprender novas competências e ferramentas open source.

## <a name="network-deployment-and-operations"></a>Implantação de rede e operações

Implementar o serviço do Azure Blockchain pode ser feito através do portal do Azure, CLI do Azure, bem como por meio de código do Visual Studio com a extensão do Azure Blockchain.  A implantação foi simplificada, incluindo nós de transação e a validação do, redes virtuais do Azure para isolamento de segurança, bem como armazenamento gerida pelo serviço de aprovisionamento.  Além disso, quando implementar um novo membro de blockchain, os utilizadores também criarem ou associar, um consórcio.  Acordo ativar várias partes em diferentes subscrições do Azure poder comunicar de forma segura entre si, num blockchain partilhado.  Esta implementação simplificada reduz a implementação de rede de blockchain dos dias para minutos.

### <a name="performance-and-service-tiers"></a>Escalões de desempenho e de serviço

O serviço do Azure Blockchain oferece dois escalões de serviço: *Básica* e *padrão*. Cada escalão oferece desempenho e capacidades para suportar o desenvolvimento simples e teste cargas de trabalho até em massa diferentes dimensionado implementações de blockchain de produção. Ambas as camadas incluem o nó, pelo menos, uma transação e um nó de validador (básico) ou dois nós de validador (padrão).

![Escalões de preço](./media/overview/pricing-tiers.png)

Para além de oferecer dois nós de validador, o *padrão* escalão fornece 2 *vCores* para cada transação e a validação do nó, ao passo que o escalão básico oferece uma configuração de vCore 1.  Oferecendo 2 vCores para nós de transação e a validação do, 1 vCore pode ser dedicado ao razão de quórum enquanto o restante de 1 vCore pode ser utilizado para outros serviços relacionados à infra-estrutura, assegurando um desempenho ideal para produção cargas de trabalho de blockchain. Para obter mais informações sobre os detalhes dos preços, consulte [preços do serviço do Azure Blockchain](https://azure.microsoft.com/pricing/details/blockchain-service).

### <a name="security-and-maintenance"></a>Segurança e manutenção

Depois de aprovisionar o seu primeiro membro de blockchain, tem a capacidade de adicionar nós de transações adicionais para o membro.  Por predefinição, os nós de transação são protegidos através de regras de firewall e terá de ser configurada para acesso.  Além disso, todos os nós de transação criptografar dados em movimento através do TLS.  Existem várias opções para proteger o acesso de nó da transação, incluindo regras de firewall, autenticação básica, as chaves de acesso, bem como integração do Azure Active Directory. Para obter mais informações, consulte [configurar os nós de transação](configure-transaction-nodes.md) e [configurar o acesso do Azure Active Directory](configure-aad.md).

Como um serviço gerido, o serviço do Azure Blockchain garante que nós de seu membro de blockchain sejam corrigidos com o anfitrião mais recentes atualizações de pilha de software de sistema e contábeis a funcionar, configurada para elevada disponibilidade (apenas no escalão padrão), eliminando grande parte o DevOps é necessário para nós de blockchain IaaS tradicionais.  Para obter mais informações sobre a aplicação de patches e atualizações, consulte [serviço do Azure Blockchain contábeis versões suportadas](ledger-versions.md).

### <a name="monitoring-and-logging"></a>Monitorização e registos

Além disso, Azure Blockchain Service fornece métricas avançadas através do serviço do Azure Monitor fornecendo informações acerca de nós de CPU, memória e utilização de armazenamento, bem como fornece informações úteis sobre a atividade de rede de blockchain como transações e os blocos são explorados, profundidade de fila de transações, bem como ligações ativas.  As métricas podem ser personalizadas para fornecer vistas das informações que são importantes para a sua aplicação de blockchain.  Além disso, podem definir os limiares através de alertas, permitindo que os usuários acionar ações como enviar uma mensagem de e-mail ou de texto, executar uma aplicação lógica, a função do Azure ou enviar para um webhook personalizado.

![Métricas](./media/overview/metrics.png)

Com o Azure Log Analytics, os utilizadores podem ver os registos relacionados com a razão de quórum, ou outras ligações de informações importantes, como tentativas para os nós de transação.

## <a name="built-in-consortium-management"></a>Gestão de consórcios incorporada

Ao implementar o seu primeiro membro de blockchain, adere ou criar um consórcio de novo.  Um consórcio é um grupo lógico utilizado para gerir a governação e a conectividade entre os membros de blockchain que transact num processo de múltiplos intervenientes.  O serviço do Azure Blockchain fornece controles de governação interna por meio de contratos inteligentes predefinidos, que determinam o que os membros de ações no consortium podem demorar.  Esses controles de governação podem ser personalizados conforme necessário pelo administrador do consortium. Quando cria um novo consortium, o membro de blockchain é o administrador predefinido do consortium, a habilitação da capacidade convidar outras partes para associar sua consortium.  Pode associar um consórcio apenas se foi convidado anteriormente.  Ao associar um consórcio, o membro de blockchain está sujeita os controles de governação colocados em vigor pelo administrador do consórcio.

![Gestão de consórcio](./media/overview/consortium.png)

Ações de gestão de consórcio como adicionar e remover membros de um consórcio podem ser acedidas através do PowerShell e uma API REST. Pode gerir programaticamente um consórcio usando interfaces comuns em vez de modificar e contratos inteligentes com base em solidez de submeter. Para obter mais informações, consulte [gestão de consórcio](consortium.md).

## <a name="develop-using-familiar-development-tools"></a>Desenvolver com as ferramentas de programação familiar

Com base em razão de quórum Ethereum aberto, pode desenvolver aplicativos para o serviço do Azure Blockchain da mesma forma para aplicativos de Ethereum existentes. Trabalhar com parceiros líderes do setor, a extensão do Azure Blockchain Development Kit Visual Studio Code permite que os desenvolvedores tirar partido de ferramentas conhecidas, como Truffle Suite para criar contratos inteligentes. Com a extensão do Kit de desenvolvimento do Azure Blockchain, os desenvolvedores podem criar ou ligar a e consortium existente para que possa criar e implementar o smart contratos todos num IDE. Utilizar a extensão do Azure Blockchain Visual Studio Code, pode criar ou ligar a um consórcio existente para que possa criar e implementar seus contratos inteligentes tudo a partir de um IDE. Para obter mais informações, consulte [Development Kit do Azure Blockchain no mercado do VS Code](https://aka.ms/vscodebcextension) e o [Guia do usuário do Kit de desenvolvimento do Azure Blockchain](https://aka.ms/vscodebcextensionwiki ).

## <a name="support-and-feedback"></a>Suporte e comentários

Precisa de ajuda ou tiver comentários?

* Visite o [blogue do Azure Blockchain](https://azure.microsoft.com/blog/topics/blockchain/), [Comunidade tecnológica da Microsoft](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain), e [fórum do Azure Blockchain](https://social.msdn.microsoft.com/Forums/home?forum=azureblockchain).
* Para enviar comentários ou pedir novas funcionalidades, crie uma entrada através do [UserVoice](https://feedback.azure.com/forums/921130-azure-blockchain-service).

## <a name="next-steps"></a>Passos Seguintes

Para começar, tente um início rápido ou obter mais detalhes destes recursos.
* [Criar um membro de blockchain com o portal do Azure](create-member.md) ou [criar um membro de blockchain com a CLI do Azure](create-member-cli.md)
* Para comparações de preços e calculadoras, consulte a [página de preços](https://azure.microsoft.com/pricing/details/blockchain-service).
* Criar a sua primeira aplicação com o [Development Kit do Azure Blockchain](https://github.com/Azure-Samples/blockchain-devkit)
* Extensão do Azure Blockchain VSCode [Guia do utilizador](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki)
