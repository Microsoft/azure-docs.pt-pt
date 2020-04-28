---
title: Acesso à aplicação VM em Azure Microsoft Docs
description: Utilize estes passos detalhados de resolução de problemas para isolar problemas na ligação a aplicações em funcionamento em máquinas virtuais em Azure.
services: virtual-machines
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: não pode iniciar a aplicação, programa não abre, ouve porta bloqueada, incapaz de iniciar programa, ouvir porta bloqueada
ms.assetid: b9ff7cd0-0c5d-4c3c-a6be-3ac47abf31ba
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: caf73ffbc18a603ace22acfbd0da490048da698a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "71058127"
---
# <a name="troubleshoot-application-connectivity-issues-on-virtual-machines-in-azure"></a>Troubleshoot application connectivity issues on virtual machines in Azure (Resolver problemas de conectividade de aplicações em máquinas virtuais no Azure)

Existem várias razões para iniciar ou ligar a uma aplicação em execução numa máquina virtual Azure (VM). As razões incluem a aplicação que não está a funcionar ou a ouvir as portas esperadas, a porta de escuta bloqueada ou as regras de rede que não passam corretamente o tráfego para a aplicação. Este artigo descreve uma abordagem metódica para encontrar e corrigir o problema.

Se tiver problemas de ligação ao seu VM utilizando RDP ou SSH, consulte primeiro um dos seguintes artigos:

* [Ligações remotas de ambiente de trabalho de resolução de problemas a uma máquina virtual Azure baseada no Windows](troubleshoot-rdp-connection.md)
* [Ligações Secure Shell (SSH) com uma máquina virtual Azure baseada em Linux](troubleshoot-ssh-connection.md).

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, também pode arquivar um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.

## <a name="quick-start-troubleshooting-steps"></a>Passos de resolução de problemas de arranque rápido
Se tiver problemas de ligação a uma aplicação, experimente os seguintes passos gerais de resolução de problemas. Após cada passo, tente ligar-se novamente à sua aplicação:

* Reiniciar a máquina virtual
* Recriar as regras de ponto final / firewall / regras do grupo de segurança da rede (NSG)
  * [Modelo de Gestor de Recursos - Gerir Grupos de Segurança de Rede](../../virtual-network/manage-network-security-group.md)
  * [Modelo clássico - Gerir pontos finais dos Serviços cloud](../../cloud-services/cloud-services-enable-communication-role-instances.md)
* Conecte-se a partir de diferentes locais, como uma rede virtual Azure diferente
* Reimplementar a máquina virtual
  * [Reimplantar O VM do Windows](redeploy-to-new-node-windows.md)
  * [Reimplantação de Linux VM](redeploy-to-new-node-linux.md)
* Recriar a máquina virtual

Para mais informações, consulte a [Problemática Endpoint Connectivity (RDP/SSH/HTTP, etc. falhas)](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).

## <a name="detailed-troubleshooting-overview"></a>Visão geral detalhada de resolução de problemas
Existem quatro áreas principais para resolver o acesso de uma aplicação que está a funcionar numa máquina virtual Azure.

