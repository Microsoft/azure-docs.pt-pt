---
title: Versões de contabilidade do Azure Blockchain Service, patching, & atualização
description: Visão geral das versões de livros suportados no Azure Blockchain Service. Incluindo políticas de remendação de sistemas e atualizações.
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: ea7c54e229178320329204a3199ab2b4c44058fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85807745"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Versões suportadas do Azure Blockchain Service

O Azure Blockchain Service utiliza o contabilidade [quórum](https://www.goquorum.com/developers) baseado no Ethereum projetado para o processamento de transações privadas dentro de um grupo de participantes conhecidos, identificado como um consórcio no Azure Blockchain Service.

Atualmente, o Azure Blockchain Service suporta a [versão 2.6.0](https://github.com/jpmorganchase/quorum/releases/tag/v2.6.0) do Quorum e [o gestor de transações tessera.](https://github.com/jpmorganchase/tessera)

## <a name="managing-updates-and-upgrades"></a>Gerir atualizações e atualizações

A versão em Quorum é feita através de lançamentos importantes, menores e de patch. Por exemplo, se a versão Qurum for 2.0.1, o tipo de libertação será classificado da seguinte forma:

|Maior | Menor  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

O Azure Blockchain Service atualiza automaticamente as versões de patch do Quorum para os membros de execução existentes no prazo de 30 dias após a disponibilização do Quorum.

## <a name="availability-of-new-ledger-versions"></a>Disponibilidade de novas versões de contabilidade

O Azure Blockchain Service fornece as versões principais e menores do livro de contabilidade Do Quorum no prazo de 60 dias após a sua disponibilização pelo fabricante Quorum. Um máximo de quatro lançamentos menores são fornecidos para que os consórcios escolham quando a provisionar um novo membro e um consórcio. Atualmente, não é suportada a atualização de uma libertação maior ou menor. Por exemplo, se estiver a executar a versão 2.x, um upgrade para a versão 3.x não está suportado. Da mesma forma, se estiver a executar a versão 2.2, atualmente não é suportada uma atualização para a versão 2.3.

## <a name="how-to-check-quorum-ledger-version"></a>Como verificar a versão do livro de razão Do Quorum

Pode verificar a versão Qurum no seu membro do Serviço Azure Blockchain, fixando-se ao seu nó utilizando registos de diagnóstico de geth ou de visualização.

### <a name="using-geth"></a>Usando geth

Fixe-se ao seu nó de serviço Azure Blockchain utilizando o geth. Por exemplo, `geth attach https://myblockchainmember.blockchain.azure.com:3200/<Access key>`.

Uma vez ligado o seu nó, o geth reporta a versão Qurum semelhante à seguinte saída:

``` text
instance: Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12
```

Para obter mais informações sobre a utilização do geth, consulte [Quickstart: Use Geth para anexar um nó de transação do Serviço Azure Blockchain](connect-geth.md).

### <a name="using-diagnostic-logs"></a>Usando registos de diagnóstico

Se ativar registos de diagnóstico, a versão Quorum é reportada para nós de transação. Por exemplo, a seguinte mensagem de registo informativo do nó inclui a versão Qurum.

``` text 
{"NodeName":"transaction-node","Message":"INFO [06-22|05:31:45.156] Starting peer-to-peer node instance=Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12\n"}
{"NodeName":"transaction-node","Message":"[*] Starting Quorum node with QUORUM_VERSION=2.6.0, TESSERA_VERSION=0.10.5 and PRIVATE_CONFIG=/working-dir/c/tm.ipc\n"}
111
```

Para obter mais informações sobre registos de diagnóstico, consulte [o Serviço De Blockchain Monitor Azure através do Azure Monitor](monitor-azure-blockchain-service.md#diagnostic-settings).

## <a name="how-to-check-genesis-file-content"></a>Como verificar o conteúdo do ficheiro génese

Para verificar o conteúdo do ficheiro génesis do seu nó blockchain, pode utilizar a aPI Ethereum JavaScript:

``` bash
admin.nodeInfo.protocols
```
Pode ligar para a API usando uma consola geth ou uma biblioteca web3. Para obter mais informações sobre a utilização do geth, consulte [Quickstart: Use Geth para anexar um nó de transação do Serviço Azure Blockchain](connect-geth.md).

## <a name="next-steps"></a>Passos seguintes

[Limites no Serviço Azure Blockchain](limits.md)
