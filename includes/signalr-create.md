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
ms.openlocfilehash: 28d003e123069c47d87d81570b4a5b69b3b9d64b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184629"
---
1. Para criar um recurso do serviço Azure SignalR, primeiro inicie sessão para o [portal do Azure](https://portal.azure.com). No lado do canto superior esquerdo da página, selecione **+ criar um recurso**. Na **pesquisar no Marketplace** texto, digite **serviço SignalR**.

2. Selecione **serviço SignalR** nos resultados e selecione **criar**.

3. No novo **SignalR** página Definições, adicione as seguintes definições para o novo recurso do SignalR:

    | Name | Valor recomendado | Descrição |
    | ---- | ----------------- | ----------- |
    | Nome do recurso | *testsignalr* | Introduza um nome de recurso exclusivo para utilizar no recurso do SignalR. O nome tem de ser uma cadeia entre 1 e 63 carateres e conter apenas números, letras e o hífen (`-`) caracteres. O nome não pode começar nem terminar com o caráter de hífen e carateres de hífen consecutivo não são válidos.|
    | Subscrição | Escolher a sua subscrição |  Selecione a subscrição do Azure que pretende utilizar para testar o SignalR. Se a sua conta tiver apenas uma subscrição, é selecionada automaticamente e o **subscrição** não for apresentada a lista pendente.|
    | Grupo de recursos | Criar um grupo de recursos com o nome *SignalRTestResources*| Selecione ou crie um grupo de recursos para o seu recurso SignalR. Este grupo é útil para organizar os vários recursos que pode querer eliminar ao mesmo tempo, eliminando o grupo de recursos. Para obter mais informações, consulte [Utilizar grupos de recursos para gerir os seus recursos do Azure](../articles/azure-resource-manager/resource-group-overview.md). |
    | Location | *EUA Leste* | Utilize **Localização** para especificar a localização geográfica onde o seu recurso SignalR está alojado. Para obter o melhor desempenho, recomendamos que crie o recurso na mesma região que os outros componentes da sua aplicação. |
    | Escalão de preço | *Gratuito* | Atualmente, **gratuito** e **padrão** opções estão disponíveis. |
    | Afixar ao dashboard | ✔ | Selecione esta caixa para que o recurso afixado ao dashboard para que seja mais fácil encontrar. |

4. Selecione **Criar**. A implementação pode demorar alguns minutos a concluir.

5. Depois da implementação estiver concluída, selecione **chaves** sob **definições**. Copie a cadeia de ligação para a chave primária. Usará essa cadeia de caracteres mais tarde para configurar a aplicação para utilizar o recurso de serviço Azure SignalR.

    A cadeia de ligação terá a seguinte forma:
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;
