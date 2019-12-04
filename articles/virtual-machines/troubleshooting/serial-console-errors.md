---
title: Erros do console serial do Azure | Microsoft Docs
description: Erros comuns no console serial do Azure
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: 6f35bd5f28ae1512726c242cf004e1b97f81b1f1
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74776644"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Erros comuns no console serial do Azure
Há um conjunto de erros conhecidos no console serial do Azure. Esta é uma lista desses erros e etapas de mitigação para eles.

## <a name="common-errors"></a>Erros comuns

Erro                             |   Mitigação
:---------------------------------|:--------------------------------------------|
"O console serial do Azure requer que o diagnóstico de inicialização esteja habilitado. Clique aqui para configurar o diagnóstico de inicialização para sua máquina virtual. " ![Erro de diagnóstico de inicialização](./media/virtual-machines-serial-console/virtual-machines-serial-console-boot-diagnostics-error.png) | Verifique se a VM ou o conjunto de dimensionamento de máquinas virtuais tem o [diagnóstico de inicialização](boot-diagnostics.md) habilitado. Se você estiver usando o console serial em uma instância de conjunto de dimensionamento de máquinas virtuais, verifique se sua instância tem o modelo mais recente.
"O console serial do Azure requer que uma máquina virtual esteja em execução. Use o botão iniciar acima para iniciar sua máquina virtual. " ![Erro desalocado](./media/virtual-machines-serial-console/virtual-machines-serial-console-deallocating-error.png) | A instância do conjunto de dimensionamento de máquinas virtuais ou VM deve estar em um estado iniciado para acessar o console serial (sua VM não deve ser interrompida ou desalocada). Verifique se a instância da VM ou do conjunto de dimensionamento de máquinas virtuais está em execução e tente novamente.
"O console serial do Azure não está habilitado para esta assinatura, contate o administrador da assinatura para habilitar". ![Erro de assinatura desabilitada](./media/virtual-machines-serial-console/virtual-machines-serial-console-subscription-disabled-error.png) | O console serial do Azure pode ser desabilitado em um nível de assinatura. Se você for um administrador de assinatura, poderá [habilitar e desabilitar o console serial do Azure](./serial-console-enable-disable.md). Se você não for um administrador de assinatura, deverá entrar em contato com o administrador da assinatura para as próximas etapas.
Uma resposta "proibida" foi encontrada ao acessar a conta de armazenamento de diagnóstico de inicialização da VM. ![Erro de firewall da conta de armazenamento](./media/virtual-machines-serial-console/virtual-machines-serial-console-firewall-error.png)| Verifique se o diagnóstico de inicialização não tem um firewall de conta. Uma conta de armazenamento de diagnóstico de inicialização acessível é necessária para que o console serial funcione. Console serial por design não pode funcionar com firewalls de conta de armazenamento habilitados na conta de armazenamento de diagnóstico de inicialização.
Você não tem as permissões necessárias para usar essa VM com o console serial. Verifique se você tem pelo menos permissões de função de colaborador de máquina virtual.| O acesso ao console serial exige que você tenha acesso ao nível de colaborador ou acima em sua VM ou conjunto de dimensionamento de máquinas virtuais. Para obter mais informações, consulte a [página Visão geral](serial-console-overview.md).
Não foi possível encontrar a conta de armazenamento ' ' usada para o diagnóstico de inicialização nesta VM. Verifique se o diagnóstico de inicialização está habilitado para esta VM, se essa conta de armazenamento não foi excluída e se você tem acesso a essa conta de armazenamento. | Verifique se você não excluiu a conta de armazenamento de diagnóstico de inicialização para sua VM ou conjunto de dimensionamento de máquinas virtuais
O provisionamento para esta VM ainda não foi bem-sucedido. Verifique se a VM está totalmente implantada e repita a conexão do console serial. | Sua VM ou conjunto de dimensionamento de máquinas virtuais ainda pode estar Provisionando. Aguarde algum tempo e tente novamente.
Você não tem as permissões necessárias para gravar na conta de armazenamento de diagnóstico de inicialização para essa VM. Verifique se você tem pelo menos permissões de colaborador de VM em ' '. | Console serial acesso requer acesso no nível de colaborador na conta de armazenamento do diagnóstico de inicialização. Para obter mais informações, consulte a [página Visão geral](serial-console-overview.md).
Não é possível determinar o grupo de recursos para a conta de armazenamento de diagnóstico de inicialização *&lt;STORAGEACCOUNTNAME&gt;* . Verifique se o diagnóstico de inicialização está habilitado para essa VM e se você tem acesso a essa conta de armazenamento. | Console serial acesso requer acesso no nível de colaborador na conta de armazenamento do diagnóstico de inicialização. Para obter mais informações, consulte a [página Visão geral](serial-console-overview.md).
A conexão do console serial com a VM encontrou um erro: ' solicitação inadequada ' (400) | Isso pode acontecer se o URI do diagnóstico de inicialização estiver incorreto. Por exemplo, "http://" foi usado em vez de "https://". O URI de diagnóstico de inicialização pode ser corrigido com este comando: `az vm boot-diagnostics enable --name vmName --resource-group rgName --storage https://<storageAccountUri>.blob.core.windows.net/`
O soquete da Web está fechado ou não pôde ser aberto. | Talvez seja necessário adicionar acesso de firewall ao `*.console.azure.com`. Uma abordagem mais detalhada, mas mais longa, é permitir o acesso de firewall aos [intervalos de IP do datacenter Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), que são alterados com bastante frequência.
Console serial não funciona com uma conta de armazenamento usando Azure Data Lake Storage Gen2 com namespaces hierárquicos. | Esse é um problema conhecido com namespaces hierárquicos. Para atenuar, verifique se a conta de armazenamento do diagnóstico de inicialização da VM não foi criada usando Azure Data Lake Storage Gen2. Essa opção só pode ser definida na criação da conta de armazenamento. Talvez seja necessário criar uma conta de armazenamento de diagnóstico de inicialização separada sem Azure Data Lake Storage Gen2 habilitado para atenuar esse problema.


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre o [console serial do Azure para VMs Linux](./serial-console-linux.md)
* Saiba mais sobre o [console serial do Azure para VMs do Windows](./serial-console-windows.md)