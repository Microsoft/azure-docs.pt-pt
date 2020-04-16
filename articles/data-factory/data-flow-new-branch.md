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
ms.openlocfilehash: af356641b4588529aea25826ff180707ff1ef4e2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413607"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Criação de um novo ramo no fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Adicione um novo ramo para fazer vários conjuntos de operações e transformações contra o mesmo fluxo de dados. Adicionar um novo ramo é útil quando se pretende utilizar a mesma fonte para vários lavatórios ou para a união de dados.

Um novo ramo pode ser adicionado da lista de transformação semelhante a outras transformações. **A Nova Sucursal** só estará disponível como ação quando houver uma transformação existente após a transformação que estás a tentar ramificar.

![Adicionar um novo ramo](media/data-flow/new-branch2.png "Adicionar um novo ramo")

No exemplo abaixo, o fluxo de dados é a leitura de dados de viagem de táxi. É necessária uma saída agregada tanto pelo dia como pelo fornecedor. Em vez de criar dois fluxos de dados separados que são lidos a partir da mesma fonte, um novo ramo pode ser adicionado. Desta forma, ambas as agregações podem ser executadas como parte do mesmo fluxo de dados. 

![Adicionar um novo ramo](media/data-flow/new-branch.png "Adicionar um novo ramo")
