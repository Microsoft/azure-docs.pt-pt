---
title: Criar uma aplicação de funções a partir do portal do Azure
description: Crie uma nova aplicação de função em Azure a partir do portal.
ms.topic: how-to
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 001b4e4f0ea7fbacd232b2a87abfe353f34919bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80985019"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Criar uma aplicação de funções a partir do portal do Azure

Este tópico mostra-lhe como usar as Funções Azure para criar uma aplicação de função no portal Azure. Uma aplicação de funções é o contentor que aloja a execução das funções individuais. 

## <a name="create-a-function-app"></a>Criar uma aplicação de funções

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Depois da aplicação de funções estar criada, pode criar funções individuais em um ou mais idiomas diferentes. Crie funções [através do portal](functions-create-first-azure-function.md#create-function), da [implementação contínua](functions-continuous-deployment.md) ou ao [carregar com FTP](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).

## <a name="service-plans"></a>Planos do serviço

A Azure Functions tem três planos de serviço diferentes: Plano de consumo, plano Premium e plano dedicado (Serviço de Aplicações). Tem de escolher o seu plano de serviço quando a sua aplicação de função é criada, e não pode ser alterada posteriormente. Para obter mais informações, veja [Choose an Azure Functions hosting plan (Escolher um plano de alojamento das Funções do Azure)](functions-scale.md) .

Se está a planear executar funções JavaScript num plano dedicado (Serviço de Aplicações), deve escolher um plano com menos núcleos. Para obter mais informações, veja [JavaScript reference for Functions (Referência do JavaScript para Funções)](functions-reference-node.md#choose-single-vcpu-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento

Ao criar uma aplicação de função, deve criar ou ligar para uma conta de Armazenamento Azure de uso geral que suporte o armazenamento de Blob, Queue e Table. Internamente, as Funções utilizam o Armazenamento para operações como a gestão de acionadores e execuções de função de registo. Algumas contas de armazenamento não suportam filas e tabelas, como as contas de armazenamento apenas de blobs, do Armazenamento Premium do Azure e contas de armazenamento para fins gerais com replicação ZRS. Estas contas são filtradas do painel da Conta de Armazenamento ao criar uma aplicação de funções.

>[!NOTE]
>Ao utilizar o plano de Consumo de alojamento, os ficheiros de configuração de enlace e código de função são armazenados no armazenamento de Ficheiro do Azure na conta de armazenamento principal. Ao eliminar a conta de armazenamento principal, este conteúdo é eliminado e não pode ser recuperado.

Para saber mais sobre os tipos de conta de armazenamento, veja [Introdução dos Serviços de Armazenamento do Azure](../storage/common/storage-introduction.md#core-storage-services). 

## <a name="next-steps"></a>Passos seguintes

Enquanto o portal Azure facilita a criação e experimentação de Funções, recomendamos [o desenvolvimento local.](functions-develop-local.md) Depois de criar uma aplicação de função no portal, ainda precisa de adicionar uma função. 

> [!div class="nextstepaction"]
> [Adicione uma função detonada HTTP](functions-create-first-azure-function.md#create-function)
