---
title: Resolução detalhada de problemas do SSH para um Azure VM [ Microsoft Docs
description: Passos de resolução de problemas mais detalhados do SSH para problemas de ligação a uma máquina virtual Azure
keywords: ssh conexão recusada,ssh error,azure ssh,SSH ligação falhou
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: ee6d437915f6c87ce9ef5f9c711d90793a96048c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77920132"
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>Detailed SSH troubleshooting steps for issues connecting to a Linux VM in Azure (Passos de resolução de problemas de SSH detalhados relacionados com a ligação a uma VM do Linux no Azure)
Existem muitas razões possíveis para que o cliente SSH possa não ser capaz de chegar ao serviço SSH no VM. Se seguiu as etapas mais gerais de resolução de problemas da [SSH,](troubleshoot-ssh-connection.md)precisa de resolver ainda mais o problema da ligação. Este artigo guia-o através de medidas detalhadas de resolução de problemas para determinar onde a ligação SSH está falhando e como resolvê-la.

## <a name="take-preliminary-steps"></a>Dê passos preliminares
O diagrama seguinte mostra os componentes envolvidos.

![Diagrama que mostra componentes do serviço SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

Os seguintes passos ajudam-no a isolar a origem da falha e a descobrir soluções ou soluções.

1. Verifique o estado do VM no portal.
   No [portal Azure,](https://portal.azure.com)selecione o nome > *VM*das **máquinas virtuais.**

   O painel de estado do VM deve mostrar **Running**. Desça para mostrar atividade recente para computação, armazenamento e recursos de rede.

2. Selecione **Definições** para examinar pontos finais, endereços IP, grupos de segurança de rede e outras definições.

   O VM deve ter um ponto final definido para o tráfego sSH que você pode ver em **Endpoints** ou grupo de **[segurança de rede](../../virtual-network/security-overview.md)**. Os pontos finais em VMs que foram criados através do Recurso Manager são armazenados num grupo de segurança de rede. Verifique se as regras foram aplicadas ao grupo de segurança da rede e são referenciadas na sub-rede.

Para verificar a conectividade da rede, verifique os pontos finais configurados e veja se consegue ligar-se ao VM através de outro protocolo, como http ou outro serviço.

Depois destes passos, tente novamente a ligação SSH.

## <a name="find-the-source-of-the-issue"></a>Encontre a fonte do problema
O cliente SSH no seu computador pode não conseguir ligar-se ao serviço SSH no VM Azure devido a problemas ou configurações erradas nas seguintes áreas:

* [Computador cliente SSH](#source-1-ssh-client-computer)
* [Dispositivo de borda da organização](#source-2-organization-edge-device)
* [Cloud service endpoint e lista de controlo de acesso (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Grupos de segurança de rede](#source-4-network-security-groups)
* [Azure VM baseado em Linux](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Fonte 1: Computador cliente SSH
Para eliminar o seu computador como fonte da falha, verifique se pode fazer ligações SSH a outro computador no local, baseado em Linux.

![Diagrama que destaca componentes de computador do cliente SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Se a ligação falhar, verifique os seguintes problemas no seu computador:

* Uma definição de firewall local que bloqueia o tráfego de Entrada ou Saída sSH (TCP 22)
* Software de procuração de clientes instalado localmente que está a impedir ligações SSH
* Software de monitorização de rede instalado localmente que está a impedir ligações SSH
* Outros tipos de software de segurança que monitorizam o tráfego ou permitem/permitem tipos específicos de tráfego

Se uma destas condições se aplicar, desative temporariamente o software e experimente uma ligação SSH a um computador no local para descobrir a razão pela qual a ligação está a ser bloqueada no seu computador. Em seguida, trabalhe com o seu administrador de rede para corrigir as definições de software para permitir ligações SSH.

Se estiver a utilizar a autenticação do certificado, verifique se tem essas permissões para a pasta .ssh no seu diretório inicial:

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/\*.pub
* Chmod 600 ~/.ssh/id_rsa (ou quaisquer outros ficheiros que tenham as suas chaves privadas armazenadas)
* Chmod 644 ~/.ssh/known_hosts (contém anfitriões aos os dois que ligou via SSH)

## <a name="source-2-organization-edge-device"></a>Fonte 2: Dispositivo de borda da organização
Para eliminar o seu dispositivo de borda de organização como fonte da falha, verifique se um computador ligado diretamente à Internet pode fazer ligações SSH ao seu VM Azure. Se estiver a aceder ao VM através de uma VPN site-to-site ou de uma ligação Azure ExpressRoute, salte para [source 4: Grupos](#nsg)de segurança da rede .

![Diagrama que destaca dispositivo de borda da organização](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Se não tiver um computador que esteja diretamente ligado à Internet, crie um novo Azure VM no seu próprio grupo de recursos ou serviço na nuvem e utilize esse novo VM. Para mais informações, consulte [Criar uma máquina virtual que executa o Linux em Azure](../linux/quick-create-cli.md). Elimine o grupo de recursos ou vM e o serviço de nuvem quando terminar os seus testes.

Se conseguir criar uma ligação SSH com um computador que esteja diretamente ligado à Internet, verifique o dispositivo de borda da sua organização para:

* Uma firewall interna que está bloqueando o tráfego ssh com a Internet
* Um servidor proxy que está a impedir ligações SSH
* Software de deteção de intrusões ou monitorização de rede em execução em dispositivos na sua rede de bordas que está a impedir ligações SSH

Trabalhe com o seu administrador de rede para corrigir as definições dos dispositivos de borda da sua organização para permitir o tráfego ssh com a Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Fonte 3: Ponto final do serviço em nuvem e ACL

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> Esta fonte aplica-se apenas aos VMs que foram criados utilizando o modelo de implementação clássico. Para VMs que foram criados usando O Gestor de Recursos, salte para [fonte 4: Grupos](#nsg)de segurança de rede .

Para eliminar o ponto final do serviço de nuvem e a ACL como fonte da falha, verifique se outro Azure VM na mesma rede virtual pode ligar-se utilizando o SSH.

![Diagrama que destaca o ponto final do serviço de nuvem e ACL](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Se não tiver outro VM na mesma rede virtual, pode facilmente criar um. Para mais informações, consulte [Create a Linux VM on Azure utilizando o CLI](../linux/quick-create-cli.md). Elimine o VM extra quando terminar os seus testes.

Se conseguir criar uma ligação SSH com um VM na mesma rede virtual, verifique as seguintes áreas:

* **A configuração do ponto final para o tráfego ssh no VM alvo.** A porta privada TCP do ponto final deve coincidir com a porta TCP em que o serviço SSH do VM está a ouvir. (A porta predefinida é de 22). Verifique o número da porta SSH TCP no portal Azure selecionando **as máquinas virtuais** > *VM* > **Definições** > de pontos**finais**.
* **O ACL para o ponto final de tráfego SSH na máquina virtual alvo.** Um ACL permite especificar ou negar o tráfego de entrada da Internet, com base no seu endereço IP fonte. Os ACLs mal configurados podem impedir a entrada do tráfego de SSH até ao ponto final. Verifique os seus ACLs para garantir que o tráfego de entrada a partir dos endereços IP públicos do seu proxy ou de outro servidor de borda é permitido. Para mais informações, consulte as listas de controlo de acesso à [rede (ACLs)](../../virtual-network/virtual-networks-acl.md).

Para eliminar o ponto final como fonte do problema, remova o ponto final atual, crie outro ponto final e especifique o nome SSH (porta TCP 22 para o número de porta pública e privada). Para mais informações, consulte [A configuração de pontos finais numa máquina virtual em Azure](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>Fonte 4: Grupos de segurança da rede
Os grupos de segurança da rede permitem-lhe ter um controlo mais granular do tráfego de entrada e saída permitido. Pode criar regras que abrangem subredes e serviços de nuvem numa rede virtual Azure. Verifique as regras do seu grupo de segurança de rede para garantir que o tráfego de SSH de e para a Internet é permitido.
Para mais informações, consulte [sobre grupos de segurança da rede](../../virtual-network/security-overview.md).

Também pode utilizar IP Check para validar a configuração NSG. Para mais informações, consulte a visão geral da [monitorização da rede Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="source-5-linux-based-azure-virtual-machine"></a>Fonte 5: Máquina virtual Azure baseada em Linux
A última fonte de possíveis problemas é a própria máquina virtual Azure.

![Diagrama que destaca a máquina virtual Azure baseada em Linux](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Se ainda não o fez, siga as instruções [para redefinir uma senha de máquinas virtuais baseadas em Linux](../linux/reset-password.md).

Tente ligar-se do computador de novo. Se ainda falhar, seguem-se algumas das questões possíveis:

* O serviço SSH não está a funcionar na máquina virtual alvo.
* O serviço SSH não está a ouvir na porta TCP 22. Para testar, instale um cliente de telnet no seu computador local e execute "telnet *cloudServiceName*.cloudapp.net 22". Este passo determina se a máquina virtual permite a comunicação de entrada e saída ao ponto final do SSH.
* A firewall local na máquina virtual alvo tem regras que estão a impedir o tráfego de SSH de entrada ou saída.
* O software de deteção de intrusões ou monitorização de rede que está a funcionar na máquina virtual Azure está a impedir as ligações SSH.

## <a name="additional-resources"></a>Recursos adicionais
Para obter mais informações sobre o acesso a aplicações de resolução de problemas, consulte [o acesso da Troubleshoot a uma aplicação em execução numa máquina virtual Azure](../linux/troubleshoot-app-connection.md)
