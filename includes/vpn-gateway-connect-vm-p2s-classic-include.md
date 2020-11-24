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
ms.openlocfilehash: 4fc14083b0912cf18f98c229adcb297b89bc8971
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95556257"
---
Crie uma Ligação de Ambiente de Trabalho Remoto para ligar a um VM que seja implantado no seu VNet. A melhor forma de verificar se pode ligar-se ao seu VM é conectar-se com o seu endereço IP privado, em vez do nome do computador. Dessa forma, está a testar para ver se consegue ligar, não se a resolução do nome está configurada corretamente. 

1. Localizar o endereço IP privado para a sua VM. Para encontrar o endereço IP privado de um VM, veja as propriedades do VM no portal Azure ou utilize o PowerShell.
2. Verifique se está ligado ao seu VNet com a ligação VPN ponto-a-local. 
3. Para abrir a Ligação de Ambiente de Trabalho Remoto, introduza *a ligação* de ambiente de trabalho *RDP* ou remote na caixa de pesquisa na barra de tarefas e, em seguida, selecione **Ligação remota do ambiente de trabalho**. Também pode abri-lo utilizando o comando **mstsc** em PowerShell. 
3. Em **Ligação de Ambiente de Trabalho Remoto,** insira o endereço IP privado do VM. Se necessário, selecione **As Opções de Exibição** para ajustar as definições adicionais e, em seguida, conecte-se.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Para resolver problemas de ligação RDP numa VM

Se tiver problemas em ligar-se a uma máquina virtual sobre a sua ligação VPN, há algumas coisas que pode verificar. 

- Certifique-se de que a ligação VPN é efetuada com êxito.
- Verifique se está a ligar-se ao endereço IP privado para o VM.
- Introduza **o ipconfig** para verificar o endereço IPv4 atribuído ao adaptador Ethernet no computador a partir do qual está a ligar. Ocorre um espaço de endereço sobreposto quando o endereço IP está dentro do intervalo de endereços do VNet a que está a ligar ou dentro do intervalo de endereços do seu VPNClientAddressPool. Quando o seu espaço de endereços se sobrepõe desta forma, o tráfego de rede não chega ao Azure e permanece na rede local.
- Se conseguir ligar-se ao VM utilizando o endereço IP privado, mas não o nome do computador, verifique se configura o DNS corretamente. Para obter mais informações sobre como funciona a resolução de nomes para VMs, consulte o artigo [Name Resolution for VMs](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)(Resolução de Nomes para VMs).
- Verifique se o pacote de configuração do cliente VPN é gerado depois de especificar os endereços IP do servidor DNS para o VNet. Se atualizar os endereços IP do servidor DNS, gere e instale um novo pacote de configuração do cliente VPN.

Para obter mais informações sobre a resolução de problemas, veja [Troubleshoot Remote Desktop connections to a VM](../articles/virtual-machines/troubleshooting/troubleshoot-rdp-connection.md)(Resolução de Problemas de ligações de Ambiente de Trabalho Remoto a uma VM).