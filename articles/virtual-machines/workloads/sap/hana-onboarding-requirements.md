---
title: Requisitos de integração para SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Requisitos de integração para SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/31/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 99ad334a526b269879034dcc0e1cd0b1b22f1f7f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101198"
---
# <a name="onboarding-requirements"></a>Requisitos de integração

Esta lista monta os requisitos para executar o SAP HANA no Azure (instâncias maiores).

**Microsoft Azure**

- Uma assinatura do Azure que pode ser vinculada a SAP HANA no Azure (instâncias grandes).
- Contrato de suporte Microsoft Premier. Para obter informações específicas relacionadas à execução do SAP no Azure [, consulte a observação de suporte SAP #2015553 – SAP no Microsoft Azure: Pré-requisitos](https://launchpad.support.sap.com/#/notes/2015553)de suporte. Se você usar unidades de instância grande do HANA com 384 e mais CPUs, também precisará estender o contrato de suporte Premier para incluir Azure Rapid Response.
- Reconhecimento dos SKUs de instância grande do HANA que você precisa depois de executar um exercício de dimensionamento com o SAP.

**Conectividade de rede**

- ExpressRoute entre o local para o Azure: Para conectar seu data center local ao Azure, solicite pelo menos uma conexão de 1 Gbps do seu ISP. A conectividade entre as unidades de instância grande do HANA e o Azure também está usando a tecnologia ExpressRoute. Essa conexão de ExpressRoute entre as unidades de instância grande do HANA e o Azure está incluída no preço das unidades de instância grande do HANA, incluindo todos os encargos de entrada e saída de dados para esse circuito do ExpressRoute específico. Portanto, você como cliente não encontra custos adicionais além do link do ExpressRoute entre o local e o Azure.

**Sistema operativo**

- Licenças para o SUSE Linux Enterprise Server 12 para aplicativos SAP.

   > [!NOTE] 
   > O sistema operacional fornecido pela Microsoft não está registrado com o SUSE. Ele não está conectado a uma instância da ferramenta de gerenciamento de assinatura.

- Ferramenta de gerenciamento de assinaturas SUSE Linux implantada no Azure em uma VM. Essa ferramenta fornece a capacidade para SAP HANA no Azure (instâncias grandes) serem registradas e, respectivamente, atualizadas pelo SUSE. (Não há acesso à Internet dentro do SAP HANA em instâncias grandes data center.) 
- Licenças para Red Hat Enterprise Linux 6,7 ou 7. x para SAP HANA.

   > [!NOTE]
   > O sistema operacional fornecido pela Microsoft não está registrado com o Red Hat. Ele não está conectado a uma instância do Gerenciador de assinaturas do Red Hat.

- Gerenciador de assinaturas do Red Hat implantado no Azure em uma VM. O Gerenciador de assinaturas do Red Hat fornece a capacidade para SAP HANA no Azure (instâncias grandes) serem registradas e, respectivamente, atualizadas pelo Red Hat. (Não há acesso direto à Internet de dentro do locatário implantado no carimbo de instância grande do Azure.)
- O SAP exige que você tenha um contrato de suporte com seu provedor Linux também. Esse requisito não é removido pela solução do SAP HANA em instâncias grandes ou pelo fato de que você executa o Linux no Azure. Ao contrário de algumas das imagens da galeria do Linux Azure, a taxa de serviço *não* é incluída na oferta da solução do SAP Hana em instâncias grandes. É sua responsabilidade atender aos requisitos do SAP em relação aos contratos de suporte com o distribuidor do Linux. 
   - Para o SuSE Linux, procure os requisitos dos contratos de suporte [no SAP Note #1984787-SuSE Linux Enterprise Server 12: Notas](https://launchpad.support.sap.com/#/notes/1984787) de instalação e [SAP Note #1056161-suporte de prioridade SuSE para aplicativos SAP](https://launchpad.support.sap.com/#/notes/1056161).
   - Para o Red Hat Linux, você precisa ter os níveis de assinatura corretos que incluem suporte e atualizações de serviço para os sistemas operacionais do SAP HANA em instâncias grandes. O Red Hat recomenda a assinatura Red Hat Enterprise Linux para a solução SAP. Consulte https://access.redhat.com/solutions/3082481. 

Para obter a matriz de suporte das diferentes versões de SAP HANA com as diferentes versões do Linux, consulte [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581).

Para a matriz de compatibilidade do sistema operacional e versões de firmware/driver do HLI, consulte [atualização do so para HLI](os-upgrade-hana-large-instance.md).


> [!IMPORTANT] 
> Para unidades do tipo II, somente a versão do sistema operacional SLES 12 SP2 tem suporte neste ponto. 


**Base de Dados**

- Licenças e componentes de instalação de software para SAP HANA (Platform ou Enterprise Edition).

**Aplicações**

- Licenças e componentes de instalação de software para quaisquer aplicativos SAP que se conectam a SAP HANA e contratos de suporte SAP relacionados.
- Licenças e componentes de instalação de software para quaisquer aplicativos não SAP usados com SAP HANA em ambientes do Azure (instâncias grandes) e contratos de suporte relacionados.

**Conhecem**

- Experiência e conhecimento do Azure IaaS e de seus componentes.
- Experiência e conhecimento de como implantar uma carga de trabalho do SAP no Azure.
- SAP HANA o certificado de instalação pessoal.
- Habilidades do SAP Architect para criar alta disponibilidade e recuperação de desastres em SAP HANA.

**SAP**

- A expectativa é que você seja um cliente SAP e tenha um contrato de suporte com o SAP.
- Especialmente para implementações da classe do tipo II de SKUs de instância grande do HANA, consulte o SAP em versões do SAP HANA e as configurações eventuals em hardware de escala vertical de tamanho grande.

**Passos seguintes?**
- Consulte a [arquitetura SAP Hana (instâncias grandes) no Azure](hana-architecture.md)