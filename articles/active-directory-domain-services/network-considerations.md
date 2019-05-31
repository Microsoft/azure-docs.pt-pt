---
title: 'Azure AD Domain Services: Orientações de redes | Documentos da Microsoft'
description: Considerações de redes para o Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2010
ms.author: mstephen
ms.openlocfilehash: 1f21d71bba01eb4bec24dbb558a126ecbbd78bbf
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66246948"
---
# <a name="networking-considerations-for-azure-ad-domain-services"></a>Considerações de redes para o Azure AD Domain Services
## <a name="how-to-select-an-azure-virtual-network"></a>Como selecionar uma rede virtual do Azure
As seguintes diretrizes ajudar a selecionar uma rede virtual para utilizar com os serviços de domínio do Azure AD.

### <a name="type-of-azure-virtual-network"></a>Tipo de rede virtual do Azure
* **Redes virtuais do Resource Manager**: O Azure AD Domain Services podem ser ativados em redes virtuais criadas com o Azure Resource Manager.
* Não é possível ativar o Azure AD Domain Services numa rede virtual do Azure clássico.
* Pode ligar a outras redes virtuais para a rede virtual em que o Azure AD Domain Services é ativado. Para obter mais informações, consulte a [conectividade de rede](network-considerations.md#network-connectivity) secção.

### <a name="azure-region-for-the-virtual-network"></a>Região do Azure para a rede virtual
* Seu domínio gerido é implementado na mesma região do Azure que a rede virtual do Azure AD Domain Services, optar por ativar o serviço.
* Selecione uma rede virtual numa região do Azure suportada pelo Azure AD Domain Services.
* Veja a página [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) para saber em que regiões do Azure estão disponíveis os Serviços de Domínio do Azure AD.

### <a name="requirements-for-the-virtual-network"></a>Requisitos para a rede virtual
* **Proximidade para cargas de trabalho do Azure**: Selecione a rede virtual que atualmente aloja/irá alojar máquinas virtuais que necessitam de aceder aos Serviços de Domínio do Azure AD. Se as cargas de trabalho são implementadas numa rede virtual diferente do que o domínio gerido, também pode optar por ligar as redes virtuais.
* **Servidores DNS personalizados/traga a sua própria**: Certifique-se de que não existem não existem servidores DNS personalizados configurados para a rede virtual. Um exemplo de um servidor DNS personalizado é uma instância do DNS do Windows Server em execução numa VM do Windows Server que tenha implementado na rede virtual. O Azure AD Domain Services não se integra com todos os servidores DNS personalizados implementados na rede virtual.
* **Domínios existentes com o mesmo nome de domínio**: Certifique-se de que não tem um domínio existente com o mesmo nome de domínio disponível na rede virtual. Por exemplo, suponha que tem um domínio chamado 'contoso.com' já disponível na rede virtual selecionada. Posteriormente, tenta ativar um domínio gerido do Azure AD Domain Services com o mesmo nome de domínio (ou seja, "contoso.com") na rede virtual. Ocorrer uma falha ao tentar ativar o Azure AD Domain Services. Esta falha é devido a conflitos de nomes para o nome de domínio na rede virtual. Nesta situação, tem de utilizar um nome diferente para configurar o seu domínio gerido dos Serviços de Domínio do Azure AD. Em alternativa, pode anular o aprovisionamento do domínio existente e, em seguida, prosseguir para ativar os Serviços de Domínio do Azure AD.

> [!WARNING]
> Não é possível mover os serviços de domínio para uma rede virtual diferente depois de ter ativado o serviço.
>
>


## <a name="guidelines-for-choosing-a-subnet"></a>Diretrizes para escolher uma sub-rede

![Design recomendado sub-rede](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

* Implementar o Azure AD Domain Services para um **separar sub-rede dedicada** na sua rede virtual do Azure.
* Não se aplicam os NSGs para a sub-rede dedicada para o seu domínio gerido. Se tem de aplicar NSGs para a sub-rede dedicada, certifique-se de que **não bloquear as portas necessárias para o serviço e gerir o seu domínio**.
* Não restringir excessivamente o número de endereços IP disponíveis na sub-rede dedicada para o seu domínio gerido. Esta restrição impede que o serviço de disponibilizar os dois controladores de domínio para o seu domínio gerido.
* **Não ative o Azure AD Domain Services na sub-rede de gateway** da sua rede virtual.

> [!WARNING]
> Quando associa um NSG com uma sub-rede na qual dos serviços de domínio do Azure AD está ativado, pode interromper a capacidade da Microsoft para o serviço e gerir o domínio. Além disso, a sincronização entre o seu inquilino do Azure AD e o seu domínio gerido é interrompida. **O SLA não é aplicável a implementações em que um NSG foi aplicado bloquear serviços de domínio do Azure AD de atualizar e gerir o seu domínio.**
>
>

## <a name="ports-required-for-azure-ad-domain-services"></a>Portas necessárias para o Azure AD Domain Services
As seguintes portas são necessárias para o Azure AD Domain Services para o serviço e manter o seu domínio gerido. Certifique-se de que estas portas não estão bloqueadas para a sub-rede em que tiver ativado o seu domínio gerido.

| Número da porta | Obrigatório? | Objetivo |
| --- | --- | --- |
| 443 | Obrigatório |Sincronização com o inquilino do Azure AD |
| 5986 | Obrigatório | Gestão do seu domínio |
| 3389 | Obrigatório | Gestão do seu domínio |
| 636 | Opcional | Proteger o acesso de LDAP (LDAPS) ao seu domínio gerido |

**Porta 443 (sincronização com o Azure AD)**
* Ele é usado para sincronizar o diretório do Azure AD com o seu domínio gerido.
* É obrigatório para permitir o acesso a esta porta no seu NSG. Sem acesso a esta porta, o seu domínio gerido não está sincronizado com o seu diretório do Azure AD. Os utilizadores podem não conseguir iniciar sessão como alterações para as palavras-passe não serão sincronizadas com o domínio gerido.
* Pode restringir o acesso de entrada para esta porta para os endereços IP que pertençam ao intervalo de endereços IP do Azure. Observe que o intervalo de endereços IP do Azure é um intervalo diferente do que o intervalo de PowerShell mostrado na regra abaixo.

**Porta 5986 (comunicação remota do PowerShell)**
* Ele é usado para realizar tarefas de gestão a utilizar a comunicação remota do PowerShell no seu domínio gerido.
* É obrigatório para permitir o acesso por essa porta no seu NSG. Sem acesso a esta porta, o seu domínio gerido não pode ser atualizada, configurado, uma cópia de segurança ou monitorizadas.
* Para qualquer novos domínios ou domínios com uma rede virtual do Azure Resource Manager, pode restringir o acesso de entrada para esta porta para os seguintes endereços IP de origem: 52.180.179.108, 52.180.177.87, 13.75.105.168, 52.175.18.134, 52.138.68.41, 52.138.65.157, 104.41.159.212, 104.45.138.161, 52.169.125.119, 52.169.218.0, 52.187.19.1, 52.187.120.237, 13.78.172.246, 52.161.110.169, 52.174.189.149, 40.68.160.142, 40.83.144.56, 13.64.151.161, 52.180.183.67, 52.180.181.39, 52.175.28.111, 52.175.16.141, 52.138.70.93, 52.138.64.115, 40.80.146.22, 40.121.211.60, 52.138.143.173, 52.169.87.10, 13.76.171.84, 52.187.169.156, 13.78.174.255, 13.78.191.178, 40.68.163.143, 23.100.14.28, 13.64.188.43, 23.99.93.197
* Para domínios com uma rede virtual clássica, pode restringir o acesso de entrada para esta porta para os seguintes endereços IP de origem: 52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209
* Os controladores de domínio para o seu domínio gerido não normalmente escutam nesta porta. O serviço abre-se esta porta nos controladores de domínio gerido apenas quando uma operação de gestão ou manutenção precisa de ser executada para o domínio gerido. Assim que a operação for concluída, o serviço encerra esta porta nos controladores de domínio gerido.

**Porta 3389 (ambiente de trabalho remoto)**
* É utilizado para ligações de ambiente de trabalho remotas para controladores de domínio para o seu domínio gerido.
* Pode restringir o acesso de entrada para os seguintes endereços IP de origem: 207.68.190.32/27, 13.106.78.32/27, 13.106.174.32/27, 13.106.4.96/27
* Isso também porta permanece em grande parte desativada no seu domínio gerido. Esse mecanismo não é utilizado numa base contínua, uma vez que a gestão e monitorização de tarefas são realizadas através do PowerShell remoto. Esta porta é utilizada apenas na rara eventualidade de que a Microsoft precisa de ligar remotamente ao seu domínio gerido para a resolução de problemas avançada. A porta é fechada, assim como a operação de resolução de problemas está concluída.

**Porta 636 (LDAP seguro)**
* É utilizado para ativar o acesso de secure LDAP ao seu domínio gerido através da internet.
* Abrir essa porta por meio de seu NSG é opcional. Abra a porta apenas se tiver acesso de secure LDAP pela internet ativada.
* Pode restringir o acesso de entrada para esta porta para os endereços IP de origem do qual pretende ligar através de secure LDAP.


## <a name="network-security-groups"></a>Grupos de Segurança de Rede
R [grupo de segurança de rede (NSG)](../virtual-network/virtual-networks-nsg.md) contém uma lista de regras de lista de controlo de acesso (ACL) que permitem ou negam o tráfego de rede para as instâncias VM numa rede Virtual. Os NSGs podem ser associados a sub-redes ou a instâncias de VM individuais dentro dessa sub-rede. Quando um NSG é associado a uma sub-rede, as regras da ACL são aplicadas a todas as instâncias de VM nessa sub-rede. Além disso, o tráfego para uma VM individual pode ser restrito ainda mais ao associar um NSG diretamente a essa VM.

### <a name="sample-nsg-for-virtual-networks-with-azure-ad-domain-services"></a>Exemplo de NSG para redes virtuais com o Azure AD Domain Services
A tabela a seguir ilustra um exemplo de NSG que pode configurar uma rede virtual com um domínio gerido do Azure AD Domain Services. Esta regra permite o tráfego de entrada sobre as portas necessárias para garantir que permanece seu domínio gerido corrigido, atualizada e pode ser monitorizado pela Microsoft. A regra predefinida do "DenyAll" aplica-se a todos os outros tráfegos de entrada da internet.

Além disso, o NSG também ilustra como bloquear o acesso de secure LDAP pela internet. Ignore esta regra se não tiver ativado acesso de secure LDAP ao seu domínio gerido através da internet. O NSG contém um conjunto de regras que permitam o acesso de LDAPS entrada através da porta TCP 636 apenas a partir de um conjunto especificado de endereços IP. A regra NSG para permitir o acesso LDAPS através da internet a partir de endereços IP especificados tem prioridade sobre a regra de DenyAll NSG.

![Exemplo de NSG para proteger o acesso LDAPS através da internet](./media/active-directory-domain-services-alerts/default-nsg.png)

**Obter mais informações** - [criar um grupo de segurança de rede](../virtual-network/manage-network-security-group.md).


## <a name="network-connectivity"></a>Conectividade de rede
Um domínio gerido do Azure AD Domain Services pode ser ativado apenas dentro de uma única rede virtual no Azure.

### <a name="scenarios-for-connecting-azure-networks"></a>Cenários para ligar a redes do Azure
Ligar redes virtuais do Azure para utilizar o domínio gerido em qualquer um dos seguintes cenários de implementação:

#### <a name="use-the-managed-domain-in-more-than-one-azure-virtual-network"></a>Utilizar o domínio gerido em mais de uma rede virtual do Azure
Pode ligar a outras redes virtuais do Azure para a rede virtual do Azure em que tiver ativado o Azure AD Domain Services. Esta ligação do VPN/VNet peering permite-lhe utilizar o domínio gerido com as suas cargas de trabalho implementadas nas outras redes virtuais.

![Conectividade de rede virtual clássica](./media/active-directory-domain-services-design-guide/classic-vnet-connectivity.png)

#### <a name="use-the-managed-domain-in-a-resource-manager-based-virtual-network"></a>Utilizar o domínio gerido numa rede virtual baseado no Resource Manager
Pode ligar uma rede virtual baseado no Resource Manager para a rede virtual clássica do Azure em que tiver ativado o Azure AD Domain Services. Esta ligação permite-lhe utilizar o domínio gerido com as suas cargas de trabalho implementadas na rede virtual baseado no Resource Manager.

![Resource Manager para conectividade de rede virtual clássica](./media/active-directory-domain-services-design-guide/classic-arm-vnet-connectivity.png)

### <a name="network-connection-options"></a>Opções de ligação de rede
* **Peering de rede de ligações de VNet a VNet com o virtual**: Peering de rede virtual é um mecanismo que liga duas redes virtuais na mesma região através da rede de backbone do Azure. Uma vez executado o peering, as duas redes virtuais aparecem como uma única para todos os fins de conetividade. Vão continuar a ser geridas como recursos separados, mas as máquinas virtuais nestas redes virtuais podem comunicar diretamente entre si através de endereços IP privados.

    ![Utilização do peering de conectividade de rede virtual](./media/active-directory-domain-services-design-guide/vnet-peering.png)

    [Obter mais informações - virtual peering de rede](../virtual-network/virtual-network-peering-overview.md)

* **Ligações de VNet a VNet através de ligações de VPN de site a site**: Ligar uma rede virtual a outra rede virtual (VNet-para-VNet) é semelhante à ligação uma rede virtual a uma localização do site no local. Ambos os tipos de conetividade utilizam um gateway de VPN para fornecer um túnel seguro através de IPsec/IKE.

    ![Conectividade de rede virtual com o Gateway de VPN](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

    [Obter mais informações - ligar redes virtuais com o gateway de VPN](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)

<br>

## <a name="related-content"></a>Conteúdo relacionado
* [Peering de rede virtual do Azure](../virtual-network/virtual-network-peering-overview.md)
* [Configurar uma ligação VNet a VNet para o modelo de implementação clássica](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)
* [Grupos de segurança de rede do Azure](../virtual-network/security-overview.md)
* [Criar um grupo de segurança de rede](../virtual-network/manage-network-security-group.md)
