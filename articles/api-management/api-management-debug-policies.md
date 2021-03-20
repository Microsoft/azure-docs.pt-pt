---
title: Debug Azure API Políticas de Gestão em Código de Estúdio Visual | Microsoft Docs
description: Saiba como depurar as políticas de gestão da API da Azure utilizando a extensão do Código do Estúdio Visual de Gestão de API da Azure
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/22/2020
ms.author: apimpm
ms.openlocfilehash: 2e45d1274cf7332dbca70eaa8fc51f0ac98e5359
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648021"
---
# <a name="debug-azure-api-management-policies-in-visual-studio-code"></a>Debug Azure API Políticas de Gestão em Código de Estúdio Visual

[As políticas](api-management-policies.md) na Azure API Management fornecem capacidades poderosas que ajudam os editores da API a lidar com preocupações transversais como a autenticação, autorização, estrangulamento, caching e transformação. As políticas são uma coleção de instruções que são executadas sequencialmente no pedido ou na resposta de uma API. 

Este artigo descreve como depurar políticas de gestão da API utilizando a [Extensão de Gestão da API Azure para o Código do Estúdio Visual.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement) 

## <a name="prerequisites"></a>Pré-requisitos

* Crie uma instância de desenvolvimento de gestão de API seguindo este [arranque rápido](get-started-create-service-instance.md) primeiro.

* Instale o [Código do Estúdio Visual](https://code.visualstudio.com/) e a versão mais recente da Extensão de Gestão API da [Azure para código de estúdio visual.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement) 

* Importe uma API para o seu caso de Gestão de API. Por exemplo, ver [Tutorial: Utilize a Extensão de Gestão da API para código de estúdio visual para importar e gerir APIs](visual-studio-code-tutorial.md).

## <a name="restrictions-and-limitations"></a>Restrições e limitações

Esta funcionalidade só está disponível no nível de Desenvolvimento da Gestão da API. Cada instância de Gestão da API suporta apenas uma sessão de depuragem simultânea.

## <a name="initiate-a-debugging-session"></a>Iniciar uma sessão de depuragem

1. Abra o Visual Studio Code.
2. Navegue para a extensão de Gestão da API ao abrigo de extensões Azure
3. Encontre o caso de Gestão da API para depurar
4. Encontre a API e a operação para depurar
5. Clique no botão direito na operação e selecione **Iniciar depuragem de política**

Neste momento, a extensão tentará iniciar e estabelecer uma sessão de depurar com o gateway de Gestão da API.

![iniciar a depuragem](media/api-management-debug-policies/initiate-debugging-session.png)

## <a name="send-a-test-request"></a>Enviar um pedido de teste
Quando a sessão de depurar estiver estabelecida, a extensão abrirá um novo editor que nos permite criar e enviar um pedido HTTP de teste a esta operação aproveitando a [extensão](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)do CLIENTE REST .

Você vai notar que o cabeçalho **Ocp-Apim-Debug** já foi adicionado ao pedido. Este cabeçalho é necessário e o valor deve ser definido para a chave de subscrição de nível de serviço, de acesso total, para desencadear a funcionalidade de depurar no gateway de Gestão API.

Modifique o pedido HTTP no editor de acordo com o seu cenário de teste. Em seguida, clique em enviar o **pedido** de teste para o gateway de Gestão da API.

![enviar um pedido de teste](media/api-management-debug-policies/rest-client.png)

## <a name="debug-policies"></a>Políticas de depuração
Após o envio do pedido HTTP de teste, a extensão abrirá a janela de depurar mostrando as políticas eficazes desta operação e para na primeira política eficaz. 

![depurar políticas](media/api-management-debug-policies/main-window.png)

Para seguir o oleoduto da política, pode passar por políticas individuais ou definir um ponto de rutura numa política e avançar diretamente para essa política. 

No painel **De Variáveis,** pode inspecionar valores de variáveis criadas pelo sistema e criadas pelo utilizador. No painel **Breakpoints,** pode ver a lista de todos os pontos de rutura que foram definidos. No painel **'Call Stack',** pode ver o atual âmbito de política eficaz. 

Se houver um erro durante a execução da política, verá os detalhes do erro na apólice onde aconteceu. 

![exceções](media/api-management-debug-policies/exception.png)

> [!TIP]
> Lembre-se de sair da sessão de depuração clicando no botão **Stop** quando terminar.


## <a name="next-steps"></a>Passos seguintes

+ Saiba mais sobre a [extensão de Gestão da API para Código de Estúdio Visual.](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement) 
+ Problemas de relatório no [repositório do GitHub](https://github.com/Microsoft/vscode-apimanagement)

