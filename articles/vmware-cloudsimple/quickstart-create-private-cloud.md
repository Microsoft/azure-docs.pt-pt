---
title: Soluções Azure VMware (AVS) Quickstart - Criar uma Nuvem Privada AVS
description: Saiba como criar e configurar uma Nuvem Privada AVS com Soluções Azure VMware (AVS)
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cafcf04dac0542f1506980d8b9484b82b558e100
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77018572"
---
# <a name="quickstart---configure-an-avs-private-cloud-environment"></a>Quickstart - Configure um ambiente de nuvem privada AVS

Neste artigo, aprenda a criar uma Nuvem Privada AVS e crie o seu ambiente AVS Private Cloud.

## <a name="before-you-begin"></a>Antes de começar

Examine os [pré-requisitos de rede](cloudsimple-network-checklist.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-avs-private-cloud"></a>Criar uma Nuvem Privada AVS

Uma Nuvem Privada AVS é uma pilha de VMware isolada que suporta anfitriões ESXi, vCenter, vSAN e NSX.

As Nuvens Privadas AVS são geridas através do portal AVS. Eles têm seu próprio servidor vCenter em seu próprio domínio de gerenciamento. A pilha é executada em nós dedicados e nós de hardware bare-metal isolados.

1. Selecione **Todos os serviços**.
2. Pesquisa de **Serviços AVS**.
3. Selecione o serviço AVS no qual pretende criar a sua Nuvem Privada AVS.
4. A partir de **uma visão geral,** clique **em Criar Nuvem Privada AVS** para abrir um novo separador de navegador para portal AVS. Se solicitado, entre com suas credenciais de entrada do Azure. 

    ![Criar nuvem privada AVS a partir de Azure](media/create-private-cloud-from-azure.png)

5. No portal AVS, forneça um nome para a sua Nuvem Privada AVS.
6. Selecione a **localização** da sua Nuvem Privada AVS.
7. Selecione o **tipo de nó**, consistente com o que você provisionou no Azure.
8. Especifique a **contagem de nós**. Pelo menos três nódosos são necessários para criar uma Nuvem Privada AVS.

    ![Criar Nuvem Privada AVS - Informações básicas](media/create-private-cloud-basic-info.png)

9. Clique em **Avançar: opções avançadas**.
10. Insira o intervalo CIDR para sub-redes vSphere/vSAN. Certifique-se de que o intervalo CIDR não se sobreponha a nenhuma das suas sub-redes do Azure locais ou outras (redes virtuais) ou com a sub-rede de gateway.

    **Opções de intervalo CIDR:** /24,/23,/22 ou/21. Uma gama DE /24 CIDR suporta até 26 nós, uma gama DE ICDR /23 suporta até 58 nós, e uma gama DE /22 e /21 CIDR suporta 64 nós (o número máximo de nós numa Nuvem Privada AVS). Para saber mais e VLANs e sub-redes, consulte [visão geral de VLANs e sub-redes](cloudsimple-vlans-subnets.md).

      > [!IMPORTANT]
      > Os endereços IP na gama vSphere/vSAN CIDR são reservados para utilização pela infraestrutura AVS Private Cloud. Não use o endereço IP nesse intervalo em qualquer máquina virtual.

11. Clique em **Avançar: examinar e criar**.
12. Examine as configurações. Se você precisar alterar as configurações, clique em **anterior**.
13. Clique em **Criar**.

Inicia-se o processo de provisionamento da Nuvem Privada AVS. Pode levar até duas horas para que a Nuvem Privada AVS seja aprovisionada.

## <a name="launch-avs-portal"></a>Lançar portal AVS

Pode aceder ao portal AVS a partir do portal Azure. O portal AVS será lançado com o seu sinal Azure em credenciais utilizando o Single Sign-On (SSO). O acesso ao portal AVS requer que autorize o pedido de Autorização de **Serviço DaVs.** Para obter mais informações sobre a concessão de permissões, consulte o pedido de Autorização de [Serviço Da AVS.](access-cloudsimple-portal.md#consent-to-avs-service-authorization-application)

1. Selecione **Todos os serviços**.
2. Pesquisa de **Serviços AVS**.
3. Selecione o serviço AVS no qual pretende criar a sua Nuvem Privada AVS.
4. A partir de uma visão geral, clique **em Ir ao portal AVS** para abrir um novo separador de navegador para portal AVS. Se solicitado, entre com suas credenciais de entrada do Azure. 

    ![Lançar portal AVS](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Criar VPN ponto a site

Uma ligação VPN Ponto-a-Local é a forma mais simples de se ligar à sua Nuvem Privada AVS a partir do seu computador. Utilize a ligação VPN ponto-a-local se estiver a ligar-se remotamente à Nuvem Privada AVS. Para um acesso rápido à sua Nuvem Privada AVS, siga os passos abaixo. O acesso à região AVS a partir da sua rede no local pode ser feito utilizando [vpn local](vpn-gateway.md) ou [Azure ExpressRoute](on-premises-connection.md).

### <a name="create-gateway"></a>Criar gateway

1. Lançar portal AVS e selecionar **Rede**.
2. Selecione **Gateway de VPN**.
3. Clique em **novo gateway de VPN**.

    ![Criar um gateway de VPN](media/create-vpn-gateway.png)

4. Para **configuração de gateway**, especifique as seguintes configurações e clique em **Avançar**.

    * Selecione **VPN ponto a site** como o tipo de gateway.
    * Insira um nome para identificar o gateway.
    * Selecione a localização Azure onde o seu serviço AVS está implantado.
    * Especifique a sub-rede do cliente para o gateway ponto a site. Os endereços DHCP serão fornecidos dessa sub-rede quando você se conectar.

5. Para **conexão/usuário**, especifique as seguintes configurações e clique em **Avançar**.

    * Para permitir automaticamente que todos os utilizadores atuais e futuros acedam à Nuvem Privada AVS através deste gateway Ponto-a-Site, selecione **adicionar automaticamente todos os utilizadores**. Quando você seleciona essa opção, todos os usuários na lista de usuários são selecionados automaticamente. Você pode substituir a opção automática desmarcando usuários individuais na lista.
    * Para selecionar somente usuários individuais, clique nas caixas de seleção na lista de usuários.

6. A seção VLANs/sub-redes permite especificar VLANs de gerenciamento e de usuário/sub-redes para o gateway e as conexões.

    * As opções **adicionar automaticamente** definem a política global para esse gateway. As configurações se aplicam ao gateway atual. As configurações podem ser substituídas na área de **seleção** .
    * **Selecione Adicionar gestão VLANs/Subnets de Nuvens Privadas AVS**.
    * Para adicionar todas as VLANs/sub-redes definidas pelo usuário, clique em **Adicionar VLANs/sub-redes definidas pelo usuário**.
    * As configurações de **selecionar** substituem as configurações globais em **adicionar automaticamente**.

7. Clique em **Avançar** para revisar as configurações. Clique nos ícones de edição para fazer alterações.
8. Clique em **criar** para criar o gateway de VPN.

### <a name="connect-to-avs-using-point-to-site-vpn"></a>Ligue-se a AVS utilizando VPN ponto-a-local

O cliente VPN é necessário para ligar a AVS a partir do seu computador. Baixe o [openvpn client](https://openvpn.net/community-downloads/) para Windows ou [viscosity](https://www.sparklabs.com/viscosity/download/) para MacOS e os X.

1. Lançar portal AVS e selecionar **Rede**.
2. Selecione **Gateway de VPN**.
3. Na lista de gateways de VPN, clique no gateway de VPN ponto a site.
4. Selecione **Utilizadores**.
5. Clique em **baixar minha configuração de VPN**.

    ![Transferir a configuração da VPN](media/download-p2s-vpn-configuration.png)

6. Importe a configuração em seu cliente VPN.

    * Instruções para [importar a configuração no cliente Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instruções para [importar a configuração no MacOS ou no os X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Ligue-se ao AVS.

## <a name="create-a-vlan-for-your-workload-vms"></a>Criar uma VLAN para suas VMs de carga de trabalho

Depois de criar uma Nuvem Privada AVS, crie um VLAN onde irá implementar os seus VMs de carga de trabalho/aplicação.

1. No portal AVS, selecione **Rede**.
2. Clique em **VLAN/sub-redes**.
3. Clique em **criar VLAN/sub-rede**.

    ![Criar VLAN/sub-rede](media/create-new-vlan-subnet.png)

4. Selecione a **Nuvem Privada AVS** para a nova VLAN/subnet.
5. Selecione uma ID de VLAN na lista. 
6. Insira um nome de sub-rede para identificar a sub-rede.
7. Especifique o intervalo e a máscara de CIDR da sub-rede. Esse intervalo não deve se sobrepor a nenhuma sub-rede existente.
8. Clique em **Submit** (Submeter).

    ![Criar detalhes de VLAN/sub-rede](media/create-new-vlan-subnet-details.png)

A VLAN/sub-rede será criada. Agora pode usar este ID VLAN para criar um grupo portuário distribuído no seu VCenter De Nuvem Privada AVS.

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Conectar seu ambiente a uma rede virtual do Azure

A AVS fornece-lhe um circuito ExpressRoute para a sua Nuvem Privada AVS. Você pode conectar sua rede virtual no Azure ao circuito do ExpressRoute. Para obter detalhes completos sobre como configurar a conexão, siga as etapas em [conexão de rede virtual do Azure usando o ExpressRoute](https://docs.azure.cloudsimple.com/cloudsimple-azure-network-connection/).

## <a name="sign-in-to-vcenter"></a>Entrar no vCenter

Agora você pode entrar no vCenter para configurar máquinas virtuais e políticas.

1. Para aceder ao vCenter, comece pelo portal AVS. Na Home Page, em **tarefas comuns**, clique em **Iniciar vSphere cliente**. Selecione a Nuvem Privada AVS e, em seguida, clique em **Lançar vSphere Client** na Nuvem Privada AVS.

    ![Iniciar cliente do vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

2. Selecione seu cliente vSphere preferencial para acessar o vCenter e entrar com seu nome de usuário e senha. Os padrões são:
    * Nome do utilizador: **CloudOwner@AVS.local**
    * Senha: **AVS123!**  

As telas do vCenter nos próximos procedimentos são do cliente vSphere (HTML5).

## <a name="change-your-vcenter-password"></a>Alterar sua senha do vCenter

O AVS recomenda que altere a sua palavra-passe na primeira vez que iniciar sessão no vCenter. A senha que você definir deve atender aos seguintes requisitos:

* Tempo de vida máximo: a senha deve ser alterada a cada 365 dias
* Restringir reutilização: os usuários não podem reutilizar nenhuma das cinco senhas anteriores
* Comprimento: 8-20 caracteres
* Caractere especial: pelo menos um caractere especial
* Caracteres alfabéticos: pelo menos um caractere maiúsculo, A-Z e, pelo menos, um caractere minúsculo, a-z
* Números: pelo menos um caractere numérico, 0-9
* Máximo de caracteres adjacentes idênticos: três

    Exemplo: CC ou CCC é aceitável como parte da senha, mas CCCC não é.

Se você definir uma senha que não atenda aos requisitos:

* Se você usar o cliente vSphere flash, ele relatará um erro
* Se você usar o cliente HTML5, ele não relatará um erro. O cliente não aceita a alteração e a senha antiga continua a funcionar.

## <a name="access-nsx-manager"></a>Acessar o Gerenciador do NSX

O NSX Manager é implantado com uma senha padrão. 

* Nome de usuário: **administrador**
* Senha: **AVS123!**

Pode encontrar o nome de domínio totalmente qualificado (FQDN) e o endereço IP do gestor NSX no portal AVS.

1. Lance o portal AVS e selecione **Recursos.**
2. Clique na Nuvem Privada AVS, que pretende utilizar.
3. Selecionar **rede de gerenciamento do vSphere**
4. Use o FQDN ou o endereço IP do **NSX Manager** e conecte-se usando um navegador da Web.

    ![Localizar FQDN do NSX Manager](media/private-cloud-nsx-manager-fqdn.png)

## <a name="create-a-port-group"></a>Criar um grupo de portas

Para criar um grupo de portas distribuídas no vSphere:

1. Siga as instruções em "adicionar um grupo de portas distribuídas" no [Guia de rede do vSphere](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf).
2. Ao configurar o grupo de portas distribuídas, forneça a ID de VLAN criada em [criar uma VLAN para suas VMs de carga de trabalho](#create-a-vlan-for-your-workload-vms).

## <a name="next-steps"></a>Passos seguintes

* [Consumir VMs VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* [Conectar-se à rede local usando o Azure ExpressRoute](on-premises-connection.md)
* [Configurar VPN site a site a partir do local](vpn-gateway.md)
