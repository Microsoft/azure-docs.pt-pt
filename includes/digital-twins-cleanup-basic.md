---
author: baanders
description: incluir arquivo para limpeza de uma instância Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: 0d8cc30c0511098caf7b6c47d7f7bd400dc32f1b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800025"
---
* **Se não necessitar de nenhum dos recursos que criou neste tutorial,** pode eliminar a instância Azure Digital Twins e todos os outros recursos deste artigo com o comando de exclusão do [grupo AZ.](/cli/azure/group#az_group_delete) Isto elimina todos os recursos da Azure num grupo de recursos, bem como o próprio grupo de recursos.
    
    > [!IMPORTANT]
    > A eliminação de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos nele contidos são eliminados permanentemente. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados.
    
    Abra [o Azure Cloud Shell](https://shell.azure.com)e execute o seguinte comando para eliminar o grupo de recursos e tudo o que contém.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```