---
title: O que é o Blockchain Gerenciador de Dados para o serviço Blockchain do Azure
description: Blockchain Gerenciador de Dados captura, transforma e entrega dados de Blockchain para tópicos de grade de eventos.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: overview
ms.service: azure-blockchain
ms.reviewer: chroyal
ms.openlocfilehash: 94534d9b98f6c93d1d439775d3269df71f587ca4
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577492"
---
# <a name="what-is-blockchain-data-manager-for-azure-blockchain-service"></a>O que é o Blockchain Gerenciador de Dados para o serviço Blockchain do Azure?

O Blockchain Gerenciador de Dados captura, transforma e entrega dados de transação do serviço Blockchain do Azure para tópicos da grade de eventos do Azure, fornecendo integração de contabilidade de Blockchain escalonável e confiável com os serviços do Azure.

Na maioria dos cenários de blockchain empresarial, uma contabilidade blockchain é uma parte de uma solução. Por exemplo, para transferir um ativo de uma entidade para outra, você precisa de um mecanismo para enviar a transação. Em seguida, você precisa de um mecanismo para ler os dados do razão para garantir que a transação ocorreu, foi aceita e as alterações de estado resultantes são então integradas à sua solução de ponta a ponta. Neste exemplo, se você escrever um contrato inteligente para transferir ativos, poderá usar o Blockchain Gerenciador de Dados para integrar aplicativos e armazenamentos de dados fora da cadeia. Para o exemplo de transferência de ativos, quando um ativo é transferido no blockchain, os eventos e as alterações de estado de propriedade são entregues pelo Blockchain Gerenciador de Dados por meio da grade de eventos. Você pode usar vários manipuladores de eventos possíveis para a grade de eventos armazenar dados blockchain fora da cadeia ou reagir a alterações de estado em tempo real.

Blockchain Gerenciador de Dados executa três funções principais: capturar, transformar e entregar.

![Funções de Gerenciador de Dados Blockchain](./media/data-manager/functions.png)

## <a name="capture"></a>Captura

Cada instância de Gerenciador de Dados do Blockchain se conecta a um nó de transação de membro do serviço Blockchain do Azure. Somente os usuários com acesso ao nó de transação podem criar uma conexão garantindo o controle de acesso apropriado aos dados do cliente. Uma instância de Gerenciador de Dados do Blockchain captura de forma confiável todos os dados brutos de bloco e transação bruto do nó de transação e pode ser dimensionada para dar suporte a cargas de trabalho corporativas.

## <a name="transform"></a>Transformação

Você pode usar Blockchain Gerenciador de Dados para decodificar o evento e o estado da propriedade Configurando aplicativos de contrato inteligente no Blockchain Gerenciador de Dados. Para adicionar um contrato inteligente, você fornece a ABI e o código de bytes do contrato. Blockchain Gerenciador de Dados usa os artefatos do contrato inteligente para decodificar e descobrir endereços de contrato. Depois de adicionar o aplicativo blockchain à instância, o Blockchain Gerenciador de Dados descobre dinamicamente o endereço do contrato inteligente quando o contrato inteligente é implantado no consórcio e envia o evento decodificado e o estado da propriedade para os destinos configurados.

## <a name="deliver"></a>Fornecer

Blockchain Gerenciador de Dados dá suporte a várias conexões de saída de tópico de grade de eventos para qualquer instância de Gerenciador de Dados de Blockchain especificada. Você pode enviar dados do blockchain para um único destino ou enviar dados do blockchain para vários destinos. Usando o Blockchain Gerenciador de Dados, você pode criar uma solução escalonável de publicação de dados baseada em eventos para qualquer implantação do Blockchain.

## <a name="configuration-options"></a>Opções de configuração

Você pode configurar o Blockchain Gerenciador de Dados para atender às necessidades da sua solução. Por exemplo, você pode provisionar:

* Uma única instância de Gerenciador de Dados do Blockchain para um membro do serviço Blockchain do Azure.
* Uma instância de Gerenciador de Dados do Blockchain por nó de transação do serviço Blockchain do Azure. Por exemplo, os nós de transação privada podem ter sua própria instância de Gerenciador de Dados Blockchain para manter a confidencialidade.
* Uma instância de Gerenciador de Dados do Blockchain pode dar suporte A várias conexões de saída. Uma instância de Gerenciador de Dados do Blockchain pode ser usada para gerenciar todos os pontos de integração de publicação de dados para um membro do serviço Blockchain do Azure.

## <a name="next-steps"></a>Passos seguintes

Tente [criar uma instância de Gerenciador de dados do Blockchain](data-manager-portal.md) para um membro do serviço Blockchain do Azure.
