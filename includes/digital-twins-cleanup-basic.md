---
author: baanders
description: incluir arquivo para limpeza de uma instância Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: c3c1b814b357a2e4b724590261657e485852f99c
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575704"
---
* **Se não necessitar de nenhum dos recursos que criou neste tutorial,** pode eliminar a instância Azure Digital Twins e todos os outros recursos deste artigo com o comando de exclusão do [grupo AZ.](/cli/azure/group?preserve-view=true&view=azure-cli-latest#az-group-delete) Isto elimina todos os recursos da Azure num grupo de recursos, bem como o próprio grupo de recursos.
    
    > [!IMPORTANT]
    > A eliminação de um grupo de recursos é irreversível. O grupo de recursos e todos os recursos nele contidos são eliminados permanentemente. Confirme que não elimina acidentalmente o grupo de recursos ou recursos errados.
    
    Abra [o Azure Cloud Shell](https://shell.azure.com)e execute o seguinte comando para eliminar o grupo de recursos e tudo o que contém.
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```