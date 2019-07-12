---
title: Resolver problemas de acesso de aplicação de VM no Azure | Documentos da Microsoft
description: Utilize estes passos de resolução de problemas detalhados para isolar problemas de estabelecer ligação a aplicações em execução em máquinas virtuais no Azure.
services: virtual-machines
documentationcenter: ''
author: genlin
manager: gwallace
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: Não é possível iniciar a aplicação, programa não abrir, escutar a porta bloqueada, não é possível iniciar o programa, escutar a porta bloqueada
ms.assetid: b9ff7cd0-0c5d-4c3c-a6be-3ac47abf31ba
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 9bc528cdd098a2e355c542c3ca8f9bcb0287f339
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710532"
---
# <a name="troubleshoot-application-connectivity-issues-on-virtual-machines-in-azure"></a>Resolver problemas de conectividade de aplicativo em máquinas virtuais no Azure

Existem vários motivos quando não é possível iniciar ou ligar a uma aplicação em execução numa máquina virtual do Azure (VM). Os motivos incluem a aplicação não está em execução ou a escutar nas portas esperadas, a porta de escuta bloqueado ou funcionamento em rede de regras não corretamente o tráfego de passar para a aplicação. Este artigo descreve uma abordagem metódica para localizar e corrigir o problema.

Se estiver a ter problemas de ligação à sua VM através de RDP ou SSH, consulte um dos seguintes artigos primeiro:

