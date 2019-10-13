---
title: Visão geral do desenvolvimento do serviço Blockchain do Azure
description: Introdução ao desenvolvimento de soluções no serviço Blockchain do Azure.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/11/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 2990917243507d93cb6a86da11cf771f3a42eebd
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298423"
---
# <a name="azure-blockchain-service-development-overview"></a>Visão geral do desenvolvimento do serviço Blockchain do Azure

Com o serviço Blockchain do Azure, você pode criar redes Blockchain do consórcio para habilitar cenários empresariais como acompanhamento de ativos, token digital, fidelidade e recompensa, a cadeia de fornecedores financeiros e a comprovada. Este artigo é uma introdução aos tópicos principais e de desenvolvimento do serviço Blockchain do Azure para implementar o Blockchain para empresas.

## <a name="client-connection-to-azure-blockchain-service"></a>Conexão de cliente com o serviço Blockchain do Azure

Há diferentes tipos de clientes para redes blockchain, incluindo nós completos, nós leves e clientes remotos. O serviço Blockchain do Azure cria uma rede Blockchain que inclui nós. Você pode usar clientes diferentes como seu gateway para o serviço Blockchain do Azure para o desenvolvimento do Blockchain. O serviço Blockchain do Azure oferece autenticação básica ou chave de acesso como um ponto de extremidade de desenvolvimento. Estes são clientes populares que você pode usar conectar.

### <a name="metamask"></a>Metamáscara

A metamáscara é uma carteira baseada em navegador (cliente remoto), cliente RPC e Gerenciador de contratos básico. Ao contrário de outras carteiras de navegador, a metamáscara injeta uma instância de Web3 no contexto de JavaScript do navegador, agindo como um cliente RPC que se conecta a uma variedade de Ethereum blockchains (*mainnet*, *Ropsten TESTNET*, *Kovan TESTNET*, nó RPC local, etc.). Você pode configurar o RPC personalizado facilmente para se conectar ao serviço Blockchain do Azure e iniciar o desenvolvimento de Blockchain usando o remix.

### <a name="geth"></a>Geth

Geth é a interface de linha de comando para executar um nó Ethereum completo implementado em go. Você não precisa executar o nó completo, mas pode iniciar seu console interativo que fornece um ambiente de tempo de execução JavaScript expondo uma API JavaScript para interagir com o serviço Blockchain do Azure.

## <a name="development-framework-configuration"></a>Configuração da estrutura de desenvolvimento

Para desenvolver soluções de blockchain empresariais sofisticadas, uma estrutura de desenvolvimento é necessária para se conectar a diferentes redes blockchain, gerenciar o ciclo de vida do contrato inteligente, automatizar o teste, implantar um contrato inteligente com scripts e equipar um console interativo.

Truffle é uma estrutura de desenvolvimento de blockchain popular para escrever, compilar, implantar e testar aplicativos descentralizados no Ethereum blockchains. Você também pode considerar o Truffle como uma estrutura que tenta integrar perfeitamente o desenvolvimento de um Smart Contract e o desenvolvimento para a Web tradicional.

Até mesmo o menor projeto interage com pelo menos dois nós blockchain: um no computador do desenvolvedor e o outro que representa a rede na qual o desenvolvedor implanta seu aplicativo. Por exemplo, a rede pública Ethereum principal ou o serviço Blockchain do Azure. O Truffle fornece um sistema para gerenciar os artefatos de compilação e implantação para cada rede e faz isso de forma a simplificar a implantação final do aplicativo. Para obter mais informações, consulte [início rápido: usar Truffle para se conectar a uma rede do serviço Blockchain do Azure](connect-truffle.md).

## <a name="ethereum-quorum-private-transaction"></a>Transação privada de quorum Ethereum

O quorum é um protocolo contábil distribuído baseado em Ethereum com transação mais privacidade de contrato e novos mecanismos de consenso. Os principais aprimoramentos feitos no go-Ethereum incluem:

* Privacidade – o quorum dá suporte a transações privadas e a contratos privados por meio da separação de estado pública e privada e utiliza trocas de mensagens criptografadas ponto a ponto para transferência direcionada de dados privados para os participantes da rede.
* Mecanismos de consenso alternativos-sem necessidade de um consenso de prova de trabalho ou de prova de jogo em uma rede com permissão. O quorum oferece vários mecanismos de consenso que são projetados para cadeias de consórcio, como reposicionamento e IBFT.  Os serviços Blockchains do Azure usam o mecanismo de consenso IBFT.

* Correspondência de pares-nó e ponto de permissão usando contratos inteligentes, garantindo que apenas partes conhecidas possam ingressar na rede
* Melhor desempenho-o quorum oferece maior desempenho do que o Geth público

Consulte [tutorial: Enviar uma transação usando o serviço Blockchain do Azure](send-transaction.md) para obter um exemplo de transação privada.

## <a name="block-explorers"></a>Bloquear Explorers

Os gerenciadores de blocos são navegadores blockchain online que exibem conteúdo de bloco individual, dados de endereço de transação e histórico. As informações básicas de bloco estão disponíveis por meio de Azure Monitor no serviço Blockchain do Azure, no entanto, se você precisar de informações mais detalhadas durante o desenvolvimento, os gerenciadores de bloqueio podem ser úteis.  Há gerenciadores de blocos de código-fonte aberto que você pode usar. Veja a seguir uma lista de gerenciadores de blocos que funcionam com o serviço Blockchain do Azure:

* [Epirus o Gerenciador de serviços Blockchain do Azure](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview) da Web3 Labs
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

## <a name="tps-measurement"></a>Medição da TPS

Como blockchain é usado em cenários mais empresariais, a velocidade de transações por segundo (TPS) é importante para evitar gargalos e ineficiências do sistema. Altas taxas de transações podem ser difíceis de serem mantidas em um blockchain descentralizado. Uma medição precisa da TPS pode ser afetada por diferentes fatores, como thread do servidor, tamanho da fila de transações, latência de rede e segurança. Se você precisar medir a velocidade da TPS durante o desenvolvimento, uma ferramenta popular de código-fonte aberto será [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Passos seguintes

[Início rápido: usar o Truffle para se conectar a uma rede do serviço Blockchain do Azure](connect-truffle.md)
