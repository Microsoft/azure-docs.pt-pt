---
title: Tiering de dados e nó de extensão para SAP HANA em Azure (Grandes Instâncias) | Microsoft Docs
description: Tiering de dados e nó de extensão para SAP HANA em Azure (Grandes Instâncias).
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
ms.openlocfilehash: eb780d10996cb22f5e6fe5bc8889e897e8c3854d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101666764"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Utilize os nivelamentos de dados SAP HANA e os nosdes de extensão

A SAP suporta um modelo de tiering de dados para o SAP BW de diferentes lançamentos SAP NetWeaver e SAP BW/4HANA. Para obter mais informações sobre o modelo de tiering de dados, consulte o documento SAP [BW/4HANA e o SAP BW em HANA com nós de extensão SAP HANA](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
Com HANA Large Instance, você pode usar a configuração option-1 dos nós de extensão SAP HANA, conforme explicado nos documentos do blog FAQ e SAP. As configurações option-2 podem ser configurada com os seguintes SKUs de grande instância HANA: S72m, S192, S192m, S384 e S384m. 

Quando se olha para a documentação, a vantagem pode não ser visível imediatamente. Mas quando olhamos para as diretrizes de dimensionamento SAP, podemos ver uma vantagem utilizando os nós de extensão opções-1 e option-2 SAP HANA. Eis alguns exemplos:

- As diretrizes de dimensionamento SAP HANA geralmente requerem o dobro da quantidade de volume de dados como memória. Quando executou o seu caso SAP HANA com os dados quentes, tem apenas 50% ou menos da memória cheia de dados. O resto da memória é idealmente guardado para SAP HANA fazendo o seu trabalho.
- Isto significa que numa unidade HANA Large Instance S192 com 2 TB de memória, executando uma base de dados SAP BW, você só tem 1 TB como volume de dados.
- Se utilizar um nó adicional de extensão SAP HANA de opção-1, também um SKU de Grande Instância S192 HANA, dá-lhe uma capacidade adicional de 2-TB para volume de dados. Na configuração option-2, obtém-se um adicional de 4 TB para volume de dados quente. Em comparação com o nó quente, a capacidade total de memória do nó de extensão "quente" pode ser utilizada para a armazenagem de dados para a opção-1. O dobro da memória pode ser utilizada para o volume de dados na configuração do nó de extensão SAP HANA option-2.
- Acaba com uma capacidade de 3 TB para os seus dados e uma relação quente-quente de 1:2 para a opção-1. Tem 5 TB de dados e uma relação de 1:4 com a configuração do nó de extensão option-2.

Quanto maior for o volume de dados em comparação com a memória, maiores são as probabilidades de os dados quentes que está a pedir serem armazenados no armazenamento do disco.

**Próximos passos**
- Consulte [a arquitetura SAP HANA (Grandes Instâncias) em Azure](hana-architecture.md)