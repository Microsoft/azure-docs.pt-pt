---
title: Solucionar problemas de acesso a aplicativos da VM no Azure | Microsoft Docs
description: Use estas etapas detalhadas de solução de problemas para isolar problemas ao conectar-se a aplicativos executados em máquinas virtuais no Azure.
services: virtual-machines
documentationcenter: ''
author: genlin
manager: gwallace
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: Não é possível iniciar o aplicativo, o programa não será aberto, escuta a porta bloqueada, não é possível iniciar o programa, porta de escuta bloqueada
ms.assetid: b9ff7cd0-0c5d-4c3c-a6be-3ac47abf31ba
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: fd79e04cdd8f9d01131c016031d696c1583eb55d
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70080406"
---
# <a name="troubleshoot-application-connectivity-issues-on-virtual-machines-in-azure"></a>Solucionar problemas de conectividade de aplicativos em máquinas virtuais no Azure

Há vários motivos pelos quais você não pode iniciar ou se conectar a um aplicativo em execução em uma VM (máquina virtual) do Azure. Os motivos incluem que o aplicativo não está em execução ou escutando nas portas esperadas, a porta de escuta bloqueada ou as regras de rede não transmitem corretamente o tráfego para o aplicativo. Este artigo descreve uma abordagem método para localizar e corrigir o problema.

Se você estiver tendo problemas para se conectar à sua VM usando RDP ou SSH, consulte um dos seguintes artigos primeiro:

* [Solucionar problemas de conexões Área de Trabalho Remota a uma máquina virtual do Azure baseada no Windows](troubleshoot-rdp-connection.md)
* [Solucionar problemas de conexões Secure Shell (SSH) para uma máquina virtual do Azure baseada em Linux](troubleshoot-ssh-connection.md).

