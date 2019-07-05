---
title: Integrar o Microsoft Azure com a infraestrutura de nuvem de Oracle | Documentos da Microsoft
description: Saiba mais sobre as soluções que integram aplicações Oracle em execução no Microsoft Azure com bases de dados na infraestrutura de nuvem da Oracle (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: jeconnoc
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: 5a60e41d3195c0f7d88fd3ba14336d693d2f528e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446686"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure-preview"></a>Soluções de aplicações Oracle integrar o Microsoft Azure e infraestrutura de nuvem da Oracle (pré-visualização)

Microsoft e a Oracle estabeleceram uma parceria para fornecer uma latência baixa, conectividade de entre Clouds de alto débito, permitindo que aproveitar o melhor de ambas as nuvens. 

Usando essa conectividade entre Clouds, pode particionar uma aplicação de várias camadas para executar na infraestrutura de nuvem da Oracle (OCI), a sua camada de base de dados e a aplicação e outras camadas no Microsoft Azure. A experiência é semelhante ao executar a pilha de toda a solução numa única cloud. 

> [!IMPORTANT]
> Esta capacidade entre Clouds está atualmente em pré-visualização e algumas [limitações aplicam-se](#preview-limitations). As pré-visualizações são tornadas disponíveis para si na condição de concordar com os [termos suplementares de utilização](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

Se estiver interessado na implantação de soluções da Oracle inteiramente na infraestrutura do Azure, veja [imagens de VM do Oracle e a respetiva implementação no Microsoft Azure](oracle-vm-solutions.md).

## <a name="scenario-overview"></a>Descrição geral do cenário

Conectividade entre a nuvem fornece uma solução para executar aplicações de líderes do setor da Oracle e suas próprias aplicações personalizadas, em máquinas virtuais do Azure ao aproveitar as vantagens dos serviços de base de dados alojada na OCI. 

Pode executar numa configuração entre Clouds de aplicações incluem:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Aplicativos de varejo do Oracle
* Gerenciamento financeiro do Oracle Hyperion

O diagrama seguinte é uma visão geral da solução ligada. Para simplificar, o diagrama mostra apenas uma camada de aplicativo e uma camada de dados. Consoante a arquitetura do aplicativo, sua solução poderia incluir camadas adicionais, como uma camada web no Azure. Para obter mais informações, consulte as secções seguintes.

![Descrição geral da solução OCI do Azure](media/oracle-oci-overview/crosscloud.png)

## <a name="preview-limitations"></a>Limitações de pré-visualização

* Conectividade entre Clouds em pré-visualização está limitada a região do Azure Leste dos EUA (eastus) e a região de (-nos-ashburn-1) de OCI Ashburn.

## <a name="networking"></a>Redes

Os clientes empresariais, muitas vezes, optam por diversificar e implementar cargas de trabalho ao longo de várias clouds por vários motivos operacionais e empresariais. A diversificar, os clientes interligações redes em nuvem através da internet, o IPSec VPN, ou utilizar a solução de conectividade direta do fornecedor da cloud através de sua rede no local. Interconectados redes em nuvem, podem exigir investimentos significativos em tempo, dinheiro, design, aprovisionamento, instalação, testes e operações. 

Para resolver estas dificuldades de cliente, Oracle e a Microsoft tem ativado uma experiência integrada de várias cloud. Rede entre Clouds é estabelecida conectando-se um [ExpressRoute](../../../expressroute/expressroute-introduction.md) circuito no Microsoft Azure com um [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) circuito no OCI. Essa conectividade é possível onde uma localização de peering do ExpressRoute do Azure está na proximidade para ou na mesma localização de peering como o FastConnect OCI. Esta configuração permite conectividade rápida e segura entre duas clouds, sem a necessidade de um fornecedor de serviços intermediária.

Utilizar o ExpressRoute e FastConnect, os clientes podem configurar o peering entre uma rede virtual no Azure com uma rede virtual de nuvem no OCI, desde que o espaço de endereços IP privados não se sobreponha. Peering de duas redes permite que um recurso na rede virtual para comunicar a um recurso na rede de virtual cloud OCI como se fossem ambos na mesma rede.

## <a name="network-security"></a>Segurança da rede

Segurança de rede é um componente crucial de qualquer aplicativo empresarial e é central para esta solução de várias cloud. O tráfego que passa através do ExpressRoute e FastConnect passa através de uma rede privada. Esta configuração permite a comunicação segura entre uma rede virtual do Azure e uma rede de virtual cloud do Oracle. Não precisa de fornecer um endereço IP público para quaisquer máquinas virtuais no Azure. Da mesma forma, não é necessário um gateway de internet no OCI. Toda a comunicação ocorre via o endereço IP privado das máquinas.

Além disso, pode configurar [listas de segurança](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) nas suas regras de rede e segurança da virtual cloud de OCI (ligado ao Azure [grupos de segurança de rede](../../../virtual-network/security-overview.md)). Utilize estas regras para controlar o tráfego que flui entre as máquinas em redes virtuais. Regras de segurança de rede podem ser adicionadas a um nível de máquina, num nível de sub-rede, bem como ao nível da rede virtual.
 
## <a name="identity"></a>identidade

A identidade é um dos principais pilares da parceria entre a Microsoft e Oracle. Um trabalho significativo se tem feito para integrar [serviço de nuvem de identidade do Oracle](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) com [Azure Active Directory](../../../active-directory/index.yml) (Azure AD). O Azure AD é baseado na nuvem acesso e identidade do serviço de gestão da Microsoft. Ele ajuda os usuários a entrar e acessar vários recursos. Azure AD permite-lhe gerir os seus utilizadores e as respetivas permissões.

Atualmente, esta integração permite-lhe gerir num local central, que é o Azure Active Directory. Azure AD sincroniza as alterações no diretório com o diretório correspondente do Oracle e é utilizado para início de sessão único entre Clouds soluções da Oracle.

## <a name="next-steps"></a>Passos Seguintes

Comece com um [rede entre Clouds](configure-azure-oci-networking.md) entre o Azure e o OCI. 

Para obter mais informações e White Papers sobre OCI, consulte a [Oracle Cloud](https://docs.cloud.oracle.com/iaas/Content/home.htm) documentação.
