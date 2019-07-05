---
title: Como encerrar uma unidade de ficheiros do Microsoft Azure FXT Edge
description: Procedimentos de arranque e encerramento seguro de um nó de filtro de borda de FXT do Azure
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: 8d779547040da9855409d3408827af2e4acd17a7
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542868"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Como desligar com segurança o hardware de ficheiros do Azure FXT Edge

Apesar de poder utilizar o botão de energia físico mudar de um nó individual, não deve utilizá-lo para desligar a unidade em circunstâncias normais.

Depois de um nó de ficheiros do Azure FXT Edge está a ser utilizado como parte de um cluster, deve usar o software de painel de controlo do cluster para encerrar o hardware. 

> [!NOTE] 
> Para evitar a perda de dados ou corrupção, utilize sempre o software de painel de controlo para encerrar um filtro de borda de FXT do Azure. Não utilize o botão de energia de físico para encerramento, a menos que é instruído para fazer isso pelo suporte ao cliente da Microsoft.
> 
> Numa emergência elétrica, desligar os cabos de energia ou utilize os seus dados eletricidade do center desligar o mecanismo.

## <a name="shut-down-a-node-from-the-control-panel"></a>Desligar um nó a partir do painel de controle

Siga estas instruções para com segurança desligar um nó de ficheiros do Azure FXT Edge:

1. Inicie sessão para o painel de controlo do cluster. (Indicações [abra as páginas de definições](fxt-cluster-create.md#open-the-settings-pages))
1. Clique nas **configurações** separador, em seguida, carregue o **Cluster** > **FXT nós** página.
1. Na lista de nós de cluster, descobrir a que pretende desligar. Clique nas **desligue** botão no seu **ações** coluna. 
1. Aguarde alguns momentos. O nó irá encerrar e desligar em si.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre os LEDs de estado e outros indicadores [estado do hardware de filtro de borda do Monitor do Azure FXT](fxt-monitor.md).
* Saiba mais sobre a energia de ficheiros do Azure FXT Edge fornecidas pelo usuário em [ligue os cabos de power](fxt-network-power.md#connect-power-cables).
