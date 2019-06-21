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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184125"
---
Crie uma ligação de ambiente de trabalho remoto para ligar a uma VM que é implementada nos VNet. A melhor forma de verificar que se pode ligar à sua VM é ligar com o respetivo endereço IP privado, em vez de seu nome de computador. Dessa forma, que está a testar para ver se pode ligar, não se a resolução de nomes está corretamente configurada. 

1. Localizar o endereço IP privado para a sua VM. Para obter o endereço IP privado de uma VM, ver as propriedades para a VM no portal do Azure ou utilizar o PowerShell.
2. Certifique-se de que está ligado à VNet com a ligação VPN ponto a Site. 
3. Para abrir a ligação de ambiente de trabalho remoto, introduza *RDP* ou *conexão de área de trabalho remoto* na caixa de pesquisa na barra de tarefas, em seguida, selecione **conexão de área de trabalho remoto**. Também pode abri-lo utilizando o **mstsc** comando no PowerShell. 
3. Na **conexão de área de trabalho remoto**, introduza o endereço IP privado da VM. Se necessário, selecione **Mostrar opções** para ajustar as definições adicionais, em seguida, ligar.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Para resolver problemas de ligação RDP numa VM

Se estiver a ter problemas em ligar a uma máquina virtual através da ligação VPN, existem algumas coisas que pode verificar. 

- Certifique-se de que a ligação VPN é efetuada com êxito.
- Certifique-se de que está a ligar para o endereço IP privado da VM.
- Introduza **ipconfig** para verificar o endereço IPv4 atribuído ao adaptador Ethernet no computador do qual está se conectando. Um espaço de endereços sobrepostos ocorre quando o endereço IP está dentro do intervalo de endereços da VNet que está a ligar a ou dentro do intervalo de endereço do seu VPNClientAddressPool. Quando o seu espaço de endereços se sobrepõe desta forma, o tráfego de rede não chega ao Azure e permanece na rede local.
- Se consegue ligar à VM utilizando o endereço IP privado, mas não o nome do computador, certifique-se de que configurou DNS corretamente. Para obter mais informações sobre como funciona a resolução de nomes para VMs, consulte o artigo [Name Resolution for VMs](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)(Resolução de Nomes para VMs).
- Certifique-se de que o pacote de configuração de cliente VPN é gerado depois de especificar os endereços IP do servidor DNS para a VNet. Se atualizar os endereços IP do servidor DNS, gere e instale um novo pacote de configuração de cliente VPN.

Para obter mais informações sobre a resolução de problemas, veja [Troubleshoot Remote Desktop connections to a VM](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)(Resolução de Problemas de ligações de Ambiente de Trabalho Remoto a uma VM).