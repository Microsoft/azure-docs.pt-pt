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
ms.openlocfilehash: 6c430f22a9d4fa0fad95bcaa41675545fffd91ec
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58227223"
---
O modelo do projeto das Funções do Azure no Visual Studio cria um projeto que pode ser publicado numa aplicação de funções no Azure. Pode utilizar uma aplicação de funções para funções de grupo como uma unidade lógica para gestão, implementação e partilha de recursos.

1. No Visual Studio, sobre o **arquivo** menu, selecione **New** > **projeto**.

2. Na **novo projeto** caixa de diálogo, selecione **instalado** > **Visual C#**   >  **Cloud**  >  **As funções do azure**. Introduza um nome para o seu projeto e selecione **OK**. O nome da aplicação de funções deve ser válido como um espaço de nomes C#. Portanto, não use sublinhados, hífenes ou outros carateres não alfanuméricos.

    ![Caixa de diálogo novo projeto para criar uma função no Visual Studio](./media/functions-vstools-create/functions-vs-new-project.png)

3. Utilize as definições especificadas na tabela a seguir à imagem.

    ![Caixa de diálogo de função de novo no Visual Studio](./media/functions-vstools-create/functions-vs-new-function.png) 

    | Definição      | Valor sugerido  | Descrição                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Versão** | Funções do Azure 2.x <br />(.NET Core) | Esta definição cria um projeto de função que utiliza a versão 2.x do runtime das funções do Azure, que suporta o .NET Core. As Funções do Azure 1.x suportam o .NET Framework. Para obter mais informações, consulte [versão de runtime das funções do Azure de destino](../articles/azure-functions/functions-versions.md).   |
    | **Modelo** | Acionador HTTP | Esta definição cria uma função acionada por um pedido HTTP. |
    | **Conta de armazenamento**  | Emulador de armazenamento | Um acionador HTTP não usa a ligação de conta de armazenamento do Azure. Todos os outros tipos de acionadores requerem uma cadeia de ligação da conta de Armazenamento válida. |
    | **Direitos de acesso** | Anónimo | A função criada pode ser acionada por qualquer cliente sem fornecer uma chave. Esta definição de autorização torna mais fácil testar a função nova. Para obter mais informações sobre chaves e a autorização, veja [Chaves de autorização](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys), no artigo [Enlaces HTTP e webhook](../articles/azure-functions/functions-bindings-http-webhook.md). |
    
    > [!NOTE]
    > Certifique-se de definir o **direitos de acesso** para `Anonymous`. Se escolher o nível padrão `Function`, tem de apresentar o [tecla de função](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) nos pedidos para o ponto final de função de acesso.
    
4. Selecione **OK** para criar o projeto de função e uma função acionada por HTTP.
