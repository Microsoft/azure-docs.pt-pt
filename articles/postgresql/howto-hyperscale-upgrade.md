---
title: Grupo de servidor de upgrade - Hyperscale (Citus) - Base de Dados Azure para PostgreSQL
description: Este artigo descreve como pode atualizar PostgreSQL e Citus na Base de Dados Azure para PostgreSQL - Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 4/5/2021
ms.openlocfilehash: 161204bf02ac36c1f5a3969cf57c61e98560c9b5
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518903"
---
# <a name="upgrade-hyperscale-citus-server-group"></a>Atualizar o grupo de servidores Hyperscale (Citus)

Estas instruções descrevem como fazer o upgrade para uma nova versão principal do PostgreSQL em todos os nós do grupo do servidor.

## <a name="test-the-upgrade-first"></a>Teste primeiro a atualização

O upgrade de PostgreSQL causa mais alterações do que possa imaginar, porque a Hyperscale (Citus) também irá atualizar as extensões da [base de dados](concepts-hyperscale-extensions.md), incluindo a extensão citus.
Recomendamos vivamente que teste a sua aplicação com a nova versão PostgreSQL e Citus antes de atualizar o seu ambiente de produção.

Uma forma conveniente de testar é fazer uma cópia do seu grupo de servidor usando [a restauração pontual](concepts-hyperscale-backup.md#restore). Atualize a cópia e teste a sua aplicação contra ela. Assim que verificar que tudo funciona corretamente, atualize o grupo de servidor original.

## <a name="upgrade-a-server-group-in-the-azure-portal"></a>Atualize um grupo de servidores no portal Azure

1. Na secção **visão geral** de um grupo de servidores Hyperscale (Citus), selecione o botão **Deabos.**
1. Aparece um diálogo, mostrando a versão atual de PostgreSQL e Citus.
   Escolha uma nova versão PostgreSQL na **lista de upgrade.**
1. Verifique o valor na **versão Citus após a atualização** é o que espera.
   Este valor muda com base na versão PostgreSQL selecionada.
1. Selecione o botão **De atualização** para continuar.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [as versões PostgreSQL suportadas.](concepts-hyperscale-versions.md)
* Veja [quais extensões](concepts-hyperscale-extensions.md) são embaladas com cada versão PostgreSQL num grupo de servidores Hyperscale (Citus).
