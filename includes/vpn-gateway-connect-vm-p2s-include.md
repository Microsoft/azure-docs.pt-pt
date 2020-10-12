---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9d72a64bcc494385384accb75061fffa19fd9337
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77149204"
---
Pode ligar a uma VM que é implementada nos VNet criando uma Ligação de Ambiente de Trabalho Remoto para a VM. A melhor forma de verificar, inicialmente, que se pode ligar à VM é ligar-se utilizando o respetivo endereço IP privado, em vez do nome do computador. Dessa forma, está a testar a possibilidade de ligação, não se a resolução de nomes está corretamente configurada.

1. Localizar o endereço IP privado. Pode encontrar o endereço IP privado de uma VM observando as propriedades da VM no portal do Azure ou utilizando o PowerShell.

   - Portal do Azure - Localizar a máquina virtual no Portal do Azure. Ver as propriedades da VM. O endereço IP privado está listado.

   - PowerShell - Utilize o exemplo para ver uma lista de VMs e endereços IP privados a partir de grupos de recursos. Não é necessário modificar este exemplo antes de o utilizar.

     ```azurepowershell-interactive
     $VMs = Get-AzVM
     $Nics = Get-AzNetworkInterface | Where VirtualMachine -ne $null

     foreach($Nic in $Nics)
     {
      $VM = $VMs | Where-Object -Property Id -eq $Nic.VirtualMachine.Id
      $Prv = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAddress
      $Alloc = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($VM.Name): $Prv,$Alloc"
     }
     ```

2. Certifique-se de que está ligado à VNet através da ligação VPN Ponto a Site.
3. Abra **a Ligação remota do ambiente de trabalho** digitando "RDP" ou "Remote Desktop Connection" na caixa de pesquisa na barra de tarefas e, em seguida, selecione Remote Desktop Connection. Também pode abrir a Ligação de Ambiente de Trabalho Remoto utilizando o comando "mstsc" no PowerShell. 
4. Na Ligação de Ambiente de Trabalho Remoto, introduza o endereço IP privado da VM. Pode clicar em "Mostrar Opções" para ajustar as definições adicionais e, em seguida, em ligar.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Para resolver problemas de ligação RDP numa VM

Se estiver a ter problemas para estabelecer ligação a uma máquina virtual através da ligação VPN, verifique o seguinte:

- Certifique-se de que a ligação VPN é efetuada com êxito.
- Certifique-se de que está a ligar-se ao endereço IP privado da VM.
- Utilize “ipconfig” para verificar o endereço IPv4 atribuído ao adaptador Ethernet no computador a partir do qual se está a ligar. Se o endereço IP estiver no âmbito do intervalo de endereços da VNet a que se está a ligar, ou no âmbito do intervalo de endereços do seu VPNClientAddressPool, tal trata-se de um espaço de endereços sobreposto. Quando o seu espaço de endereços se sobrepõe desta forma, o tráfego de rede não chega ao Azure e permanece na rede local.
- Caso consiga ligar-se à VM utilizando o endereço IP privado, mas não o nome do computador, certifique-se de que configurou o DNS corretamente. Para obter mais informações sobre como funciona a resolução de nomes para VMs, consulte o artigo [Name Resolution for VMs](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)(Resolução de Nomes para VMs).
- Certifique-se de que o pacote de configuração de clientes VPN gerado depois dos endereços IP do servidor DNS foi especificado para a VNet. Se atualizou os endereços IP do servidor DNS, gere e instale um novo pacote de configuração de cliente VPN.
- Para obter mais informações sobre ligações RDP, veja [Troubleshoot Remote Desktop connections to a VM](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)(Resolução de Problemas de ligações de Ambiente de Trabalho Remoto a uma VM).