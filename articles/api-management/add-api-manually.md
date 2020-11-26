---
title: Adicionar uma API manualmente com o portal do Azure | Microsoft Docs
description: Este tutorial mostra como utilizar a Gestão de API (APIM) para adicionar manualmente uma API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 39a3b9d7dd9efbda93de0b5d7c5f9938922d0012
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183860"
---
# <a name="add-an-api-manually"></a>Adicionar uma API manualmente

Os passos neste artigo mostram como utilizar o portal do Azure para adicionar uma API manualmente à instância de Gestão de API (APIM). Um cenário comum de quando será útil criar uma API em branco e defini-la manualmente é quando pretende simular uma API. Para obter detalhes sobre a simulação de uma API, veja [Simular respostas de API](mock-api-responses.md).

Se pretender importar uma API existente, veja a secção [Tópicos relacionados](#related-topics).

Neste artigo, vamos criar uma API em branco e especificar [httpbin.org](https://httpbin.org) (um serviço de teste público) como uma API de back-end.

## <a name="prerequisites"></a>Pré-requisitos

Complete o seguinte quickstart: [Criar uma instância de gestão API Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>Criar uma API

1. Navegue para o seu serviço de Gestão API no portal Azure e selecione **APIs** no menu.
2. No menu à esquerda, selecione **+ Adicionar API**.
3. Selecione **API em branco** na lista.  
    ![API em Branco](media/add-api-manually/blank-api.png)  
4. Introduza definições para a API. As definições são explicadas no Import e publicam o seu primeiro tutorial [da API.](import-and-publish.md#import-and-publish-a-backend-api)
5. Selecione **Criar**.

Neste momento, não tem nenhuma operação na API Management que mapear para as operações na sua API de back-end. Se chamar uma operação que está exposta na parte de trás mas não através da Administração da API, obtém-se um **404**.

>[!NOTE] 
> Por predefinição, quando adicionar uma API, mesmo que esteja ligada a algum serviço back-end, a APIM não exporá nenhuma operação até que as permita. Para permitir uma operação do seu serviço back-end, crie uma operação APIM que mapeia para a operação back-end.

## <a name="add-and-test-an-operation"></a>Adicionar e testar uma operação

Esta secção mostra como adicionar uma operação "/get" para mapeá-la para a operação "http://httpbin.org/get" do back-end.

### <a name="add-an-operation"></a>Adicionar uma operação

1. Selecione a API que criou no passo anterior.
2. Clique em **+ Adicionar Operação**.
3. No **URL**, selecione **GET** e introduza "*/get*" no recurso.
4. Introduza "*FetchData*" para **Nome a apresentar**.
5. Selecione **Guardar**.

### <a name="test-an-operation"></a>Testar uma operação

Teste a operação no portal do Azure. Em alternativa, pode testá-la no **Portal do programador**.

1. Selecione o separador **Teste**.
2. Selecione **FetchData**.
3. Prima **Enviar**.

É apresentada a resposta gerada pela operação "http://httpbin.org/get". Se pretender transformar as operações, veja [Transformar e proteger a sua API](transform-api.md).

## <a name="add-and-test-a-parameterized-operation"></a>Adicionar e testar uma operação com parâmetros

Esta secção mostra como adicionar uma operação que assume um parâmetro. Neste caso, vamos mapear a operação para "http://httpbin.org/status/200".

### <a name="add-the-operation"></a>Adicionar a operação

1. Selecione a API que criou no passo anterior.
2. Clique em **+ Adicionar Operação**.
3. No **URL**, selecione **GET** e introduza "*/status/{code}*" no recurso. Opcionalmente, pode fornecer algumas informações associadas a este parâmetro. Por exemplo, introduza "*Number*" para **TIPO**, "*200*" (predefinição) para **VALORES**.
4. Introduza "GetStatus" para **Nome a apresentar**.
5. Selecione **Guardar**.

### <a name="test-the-operation"></a>Testar a operação 

Teste a operação no portal do Azure.  Em alternativa, pode testá-la no **Portal do programador**.

1. Selecione o separador **Teste**.
2. Selecione **GetStatus**. Por predefinição, o valor do código está definido como "*200*". Pode alterá-lo para testar outros valores. Por exemplo, escreva "*418*".
3. Prima **Enviar**.

    É apresentada a resposta gerada pela operação "http://httpbin.org/status/200". Se pretender transformar as operações, veja [Transformar e proteger a sua API](transform-api.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Transformar e proteger a sua API](transform-api.md)
