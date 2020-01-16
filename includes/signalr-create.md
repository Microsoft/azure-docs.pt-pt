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
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76020891"
---
1. Para criar um recurso do serviço de Signaler do Azure, primeiro entre no [portal do Azure](https://portal.azure.com). No lado superior esquerdo da página, selecione **+ criar um recurso**. Na caixa de texto **Pesquisar no Marketplace** , insira **serviço de signalr**.

2. Selecione **serviço de signalr** nos resultados e selecione **criar**.

3. Na página Configurações do novo **signalr** , adicione as seguintes configurações para o novo recurso do signalr:

    | Nome | Valor recomendado | Descrição |
    | ---- | ----------------- | ----------- |
    | Nome do recurso | *testsignalr* | Introduza um nome de recurso exclusivo para utilizar no recurso do SignalR. O nome deve ser uma cadeia de caracteres de 1 a 63 caracteres e conter apenas números, letras e o caractere de hífen (`-`). O nome não pode começar ou terminar com o caractere de hífen, e caracteres de hífen consecutivos não são válidos.|
    | Subscrição | Escolher a sua subscrição |  Selecione a subscrição do Azure que pretende utilizar para testar o SignalR. Se sua conta tiver apenas uma assinatura, ela será selecionada automaticamente e a lista suspensa **assinatura** não será exibida.|
    | Grupo de recursos | Crie um grupo de recursos chamado *SignalRTestResources*| Selecione ou crie um grupo de recursos para o seu recurso SignalR. Esse grupo é útil para organizar vários recursos que você pode querer excluir ao mesmo tempo excluindo o grupo de recursos. Para obter mais informações, consulte [Utilizar grupos de recursos para gerir os seus recursos do Azure](../articles/azure-resource-manager/management/overview.md). |
    | Localização | *EUA Leste* | Utilize **Localização** para especificar a localização geográfica onde o seu recurso SignalR está alojado. Para obter o melhor desempenho, recomendamos que crie o recurso na mesma região que os outros componentes da sua aplicação. |
    | Escalão de preço | *Gratuito* | Atualmente, as opções **gratuitas** e **padrão** estão disponíveis. |
    | Afixar ao dashboard | ✔ | Selecione esta caixa para que o recurso seja fixado ao seu painel para que seja mais fácil de localizar. |

4. Selecione **Criar**. A implantação pode levar alguns minutos para ser concluída.

5. Depois que a implantação for concluída, selecione **chaves** em **configurações**. Copie a cadeia de conexão para a chave primária. Você usará essa cadeia de caracteres posteriormente para configurar seu aplicativo para usar o recurso de serviço de Signaler do Azure.

    A cadeia de ligação terá a seguinte forma:
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;
