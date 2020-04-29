---
title: Visão geral do desenvolvimento do Serviço Azure Blockchain
description: Introdução no desenvolvimento de soluções no Serviço Blockchain Azure.
ms.date: 03/26/2020
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 4a98e5ea025894303bc5c77bba0c6154a08315f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80348384"
---
# <a name="azure-blockchain-service-development-overview"></a>Visão geral do desenvolvimento do Serviço Azure Blockchain

Com o Azure Blockchain Service, pode criar redes blockchain de consórcio para permitir cenários empresariais como rastreio de ativos, token digital, fidelização e recompensa, cadeia de fornecimento financeira e proveniência. As seguintes secções introduzem o desenvolvimento do Serviço Azure Blockchain para a implementação de soluções blockchain da empresa.

## <a name="connecting-to-azure-blockchain-service"></a>Ligação ao Serviço Azure Blockchain

Existem diferentes tipos de clientes para redes blockchain, incluindo nós completos, nós de luz e clientes remotos. O Azure Blockchain Service constrói uma rede blockchain que inclui nódosos. Pode utilizar diferentes clientes como porta de entrada para o Azure Blockchain Service para desenvolvimento de blockchain. O Azure Blockchain Service oferece a autenticação básica ou a chave de acesso como ponto final de desenvolvimento. Seguem-se clientes populares que pode utilizar a ligação.

### <a name="visual-studio-code"></a>Visual Studio Code

Pode ligar-se aos membros do consórcio utilizando a extensão do Código visual do Kit de Desenvolvimento do Kit de Desenvolvimento de Blockchain. Uma vez ligado a um consórcio, pode compilar, construir e implementar contratos inteligentes para um membro do consórcio Azure Blockchain Service.

Para desenvolver soluções sofisticadas de blockchain empresarial, é necessário um quadro de desenvolvimento para se conectar a diferentes redes blockchain e gerir ciclos de vida inteligentes de contratos. A maioria dos projetos interage com pelo menos dois nódosos blockchain. Os desenvolvedores usam uma blockchain local durante o desenvolvimento. Quando a aplicação estiver pronta para testar ou lançar, o desenvolvedor implanta-se numa rede blockchain. Por exemplo, a principal rede pública ethereum ou o Serviço Azure Blockchain. Kit de desenvolvimento Azure Blockchain para extensão Ethereum em Visual Studio Code usa Truffle. Truffle é um popular quadro de desenvolvimento blockchain para escrever, compilar, implementar e testar aplicações descentralizadas em blockchains Ethereum. Você também pode pensar em Truffle como um quadro que tenta integrar perfeitamente o desenvolvimento inteligente de contratos e o desenvolvimento web tradicional.

Para mais informações, consulte [Quickstart: Use Visual Studio Code para se conectar a uma rede de consórcio seleções do Serviço Blockchain Azure](connect-vscode.md).

### <a name="metamask"></a>MetaMask

MetaMask é uma carteira baseada no navegador (cliente remoto), cliente RPC e explorador de contrato básico. Ao contrário de outras carteiras de navegador, o MetaMask injeta uma instância web3 no contexto JavaScript do navegador, atuando como um cliente RPC que se conecta a uma variedade de blockchains Ethereum *(mainnet,* *testnet Ropsten,* *testnet Kovan,* nó RPC local, etc.). Pode configurar o RPC personalizado facilmente para ligar ao Azure Blockchain Service e iniciar o desenvolvimento da blockchain usando o Remix.

Para mais informações, consulte [Quickstart: Use MetaMask para ligar e implementar um contrato inteligente](connect-metamask.md)

### <a name="geth"></a>Estação Geth

Geth é a interface de linha de comando para executar um nó Ethereum completo implementado em Go. Não precisa de executar o nó completo, mas pode lançar a sua consola interativa que fornece um ambiente de tempo de execução JavaScript expondo um API JavaScript para interagir com o Serviço Blockchain Azure.

Para mais informações, consulte [Quickstart: Use Geth para anexar a um nó de transações](connect-geth.md)do Serviço Blockchain Azure .

## <a name="ethereum-quorum-private-transactions"></a>Transações privadas ethereum Quorum

Quorum é um protocolo de contabilidade distribuído baseado no Ethereum com transação mais privacidade contratual e novos mecanismos de consenso. As principais melhorias sobre o Go-Ethereum incluem:

* **Privacidade** - O Quorum apoia transações privadas e contratos privados através da separação do Estado público e privado e utiliza trocas de mensagens encriptadas entre pares para transferência direcionada de dados privados para os participantes da rede.
* Não são **necessários mecanismos de consenso alternativos** - prova de trabalho ou consenso de prova de participação para uma rede autorizada. O Quorum oferece múltiplos mecanismos de consenso que são projetados para cadeias de consórcios como raft e IBFT.O Serviço Azure Blockchain utiliza o mecanismo de consenso IBFT.
* **Permissão por pares** - o nó e a permissão pelos pares usando contratos inteligentes garantem que apenas partes conhecidas podem aderir à rede.
* **Higher Performance** - Quorum oferece um desempenho mais elevado do que o público Geth.

## <a name="block-explorers"></a>Exploradores de blocos

Os exploradores de blocos são navegadores de blockchain online que exibem conteúdo de blocos individuais, dados de endereços de transação e histórico. A informação básica do bloco está disponível através do Azure Monitor no Serviço Blockchain Azure. No entanto, se precisar de mais informações detalhadas durante o desenvolvimento, os exploradores de blocos podem ser úteis.  Os seguintes exploradores de blocos trabalham com o Serviço Azure Blockchain:

* [Epirus Azure Blockchain Service Explorer](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview) da Web3 Labs
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

Também pode construir o seu próprio explorador de blocos utilizando o Blockchain Data Manager e o Azure Cosmos DB, consulte [Tutorial: Use Blockchain Data Manager para enviar dados para o Azure Cosmos DB](data-manager-cosmosdb.md).

## <a name="tps-measurement"></a>Medição de TPS

Como blockchain é usado em mais cenários empresariais, transações por segundo (TPS) velocidade é importante para evitar estrangulamentos e ineficiências do sistema. As elevadas taxas de transação podem ser difíceis de manter dentro de uma blockchain descentralizada. Uma medição tPS precisa pode ser afetada por diferentes fatores, tais como linha de servidor, tamanho da fila de transações, latência da rede e segurança. Se precisar medir a velocidade do TPS durante o desenvolvimento, uma popular ferramenta de código aberto é [chainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Passos seguintes

Experimente um quickstart usando o Kit de Desenvolvimento Azure Blockchain para o Ethereum para anexar a um consórcio no Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Use o Código do Estúdio Visual para ligar ao Serviço Azure Blockchain](connect-vscode.md)