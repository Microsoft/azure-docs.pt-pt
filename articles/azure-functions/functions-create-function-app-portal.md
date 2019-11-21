---
title: Create a function app from the Azure Portal
description: Create a new function app in Azure from the portal.
ms.topic: conceptual
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 74eaa6837f362c849277a761da3ae79c3a8ac353
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230778"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Criar uma aplicação de funções a partir do portal do Azure

This topic shows you how to use Azure Functions to create a function app in the Azure portal. Uma aplicação de funções é o contentor que aloja a execução das funções individuais. 

## <a name="create-a-function-app"></a>Criar uma aplicação de função

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Quando criar uma aplicação de funções, indique um **Nome da aplicação** válido, que contenha apenas letras, números e hífenes. O caráter de sublinhado ( **_** ) não é um caráter permitido.

Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas. O nome da sua conta do Storage tem de ser exclusivo no Azure. 

Depois da aplicação de funções estar criada, pode criar funções individuais em um ou mais idiomas diferentes. Crie funções [através do portal](functions-create-first-azure-function.md#create-function), da [implementação contínua](functions-continuous-deployment.md) ou ao [carregar com FTP](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).

## <a name="service-plans"></a>Planos do serviço

Azure Functions has three different service plans: Consumption plan, Premium plan, and Dedicated (App Service) plan. You must choose your service plan when your function app is created, and it cannot subsequently be changed. Para obter mais informações, veja [Choose an Azure Functions hosting plan (Escolher um plano de alojamento das Funções do Azure)](functions-scale.md) .

If you are planning to run JavaScript functions on a Dedicated (App Service) plan, you should choose a plan with fewer cores. Para obter mais informações, veja [JavaScript reference for Functions (Referência do JavaScript para Funções)](functions-reference-node.md#choose-single-vcpu-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento

When creating a function app, you must create or link to a general-purpose Azure Storage account that supports Blob, Queue, and Table storage. Internamente, as Funções utilizam o Armazenamento para operações como a gestão de acionadores e execuções de função de registo. Algumas contas de armazenamento não suportam filas e tabelas, como as contas de armazenamento apenas de blobs, do Armazenamento Premium do Azure e contas de armazenamento para fins gerais com replicação ZRS. Estas contas são filtradas do painel da Conta de Armazenamento ao criar uma aplicação de funções.

>[!NOTE]
>Ao utilizar o plano de Consumo de alojamento, os ficheiros de configuração de enlace e código de função são armazenados no armazenamento de Ficheiro do Azure na conta de armazenamento principal. Ao eliminar a conta de armazenamento principal, este conteúdo é eliminado e não pode ser recuperado.

Para saber mais sobre os tipos de conta de armazenamento, veja [Introdução dos Serviços de Armazenamento do Azure](../storage/common/storage-introduction.md#azure-storage-services). 

## <a name="next-steps"></a>Passos seguintes

While the Azure portal makes it easy to create and try out Functions, we recommend [local development](functions-develop-local.md). After creating a function app in the portal, you still need to add a function. 

> [!div class="nextstepaction"]
> [Add an HTTP triggered function](functions-create-first-azure-function.md#create-function)
