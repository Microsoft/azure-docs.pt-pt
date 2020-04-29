---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6d0737a7300b2a6025f776c1ed65a05cacf2141a
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67184125"
---
Crie uma ligação remota para o ambiente de trabalho para ligar a um VM que está implantado no seu VNet. A melhor maneira de verificar se pode ligar ao seu VM é ligar-se ao seu endereço IP privado, em vez do seu nome de computador. Dessa forma, está a testar para ver se consegue ligar-se, não se a resolução de nomeestá corretamente configurada. 

1. Localizar o endereço IP privado para a sua VM. Para encontrar o endereço IP privado de um VM, veja as propriedades para o VM no portal Azure ou utilize a PowerShell.
2. Verifique se está ligado ao seu VNet com a ligação VPN Ponto-ao-Local. 
3. Para abrir a ligação remota de ambiente de trabalho, introduza a *ligação* *de rdp* ou ambiente de trabalho remoto na caixa de pesquisa na barra de tarefas e, em seguida, selecione **A Ligação remota**de ambiente de trabalho . Também pode abri-lo utilizando o comando **mstsc** no PowerShell. 
3. Na **Ligação Remota**de Ambiente de Trabalho, introduza o endereço IP privado do VM. Se necessário, selecione **Mostrar Opções** para ajustar definições adicionais e, em seguida, ligar.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Para resolver problemas de ligação RDP numa VM

Se está com dificuldades em ligar-se a uma máquina virtual sobre a sua ligação VPN, há algumas coisas que pode verificar. 

- Certifique-se de que a ligação VPN é efetuada com êxito.
- Verifique se está a ligar-se ao endereço IP privado para o VM.
- Introduza **ipconfig** para verificar o endereço IPv4 atribuído ao adaptador Ethernet no computador a partir do qual está a ligar. Ocorre um espaço de endereço sobreposto quando o endereço IP está dentro do intervalo de endereços do VNet a que está a ligar, ou dentro do intervalo de endereços do seu VPNClientAddressPool. Quando o seu espaço de endereços se sobrepõe desta forma, o tráfego de rede não chega ao Azure e permanece na rede local.
- Se conseguir ligar-se ao VM utilizando o endereço IP privado, mas não o nome do computador, verifique se configurabem o DNS. Para obter mais informações sobre como funciona a resolução de nomes para VMs, consulte o artigo [Name Resolution for VMs](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)(Resolução de Nomes para VMs).
- Verifique se o pacote de configuração do cliente VPN é gerado depois de especificar os endereços IP do servidor DNS para o VNet. Se atualizar os endereços IP do servidor DNS, gere e instala um novo pacote de configuração de cliente VPN.

Para obter mais informações sobre a resolução de problemas, veja [Troubleshoot Remote Desktop connections to a VM](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)(Resolução de Problemas de ligações de Ambiente de Trabalho Remoto a uma VM).