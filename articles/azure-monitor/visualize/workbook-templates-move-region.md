---
title: Modelos de livro do monitor Azure - Mover Regiões
description: Como mover um modelo de livro para uma região diferente
services: azure-monitor
author: gardnerjr
manager: acearun
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/12/2020
ms.author: jgardner
ms.openlocfilehash: 9787118b1912a5697c02e116be99b1f396748937
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100613802"
---
# <a name="move-an-azure-workbook-template-to-another-region"></a>Mova um modelo de livro Azure para outra região

Este artigo descreve como mover os recursos do modelo do livro Azure para uma região de Azure diferente. Pode mover os seus recursos para outra região por várias razões. Por exemplo, aproveitar uma nova região de Azure, para implantar características ou serviços disponíveis apenas em regiões específicas, para satisfazer os requisitos de política interna e governação, ou em resposta aos requisitos de planeamento de capacidades.

Atualmente não existe um portal UI para criar recursos do Modelo de Livro, a única forma atual de criá-los é [através de implementações do Modelo de Gestor de Recursos Azure (modelo ARM).](../visualize/workbooks-automate.md) Como tal, a maneira mais fácil de mover um modelo é reutilizar o modelo ARM anterior e atualizá-lo para ser implantado na nova região.

## <a name="prerequisites"></a>Pré-requisitos

* Certifique-se de que os modelos de livro são suportados na região alvo.

## <a name="prepare"></a>Preparação

* Identifique o modelo ARM anteriormente utilizado para o modelo do livro.

## <a name="move"></a>Mover

1. Atualize o modelo anteriormente utilizado para fazer referência à nova região.

   > [!NOTE]
   > Pode ser necessário utilizar um novo nome para o modelo do livro para evitar nomes duplicados.

2. Implemente o modelo atualizado através da implementação do modelo ARM para criar um novo modelo de livro na região desejada.

## <a name="verify"></a>Verificação

Utilize os livros de trabalho Azure para localizar a UI recentemente implantada. Certifique-se de que a localização é a localização do alvo.

## <a name="clean-up"></a>Limpeza

Uma vez criado o seu modelo de livro na nova região, elimine o modelo original de livro na região anterior.
1. Encontre o modelo de livro nos livros de trabalho Azure Navega na UI.
2. Selecione o modelo de livro para eliminar.
3. Selecione o comando "Eliminar".

Se renomeou o seu modelo de livro para o importar para uma nova região, pode renomear o modelo de livro para o nome anterior depois de o item original ter sido eliminado utilizando o comando "Renomear" na vista do modelo do livro Azure.

## <a name="next-steps"></a>Passos Seguintes

Precisa mover um livro em vez de um modelo? Veja como [mover um Livro de Azure para outra região.](./workbooks-move-region.md)

