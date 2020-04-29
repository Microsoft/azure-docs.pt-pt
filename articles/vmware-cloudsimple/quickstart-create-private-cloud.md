---
title: 'Quickstart: Criar uma nuvem privada'
titleSuffix: Azure VMware Solutions by CloudSimple
description: Saiba como criar e configurar uma Nuvem Privada com Soluções VMware Azure pela CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a2fe7daf1476f19f6a6eea83174342fa1273fe14
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81867967"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>Quickstart - Configure um ambiente de nuvem privada

Neste artigo, aprenda a criar uma CloudSimple Private Cloud e crie o seu ambiente Private Cloud.

## <a name="before-you-begin"></a>Antes de começar

Rever [os pré-requisitos de networking](cloudsimple-network-checklist.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Criar uma Cloud Privada

Uma Nuvem Privada é uma pilha de VMware isolada que suporta anfitriões ESXi, vCenter, vSAN e NSX.

As Nuvens Privadas são geridas através do portal CloudSimple. Eles têm o seu próprio servidor vCenter no seu próprio domínio de gestão. A pilha funciona em nódos os desdedicados e nos nódosos de hardware de metal isolados.

1. Selecione **Todos os serviços**.
2. Pesquisa por **Serviços CloudSimple**.
3. Selecione o serviço CloudSimple no qual pretende criar a sua Cloud Privada.
4. A partir de **uma visão geral,** clique **em Criar Cloud Privada** para abrir um novo separador de navegador para o portal CloudSimple.  Se solicitado, inscreva-se com o seu sinal Azure em credenciais.  

    ![Criar nuvem privada de Azure](media/create-private-cloud-from-azure.png)

5. No portal CloudSimple, forneça um nome para a sua Nuvem Privada.
6. Selecione a **localização** da sua Nuvem Privada.
7. Selecione **tipo nó,** consistente com o que forprovisionou no Azure.
8. Especificar **contagem do nó**.  Pelo menos três nódosos são necessários para criar uma Nuvem Privada.

    ![Criar Nuvem Privada - Informações básicas](media/create-private-cloud-basic-info.png)

9. Clique **em Seguir: Opções avançadas**.
10. Introduza a gama CIDR para as subredes vSphere/vSAN. Certifique-se de que a gama CIDR não se sobrepõe a nenhuma das suas subredes Azure (redes virtuais) ou com a sub-rede gateway.

    **Opções de gama CIDR:** /24, /23, /22, ou /21. Uma gama DE /24 CIDR suporta até 26 nós, uma gama DE ICDR /23 suporta até 58 nós, e uma gama DE /22 e /21 CIDR suporta 64 nós (o número máximo de nós numa Nuvem Privada).  Para saber mais e VLANs e subredes, consulte [VLANs e sub-redes visão geral](cloudsimple-vlans-subnets.md).

      > [!IMPORTANT]
      > Os endereços IP na gama icdr vSphere/vSAN são reservados para utilização pela infraestrutura Cloud Privada.  Não utilize o endereço IP nesta gama em nenhuma máquina virtual.

11. Clique em **Seguir: Rever e criar**.
12. Reveja as definições. Se precisar de alterar quaisquer definições, clique em **Anterior**.
13. Clique em **Criar**.

O processo de provisionamento da Nuvem Privada começa.  Pode levar até duas horas para a Nuvem Privada ser aprovisionada.

## <a name="launch-cloudsimple-portal"></a>Lançar portal CloudSimple

Pode aceder ao portal CloudSimple a partir do portal Azure.  O portal CloudSimple será lançado com o seu sinal Azure em credenciais utilizando o Single Sign-On (SSO).  Aceder ao portal CloudSimple requer que autorize o pedido de Autorização de **Serviço CloudSimple.**  Para obter mais informações sobre a concessão de permissões, consulte [o consentimento para a aplicação de autorização](access-cloudsimple-portal.md#consent-to-cloudsimple-service-authorization-application)de serviço CloudSimple .

1. Selecione **Todos os serviços**.
2. Pesquisa por **Serviços CloudSimple**.
3. Selecione o serviço CloudSimple no qual pretende criar a sua Cloud Privada.
4. A partir de uma visão geral, clique **em Ir para o portal CloudSimple** para abrir um novo separador de navegador para o portal CloudSimple.  Se solicitado, inscreva-se com o seu sinal Azure em credenciais.  

    ![Lançar portal CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Criar VPN ponto-a-local

Uma ligação VPN Ponto-a-Local é a forma mais simples de se ligar à sua Nuvem Privada a partir do seu computador. Utilize a ligação VPN ponto-a-local se estiver a ligar-se à Nuvem Privada remotamente.  Para um acesso rápido à sua Nuvem Privada, siga os passos abaixo.  O acesso à região CloudSimple a partir da sua rede no local pode ser feito utilizando [vpn local](vpn-gateway.md) ou [Azure ExpressRoute](on-premises-connection.md).

### <a name="create-gateway"></a>Criar porta de entrada

1. Lançar o portal CloudSimple e selecionar **rede**.
2. Selecione **VPN Gateway**.
3. Clique em **Novo Gateway VPN**.

    ![Criar um gateway de VPN](media/create-vpn-gateway.png)

4. Para **a configuração gateway,** especifique as seguintes definições e clique **em Seguinte**.

    * Selecione **VPN ponto-a-local** como o tipo de gateway.
    * Insira um nome para identificar o portal.
    * Selecione a localização Azure onde o seu serviço CloudSimple está implantado.
    * Especifique a sub-rede do cliente para o gateway Ponto-a-Local.  Os endereços DHCP serão dados a partir desta subrede quando se ligar.

5. Para **ligar/utilizador,** especifique as seguintes definições e clique **em Seguinte**.

    * Para permitir automaticamente que todos os utilizadores atuais e futuros acedam à Cloud Privada através deste gateway Ponto-a-Site, selecione **adicionar automaticamente todos os utilizadores**. Quando selecionar esta opção, todos os utilizadores da lista de utilizadores são automaticamente selecionados. Pode anular a opção automática desmarcando utilizadores individuais na lista.
    * Para selecionar apenas utilizadores individuais, clique nas caixas de verificação na lista de utilizadores.

6. A secção VLANs/Subnets permite especificar a gestão e o utilizador VLANs/subnets para o gateway e ligações.

    * As opções **de adicionar automaticamente** definiram a política global para este gateway. As definições aplicam-se ao gateway atual. As definições podem ser ultrapassadas na área **Select.**
    * **Selecione Adicionar gestão VLANs/Subnets de Nuvens Privadas**.
    * Para adicionar todos os VLANs/subredes definidos pelo utilizador, clique em **Adicionar VLANs/Subnets definidos pelo utilizador**.
    * As definições **Select** sobrepõem as definições globais sob **adicionar automaticamente**.

7. Clique em **Seguir** para rever as definições. Clique nos ícones Editar para efazer quaisquer alterações.
8. Clique em **Criar** o gateway VPN.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Ligue-se à CloudSimple usando VPN ponto-a-site

O cliente VPN é necessário para ligar à CloudSimple a partir do seu computador.  Baixe [o cliente OpenVPN](https://openvpn.net/community-downloads/) para Windows ou [Viscosity](https://www.sparklabs.com/viscosity/download/) para macOS e OS X.

1. Lançar o portal CloudSimple e selecionar **rede**.
2. Selecione **VPN Gateway**.
3. A partir da lista de gateways VPN, clique no gateway VPN Ponto-a-Local.
4. Selecione **Utilizadores**.
5. Clique em **Baixar a minha configuração VPN**.

    ![Transferir a configuração da VPN](media/download-p2s-vpn-configuration.png)

6. Importe a configuração do seu cliente VPN.

    * Instruções para [importar configuração no cliente do Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instruções para [importar configuração no macOS ou no OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Ligue-se ao CloudSimple.

## <a name="create-a-vlan-for-your-workload-vms"></a>Crie um VLAN para os seus VMs de carga de trabalho

Depois de criar uma Nuvem Privada, crie um VLAN onde irá implementar os seus VMs de carga de trabalho/aplicação.

1. No portal CloudSimple, selecione **Rede**.
2. Clique em **VLAN/Subnets**.
3. Clique **em Criar VLAN/Subnet**.

    ![Criar VLAN/Subnet](media/create-new-vlan-subnet.png)

4. Selecione a **Nuvem Privada** para a nova VLAN/subnet.
5. Selecione um ID VLAN da lista.  
6. Introduza um nome de sub-rede para identificar a sub-rede.
7. Especifique a gama CIDR da sub-rede e a máscara.  Esta gama não deve sobrepor-se a quaisquer subredes existentes.
8. Clique em **Submeter**.

    ![Criar detalhes VLAN/Subnet](media/create-new-vlan-subnet-details.png)

A VLAN/subnet será criada.  Agora pode usar este ID VLAN para criar um grupo portuário distribuído no seu vCenter Private Cloud.

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Ligue o seu ambiente a uma rede virtual Azure

A CloudSimple fornece-lhe um circuito ExpressRoute para a sua Nuvem Privada. Pode ligar a sua rede virtual no Azure ao circuito ExpressRoute. Para mais detalhes sobre a configuração da ligação, siga os passos na [Ligação de Rede Virtual Azure utilizando](https://docs.microsoft.com/azure/vmware-cloudsimple/cloudsimple-azure-network-connection)a ExpressRoute .

## <a name="sign-in-to-vcenter"></a>Inscreva-se no vCenter

Agora pode iniciar sessão no vCenter para configurar máquinas e políticas virtuais.

1. Para aceder ao vCenter, comece pelo portal CloudSimple. Na página Inicial, em **tarefas comuns,** clique em **Lançar vSphere Client**.  Selecione a Nuvem Privada e, em seguida, clique em **Lançar vSphere Client** na Nuvem Privada.

    ![Lançar cliente vSphere](media/launch-vcenter-from-cloudsimple-portal.png)

2. Selecione o seu cliente vSphere preferido para aceder ao vCenter e iniciar sessão com o seu nome de utilizador e senha.  Os incumprimentos são:
    * Nome do utilizador:`CloudOwner@cloudsimple.local`
    * Palavra-passe: `CloudSimple123!`

Os ecrãs vCenter nos próximos procedimentos são do cliente vSphere (HTML5).

## <a name="change-your-vcenter-password"></a>Altere a sua senha vCenter

O CloudSimple recomenda que altere a sua palavra-passe na primeira vez que iniciar sessão no vCenter.  
A palavra-passe que definir deve satisfazer os seguintes requisitos:

* Duração máxima: A palavra-passe deve ser alterada a cada 365 dias
* Restringir a reutilização: Os utilizadores não podem reutilizar nenhuma das cinco senhas anteriores
* Comprimento: 8 - 20 caracteres
* Personagem especial: Pelo menos um personagem especial
* Caracteres alfabéticos: Pelo menos um personagem maiúsculo, A-Z, e pelo menos um personagem minúsculo, a-z
* Números: Pelo menos um personagem numérico, 0-9
* Máximo idêntico sintetíguo caracteres adjacentes: Três

    Exemplo: CC ou CCC é aceitável como parte da senha, mas cCCC não é.

Se definir uma palavra-passe que não satisfaz os requisitos:

* se utilizar o vSphere Flash Client, reporta um erro
* Se utilizar o cliente HTML5, não reporta um erro. O cliente não aceita a mudança e a velha senha continua a funcionar.

## <a name="access-nsx-manager"></a>Access NSX manager

O gestor NSX é implantado com uma senha padrão. 

* Nome do utilizador: **administrador**
* Palavra-passe: **CloudSimple123!**

Pode encontrar o nome de domínio totalmente qualificado (FQDN) e endereço IP do gestor NSX no portal CloudSimple.

1. Lance o portal CloudSimple e selecione **Recursos.**
2. Clique na Nuvem Privada, que pretende utilizar.
3. Selecione **rede de gestão vSphere**
4. Utilize o endereço FQDN ou IP do **NSX Manager** e conecte-se utilizando um navegador web.

    ![Encontre o gestor da NSX FQDN](media/private-cloud-nsx-manager-fqdn.png)

## <a name="create-a-port-group"></a>Criar um grupo portuário

Para criar um grupo portuário distribuído na vSphere:

1. Siga as instruções em "Adicionar um grupo portuário distribuído" no Guia de [Rede vSphere](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf).
2. Ao configurar o grupo portuário distribuído, forneça o ID VLAN criado em [Create a VLAN para os seus VMs de carga de trabalho](#create-a-vlan-for-your-workload-vms).

## <a name="next-steps"></a>Passos seguintes

* [Consumir VMs de VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* [Ligue-se à rede no local utilizando o Azure ExpressRoute](on-premises-connection.md)
* [Configurar VPN local-a-local a partir do local](vpn-gateway.md)
