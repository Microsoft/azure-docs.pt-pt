---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: azure-netapp-files
author: b-juche
ms.service: azure-netapp-files
ms.topic: include
ms.date: 09/10/2019
ms.author: b-juche
ms.custom: include file
ms.openlocfilehash: e78be737ef8d191cddc2da0f738250ca04cae9a1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91672152"
---
1. Especificar a subscrição que foi aprovada para ficheiros Azure NetApp:
    
    ```azurecli-interactive
    az account set --subscription <subscriptionId>
    ```

1. Registe-se o Fornecedor de Recursos Azure: 
    
    ```azurecli-interactive
    az provider register --namespace Microsoft.NetApp --wait  
    ```