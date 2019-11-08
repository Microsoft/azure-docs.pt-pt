---
title: incluir ficheiro
description: incluir ficheiro
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 11/06/2019
ms.openlocfilehash: 66f5c72fcabb62e21f0110cb981b7271244c0648
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73800059"
---
>[!IMPORTANT]
>Você pode usar os recursos que criou como pré-requisitos para outros tutoriais de Azure Machine Learning e artigos de instruções.

### <a name="delete-everything"></a>Excluir tudo

Se você não planeja usar nada que criou, exclua o grupo de recursos inteiro para não incorrer em encargos:

1. No portal do Azure, selecione **grupos de recursos** no lado esquerdo da janela.
 
   ![Eliminar grupo de recursos no portal do Azure](./media/aml-ui-cleanup/delete-resources.png)

1. Na lista, selecione o grupo de recursos que você criou.

1. Selecione **Eliminar grupo de recursos**.

Excluir o grupo de recursos também exclui todos os recursos que você criou no designer.  

### <a name="delete-individual-assets"></a>Excluir ativos individuais

No designer em que você criou o experimento, exclua ativos individuais selecionando-os e, em seguida, selecionando o botão **excluir** .

O destino de computação que você criou aqui é *automaticamente dimensionado* para zero nós quando ele não está sendo usado. Isso é para minimizar os encargos. Se você quiser excluir o destino de computação, siga estas etapas:

![Excluir ativos](./media/aml-ui-cleanup/delete-asset.png)

Os conjuntos de valores podem ter o registro cancelado do seu espaço de trabalho selecionando cada conjunto de registros e selecionando **Cancelar registro**.

![Cancelar registro do conjunto de registros](./media/aml-ui-cleanup/unregister-dataset.png)

Para excluir um conjunto de um, navegue até a conta de armazenamento usando o portal do Azure ou o Gerenciador de Armazenamento e exclua manualmente esses ativos.


