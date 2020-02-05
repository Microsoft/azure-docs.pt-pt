---
title: Soluções Azure VMware (AVS) - Nuvem Privada Segura AVS
description: Descreve como proteger a Azure VMware Solutions (AVS) Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b053f1493c2380153711176a4748ebf90b479a6c
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020085"
---
# <a name="how-to-secure-your-avs-private-cloud-environment"></a>Como garantir o seu ambiente De Nuvem Privada AVS

Defina o controlo de acesso baseado em funções (RBAC) para o Serviço AVS, portal AVS e Nuvem Privada AVS a partir de Azure. Os utilizadores, grupos e funções para aceder ao vCenter da Nuvem Privada AVS são especificados usando VMware SSO. 

## <a name="rbac-for-avs-service"></a>RBAC para serviço AVS

A criação do serviço AVS requer o papel **de Proprietário** ou **Colaborador** na subscrição do Azure. Por padrão, todos os proprietários e colaboradores podem criar um serviço AVS e aceder ao portal AVS para criar e gerir as Nuvens Privadas AVS. Apenas um serviço AVS pode ser criado por região. Para restringir o acesso a administradores específicos, siga o procedimento abaixo.

1. Criar um Serviço AVS num novo **grupo de recursos** no portal Azure
2. Especifique o RBAC para o grupo de recursos.
3. Compre nódosos e use o mesmo grupo de recursos que o serviço AVS

Apenas os utilizadores que possuam privilégios **Proprietário** ou **Contribuinte** no grupo de recursos verão o serviço AVS e lançarão o portal AVS.

Para obter mais informações sobre o RBAC, consulte [o que é o controlo de acesso baseado em papéis (RBAC) para os recursos Do Azure.](../role-based-access-control/overview.md)

## <a name="rbac-for-avs-private-cloud-vcenter"></a>RBAC para AVS Private Cloud vCenter

Um utilizador padrão `CloudOwner@AVS.local` é criado no domínio VCenter SSO quando é criada uma Nuvem Privada AVS. O utilizador do CloudOwner tem privilégios para gerir o vCenter. Fontes de identidade adicionais são adicionadas ao vCenter SSO para dar acesso a diferentes utilizadores. As funções e grupos pré-definidos são configurados no vCenter que pode ser usado para adicionar utilizadores adicionais.

### <a name="add-new-users-to-vcenter"></a>Adicione novos utilizadores ao vCenter

1. [Aumente os privilégios](escalate-private-cloud-privileges.md) para **CloudOwner@AVS.local** utilizador na Nuvem Privada AVS.
2. Assine o vCenter usando **CloudOwner@AVS.local**
3. Adicione utilizadores de [sinal único vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. Adicione os utilizadores a [grupos de inscrição individuais vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

Para obter mais informações sobre papéis e grupos pré-definidos, consulte o [modelo de permissão de permissão AVS Private Cloud do artigo VMware vCenter.](learn-private-cloud-permissions.md)

### <a name="add-new-identity-sources"></a>Adicione novas fontes de identidade

Pode adicionar fornecedores de identidade adicionais para o domínio VCenter SSO da sua Nuvem Privada AVS. Os fornecedores de identidade fornecem autenticação e os grupos VCenter SSO fornecem autorização para os utilizadores.

* [Use o Diretório Ativo como fornecedor de identidade](set-vcenter-identity.md) no AVS Private Cloud vCenter.
* [Use a AD Azure como fornecedor de identidade](azure-ad.md) no AVS Private Cloud vCenter

1. [Aumente os privilégios](escalate-private-cloud-privileges.md) para **CloudOwner@AVS.local** utilizador na Nuvem Privada AVS.
2. Assine o vCenter usando **CloudOwner@AVS.local**
3. Adicione os utilizadores do fornecedor de identidade a [grupos de inscrição individuais vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

## <a name="secure-network-on-your-avs-private-cloud-environment"></a>Rede segura no seu ambiente AVS Private Cloud

A segurança da rede do ambiente Da Vs Private Cloud é controlada garantindo o acesso à rede e controlando o tráfego da rede entre recursos.

### <a name="access-to-avs-private-cloud-resources"></a>Acesso aos recursos privados da AVS Cloud

AVS Private Cloud vCenter e o acesso de recursos é sobre uma ligação de rede segura:

* **[Ligação ExpressRoute](on-premises-connection.md)** . A ExpressRoute proporciona uma ligação segura, de alta largura de banda e de baixa latência do seu ambiente no local. A utilização da ligação permite que os seus serviços, redes e utilizadores no local acedam ao seu VCenter De Nuvem Privada AVS.
* **[Gateway VPN site-to-site](vpn-gateway.md)** . O VPN do site-to-site dá acesso aos seus recursos DaVS Private Cloud a partir do local através de um túnel seguro. Especifica quais as redes no local que podem enviar e receber tráfego de rede para a sua Nuvem Privada AVS.
* **[Gateway VPN ponto-a-local](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)** . Utilize a ligação VPN ponto-a-local para um acesso remoto rápido ao seu VCenter De Nuvem Privada AVS.

### <a name="control-network-traffic-in-avs-private-cloud"></a>Controle o tráfego da rede em AVS Private Cloud

As tabelas de firewall e as regras controlam o tráfego da rede na Nuvem Privada AVS. A tabela de firewall permite controlar o tráfego da rede entre uma rede de origem ou endereço IP e uma rede de destino ou endereço IP com base na combinação de regras definidas na tabela.

1. Crie uma [mesa de firewall.](firewall.md#add-a-new-firewall-table)
2. [Adicione regras](firewall.md#create-a-firewall-rule) à mesa de firewall.
3. [Fixe uma tabela de firewall a uma VLAN/subnet] (firewall.md#attach-vlanssubnet.
