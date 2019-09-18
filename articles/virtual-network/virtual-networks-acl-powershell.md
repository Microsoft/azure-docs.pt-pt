---
title: Gerenciar listas de controle de acesso de ponto de extremidade do Azure | PowerShell | Clássico | Microsoft Docs
description: Saiba como gerenciar ACLs com o PowerShell
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.assetid: c84e40af-f351-4572-b3f0-d572d46bafe7
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 4fe8349863c16a15886f9f1d33056f0bbfec31f2
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056755"
---
# <a name="manage-endpoint-access-control-lists-using-powershell-in-the-classic-deployment-model"></a>Gerenciar listas de controle de acesso de ponto de extremidade usando o PowerShell no modelo de implantação clássico
Você pode criar e gerenciar ACLs (listas de controle de acesso) de rede para pontos de extremidade usando Azure PowerShell ou no Portal de Gerenciamento. Neste tópico, você encontrará procedimentos para tarefas comuns de ACL que você pode concluir usando o PowerShell. Para obter a lista de cmdlets Azure PowerShell consulte [cmdlets de gerenciamento do Azure](https://go.microsoft.com/fwlink/?LinkId=317721). Para obter mais informações sobre ACLs, consulte [o que é uma ACL (lista de controle de acesso) de rede?](virtual-networks-acl.md). Se você quiser gerenciar suas ACLs usando o Portal de Gerenciamento, consulte [como configurar pontos de extremidade para uma máquina virtual](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="manage-network-acls-by-using-azure-powershell"></a>Gerenciar ACLs de rede usando Azure PowerShell
Você pode usar Azure PowerShell cmdlets para criar, remover e configurar (definir) listas de controle de acesso (ACLs) de rede. Incluímos alguns exemplos de algumas das maneiras que você pode configurar uma ACL usando o PowerShell.

Para recuperar uma lista completa dos cmdlets do PowerShell ACL, você pode usar um dos seguintes:

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>Criar uma ACL de rede com regras que permitem o acesso de uma sub-rede remota
O exemplo a seguir ilustra uma maneira de criar uma nova ACL que contém regras. Essa ACL é então aplicada a um ponto de extremidade de máquina virtual. As regras de ACL no exemplo abaixo permitirão o acesso de uma sub-rede remota. Para criar uma nova ACL de rede com regras de permissão para uma sub-rede remota, abra um Azure PowerShell ISE. Copie e cole o script abaixo, configurando o script com seus próprios valores e, em seguida, execute o script.

1. Crie o novo objeto de ACL de rede.
   
        $acl1 = New-AzureAclConfig
2. Defina uma regra que permita o acesso de uma sub-rede remota. No exemplo a seguir, você define a regra *100* (que tem prioridade sobre a regra 200 e superior) para permitir que a sub-rede remota *10.0.0.0/8* acesse o ponto de extremidade da máquina virtual. Substitua os valores pelos seus próprios requisitos de configuração. O nome "configuração de ACL do SharePoint" deve ser substituído pelo nome amigável para o qual você deseja chamar essa regra.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"
3. Para regras adicionais, repita o cmdlet, substituindo os valores pelos seus próprios requisitos de configuração. Certifique-se de alterar a ordem do número da regra para refletir a ordem na qual você deseja que as regras sejam aplicadas. O número de regra inferior tem precedência sobre o número mais alto.
   
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
4. Em seguida, você pode criar um novo ponto de extremidade (Adicionar) ou definir a ACL para um ponto de extremidade existente (conjunto). Neste exemplo, adicionaremos um novo ponto de extremidade de máquina virtual chamado "Web" e atualizaremos o ponto de extremidade da máquina virtual com as configurações de ACL.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM
5. Em seguida, combine os cmdlets e execute o script. Para este exemplo, os cmdlets combinados teriam a seguinte aparência:
   
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

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>Remover uma regra de ACL de rede que permite o acesso de uma sub-rede remota
O exemplo a seguir ilustra uma maneira de remover uma regra de ACL de rede.  Para remover uma regra de ACL de rede com regras de permissão para uma sub-rede remota, abra um Azure PowerShell ISE. Copie e cole o script abaixo, configurando o script com seus próprios valores e, em seguida, execute o script.

1. A primeira etapa é obter o objeto de ACL de rede para o ponto de extremidade da máquina virtual. Em seguida, você removerá a regra de ACL. Nesse caso, estamos removendo-o por ID de regra. Isso removerá apenas a ID de regra 0 da ACL. Ele não remove o objeto ACL do ponto de extremidade da máquina virtual.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1
2. Em seguida, você deve aplicar o objeto de ACL de rede ao ponto de extremidade da máquina virtual e atualizar a máquina virtual.
   
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>Remover uma ACL de rede de um ponto de extremidade de máquina virtual
Em determinados cenários, talvez você queira remover um objeto de ACL de rede de um ponto de extremidade de máquina virtual. Para fazer isso, abra um Azure PowerShell ISE. Copie e cole o script abaixo, configurando o script com seus próprios valores e, em seguida, execute o script.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>Passos seguintes
[O que é uma ACL (lista de controle de acesso) de rede?](virtual-networks-acl.md)