Se precisar de mais ajuda a qualquer momento neste artigo, você poderá entrar em contato com os especialistas do Azure no [msdn do Azure e nos fóruns de Stack Overflow](https://azure.microsoft.com/support/forums/). Como alternativa, você também pode arquivar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.

## <a name="quick-start-troubleshooting-steps"></a>Etapas de solução de problemas de início rápido
Se você tiver problemas para se conectar a um aplicativo, tente as seguintes etapas gerais de solução de problemas. Após cada etapa, tente se conectar ao seu aplicativo novamente:

* Reiniciar a máquina virtual
* Recriar as regras de ponto de extremidade/firewall/regras de NSG (grupo de segurança de rede)
  * [Modelo do Resource Manager – gerenciar grupos de segurança de rede](../../virtual-network/manage-network-security-group.md)
  * [Modelo clássico – gerenciar pontos de extremidade de serviços de nuvem](../../cloud-services/cloud-services-enable-communication-role-instances.md)
* Conectar-se de um local diferente, como uma rede virtual do Azure diferente
* Reimplementar a máquina virtual
  * [Reimplantar a VM do Windows](redeploy-to-new-node-windows.md)
  * [Reimplantar VM Linux](redeploy-to-new-node-linux.md)
* Recriar a máquina virtual

Para obter mais informações, consulte [Solucionando problemas de conectividade de ponto de extremidade (RDP/SSH/http, etc. falhas)](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).

## <a name="detailed-troubleshooting-overview"></a>Visão geral detalhada da solução de problemas
Há quatro áreas principais para solucionar problemas de acesso de um aplicativo que está sendo executado em uma máquina virtual do Azure.

![solucionar problemas não é possível iniciar o aplicativo](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. O aplicativo em execução na máquina virtual do Azure.
   * O aplicativo está sendo executado corretamente?
2. A máquina virtual do Azure.
   * A própria VM está sendo executada corretamente e respondendo a solicitações?
3. Pontos de extremidade de rede do Azure.
   * Pontos de extremidade de serviço de nuvem para máquinas virtuais no modelo de implantação clássico.
   * Grupos de segurança de rede e regras de NAT de entrada para máquinas virtuais no modelo de implantação do Gerenciador de recursos.
   * O fluxo de tráfego de usuários para a VM/aplicativo nas portas esperadas é possível?
4. Seu dispositivo de borda da Internet.
   * As regras de firewall estão em vigor impedindo o tráfego de fluir corretamente?

Para computadores cliente que acessam o aplicativo em uma conexão VPN site a site ou ExpressRoute, as principais áreas que podem causar problemas são o aplicativo e a máquina virtual do Azure.

Para determinar a origem do problema e sua correção, siga estas etapas.

## <a name="step-1-access-application-from-target-vm"></a>Passo 1: Aplicativo de acesso da VM de destino
Tente acessar o aplicativo com o programa cliente apropriado da VM na qual ele está sendo executado. Use o nome do host local, o endereço IP local ou o endereço de loopback (127.0.0.1).

![iniciar o aplicativo diretamente da VM](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Por exemplo, se o aplicativo for um servidor Web, abra um navegador na VM e tente acessar uma página da web hospedada na VM.

Se você puder acessar o aplicativo, vá para a [etapa 2](#step2).

Se você não puder acessar o aplicativo, verifique as seguintes configurações:

* O aplicativo está em execução na máquina virtual de destino.
* O aplicativo está escutando nas portas TCP e UDP esperadas.

Em máquinas virtuais baseadas em Windows e Linux, use o comando **netstat-** a para mostrar as portas de escuta ativas. Examine a saída das portas esperadas nas quais seu aplicativo deve estar escutando. Reinicie o aplicativo ou configure-o para usar as portas esperadas conforme necessário e tente acessar o aplicativo localmente novamente.

## <a id="step2"></a>Etapa 2: Acessar o aplicativo de outra VM na mesma rede virtual
Tente acessar o aplicativo de uma VM diferente, mas na mesma rede virtual, usando o nome de host da VM ou seu endereço IP público, privado ou do provedor atribuído pelo Azure. Para máquinas virtuais criadas usando o modelo de implantação clássico, não use o endereço IP público do serviço de nuvem.

![iniciar o aplicativo de uma VM diferente](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Por exemplo, se o aplicativo for um servidor Web, tente acessar uma página da Web a partir de um navegador em uma VM diferente na mesma rede virtual.

Se você puder acessar o aplicativo, vá para a [etapa 3](#step3).

Se você não puder acessar o aplicativo, verifique as seguintes configurações:

* O Firewall do host na VM de destino está permitindo a solicitação de entrada e o tráfego de resposta de saída.
* O software de detecção de intrusão ou de monitoramento de rede em execução na VM de destino está permitindo o tráfego.
* Os pontos de extremidade dos serviços de nuvem ou os grupos de segurança de rede estão permitindo o tráfego:
  * [Modelo clássico – gerenciar pontos de extremidade de serviços de nuvem](../../cloud-services/cloud-services-enable-communication-role-instances.md)
  * [Modelo do Resource Manager – gerenciar grupos de segurança de rede](../../virtual-network/manage-network-security-group.md)
* Um componente separado em execução na sua VM no caminho entre a VM de teste e sua VM, como um balanceador de carga ou firewall, está permitindo o tráfego.

Em uma máquina virtual baseada no Windows, use o Firewall do Windows com segurança avançada para determinar se as regras de firewall excluem o tráfego de entrada e de saída do aplicativo.

## <a id="step3"></a>Etapa 3: Acessar o aplicativo de fora da rede virtual
Tente acessar o aplicativo de um computador fora da rede virtual como a VM na qual o aplicativo está sendo executado. Use uma rede diferente como seu computador cliente original.

![iniciar o aplicativo de um computador fora da rede virtual](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Por exemplo, se o aplicativo for um servidor Web, tente acessar a página da Web a partir de um navegador em execução em um computador que não esteja na rede virtual.

Se você não puder acessar o aplicativo, verifique as seguintes configurações:

* Para VMs criadas usando o modelo de implantação clássico:
  
  * Verifique se a configuração do ponto de extremidade para a VM está permitindo o tráfego de entrada, especialmente o protocolo (TCP ou UDP) e os números de porta pública e privada.
  * Verifique se as ACLs (listas de controle de acesso) no ponto de extremidade não estão impedindo o tráfego de entrada da Internet.
  * Para obter mais informações, consulte [como configurar pontos de extremidade para uma máquina virtual](../windows/classic/setup-endpoints.md).
* Para VMs criadas usando o modelo de implantação do Resource Manager:
  
  * Verifique se a configuração da regra NAT de entrada para a VM está permitindo o tráfego de entrada, especialmente o protocolo (TCP ou UDP) e os números de porta pública e privada.
  * Verifique se os grupos de segurança de rede estão permitindo a solicitação de entrada e o tráfego de resposta de saída.
  * Para obter mais informações, consulte [o que é um grupo de segurança de rede?](../../virtual-network/security-overview.md)

Se a máquina virtual ou o ponto de extremidade for um membro de um conjunto com balanceamento de carga:

* Verifique se o protocolo de investigação (TCP ou UDP) e o número da porta estão corretos.
* Se o protocolo de investigação e a porta forem diferentes do protocolo e da porta do conjunto de balanceamento de carga:
  * Verifique se o aplicativo está escutando no protocolo de investigação (TCP ou UDP) e no número da porta (use **netstat – a** na VM de destino).
  * Verifique se o Firewall do host na VM de destino está permitindo a solicitação de investigação de entrada e o tráfego de resposta da investigação de saída.

Se você puder acessar o aplicativo, verifique se o dispositivo de borda da Internet está permitindo:

* O aplicativo de saída solicita o tráfego do computador cliente para a máquina virtual do Azure.
* O tráfego de resposta do aplicativo de entrada da máquina virtual do Azure.

## <a name="step-4-if-you-cannot-access-the-application-use-ip-verify-to-check-the-settings"></a>Etapa 4 se você não puder acessar o aplicativo, use a verificação de IP para verificar as configurações. 

Para obter mais informações, consulte [visão geral do monitoramento de rede do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="additional-resources"></a>Recursos adicionais
[Solucionar problemas de conexões Área de Trabalho Remota a uma máquina virtual do Azure baseada no Windows](troubleshoot-rdp-connection.md)

[Solucionar problemas de conexões Secure Shell (SSH) para uma máquina virtual do Azure baseada em Linux](troubleshoot-ssh-connection.md)


