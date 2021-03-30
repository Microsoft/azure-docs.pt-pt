---
title: Visão geral do desenvolvimento do serviço Azure Blockchain
description: Introdução no desenvolvimento de soluções no Serviço Azure Blockchain.
ms.date: 03/26/2020
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 4a98e5ea025894303bc5c77bba0c6154a08315f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "80348384"
---
# <a name="azure-blockchain-service-development-overview"></a>Visão geral do desenvolvimento do serviço Azure Blockchain

Com o Azure Blockchain Service, pode criar redes de blockchain de consórcio para permitir cenários empresariais como rastreio de ativos, token digital, fidelização e recompensa, cadeia de fornecimento financeira e proveniência. As secções seguintes introduzem o desenvolvimento do Serviço Azure Blockchain para implementar soluções de blockchain empresarial.

## <a name="connecting-to-azure-blockchain-service"></a>Ligação ao Serviço Azure Blockchain

Existem diferentes tipos de clientes para redes blockchain, incluindo nós completos, nós de luz e clientes remotos. O Azure Blockchain Service constrói uma rede blockchain que inclui nós. Pode utilizar diferentes clientes como porta de entrada para o Serviço Azure Blockchain para o desenvolvimento da blockchain. O Azure Blockchain Service oferece a autenticação básica ou a chave de acesso como ponto final de desenvolvimento. Seguem-se clientes populares que pode utilizar no connect.

### <a name="visual-studio-code"></a>Visual Studio Code

Pode ligar-se aos membros do consórcio utilizando a extensão do Código do Estúdio Visual do Kit de Desenvolvimento Azure Blockchain. Uma vez ligado a um consórcio, você pode compilar, construir e implementar contratos inteligentes para um membro do consórcio Azure Blockchain Service.

Para desenvolver soluções sofisticadas de blockchain empresarial, é necessário um quadro de desenvolvimento para ligar a diferentes redes blockchain e gerir ciclos de vida de contratos inteligentes. A maioria dos projetos interagem com pelo menos dois nós blockchain. Os desenvolvedores usam uma blockchain local durante o desenvolvimento. Quando a aplicação estiver pronta para teste ou lançamento, o desenvolvedor implementa-se numa rede blockchain. Por exemplo, a principal rede pública Ethereum ou O Serviço Azure Blockchain. O Kit de Desenvolvimento Azure Blockchain para extensão Ethereum no Código do Estúdio Visual utiliza Truffle. Truffle é um quadro popular de desenvolvimento blockchain para escrever, compilar, implementar e testar aplicações descentralizadas em blockchains Ethereum. Você também pode pensar em Truffle como um quadro que tenta integrar perfeitamente o desenvolvimento inteligente de contratos e o desenvolvimento web tradicional.

Para obter mais informações, consulte [Quickstart: Use Visual Studio Code para ligar a uma rede de consórcios do Azure Blockchain Service](connect-vscode.md).

### <a name="metamask"></a>MetaMask

MetaMask é uma carteira baseada no navegador (cliente remoto), cliente RPC e explorador de contratos básico. Ao contrário de outras carteiras de navegador, metaMask injeta uma instância web3 no contexto javaScript do navegador, agindo como um cliente RPC que se conecta a uma variedade de blockchains Ethereum *(mainnet,* *Ropsten testnet,* *Kovan testnet,* nó RPC local, etc.). Pode configurar o RPC personalizado facilmente para ligar ao Azure Blockchain Service e iniciar o desenvolvimento da blockchain usando o Remix.

Para obter mais informações, consulte [Quickstart: Use MetaMask para ligar e implementar um contrato inteligente](connect-metamask.md)

### <a name="geth"></a>Geth

Geth é a interface de linha de comando para executar um nó Ethereum completo implementado em Go. Não precisa de executar o nó completo, mas pode lançar a sua consola interativa que fornece um ambiente de tempo de execução JavaScript expondo uma API JavaScript para interagir com o Azure Blockchain Service.

Para obter mais informações, consulte [Quickstart: Use Geth para anexar um nó de transação do Serviço Azure Blockchain](connect-geth.md).

## <a name="ethereum-quorum-private-transactions"></a>Transações privadas Ethereum Quorum

O Qurum é um protocolo de contabilidade distribuído baseado no Ethereum com transações mais privacidade contratual e novos mecanismos de consenso. As principais melhorias ao longo Go-Ethereum incluem:

* **Privacidade** - O Quorum apoia transações privadas e contratos privados através da separação do Estado público e privado e utiliza trocas de mensagens encriptadas entre pares para transferência direcionada de dados privados para os participantes da rede.
* **Mecanismos** de consenso alternativos - prova de trabalho ou consenso de prova de participação não são necessários para uma rede autorizada. O Quorum oferece múltiplos mecanismos de consenso concebidos para cadeias de consórcios como RAFT e IBFT.  O Serviço Azure Blockchain utiliza o mecanismo de consenso IBFT.
* **Permissão por pares** - a permissão de nó e pares usando contratos inteligentes garante que apenas as partes conhecidas podem aderir à rede.
* **Maior Desempenho** - O Quorum oferece um desempenho superior ao do Geth público.

## <a name="block-explorers"></a>Exploradores de blocos

Os exploradores de blocos são navegadores de blockchain online que exibem conteúdo de bloco individual, dados de endereços de transação e histórico. As informações básicas do bloco estão disponíveis através do Azure Monitor no Serviço Azure Blockchain. No entanto, se precisar de mais informações detalhadas durante o desenvolvimento, os exploradores de blocos podem ser úteis.  Os seguintes exploradores de blocos trabalham com o Serviço Azure Blockchain:

* [Explorador de serviços Epirus Azure Blockchain](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview) da Web3 Labs
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

Também pode construir o seu próprio explorador de blocos utilizando o Blockchain Data Manager e o Azure Cosmos DB, consulte [Tutorial: Use o Blockchain Data Manager para enviar dados para Azure Cosmos DB](data-manager-cosmosdb.md).

## <a name="tps-measurement"></a>Medição de TPS

Como o blockchain é usado em mais cenários empresariais, as transações por segundo (TPS) são importantes para evitar estrangulamentos e ineficiências do sistema. As elevadas taxas de transação podem ser difíceis de manter dentro de uma blockchain descentralizada. Uma medição exata de TPS pode ser afetada por diferentes fatores, tais como o fio do servidor, o tamanho da fila de transações, a latência da rede e a segurança. Se precisar de medir a velocidade de TPS durante o desenvolvimento, uma ferramenta popular de código aberto é [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Passos seguintes

Experimente um quickstart usando o Azure Blockchain Development Kit para o Ethereum para anexar a um consórcio no Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Use código de estúdio visual para ligar ao Serviço Azure Blockchain](connect-vscode.md)