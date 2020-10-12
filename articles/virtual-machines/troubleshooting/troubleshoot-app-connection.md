---
title: Acesso a aplicações VM de resolução de problemas em Azure Microsoft Docs
description: Utilize estes passos detalhados de resolução de problemas para isolar problemas na ligação a aplicações em execução em máquinas virtuais em Azure.
services: virtual-machines
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: não pode iniciar a aplicação, o programa não abre, ouve porta bloqueada, incapaz de iniciar o programa, ouvir porta bloqueada
ms.assetid: b9ff7cd0-0c5d-4c3c-a6be-3ac47abf31ba
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: a2fe1b8bdc80a5265add22bd3602050034471fe2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87036442"
---
# <a name="troubleshoot-application-connectivity-issues-on-virtual-machines-in-azure"></a>Troubleshoot application connectivity issues on virtual machines in Azure (Resolver problemas de conectividade de aplicações em máquinas virtuais no Azure)

Existem várias razões quando não pode iniciar ou ligar a uma aplicação em execução numa máquina virtual Azure (VM). As razões incluem a aplicação não funcionando ou escutando nas portas esperadas, a porta de escuta bloqueada, ou regras de networking que não passam corretamente o tráfego para a aplicação. Este artigo descreve uma abordagem metódica para encontrar e corrigir o problema.

Se tiver problemas de ligação ao seu VM utilizando RDP ou SSH, consulte primeiro um dos seguintes artigos:

* [Resolução de problemas Ligações de ambiente de trabalho remoto a uma máquina virtual Azure baseada no Windows](troubleshoot-rdp-connection.md)
* [Resolução de problemas Ligações Secure Shell (SSH) a uma máquina virtual Azure baseada em Linux](troubleshoot-ssh-connection.md).

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn azure e nos fóruns Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, também pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter **Apoio**.

## <a name="quick-start-troubleshooting-steps"></a>Etapas de resolução de problemas de arranque rápido
Se tiver problemas de ligação a uma aplicação, experimente as seguintes etapas gerais de resolução de problemas. Depois de cada passo, tente ligar-se novamente à sua aplicação:

* Reiniciar a máquina virtual
* Recriar as regras do ponto final / firewall / grupo de segurança de rede (NSG)
  * [Modelo de Gestor de Recursos - Gerir grupos de segurança de rede](../../virtual-network/manage-network-security-group.md)
  * [Modelo clássico - Gerir pontos finais dos Serviços cloud](../../cloud-services/cloud-services-enable-communication-role-instances.md)
* Conecte-se a partir de diferentes localizações, como uma rede virtual Azure diferente
* Reimplementar a máquina virtual
  * [Reimplantia Janelas VM](redeploy-to-new-node-windows.md)
  * [Reposicionar Linux VM](redeploy-to-new-node-linux.md)
* Recriar a máquina virtual

Para obter mais informações, consulte [a conectividade endpoint de resolução de problemas (RDP/SSH/HTTP, etc. falhas)](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).

## <a name="detailed-troubleshooting-overview"></a>Visão geral detalhada da resolução de problemas
Existem quatro áreas principais para resolver o acesso de uma aplicação que está a funcionar numa máquina virtual Azure.

