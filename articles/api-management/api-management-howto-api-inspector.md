---
title: Depurar as APIs utilizando o rastreio de pedidos na Gestão de API do Azure | Microsoft Docs
description: Siga os passos deste tutorial para aprender a inspecionar os passos de processamento de pedidos na Gestão de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: ff3dde8ac95b678866ba6f5216ba23357b067765
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203573"
---
# <a name="debug-your-apis-using-request-tracing"></a>Depurar as suas APIs com o rastreio de pedidos

Este tutorial descreve como inspecionar o processamento de pedidos para ajudá-lo com a depuração e resolução de problemas da API. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Rastrear uma chamada

![Inspetor de API](media/api-management-howto-api-inspector/api-inspector001.PNG)

## <a name="prerequisites"></a>Pré-requisitos

+ Conhecer a [terminologia da Gestão de API do Azure](api-management-terminology.md).
+ Conclua o início rápido seguinte: [Criar uma instância de gestão de API do Azure](get-started-create-service-instance.md).
+ Além disso, conclua o tutorial seguinte: [Importar e publicar a sua primeira API](import-and-publish.md).

## <a name="trace-a-call"></a>Rastrear uma chamada

![Rastreio da API](media/api-management-howto-api-inspector/06-DebugYourAPIs-01-TraceCall.png)

1. Selecione **APIs**.
2. Clique em **API da Conferência de Demonstração** na lista de API.
3. Mude para o separador **Teste**.
4. Selecione a operação **GetSpeakers**.
5. Certifique-se de que inclui um cabeçalho de HTTP chamado **Ocp-Apim-Trace** com o valor definido para **verdadeiro**.

    > [!NOTE]
    > Se Ocp-Apim-Subscription-Key não estiver preenchido automaticamente, pode obtê-lo ao ir ao Portal do Programador e expor as chaves na página de perfil.

6. Clique em **"Enviar"** para efetuar uma chamada à API. 
7. Aguarde pela conclusão da chamada. 
8. Vá para o separador **Rastreio** na **consola API**. Pode clicar em qualquer uma das ligações seguintes para ir para as informações de rastreio de detalhado: **entrada**, **back-end**, **saída**.

    Na secção **Entrada**, pode ver o pedido original de Gestão de API recebido pelo autor da chamada e todas as políticas aplicadas ao pedido, incluindo as políticas de definição de cabeçalho e de limitação de taxas adicionadas no passo 2.

    Na secção **back-end**, pode ver os pedidos de Gestão de API enviados para o back-end de API e a resposta que recebeu.

    Na secção **saída**, pode ver todas as políticas aplicadas à resposta antes de devolver ao autor da chamada.

    > [!TIP]
    > Cada passo mostra também o tempo decorrido desde que o pedido é recebido através da Gestão de API.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Rastrear uma chamada

Avance para o tutorial seguinte:

> [!div class="nextstepaction"]
> [Usar revisões](api-management-get-started-revise-api.md)
