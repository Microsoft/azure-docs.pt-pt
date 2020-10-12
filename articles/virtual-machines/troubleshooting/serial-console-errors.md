---
title: Erros da Consola em Série Azure Microsoft Docs
description: Erros comuns dentro da Consola em Série Azure
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
ms.openlocfilehash: cad12a55332a6c7898f9709776c58d7dba8dd81a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86526440"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Erros comuns dentro da Consola em Série Azure
Existem um conjunto de erros conhecidos dentro da Consola Em Série Azure. Esta é uma lista desses erros e passos de atenuação para eles.

## <a name="common-errors"></a>Erros comuns

Erro                             |   Mitigação
:---------------------------------|:--------------------------------------------|
"A Azure Serial Console requer que os diagnósticos de arranque sejam ativados. Clique aqui para configurar diagnósticos de arranque para a sua máquina virtual." | Certifique-se de que o conjunto de balanças de vm ou de máquina virtual tem [diagnósticos de arranque](boot-diagnostics.md) ativados. Se estiver a utilizar uma consola em série numa instância de escala de máquina virtual, certifique-se de que o seu exemplo tem o modelo mais recente.
"A Azure Serial Console requer uma máquina virtual para estar em funcionamento. Utilize o botão Iniciar acima para ligar a sua máquina virtual."  | A instância de definição de escala de VM ou de máquina virtual deve estar num estado iniciado para aceder à consola em série (o seu VM não deve ser interrompido ou translocado). Certifique-se de que a sua instância de conjunto de balanças de vm ou de máquina virtual está a funcionar e tente novamente.
"A Azure Serial Console não está ativada para esta subscrição, contacte o administrador de subscrição para ativar." | A Consola em Série Azure pode ser desativada a nível de subscrição. Se for um administrador de subscrição, poderá [ativar e desativar a Consola em Série Azure](./serial-console-enable-disable.md). Se não for administrador de subscrição, deve contactar o administrador de subscrição para os próximos passos.
Foi encontrada uma resposta "Proibida" ao aceder à conta de armazenamento de diagnóstico de arranque deste VM. | Certifique-se de que os diagnósticos de arranque não têm uma firewall de conta. Uma conta de armazenamento de diagnóstico de arranque acessível é necessária para que a consola em série funcione. A consola em série por design não pode funcionar com firewalls de conta de armazenamento ativadas na conta de armazenamento de diagnóstico de arranque.
Não tem as permissões necessárias para utilizar este VM com a consola em série. Certifique-se de que tem pelo menos permissões de função de Contribuinte de Máquina Virtual.| O acesso à consola em série requer que tenha acesso ao nível do contribuinte ou acima no seu conjunto de escala de VM ou de máquina virtual. Para mais informações, consulte a [página geral.](serial-console-overview.md)
A conta de armazenamento '' utilizada para diagnósticos de arranque neste VM não foi encontrada. Verifique se o diagnóstico de arranque está ativado para este VM, esta conta de armazenamento não foi eliminada e tem acesso a esta conta de armazenamento. | Verifique duas vezes se não apagou a conta de armazenamento de diagnóstico de arranque para o seu conjunto de escala de VM ou máquina virtual
A ligação da consola em série ao VM encontrou um erro: 'Mau Pedido' (400) | Isto pode acontecer se o seu URI de diagnóstico de arranque estiver incorreto. Por exemplo, foi usado "http://" em vez de "https://". O boot diagnostics URI pode ser corrigido com este comando: `az vm boot-diagnostics enable --name vmName --resource-group rgName --storage https://<storageAccountUri>.blob.core.windows.net/`
Não tem as permissões necessárias para escrever na conta de armazenamento de diagnóstico de arranque para este VM. Certifique-se de que tem pelo menos permissões de colaboradores VM | O acesso à consola em série requer acesso ao nível do contribuinte na conta de armazenamento de diagnóstico de arranque. Para mais informações, consulte a [página geral.](serial-console-overview.md)
Não é possível determinar o grupo de recursos para a conta de armazenamento de diagnóstico de arranque * &lt; STORAGEACCOUNTNAME &gt; *. Verifique se os diagnósticos de arranque estão ativados para este VM e tem acesso a esta conta de armazenamento. | O acesso à consola em série requer acesso ao nível do contribuinte na conta de armazenamento de diagnóstico de arranque. Para mais informações, consulte a [página geral.](serial-console-overview.md)
O provisionamento para este VM ainda não foi bem sucedido. Certifique-se de que o VM está totalmente implantado e que relemque a ligação da consola em série. | O seu conjunto de balanças de VM ou máquina virtual ainda pode estar a ser a provisionamento. Espere um pouco e tente de novo.
A tomada web está fechada ou não pode ser aberta. | Pode ser necessário adicionar acesso a firewall a `*.console.azure.com` . Uma abordagem mais detalhada, mas mais longa, é permitir o acesso de firewall às gamas IP do [Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653), que mudam regularmente.
A consola em série não funciona com uma conta de armazenamento utilizando a Azure Data Lake Storage Gen2 com espaços hierárquicos. | Esta é uma questão conhecida com espaços hierárquicos. Para mitigar, certifique-se de que a conta de armazenamento de diagnóstico de arranque da sua VM não é criada usando a Azure Data Lake Storage Gen2. Esta opção só pode ser definida aquando da criação de conta de armazenamento. Poderá ter de criar uma conta de armazenamento de diagnóstico de arranque separada sem que o Azure Data Lake Storage Gen2 possa mitigar este problema.
A ligação da consola em série ao VM encontrou um erro: 'Forbidden'(SubscriptionNotEnabled) - Nome de subscrição indefinido, id \<subscription id> está em estado não ativado indefinido | Este problema pode ocorrer se a subscrição em que um utilizador criou a sua conta de armazenamento Cloud Shell tiver sido desativada. Para mitigar, lance a Cloud Shell e [execute os passos necessários](../../cloud-shell/persisting-shell-storage.md#unmount-clouddrive-1) para reprovisionar uma conta de armazenamento de suporte para a Cloud Shell na subscrição atual.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a [Consola Em Série Azure para Os VMs Linux](./serial-console-linux.md)
* Saiba mais sobre a [Consola Em Série Azure para VMs Windows](./serial-console-windows.md)
