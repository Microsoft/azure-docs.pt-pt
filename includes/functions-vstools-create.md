---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3d93d3aa3e4e646f8e054f96f17bbe4a011d422d
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211409"
---
O modelo do projeto das Funções do Azure no Visual Studio cria um projeto que pode ser publicado numa aplicação de funções no Azure. Você pode usar uma aplicação de função para agrupar funções como uma unidade lógica para uma gestão mais fácil, implementação, escalagem e partilha de recursos.

1. No Estúdio Visual, no menu **File,** selecione **New** > **Project**.

1. Na caixa de diálogo **Criar um novo projeto,** procure `functions`, escolha o modelo **funções Azure** e selecione **Next**.

1. Insira um nome para o seu projeto e selecione **Criar**. O nome da aplicação de funções deve ser válido como um espaço de nomes C#. Portanto, não use sublinhados, hífenes ou outros carateres não alfanuméricos.

1. Na **Create uma nova aplicação funções Azure,** utilize as seguintes opções:

    + **Funções Azure v2 (.NÚCLEO NET)**
    + **Gatilho HTTP**
    + **Conta de Armazenamento**: **Emulador** de Armazenamento
    + **Nível de autorização**: **Anónimo** 

    | Opção      | Valor sugerido  | Descrição                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Funções tempo de funcionamento** | **Funções Azure 2.x <br />(.NET Core)** | Esta definição cria um projeto de função que utiliza o tempo de execução da versão 2.x das Funções Azure, que suporta o Núcleo .NET. As Funções do Azure 1.x suportam o .NET Framework. Para mais informações, consulte a versão de tempo de execução das [Funções Target Azure](../articles/azure-functions/functions-versions.md).   |
    | **Modelo de função** | **Gatilho HTTP** | Esta definição cria uma função desencadeada por um pedido HTTP. |
    | **Storage Account**  | **Emulador de armazenamento** | Um gatilho HTTP não utiliza a ligação da conta Azure Storage. Todos os outros tipos de acionadores requerem uma cadeia de ligação da conta de Armazenamento válida. Como as Funções requerem uma conta de armazenamento, uma é atribuída ou criada quando publica o seu projeto para o Azure. |
    | **Nível de autorização** | **Anónimo** | A função criada pode ser acionada por qualquer cliente sem fornecer uma chave. Esta definição de autorização torna mais fácil testar a função nova. Para obter mais informações sobre chaves e a autorização, veja [Chaves de autorização](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys), no artigo [Enlaces HTTP e webhook](../articles/azure-functions/functions-bindings-http-webhook.md). |
    
    > [!NOTE]
    > Certifique-se de que fixa o **nível** de Autorização para `Anonymous`. Se escolher o nível padrão de `Function`, é necessário apresentar a chave de [função](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) nos pedidos para aceder ao ponto final da sua função.
    
4. Selecione **Criar** para criar o projeto de função e função desencadeada por HTTP.
