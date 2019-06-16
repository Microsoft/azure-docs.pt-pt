---
title: Descrição geral de desenvolvimento de Blockchain serviço do Azure
description: Introdução no desenvolvimento de soluções no Azure Blockchain Service.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 388a5d8c80c3e2462602959e9d5cbc1452974d1f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027904"
---
# <a name="azure-blockchain-service-development-overview"></a>Descrição geral de desenvolvimento de Blockchain serviço do Azure

Com o serviço do Azure Blockchain, pode criar consortium redes de blockchain para permitir cenários empresariais, como controle de ativos destinados, digital token, fidelidade e recompensa, cadeia de fornecimento financeiros e provenance. Este artigo é uma introdução à descrição geral do desenvolvimento de serviço do Azure Blockchain e tópicos principais para implementar o blockchain para a empresa.

## <a name="client-connection-to-azure-blockchain-service"></a>Ligação de cliente no serviço do Azure Blockchain

Existem diferentes tipos de clientes para redes de blockchain, incluindo nós completas, nós leves e clientes remotos. O serviço do Azure Blockchain baseia-se uma rede de blockchain que inclui nós. Pode usar diferentes clientes como o seu gateway no serviço do Azure Blockchain para o desenvolvimento de blockchain. O serviço do Azure Blockchain oferece autenticação básica ou chave de acesso como um ponto de extremidade de desenvolvimento. Seguem-se os clientes populares, pode utilizar o ligar.

### <a name="metamask"></a>MetaMask

MetaMask é uma carteira baseada no browser (cliente remoto), o cliente RPC e o Explorador de contrato básico. Ao contrário de outros wallets de navegador, MetaMask injeta uma instância de web3 no contexto de JavaScript do navegador, que atua como um cliente RPC que se liga a uma variedade de blockchain Ethereum (*mainnet*, *Ropsten testnet*, *Kovan testnet*, nó RPC local, etc.). Pode configurar RPC personalizado facilmente para ligar ao serviço do Azure Blockchain e começar o desenvolvimento de blockchain com Remix.

### <a name="geth"></a>Geth

Geth é a interface de linha de comandos para a execução de um nó completo de Ethereum implementado em Go. Não precisa de executar completa de nó, mas pode iniciar a sua consola interativa que fornece um ambiente de tempo de execução de JavaScript expor uma API de JavaScript para interagir com o serviço do Azure Blockchain.

## <a name="development-framework-configuration"></a>Configuração da estrutura de desenvolvimento

Para desenvolver soluções de blockchain empresarial sofisticada, uma estrutura de desenvolvimento é necessária para ligar a redes de blockchain diferentes, gerir o ciclo de vida do contrato inteligente, automatizar os testes, implementar o contrato inteligente com scripts e proporciona uma consola interativa.

Truffle é uma estrutura de desenvolvimento de blockchain populares para escrever, compilar, implantar e testar aplicativos descentralizados em Ethereum blockchain. Também pode pensar Truffle como uma estrutura que tenta integrar totalmente o desenvolvimento de smart de contrato e desenvolvimento da web tradicional.

Até mesmo o projeto menor interage com, pelo menos, dois nós de blockchain: Uma na máquina do desenvolvedor e o outro que representa a rede em que o desenvolvedor implanta seu aplicativo. Por exemplo, a rede principal de Ethereum pública ou serviço do Azure Blockchain. Truffle fornece um sistema para gerenciar os artefactos de compilação e implementação para cada rede e faz isso de forma que simplifica a implementação de aplicação final. Para obter mais informações, consulte [início rápido: Utilizar Truffle para ligar a uma rede de um serviço do Azure Blockchain](connect-truffle.md).

## <a name="ethereum-quorum-private-transaction"></a>Transação de Ethereum quórum privada

Quórum é um protocolo baseado em Ethereum livro razão distribuído com a transação e privacidade de contrato e novos mecanismos de consenso. Principais aprimoramentos de segurança através de Go-Ethereum incluem:

* Privacidade - quórum suporta transações privadas e contratos privados através de separação de estado públicas e privadas e utiliza as trocas de mensagens de criptografado de ponto-a-ponto direcionado transferência de dados privados aos participantes de rede.
* Alternativos mecanismos de consenso - sem necessidade de uma prova de trabalho ou uma prova de jogo consenso numa rede recebendo. Quórum oferece vários mecanismos de consenso que foram concebidos para cadeias de consórcio como série e IBFT.  Os serviços do Azure Blockchain utiliza o mecanismo de consenso IBFT.

* Configurar o peering concessão de permissões - concessão de permissões de nós e ponto a ponto com contratos inteligentes, garantir que apenas as partes conhecidas pode juntar-se a rede
* Um desempenho mais elevado - quórum oferece um desempenho mais elevado do que Geth pública

Consulte [Tutorial: Enviar uma transação com o Azure Blockchain Service](send-transaction.md) para obter um exemplo de transação privada.

## <a name="block-explorers"></a>Exploradores de bloco

Exploradores de bloco são os browsers de online blockchain que exibem conteúdo de bloco individuais, dados de transação de endereços e histórico. Informações de bloco básico estão disponíveis através do Azure Monitor no serviço do Azure Blockchain, no entanto, se precisar de obter informações mais detalhadas durante o desenvolvimento, os gerenciadores de bloco podem ser útil.  Existem exploradores de bloco de código aberto populares, que pode utilizar. Segue-se uma lista de gerenciadores de bloco que funcionam com o Azure Blockchain Service:

* [O serviço do Azure Blockchain Explorer](https://web3labs.com/azure-offer) de Web3 laboratórios
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

## <a name="tps-measurement"></a>Medida de TPS

Como o blockchain é utilizado em mais cenários de empresa, transações por segundo (TPS) velocidade é importante para evitar afunilamentos e ineficiências de sistema. Velocidades de transação elevada podem ser difícil manter-se dentro de um blockchain descentralizado. Uma medida de TPS precisa pode ser afetada por fatores diferentes como thread do servidor, tamanho da fila de transações, latência de rede e segurança. Se precisar medir a velocidade TPS durante o desenvolvimento, é uma ferramenta de código aberto popular [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Passos Seguintes

[Quickstart: Utilizar Truffle para ligar a uma rede de um serviço do Azure Blockchain](connect-truffle.md)
