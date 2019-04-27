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
ms.devlang: na
ms.topic: tutorial
ms.date: 08/27/2018
ms.author: apimpm
ms.openlocfilehash: 14d4bf6d7e1d1f474e2388c4e2ce232574ebf0d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60798468"
---
# <a name="add-an-api-manually"></a>Adicionar uma API manualmente

Os passos neste artigo mostram como utilizar o portal do Azure para adicionar uma API manualmente para a instância de gestão de API (APIM). Um cenário comum de quando será útil criar uma API em branco e defini-la manualmente é quando pretende simular uma API. Para obter detalhes sobre a simulação de uma API, veja [Simular respostas de API](mock-api-responses.md).

Se pretender importar uma API existente, veja a secção [Tópicos relacionados](#related-topics).

Neste artigo, vamos criar uma API em branco e especificar [httpbin.org](https://httpbin.org) (um serviço de teste público) como uma API de back-end.

## <a name="prerequisites"></a>Pré-requisitos

Conclua o início rápido seguinte: [crie uma instância da Gestão de API do Azure](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>Criar uma API

1. Selecione **APIs** em **GESTÃO DE API**.
2. No menu à esquerda, selecione **+ Adicionar API**.
3. Selecione **API em branco** na lista.

    ![API em branco](media/add-api-manually/blank-api.png)
4. Introduza definições para a API.

    ![Definições](media/add-api-manually/settings.png)

    |**Nome**|**Valor**|**Descrição**|
    |---|---|---|
    |**Nome a apresentar**|"*Blank API*" |Este nome é apresentado no portal do Programador.|
    |**URL do Serviço Web** (opcional)| "*https://httpbin.org*"| Se pretender simular uma API, poderá não introduzir nada. <br/>Neste caso, vamos introduzir [https://httpbin.org](https://httpbin.org). Este é um serviço de teste público. <br/>Se pretender importar uma API que está mapeada para um back-end automaticamente, veja um dos tópicos na secção [Tópicos relacionados](#related-topics).|
    |**Esquema do URL**|"*HTTPS*"|Neste caso, embora o back-end tenha acesso HTTP não seguro, especificamos um acesso APIM de HTTPS seguro para o back-end. <br/>Este tipo de cenário (HTTPS para HTTP) é chamado terminação HTTPS. Poderá fazê-lo se a sua API existir dentro de uma rede virtual (onde saiba que o acesso é seguro, mesmo que não seja utilizado HTTPS). <br/>Poderá utilizar a "terminação HTTPS" para poupar alguns ciclos da CPU.|
    |**Sufixo do URL**|"*hbin*"| O sufixo é um nome que identifica esta API específica nesta instância de APIM. Tem de ser exclusivo nesta instância de APIM.|
    |**Produtos**|"*Unlimited*" |Associe a API a um produto e publique-a. Se pretender que a API seja publicada e esteja disponível para programadores, adicione-a a um produto. Pode fazê-lo durante a criação da API ou defini-lo mais tarde.<br/><br/>Os produtos são associações de uma ou mais APIs. Pode incluir um número de APIs e disponibilizá-las para os programadores através do portal do programador. <br/>Os programadores têm de subscrever primeiro um produto para obter acesso à API. Quando subscrevem, recebem uma chave de subscrição que é válida para qualquer API nesse produto. Se tiver criado a instância APIM, já é um administrador, pelo que tem todos os produtos subscritos por predefinição.<br/><br/> Por predefinição, cada instância daAPI Management é fornecida com dois produtos de exemplo: **Starter** e **ilimitado**.| 
5. Selecione **Criar**.

Neste momento, não tem operações na APIM que mapeiem para as operações na sua API de back-end. Se chamar uma operação que está exposta através do back-end, mas não através da APIM, obtém um **404**.

>[!NOTE] 
> Por predefinição, quando adiciona uma API, mesmo que esteja ligada a algum serviço de back-end, a APIM não irá expor quaisquer operações até colocá-las na lista de permissões. Para colocar uma operação do seu serviço de back-end numa lista de permissões, crie uma operação de APIM que mapeie para a operação de back-end.

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

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Transformar e proteger a sua API](transform-api.md)
