---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 7a28757a0a5fabcd2aeac4e772cb39287fff3580
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95562294"
---
Se estiver a ter problemas para estabelecer ligação a uma máquina virtual através da ligação VPN, verifique o seguinte:

- Certifique-se de que a ligação VPN é efetuada com êxito.
- Certifique-se de que está a ligar-se ao endereço IP privado da VM.
- Caso consiga ligar-se à VM utilizando o endereço IP privado, mas não o nome do computador, certifique-se de que configurou o DNS corretamente. Para obter mais informações sobre como funciona a resolução de nomes para VMs, consulte o artigo [Name Resolution for VMs](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)(Resolução de Nomes para VMs).

Quando se liga através de Ponto a Site, verifique os seguintes itens adicionais:

- Utilize “ipconfig” para verificar o endereço IPv4 atribuído ao adaptador Ethernet no computador a partir do qual se está a ligar. Se o endereço IP estiver no âmbito do intervalo de endereços da VNet a que se está a ligar, ou no âmbito do intervalo de endereços do seu VPNClientAddressPool, tal trata-se de um espaço de endereços sobreposto. Quando o seu espaço de endereços se sobrepõe desta forma, o tráfego de rede não chega ao Azure e permanece na rede local.
- Certifique-se de que o pacote de configuração de clientes VPN gerado depois dos endereços IP do servidor DNS foi especificado para a VNet. Se atualizou os endereços IP do servidor DNS, gere e instale um novo pacote de configuração de cliente VPN.

Para obter mais informações sobre a resolução de problemas de uma ligação RDP, veja [Troubleshoot Remote Desktop connections to a VM](../articles/virtual-machines/troubleshooting/troubleshoot-rdp-connection.md)(Resolução de Problemas de ligações de Ambiente de Trabalho Remoto a uma VM).