![resolução de problemas não pode começar aplicação](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. A aplicação em execução na máquina virtual Azure.
   * A aplicação em si está a funcionar corretamente?
2. A máquina virtual Azure.
   * O próprio VM está a funcionar corretamente e a responder aos pedidos?
3. Pontos finais da rede Azure.
   * Pontos finais de serviço em nuvem para máquinas virtuais no modelo de implementação Classic.
   * Grupos de Segurança de Rede e regras de NAT de entrada para máquinas virtuais no modelo de implementação do Gestor de Recursos.
   * O fluxo de tráfego dos utilizadores para o VM/aplicação nas portas esperadas?
4. O seu dispositivo de ponta da Internet.
   * As regras de firewall estão em vigor que impedem o tráfego de fluir corretamente?

Para computadores clientes que acedam à aplicação através de uma ligação VPN ou ExpressRoute site-to-site, as principais áreas que podem causar problemas são a aplicação e a máquina virtual Azure.

Para determinar a origem do problema e a sua correção, siga estes passos.

## <a name="step-1-access-application-from-target-vm"></a>Passo 1: Aplicação de acesso do Target VM
Tente aceder à aplicação com o programa de cliente apropriado a partir do VM em que está a funcionar. Utilize o nome do anfitrião local, o endereço IP local ou o endereço de backback (127.0.0.1).

![iniciar aplicação diretamente a partir do VM](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Por exemplo, se a aplicação for um servidor web, abra um navegador no VM e tente aceder a uma página web hospedada no VM.

Se conseguir aceder à aplicação, vá ao [Passo 2](#step2).

Se não conseguir aceder à aplicação, verifique as seguintes definições:

* A aplicação está a funcionar na máquina virtual alvo.
* A aplicação está a ouvir as portas tCP e UDP esperadas.

Em ambas as máquinas virtuais baseadas no Windows e no Linux, utilize o **netstat - um** comando para mostrar as portas de escuta ativas. Examine a saída das portas esperadas nas quais a sua aplicação deve estar a ouvir. Reiniciar a aplicação ou configurá-la para utilizar as portas esperadas conforme necessário e tentar aceder à aplicação localmente novamente.

## <a name="step-2-access-application-from-another-vm-in-the-same-virtual-network"></a><a id="step2"></a>Passo 2: Aplicação de acesso a outra VM na mesma rede virtual
Tente aceder à aplicação a partir de um VM diferente, mas na mesma rede virtual, utilizando o nome de anfitrião do VM ou o seu endereço IP público, privado ou fornecedor atribuído ao Azure. Para máquinas virtuais criadas utilizando o modelo de implementação clássico, não utilize o endereço IP público do serviço na nuvem.

![iniciar aplicação a partir de um VM diferente](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Por exemplo, se a aplicação for um servidor web, tente aceder a uma página web a partir de um navegador numa VM diferente na mesma rede virtual.

Se conseguir aceder à aplicação, vá ao [Passo 3](#step3).

Se não conseguir aceder à aplicação, verifique as seguintes definições:

* A firewall hospedeira no alvo VM está a permitir o pedido de entrada e o tráfego de resposta de saída.
* O software de deteção de intrusões ou monitorização de rede em execução no VM alvo está a permitir o tráfego.
* Os pontos finais dos Serviços cloud ou os Grupos de Segurança da Rede estão a permitir o tráfego:
  * [Modelo clássico - Gerir pontos finais dos Serviços cloud](../../cloud-services/cloud-services-enable-communication-role-instances.md)
  * [Modelo de Gestor de Recursos - Gerir Grupos de Segurança de Rede](../../virtual-network/manage-network-security-group.md)
* Um componente separado que funciona no seu VM no caminho entre o VM de teste e o seu VM, como um equilibrista de carga ou firewall, está a permitir o tráfego.

Numa máquina virtual baseada no Windows, utilize o Windows Firewall com Advanced Security para determinar se as regras de firewall excluem o tráfego de entrada e saída da sua aplicação.

## <a name="step-3-access-application-from-outside-the-virtual-network"></a><a id="step3"></a>Passo 3: Aplicação de acesso de fora da rede virtual
Tente aceder à aplicação a partir de um computador fora da rede virtual como o VM em que a aplicação está em execução. Use uma rede diferente como o seu computador cliente original.

![iniciar aplicação a partir de um computador fora da rede virtual](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Por exemplo, se a aplicação for um servidor web, tente aceder à página web a partir de um navegador que funciona num computador que não esteja na rede virtual.

Se não conseguir aceder à aplicação, verifique as seguintes definições:

* Para VMs criados usando o modelo de implementação clássico:
  
  * Verifique se a configuração do ponto final para o VM está a permitir o tráfego de entrada, especialmente o protocolo (TCP ou UDP) e os números de portas públicas e privadas.
  * Verifique se as listas de controlo de acesso (ACLs) no ponto final não estão a impedir a entrada de tráfego da Internet.
  * Para mais informações, consulte [Como configurar pontos finais para uma máquina virtual](../windows/classic/setup-endpoints.md).
* Para VMs criados usando o modelo de implementação do Gestor de Recursos:
  
  * Verifique se a configuração da regra NAT de entrada para o VM está a permitir o tráfego de entrada, especialmente o protocolo (TCP ou UDP) e os números de porto público e privado.
  * Verifique se os Grupos de Segurança da Rede estão a permitir o pedido de entrada e o tráfego de resposta de saída.
  * Para mais informações, consulte o que é um grupo de segurança da [rede?](../../virtual-network/security-overview.md)

Se a máquina virtual ou o ponto final for membro de um conjunto equilibrado em carga:

* Verifique se o protocolo da sonda (TCP ou UDP) e o número da porta estão corretos.
* Se o protocolo de sonda e a porta forem diferentes do protocolo definido e da porta equilibrados:
  * Verifique se a aplicação está a ouvir o protocolo da sonda (TCP ou UDP) e o número da porta (use **netstat – a** no VM alvo).
  * Verifique se a firewall do hospedeiro no alvo VM está a permitir o pedido de sonda de entrada e o tráfego de resposta à sonda de saída.

Se conseguir aceder à aplicação, certifique-se de que o seu dispositivo de ponta de Internet está a permitir:

* A aplicação de saída solicita tráfego do seu computador cliente para a máquina virtual Azure.
* O tráfego de resposta à aplicação de entrada da máquina virtual Azure.

## <a name="step-4-if-you-cannot-access-the-application-use-ip-verify-to-check-the-settings"></a>Passo 4 Se não conseguir aceder à aplicação, utilize IP Verifique para verificar as definições. 

Para mais informações, consulte a visão geral da [monitorização da rede Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="additional-resources"></a>Recursos adicionais
[Ligações remotas de ambiente de trabalho de resolução de problemas a uma máquina virtual Azure baseada no Windows](troubleshoot-rdp-connection.md)

[Ligações De troubleshoot Secure Shell (SSH) a uma máquina virtual Azure baseada em Linux](troubleshoot-ssh-connection.md)


