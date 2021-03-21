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
ms.openlocfilehash: e5cfc9beb5473917a76f822862ce3d61675d6493
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93406729"
---
1. Para criar um recurso de Serviço Azure SignalR, inscreva-se primeiro no [portal Azure](https://portal.azure.com). No lado superior esquerdo da página, selecione **+ Criar um recurso**. Na caixa de texto **do Mercado,** insira o **Serviço SignalR**.

2. Selecione **o Serviço SignalR** nos resultados e selecione **Criar**.

3. Na nova página de definições **do SignalR,** adicione as seguintes definições para o seu novo recurso SignalR:

    | Name | Valor recomendado | Description |
    | ---- | ----------------- | ----------- |
    | Nome do recurso | *testsignalr* | Introduza um nome de recurso exclusivo para utilizar no recurso do SignalR. O nome deve ser uma cadeia de 1 a 63 caracteres e conter apenas números, letras e o `-` carácter hífen ( ). O nome não pode começar ou terminar com o carácter hífen, e os caracteres de hífen consecutivos não são válidos.|
    | Subscrição | Escolher a sua subscrição |  Selecione a subscrição do Azure que pretende utilizar para testar o SignalR. Se a sua conta tiver apenas uma subscrição, é selecionada automaticamente e a entrega da **Subscrição** não é apresentada.|
    | Grupo de recursos | Criar um grupo de recursos chamado *SignalRTestResources*| Selecione ou crie um grupo de recursos para o seu recurso SignalR. Este grupo é útil para organizar múltiplos recursos que você pode querer eliminar ao mesmo tempo, eliminando o grupo de recursos. Para obter mais informações, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../articles/azure-resource-manager/management/overview.md). |
    | Localização | *E.U.A Leste* | Utilize **Localização** para especificar a localização geográfica onde o seu recurso SignalR está alojado. Para obter o melhor desempenho, recomendamos que crie o recurso na mesma região que os outros componentes da sua aplicação. |
    | Escalão de preço | *Gratuito* | Atualmente, as opções **Free** e **Standard** estão disponíveis. |
    | Afixar ao dashboard | ✔ | Selecione esta caixa para ter o recurso fixado no seu painel de instrumentos para que seja mais fácil de encontrar. |

4. Selecione **Rever + criar**. Aguarde que a validação esteja completa. 

5. Selecione **Criar**. A implantação pode levar alguns minutos para ser concluída.

6. Depois de concluída a implementação, selecione **Teclas** em **DEFINIÇÕES**. Copie a sua cadeia de ligação para a chave primária. Mais tarde utilizará esta cadeia para configurar a sua aplicação para utilizar o recurso Azure SignalR Service.

    A cadeia de ligação terá a seguinte forma:
    
    `Endpoint=<service_endpoint>;AccessKey=<access_key>;`
