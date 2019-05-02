---
title: Solução de VMware do Azure por CloudSimple guia de introdução - criar uma nuvem privada
description: Saiba como criar e configurar uma nuvem privada com a solução de VMware do Azure por CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: efeadd2e1600e5ee572c4a69dde0ff4c53a13cd7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577664"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>Início rápido - configurar um ambiente de nuvem privada

Neste artigo, saiba como criar uma nuvem privada CloudSimple e configurar o ambiente de nuvem privada.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Criar uma nuvem privada

1. Selecione **Todos os serviços**.
2. Procure **CloudSimple serviços**.
3. Selecione o serviço de CloudSimple no qual pretende criar a sua nuvem privada.
4. A partir da descrição geral, clique em **criar nuvem privada** para abrir um novo separador do browser para o portal de CloudSimple.  Se lhe for pedido, inicie sessão com o Azure inscrever-se nas credenciais.  

    ![Criar nuvem privada a partir do Azure](media/create-private-cloud-from-azure.png)

5. No portal de CloudSimple, forneça um nome para a sua nuvem privada
6. Selecione o **localização** de sua nuvem privada
7. Selecione o **tipo de nó** compradas no Azure.  Pode escolher o [opção CS28 ou CS36](cloudsimple-node.md#vmware-solution-by-cloudsimple-nodes-sku). A última opção inclui a capacidade de computação e memória máxima.
8. Especifique a **contagem de nós**.  Nós de mínimo, três são necessários para criar uma nuvem privada

    ![Criar nuvem privada – informações básicas](media/create-private-cloud-basic-info.png)

9. Clique em **seguinte: Opções Avançadas**.
10. Introduza o intervalo CIDR para sub-redes de vSphere/vSAN. Certifique-se de que o intervalo CIDR não se sobrepõe a qualquer uma das suas instalações ou outras sub-redes do Azure.

    ![Criar nuvem privada - opções avançadas](media/create-private-cloud-advanced-options.png)

11. Selecione **seguinte: Reveja e criar**.
12. Reveja as definições. Se precisar de alterar quaisquer definições, clique em **Previous**.
13. Clique em **Criar**.

Nuvem privada, processo de aprovisionamento será iniciada.  Pode demorar até duas horas para a nuvem privada a ser aprovisionado.

## <a name="launch-cloudsimple-portal"></a>Iniciar CloudSimple portal

Pode acessar CloudSimple portal a partir do portal do Azure.  Portal de CloudSimple será iniciado com o seu Azure entrar credenciais através do início de sessão único (SSO).  Aceder ao portal do CloudSimple requer a autorizar a **autorização de serviço CloudSimple** aplicação.  Para obter mais informações sobre a concessão de permissões, consulte [autorizar a aplicação de autorização de serviço CloudSimple](https://docs.azure.cloudsimple.com/access-cloudsimple-portal/#consent-to-cloudsimple-service-authorization-application)

1. Selecione **Todos os serviços**.
2. Procure **CloudSimple serviços**.
3. Selecione o serviço de CloudSimple no qual pretende criar a sua nuvem privada.
4. A partir da descrição geral, clique em **aceda ao portal do CloudSimple** para abrir um novo separador do browser para o portal de CloudSimple.  Se lhe for pedido, inicie sessão com o Azure inscrever-se nas credenciais.  

    ![Iniciar CloudSimple Portal](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>Criar ponto-para-site VPN

Uma ligação de VPN ponto a site é a forma mais simples de ligar à sua nuvem privada a partir do computador. Utilize a ligação de VPN ponto a site se estiver a ligar para a nuvem privada remotamente.  Para obter acesso rápido a sua nuvem privada, siga os passos abaixo.  Acesso a CloudSimple região da sua rede no local pode ser feito utilizando [VPN Site a Site](https://docs.azure.cloudsimple.com/vpn-gateway/) ou [Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/).

### <a name="create-gateway"></a>Criar gateway

1. Iniciar CloudSimple portal e selecione **rede**.
2. Selecione **Gateway de VPN**.
3. Clique em **novo Gateway VPN**.

    ![Criar um gateway de VPN](media/create-vpn-gateway.png)

4. Para **configuração do Gateway**, especifique as seguintes definições e clique em **próxima**.

    * Selecione **Point-to-Site VPN** como o tipo de gateway.
    * Introduza um nome para identificar o gateway.
    * Selecione a localização do Azure em que o seu serviço CloudSimple é implementado.
    * Especifique a sub-rede de cliente para o gateway de ponto a site.  Terá a chance desta sub-rede endereços DHCP quando se liga.

5. Para **ligação/usuário**, especifique as seguintes definições e clique em **próxima**.

    * Para permitir automaticamente todos os utilizadores atuais e futuros acessar a nuvem privada através deste gateway de ponto a site, selecione **adicionar automaticamente todos os utilizadores**. Quando seleciona esta opção, todos os utilizadores na lista de utilizadores são automaticamente selecionados. É possível substituir a opção automática, desselecione utilizadores individuais na lista.
    * Para selecionar apenas os utilizadores individuais, clique nas caixas de seleção na lista de utilizadores.

6. A secção de VLANs/sub-redes permite-lhe especificar o gerenciamento e VLANs/sub-redes de utilizador para o gateway e ligações.

    * O **adicionar automaticamente** opções definir a política global para este gateway. As definições aplicam-se para o gateway atual. As definições podem ser substituídas na **selecione** área.
    * Selecione **adicionar gerenciamento de VLANs/sub-redes de nuvens privadas**. 
    * Para adicionar VLANs/sub-redes tudo definido pelo utilizador, clique em **adicionar VLANs/sub-redes definidas pelo utilizador**. 
    * O **selecionar** definições substituem as definições globais sob **adicionar automaticamente**. 

7. Clique em **seguinte** para rever as definições. Clique nos ícones de edição para fazer alterações.
8. Clique em **criar** para criar o gateway VPN.

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>Ligar ao CloudSimple através da VPN ponto a site

Cliente VPN é necessária para ligar ao CloudSimple do seu computador.  Baixe [cliente OpenVPN](https://openvpn.net/community-downloads/) para Windows ou [Viscosity](https://www.sparklabs.com/viscosity/download/) para macOS e OS X.

1. Iniciar CloudSimple portal e selecione **rede**.
2. Selecione **Gateway de VPN**.
3. Na lista de gateways de VPN, clique no gateway VPN ponto a site.
4. Selecione **utilizadores**.
5. Clique em **transferir minha configuração de VPN**

    ![Transferir a configuração da VPN](media/download-p2s-vpn-configuration.png)

6. Importar a configuração do seu cliente VPN

    * Instruções para [importar configuração no cliente Windows](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)
    * Instruções para [importar configuração no macOS ou OS X](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)

7. Ligar ao CloudSimple

## <a name="create-a-vlan-for-your-workload-vms"></a>Criar uma VLAN para a sua carga de trabalho VMs

Depois de criar uma nuvem privada, crie uma VLAN onde irá implementar a sua carga de trabalho/aplicação VMs.

1. No Portal do CloudSimple, selecione **rede**.
2. Clique em **VLAN/sub-redes**.
3. Clique em **criar sub-rede/VLAN**

    ![Criar sub-rede/VLAN](media/create-new-vlan-subnet.png)

4. Selecione o **nuvem privada** para a nova sub-rede/VLAN.
5. Selecione um ID de VLAN na lista.  
6. Introduza um nome de sub-rede para identificar a sub-rede.
7. Especifique o intervalo CIDR de sub-rede e a máscara.  Este intervalo não pode sobrepor com quaisquer sub-redes existentes.
8. Clique em **Submit** (Submeter).

    ![Criar os detalhes da sub-rede/VLAN](media/create-new-vlan-subnet-details.png)

Será possível criar a sub-rede/VLAN.  Agora, pode utilizar este ID de VLAN para criar um grupo de porta distribuída no seu vCenter de nuvem privada. 

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>Ligar o seu ambiente para uma rede virtual do Azure

CloudSimple fornece um circuito do ExpressRoute para a sua nuvem privada. Pode ligar a rede virtual no Azure para o circuito do ExpressRoute. Para obter detalhes completos sobre como configurar a ligação, siga os passos em [ligação rede Virtual do Azure através do ExpressRoute](https://docs.azure.cloudsimple.com/azure-er-connection)

## <a name="sign-in-to-vcenter"></a>Inicie sessão no vCenter

Pode agora iniciar sessão no vCenter para configurar máquinas virtuais e as políticas.

1. Para aceder ao vCenter, inicie a partir do portal do CloudSimple. Na Home page, sob **tarefas comuns**, clique em **iniciar vSphere Client**.  Selecione a nuvem privada e, em seguida, clique em **inicie vSphere Client** sobre a nuvem privada.

    ![Inicie o vSphere Client](media/launch-vcenter-from-cloudsimple-portal.png)

2. Selecione o seu cliente de vSphere preferencial para aceder ao vCenter e inicie sessão com o nome de utilizador e palavra-passe.  As predefinições são:
    * Nome de utilizador: **CloudOwner@cloudsimple.local**
    * Palavra-passe: **CloudSimple123!**  

As telas de vCenter nos seguintes procedimentos são do cliente vSphere (HTML5).

## <a name="change-your-vcenter-password"></a>Alterar a palavra-passe do vCenter

CloudSimple recomenda que altere a palavra-passe pela primeira vez que iniciar sessão no vCenter.  
A palavra-passe que definir tem de cumprir os seguintes requisitos:

* Duração máxima: Palavra-passe tem de ser alterada cada 365 dias
* Restringir a reutilização: Os utilizadores não é possível reutilizar qualquer uma das palavras-cinco passe anteriores
* Duração: 8 - 20 carateres
* Caráter especial: Pelo menos um caráter especial
* Caracteres alfabéticos: Pelo menos um caráter em maiúsculas, A-Z e, pelo menos, um caráter minúsculo, a-z
* Números: Pelo menos um caráter numérico, 0 a 9
* Número máximo de carateres adjacente idêntico: Três

    Exemplo: CC ou CCC é aceitável como parte da palavra-passe, mas não de CCCC.

Se definir uma palavra-passe não cumpre os requisitos:

* Se usar o vSphere Flash Client, ele relata um erro
* Se utilizar o cliente do HTML5, ele não relatar um erro. O cliente não aceita a mudança e a palavra-passe antiga continua a funcionar.

## <a name="change-nsx-administrator-password"></a>Alteração de senha de administrador NSX

Gestor de NSX é implementado com uma palavra-passe predefinida.  Recomendamos que altere a palavra-passe depois de criar sua nuvem privada.

   * Nome de utilizador: **admin**
   * Palavra-passe: **CloudSimple123!**

Pode encontrar o nome de domínio completamente qualificado (FQDN) e o endereço de e-mail do Gestor de NSX no portal de CloudSimple.

1. Iniciar CloudSimple portal e selecione **recursos**.
2. Clique na nuvem privada, o que pretende utilizar.
3. Selecione **rede de gestão do vSphere**
4. Utilize o endereço IP ou FQDN do **NSX Manager** e ligar através de um navegador da web. 

    ![Localizar NSX Manager FQDN](media/private-cloud-nsx-manager-fqdn.png)

Para alterar a palavra-passe, siga as instruções em [NSX Manager Installation](https://docs.vmware.com/en/VMware-NSX-T-Data-Center/2.2/com.vmware.nsxt.install.doc/GUID-A65FE3DD-C4F1-47EC-B952-DEDF1A3DD0CF.html).

## <a name="create-a-port-group"></a>Criar um grupo de portas

Para criar um grupo de portas distribuída no vSphere:

1. Siga as instruções em "Adicionar um grupo de portas distribuída," a [vSphere guia de rede do](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf).
2. Quando configurar o grupo de porta distribuída, forneça o ID de VLAN que criou no [criar uma VLAN para as VMs de carga de trabalho](#create-a-vlan-for-your-workload-vms).

## <a name="next-steps"></a>Passos Seguintes

* [Consumir as VMs de VMware no Azure](https://docs.azure.cloudsimple.com/quickstart-create-vmware-virtual-machine)
* [Consumir as VMs de VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* [Ligar à rede no local utilizam o Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection/)
* [Configurar a VPN de Site a Site no local](https://docs.azure.cloudsimple.com/vpn-gateway/)
