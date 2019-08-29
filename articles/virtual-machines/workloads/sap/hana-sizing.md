---
title: Dimensionamento de SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Dimensionamento de SAP HANA no Azure (instâncias grandes).
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
ms.openlocfilehash: f2540a0f9ea702ff620ccd4b68208cbb2e70aafd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101134"
---
# <a name="sizing"></a>Dimensionamento

O dimensionamento para instância grande do HANA não é diferente do dimensionamento para HANA em geral. Para sistemas existentes e implantados que você deseja mover de outro RDBMS para HANA, o SAP fornece vários relatórios que são executados em seus sistemas SAP existentes. Se o banco de dados for movido para o HANA, esses relatórios verificarão e calcularão os requisitos de memória para a instância do HANA. Para obter mais informações sobre como executar esses relatórios e obter seus patches ou versões mais recentes, leia as seguintes notas SAP:

- [SAP Note #1793345-Sizing for SAP Suite no HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [Relatório de dimensionamento do SAP Note #1872170-Suite no HANA e S/4 HANA](https://launchpad.support.sap.com/#/notes/1872170)
- [Observação SAP #2121330-perguntas frequentes: SAP BW no relatório de dimensionamento do HANA](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP Note #1736976 relatório de dimensionamento para BW no HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP Note #2296290-novo relatório de dimensionamento para BW no HANA](https://launchpad.support.sap.com/#/notes/2296290)

Para implementações em campo verde, o SAP Quick Sizing está disponível para calcular os requisitos de memória da implementação do software SAP na parte superior do HANA.

Requisitos de memória para HANA aumentam à medida que aumenta o volume de dados. Lembre-se do seu consumo de memória atual para ajudá-lo a prever o que será no futuro. Com base nos requisitos de memória, você pode mapear sua demanda em um dos SKUs de instância grande do HANA.

**Passos seguintes?**
- Consulte [os requisitos de integração](hana-onboarding-requirements.md)