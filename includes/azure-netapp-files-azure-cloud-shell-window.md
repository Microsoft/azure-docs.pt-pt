---
title: incluir ficheiro
description: incluir ficheiro
services: azure-netapp-files
author: b-juche
ms.service: azure-netapp-files
ms.topic: include
ms.date: 09/10/2019
ms.author: b-juche
ms.custom: include file
ms.openlocfilehash: e78be737ef8d191cddc2da0f738250ca04cae9a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
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