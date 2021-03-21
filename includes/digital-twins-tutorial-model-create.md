---
author: baanders
description: incluir arquivo para Azure Digital Twins - instruções de modelo para tutorial de linha de comando
ms.topic: include
ms.date: 3/5/2021
ms.author: baanders
ms.openlocfilehash: a94b9304ecd6c6630f6ad45652e76d2879bbc1b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463924"
---
1. **Atualize o número da versão**, para indicar que está a fornecer uma versão mais atualizada deste modelo. Faça-o alterando o *1* no final do `@id` valor para um *2*. Qualquer número maior do que o número atual da versão também funcionará.
1. **Editar uma propriedade.** Mude o nome da `Humidity` propriedade para *HumidityLevel* (ou algo diferente, se quiser. Se utilizar algo diferente do *HumidityLevel,* lembre-se do que utilizou e continue a usá-lo em vez de *HumidityLevel* durante todo o tutorial).
1. **Adicione uma propriedade.** Por baixo da `HumidityLevel` propriedade que termina na linha 15, cole o seguinte código para adicionar uma propriedade ao `RoomName` quarto:

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="16-20":::

1. **Adicione uma relação.** Por baixo da `RoomName` propriedade que acabou de adicionar, cole o seguinte código para adicionar a capacidade deste tipo de *gémeos de* formar relações com outros gémeos:

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="21-24":::

Quando terminar, o modelo atualizado deve coincidir com isto:

:::code language="json" source="~/digital-twins-docs-samples/models/Room.json":::

Certifique-se de que guarda o ficheiro antes de seguir em frente.