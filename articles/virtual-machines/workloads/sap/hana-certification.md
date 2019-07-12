---
title: Certificação do SAP HANA no Azure (instâncias grandes) | Documentos da Microsoft
description: Certificação do SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d623a189da5c5eccd73018e1ae0b7e5564fe09ab
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709695"
---
# <a name="certification"></a>Certificação

Além da certificação NetWeaver, o SAP requer uma certificação especial para o SAP HANA suportar o SAP HANA em determinados infraestruturas, como o IaaS do Azure.

O núcleo, a nota SAP NetWeaver e para a certificação do SAP HANA de grau, é [1928533 no SAP Note # – aplicações SAP no Azure: Produtos suportados e tipos de VM do Azure](https://launchpad.support.sap.com/#/notes/1928533).

Os registos de certificação para o SAP HANA nas unidades do Azure (instâncias grandes) podem ser encontrados no [plataformas IaaS com certificação SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) site. 

O SAP HANA em tipos de Azure (instâncias grandes), referidos no SAP HANA certificadas site de plataformas de IaaS, fornece à Microsoft e SAP os clientes a capacidade de implantar grandes do SAP Business Suite, SAP BW, S/4 HANA, BW/4HANA ou outras cargas de trabalho do SAP HANA no Azure. A solução baseia-se o carimbo de hardware dedicado de certificado do SAP HANA ([SAP HANA adaptadas a integração do Centro de dados – TDI](https://scn.sap.com/docs/DOC-63140)). Se executar uma solução configurada TDI do SAP HANA, todos os aplicativos baseados em SAP HANA (como o SAP Business Suite no SAP HANA, SAP BW em SAP HANA, S4/HANA e BW4/HANA) funciona na infraestrutura de hardware.

Em comparação com a execução do SAP HANA nas VMs, essa solução tem um benefício. Ele fornece para volumes muito maiores de memória. Para ativar esta solução, precisa entender os seguintes aspetos fundamentais:

- Executam a aplicações de camada como não SAP de aplicações SAP em VMs que estão alojadas numa carimbos usual de hardware do Azure.
- Cliente infraestrutura no local, centros de dados, e implementações de aplicações estão ligadas para a plataforma na cloud através do ExpressRoute (recomendado) ou uma rede privada virtual (VPN). O Active Directory e DNS também é expandido para o Azure.
- A instância de base de dados do SAP HANA para carga de trabalho do HANA é executado no SAP HANA no Azure (instâncias grandes). O carimbo de data / instância grande está ligada em rede do Azure, para que o software em execução em VMs pode interagir com a instância HANA em execução numa instância grande do HANA.
- Hardware de SAP HANA no Azure (instâncias grandes) é fornecido num de IaaS com SUSE Linux Enterprise Server ou do Red Hat Enterprise Linux pré-instalado de hardware dedicado. Tal como acontece com as máquinas virtuais, ainda mais as atualizações e manutenção para o sistema operativo é sua responsabilidade.
- Instalação do HANA ou quaisquer componentes adicionais necessários para executar o SAP HANA em unidades de instância grande do HANA é sua responsabilidade. Todas as respectivas operações em curso e administração de SAP HANA no Azure também são da responsabilidade do cliente.
- Além das soluções descritas aqui, pode instalar os outros componentes na sua subscrição do Azure que se conecta ao SAP HANA no Azure (instâncias grandes). Os exemplos são componentes que permitem a comunicação com ou diretamente para o SAP HANA de base de dados, tais como servidores de atalhos, servidores RDP e SAP HANA Studio, serviços de dados do SAP para cenários de BI do SAP, ou soluções de monitorização de rede.
- Como no Azure, a instância grande do HANA oferece suporte a alta disponibilidade e a funcionalidade de recuperação após desastre.

**Passos seguintes?**
- Consulte [SKUs disponíveis para HLI](hana-available-skus.md) 