---
title: Integre o Microsoft Azure com a Oracle Cloud Infrastructure | Microsoft Docs
description: Conheça as soluções que integram as aplicações da Oracle em execução no Microsoft Azure com bases de dados em Oracle Cloud Infrastructure (OCI).
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 06/01/2020
ms.author: kegorman
ms.openlocfilehash: b27db94775e2ba8275dbdab4bf5bd61cc0fdf5c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666873"
---
# <a name="oracle-application-solutions-integrating-microsoft-azure-and-oracle-cloud-infrastructure"></a>Soluções de aplicações oracle que integram a Microsoft Azure e a Oracle Cloud Infrastructure

A Microsoft e a Oracle estabeleceram uma parceria para fornecer baixa latência, alta conectividade transversal de produção cruzada, permitindo-lhe tirar partido do melhor de ambas as nuvens. 

Utilizando esta conectividade transversal, pode dividir uma aplicação multi-nível para executar o seu nível de base de dados na Oracle Cloud Infrastructure (OCI), e a aplicação e outros níveis no Microsoft Azure. A experiência é semelhante a executar toda a pilha de solução numa única nuvem. 

Se estiver interessado em executar o seu middleware, incluindo o WebLogic Server, na infraestrutura Azure, mas tiver a base de dados Oracle a funcionar dentro do OCI, consulte [aplicações do WebLogic Server Azure](oracle-weblogic.md).

Se estiver interessado em implementar soluções Oracle inteiramente na infraestrutura Azure, consulte [as imagens da Oracle VM e a sua implementação no Microsoft Azure](oracle-vm-solutions.md).

## <a name="scenario-overview"></a>Scenario overview (Descrição geral do cenário)

A conectividade cross-cloud fornece uma solução para você executar as aplicações líderes da Oracle, e suas próprias aplicações personalizadas, em máquinas virtuais Azure enquanto desfruta dos benefícios dos serviços de base de dados hospedados no OCI. 

A partir de maio de 2020, as seguintes aplicações são certificadas numa configuração de nuvem cruzada:

* Suíte E-Business
* JD Edwards EnterpriseOne
* PeopleSoft
* Aplicações Oracle Retail
* Oracle Hyperion Gestão Financeira

O diagrama a seguir é uma visão geral de alto nível da solução conectada. Para simplificar, o diagrama mostra apenas um nível de aplicação e um nível de dados. Dependendo da arquitetura da aplicação, a sua solução pode incluir níveis adicionais, como um cluster WebLogic Server ou um nível web em Azure. Para mais informações, consulte as seguintes secções.

![Visão geral da solução Azure OCI](media/oracle-oci-overview/crosscloud.png)

## <a name="region-availability"></a>Disponibilidade da Região 

A conectividade entre nuvens cruzadas limita-se às seguintes regiões:
* Azure East US (EastUS) & OCI Ashburn, VA (Leste dos EUA)
* Azure UK South (UKSouth) & OCI London (Reino Unido Sul)
* Azure Canada Central (CanadáCentral) & OCI Toronto (Canadá Sudeste)
* Azure West Europe (WestEurope) & OCI Amsterdam (Noroeste da Holanda)
* Azure Japan East (JapãoEast) & OCI Tokyo (Japão Leste)
* Azure West US (WestUS) & OCI San Jose (EUA)

## <a name="networking"></a>Rede

Os clientes empresariais optam frequentemente por diversificar e implementar cargas de trabalho sobre várias nuvens por várias razões de negócio e operacional. Para diversificar, os clientes interligam redes de nuvem usando a internet, IPSec VPN, ou usando a solução de conectividade direta do fornecedor de nuvem através da sua rede no local. A interligação de redes em nuvem pode exigir investimentos significativos em tempo, dinheiro, design, aquisições, instalação, testes e operações. 

Para responder a estes desafios do cliente, a Oracle e a Microsoft permitiram uma experiência multi-cloud integrada. A rede cross-cloud é estabelecida ligando um circuito [ExpressRoute](../../../expressroute/expressroute-introduction.md) no Microsoft Azure com um circuito [FastConnect](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/fastconnectoverview.htm) em OCI. Esta conectividade é possível quando um local de observação Azure ExpressRoute está próximo ou no mesmo local de observação que o OCI FastConnect. Esta configuração permite uma conectividade segura e rápida entre as duas nuvens sem a necessidade de um prestador de serviços intermédio.

Utilizando o ExpressRoute e o FastConnect, os clientes podem espreitar uma rede virtual em Azure com uma rede de nuvem virtual no OCI, desde que o espaço de endereço IP privado não se sobreponha. Espreitar as duas redes permite que um recurso na rede virtual comunique a um recurso na rede de nuvem virtual OCI como se ambos estivessem na mesma rede.

## <a name="network-security"></a>Segurança da rede

A segurança da rede é um componente crucial de qualquer aplicação da empresa, e é central para esta solução multi-nuvem. Qualquer tráfego que passe por ExpressRoute e FastConnect passa por uma rede privada. Esta configuração permite uma comunicação segura entre uma rede virtual Azure e uma rede de nuvem virtual Oracle. Não precisa de fornecer um endereço IP público a nenhuma máquina virtual em Azure. Da mesma forma, você não precisa de uma porta de entrada na Internet em OCI. Toda a comunicação acontece através do endereço IP privado das máquinas.

Além disso, pode configurar [listas de segurança](https://docs.cloud.oracle.com/iaas/Content/Network/Concepts/securitylists.htm) na sua rede de nuvem virtual OCI e regras de segurança (anexadas a [grupos de segurança da rede](../../../virtual-network/network-security-groups-overview.md)Azure). Utilize estas regras para controlar o tráfego que flui entre máquinas nas redes virtuais. As regras de segurança da rede podem ser adicionadas a um nível de máquina, a nível de sub-redes, bem como ao nível da rede virtual.

As [Aplicações Azure do Servidor WebLogic](oracle-weblogic.md) criam cada um um grupo de segurança de rede pré-configurado para trabalhar com as configurações de porta do WebLogic Server.
 
## <a name="identity"></a>Identidade

A identidade é um dos pilares fundamentais da parceria entre a Microsoft e a Oracle. Foram realizados trabalhos significativos para integrar o [Oracle Identity Cloud Service](https://docs.oracle.com/en/cloud/paas/identity-cloud/index.html) (IDCS) com o [Azure Ative Directory](../../../active-directory/index.yml) (Azure AD). Azure AD é o serviço de gestão de identidade e acesso baseado na nuvem da Microsoft. Os seus utilizadores podem iniciar seduca e aceder a vários recursos com a ajuda do Azure AD. O Azure AD também permite gerir os seus utilizadores e as suas permissões.

Atualmente, esta integração permite-lhe gerir numa localização central, que é o Azure Ative Directory. O Azure AD sincroniza quaisquer alterações no diretório com o diretório oráculo correspondente e é utilizado para soluções oracle de sinalização única e de nuvem cruzada.

## <a name="next-steps"></a>Passos seguintes

Começa com uma [rede de nuvens cruzadas](configure-azure-oci-networking.md) entre o Azure e o OCI. 

Para obter mais informações e documentos brancos sobre o OCI, consulte a documentação da [Oracle Cloud.](https://docs.cloud.oracle.com/iaas/Content/home.htm)
