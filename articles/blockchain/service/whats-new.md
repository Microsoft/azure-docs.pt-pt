---
title: Novidades? Notas de lançamento - Serviço Azure Blockchain
description: Saiba quais as novidades com o Azure Blockchain Service, como as últimas notas de lançamento, versões, problemas conhecidos e mudanças futuras.
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: 6a3113a2d28e704b188d701da13493ecd8263cab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94335031"
---
# <a name="whats-new-in-azure-blockchain-service"></a>Quais as novidades no Serviço Azure Blockchain?

> Seja notificado sobre quando revisitar esta página para obter atualizações copiando e colando este URL: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Blockchain+Service%22&locale=en-us` no ícone do leitor de feed [ ![ RSS RSS](./media/whats-new/feed-icon-16x16.png)](/api/search/rss?locale=en-us&search=%2522Release%2bnotes%2b-%2bAzure%2bBlockchain%2bService%2522).

O Azure Blockchain Service recebe melhorias de forma contínua. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece-lhe informações sobre:

- Novas funcionalidades
- Atualizações de versão
- Problemas conhecidos

---

## <a name="june-2020"></a>Junho de 2020

### <a name="version-upgrades"></a>Atualizações de versão

- Atualização da versão Quorum para 2.6.0. Com a versão 2.6.0, pode enviar transações privadas assinadas. Para obter mais informações sobre o envio de transações privadas, consulte a documentação da API do [Qurum.](https://docs.goquorum.consensys.net/en/latest/Reference/APIs/ContractExtensionAPIs/#apis)
- A versão Tessera atualização para 0.10.5.

### <a name="contract-size-and-transaction-size-increased-to-128-kb"></a>Dimensão do contrato e dimensão das transações aumentou para 128 KB

Tipo: Alteração de configuração

O tamanho do contrato (MaxCodeSize) foi aumentado para 128 KB para que possa implementar contratos inteligentes de maior dimensão. Além disso, o tamanho da transação (txnSizeLimit) foi aumentado para 128 KB. As alterações de configuração aplicam-se aos novos consórcios criados no Azure Blockchain Service após 19 de junho de 2020.

### <a name="trietimeout-value-reduced"></a>Valor TrieTimeout reduzido

Tipo: Alteração de configuração

O valor TrieTimeout foi reduzido de modo a que o estado de memória seja escrito para o disco com mais frequência. O valor mais baixo garante uma recuperação mais rápida de um nó no caso raro de uma queda do nó.

## <a name="may-2020"></a>Maio de 2020

### <a name="version-upgrades"></a>Atualizações de versão

- Upgrade da versão Ubuntu para 18.04
- Upgrade da versão quorum para 2.5.0
- Atualização da versão Tessera 0.10.4

### <a name="azure-blockchain-service-supports-sending-rawprivate-transactions"></a>Azure Blockchain Service suporta envio de transações rawPrivate

Tipo: Recurso

Os clientes podem assinar transações privadas fora da conta no nó.

### <a name="two-phase-member-provisioning"></a>Provisão de membros em duas fases

Tipo: Melhoramento

Duas fases ajudam a otimizar cenários onde um membro está a ser criado num consórcio há muito existente. A infraestrutura membro é a provisionada na primeira fase. Na segunda fase, o membro é sincronizado com blockchain. O provisionamento em duas fases ajuda a evitar a falha de criação dos membros devido a intervalos de tempo.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="ethestimategas-function-throws-exception-in-quorum-v260"></a>eth.estima A funçãogas lança exceção no Quorum v2.6.0

No Quorum v2.6.0, as chamadas para a função *eth.estimateGas* sem fornecer o parâmetro de *valor* adicional causam uma exceção *do manipulador de métodos.* A equipa do Quorum foi notificada e espera-se uma correção no final de julho de 2020. Pode utilizar as seguintes soluções até que uma correção esteja disponível:

- Evite utilizar *eth.estimaGas* uma vez que pode afetar o desempenho. Para obter mais informações sobre problemas de desempenho eth.estimateGas, consulte [a função Call eth.estimateGas reduz o desempenho](#calling-ethestimategas-function-reduces-performance). Inclua um valor de gás para cada transação. A maioria das bibliotecas chamará eth.estimaGas se não for fornecido um valor de gás que faça com que o Qurum v2.6.0 se despenhe.
- Se precisar de ligar para *eth.estimateGas,* a equipa do Quorum sugere que passe o *valor* do parâmetro adicional como *0* como uma solução alternativa.

### <a name="mining-stops-if-fewer-than-four-validator-nodes"></a>A mineração para se menos de quatro nódoas validantes

As redes de produção devem ter pelo menos quatro nós validadores. O Qurum recomenda que pelo menos quatro nómadas validadores sejam necessários para cumprir a tolerância à falha de colisão da IBFT (3F+1). Deve ter pelo menos dois nós de nível *standard* do Serviço Azure Blockchain para obter quatro nós validadores. Um nó padrão é provisionado com dois nós validadores.  

Se a rede Blockchain no Serviço Azure Blockchain não tiver quatro nós validadores, então a mineração pode parar na rede. Pode detetar que a mineração parou colocando um alerta em blocos processados. Numa rede saudável, o bloco processado será de 60 blocos por nó por cada cinco minutos.

Como mitigação, a equipa do Serviço Azure Blockchain tem de reiniciar o nó. Os clientes precisam de abrir um pedido de apoio para reiniciar o nó. A equipa do Serviço Azure Blockchain está a trabalhar para detetar e corrigir automaticamente problemas de mineração.

Utilize o nível *Standard* para implantações de nível de produção. Utilize o nível *básico* para desenvolvimento, teste e prova de conceitos. A alteração do nível de preços entre o básico e o padrão após a criação dos membros não é apoiada.

### <a name="blockchain-data-manager-requires-standard-tier-node"></a>O Gestor de Dados blockchain requer um nó de nível padrão

Utilize o nível *Standard* se estiver a utilizar o Blockchain Data Manager. O nível *básico* tem apenas memória de 4 GB. Assim, não é capaz de escalar para o uso exigido pelo Blockchain Data Manager e outros serviços que o executam.

Utilize o nível *básico* para desenvolvimento, teste e prova de conceitos. A alteração do nível de preços entre o básico e o padrão após a criação dos membros não é apoiada.

### <a name="large-volume-of-unlock-account-calls-causes-geth-to-crash"></a>Grande volume de chamadas de conta de desbloqueio faz com que o geth caia

Um grande volume de chamadas de conta de desbloqueio durante a submissão de transações pode fazer com que o geth caia num nó de transação. Como resultado, tem que parar de ingerir transações. Caso contrário, a fila de transações pendente aumenta.

Geth reinicia automaticamente em menos de um minuto. Dependendo do nó, a sincronização pode demorar muito tempo. A equipa do Serviço Azure Blockchain está a permitir um recurso de arquivamento que reduzirá o tempo para sincronizar.

Para identificar falhas de geth, pode verificar registos de qualquer mensagem de erro nas mensagens Blockchain nos registos de aplicações. Também pode verificar se os blocos processados diminuem enquanto as transações pendentes aumentam.

Para mitigar o problema, envie transações assinadas em vez de enviar transações não assinadas com o comando de desbloquear a conta. Para as transações que já estão assinadas externamente, não há necessidade de desbloquear a conta.

Se pretender enviar transações não assinadas, desbloqueie a conta por tempo infinito enviando 0 como parâmetro de tempo no comando de desbloqueio. Pode bloquear a conta depois de todas as transações serem submetidas.  

Seguem-se os parâmetros de azure blockchain que o Serviço Azure Blockchain utiliza. Não é possível ajustar estes parâmetros.

- Período do bloco de Istambul: 5 segundos
- Limite de gás no chão: 700000000
- Limite de gás ceil: 8000000000

### <a name="large-volume-of-private-transactions-reduces-performance"></a>Grande volume de transações privadas reduz desempenho

Se estiver a utilizar o serviço Azure Blockchain Basic tier e transações privadas, a Tessera pode falhar.

Pode detetar a falha tessera, revendo os registos de aplicações blockchain e procurando a mensagem `Tessera crashed. Restarting Tessera...` .

O Serviço Azure Blockchain reinicia a Tessera quando há uma falha. O recomeço demora cerca de um minuto.

Utilize o nível *Standard* se estiver a enviar um grande volume de transações privadas. Utilize o nível *básico* para desenvolvimento, teste e prova de conceitos. A alteração do nível de preços entre o básico e o padrão após a criação dos membros não é apoiada.

### <a name="calling-ethestimategas-function-reduces-performance"></a>Chamando eth.estimaGas função reduz desempenho

Chamar *eth.estimaGas* a funçãogas várias vezes reduz drasticamente as transações por segundo. Não utilize a função *eth.estimateGas* para cada submissão de transação. A *função eth.estimagas* é intensiva em memória.

Se possível, utilize um valor de gás conservador para submeter transações e minimize a utilização de *eth.estimateGas*.

### <a name="unbounded-loops-in-smart-contracts-reduces-performance"></a>Loops ilimitados em contratos inteligentes reduz o desempenho

Evite laços ilimitados em contratos inteligentes, uma vez que podem reduzir o desempenho. Para obter mais informações, veja os seguintes recursos:

- [Evite laços sem limites](https://blog.b9lab.com/getting-loopy-with-solidity-1d51794622ad )
- [Boas práticas de segurança de contratos inteligentes](https://github.com/ConsenSys/smart-contract-best-practices)
- [Orientações inteligentes do contrato fornecidas pelo Quorum](https://docs.goquorum.consensys.net/en/stable/Concepts/Security/Framework/DecentralizedApplication/SmartContractsSecurity/)
- [Orientações sobre limites de gás e lacetes fornecidos pela Solidity](https://solidity.readthedocs.io/en/develop/security-considerations.html#gas-limit-and-loops)