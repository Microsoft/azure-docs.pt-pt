---
author: baanders
description: incluir arquivo para limpeza de uma instância Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: 9a02c4f5c5699b4a6308bfaa519fa9eb776414d6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244731"
---
* **Se não necessitar de nenhum dos recursos que criou neste tutorial,** pode eliminar a instância Azure Digital Twins e todos os outros recursos deste artigo com o comando de exclusão do [grupo AZ.](/cli/azure/group#az-group-delete) Isto elimina todos os recursos da Azure num grupo de recursos, bem como o próprio grupo de recursos.
    
    > [!IMPORTANT]
    > A eliminação de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos nele contidos são eliminados permanentemente. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados.
    
    Abra [o Azure Cloud Shell](https://shell.azure.com)e execute o seguinte comando para eliminar o grupo de recursos e tudo o que contém.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```