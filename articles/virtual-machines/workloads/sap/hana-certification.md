---
title: Certificação de SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Certificação de SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3c2c919d6e85a2b708dc247b1642e7e8987df81b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099888"
---
# <a name="certification"></a>Certificação

Além da certificação NetWeaver, o SAP requer uma certificação especial para SAP HANA oferecer suporte a SAP HANA em determinadas infra-estruturas, como o Azure IaaS.

A observação principal do SAP no NetWeaver, e com um grau SAP Hana certificação, [é SAP Note #1928533 – aplicativos SAP no Azure: Produtos com suporte e tipos](https://launchpad.support.sap.com/#/notes/1928533)de VM do Azure.

Os registros de certificação para SAP HANA em unidades do Azure (instâncias grandes) podem ser encontrados no site de [plataformas de IaaS certificado pelo SAP Hana](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) . 

O SAP HANA em tipos do Azure (instâncias grandes), mencionado em SAP HANA site de plataformas de IaaS certificadou, fornece aos clientes da Microsoft e SAP a capacidade de implantar grandes cargas de trabalho do SAP Business Suite, SAP BW, S/4 HANA, BW/4HANA ou outra carga de SAP HANA no Azure. A solução baseia-se no selo de hardware dedicado certificado pelo SAP-HANA ([SAP Hana a integração de data center personalizada – TDI](https://scn.sap.com/docs/DOC-63140)). Se você executar uma solução configurada por TDI SAP HANA, todos os aplicativos baseados em SAP HANA (como o SAP Business Suite no SAP HANA, SAP BW em SAP HANA, S4/HANA e BW4/HANA) funcionarão na infraestrutura de hardware.

Em comparação com a execução de SAP HANA em VMs, essa solução tem um benefício. Ele fornece volumes de memória muito maiores. Para habilitar essa solução, você precisa entender os seguintes aspectos principais:

- A camada de aplicativo SAP e os aplicativos não SAP são executados em VMs hospedadas nos carimbos de hardware usuais do Azure.
- A infraestrutura local, os data centers e as implantações de aplicativos do cliente estão conectados à plataforma de nuvem por meio do ExpressRoute (recomendado) ou de uma VPN (rede virtual privada). Active Directory e DNS também são estendidos para o Azure.
- A instância de banco de dados SAP HANA para cargas de trabalho do HANA é executada em SAP HANA no Azure (instâncias grandes). O carimbo de instância grande está conectado à rede do Azure, portanto, o software em execução nas VMs pode interagir com a instância do HANA em execução no HANA em instâncias grandes.
- O hardware de SAP HANA no Azure (instâncias grandes) é um hardware dedicado fornecido em uma IaaS com SUSE Linux Enterprise Server ou Red Hat Enterprise Linux pré-instalado. Assim como acontece com as máquinas virtuais, atualizações e manutenção posteriores ao sistema operacional são de sua responsabilidade.
- A instalação do HANA ou de quaisquer componentes adicionais necessários para executar SAP HANA em unidades do SAP HANA em instâncias grandes é sua responsabilidade. Todas as respectivas operações e administração em andamento do SAP HANA no Azure também são de sua responsabilidade.
- Além das soluções descritas aqui, você pode instalar outros componentes em sua assinatura do Azure que se conectam ao SAP HANA no Azure (instâncias grandes). Os exemplos são componentes que permitem a comunicação com ou diretamente para o banco de dados SAP HANA, como servidores de salto, servidores RDP, SAP HANA Studio, SAP Data Services para cenários do SAP BI ou soluções de monitoramento de rede.
- Como no Azure, o HANA em instâncias grandes oferece suporte para a funcionalidade de alta disponibilidade e recuperação de desastres.

**Passos seguintes?**
- Consulte os [SKUs disponíveis para o HLI](hana-available-skus.md) 