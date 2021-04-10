---
title: Dimensionamento de SAP HANA em Azure (Grandes Instâncias) | Microsoft Docs
description: Dimensionamento de SAP HANA em Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d49191abbba9c189672be4cd8bad4346e9689bf6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675420"
---
# <a name="sizing"></a>Dimensionamento

O tamanho para HANA Large Instance não é diferente do tamanho para HANA em geral. Para os sistemas existentes e implantados que pretende mover de outros RDBMS para HANA, o SAP fornece uma série de relatórios que funcionam nos seus sistemas SAP existentes. Se a base de dados for transferida para a HANA, estes relatórios verificam os dados e calculam os requisitos de memória para a instância HANA. Para obter mais informações sobre como executar estes relatórios e obter os seus mais recentes patches ou versões, leia as seguintes Notas SAP:

- [SAP Note #1793345 - Dimensionamento para SAP Suite em HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP Note #1872170 - Suíte em relatório de dimensionamento HANA e S/4 HANA](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP Note #2121330 - FAQ: SAP BW sobre relatório de dimensionamento de HANA](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP Note #1736976 - Relatório de dimensionamento para bW em HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP Note #2296290 - Novo relatório de dimensionamento para bW em HANA](https://launchpad.support.sap.com/#/notes/2296290)

Para implementações de campo verde, o SAP Quick Sizer está disponível para calcular os requisitos de memória da implementação do software SAP em cima da HANA.

Os requisitos de memória para HANA aumentam à medida que o volume de dados aumenta. Esteja atento ao seu consumo atual de memória para ajudá-lo a prever o que será no futuro. Com base nos requisitos de memória, pode mapear a sua demanda num dos SKUs de Grande Instância HANA.

**Próximos passos**
- Consulte [os requisitos de embarque](hana-onboarding-requirements.md)