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
ms.openlocfilehash: a27d44a7684b4ad3d39d7fba50ca52e08e932971
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769778"
---
O modelo do projeto das Funções do Azure no Visual Studio cria um projeto que pode ser publicado numa aplicação de funções no Azure. Você pode usar um aplicativo de função para agrupar funções como uma unidade lógica para facilitar o gerenciamento, a implantação, o dimensionamento e o compartilhamento de recursos.

1. No Visual Studio, no menu **arquivo** , selecione **novo** **projeto**de > .

1. Na caixa de diálogo **criar um novo projeto** , procure `functions`, escolha o modelo de **Azure Functions** e selecione **Avançar**.

1. Insira um nome para seu projeto e selecione **criar**. O nome da aplicação de funções deve ser válido como um espaço de nomes C#. Portanto, não use sublinhados, hífenes ou outros carateres não alfanuméricos.

1. Em **criar um novo aplicativo Azure Functions**, use as seguintes opções:

    + **Azure Functions v2 (.NET Core)**
    + **Gatilho HTTP**
    + **Conta de armazenamento**: **emulador de armazenamento**
    + **Nível de autorização**: **anônimo** 

    | Opção      | Valor sugerido  | Descrição                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Tempo de execução de funções** | **<br />Azure Functions 2. x (.NET Core)** | Essa configuração cria um projeto de função que usa o tempo de execução da versão 2. x de Azure Functions, que dá suporte ao .NET Core. As Funções do Azure 1.x suportam o .NET Framework. Para obter mais informações, consulte [Target Azure Functions Runtime versão](../articles/azure-functions/functions-versions.md).   |
    | **Modelo de função** | **Gatilho HTTP** | Essa configuração cria uma função disparada por uma solicitação HTTP. |
    | **Storage Account**  | **Emulador de armazenamento** | Um gatilho HTTP não usa a conexão da conta de armazenamento do Azure. Todos os outros tipos de acionadores requerem uma cadeia de ligação da conta de Armazenamento válida. Como as funções exigem uma conta de armazenamento, uma é atribuída ou criada quando você publica seu projeto no Azure. |
    | **Nível de autorização** | **Modo** | A função criada pode ser acionada por qualquer cliente sem fornecer uma chave. Esta definição de autorização torna mais fácil testar a função nova. Para obter mais informações sobre chaves e a autorização, veja [Chaves de autorização](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys), no artigo [Enlaces HTTP e webhook](../articles/azure-functions/functions-bindings-http-webhook.md). |
    
    > [!NOTE]
    > Certifique-se de definir o **nível de autorização** para `Anonymous`. Se você escolher o nível padrão de `Function`, será necessário apresentar a chave de [função](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) em solicitações para acessar seu ponto de extremidade de função.
    
4. Selecione **criar** para criar o projeto de função e a função disparada por http.
