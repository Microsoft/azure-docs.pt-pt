---
title: Integrar o Microsoft Azure com a Oracle Cloud Infrastructure  Microsoft Docs
description: Conheça soluções que integram aplicações oracle em execução no Microsoft Azure com bases de dados em Oracle Cloud Infrastructure (OCI).
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
ms.openlocfilehash: e1249913300be532cc6514f1478bbc6f4183c001
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78300558"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure-preview"></a>Soluções de aplicação Oracle que integram a Microsoft Azure e a Oracle Cloud Infrastructure (pré-visualização)

A Microsoft e a Oracle estabeleceram uma parceria para fornecer baixa latência, conectividade transversal de alta produção, permitindo-lhe aproveitar o melhor de ambas as nuvens. 

Utilizando esta conectividade transversal, pode dividir uma aplicação de vários níveis para executar o seu nível de base de dados na Oracle Cloud Infrastructure (OCI), e na aplicação e outros níveis no Microsoft Azure. A experiência é semelhante a executar toda a pilha de soluções numa única nuvem. 

> [!IMPORTANT]
> Esta capacidade de nuvem cruzada está atualmente em pré-visualização, e [as limitações aplicam-se.](#region-availability) Para estabelecer uma baixa conectividade de latência entre o Azure e o OCI, a subscrição do Azure deve ser ativada primeiro para esta capacidade. Deve inscrever-se na pré-visualização preenchendo este formulário de [sondagem](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyzVVsi364tClw522rL9tkpUMVFGVVFWRlhMNUlRQTVWSTEzT0dXMlRUTyQlQCN0PWcu)curta . Receberá um e-mail assim que a sua subscrição tiver sido inscrita. Não poderá utilizar a capacidade até receber um e-mail de confirmação. Pode também contactar o seu representante da Microsoft para estar ativado para esta pré-visualização. O acesso à capacidade de pré-visualização está sujeito a disponibilidade e restringido pela Microsoft a seu exclusivo critério. A conclusão do inquérito não garante o acesso. Esta pré-visualização é fornecida sem um acordo de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Consulte os [Termos De Utilização Suplementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para visualizações do Microsoft Azure para mais detalhes. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

Se estiver interessado em implementar soluções Oracle inteiramente na infraestrutura Azure, consulte [as imagens da Oracle VM e a sua implementação no Microsoft Azure](oracle-vm-solutions.md).

## <a name="scenario-overview"></a>Descrição geral do cenário

A conectividade cross-cloud fornece uma solução para você executar as aplicações líderes do setor da Oracle, e suas próprias aplicações personalizadas, em máquinas virtuais Azure enquanto desfruta dos benefícios dos serviços de base de dados hospedados no OCI. 

As aplicações que pode executar numa configuração de nuvem cruzada incluem:

* Suíte E-Business
* JD Edwards EnterpriseOne
* PeopleSoft
* Aplicações oracle Retail
* Gestão Financeira Oracle Hyperion

O diagrama que se segue é uma visão geral de alto nível da solução conectada. Para a simplicidade, o diagrama mostra apenas um nível de aplicação e um nível de dados. Dependendo da arquitetura de aplicação, a sua solução poderia incluir camadas adicionais, como um nível web em Azure. Para obter mais informações, consulte as secções seguintes.

![Visão geral da solução Azure OCI](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>Disponibilidade da Região 

A conectividade transversal limita-se às seguintes regiões:
* Azure East US (leste) & OCI Ashburn (EUA Leste)
* Azure UK South (uksouth) & OCI London (UK Sul)
* Azure Canada Central (canadácentral) e OCI Toronto (Canadá Sudeste)
* Azure West Europe (Europa Ocidental) e OCI Amsterdam (Noroeste dos Países Baixos)

## <a name="networking"></a>Redes

Os clientes empresariais optam frequentemente por diversificar e implantar cargas de trabalho sobre várias nuvens por várias razões de negócio e operacionais. Para diversificar, os clientes interligam redes de nuvem utilizando a internet, IPSec VPN, ou usando a solução de conectividade direta do fornecedor de nuvem através da sua rede no local. As redes de nuvem interligadas podem exigir investimentos significativos no tempo, dinheiro, design, aquisição, instalação, testes e operações. 

Para responder a estes desafios dos clientes, a Oracle e a Microsoft permitiram uma experiência integrada em várias nuvens. A rede cross-cloud é estabelecida ligando um circuito [ExpressRoute](../../../expressroute/expressroute-introduction.md) no Microsoft Azure a um circuito [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) no OCI. Esta conectividade é possível quando um local de observação do Azure ExpressRoute está próximo ou no mesmo local de observação que o OCI FastConnect. Esta configuração permite uma conectividade segura e rápida entre as duas nuvens sem a necessidade de um prestador de serviços intermédio.

Utilizando o ExpressRoute e o FastConnect, os clientes podem utilizar uma rede virtual em Azure com uma rede de nuvem virtual no OCI, desde que o espaço privado de endereçoip não se sobreponha. Espreitar as duas redes permite que um recurso na rede virtual comunique a um recurso da rede de nuvem virtual OCI como se ambas estivessem na mesma rede.

## <a name="network-security"></a>Segurança da rede

A segurança da rede é um componente crucial de qualquer aplicação da empresa, e é central para esta solução multi-nuvem. Qualquer tráfego que passe por ExpressRoute e FastConnect passa por uma rede privada. Esta configuração permite uma comunicação segura entre uma rede virtual Azure e uma rede de nuvem virtual Oracle. Não precisa de fornecer um endereço IP público a nenhuma máquina virtual em Azure. Da mesma forma, você não precisa de um portal de internet no OCI. Toda a comunicação acontece através do endereço IP privado das máquinas.

Além disso, pode configurar [listas](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) de segurança na sua rede de nuvem virtual OCI e regras de segurança (anexadas aos [grupos](../../../virtual-network/security-overview.md)de segurança da rede Azure). Utilize estas regras para controlar o tráfego que flui entre máquinas nas redes virtuais. As regras de segurança da rede podem ser adicionadas a nível de máquina, a nível de subnet, bem como a nível de rede virtual.
 
## <a name="identity"></a>Identidade

A identidade é um dos pilares fundamentais da parceria entre a Microsoft e a Oracle. Foram realizados trabalhos significativos para integrar o [Oracle Identity Cloud Service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) com o [Azure Ative Directory](../../../active-directory/index.yml) (Azure AD). O Azure AD é o serviço de gestão de identidade e acesso baseado na nuvem da Microsoft. Ajuda os seus utilizadores a iniciars sessão e a aceder a vários recursos. A Azure AD também lhe permite gerir os seus utilizadores e as suas permissões.

Atualmente, esta integração permite-lhe gerir numa localização central, que é o Azure Ative Directory. A Azure AD sincroniza quaisquer alterações no diretório com o diretório oracle correspondente e é usada para uma única assinatura para soluções Oracle de nuvem cruzada.

## <a name="next-steps"></a>Passos seguintes

Inicie-se com uma [rede de nuvem cruzada](configure-azure-oci-networking.md) entre o Azure e o OCI. 

Para mais informações e documentos brancos sobre o OCI, consulte a documentação da [Oracle Cloud.](https://docs.cloud.oracle.com/iaas/Content/home.htm)
