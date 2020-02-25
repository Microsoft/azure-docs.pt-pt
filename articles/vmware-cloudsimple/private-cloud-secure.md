---
title: Soluções Azure VMware by CloudSimple - Nuvem Privada Segura
description: Descreve como proteger soluções Azure VMware pela CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4541874a9e8fc4111e5c65d02f07535c4d14f9f1
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565983"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>Como garantir o seu ambiente de Nuvem Privada

Defina o controlo de acesso baseado em papéis (RBAC) para o CloudSimple Service, o portal CloudSimple e a Cloud Privada do Azure.  Os utilizadores, grupos e funções para aceder ao vCenter da Cloud Privada são especificados usando VMware SSO.  

## <a name="rbac-for-cloudsimple-service"></a>RBAC para serviço CloudSimple

A criação do serviço CloudSimple requer o papel **de Proprietário** ou **Colaborador** na subscrição do Azure.  Por padrão, todos os proprietários e colaboradores podem criar um serviço CloudSimple e aceder ao portal CloudSimple para criar e gerir Nuvens Privadas.  Apenas um serviço CloudSimple pode ser criado por região.  Para restringir o acesso a administradores específicos, siga o procedimento abaixo.

1. Crie um Serviço CloudSimple num novo **grupo de recursos** no portal Azure
2. Especifique o RBAC para o grupo de recursos.
3. Compre nódosos e use o mesmo grupo de recursos que o serviço CloudSimple

Apenas os utilizadores que possuam privilégios **Proprietário** ou **Colaborador** no grupo de recursos verão o serviço CloudSimple e lançarão o portal CloudSimple.

Para obter mais informações sobre o RBAC, consulte [o que é o controlo de acesso baseado em papéis (RBAC) para os recursos Do Azure.](../role-based-access-control/overview.md)

## <a name="rbac-for-private-cloud-vcenter"></a>RBAC para private Cloud vCenter

Um utilizador padrão `CloudOwner@cloudsimple.local` é criado no domínio vCenter SSO quando uma Nuvem Privada é criada.  O utilizador do CloudOwner tem privilégios para gerir o vCenter. Fontes de identidade adicionais são adicionadas ao vCenter SSO para dar acesso a diferentes utilizadores.  As funções e grupos pré-definidos são configurados no vCenter que pode ser usado para adicionar utilizadores adicionais.

### <a name="add-new-users-to-vcenter"></a>Adicione novos utilizadores ao vCenter

1. [Aumente os privilégios](escalate-private-cloud-privileges.md) para **cloudOwner\@cloudsimple.utilizador local** na Nuvem Privada.
2. Assine o vCenter usando **cloudOwner\@cloudsimple.local**
3. Adicione utilizadores de [sinal único vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html).
4. Adicione os utilizadores a [grupos de inscrição individuais vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

Para obter mais informações sobre papéis e grupos pré-definidos, consulte o [modelo de permissão CloudSimple Private Cloud do artigo VMware vCenter.](learn-private-cloud-permissions.md)

### <a name="add-new-identity-sources"></a>Adicione novas fontes de identidade

Pode adicionar fornecedores de identidade adicionais para o domínio VCenter SSO da sua Nuvem Privada.  Os fornecedores de identidade fornecem autenticação e os grupos VCenter SSO fornecem autorização para os utilizadores.

* [Use o Diretório Ativo como fornecedor de identidade](set-vcenter-identity.md) no private Cloud vCenter.
* [Use a AD Azure como fornecedor de identidade](azure-ad.md) no private Cloud vCenter

1. [Aumente os privilégios](escalate-private-cloud-privileges.md) para **cloudOwner\@cloudsimple.utilizador local** na Nuvem Privada.
2. Assine o vCenter usando **cloudOwner\@cloudsimple.local**
3. Adicione os utilizadores do fornecedor de identidade a [grupos de inscrição individuais vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

## <a name="secure-network-on-your-private-cloud-environment"></a>Rede segura no seu ambiente Cloud Privado

A segurança da rede private Cloud é controlada garantindo o acesso à rede e controlando o tráfego da rede entre recursos.

### <a name="access-to-private-cloud-resources"></a>Acesso aos recursos privados da Cloud

O vCenter de Cloud Privado e o acesso aos recursos é sobre uma ligação de rede segura:

* **[Ligação ExpressRoute](on-premises-connection.md)** . A ExpressRoute proporciona uma ligação segura, de alta largura de banda e de baixa latência do seu ambiente no local.  A utilização da ligação permite que os seus serviços, redes e utilizadores no local acedam ao seu vCenter Private Cloud.
* **[Gateway VPN site-to-site](vpn-gateway.md)** . O VPN do site-to-site dá acesso aos seus recursos da Cloud Privada a partir do local através de um túnel seguro.  Especifica quais as redes no local que podem enviar e receber tráfego de rede para a sua Cloud Privada.
* **[Gateway VPN ponto-a-local](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)** . Utilize a ligação VPN ponto-a-local para um acesso remoto rápido ao seu vCenter de Nuvem Privada.

### <a name="control-network-traffic-in-private-cloud"></a>Controle o tráfego da rede em Private Cloud

As tabelas de firewall e as regras controlam o tráfego da rede na Nuvem Privada.  A tabela de firewall permite controlar o tráfego da rede entre uma rede de origem ou endereço IP e uma rede de destino ou endereço IP com base na combinação de regras definidas na tabela.

1. Crie uma [mesa de firewall.](firewall.md#add-a-new-firewall-table)
2. [Adicione regras](firewall.md#create-a-firewall-rule) à mesa de firewall.
3. [Fixe uma tabela de firewall a uma VLAN/subnet](firewall.md#attach-vlans-subnet).
