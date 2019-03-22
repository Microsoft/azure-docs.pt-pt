---
title: Utilizar etiquetas personalizadas para moderação de conteúdos - Content Moderator
titlesuffix: Azure Cognitive Services
description: O Content Moderator inclui etiquetas predefinidas e, é possível criar etiquetas personalizadas para moderadores conteúdo específico para o seu negócio.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: fa3b7be6ac7cc398685bf5ad7d21173bcb072b69
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58118413"
---
# <a name="create-and-use-moderation-tags"></a>Criar e utilizar etiquetas de moderação

Além das etiquetas de predefinidos **isadult** (**uma**) e **isracy** (**r**), pode criar etiquetas personalizadas para mais direcionada a análise. Estas etiquetas personalizadas, em seguida, estão disponíveis para os revisores humanos atribuir a imagens ou texto.

## <a name="create-tags"></a>Criar etiquetas

1. Selecione etiquetas a partir do separador Definições.

   ![Etiquetas de moderação de conteúdos](images/tags-1.png)

2. Introduza um código de curto de duas letras para a marca.
3. Introduza um nome para a marca. Mantenha o nome curto e descritivo. Por exemplo, **isbullying**.
4. Introduza uma descrição.
5. Clique em Adicionar.
6. Quando tiver terminado de criar etiquetas, clique em Guardar.

![Definir etiquetas de moderação de conteúdos](images/tags-2-define.png)

## <a name="using-custom-tags"></a>Utilizar etiquetas personalizadas

Etiquetas personalizadas são utilizadas durante a revisão humana. Serão apresentados na pré-visualização e o revisor seleciona-lo ao clicar no mesmo.

![Utilizar etiquetas de moderação de conteúdos](images/tags-3-use.png)

Pode desativar etiquetas diferentes para diferentes revisões, marcando ou desmarcando está visível.
 
![Desativar etiquetas de moderação de conteúdos](images/tags-4-disable.png)

Enquanto não é possível eliminar as etiquetas de predefinidos **isadult** e **isracy**, pode eliminar quaisquer etiquetas personalizadas definidas. Clique a lata de lixo junto à etiqueta que pretende eliminar.

![A eliminar etiquetas de moderação de conteúdos](images/tags-5-delete.png)

## <a name="next-steps"></a>Passos Seguintes

Para saber como utilizar etiquetas para moderação de imagens, consulte [revisão moderado imagens](Review-Moderated-Images.md).
