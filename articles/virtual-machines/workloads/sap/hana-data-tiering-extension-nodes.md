---
title: Niversões de nidismo de dados e extensão para SAP HANA em Azure (Grandes Instâncias) / Microsoft Docs
description: Niversões de nidismo de dados e extensão para SAP HANA em Azure (Grandes Instâncias).
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
ms.openlocfilehash: c3553ac9704ac26d0bdaae0f93b89f41a87ac716
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617151"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Utilize os nódosos de nidismo de dados SAP HANA e de extensão

A SAP suporta um modelo de tiering de dados para SAP BW de diferentes lançamentos SAP NetWeaver e SAP BW/4HANA. Para obter mais informações sobre o modelo de tiering de dados, consulte o documento [SAP BW/4HANA e SAP BW em HANA com nós de extensão SAP HANA](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
Com a HANA Large Instance, pode utilizar a configuração da opção-1 dos nós de extensão SAP HANA, conforme explicado nos documentos do blog FAQ e SAP. As configurações option-2 podem ser configuradas com as seguintes SKUs de grande instância HANA: S72m, S192, S192m, S384 e S384m. 

Quando se olha para a documentação, a vantagem pode não ser visível imediatamente. Mas quando se olha para as diretrizes de dimensionamento SAP, pode ver uma vantagem utilizando os nós de extensão Da opção-1 e opção-2 SAP HANA. Eis alguns exemplos:

- As diretrizes de dimensionamento SAP HANA geralmente requerem o dobro da quantidade de volume de dados como memória. Ao executar a sua instância SAP HANA com os dados quentes, tem apenas 50% ou menos da memória preenchida com dados. O resto da memória é idealmente realizada para a SAP HANA fazer o seu trabalho.
- Isto significa que numa unidade HANA Large Instance S192 com 2 TB de memória, com uma base de dados SAP BW, só tem 1 TB como volume de dados.
- Se utilizar um nó de extensão SAP HANA adicional de opção-1, também um S192 HANA Large Instance SKU, dá-lhe uma capacidade adicional de 2 TB para o volume de dados. Na configuração option-2, obtém-se mais 4 TB para volume de dados quentes. Em comparação com o nó quente, a capacidade de memória completa do nó de extensão "quente" pode ser utilizada para armazenamento de dados para a opção-1. O dobro da memória pode ser utilizado para o volume de dados na configuração do nó de extensão SAP HANA da opção 2.
- Acaba com uma capacidade de 3 TB para os seus dados e uma relação quente-quente de 1:2 para a opção-1. Tem 5 TB de dados e uma relação 1:4 com a configuração do nó de extensão option-2.

Quanto maior for o volume de dados em comparação com a memória, maior é a probabilidade de os dados quentes que está a pedir serem armazenados no armazenamento do disco.

**Passos seguintes**
- Consulte [a arquitetura SAP HANA (Grandes Instâncias) em Azure](hana-architecture.md)