---
title: Requisitos de embarque para SAP HANA em Azure (Grandes Instâncias) / Microsoft Docs
description: Requisitos de embarque para SAP HANA em Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/31/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 141a75a75a214ff4a6f136df7570d6e81f7f4e82
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617008"
---
# <a name="onboarding-requirements"></a>Requisitos de integração

Esta lista reúne requisitos para executar SAP HANA em Azure (Instâncias Maiores).

**Microsoft Azure**

- Uma subscrição Azure que pode ser ligada ao SAP HANA em Azure (Grandes Instâncias).
- Contrato de suporte da Microsoft Premier. Para obter informações específicas relacionadas com o funcionamento do SAP em Azure, consulte a Nota de [Suporte SAP #2015553 – SAP no Microsoft Azure: Suporte pré-requisitos](https://launchpad.support.sap.com/#/notes/2015553). Se utilizar unidades HANA Large Instance com 384 e mais CPUs, também precisa de alargar o contrato de apoio premier para incluir a Resposta Rápida Azure.
- Consciência das SKUs de Grande Instância HANA que você precisa depois de realizar um exercício de tamanho com SAP.

**Conectividade da rede**

- ExpressRoute entre as instalações do Azure: Para ligar o seu centro de dados no local ao Azure, certifique-se de encomendar pelo menos uma ligação de 1-Gbps do seu ISP. A conectividade entre as unidades HANA Large Instance e azure também está a usar a tecnologia ExpressRoute. Esta ligação ExpressRoute entre as unidades HANA Large Instance e Azure está incluída no preço das unidades HANA Large Instance, incluindo todos os dados de entrada e taxas de saída para este circuito expressroute específico. Portanto, você, como cliente, não encontrará custos adicionais para além da sua ligação ExpressRoute entre as instalações e o Azure.

**Sistema operativo**

- Licenças para SUSE Linux Enterprise Server 12 para Aplicações SAP.

   > [!NOTE] 
   > O sistema operativo entregue pela Microsoft não está registado na SUSE. Não está ligado a uma instância de Ferramenta de Gestão de Assinaturas.

- Ferramenta de Gestão de Assinaturas SUSE Linux implantada em Azure num VM. Esta ferramenta fornece a capacidade para que o SAP HANA no Azure (Grandes Instâncias) seja registado e, respectivamente, atualizado pela SUSE. (Não existe acesso à Internet dentro do centro de dados HANA Large Instance.) 
- Licenças para Red Hat Enterprise Linux 6.7 ou 7.x para SAP HANA.

   > [!NOTE]
   > O sistema operativo entregue pela Microsoft não está registado com a Red Hat. Não está ligado a um gerente de subscrição de chapéu vermelho.

- Gestor de subscrição da Red Hat implantado em Azure num VM. O Red Hat Subscription Manager fornece a capacidade para que o SAP HANA em Azure (Grandes Instâncias) seja registado e, respectivamente, atualizado pela Red Hat. (Não existe acesso direto à Internet a partir do interior do inquilino implantado no carimbo De Grande Instância Azure.)
- A SAP exige que tenha um contrato de apoio com o seu fornecedor Linux também. Este requisito não é removido pela solução da HANA Large Instance ou pelo facto de ter gerido o Linux em Azure. Ao contrário de algumas das imagens da galeria Linux Azure, a taxa de serviço *não* está incluída na oferta de solução da HANA Large Instance. É da sua responsabilidade cumprir os requisitos da SAP relativamente a contratos de apoio com o distribuidor Linux. 
   - Para o SUSE Linux, procure os requisitos dos contratos de suporte em [Nota SAP #1984787 - SUSE Linux Enterprise Server 12: Notas](https://launchpad.support.sap.com/#/notes/1984787) de instalação e [nota SAP #1056161 - Suporte prioritário para aplicações SAP](https://launchpad.support.sap.com/#/notes/1056161).
   - Para o Red Hat Linux, é necessário ter os níveis de subscrição corretos que incluem atualizações de suporte e serviço aos sistemas operativos da HANA Large Instance. A Red Hat recomenda a subscrição red hat enterprise linux para a solução SAP. Consulte https://access.redhat.com/solutions/3082481. 

Para a matriz de suporte das diferentes versões SAP HANA com as diferentes versões Linux, consulte [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581).

Para a matriz de compatibilidade do sistema operativo e versões de firmware/controlador HLI, consulte o [Os Upgrade para HLI](os-upgrade-hana-large-instance.md).


> [!IMPORTANT] 
> Para unidades do tipo II apenas a versão SLES 12 SP2 OS é suportada neste momento. 


**Base de Dados**

- Licenças e componentes de instalação de software para SAP HANA (plataforma ou edição empresarial).

**Aplicações**

- Licenças e componentes de instalação de software para quaisquer aplicações SAP que se conectem com o SAP HANA e contratos de suporte sap relacionados.
- Licenças e componentes de instalação de software para quaisquer aplicações não SAP utilizadas com sap HANA em ambientes Azure (Grandes Instâncias) e contratos de suporte relacionados.

**Competências**

- Experiência com e conhecimento do Azure IaaS e seus componentes.
- Experiência e conhecimento de como implementar uma carga de trabalho SAP em Azure.
- Instalação SAP HANA certificada pessoal.
- Competências do arquiteto SAP para projetar alta disponibilidade e recuperação de desastres em torno do SAP HANA.

**SAP**

- A expectativa é que seja um cliente SAP e tenha um contrato de apoio com a SAP.
- Especialmente para implementações da classe Tipo II da HANA Large Instance SKUs, consulte a SAP sobre versões do SAP HANA e as configurações eventuais em hardware de grande escala.

**Passos seguintes?**
- Consulte [a arquitetura SAP HANA (Grandes Instâncias) em Azure](hana-architecture.md)