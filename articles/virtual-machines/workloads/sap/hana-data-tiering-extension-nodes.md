---
title: Nós de extensão e camadas de dados para SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: Nós de extensão e camadas de dados para SAP HANA no Azure (instâncias grandes).
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
ms.openlocfilehash: 261009edc20f946fa86f0482d8ab5045f4b4f84b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099849"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Usar SAP HANA nós de extensão e camadas de dados

O SAP dá suporte a um modelo de camadas de dados para SAP BW de diferentes versões do SAP NetWeaver e SAP BW/4HANA. Para obter mais informações sobre o modelo de camadas de dados, consulte o documento SAP [SAP BW/4HANA e SAP BW no Hana com nós de extensão SAP Hana](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
Com o HANA em instâncias grandes, você pode usar a configuração opção-1 de nós de extensão SAP HANA, conforme explicado nas perguntas frequentes e documentos de blog do SAP. As configurações da opção 2 podem ser configuradas com as seguintes SKUs de instância grande do HANA: S72m, S192, S192m, S384 e S384m. 

Ao examinar a documentação, a vantagem pode não estar visível imediatamente. Mas, ao examinar as diretrizes de dimensionamento do SAP, você pode ver uma vantagem usando a opção-1 e a opção-2 SAP HANA nós de extensão. Aqui estão exemplos:

- SAP HANA diretrizes de dimensionamento geralmente exigem o dobro da quantidade de volume de dados como memória. Quando você executa sua instância do SAP HANA com os dados ativos, você tem apenas 50% ou menos da memória preenchida com dados. O restante da memória é ideal para SAP HANA fazer seu trabalho.
- Isso significa que, em uma unidade de S192 de instância grande do HANA com 2 TB de memória, executando um banco de dados SAP BW, você terá apenas 1 TB como volume.
- Se você usar um nó de extensão de SAP HANA adicional da opção 1, também uma SKU de instância grande do HANA S192, ele fornecerá uma capacidade adicional de 2 TB para o volume de dados. Na configuração da opção 2, você obtém mais 4 TB para o volume de dados quente. Em comparação com o nó ativo, a capacidade de memória total do nó de extensão "quente" pode ser usada para armazenamento de dados para a opção 1. O dobro da memória pode ser usado para o volume de dados na opção-2 SAP HANA configuração de nó de extensão.
- Você acaba com uma capacidade de 3 TB para seus dados e uma taxa de quente a quente de 1:2 para a opção 1. Você tem 5 TB de dados e uma proporção de 1:4 com a configuração de nó de extensão opção 2.

Quanto maior o volume de dados comparado à memória, maior é a probabilidade de que os dados quentes que você está solicitando sejam armazenados no armazenamento em disco.

**Passos seguintes?**
- Consulte a [arquitetura SAP Hana (instâncias grandes) no Azure](hana-architecture.md)