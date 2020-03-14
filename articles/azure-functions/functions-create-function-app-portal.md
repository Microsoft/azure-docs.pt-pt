---
title: Criar uma aplicação de função a partir do Portal Azure
description: Crie uma nova aplicação de funções em Azure a partir do portal.
ms.topic: conceptual
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 086a543e75d083094d4dfa789e71afaba5da6d6e
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79368796"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Criar uma aplicação de funções a partir do portal do Azure

Este tópico mostra como usar as Funções Azure para criar uma aplicação de função no portal Azure. Uma aplicação de funções é o contentor que aloja a execução das funções individuais. 

## <a name="create-a-function-app"></a>Criar uma aplicação de função

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Depois da aplicação de funções estar criada, pode criar funções individuais em um ou mais idiomas diferentes. Crie funções [através do portal](functions-create-first-azure-function.md#create-function), da [implementação contínua](functions-continuous-deployment.md) ou ao [carregar com FTP](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).

## <a name="service-plans"></a>Planos do serviço

A Azure Functions tem três planos de serviço diferentes: plano de consumo, plano Premium e plano dedicado (App Service). Tem de escolher o seu plano de serviço quando a sua aplicação de funções for criada e posteriormente não poderá ser alterada. Para obter mais informações, veja [Choose an Azure Functions hosting plan (Escolher um plano de alojamento das Funções do Azure)](functions-scale.md) .

Se está a planear executar funções JavaScript num plano dedicado (App Service), deve escolher um plano com menos núcleos. Para obter mais informações, veja [JavaScript reference for Functions (Referência do JavaScript para Funções)](functions-reference-node.md#choose-single-vcpu-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento

Ao criar uma aplicação de função, deve criar ou ligar-se a uma conta de Armazenamento Azure de fins gerais que suporte o armazenamento de Blob, Queue e Mesa. Internamente, as Funções utilizam o Armazenamento para operações como a gestão de acionadores e execuções de função de registo. Algumas contas de armazenamento não suportam filas e tabelas, como as contas de armazenamento apenas de blobs, do Armazenamento Premium do Azure e contas de armazenamento para fins gerais com replicação ZRS. Estas contas são filtradas do painel da Conta de Armazenamento ao criar uma aplicação de funções.

>[!NOTE]
>Ao utilizar o plano de Consumo de alojamento, os ficheiros de configuração de enlace e código de função são armazenados no armazenamento de Ficheiro do Azure na conta de armazenamento principal. Ao eliminar a conta de armazenamento principal, este conteúdo é eliminado e não pode ser recuperado.

Para saber mais sobre os tipos de conta de armazenamento, veja [Introdução dos Serviços de Armazenamento do Azure](../storage/common/storage-introduction.md#azure-storage-services). 

## <a name="next-steps"></a>Passos seguintes

Enquanto o portal Azure facilita a criação e experimenta funções, recomendamos o [desenvolvimento local.](functions-develop-local.md) Depois de criar uma aplicação de função no portal, ainda precisa adicionar uma função. 

> [!div class="nextstepaction"]
> [Adicione uma função ativada http](functions-create-first-azure-function.md#create-function)
