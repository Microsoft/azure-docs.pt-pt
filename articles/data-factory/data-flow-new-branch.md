---
title: Múltiplos ramos no fluxo de dados de mapeamento
description: Replicação de fluxos de dados no fluxo de dados de mapeamento com vários ramos
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 71fb9f1ba9952be0e6b3910dd1079aa6d3c0482d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834525"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Criação de um novo ramo no fluxo de dados de mapeamento

Adicione um novo ramo para fazer vários conjuntos de operações e transformações contra o mesmo fluxo de dados. Adicionar um novo ramo é útil quando se pretende utilizar a mesma fonte para vários lavatórios ou para a união de dados.

Um novo ramo pode ser adicionado da lista de transformação semelhante a outras transformações. **A Nova Sucursal** só estará disponível como ação quando houver uma transformação existente após a transformação que estás a tentar ramificar.

![Adicionar um novo ramo](media/data-flow/new-branch2.png "Adicionar um novo ramo")

No exemplo abaixo, o fluxo de dados é a leitura de dados de viagem de táxi. É necessária uma saída agregada tanto pelo dia como pelo fornecedor. Em vez de criar dois fluxos de dados separados que são lidos a partir da mesma fonte, um novo ramo pode ser adicionado. Desta forma, ambas as agregações podem ser executadas como parte do mesmo fluxo de dados. 

![Adicionar um novo ramo](media/data-flow/new-branch.png "Adicionar um novo ramo")
