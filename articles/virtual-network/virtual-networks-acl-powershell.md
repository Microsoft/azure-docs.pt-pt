---
title: Gerir listas de controlo de acesso de ponto final do Azure | PowerShell | Clássico | Documentos da Microsoft
description: Saiba como gerir ACLs com o PowerShell
services: virtual-network
documentationcenter: na
author: genlin
ms.assetid: c84e40af-f351-4572-b3f0-d572d46bafe7
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: c43dacaf7bb5ab17fe740dd429e4a40dbc11bb6e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64726927"
---
# <a name="manage-endpoint-access-control-lists-using-powershell-in-the-classic-deployment-model"></a>Gerir listas de controlo de acesso de ponto final com o PowerShell no modelo de implementação clássica
Pode criar e gerir o controlo de acesso de rede lista (ACLs) para pontos finais com o Azure PowerShell ou no Portal de gestão. Neste tópico, encontrará procedimentos para tarefas comuns de ACL que pode realizar com o PowerShell. Para obter a lista do Azure PowerShell cmdlets ver [Cmdlets de gestão do Azure](https://go.microsoft.com/fwlink/?LinkId=317721). Para obter mais informações sobre as ACLs, consulte [o que é uma lista de controlo de acesso de rede (ACL)?](virtual-networks-acl.md). Se pretender gerir suas ACLs ao utilizar o Portal de gestão, consulte [como configurar pontos finais para uma Máquina Virtual](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="manage-network-acls-by-using-azure-powershell"></a>Gerir ACLs de rede com o Azure PowerShell
Pode utilizar cmdlets do Azure PowerShell para criar, remover e configurar (set) controlo de acesso de rede lista (ACLs). Incluímos alguns exemplos de algumas das formas que pode configurar uma ACL com o PowerShell.

Para obter uma lista completa dos cmdlets do PowerShell de ACL, pode usar qualquer um dos seguintes procedimentos:

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>Criar uma ACL de rede com regras que permitem o acesso a partir de uma sub-rede remota
O exemplo abaixo ilustra uma forma de criar uma nova ACL contém regras. Esta ACL, em seguida, é aplicada a um ponto de final de máquina virtual. As regras da ACL no exemplo a seguir permitirá o acesso a partir de uma sub-rede remota. Para criar uma nova ACL de rede com regras de autorização para uma sub-rede remota, abra um ISE do PowerShell do Azure. Copie e cole o script abaixo, configurar o script com seus próprios valores e, em seguida, execute o script.

1. Crie o novo objeto ACL de rede.
   
        $acl1 = New-AzureAclConfig
2. Defina uma regra que permita o acesso a partir de uma sub-rede remota. No exemplo abaixo, que defina a regra *100* (que tem prioridade sobre regra 200 e superior) para permitir que a sub-rede remota *10.0.0.0/8* acesso para o ponto final de máquina virtual. Substitua os valores pelos seus próprios requisitos de configuração. O nome "Configuração da ACL do SharePoint" deve ser substituído com o nome amigável que deseja chamar esta regra.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
3. Para regras adicionais, repita o cmdlet, substituindo os valores pelos seus próprios requisitos de configuração. Não se esqueça de alterar a regra número Order para refletir a ordem na qual pretende que as regras a serem aplicadas. O número mais baixo da regra tem precedência sobre o número mais alto.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
4. Em seguida, pode criar um novo ponto final (adicionar) ou definir a ACL para um ponto de extremidade existente (Set). Neste exemplo, iremos adicionar um novo ponto final máquina virtual denominado "web" e atualizar o ponto de final de máquina virtual com as definições de ACL.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM
5. Em seguida, combine os cmdlets e execute o script. Neste exemplo, os cmdlets combinados teria o seguinte aspeto:
   
        $acl1 = New-AzureAclConfig
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        |Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        |Update-AzureVM

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>Remover uma regra de ACL de rede que permita o acesso a partir de uma sub-rede remota
O exemplo abaixo ilustra uma forma remover uma regra ACL de rede.  Para remover uma regra de ACL de rede com regras de autorização para uma sub-rede remota, abra um ISE do PowerShell do Azure. Copie e cole o script abaixo, configurar o script com seus próprios valores e, em seguida, execute o script.

1. Primeira etapa é obter o objeto de ACL de rede para o ponto final de máquina virtual. Em seguida, irá remover a regra da ACL. Neste caso, estamos a removê-lo por ID de regra. Apenas este procedimento removerá o ID da regra 0 da ACL. Não remove o objeto ACL do ponto de extremidade de máquina virtual.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1
2. Em seguida, tem de aplicar o objeto de ACL de rede para o ponto final de máquina virtual e atualizar a máquina virtual.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>Remover uma ACL de rede de um ponto de final de máquina virtual
Em determinados cenários, pode querer remover um objeto de ACL de rede de um ponto de final de máquina virtual. Para tal, abra um ISE do PowerShell do Azure. Copie e cole o script abaixo, configurar o script com seus próprios valores e, em seguida, execute o script.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>Passos Seguintes
[O que é uma lista de controlo de acesso de rede (ACL)?](virtual-networks-acl.md)

