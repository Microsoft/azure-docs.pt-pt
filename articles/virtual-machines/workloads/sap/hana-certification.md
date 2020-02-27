---
title: Certificação do SAP HANA no Azure (Grandes Instâncias)  Microsoft Docs
description: Certificação do SAP HANA em Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2a02f0e1b05b9de8105126d1c9e4e3f79057285f
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617233"
---
# <a name="certification"></a>Certificação

Além da certificação NetWeaver, a SAP requer uma certificação especial para a SAP HANA para apoiar a SAP HANA em certas infraestruturas, como o Azure IaaS.

A nota principal do SAP na NetWeaver, e em certa medida a certificação SAP HANA, é [a SAP Note #1928533 – Aplicações SAP em Azure: Produtos suportados e tipos de VM Azure.](https://launchpad.support.sap.com/#/notes/1928533)

Os registos de certificação das unidades SAP HANA em Azure (Grandes Instâncias) podem ser encontrados no site [iaaS certificado sAP HANA Platforms.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) 

Os tipos SAP HANA on Azure (Grandes Instâncias), referidos no site iaaS certificado sap HANA Platforms, fornecem aos clientes da Microsoft e da SAP a capacidade de implementar grandes sap business suite, SAP BW, S/4 HANA, BW/4HANA, ou outras cargas de trabalho DaAP HANA em Azure. A solução baseia-se no carimbo de hardware dedicado certificado sAP-HANA ( Integração de centros de[dados personalizadoS SAP HANA – TDI).](https://scn.sap.com/docs/DOC-63140) Se executar uma solução configurada por SAP HANA TDI, todas as aplicações baseadas em SAP HANA (como a SAP Business Suite em SAP HANA, SAP BW em SAP HANA, S4/HANA e BW4/HANA) funcionam na infraestrutura de hardware.

Em comparação com a execução do SAP HANA em VMs, esta solução tem um benefício. Fornece volumes de memória muito maiores. Para permitir esta solução, é necessário compreender os seguintes aspectos-chave:

- A camada de aplicação SAP e as aplicações não SAP funcionam em VMs que estão hospedados nos habituais selos de hardware Azure.
- A infraestrutura, centros de dados e implementações de aplicações do cliente no local estão ligados à plataforma da nuvem através do ExpressRoute (recomendado) ou de uma rede privada virtual (VPN). O Diretório Ativo e o DNS também são estendidos ao Azure.
- A base de dados SAP HANA para a carga de trabalho da HANA funciona no SAP HANA on Azure (Grandes Instâncias). O carimbo de grande instância está ligado à rede Azure, para que o software que funciona em VMs possa interagir com a instância HANA em execução em HANA Large Instance.
- Hardware da SAP HANA on Azure (Grandes Instâncias) é um hardware dedicado fornecido num IaaS com SUSE Linux Enterprise Server ou Red Hat Enterprise Linux pré-instalado. Tal como acontece com as máquinas virtuais, novas atualizações e manutenção do sistema operativo são da sua responsabilidade.
- A instalação de HANA ou quaisquer componentes adicionais necessários para executar o SAP HANA em unidades da HANA Large Instance é da sua responsabilidade. Todas as respetivas operações em curso e administração da SAP HANA no Azure também são da sua responsabilidade.
- Além das soluções aqui descritas, pode instalar outros componentes na sua subscrição Azure que se ligam ao SAP HANA no Azure (Grandes Instâncias). Exemplos são componentes que permitem a comunicação com ou diretamente para a base de dados SAP HANA, tais como servidores de salto, servidores RDP, Estúdio SAP HANA, Serviços de Dados SAP para cenários SAP BI ou soluções de monitorização de rede.
- Tal como em Azure, a HANA Large Instance oferece suporte para uma funcionalidade de alta disponibilidade e recuperação de desastres.

**Passos seguintes?**
- Consulte [as SKUs disponíveis para hli](hana-available-skus.md) 