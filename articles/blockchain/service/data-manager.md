---
title: O que é Blockchain Data Manager para o Serviço Azure Blockchain
description: Blockchain Data Manager para capturar, transformar e entregar dados blockchain para Tópicos de Grelha de Eventos.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: chroyal
ms.openlocfilehash: 78c93880007259267b26cf53e93de722be1c7323
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209448"
---
# <a name="what-is-blockchain-data-manager-for-azure-blockchain-service"></a>O que é o Blockchain Data Manager para o Azure Blockchain Service?

O Blockchain Data Manager captura, transforma e entrega dados de transações do Azure Blockchain Service aos Tópicos da Grelha de Eventos Do Evento Azure, proporcionando uma integração fiável e escalável do livro de blockchain com os serviços Azure.

Na maioria dos cenários de blockchain da empresa, um livro-razão blockchain é uma parte de uma solução. Por exemplo, para transferir um ativo de uma entidade para outra, precisa de um mecanismo para submeter a transação. Em seguida, é necessário um mecanismo de leitura dos dados do livro-razão para garantir que a transação ocorreu, foi aceite, e as alterações de estado resultantes são então integradas com a sua solução de ponta a ponta. Neste exemplo, se escrever um contrato inteligente para transferir ativos, pode utilizar o Blockchain Data Manager para integrar aplicações fora da cadeia e lojas de dados. Para o exemplo de transferência de ativos, quando um ativo é transferido na blockchain, eventos e alterações do estado de propriedade são entregues pelo Blockchain Data Manager via Event Grid. Em seguida, pode utilizar vários manipuladores de eventos possíveis para a Rede de Eventos para armazenar dados blockchain fora da cadeia ou reagir a alterações de estado em tempo real.

O Blockchain Data Manager desempenha três funções principais: capturar, transformar e entregar.

![Funções do Gestor de Dados blockchain](./media/data-manager/functions.png)

## <a name="capture"></a>Captura

Cada instância do Gestor de Dados blockchain liga-se a um nó de transação do Membro do Serviço Blockchain Azure. Apenas os utilizadores com acesso ao nó de transações podem criar uma ligação garantindo o controlo adequado do acesso aos dados dos clientes. Uma instância do Blockchain Data Manager captura de forma fiável todos os dados de bloqueio bruto e transação bruta do nó de transações e pode escalar para suportar as cargas de trabalho da empresa.

## <a name="transform"></a>Transformar

Pode utilizar o Blockchain Data Manager para descodificar o estado de eventos e propriedades configurando aplicações de contratos inteligentes dentro do Blockchain Data Manager. Para adicionar um contrato inteligente, fornece o contrato ABI e bytecode. O Blockchain Data Manager utiliza os artefactos de contrato inteligente para descodificar e descobrir endereços de contrato. Depois de adicionar a aplicação blockchain à instância, o Blockchain Data Manager descobre dinamicamente o endereço de contrato inteligente quando o contrato inteligente é implementado para o consórcio e envia eventos descodificados e estado de propriedade para destinos configurados.

## <a name="deliver"></a>Fornecer

O Blockchain Data Manager suporta várias ligações de saída do Tópico de Evento para qualquer instância do Gestor de Dados blockchain. Pode enviar dados blockchain para um único destino ou enviar dados blockchain para vários destinos. Utilizando o Blockchain Data Manager, pode construir uma solução de publicação de dados escalável para qualquer implementação de blockchain.

## <a name="configuration-options"></a>Opções de configuração

Pode configurar o Gestor de Dados blockchain para atender às necessidades da sua solução. Por exemplo, pode fornecer:

* Um único Blockchain Data Manager, por exemplo, para um membro do Serviço Azure Blockchain.
* Uma instância de Gestor de Dados blockchain por nó de transação do Serviço Azure Blockchain. Por exemplo, os nós de transação privada podem ter a sua própria instância de Blockchain Data Manager para manter a confidencialidade.
* Uma instância do Blockchain Data Manager pode suportar várias ligações de saída. Uma instância do Blockchain Data Manager pode ser usada para gerir todos os pontos de integração de publicação de dados para um membro do Serviço Azure Blockchain.

## <a name="next-steps"></a>Passos seguintes

Tente [criar um Blockchain Data Manager para](data-manager-portal.md) um membro do Serviço Azure Blockchain.
