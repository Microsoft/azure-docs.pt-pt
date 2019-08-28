---
title: Criar uma aplicação de funções a partir do Portal do Azure | Microsoft Docs
description: Crie uma nova aplicação de funções no Serviço de Aplicações do Azure a partir do portal.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: quickstart
ms.date: 04/11/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: eb362ec652b306a12a41e7e96dcbc86638369c17
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085898"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Criar uma aplicação de funções a partir do portal do Azure

As Aplicações das Funções do Azure utilizam a infraestrutura do Serviço de Aplicações do Azure. Este tópico mostra como criar uma aplicação de funções no portal do Azure. Uma aplicação de funções é o contentor que aloja a execução das funções individuais. Quando cria uma aplicação de funções no plano de alojamento do Serviço de Aplicações, a sua aplicação de função pode utilizar todas as funcionalidades do Serviço de Aplicações.

## <a name="create-a-function-app"></a>Criar uma aplicação de função

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Quando criar uma aplicação de funções, indique um **Nome da aplicação** válido, que contenha apenas letras, números e hífenes. O caráter de sublinhado ( **_** ) não é um caráter permitido.

Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas. O nome da sua conta do Storage tem de ser exclusivo no Azure. 

Depois da aplicação de funções estar criada, pode criar funções individuais em um ou mais idiomas diferentes. Crie funções [através do portal](functions-create-first-azure-function.md#create-function), da [implementação contínua](functions-continuous-deployment.md) ou ao [carregar com FTP](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).

## <a name="service-plans"></a>Planos do serviço

Azure Functions tem dois planos de serviço diferentes: Plano de consumo e plano do serviço de aplicativo. O plano de Consumo aloca automaticamente a capacidade de computação quando o código está em execução, aumenta horizontalmente conforme necessário para processar a carga e, em seguida, reduz horizontalmente quando o código não está em execução. O plano do Serviço de Aplicações concede acesso à aplicação de funções a todas as instalações do Serviço de Aplicações. Tem de escolher o plano de serviço quando a aplicação de funções é criada e não pode ser alterada atualmente. Para obter mais informações, veja [Choose an Azure Functions hosting plan (Escolher um plano de alojamento das Funções do Azure)](functions-scale.md) .

Se estiver a planear executar funções do JavaScript num plano do Serviço de Aplicações, deve escolher um plano com menos núcleos. Para obter mais informações, veja [JavaScript reference for Functions (Referência do JavaScript para Funções)](functions-reference-node.md#choose-single-vcpu-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento

Quando criar uma aplicação de funções no Serviço de Aplicações, tem de criar ou ligar a uma conta de Armazenamento do Azure para fins gerais que suporte o armazenamento de Blobs, Filas e Tabelas. Internamente, as Funções utilizam o Armazenamento para operações como a gestão de acionadores e execuções de função de registo. Algumas contas de armazenamento não suportam filas e tabelas, como as contas de armazenamento apenas de blobs, do Armazenamento Premium do Azure e contas de armazenamento para fins gerais com replicação ZRS. Estas contas são filtradas do painel da Conta de Armazenamento ao criar uma aplicação de funções.

>[!NOTE]
>Ao utilizar o plano de Consumo de alojamento, os ficheiros de configuração de enlace e código de função são armazenados no armazenamento de Ficheiro do Azure na conta de armazenamento principal. Ao eliminar a conta de armazenamento principal, este conteúdo é eliminado e não pode ser recuperado.

Para saber mais sobre os tipos de conta de armazenamento, veja [Introdução dos Serviços de Armazenamento do Azure](../storage/common/storage-introduction.md#azure-storage-services). 

## <a name="next-steps"></a>Passos Seguintes

Embora o portal do Azure facilite a criação e a experimentação de funções, recomendamos o [desenvolvimento local](functions-develop-local.md). Depois de criar um aplicativo de funções no portal, você ainda precisará adicionar uma função. 

> [!div class="nextstepaction"]
> [Adicionar uma função disparada por HTTP](functions-create-first-azure-function.md#create-function)
