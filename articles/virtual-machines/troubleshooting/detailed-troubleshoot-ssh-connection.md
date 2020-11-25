---
title: Resolução detalhada de problemas do SSH para um VM Azure / Microsoft Docs
description: Etapas de resolução de problemas mais detalhadas do SSH para problemas ligados a uma máquina virtual Azure
keywords: ligação ssh recusou,ssh erro,azure ssh,ligação SSH falhou
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
ms.openlocfilehash: c117f9697299b94a54d9184093e65c56822b8bd2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002634"
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>Passos de resolução de problemas de SSH detalhados relacionados com a ligação a uma VM do Linux no Azure
Existem muitas razões possíveis para que o cliente SSH possa não ser capaz de chegar ao serviço SSH no VM. Se tiver seguido os passos mais [gerais de resolução de problemas do SSH,](troubleshoot-ssh-connection.md)tem de resolver ainda mais o problema da ligação. Este artigo guia-o através de etapas detalhadas de resolução de problemas para determinar onde a ligação SSH está falhando e como resolvê-la.

## <a name="take-preliminary-steps"></a>Tomar medidas preliminares
O diagrama seguinte mostra os componentes envolvidos.

![Diagrama que mostra componentes do serviço SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

Os seguintes passos ajudam-no a isolar a origem da falha e a descobrir soluções ou soluções alternativas.

1. Verifique o estado do VM no portal.
   No [portal Azure,](https://portal.azure.com)selecione o nome VM das **máquinas virtuais**  >  *VM name*.

   O painel de estado para o VM deve mostrar **Running**. Percorra para baixo para mostrar atividade recente para computação, armazenamento e recursos de rede.

2. Selecione **Definições** para examinar pontos finais, endereços IP, grupos de segurança de rede e outras definições.

   O VM deve ter um ponto final definido para o tráfego SSH que pode ver em **Endpoints** ou **[grupo de segurança de rede](../../virtual-network/network-security-groups-overview.md)**. Os pontos finais em VMs que foram criados através do Gestor de Recursos são armazenados num grupo de segurança de rede. Verifique se as regras foram aplicadas ao grupo de segurança da rede e são referenciadas na sub-rede.

Para verificar a conectividade da rede, verifique os pontos finais configurados e veja se pode ligar-se ao VM através de outro protocolo, como HTTP ou outro serviço.

Depois destes passos, tente novamente a ligação SSH.

## <a name="find-the-source-of-the-issue"></a>Encontre a origem da questão
O cliente SSH no seu computador pode não conseguir ligar-se ao serviço SSH no Azure VM devido a problemas ou configurações erradas nas seguintes áreas:

* [Computador cliente SSH](#source-1-ssh-client-computer)
* [Dispositivo de borda da organização](#source-2-organization-edge-device)
* [Lista de controlo de serviços em nuvem e acesso (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Grupos de segurança de rede](#source-4-network-security-groups)
* [Azure VM baseado em Linux](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Fonte 1: Computador cliente SSH
Para eliminar o seu computador como fonte da falha, verifique se pode es fazer ligações SSH a outro computador baseado no Linux.

![Diagrama que destaca componentes de computador cliente SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Se a ligação falhar, verifique se há problemas a seguir no seu computador:

* Uma definição de firewall local que está bloqueando o tráfego SSH de entrada ou saída (TCP 22)
* Software de procuração de clientes instalado localmente que está a impedir ligações SSH
* Software de monitorização de rede instalado localmente que está a impedir ligações SSH
* Outros tipos de software de segurança que monitorizam o tráfego ou permitem/não permitem tipos específicos de tráfego

Se uma destas condições se aplicar, desative temporariamente o software e tente uma ligação SSH a um computador no local para descobrir a razão pela qual a ligação está a ser bloqueada no seu computador. Em seguida, trabalhe com o seu administrador de rede para corrigir as definições de software para permitir ligações SSH.

Se estiver a utilizar a autenticação de certificados, verifique se tem estas permissões para a pasta .ssh no seu diretório de casa:

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/ \* .pub
* Chmod 600 ~/.ssh/id_rsa (ou quaisquer outros ficheiros que tenham as suas chaves privadas armazenadas)
* Chmod 644 ~/.ssh/known_hosts (contém anfitriões a que ligou via SSH)

## <a name="source-2-organization-edge-device"></a>Fonte 2: Dispositivo de borda de organização
Para eliminar o dispositivo de borda da organização como fonte da falha, verifique se um computador diretamente ligado à Internet pode fazer ligações SSH ao seu Azure VM. Se estiver a aceder ao VM sobre uma VPN site-to-site ou uma ligação Azure ExpressRoute, salte para [a Fonte 4: Grupos de segurança da rede](#nsg).

![Diagrama que destaca o dispositivo de borda da organização](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Se não tiver um computador que esteja diretamente ligado à Internet, crie um novo Azure VM no seu próprio grupo de recursos ou serviço de nuvem e utilize esse novo VM. Para obter mais informações, consulte [Criar uma máquina virtual que executa o Linux em Azure.](../linux/quick-create-cli.md) Elimine o grupo de recursos ou o serviço VM e cloud quando terminar os seus testes.

Se conseguir criar uma ligação SSH com um computador que esteja diretamente ligado à Internet, verifique se o dispositivo de borda da organização é:

* Uma firewall interna que está bloqueando o tráfego SSH com a Internet
* Um servidor proxy que está a impedir ligações SSH
* Software de deteção de intrusões ou monitorização de rede em execução em dispositivos na sua rede de arestas que está a impedir ligações SSH

Trabalhe com o seu administrador de rede para corrigir as definições dos dispositivos de borda da sua organização para permitir o tráfego SSH com a Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Fonte 3: Ponto final do serviço em nuvem e ACL

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

> [!NOTE]
> Esta fonte aplica-se apenas aos VMs que foram criados utilizando o modelo de implementação clássico. Para os VMs que foram criados utilizando o Gestor de Recursos, salte para a [fonte 4: Grupos de segurança da rede](#nsg).

Para eliminar o ponto final do serviço de nuvem e a ACL como a fonte da falha, verifique se outro Azure VM na mesma rede virtual pode ligar-se através do SSH.

![Diagrama que destaca o ponto final do serviço em nuvem e a ACL](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Se não tiver outro VM na mesma rede virtual, pode facilmente criar um. Para obter mais informações, consulte [Criar um Linux VM em Azure utilizando o CLI](../linux/quick-create-cli.md). Elimine o VM extra quando terminar os testes.

Se conseguir criar uma ligação SSH com um VM na mesma rede virtual, verifique as seguintes áreas:

* **A configuração do ponto final para o tráfego de SSH no VM alvo.** A porta TCP privada do ponto final deve coincidir com a porta TCP em que o serviço SSH do VM está a ouvir. (A porta predefinido é 22). Verifique o número da porta SSH TCP no portal Azure selecionando **máquinas virtuais**  >  *VM name*  >  **Setpoints**  >  **Endpoints**.
* **O ACL para o ponto final de tráfego SSH na máquina virtual alvo.** Um ACL permite especificar tráfego permitido ou negado a partir da Internet, com base no seu endereço IP de origem. ACLs mal configurados podem impedir a entrada de tráfego SSH para o ponto final. Verifique os seus ACLs para garantir que o tráfego de entrada a partir dos endereços IP públicos do seu servidor de procuração ou de outra borda é permitido. Para obter mais informações, consulte [sobre as listas de controlo de acesso à rede (ACLs)](/previous-versions/azure/virtual-network/virtual-networks-acl).

Para eliminar o ponto final como fonte do problema, retire o ponto final atual, crie outro ponto final e especifique o nome SSH (porta TCP 22 para o número de porta pública e privada). Para obter mais informações, consulte [Configurar pontos finais numa máquina virtual em Azure](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints?toc=/azure/virtual-machines/windows/classic/toc.json).

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>Fonte 4: Grupos de segurança de rede
Os grupos de segurança da rede permitem-lhe ter mais controlo granular do tráfego permitido de entrada e saída. Pode criar regras que abrangem sub-redes e serviços em nuvem numa rede virtual Azure. Verifique as regras do seu grupo de segurança de rede para garantir que o tráfego SSH de e para a Internet é permitido.
Para obter mais informações, consulte [sobre grupos de segurança de rede.](../../virtual-network/network-security-groups-overview.md)

Também pode utilizar IP Verifique para validar a configuração NSG. Para obter mais informações, consulte [a visão geral da monitorização da rede Azure](../../network-watcher/network-watcher-monitoring-overview.md). 

## <a name="source-5-linux-based-azure-virtual-machine"></a>Fonte 5: Máquina virtual Azure baseada em Linux
A última fonte de possíveis problemas é a própria máquina virtual Azure.

![Diagrama que destaca a máquina virtual Azure baseada em Linux](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Se ainda não o fez, siga as instruções [para redefinir uma palavra-passe com máquinas virtuais baseadas em Linux](./reset-password.md).

Tente ligar-se do computador novamente. Se ainda falhar, são algumas das questões possíveis:

* O serviço SSH não está a funcionar na máquina virtual alvo.
* O serviço SSH não está a ouvir na porta TCP 22. Para testar, instale um cliente telnet no seu computador local e execute "telnet *cloudServiceName*.cloudapp.net 22". Este passo determina se a máquina virtual permite a comunicação de entrada e saída para o ponto final SSH.
* A firewall local na máquina virtual alvo tem regras que impedem o tráfego SSH de entrada ou saída.
* O software de deteção de intrusões ou monitorização de rede que está a funcionar na máquina virtual Azure está a impedir ligações SSH.

## <a name="additional-resources"></a>Recursos adicionais
Para obter mais informações sobre o acesso à aplicação de resolução de [problemas, consulte o acesso a uma aplicação em execução numa máquina virtual Azure](./troubleshoot-app-connection.md)