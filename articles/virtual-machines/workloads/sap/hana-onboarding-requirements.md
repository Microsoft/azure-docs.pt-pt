---
title: Requisitos de embarque para SAP HANA em Azure (Grandes Instâncias) | Microsoft Docs
description: Requisitos de embarque para SAP HANA em Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/31/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 16608facab27fc18924cf3a5c6da7e35db048f76
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101675609"
---
# <a name="onboarding-requirements"></a>Requisitos de integração

Esta lista reúne requisitos para executar SAP HANA em Azure (Instâncias Maiores).

**Microsoft Azure**

- Uma subscrição Azure que pode ser ligada ao SAP HANA em Azure (Grandes Instâncias).
- Contrato de suporte da Microsoft Premier. Para obter informações específicas relacionadas com a execução do SAP em Azure, consulte [a nota de suporte do SAP #2015553 – SAP no Microsoft Azure: Pré-requisitos de suporte](https://launchpad.support.sap.com/#/notes/2015553). Se utilizar unidades HANA Large Instance com 384 e mais CPUs, também precisa estender o contrato de apoio premier para incluir a Azure Rapid Response.
- Consciência dos SKUs de grande instância HANA que você precisa depois de realizar um exercício de tamanho com SAP.

**Conectividade de rede**

- ExpressRoute entre as instalações para Azure: Para ligar o seu centro de dados no local ao Azure, certifique-se de encomendar pelo menos uma ligação de 1-Gbps do seu ISP. A conectividade entre as unidades HANA Large Instance e a Azure também está a utilizar a tecnologia ExpressRoute. Esta ligação ExpressRoute entre as unidades HANA Large Instance e Azure está incluída no preço das unidades HANA Large Instance, incluindo todas as entradas de dados e encargos de saída para este circuito específico ExpressRoute. Portanto, você, como cliente, não encontre custos adicionais além da sua ligação ExpressRoute entre as instalações e a Azure.

**Sistema operativo**

- Licenças para SUSE Linux Enterprise Server 12 para aplicações SAP.

   > [!NOTE] 
   > O sistema operativo entregue pela Microsoft não está registado na SUSE. Não está ligado a uma instância de Ferramenta de Gestão de Assinaturas.

- Ferramenta de gestão de subscrição SUSE Linux implantada em Azure num VM. Esta ferramenta fornece a capacidade para que o SAP HANA em Azure (Grandes Instâncias) seja registado e atualizado respectivamente pela SUSE. (Não existe acesso à Internet dentro do centro de dados HANA Large Instance.) 
- Licenças para Red Hat Enterprise Linux 6.7 ou 7.x para SAP HANA.

   > [!NOTE]
   > O sistema operativo entregue pela Microsoft não está registado com o Red Hat. Não está ligado a uma instância do Diretor de Assinaturas do Chapéu Vermelho.

- Red Hat Subscription Manager implantado em Azure em um VM. O Gestor de Assinaturas red Hat fornece a capacidade para que o SAP HANA em Azure (Grandes Instâncias) seja registado e atualizado respectivamente pela Red Hat. (Não existe acesso direto à Internet a partir do arrendatário implantado no carimbo Azure Large Instance.)
- A SAP também exige que tenha um contrato de apoio com o seu fornecedor Linux. Este requisito não é removido pela solução da HANA Large Instance ou pelo facto de executar o Linux em Azure. Ao contrário de algumas das imagens da galeria Linux Azure, a taxa de serviço *não* está incluída na oferta de solução da HANA Large Instance. É sua responsabilidade cumprir os requisitos da SAP no que diz respeito a contratos de apoio com o distribuidor Linux. 
   - Para a SUSE Linux, procure os requisitos dos contratos de apoio em [#1984787 SAP Note - SUSE Linux Enterprise Server 12: Notas de instalação](https://launchpad.support.sap.com/#/notes/1984787) e #1056161 DE NOTAS SAP - Suporte prioritário [SUSE para aplicações SAP](https://launchpad.support.sap.com/#/notes/1056161).
   - Para o Red Hat Linux, é necessário ter os níveis de subscrição corretos que incluem atualizações de suporte e serviço nos sistemas operativos da HANA Large Instance. A Red Hat recomenda a subscrição red hat Enterprise Linux para a solução SAP. Consulte https://access.redhat.com/solutions/3082481 . 

Para obter a matriz de suporte das diferentes versões SAP HANA com as diferentes versões Linux, consulte [a SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581).

Para a matriz de compatibilidade do sistema operativo e as versões de firmware/condutor HLI, consulte [o UPGRADE DE OS para HLI](os-upgrade-hana-large-instance.md).


> [!IMPORTANT] 
> Para as unidades do tipo II, apenas a versão SLES 12 SP2 OS é suportada neste momento. 


**Base de dados**

- Licenças e componentes de instalação de software para SAP HANA (plataforma ou edição empresarial).

**Aplicações**

- Licenças e componentes de instalação de software para quaisquer aplicações SAP que se liguem ao SAP HANA e contratos de suporte SAP relacionados.
- Licenças e componentes de instalação de software para quaisquer aplicações não-SAP utilizadas com ambientes SAP HANA em Azure (Grandes Instâncias) e contratos de suporte conexos.

**Competências**

- Experiência e conhecimento da Azure IaaS e dos seus componentes.
- Experimente e conheça como implementar uma carga de trabalho SAP em Azure.
- Instalação SAP HANA certificada pessoal.
- Habilidades de arquiteto SAP para projetar alta disponibilidade e recuperação de desastres em torno da SAP HANA.

**SAP**

- A expectativa é que seja cliente DAIR e tenha um contrato de apoio com a SAP.
- Especialmente para implementações da classe Tipo II de SKUs de grande instância HANA, consulte o SAP sobre versões de SAP HANA e as configurações eventuais em hardware de grande escala.

**Próximos passos**
- Consulte [a arquitetura SAP HANA (Grandes Instâncias) em Azure](hana-architecture.md)