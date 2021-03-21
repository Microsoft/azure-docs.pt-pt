---
title: Livros de monitorização Azure - Mover Regiões
description: Como mover um livro para uma região diferente
services: azure-monitor
author: gardnerjr
manager: acearun
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/12/2020
ms.author: jgardner
ms.openlocfilehash: d58d9ab48fa16fb5258f097ed4567e539e21c72c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100615981"
---
# <a name="move-an-azure-workbook-to-another-region"></a>Mover um livro de Azure para outra região

Este artigo descreve como mover os recursos do Azure Workbook para uma região de Azure diferente. Pode mover os seus recursos para outra região por várias razões. Por exemplo, aproveitar uma nova região de Azure, para implantar características ou serviços disponíveis apenas em regiões específicas, para satisfazer os requisitos de política interna e governação, ou em resposta aos requisitos de planeamento de capacidades.

## <a name="prerequisites"></a>Pré-requisitos

* Certifique-se de que os livros de trabalho são suportados na região alvo.

* Estas instruções aplicam-se tanto aos livros partilhados ( ) como aos `microsoft.insights/workbooks` livros privados `microsoft.insights/myworkbooks` ( ) guardados no Azure Monitor e na maioria dos tipos de recursos.

  No entanto, para livros especificamente ligados ao tipo de recurso Application Insights, esses livros são armazenados na região de Azure onde o recurso Application Insights é guardado.

  Esses livros não podem ser movidos individualmente para outra região.

## <a name="move"></a>Mover

A forma mais simples de mover um Livro de Trabalho Azure é usar "Save as" na ferramenta Workbooks no Portal UI:

1. Abra o livro-alvo no espectador do livro.
2. Utilize o botão de barra de ferramentas "Editar" para introduzir o modo de edição.
3. Utilize o botão de barra de ferramentas "Save As".
4. Na forma Save, escolha um nome e a região desejada para o livro. Certifique-se de que os outros campos de subscrição, grupo de recursos e partilha são adequados.

   > [!NOTE]
   > Pode ser necessário utilizar um novo nome para o livro para evitar nomes duplicados.

5. Guarde. 

## <a name="verify"></a>Verificação

Utilize os livros de trabalho Azure para localizar a UI para localizar o novo livro. Certifique-se de que a localização é a localização do alvo.

## <a name="clean-up"></a>Limpeza

Uma vez criado o seu livro na nova região, elimine o livro original na região anterior.
1. Abra o livro original no espectador do livro.
2. Utilize o botão de barra de ferramentas "Editar" para introduzir o modo de edição.
3. A partir das ferramentas de edição dropdown (ícone de lápis), escolha "Delete Workbook".

Se renomeou o seu livro para o importar para uma nova região, pode renomear o livro para o nome anterior depois de o livro original ter sido eliminado utilizando o item "Renomear" das ferramentas de edição do Modo de Edição.

## <a name="next-steps"></a>Passos Seguintes

Precisa mover um modelo de livro em vez de um livro? Veja como [mover um modelo de livro Azure para outra região.](./workbook-templates-move-region.md)

Veja como [implementar livros de trabalho e modelos de livro](../visualize/workbooks-automate.md) através de modelos ARM.
