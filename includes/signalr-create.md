---
title: incluir ficheiro
description: incluir ficheiro
services: signalr
author: wesmc7777
ms.service: signalr
ms.topic: include
ms.date: 04/17/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 57407606214d8d3a305476cfbfdabca9eee937e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60690319"
---
1. Para criar um novo recurso do Serviço Azure SignalR, primeiro inicie sessão no [portal do Azure](https://portal.azure.com). No canto superior esquerdo da página, clique em **+ Criar um recurso**. Na caixa de texto **Pesquisar no Marketplace**, escreva **Serviço SignalR** e prima **enter**.

2. Clique em **Serviço SignalR** nos resultados e clique em **Criar**.

3. Na página de definições do **SignalR**, adicione as seguintes definições ao novo recurso do SignalR:

    | Name | Valor recomendado | Descrição |
    | ---- | ----------------- | ----------- |
    | Nome do Recurso | *testsignalr* | Introduza um nome de recurso exclusivo para utilizar no recurso do SignalR. O nome tem de ser uma cadeia entre 1 e 63 carateres e conter apenas números, letras e o caráter `-`. O nome não pode começar nem terminar com o caráter `-` e os carateres `-` consecutivos não são válidos.|
    | Subscrição | Escolher a sua subscrição |  Selecione a subscrição do Azure que pretende utilizar para testar o SignalR. Se a sua conta tiver apenas uma subscrição, esta é selecionada automaticamente e não é apresentado o menu pendente **Subscrição**.|
    | Grupo de recursos | Crie um novo grupo de recursos chamado *SignalRTestResources*| Selecione ou crie um grupo de recursos para o seu recurso SignalR. Este grupo é útil para organizar os vários recursos que pretende eliminar em simultâneo, ao eliminar o grupo de recursos. Para obter mais informações, veja [Utilizar Grupos de recursos para gerir os recursos do Azure](../articles/azure-resource-manager/resource-group-overview.md). |
    | Location | *EUA Leste* | Utilize **Localização** para especificar a localização geográfica onde o seu recurso SignalR está alojado. Para obter o melhor desempenho, recomendamos que crie o recurso na mesma região que os outros componentes da sua aplicação. |
    | Escalão de preço | *Gratuito* | Atualmente, existem as opções **Gratuito** e **Standard** disponíveis. |
    | Afixar ao dashboard | ✔ | Assinale esta caixa para que o recurso seja afixado ao dashboard, tornando-o mais fácil de encontrar. |

4. Clique em **Criar**. A implementação pode demorar alguns minutos.

5. Assim que a implementação estiver concluída, clique em **Chaves** sob **DEFINIÇÕES**. Copie a sua cadeia de ligação da chave primária. Irá utilizar isto posteriormente para configurar a aplicação para utilizar o recurso do Serviço Azure SignalR.

    A cadeia de ligação terá a seguinte forma:
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;
