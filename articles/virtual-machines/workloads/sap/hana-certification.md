---
title: Certificação da SAP HANA em Azure (Grandes Instâncias) / Microsoft Docs
description: Certificação de SAP HANA em Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7cbd50fae2b53d801f7aa09fae2b4540eef13c9e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967895"
---
# <a name="certification"></a>Certificação

Além da certificação NetWeaver, a SAP requer uma certificação especial para a SAP HANA para apoiar o SAP HANA em determinadas infraestruturas, como o Azure IaaS.

O núcleo SAP Nota na NetWeaver, e em um grau de certificação SAP HANA, é [SAP Note #1928533 – aplicações SAP no Azure: Produtos suportados e tipos de VM Azure](https://launchpad.support.sap.com/#/notes/1928533).

Os registos de certificação das unidades SAP HANA em Azure (Grandes Instâncias) podem ser encontrados no site [das plataformas iaaS certificadas da SAP HANA.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) 

Os tipos SAP HANA on Azure (Grandes Instâncias), referidos no site das plataformas iaaS certificadas da SAP HANA, proporcionam aos clientes da Microsoft e da SAP a capacidade de implantar grandes unidades de negócios SAP, SAP BW, S/4 HANA, BW/4HANA, ou outras cargas de trabalho SAP HANA em Azure. A solução baseia-se no carimbo de hardware dedicado certificado da SAP-HANA[(SAP HANA, integração de centros de dados personalizados – TDI).](https://scn.sap.com/docs/DOC-63140) Se executar uma solução configurada pela SAP HANA TDI, todas as aplicações baseadas em SAP HANA (como SAP Business Suite em SAP HANA, SAP BW em SAP HANA, S4/HANA e BW4/HANA) funcionam na infraestrutura de hardware.

Em comparação com a execução do SAP HANA em VMs, esta solução tem um benefício. Fornece volumes de memória muito maiores. Para ativar esta solução, é necessário compreender os seguintes aspectos-chave:

- A camada de aplicação SAP e as aplicações não-SAP são executadas em VMs que estão hospedados nos selos de hardware Azure habituais.
- As infraestruturas de clientes no local, centros de dados e implementações de aplicações estão ligadas à plataforma cloud através do ExpressRoute (recomendado) ou de uma rede privada virtual (VPN). O Ative Directory e o DNS também são estendidos ao Azure.
- A instância da base de dados SAP HANA para a carga de trabalho HANA funciona em SAP HANA on Azure (Grandes Instâncias). O carimbo de Grande Instância está ligado à rede Azure, para que o software que está a funcionar em VMs possa interagir com a instância HANA em execução em HANA Large Instance.
- Hardware da SAP HANA on Azure (Grandes Instâncias) é hardware dedicado fornecido num IaaS com suse Linux Enterprise Server ou Red Hat Enterprise Linux pré-instalado. Tal como acontece com as máquinas virtuais, mais atualizações e manutenção no sistema operativo é da sua responsabilidade.
- A instalação de HANA ou quaisquer componentes adicionais necessários para executar o SAP HANA em unidades da HANA Large Instance é da sua responsabilidade. Todas as operações em curso e administração da SAP HANA em Azure também são da sua responsabilidade.
- Além das soluções descritas aqui, pode instalar outros componentes na sua subscrição Azure que se conecta ao SAP HANA em Azure (Grandes Instâncias). Exemplos são componentes que permitem a comunicação com ou diretamente à base de dados SAP HANA, tais como servidores de salto, servidores RDP, Estúdio SAP HANA, Serviços de Dados SAP para cenários SAP BI ou soluções de monitorização de rede.
- Tal como em Azure, a HANA Large Instance oferece suporte para alta disponibilidade e funcionalidade de recuperação de desastres.

**Próximos passos**
- Consulte [SKUs disponíveis para HLI](hana-available-skus.md) 