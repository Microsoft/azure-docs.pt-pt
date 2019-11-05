---
title: incluir ficheiro
description: incluir ficheiro
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 10/22/2019
ms.openlocfilehash: 5a66212122745d0f4426e48e9487e9d674cec53f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489973"
---
>[!IMPORTANT]
>Você pode usar os recursos que criou como pré-requisitos para outros tutoriais de Azure Machine Learning e artigos de instruções.

### <a name="delete-everything"></a>Excluir tudo

Se você não planeja usar nada que criou, exclua o grupo de recursos inteiro para não incorrer em encargos:

1. No portal do Azure, selecione **grupos de recursos** no lado esquerdo da janela.
 
   ![Eliminar grupo de recursos no portal do Azure](./media/aml-ui-cleanup/delete-resources.png)

1. Na lista, selecione o grupo de recursos que você criou.

1. No lado direito da janela, selecione o botão de reticências ( **...** ).

1. Selecione **Eliminar grupo de recursos**.

Excluir o grupo de recursos também exclui todos os recursos que você criou no designer.  

### <a name="delete-only-the-compute-target"></a>Excluir somente o destino de computação

O destino de computação que você criou aqui é *automaticamente dimensionado* para zero nós quando ele não está sendo usado. Isso é para minimizar os encargos. Se você quiser excluir o destino de computação, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com), abra seu espaço de trabalho.

    ![Excluir o destino de computação](./media/aml-ui-cleanup/delete-compute-target.png)

1. Na seção **computação** do seu espaço de trabalho, selecione o recurso.

1. Selecione **Eliminar**.

### <a name="delete-individual-assets"></a>Excluir ativos individuais

No designer em que você criou o experimento, exclua ativos individuais selecionando-os e, em seguida, selecionando o botão **excluir** .

![Excluir ativos](./media/aml-ui-cleanup/delete-asset.png)

Os conjuntos de valores podem ter o registro cancelado do seu espaço de trabalho selecionando cada conjunto de registros e selecionando **Cancelar registro**.

![Cancelar registro do conjunto de registros](./media/aml-ui-cleanup/unregister-dataset.png)


