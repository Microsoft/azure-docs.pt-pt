---
title: SSH resolução de problemas detalhada para uma VM do Azure | Documentos da Microsoft
description: Obter mais passos para problemas de ligação a uma máquina virtual do Azure de resolução de problemas de SSH
keywords: SSH ligação recusada, ssh erro, azure ssh, ligação de SSH falhou
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: aedf06c5a5e225f0cafb81b17923d6c742da69eb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60506238"
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>SSH detalhada, resolução de problemas de passos para problemas de ligação a uma VM do Linux no Azure
Existem muitas razões possíveis para que o cliente SSH pode não ser capaz de alcançar o serviço SSH na VM. Se tiver acompanhado por meio de mais [SSH geral, passos de resolução de problemas](troubleshoot-ssh-connection.md), precisa resolver o problema de ligação. Este artigo orienta-o pelos passos de resolução de problemas detalhados para determinar em que está a falhar a ligação SSH e como resolvê-lo.

## <a name="take-preliminary-steps"></a>Executar etapas preliminares
O diagrama seguinte mostra os componentes que estão envolvidos.

![Diagrama que mostra os componentes do serviço SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

Os passos seguintes ajudam a isolar a origem da falha e descobrir soluções ou soluções alternativas.

1. Verificar o estado da VM no portal.
   Na [portal do Azure](https://portal.azure.com), selecione **máquinas virtuais** > *nome da VM*.

   O painel de estado para a VM deve mostrar **em execução**. Desloque-se para baixo para mostrar a atividade recente para computação, armazenamento e recursos de rede.

2. Selecione **definições** para examinar os pontos de extremidade, endereços IP, grupos de segurança de rede e outras definições.

   A VM deve ter um ponto final definido para o tráfego SSH que pode ver na **pontos de extremidade** ou  **[grupo de segurança de rede](../../virtual-network/security-overview.md)** . Pontos finais em VMs que foram criadas utilizando o Gestor de recursos são armazenados num grupo de segurança de rede. Certifique-se de que as regras foram aplicadas ao grupo de segurança de rede e são referenciadas na sub-rede.

Para verificar a conectividade de rede, verifique os pontos finais configurados e veja se pode ligar à VM através de outro protocolo, como HTTP ou outro serviço.

Após estes passos, tente novamente a ligação de SSH.

## <a name="find-the-source-of-the-issue"></a>Localizar a origem do problema
O cliente SSH no seu computador pode não conseguir ligar ao serviço SSH na VM do Azure devido a problemas ou configurações incorretas nas seguintes áreas:

* [Computador de cliente SSH](#source-1-ssh-client-computer)
* [Dispositivo de limite de organização](#source-2-organization-edge-device)
* [Ponto final de serviço de nuvem e de acesso (ACL) de lista de controlo](#source-3-cloud-service-endpoint-and-acl)
* [Grupos de segurança de rede](#source-4-network-security-groups)
* [Baseado em Linux VM do Azure](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Origem 1: Computador de cliente SSH
Para eliminar o seu computador como a origem da falha, certifique-se de que ele possa fazer ligações SSH para o outro local, computador baseado em Linux.

![Diagrama realça os componentes de computador do cliente SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Se a ligação falhar, verifique os seguintes problemas no seu computador:

* Uma definição de local firewall que está a bloquear o tráfego SSH de entrada ou saído (TCP 22)
* Software de proxy de cliente que está a impedir ligações SSH instalado localmente
* Localmente instalado software que está a impedir ligações SSH de monitorização de rede
* Outros tipos de software de segurança que monitorizar o tráfego de ou para permitirem/não permitam tipos específicos de tráfego

Se uma das seguintes condições se aplicarem, temporariamente desative o software e tente uma ligação SSH a um computador no local para descobrir o motivo pelo qual que a ligação está a ser bloqueada no seu computador. Em seguida, contactar o administrador de rede para corrigir as definições de software para permitir ligações SSH.

Se estiver a utilizar autenticação de certificados, certifique-se de que tem estas permissões para a pasta. SSH no diretório raiz:

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/\*. pub
* Chmod 600 ~/.ssh/id_rsa (ou outros ficheiros que tenham as chaves privadas armazenadas nas mesmas)
* ~/.Ssh/known_hosts chmod 644 (contém anfitriões que se ligar à através de SSH)

## <a name="source-2-organization-edge-device"></a>Origem 2: Dispositivo de limite de organização
Para eliminar o seu dispositivo de borda da organização como a origem da falha, certifique-se de que um computador diretamente ligado à Internet pode efetuar ligações SSH à VM do Azure. Se estiver a aceder a VM através de uma VPN de site a site ou uma ligação do ExpressRoute do Azure, avance para o [4 de origem: Grupos de segurança de rede](#nsg).

![Diagrama que destaca o dispositivo de limite de organização](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Se não tiver um computador que está diretamente ligado à Internet, criar uma nova VM do Azure no seu próprio grupo de recursos ou serviço em nuvem e utilizar essa nova VM. Para obter mais informações, consulte [criar uma máquina virtual com Linux no Azure](../linux/quick-create-cli.md). Elimine o grupo de recursos ou o serviço de nuvem e de VM quando tiver terminado com os seus testes.

Se pode criar uma ligação SSH com um computador que está diretamente ligado à Internet, verifique o seu dispositivo do edge de organização para:

* Um firewall interno que está a bloquear o tráfego SSH com a Internet
* Um servidor proxy que está a impedir ligações SSH
* Detecção de intrusão ou de software em execução em dispositivos na sua rede de borda que está a impedir ligações SSH de monitorização de rede

Contactar o administrador de rede para corrigir as definições dos seus dispositivos de borda da organização para permitir o tráfego SSH com a Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Origem de 3: Ponto final de serviço de nuvem e de ACL
> [!NOTE]
> Esta origem de só se aplica a VMs que foram criadas utilizando o modelo de implementação clássica. Para as VMs que foram criadas utilizando o Gestor de recursos, avance para o [4 de origem: Grupos de segurança de rede](#nsg).

Para eliminar o ponto final de serviço de nuvem e a ACL como a origem da falha, certifique-se de que outra VM do Azure na mesma rede virtual pode ligar-se através de SSH.

![Diagrama que destaca o ponto final de serviço de nuvem e de ACL](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Se não tiver outra VM na mesma rede virtual, pode facilmente criar um. Para obter mais informações, consulte [criar uma VM do Linux no Azure com a CLI](../linux/quick-create-cli.md). Elimine a VM extra quando tiver terminado com os seus testes.

Se pode criar uma ligação SSH com uma VM na mesma rede virtual, verifique as seguintes áreas:

* **A configuração de ponto final para o tráfego SSH na VM de destino.** A porta TCP privada do ponto de extremidade deve corresponder a porta TCP no qual o serviço SSH na VM está a escutar. (A porta predefinida é de 22). Verifique se o número da porta TCP de SSH no portal do Azure, selecionando **máquinas virtuais** > *nome da VM* > **definições**  >   **Pontos de extremidade**.
* **A ACL para o ponto de final de tráfego SSH na máquina de virtual de destino.** Uma ACL permite-lhe especificar permitido ou negado o tráfego de entrada a partir da Internet, com base no respetivo endereço IP de origem. ACLs mal configuradas podem impedir que o tráfego SSH de entrada para o ponto final. Verifique suas ACLs para se certificar de que o tráfego de entrada dos endereços IP públicos do seu proxy ou outro servidor de borda é permitido. Para obter mais informações, consulte [sobre o acesso de rede (ACLs) de listas de controle](../../virtual-network/virtual-networks-acl.md).

Para eliminar o ponto de extremidade como uma origem do problema, remova o ponto final atual, criar outro ponto final e especifique o nome SSH (porta TCP 22 para o número de porta pública e privada). Para obter mais informações, consulte [configurar pontos finais numa máquina virtual no Azure](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>Origem de 4: Grupos de segurança de rede
Grupos de segurança de rede permitem-lhe ter um controle mais granular do tráfego de entrada e saído permitido. Pode criar regras que abrangem a sub-redes e serviços em nuvem na rede virtual do Azure. Verifique as suas regras de grupo de segurança de rede para se certificar de que o tráfego SSH e para a Internet é permitido.
Para obter mais informações, consulte [sobre os grupos de segurança de rede](../../virtual-network/security-overview.md).

Também pode utilizar o IP verificar para validar a configuração de NSG. Para obter mais informações, consulte [descrição geral da monitorização de rede do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="source-5-linux-based-azure-virtual-machine"></a>Origem 5: Baseado em Linux máquina virtual do Azure
A última fonte possíveis problemas é a máquina virtual do Azure em si.

![Diagrama que destaca baseado em Linux máquina virtual do Azure](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Se ainda não fez isso, siga as instruções [para repor uma palavra-passe baseado em Linux as máquinas virtuais](../linux/reset-password.md).

Tente ligar novamente a partir do seu computador. Se continuar a falhar, seguem-se alguns dos problemas possíveis:

* O serviço SSH não está em execução na máquina de virtual de destino.
* O serviço SSH não está a escutar a porta TCP 22. Para testar, instalar o cliente telnet em seu computador local e executar "telnet *cloudServiceName*. cloudapp.net 22". Este passo determina se a máquina virtual permite a comunicação de entrada e saída para o ponto final SSH.
* O firewall local na máquina de virtual de destino tem regras que estão a impedir o tráfego SSH de entrada ou saído.
* Detecção de intrusão ou de software que está em execução na máquina virtual do Azure de monitorização de rede está a impedir ligações SSH.

## <a name="additional-resources"></a>Recursos adicionais
Para obter mais informações sobre resolução de problemas de acesso da aplicação, consulte [resolver problemas de acesso para uma aplicação em execução numa máquina virtual do Azure](../linux/troubleshoot-app-connection.md)