* [Resolver problemas de ligações de ambiente de trabalho remoto para um baseado em Windows Máquina Virtual do Azure](troubleshoot-rdp-connection.md)
* [Resolver problemas de ligações Secure Shell (SSH) para uma máquina virtual do Azure baseado em Linux](troubleshoot-ssh-connection.md).

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure no [do Azure do MSDN e os fóruns de Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, também pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.

## <a name="quick-start-troubleshooting-steps"></a>Passos de resolução de problemas de início rápido
Se tiver problemas a ligar a uma aplicação, tente os seguintes passos de resolução de problemas gerais. Após cada passo, tente ligar ao seu aplicativo novamente:

* Reiniciar a máquina virtual
* Recrie o ponto final / regras de firewall / regras de grupo (NSG) de segurança de rede
  * [Modelo do Resource Manager - gerir grupos de segurança de rede](../../virtual-network/manage-network-security-group.md)
  * [Modelo clássico – os pontos finais gerem serviços em nuvem](../../cloud-services/cloud-services-enable-communication-role-instances.md)
* Ligar a partir de uma localização diferente, por exemplo, a outra rede virtual do Azure
* Reimplementar a máquina virtual
  * [Reimplementar VM do Windows](redeploy-to-new-node-windows.md)
  * [Reimplementar VM do Linux](redeploy-to-new-node-linux.md)
* Recriar a máquina virtual

Para obter mais informações, consulte [resolução de problemas de conectividade de ponto final (RDP/SSH/HTTP, falhas de etc.)](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).

## <a name="detailed-troubleshooting-overview"></a>Visão geral de resolução de problemas detalhada
Existem quatro áreas principais para resolver problemas de acesso de uma aplicação que está em execução numa máquina virtual do Azure.

![resolução de problemas não é possível iniciar a aplicação](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. O aplicativo em execução na máquina virtual do Azure.
   * A própria aplicação está a funcionar corretamente?
2. A máquina virtual do Azure.
   * É a própria VM em execução corretamente e responder a pedidos?
3. Pontos finais de rede do Azure.
   * Cloud pontos finais de serviço para máquinas virtuais no modelo de implementação clássica.
   * Grupos de segurança de rede e regras NAT de entrada para máquinas virtuais no modelo de implementação do Resource Manager.
   * Pode tráfego de fluxo de usuários do aplicativo/VM nas portas esperados?
4. O dispositivo de borda de Internet.
   * Estão as regras de firewall no local a impedir que o tráfego que flui corretamente?

Para computadores de cliente que estão a aceder à aplicação através de uma ligação de site-site VPN ou ExpressRoute, as áreas principais que podem causar problemas são o aplicativo e a máquina virtual do Azure.

Para determinar a origem do problema e sua correção, siga estes passos.

## <a name="step-1-access-application-from-target-vm"></a>Passo 1: Aplicação de acesso da VM de destino
Tente aceder à aplicação com o programa de cliente adequado a partir da VM em que está em execução. Utilize o nome de anfitrião local, o endereço IP local ou o endereço de loopback (127.0.0.1).

![iniciar o aplicativo diretamente a partir da VM](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Por exemplo, se o aplicativo for um servidor web, abra um browser na VM e tente aceder a uma página web alojada na VM.

Se consegue aceder à aplicação, aceda a [passo 2](#step2).

Se não é possível aceder à aplicação, verifique se as seguintes definições:

* A aplicação está em execução na máquina de virtual de destino.
* A aplicação está a escutar nas portas TCP e UDP esperadas.

No Windows e máquinas virtuais baseadas em Linux, utilize o **netstat - a** comando para mostrar as portas de escuta ativas. Examine a saída para as portas esperadas no qual a aplicação deve escutar. Reinicie a aplicação ou configurá-lo para utilizar as portas esperadas conforme necessário e tente novamente a aceder a aplicação localmente.

## <a id="step2"></a>Passo 2: Aplicação de acesso a partir de outra VM na mesma rede virtual
Tente aceder à aplicação a partir de uma VM diferente, mas na mesma rede virtual, com o nome de anfitrião da VM ou o respetivo atribuído o Azure pública, privada ou fornecedor de endereço IP. Para máquinas de virtuais criadas com o modelo de implementação clássica, não utilize o endereço IP público do serviço cloud.

![iniciar o aplicativo a partir de uma VM diferente](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Por exemplo, se o aplicativo for um servidor web, tente acessar uma página da web a partir de um browser numa VM diferente na mesma rede virtual.

Se consegue aceder à aplicação, aceda a [passo 3](#step3).

Se não é possível aceder à aplicação, verifique se as seguintes definições:

* A firewall do anfitrião na VM de destino é permitir que a solicitação de entrada e o tráfego de saída de resposta.
* Detecção de intrusão ou de software em execução na VM de destino de monitorização de rede está a permitir o tráfego.
* Grupos de segurança de rede ou de pontos finais de serviços cloud estão a permitir o tráfego:
  * [Modelo clássico – os pontos finais gerem serviços em nuvem](../../cloud-services/cloud-services-enable-communication-role-instances.md)
  * [Modelo do Resource Manager - gerir grupos de segurança de rede](../../virtual-network/manage-network-security-group.md)
* Um componente separado em execução na sua VM no caminho entre o teste de VM e a sua VM, como uma firewall, ou um balanceador de carga está a permitir o tráfego.

Numa máquina virtual baseada no Windows, utilize a Firewall do Windows com segurança avançada para determinar se as regras de firewall excluir tráfego de entrada e saída da sua aplicação.

## <a id="step3"></a>Passo 3: Aplicação de acesso a partir de fora da rede virtual
Tente aceder à aplicação a partir de um computador fora da rede virtual da VM em que a aplicação está em execução. Utilize uma rede diferente que o computador cliente original.

![iniciar o aplicativo a partir de um computador fora da rede virtual](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Por exemplo, se o aplicativo for um servidor web, tente aceder a página da web a partir de um browser em execução num computador que não esteja na rede virtual.

Se não é possível aceder à aplicação, verifique se as seguintes definições:

* Para as VMs criadas com o modelo de implementação clássica:
  
  * Certifique-se de que a configuração de ponto final para a VM está a permitir o tráfego de entrada, especialmente, o protocolo (TCP ou UDP) e os números de porta pública e privada.
  * Certifique-se de que as listas de controlo de acesso (ACLs) no ponto final não estão a impedir o tráfego de entrada da Internet.
  * Para obter mais informações, consulte [como configurar pontos finais para uma Máquina Virtual](../windows/classic/setup-endpoints.md).
* Para as VMs criadas com o modelo de implementação do Resource Manager:
  
  * Certifique-se de que a configuração da regra NAT entrada para a VM está a permitir o tráfego de entrada, especialmente, o protocolo (TCP ou UDP) e os números de porta pública e privada.
  * Certifique-se de que os grupos de segurança de rede está a permitir que a solicitação de entrada e o tráfego de saída de resposta.
  * Para obter mais informações, consulte [o que é um grupo de segurança de rede?](../../virtual-network/security-overview.md)

Se a máquina virtual ou o ponto final for um membro de um conjunto com balanceamento de carga:

* Certifique-se de que o protocolo de sonda (TCP ou UDP) e o número da porta estão corretos.
* Se o protocolo de sonda e a porta é diferente do que o protocolo de conjunto com balanceamento de carga e a porta:
  * Certifique-se de que a aplicação está a escutar o protocolo de sonda (TCP ou UDP) e o número de porta (utilize **netstat – a** na VM de destino).
  * Certifique-se de que a firewall do anfitrião na VM de destino está a permitir o pedido de sonda de entrada e o tráfego de resposta de sonda de saída.

Se consegue aceder à aplicação, certifique-se de que o seu dispositivo do edge de Internet está a permitir:

* O saída pedido tráfego de aplicativo do computador cliente para a máquina virtual do Azure.
* O tráfego de resposta de aplicação de entrada da máquina virtual do Azure.

## <a name="step-4-if-you-cannot-access-the-application-use-ip-verify-to-check-the-settings"></a>Passo 4 se não é possível aceder à aplicação, utilize Certifique-se de IP para verificar as definições. 

Para obter mais informações, consulte [descrição geral da monitorização de rede do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="additional-resources"></a>Recursos adicionais
[Resolver problemas de ligações de ambiente de trabalho remoto para um baseado em Windows Máquina Virtual do Azure](troubleshoot-rdp-connection.md)

[Resolver problemas de ligações Secure Shell (SSH) para uma máquina virtual do Azure baseado em Linux](troubleshoot-ssh-connection.md)


