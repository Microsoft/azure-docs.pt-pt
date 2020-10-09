---
title: O que é o Gestor de Dados blockchain para o Serviço Azure Blockchain
description: O Gestor de Dados blockchain para capturar, transformar e entregar dados blockchain a Tópicos de Grelha de Eventos.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: chroyal
ms.openlocfilehash: 78c93880007259267b26cf53e93de722be1c7323
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77209448"
---
# <a name="what-is-blockchain-data-manager-for-azure-blockchain-service"></a>O que é o Blockchain Data Manager para o Azure Blockchain Service?

O Blockchain Data Manager captura, transforma e entrega dados de transações do Azure Blockchain Service a Tópicos de Grelha de Eventos Azure, proporcionando uma integração fiável e escalável de contabilidade blockchain com serviços Azure.

Na maioria dos cenários de blockchain da empresa, um livro-razão blockchain é uma parte de uma solução. Por exemplo, para transferir um ativo de uma entidade para outra, precisa de um mecanismo para submeter a transação. Em seguida, precisa de um mecanismo de leitura de dados de contabilidade para garantir que a transação ocorreu, foi aceite, e as alterações de estado resultantes são então integradas com a sua solução de ponta a ponta. Neste exemplo, se escrever um contrato inteligente para transferir ativos, pode utilizar o Blockchain Data Manager para integrar aplicações off-chain e lojas de dados. Para o exemplo de transferência de ativos, quando um ativo é transferido na blockchain, eventos e alterações do estado da propriedade são entregues pelo Blockchain Data Manager via Event Grid. Em seguida, pode utilizar vários possíveis manipuladores de eventos para a Grade de Eventos para armazenar dados de blockchain fora da cadeia ou reagir às mudanças de estado em tempo real.

O Gestor de Dados blockchain desempenha três funções principais: capturar, transformar e entregar.

![Funções de Gestor de Dados blockchain](./media/data-manager/functions.png)

## <a name="capture"></a>Recolha

Cada instância do Gestor de Dados blockchain liga-se a um nó de transação do membro do Serviço Azure Blockchain. Apenas os utilizadores com acesso ao nó de transação podem criar uma ligação que garanta o controlo adequado do acesso aos dados dos clientes. Uma instância do Gestor de Dados blockchain captura de forma fiável todos os dados de transação bruta e de bloco bruto do nó de transação e pode escalar para suportar cargas de trabalho da empresa.

## <a name="transform"></a>Transformação

Pode utilizar o Blockchain Data Manager para descodificar o estado do evento e da propriedade, configurando aplicações de contratos inteligentes dentro do Blockchain Data Manager. Para adicionar um contrato inteligente, você fornece o contrato ABI e bytecode. O Blockchain Data Manager utiliza os artefactos de contrato inteligente para descodificar e descobrir endereços contratuais. Depois de adicionar a aplicação blockchain ao caso, blockchain Data Manager descobre dinamicamente o endereço do contrato inteligente quando o contrato inteligente é implementado para o consórcio e envia evento e estado de propriedade descodificados para destinos configurados.

## <a name="deliver"></a>Fornecer

O Blockchain Data Manager suporta várias ligações de saída de Tópicos de Grelha de Evento para qualquer instância do Gestor de Dados blockchain. Pode enviar dados blockchain para um único destino ou enviar dados blockchain para vários destinos. Utilizando o Blockchain Data Manager, pode construir uma solução de publicação de dados baseada em eventos escalável para qualquer implementação de blockchain.

## <a name="configuration-options"></a>Opções de configuração

Pode configurar o Blockchain Data Manager para atender às necessidades da sua solução. Por exemplo, pode prever:

* Uma única instância do Gestor de Dados blockchain para um membro do Serviço Azure Blockchain.
* Um exemplo de Gestor de Dados blockchain por nó de transação do Serviço Azure Blockchain. Por exemplo, os nós de transações privadas podem ter a sua própria instância de Gestor de Dados blockchain para manter a confidencialidade.
* Uma instância do Gestor de Dados blockchain pode suportar várias ligações de saída. Uma instância do Gestor de Dados blockchain pode ser usada para gerir todos os pontos de integração da publicação de dados para um membro do Serviço Azure Blockchain.

## <a name="next-steps"></a>Passos seguintes

Tente [criar uma instância do Gestor de Dados blockchain](data-manager-portal.md) para um membro do Serviço Azure Blockchain.
