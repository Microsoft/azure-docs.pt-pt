---
title: Integre o Microsoft Azure com a infraestrutura de nuvem da Oracle | Microsoft Docs
description: Saiba mais sobre soluções que integram aplicativos Oracle em execução em Microsoft Azure com bancos de dados no Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: ''
ms.assetid: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: 4628955998ab8b289a429cdfb85e23f7f97b0b40
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101423"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure-preview"></a>Soluções de aplicativos Oracle que integram Microsoft Azure e a infraestrutura de nuvem da Oracle (versão prévia)

A Microsoft e a Oracle fizeram parceria para fornecer conectividade de nuvem cruzada de baixa latência e alta taxa de transferência, permitindo que você aproveite o melhor das duas nuvens. 

Usando essa conectividade entre nuvens, você pode particionar um aplicativo multicamadas para executar sua camada de banco de dados no Oracle Cloud Infrastructure (OCI) e o aplicativo e outras camadas em Microsoft Azure. A experiência é semelhante à execução de toda a pilha de solução em uma única nuvem. 

> [!IMPORTANT]
> Esse recurso de nuvem está atualmente em visualização e as [limitações se aplicam](#preview-limitations). Para habilitar a conectividade de baixa latência entre o Azure e o OCI, sua assinatura do Azure deve primeiro ser listada em branco para esse recurso. Você deve se registrar na visualização concluindo este pequeno [formulário de pesquisa](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyzVVsi364tClw522rL9tkpUMVFGVVFWRlhMNUlRQTVWSTEzT0dXMlRUTyQlQCN0PWcu). Receberá um e-mail assim que a sua subscrição tiver sido inscrita. Você não poderá usar a funcionalidade até receber um email de confirmação. Você também pode contatar seu representante da Microsoft para ser habilitado para esta versão prévia. O acesso ao recurso de visualização está sujeito à disponibilidade e restrito pela Microsoft a seu exclusivo critério. A conclusão da pesquisa não garante o acesso. Essa visualização é fornecida sem um contrato de nível de serviço e não deve ser usada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Consulte os [termos de uso complementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para ver os detalhes de Microsoft Azure. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

Se você estiver interessado em implantar soluções Oracle totalmente na infraestrutura do Azure, consulte [imagens de VM Oracle e sua implantação em Microsoft Azure](oracle-vm-solutions.md).

## <a name="scenario-overview"></a>Descrição geral do cenário

A conectividade entre nuvem fornece uma solução para que você execute aplicativos líderes do setor da Oracle e seus próprios aplicativos personalizados, em máquinas virtuais do Azure, aproveitando os benefícios dos serviços de banco de dados hospedados no OCI. 

Os aplicativos que você pode executar em uma configuração de nuvem cruzada incluem:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Aplicativos de varejo da Oracle
* Gerenciamento financeiro do Oracle Hyperion

O diagrama a seguir é uma visão geral de alto nível da solução conectada. Para simplificar, o diagrama mostra apenas uma camada de aplicativo e uma camada de dados. Dependendo da arquitetura do aplicativo, sua solução pode incluir camadas adicionais, como uma camada da Web no Azure. Para obter mais informações, consulte as secções seguintes.

![Visão geral da solução de OCI do Azure](media/oracle-oci-overview/crosscloud.png)

## <a name="preview-limitations"></a>Limitações de visualização

* A conectividade entre nuvem na visualização é limitada à região do Azure leste dos EUA (lesteus) e à região de OCI Ashburn (US-Ashburn-1).

## <a name="networking"></a>Redes

Os clientes corporativos geralmente optam por diversificar e implantar cargas de trabalho em várias nuvens por vários motivos operacionais e de negócios. Para diversificar, os clientes interconectam redes em nuvem usando a Internet, VPN IPSec ou usando a solução de conectividade direta do provedor de nuvem por meio de sua rede local. A interconexão de redes em nuvem pode exigir investimentos significativos em tempo, dinheiro, design, aquisição, instalação, teste e operações. 

Para resolver esses desafios do cliente, a Oracle e a Microsoft habilitaram uma experiência de várias nuvens integrada. A rede entre redes em nuvem é estabelecida conectando um circuito do [ExpressRoute](../../../expressroute/expressroute-introduction.md) no Microsoft Azure com um circuito [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) no OCI. Essa conectividade é possível quando um local de emparelhamento do ExpressRoute do Azure está em proximidade com ou no mesmo local de emparelhamento que o FastConnect de OCI. Essa configuração permite uma conectividade segura e rápida entre as duas nuvens sem a necessidade de um provedor de serviços intermediário.

Usando o ExpressRoute e o FastConnect, os clientes podem emparelhar uma rede virtual no Azure com uma rede de nuvem virtual no OCI, desde que o espaço de endereço IP privado não se sobreponha. O emparelhamento das duas redes permite que um recurso na rede virtual se comunique com um recurso na rede de nuvem virtual de OCI como se eles estivessem na mesma rede.

## <a name="network-security"></a>Segurança da rede

A segurança de rede é um componente crucial de qualquer aplicativo empresarial e é fundamental para essa solução de várias nuvens. Qualquer tráfego que passará pelo ExpressRoute e FastConnect passará por uma rede privada. Essa configuração permite a comunicação segura entre uma rede virtual do Azure e uma rede de nuvem virtual da Oracle. Você não precisa fornecer um endereço IP público para nenhuma máquina virtual no Azure. Da mesma forma, você não precisa de um gateway de Internet no OCI. Toda a comunicação ocorre por meio do endereço IP privado das máquinas.

Além disso, você pode configurar [listas de segurança](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) em sua rede de nuvem virtual de OCI e regras de segurança (conectadas a [grupos de segurança de rede](../../../virtual-network/security-overview.md)do Azure). Use essas regras para controlar o fluxo de tráfego entre as máquinas nas redes virtuais. As regras de segurança de rede podem ser adicionadas em um nível de máquina, em um nível de sub-rede, bem como no nível de rede virtual.
 
## <a name="identity"></a>identidade

A identidade é um dos pilares principais da parceria entre a Microsoft e a Oracle. Foi feito um trabalho significativo para integrar o IDCS ( [serviço de nuvem](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) de identidades) da Oracle com o [Azure Active Directory](../../../active-directory/index.yml) (Azure AD). O Azure AD é o serviço de gerenciamento de identidade e acesso baseado em nuvem da Microsoft. Ele ajuda os usuários a entrar e acessar vários recursos. O AD do Azure também permite que você gerencie seus usuários e suas permissões.

Atualmente, essa integração permite que você gerencie em um local central, que é Azure Active Directory. O Azure AD sincroniza todas as alterações no diretório com o diretório Oracle correspondente e é usado para logon único em soluções do Oracle de nuvem cruzada.

## <a name="next-steps"></a>Passos Seguintes

Introdução a uma [rede entre nuvens](configure-azure-oci-networking.md) entre o Azure e o OCI. 

Para obter mais informações e White papers sobre o OCI, consulte a documentação da [nuvem do Oracle](https://docs.cloud.oracle.com/iaas/Content/home.htm) .
