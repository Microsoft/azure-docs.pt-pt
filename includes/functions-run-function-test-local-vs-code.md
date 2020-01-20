---
title: incluir ficheiro
description: incluir ficheiro
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/31/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: ebcbe7e8af7e01a9b7e75282be4e4f4ceb80b806
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279416"
---
## <a name="run-the-function-locally"></a>Executar localmente a função

As Ferramentas de Núcleo das Funções do Azure permitem-lhe executar um projeto de funções do Azure no seu computador de programação local.

1. Para testar a sua função, defina um ponto de interrupção no código de função e prima F5 para iniciar o projeto da aplicação de funções. São apresentados os resultados das Ferramentas de Núcleo no painel **Terminal**.

1. No painel **Terminal**, copie o ponto final do URL da sua função acionada por HTTP. 

    ![Resultado local do Azure](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Cole o URL do pedido HTTP na barra de endereço do browser. Anexe a cadeia de consulta `?name=<yourname>` a este URL e execute o pedido. A execução é colocada em pausa quando o ponto de interrupção é atingido.

1. Quando continua a execução, a imagem abaixo mostra a resposta no browser ao pedido GET:

    ![Resposta da função localhost no browser](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Para parar a depuração, prima Shift + F5.
