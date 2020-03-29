---
title: Erros da Consola Em Série Azure / Microsoft Docs
description: Erros comuns dentro da Consola Em Série Azure
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
ms.openlocfilehash: 61ae0ef92fe522a2a038a6076a5e0c0a10ee47b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060694"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Erros comuns dentro da Consola Em Série Azure
Há um conjunto de erros conhecidos dentro da Consola Em Série Azure. Esta é uma lista desses erros e passos de mitigação para eles.

## <a name="common-errors"></a>Erros comuns

Erro                             |   Mitigação
:---------------------------------|:--------------------------------------------|
"A Consola Em Série Azure requer que os diagnósticos de arranque sejam ativados. Clique aqui para configurar diagnósticos de arranque para a sua máquina virtual." | Certifique-se de que o conjunto de escala de vm ou de máquina virtual tem [diagnósticos](boot-diagnostics.md) de arranque ativados. Se estiver a utilizar uma consola em série numa instância de conjunto de escala de máquina virtual, certifique-se de que a sua instância tem o modelo mais recente.
"A Consola Em Série Azure requer uma máquina virtual a funcionar. Use o botão Iniciar acima para ligar a sua máquina virtual."  | A instância de conjunto de métricas vm ou virtual deve estar em estado de partida para aceder à consola em série (o seu VM não deve ser parado ou ser transferido). Certifique-se de que a sua configuração de conjunto de métricas vm ou virtual está a funcionar e tente novamente.
"A Consola Em Série Azure não está ativada para esta subscrição, contacte o seu administrador de subscrição para ativar." | A Consola Em Série Azure pode ser desativada a nível de subscrição. Se for um administrador de subscrição, poderá [ativar e desativar a Consola Em Série Azure](./serial-console-enable-disable.md). Se não for administrador de subscrição, deverá contactar o seu administrador de subscrição para os próximos passos.
Uma resposta "Proibida" foi encontrada ao aceder à conta de armazenamento de diagnóstico de botas deste VM. | Certifique-se de que os diagnósticos de arranque não têm uma firewall de conta. É necessária uma conta de armazenamento de diagnóstico de arranque acessível para que a consola em série funcione. A consola em série por design não pode funcionar com firewalls de conta de armazenamento ativadas na conta de armazenamento de diagnóstico de arranque.
Não tem as permissões necessárias para utilizar este VM com a consola em série. Certifique-se de que tem pelo menos permissões de contribuidores de máquinas virtuais.| O acesso à consola em série requer que tenha acesso ao nível do contribuinte ou superior no seu conjunto de escala de vm ou máquina virtual. Para mais informações, consulte a [página de visão geral](serial-console-overview.md).
A conta de armazenamento '' utilizada para diagnósticos de arranque neste VM não foi encontrada. Verifique se o diagnóstico de arranque está ativado para este VM, esta conta de armazenamento não foi eliminada e tem acesso a esta conta de armazenamento. | Verifique duas vezes se não apagou a conta de armazenamento de diagnóstico de arranque para o seu conjunto de escala de VM ou máquina virtual
A ligação da consola em série ao VM encontrou um erro: 'Bad Request' (400) | Isto pode acontecer se o diagnóstico de arranque URI estiver incorreto. Por exemplo, "http://" foi usado em vez de "https://". Os diagnósticos de arranque URI podem ser corrigidos com este comando:`az vm boot-diagnostics enable --name vmName --resource-group rgName --storage https://<storageAccountUri>.blob.core.windows.net/`
Não tem as permissões necessárias para escrever na conta de armazenamento de diagnóstico sinuoso deste VM. Certifique-se de que tem pelo menos permissões vm colaborador | O acesso à consola em série requer acesso ao nível dos contribuintes na conta de armazenamento de diagnósticos de arranque. Para mais informações, consulte a [página de visão geral](serial-console-overview.md).
Incapaz de determinar o grupo de recursos * &lt;&gt;* para a conta de armazenamento de diagnóstico de arranque STORAGEACCOUNTNAME . Verifique se o diagnóstico de arranque está ativado para este VM e tem acesso a esta conta de armazenamento. | O acesso à consola em série requer acesso ao nível dos contribuintes na conta de armazenamento de diagnósticos de arranque. Para mais informações, consulte a [página de visão geral](serial-console-overview.md).
O fornecimento para este VM ainda não foi bem sucedido. Certifique-se de que o VM está totalmente implantado e que tente novamente a ligação da consola em série. | O seu conjunto de escala de máquinas VM ou virtual ainda pode estar a provisionar. Espere um pouco e tente de novo.
A tomada da web está fechada ou não pode ser aberta. | Pode ser necessário adicionar `*.console.azure.com`acesso à firewall a . Uma abordagem mais detalhada, mas mais longa, é permitir o acesso à firewall às gamas IP do [Microsoft Azure Datacenter,](https://www.microsoft.com/download/details.aspx?id=41653)que mudam regularmente.
A consola em série não funciona com uma conta de armazenamento utilizando o Azure Data Lake Storage Gen2 com espaços hierárquicos. | Esta é uma questão conhecida com espaços hierárquicos. Para mitigar, certifique-se de que a conta de armazenamento de diagnóstico de arranque do seu VM não é criada utilizando o Azure Data Lake Storage Gen2. Esta opção só pode ser definida na criação de conta de armazenamento. Poderá ter de criar uma conta separada de armazenamento de diagnóstico de boot sem o Azure Data Lake Storage Gen2 habilitado a mitigar este problema.
A ligação da consola em série ao VM encontrou um erro: 'Forbidden'(SubscriçãoNotEnabled) - Nome de subscrição indefinido, id de subscrição id \<> está em estado não ativado indefinido | Este problema pode ocorrer se a subscrição em que um utilizador criou a sua conta de armazenamento Cloud Shell tiver sido desativada. Para mitigar, lance a Cloud Shell e [execute os passos necessários](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#unmount-clouddrive-1) para refornecer uma conta de armazenamento de apoio para a Cloud Shell na subscrição atual.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a [Consola Em Série Azure para VMs Linux](./serial-console-linux.md)
* Saiba mais sobre a [consola em série Azure para VMs do Windows](./serial-console-windows.md)