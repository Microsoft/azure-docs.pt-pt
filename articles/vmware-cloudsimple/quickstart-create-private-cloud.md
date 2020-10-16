---
title: 'Quickstart: Criar uma nuvem privada'
titleSuffix: Azure VMware Solutions by CloudSimple
description: Saiba como criar e configurar uma Nuvem Privada com Soluções VMware Azure by CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 487308aca3231650aee3fac5ae127006649e19b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87073906"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>Quickstart - Configurar um ambiente private Cloud

Neste artigo, aprenda a criar uma CloudSimple Private Cloud e crie o seu ambiente Private Cloud.

## <a name="before-you-begin"></a>Antes de começar

Rever [pré-requisitos de rede.](cloudsimple-network-checklist.md)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Criar uma Cloud Privada

A Private Cloud é uma pilha de VMware isolada que suporta anfitriões ESXi, vCenter, vSAN e NSX.

Nuvens Privadas são geridas através do portal CloudSimple. Têm o seu próprio servidor vCenter no seu próprio domínio de gestão. A pilha funciona com nós dedicados e nós de hardware de metal nu isolados.

1. Selecione **Todos os serviços**.
2. Procure por **Serviços CloudSimple**.
3. Selecione o serviço CloudSimple no qual pretende criar a sua Cloud Privada.
4. A partir **do resumo,** clique em **Criar Cloud Privada** para abrir um novo separador de navegador para o portal CloudSimple.  Se solicitado, inscreva-se com o seu sinal de Azure em credenciais.  

    ![Criar Nuvem Privada a partir de Azure](media/create-private-cloud-from-azure.png)

5. No portal CloudSimple, forneça um nome para a sua Nuvem Privada.
6. Selecione a **localização** da sua Nuvem Privada.
7. Selecione **o tipo de nó,** consistente com o que provisionou no Azure.
8. Especificar **a contagem de nós**.  Pelo menos três nós são necessários para criar uma Nuvem Privada.

    ![Criar Nuvem Privada - Informações básicas](media/create-private-cloud-basic-info.png)

9. Clique **em seguida: Opções avançadas**.
10. Introduza a gama CIDR para sub-redes vSphere/vSAN. Certifique-se de que a gama CIDR não se sobrepõe a nenhuma das suas sub-redes Azure (redes virtuais) ou com a sub-rede gateway.

    **Opções de gama CIDR:** /24, /23, /22, ou /21. Uma gama CIDR /24 suporta até 26 nós, uma gama CIDR /23 suporta até 58 nós, e uma gama CIDR /22 e /21 suporta 64 nós (o número máximo de nós numa Nuvem Privada).  Para saber mais e VLANs e sub-redes, consulte [VLANs e sub-redes.](cloudsimple-vlans-subnets.md)

      > [!IMPORTANT]
      > Os endereços IP na gama vSphere/vSAN CIDR são reservados para utilização pela infraestrutura Private Cloud.  Não utilize o endereço IP nesta gama em qualquer máquina virtual.

11. Clique **em seguida: Reveja e crie**.
12. Reveja as definições. Se precisar de alterar quaisquer definições, clique em **Anterior**.
13. Clique em **Criar**.

O processo de provisionamento de nuvens privadas começa.  Pode levar até duas horas para que a Nuvem Privada seja a provisionada.

## <a name="launch-cloudsimple-portal"></a>Lançar portal CloudSimple

