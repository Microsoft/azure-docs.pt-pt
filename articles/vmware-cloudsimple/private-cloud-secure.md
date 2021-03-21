---
title: Azure VMware Solutions by CloudSimple - Secure Private Cloud
description: Descreve como garantir soluções Azure VMware por CloudSimple Private Cloud
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5e70745cd6e2f6a2a13581052f65e014bd0d0481
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899172"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>Como proteger o seu ambiente Private Cloud

Defina o controlo de acesso baseado em funções (RBAC) para o Serviço CloudSimple, portal CloudSimple e Cloud Privada de Azure.  Os utilizadores, grupos e funções para aceder ao vCenter da Nuvem Privada são especificados usando VMware SSO.  

## <a name="azure-rbac-for-cloudsimple-service"></a>Azure RBAC para serviço CloudSimple

A criação do serviço CloudSimple requer o papel **de Proprietário** ou **Contribuinte** na subscrição do Azure.  Por padrão, todos os proprietários e colaboradores podem criar um serviço CloudSimple e aceder ao portal CloudSimple para criar e gerir nuvens privadas.  Apenas um serviço CloudSimple pode ser criado por região.  Para restringir o acesso a administradores específicos, siga o procedimento abaixo.

1. Criar um Serviço CloudSimple num novo **grupo de recursos** no portal Azure
2. Especificar o RBAC Azure para o grupo de recursos.
3. Comprar nódes e usar o mesmo grupo de recursos que o serviço CloudSimple

Apenas os utilizadores que tenham privilégios **proprietário** ou **contribuinte** no grupo de recursos irão ver o serviço CloudSimple e lançar o portal CloudSimple.

Para obter mais informações, consulte [o que é o controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md).

## <a name="rbac-for-private-cloud-vcenter"></a>RBAC para Private Cloud vCenter

Um utilizador predefinido `CloudOwner@cloudsimple.local` é criado no domínio SSO vCenter quando uma Nuvem Privada é criada.  O utilizador cloudOwner tem privilégios para gerir o vCenter. Fontes de identidade adicionais são adicionadas ao sSO vCenter para dar acesso a diferentes utilizadores.  As funções e grupos pré-definidos são criados no vCenter que podem ser usados para adicionar utilizadores adicionais.

### <a name="add-new-users-to-vcenter"></a>Adicionar novos utilizadores ao vCenter

1. [Aumente os privilégios](escalate-private-cloud-privileges.md) para **\@ cloudOwner cloudsimple.local** user on the Private Cloud.
2. Inscreva-se no vCenter usando **\@ cloudOwner cloudsimple.local**
3. [Adicionar vCenter Single Sign-On Utilizadores](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. Adicione os utilizadores aos [grupos de inscrição única do vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

Para obter mais informações sobre funções e grupos pré-definidos, consulte [o modelo de permissão cloudsimple private cloud do artigo VMware vCenter vCenter.](learn-private-cloud-permissions.md)

### <a name="add-new-identity-sources"></a>Adicionar novas fontes de identidade

Pode adicionar fornecedores de identidade adicionais para o domínio SSO vCenter da sua Nuvem Privada.  Os fornecedores de identidade fornecem a autenticação e os grupos SSO vCenter fornecem autorização para os utilizadores.

* [Utilize o Ative Directory como fornecedor de identidade](set-vcenter-identity.md) no Private Cloud vCenter.
* [Use a Azure AD como fornecedor de identidade](azure-ad.md) no Private Cloud vCenter

1. [Aumente os privilégios](escalate-private-cloud-privileges.md) para **\@ cloudOwner cloudsimple.local** user on the Private Cloud.
2. Inscreva-se no vCenter usando **\@ cloudOwner cloudsimple.local**
3. Adicione os utilizadores do fornecedor de identidade aos [grupos de inscrição única do vCenter.](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)

## <a name="secure-network-on-your-private-cloud-environment"></a>Rede segura no seu ambiente Private Cloud

A segurança da rede do ambiente Private Cloud é controlada garantindo o acesso à rede e controlando o tráfego de rede entre recursos.

### <a name="access-to-private-cloud-resources"></a>Acesso aos recursos private Cloud

Private Cloud vCenter e acesso a recursos é sobre uma ligação de rede segura:

* **[Ligação ExpressRoute](on-premises-connection.md)**. O ExpressRoute fornece uma ligação segura, de alta largura de banda e baixa latência do seu ambiente no local.  A utilização da ligação permite que os seus serviços, redes e utilizadores no local acedam ao seu VCenter Private Cloud.
* **[Gateway VPN local-to-site](vpn-gateway.md)**. A VPN local-a-local dá acesso aos seus recursos private Cloud a partir do local através de um túnel seguro.  Especifica quais as redes no local que podem enviar e receber tráfego de rede para a sua Nuvem Privada.
* **[Gateway VPN ponto a local.](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)** Utilize a ligação VPN ponto-a-local para um acesso remoto rápido ao seu VCenter Cloud Privado.

### <a name="control-network-traffic-in-private-cloud"></a>Tráfego de rede de controlo em Private Cloud

Tabelas de firewall e regras controlam o tráfego da rede na Nuvem Privada.  A tabela de firewall permite-lhe controlar o tráfego de rede entre uma rede de origem ou endereço IP e uma rede de destino ou endereço IP com base na combinação de regras definidas na tabela.

1. Criar uma [tabela de firewall](firewall.md#add-a-new-firewall-table).
2. [Adicione regras](firewall.md#create-a-firewall-rule) à mesa de firewall.
3. [Fixe uma tabela de firewall a uma sub-rede VLAN/.](firewall.md#attach-vlans-subnet)
