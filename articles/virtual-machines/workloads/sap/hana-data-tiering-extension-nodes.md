---
title: Nós de extensão e de criação de camadas de dados para o SAP HANA no Azure (instâncias grandes) | Documentos da Microsoft
description: Disposição em camadas e a extensão de nós de dados para o SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b5868aaf29d6b57ea608763840a08f1991a982e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60628596"
---
# <a name="use-sap-hana-data-tiering-and-extension-nodes"></a>Utilizar nós de extensão e de criação de camadas de dados do SAP HANA

SAP suporta um modelo de camadas de dados para SAP BW das diferentes versões do SAP NetWeaver e SAP BW/4HANA. Para obter mais informações sobre o modelo de camadas de dados, consulte o documento SAP [SAP BW/4HANA e do SAP BW em HANA connosco de extensão do SAP HANA](https://www.sap.com/documents/2017/05/ac051285-bc7c-0010-82c7-eda71af511fa.html#).
Com a instância grande do HANA, pode utilizar a opção 1 configuração de nós de extensão do SAP HANA conforme explicado nos documentos de blog de perguntas frequentes e SAP. Opção 2 configurações podem ser configuradas com SKUs seguintes instância grande de HANA: S72m, S192, S192m, S384 e S384m. 

Quando examinar a documentação, a vantagem pode não estar visível imediatamente. Mas quando examinar as diretrizes de dimensionamento do SAP, verá uma vantagem, utilizando a opção-1 e a opção 2 nós de extensão do SAP HANA. Seguem-se exemplos:

- Diretrizes de dimensionamento do SAP HANA geralmente exigem o dobro da quantidade do volume de dados como memória. Quando executar a sua instância do SAP HANA com os dados de acesso frequente, tem apenas 50 por cento ou menor de memória preenchido com dados. O restante da memória ideal é que está a ser retido para o SAP HANA fazendo seu trabalho.
- Isso significa que numa unidade de S192 instância grande do HANA com 2 TB de memória, execução de uma base de dados do SAP BW, só tem 1 TB como volume de dados.
- Se utilizar um nó adicional para a extensão de SAP HANA de opção-1, também um S192 HANA grandes instância SKU, ele fornece uma capacidade adicional de 2 TB para o volume de dados. Na configuração da opção-2, receberá um adicional 4 TB para volumes de dados muito ou pouco. Em comparação com o nó de acesso frequente, a capacidade de memória cheia do nó de extensão "quente" pode ser utilizada para o armazenamento de dados para a opção-1. Duplicar a memória pode ser utilizada para o volume de dados na configuração de nó de extensão de SAP HANA de opção-2.
- Acaba tendo uma capacidade de 3 TB para os seus dados e uma proporção de acesso frequente para quente de 1:2 para a opção-1. Tem 5 TB de dados e uma proporção de 1:4 com a configuração de nó de extensão de opção-2.

Quanto maior for o volume de dados em comparação comparado a memória, maior será o provável que os dados de acesso pouco frequente que estão fazendo para são armazenados no armazenamento de disco.

**Passos seguintes?**
- Consulte [arquitetura do SAP HANA (instâncias grandes) no Azure](hana-architecture.md)