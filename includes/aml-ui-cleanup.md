---
title: incluir ficheiro
description: incluir ficheiro
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 05/06/2019
ms.openlocfilehash: 623e993dfbe6bbb3297fa6470865ab1a04f55b37
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65920639"
---
>[!IMPORTANT]
>Pode utilizar os recursos que criou como pré-requisitos para outros tutoriais de serviço do Azure Machine Learning e artigos de instruções.


### <a name="delete-everything"></a>Eliminar tudo

Se não planeja usar nada que criou, elimine o grupo de recursos inteiro, pelo que não existem custos:

1. No portal do Azure, selecione **grupos de recursos** no lado esquerdo da janela.
 
   ![Eliminar grupo de recursos no portal do Azure](./media/aml-ui-cleanup/delete-resources.png)

1. Na lista, selecione o grupo de recursos que criou.

1. No lado direito da janela, selecione o botão de reticências (**...** ).

1. Selecione **Eliminar grupo de recursos**.

Eliminar o grupo de recursos também elimina a todos os recursos que criou na interface do visual.  

### <a name="delete-only-the-compute-target"></a>Elimine apenas a computação de destino

O destino de computação que criou aqui *automaticamente é dimensionado automaticamente* para zero nós quando não está a ser utilizado. Isso serve para minimizar os custos. Se pretender eliminar o destino de computação, siga estes passos:

1. Na [portal do Azure](https://portal.azure.com), abra a área de trabalho.

    ![Eliminar o destino de computação](./media/aml-ui-cleanup/delete-compute-target.png)

1. Na **computação** secção da área de trabalho, selecione o recurso.

1. Selecione **Eliminar**.

### <a name="delete-individual-assets"></a>Eliminar recursos individuais

Na interface do visual onde criou a sua experimentação, eliminar recursos individuais, selecionando-os e, em seguida, selecionando o **eliminar** botão.

![Eliminar experimentações](./media/aml-ui-cleanup/delete-experiment.png)