Pode aceder ao portal CloudSimple a partir do portal Azure.  O portal CloudSimple será lançado com o seu sinal de Azure em credenciais utilizando Sign-On Single (SSO).  Aceder ao portal CloudSimple requer que autorize a aplicação **de Autorização de Serviço CloudSimple.**  Para obter mais informações sobre a concessão de permissões, consulte [o pedido de Autorização de Serviço Consent to CloudSimple](access-cloudsimple-portal.md#consent-to-cloudsimple-service-authorization-application).

1. Selecione **Todos os serviços**.
2. Procure por **Serviços CloudSimple**.
3. Selecione o serviço CloudSimple no qual pretende criar a sua Cloud Privada.
4. A partir de uma visão geral, clique **em Ir ao portal CloudSimple** para abrir um novo separador de navegador para o portal CloudSimple.  Se solicitado, inscreva-se com o seu sinal de Azure em credenciais.  

    ![Lançar portal CloudSimple](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Criar VPN ponto-a-local

Uma ligação VPN ponto-a-local é a forma mais simples de ligar à sua Nuvem Privada a partir do seu computador. Utilize a ligação VPN ponto-a-local se estiver a ligar-se remotamente à Nuvem Privada.  Para um acesso rápido à sua Nuvem Privada, siga os passos abaixo.  O acesso à região CloudSimple a partir da sua rede de acesso pode ser feito utilizando [a VPN site-to-site](vpn-gateway.md) ou [a Azure ExpressRoute](on-premises-connection.md).

### <a name="create-gateway"></a>Criar porta de entrada

1. Lançar portal CloudSimple e selecionar **Rede**.
2. Selecione **VPN Gateway**.
3. Clique em **Novo Gateway VPN**.

    ![Criar um gateway de VPN](media/create-vpn-gateway.png)

4. Para **a configuração do Gateway**, especifique as seguintes definições e clique em **Seguinte**.

    * Selecione **Point-to-Site VPN** como o tipo de gateway.
    * Insira um nome para identificar o portal.
    * Selecione a localização Azure onde o seu serviço CloudSimple está implantado.
    * Especifique a sub-rede do cliente para o gateway Ponto-a-Local.  Os endereços DHCP serão dados a partir desta sub-rede quando se ligar.

5. Para **Ligação/Utilizador**, especifique as seguintes definições e clique em **Seguinte**.

    * Para permitir automaticamente que todos os utilizadores atuais e futuros acedam à Nuvem Privada através deste gateway Ponto-a-Local, selecione **Adicionar automaticamente todos os utilizadores**. Quando seleciona esta opção, todos os utilizadores da lista de utilizadores são automaticamente selecionados. Pode anular a opção automática desseleccionando utilizadores individuais na lista.
    * Para selecionar apenas utilizadores individuais, clique nas caixas de verificação na lista de utilizadores.

6. A secção VLANs/Subnets permite especificar VLANs/subnets de gestão e utilizador para o gateway e conexões.

    * As opções **de adicionar automaticamente** definem a política global para este gateway. As definições aplicam-se ao gateway atual. As definições podem ser ultrapassadas na área **Select.**
    * **Selecione Adicionar VLANs/Subnets de Nuvens Privadas .**
    * Para adicionar todos os VLANs/subnets definidos pelo utilizador, clique em  **Adicionar VLANs/Subnets definidos pelo utilizador**.
    * As definições **Select** sobrepõem-se às definições globais em **adicionar automaticamente**.

7. Clique **em Seguinte** para rever as definições. Clique nos ícones editar para escoar quaisquer alterações.
8. Clique em **Criar** para criar o gateway VPN.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Ligue-se à CloudSimple usando VPN ponto-a-local

O cliente VPN é necessário para ligar ao CloudSimple a partir do seu computador.  Baixe [o cliente OpenVPN](https://openvpn.net/community-downloads/) para Windows ou [Viscosidade](https://www.sparklabs.com/viscosity/download/) para macOS e OS X.

1. Lançar portal CloudSimple e selecionar **Rede**.
2. Selecione **VPN Gateway**.
3. A partir da lista de gateways VPN, clique na porta VPN ponto-a-local.
4. Selecione **Utilizadores**.
5. Clique **em Baixar a minha configuração VPN**.

    ![Transferir a configuração da VPN](media/download-p2s-vpn-configuration.png)

6. Importe a configuração no seu cliente VPN.

    * Instruções para [a configuração de importação no cliente Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instruções para [a configuração de importação em macOS ou OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Ligue-se ao CloudSimple.

## <a name="create-a-vlan-for-your-workload-vms"></a>Crie um VLAN para os seus VMs de carga de trabalho

Depois de criar uma Nuvem Privada, crie um VLAN onde irá implementar os seus VMs de carga de trabalho/aplicação.

1. No portal CloudSimple, selecione **Rede**.
2. Clique em **VLAN/Subnets**.
3. Clique **em Criar VLAN/sub-rede**.

    ![Criar VLAN/Sub-rede](media/create-new-vlan-subnet.png)

4. Selecione a **Nuvem Privada** para a nova VLAN/sub-rede.
5. Selecione um ID VLAN da lista.  
6. Introduza um nome de sub-rede para identificar a sub-rede.
7. Especifique a gama e a máscara cidr da sub-rede.  Este intervalo não deve sobrepor-se a quaisquer sub-redes existentes.
8. Clique **em Submeter.**

    ![Criar detalhes VLAN/Sub-rede](media/create-new-vlan-subnet-details.png)

A VLAN/sub-rede será criada.  Agora pode utilizar este ID VLAN para criar um grupo de portas distribuído no seu VCenter Private Cloud.

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Ligue o seu ambiente a uma rede virtual Azure

O CloudSimple fornece-lhe um circuito ExpressRoute para a sua Nuvem Privada. Pode ligar a sua rede virtual em Azure ao circuito ExpressRoute. Para obter todos os detalhes sobre a configuração da ligação, siga os passos na [Ligação da Rede Virtual Azure utilizando o ExpressRoute](./cloudsimple-azure-network-connection.md).

## <a name="sign-in-to-vcenter"></a>Inscreva-se no vCenter

Agora pode iniciar sôms no vCenter para configurar máquinas e políticas virtuais.

1. Para aceder ao vCenter, comece a partir do portal CloudSimple. Na página inicial, em **Tarefas Comuns,** clique em **Launch vSphere Client**.  Selecione a Nuvem Privada e, em seguida, clique em **Lançar vSphere Client** na Nuvem Privada.

    ![Lançamento vSphere Cliente](media/launch-vcenter-from-cloudsimple-portal.png)

2. Selecione o seu cliente vSphere preferido para aceder ao vCenter e iniciar sôr-se com o seu nome de utilizador e senha.  Os predefinidos são:
    * Nome do utilizador: `CloudOwner@cloudsimple.local`
    * Palavra-passe: `CloudSimple123!`

Os ecrãs vCenter nos próximos procedimentos são do cliente vSphere (HTML5).

## <a name="change-your-vcenter-password"></a>Altere a sua palavra-passe vCenter

O CloudSimple recomenda que altere a sua palavra-passe na primeira vez que iniciar sôs o seu sºC no vCenter.  
A palavra-passe que definiu deve satisfazer os seguintes requisitos:

* Duração máxima: A palavra-passe deve ser alterada a cada 365 dias
* Restringir a reutilização: Os utilizadores não podem reutilizar nenhuma das cinco palavras-passe anteriores
* Comprimento: 8 - 20 caracteres
* Personagem especial: Pelo menos um personagem especial
* Caracteres alfabéticos: Pelo menos um personagem maiúsculo, A-Z, e pelo menos um personagem minúsculo, a-z
* Números: Pelo menos um personagem numérico, 0-9
* Máximo idênticos caracteres adjacentes: Três

    Exemplo: CC ou CCC é aceitável como parte da palavra-passe, mas CCCC não é.

Se definir uma palavra-passe que não satisfaça os requisitos:

* se utilizar o vSphere Flash Client, reporta um erro
* Se utilizar o cliente HTML5, não reporta um erro. O cliente não aceita a mudança e a velha senha continua a funcionar.

## <a name="access-nsx-manager"></a>Gestor de Acesso NSX

O gestor NSX é implementado com uma senha padrão. 

* Nome de utilizador: **administrador**
* Palavra-passe: **CloudSimple123!**

Pode encontrar o nome de domínio totalmente qualificado (FQDN) e endereço IP do gestor NSX no portal CloudSimple.

1. Lançar portal CloudSimple e selecionar **Recursos.**
2. Clique na Nuvem Privada, que pretende utilizar.
3. Selecione **rede de gestão vSphere**
4. Utilize o endereço FQDN ou IP do **NSX Manager** e conecte-se através de um navegador web.

    ![Encontre o NSX Manager FQDN](media/private-cloud-nsx-manager-fqdn.png)

## <a name="create-a-port-group"></a>Criar um grupo portuário

Para criar um grupo portuário distribuído na vSphere:

1. Siga as instruções em "Adicionar um grupo de portas distribuído" no [guia de rede vSphere](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf).
2. Ao configurar o grupo portuário distribuído, forneça o ID VLAN criado na [Create a VLAN para os seus VMs de carga de trabalho](#create-a-vlan-for-your-workload-vms).

## <a name="next-steps"></a>Passos seguintes

* [Consumir VMs de VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* [Ligue-se à rede de instalações utilizando a Azure ExpressRoute](on-premises-connection.md)
* [Configurar VPN site-to-site a partir de instalações](vpn-gateway.md)
