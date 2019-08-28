---
title: Solução de problemas de SSH detalhada para uma VM do Azure | Microsoft Docs
description: Etapas de solução de problemas de SSH mais detalhadas para problemas de conexão a uma máquina virtual do Azure
keywords: conexão SSH recusada, erro de SSH, Azure SSH, falha na conexão SSH
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: gwallace
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 41265973df21be289e63cbd6ed2703febc50cff2
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70090502"
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>Etapas detalhadas de solução de problemas de SSH para problemas de conexão a uma VM Linux no Azure
Há muitos motivos possíveis para que o cliente SSH não consiga acessar o serviço SSH na VM. Se você tiver seguido as etapas de [solução de problemas de SSH mais gerais](troubleshoot-ssh-connection.md), será necessário solucionar o problema de conexão. Este artigo orienta você pelas etapas de solução de problemas detalhadas para determinar onde a conexão SSH está falhando e como resolvê-la.

## <a name="take-preliminary-steps"></a>Executar etapas preliminares
O diagrama a seguir mostra os componentes envolvidos.

![Diagrama que mostra os componentes do serviço SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

As etapas a seguir ajudam a isolar a origem da falha e descobrir soluções ou soluções alternativas.

1. Verifique o status da VM no Portal.
   Na [portal do Azure](https://portal.azure.com), selecione **máquinas** > virtuais nome da*VM*.

   O painel de status da VM deve mostrar **em execução**. Role para baixo para mostrar a atividade recente dos recursos de computação, armazenamento e rede.

2. Selecione **configurações** para examinar pontos de extremidade, endereços IP, grupos de segurança de rede e outras configurações.

   A VM deve ter um ponto de extremidade definido para o tráfego SSH que você pode exibir em **pontos** de extremidade ou **[grupo de segurança de rede](../../virtual-network/security-overview.md)** . Os pontos de extremidade em VMs que foram criados usando o Resource Manager são armazenados em um grupo de segurança de rede. Verifique se as regras foram aplicadas ao grupo de segurança de rede e são referenciadas na sub-rede.

Para verificar a conectividade de rede, verifique os pontos de extremidade configurados e veja se você pode se conectar à VM por meio de outro protocolo, como HTTP ou outro serviço.

Após essas etapas, tente a conexão SSH novamente.

## <a name="find-the-source-of-the-issue"></a>Localizar a origem do problema
O cliente SSH em seu computador pode falhar ao se conectar ao serviço SSH na VM do Azure devido a problemas ou configurações incorretas nas seguintes áreas:

* [Computador cliente SSH](#source-1-ssh-client-computer)
* [Dispositivo de borda da organização](#source-2-organization-edge-device)
* [Ponto de extremidade do serviço de nuvem e lista de controle de acesso (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Grupos de segurança de rede](#source-4-network-security-groups)
* [VM do Azure baseada em Linux](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Fonte 1: Computador cliente SSH
Para eliminar o computador como a origem da falha, verifique se ele pode fazer conexões SSH com outro computador local baseado em Linux.

![Diagrama que realça os componentes do computador cliente SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Se a conexão falhar, verifique os seguintes problemas no computador:

* Uma configuração de firewall local que está bloqueando o tráfego SSH de entrada ou de saída (TCP 22)
* Software proxy de cliente instalado localmente que está impedindo conexões SSH
* Software de monitoramento de rede instalado localmente que está impedindo conexões SSH
* Outros tipos de software de segurança que monitoram o tráfego ou permitem/proíbe tipos específicos de tráfego

Se uma dessas condições se aplicar, Desabilite temporariamente o software e tente uma conexão SSH com um computador local para descobrir o motivo pelo qual a conexão está sendo bloqueada no computador. Em seguida, trabalhe com o administrador de rede para corrigir as configurações de software para permitir conexões SSH.

Se você estiver usando a autenticação de certificado, verifique se você tem essas permissões para a pasta. ssh em seu diretório base:

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/\*. pub
* Chmod 600 ~/.ssh/id_rsa (ou qualquer outro arquivo que tenha suas chaves privadas armazenadas neles)
* Chmod 644 ~/.ssh/known_hosts (contém hosts aos quais você se conectou via SSH)

## <a name="source-2-organization-edge-device"></a>Fonte 2: Dispositivo de borda da organização
Para eliminar o dispositivo de borda da organização como a origem da falha, verifique se um computador conectado diretamente à Internet pode fazer conexões SSH com a VM do Azure. Se você estiver acessando a VM em uma VPN site a site ou uma conexão do Azure ExpressRoute, pule [para a fonte 4: Grupos](#nsg)de segurança de rede.

![Diagrama que realça o dispositivo de borda da organização](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Se você não tiver um computador conectado diretamente à Internet, crie uma nova VM do Azure em seu próprio grupo de recursos ou serviço de nuvem e use essa nova VM. Para obter mais informações, consulte [criar uma máquina virtual executando o Linux no Azure](../linux/quick-create-cli.md). Exclua o grupo de recursos ou a VM e o serviço de nuvem quando terminar o teste.

Se você puder criar uma conexão SSH com um computador conectado diretamente à Internet, verifique o dispositivo de borda da organização para:

* Um firewall interno que está bloqueando o tráfego SSH com a Internet
* Um servidor proxy que está impedindo conexões SSH
* Software de detecção de intrusão ou monitoramento de rede em execução em dispositivos na sua rede de borda que está impedindo conexões SSH

Trabalhe com o administrador de rede para corrigir as configurações dos dispositivos de borda da sua organização para permitir o tráfego SSH com a Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Fonte 3: Ponto de extremidade de serviço de nuvem e ACL
> [!NOTE]
> Essa origem se aplica somente a VMs que foram criadas usando o modelo de implantação clássico. Para VMs que foram criadas usando o Resource Manager, pule para [a fonte 4: Grupos](#nsg)de segurança de rede.

Para eliminar o ponto de extremidade do serviço de nuvem e a ACL como a origem da falha, verifique se outra VM do Azure na mesma rede virtual pode se conectar usando SSH.

![Diagrama que realça o ponto de extremidade do serviço de nuvem e ACL](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Se você não tiver outra VM na mesma rede virtual, poderá facilmente criar uma. Para obter mais informações, consulte [criar uma VM do Linux no Azure usando a CLI](../linux/quick-create-cli.md). Exclua a VM extra quando terminar seu teste.

Se você puder criar uma conexão SSH com uma VM na mesma rede virtual, verifique as seguintes áreas:

* **A configuração do ponto de extremidade para o tráfego SSH na VM de destino.** A porta TCP privada do ponto de extremidade deve corresponder à porta TCP na qual o serviço SSH na VM está escutando. (A porta padrão é 22). Verifique o número da porta SSH TCP no portal do Azure selecionando **máquinas** > virtuais*nome* > da VM**configurações** > **pontos de extremidade**.
* **A ACL para o ponto de extremidade de tráfego SSH na máquina virtual de destino.** Uma ACL permite que você especifique o tráfego de entrada permitido ou negado da Internet, com base em seu endereço IP de origem. ACLs configuradas incorretamente podem impedir o tráfego de SSH de entrada para o ponto de extremidade. Verifique suas ACLs para garantir que o tráfego de entrada dos endereços IP públicos do seu proxy ou de outro servidor de borda seja permitido. Para obter mais informações, consulte [sobre ACLs (listas de controle de acesso) de rede](../../virtual-network/virtual-networks-acl.md).

Para eliminar o ponto de extremidade como uma origem do problema, remova o ponto de extremidade atual, crie outro ponto de extremidade e especifique o nome do SSH (porta TCP 22 para o número da porta pública e privada). Para obter mais informações, consulte [Configurar pontos de extremidade em uma máquina virtual no Azure](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>Fonte 4: Grupos de segurança de rede
Os grupos de segurança de rede permitem que você tenha um controle mais granular do tráfego de entrada e de saída permitido. Você pode criar regras que abrangem sub-redes e serviços de nuvem em uma rede virtual do Azure. Verifique as regras do grupo de segurança de rede para garantir que o tráfego SSH de e para a Internet seja permitido.
Para obter mais informações, consulte [sobre grupos de segurança de rede](../../virtual-network/security-overview.md).

Você também pode usar a verificação de IP para validar a configuração do NSG. Para obter mais informações, consulte [visão geral do monitoramento de rede do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="source-5-linux-based-azure-virtual-machine"></a>Fonte 5: Máquina virtual do Azure baseada em Linux
A última fonte de possíveis problemas é a própria máquina virtual do Azure.

![Diagrama que realça a máquina virtual do Azure baseada em Linux](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Se você ainda não tiver feito isso, siga as instruções [para redefinir uma senha de máquinas virtuais baseadas em Linux](../linux/reset-password.md).

Tente se conectar do computador novamente. Se ainda falhar, veja a seguir alguns dos possíveis problemas:

* O serviço SSH não está em execução na máquina virtual de destino.
* O serviço SSH não está escutando na porta TCP 22. Para testar, instale um cliente Telnet no computador local e execute "Telnet *cloudServiceName*. cloudapp.net 22". Esta etapa determina se a máquina virtual permite a comunicação de entrada e saída para o ponto de extremidade SSH.
* O firewall local na máquina virtual de destino tem regras que estão impedindo o tráfego SSH de entrada ou de saída.
* O software de detecção de intrusão ou de monitoramento de rede em execução na máquina virtual do Azure está impedindo conexões SSH.

## <a name="additional-resources"></a>Recursos adicionais
Para obter mais informações sobre como solucionar problemas de acesso a aplicativos, consulte [solucionar problemas de acesso a um aplicativo em execução em uma máquina virtual do Azure](../linux/troubleshoot-app-connection.md)
