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
ms.openlocfilehash: 39bff26baea622e6c0ed524ca68c3c8bae4e770d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76020891"
---
1. Para criar um recurso de serviço De Sinalização Azure, inste primeiro o [portal Azure](https://portal.azure.com). No lado superior esquerdo da página, selecione **+ Crie um recurso**. Na caixa de texto **Do Mercado,** introduza o **Serviço SignalR**.

2. Selecione **O Serviço SignalR** nos resultados e selecione **Criar**.

3. Na nova página de definições do **SignalR,** adicione as seguintes definições para o seu novo recurso SignalR:

    | Nome | Valor recomendado | Descrição |
    | ---- | ----------------- | ----------- |
    | Nome do recurso | *testsignalr* | Introduza um nome de recurso exclusivo para utilizar no recurso do SignalR. O nome deve ser uma cadeia de 1 a 63 caracteres e`-`conter apenas números, letras e o caráter do hífen. O nome não pode começar ou terminar com o caráter hífen, e os caracteres de hífen consecutivos não são válidos.|
    | Subscrição | Escolher a sua subscrição |  Selecione a subscrição do Azure que pretende utilizar para testar o SignalR. Se a sua conta tiver apenas uma subscrição, é automaticamente selecionada e a entrega da **Subscrição** não é apresentada.|
    | Grupo de recursos | Criar um grupo de recursos chamado *SignalRTestResources*| Selecione ou crie um grupo de recursos para o seu recurso SignalR. Este grupo é útil para organizar vários recursos que você pode querer apagar ao mesmo tempo, eliminando o grupo de recursos. Para obter mais informações, veja [Utilizar grupos de recursos para gerir os recursos do Azure](../articles/azure-resource-manager/management/overview.md). |
    | Localização | *E.U.A. Leste* | Utilize **Localização** para especificar a localização geográfica onde o seu recurso SignalR está alojado. Para obter o melhor desempenho, recomendamos que crie o recurso na mesma região que os outros componentes da sua aplicação. |
    | Escalão de preço | *Gratuito* | Atualmente, estão disponíveis opções **Gratuitas** e **Standard.** |
    | Afixar ao dashboard | ✔ | Selecione esta caixa para ter o recurso fixado no seu painel de instrumentos para que seja mais fácil de encontrar. |

4. Selecione **Criar**. O destacamento pode levar alguns minutos para ser concluído.

5. Depois de concluída a colocação em que stão, selecione **Teclas** em **DEFINIÇÕES**. Copie a sua corda de ligação para a chave principal. Mais tarde, utilizará esta cadeia para configurar a sua aplicação para utilizar o recurso do Serviço De Sinalização Azure.

    A cadeia de ligação terá a seguinte forma:
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;
