---
title: Vários ramos no fluxo de dados de mapeamento
description: Replicação de fluxos de dados no fluxo de dados de mapeamento com vários ramos
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: a11dbfbd6d6510b5c421e54cd2547c3aedb1bfb6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100378201"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Criação de um novo ramo no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Adicione um novo ramo para fazer vários conjuntos de operações e transformações contra o mesmo fluxo de dados. Adicionar um novo ramo é útil quando pretende usar a mesma fonte para vários lavatórios ou para se unir em conjunto.

Um novo ramo pode ser adicionado a partir da lista de transformação semelhante a outras transformações. **A New Branch** só estará disponível como ação quando houver uma transformação existente após a transformação que está a tentar ramificar.

![A screenshot mostra a nova opção de ramo no menu entradas /saídas múltiplas.](media/data-flow/new-branch2.png "Adicionar um novo ramo")

No exemplo abaixo, o fluxo de dados está a ler dados de viagem de táxi. É necessária uma saída agregada tanto por dia como pelo fornecedor. Em vez de criar dois fluxos de dados separados que lêem a partir da mesma fonte, um novo ramo pode ser adicionado. Desta forma, ambas as agregações podem ser executadas como parte do mesmo fluxo de dados. 

![A screenshot mostra o fluxo de dados com dois ramos da fonte.](media/data-flow/new-branch.png "Adicionar um novo ramo")
