---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 06/04/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 164620bdcee7ac546468354f999dcb3ad96ecf4b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84731046"
---
O modelo de projeto Azure Functions no Visual Studio cria um projeto que pode publicar para uma aplicação de função em Azure. Pode utilizar uma aplicação de função para agrupar funções como uma unidade lógica para facilitar a gestão, implementação, escala e partilha de recursos.

1. A partir do menu Estúdio Visual, selecione **File**  >  **New**  >  **Project**.

1. Em **Criar um novo projeto,** introduzir *funções* na caixa de pesquisa, escolher o modelo **Azure Functions** e, em seguida, selecionar **Seguinte**.

1. Em **Configurar o seu novo projeto,** insira um **nome de Projeto** para o seu projeto e, em seguida, selecione **Create**. O nome da aplicação de funções deve ser válido como um espaço de nomes C#. Portanto, não use sublinhados, hífenes ou outros carateres não alfanuméricos.

1. Para criar **uma nova definição de aplicação Azure Functions,** utilize os valores no quadro seguinte:

    | Definição      | Valor  | Descrição                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Tempo de execução das funções** | **Funções Azure v3 <br /> (.NET Core)** | Este valor cria um projeto de função que utiliza a versão 3.x runtime das Funções Azure, que suporta .NET Core 3.x. As Funções do Azure 1.x suportam o .NET Framework. Para obter mais informações, consulte [as versões de tempo de execução do Azure Functions](../articles/azure-functions/functions-versions.md).   |
    | **Modelo de função** | **Acionador HTTP** | Este valor cria uma função desencadeada por um pedido HTTP. |
    | **Conta de armazenamento (AzureWebJobsStorage)**  | **Emulador de armazenamento** | Como uma Função Azure requer uma conta de armazenamento, uma é atribuída ou criada quando publica o seu projeto ao Azure. Um gatilho HTTP não utiliza uma cadeia de ligação de conta Azure Storage; todos os outros tipos de gatilho requerem uma cadeia de ligação válida da conta Azure Storage.  |
    | **Nível de autorização** | **Anónimo** | A função criada pode ser acionada por qualquer cliente sem fornecer uma chave. Esta definição de autorização torna mais fácil testar a função nova. Para obter mais informações sobre chaves e autorização, consulte [as chaves de autorização](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) e as [ligações HTTP e webhook](../articles/azure-functions/functions-bindings-http-webhook.md). |
    
    
    ![Definições de projeto de funções Azure](./media/functions-vs-tools-create/functions-project-settings.png)

    Certifique-se de que define o **nível de Autorização** para **Anónimo**. Se escolher o nível predefinido de **Função,** é-lhe exigido que apresente a [tecla de função](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) nos pedidos de acesso ao ponto final da função.

1. Selecione **Criar** para criar o projeto de função e a função de gatilho HTTP.
