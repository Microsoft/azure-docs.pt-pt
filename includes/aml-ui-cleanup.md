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
ms.openlocfilehash: 754c9799ed4c2fd90cbcf1e9717b0be21edd54cb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75659882"
---
>[!IMPORTANT]
>Pode utilizar os recursos que criou como pré-requisitos para outros tutoriais de Aprendizagem automática e artigos de como fazer.

### <a name="delete-everything"></a>Apagar tudo

Se não planeia usar nada do que criou, elimine todo o grupo de recursos para não incorrer em quaisquer encargos.

1. No portal Azure, selecione **Grupos de Recursos** no lado esquerdo da janela.
 
   ![Eliminar grupo de recursos no portal do Azure](./media/aml-ui-cleanup/delete-resources.png)

1. Na lista, selecione o grupo de recursos que criou.

1. Selecione **Eliminar grupo de recursos**.

A eliminação do grupo de recursos também elimina todos os recursos que criou no designer. 

### <a name="delete-individual-assets"></a>Eliminar ativos individuais

No designer onde criou a sua experiência, elimine os ativos individuais selecionando-os e selecionando-os e selecionando o botão **Delete.**

The compute target that you created here *automatically autoscales* to zero nodes when it's not being used. Esta ação é tomada para minimizar as acusações.Se pretender eliminar o alvo do cálculo, tome estas medidas:

![Eliminar ativos](./media/aml-ui-cleanup/delete-asset.png)

Pode desregistar conjuntos de dados do seu espaço de trabalho selecionando cada conjunto de dados e selecionando **Não registar**.

![Desregistar conjunto de dados](./media/aml-ui-cleanup/unregister-dataset1225.png)

Para eliminar um conjunto de dados, aceda à conta de armazenamento utilizando o portal Azure ou o Azure Storage Explorer e elimine manualmente esses ativos.