![resolução de problemas não pode iniciar aplicação](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. A aplicação em execução na máquina virtual Azure.
   * A aplicação em si está a funcionar corretamente?
2. A máquina virtual Azure.
   * O próprio VM está a funcionar corretamente e a responder aos pedidos?
3. Pontos finais da rede Azure.
   * Pontos finais de serviço em nuvem para máquinas virtuais no modelo de implementação Classic.
   * Grupos de segurança de rede e regras NAT de entrada para máquinas virtuais no modelo de implementação do Gestor de Recursos.
   * O tráfego pode fluir dos utilizadores para o VM/aplicação nas portas esperadas?
4. O seu dispositivo de borda da Internet.
   * As regras de firewall estão em vigor para impedir que o tráfego flua corretamente?

Para computadores clientes que estão a aceder à aplicação por uma ligação VPN ou ExpressRoute, as principais áreas que podem causar problemas são a aplicação e a máquina virtual Azure.

Para determinar a origem do problema e a sua correção, siga estes passos.

## <a name="step-1-access-application-from-target-vm"></a>Passo 1: Aplicação de acesso a partir do VM alvo
Tente aceder à aplicação com o programa de cliente apropriado a partir do VM em que está em execução. Utilize o nome do anfitrião local, o endereço IP local ou o endereço de retorno (127.0.0.1).

![iniciar a aplicação diretamente a partir do VM](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Por exemplo, se a aplicação for um servidor web, abra um browser no VM e tente aceder a uma página web hospedada no VM.

Se puder aceder à aplicação, vá ao [passo 2](#step2).

Se não conseguir aceder à aplicação, verifique as seguintes definições:

* A aplicação está a ser em execução na máquina virtual alvo.
* A aplicação está a ser escutada nas portas TCP e UDP esperadas.

Tanto nas máquinas virtuais baseadas no Windows como no Linux, utilize o **netstat - um** comando para mostrar as portas de audição ativas. Examine a saída das portas esperadas nas quais a sua aplicação deve estar a ouvir. Reinicie a aplicação ou configuure-a para utilizar as portas esperadas conforme necessário e tente aceder novamente à aplicação localmente.

## <a name="step-2-access-application-from-another-vm-in-the-same-virtual-network"></a><a id="step2"></a>Passo 2: Aplicação de acesso a partir de outro VM na mesma rede virtual
Tente aceder à aplicação a partir de um VM diferente mas na mesma rede virtual, utilizando o nome de anfitrião do VM ou o seu endereço IP público, privado ou fornecedor atribuído pelo Azure. Para máquinas virtuais criadas utilizando o modelo de implementação clássico, não utilize o endereço IP público do serviço de nuvem.

![iniciar a aplicação a partir de um VM diferente](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Por exemplo, se a aplicação for um servidor web, tente aceder a uma página web a partir de um navegador num VM diferente na mesma rede virtual.

Se puder aceder à aplicação, vá ao [passo 3](#step3).

Se não conseguir aceder à aplicação, verifique as seguintes definições:

* A firewall do anfitrião no VM alvo está a permitir o pedido de entrada e o tráfego de resposta de saída.
* O software de deteção de intrusões ou monitorização de rede em execução no VM alvo está a permitir o tráfego.
* Os pontos finais dos Serviços de Nuvem ou grupos de segurança da rede estão a permitir o tráfego:
  * [Modelo clássico - Gerir pontos finais dos Serviços cloud](../../cloud-services/cloud-services-enable-communication-role-instances.md)
  * [Modelo de Gestor de Recursos - Gerir grupos de segurança de rede](../../virtual-network/manage-network-security-group.md)
* Um componente separado que funciona no seu VM no caminho entre o VM de teste e o seu VM, como um equilibrador de carga ou firewall, está a permitir o tráfego.

Numa máquina virtual baseada no Windows, utilize o Windows Firewall com Segurança Avançada para determinar se as regras de firewall excluem o tráfego de entrada e saída da sua aplicação.

## <a name="step-3-access-application-from-outside-the-virtual-network"></a><a id="step3"></a>Passo 3: Aplicação de acesso de fora da rede virtual
Tente aceder à aplicação a partir de um computador fora da rede virtual como o VM em que a aplicação está em execução. Use uma rede diferente como o seu computador cliente original.

![iniciar a aplicação a partir de um computador fora da rede virtual](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Por exemplo, se a aplicação for um servidor web, tente aceder à página web a partir de um navegador que esteja a funcionar num computador que não esteja na rede virtual.

Se não conseguir aceder à aplicação, verifique as seguintes definições:

* Para VMs criados utilizando o modelo de implementação clássico:
  
  * Verifique se a configuração do ponto final para o VM está a permitir o tráfego de entrada, especialmente o protocolo (TCP ou UDP) e os números de porta público e privado.
  * Verifique se as listas de controlo de acesso (ACLs) no ponto final não estão a impedir a entrada de tráfego a partir da Internet.
  * Para obter mais informações, consulte [Como Configurar pontos finais para uma máquina virtual.](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints)
* Para VMs criados utilizando o modelo de implementação do Gestor de Recursos:
  
  * Verifique se a configuração da regra NAT de entrada para o VM está a permitir o tráfego de entrada, especialmente o protocolo (TCP ou UDP) e os números de porta público e privado.
  * Verifique se os Grupos de Segurança da Rede estão a permitir o pedido de entrada e o tráfego de resposta de saída.
  * Para mais informações, veja [o que é um grupo de segurança de rede?](../../virtual-network/security-overview.md)

Se a máquina virtual ou o ponto final forem membros de um conjunto equilibrado de carga:

* Verifique se o protocolo da sonda (TCP ou UDP) e o número da porta estão corretos.
* Se o protocolo da sonda e a porta forem diferentes do protocolo e porta equilibrados em carga:
  * Verifique se a aplicação está a ouvir o protocolo da sonda (TCP ou UDP) e o número da porta (utilizar **netstat – a** no VM-alvo).
  * Verifique se a firewall do anfitrião no VM alvo está a permitir o pedido de entrada da sonda e o tráfego de resposta da sonda de saída.

Se conseguir aceder à aplicação, certifique-se de que o seu dispositivo de borda da Internet está a permitir:

* A aplicação de saída solicita tráfego do computador do seu cliente para a máquina virtual Azure.
* O tráfego de resposta à aplicação de entrada da máquina virtual Azure.

## <a name="step-4-if-you-cannot-access-the-application-use-ip-verify-to-check-the-settings"></a>Passo 4 Se não conseguir aceder à aplicação, utilize IP Verifique as definições. 

Para obter mais informações, consulte [a visão geral da monitorização da rede Azure](../../network-watcher/network-watcher-monitoring-overview.md). 

## <a name="additional-resources"></a>Recursos adicionais
[Resolução de problemas Ligações de ambiente de trabalho remoto a uma máquina virtual Azure baseada no Windows](troubleshoot-rdp-connection.md)

[Resolução de problemas Ligações Secure Shell (SSH) a uma máquina virtual Azure baseada em Linux](troubleshoot-ssh-connection.md)
