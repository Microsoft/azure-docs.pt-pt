---
title: Como criar e publicar um produto na Gestão de API do Azure
description: Saiba como criar e publicar produtos na Gestão de API do Azure. Uma vez publicado um produto, os desenvolvedores podem começar a usar as APIs do produto.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 08/10/2018
ms.author: apimpm
ms.openlocfilehash: 69b5e381ed8446b45f68b4b1ce9bb13df47039c0
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87904909"
---
# <a name="create-and-publish-a-product"></a>Criar e publicar um produto  

Na Gestão de API do Azure, os produtos contêm uma ou mais APIs, bem como quotas de utilização e os termos de utilização. Depois de um produto ser publicado, os programadores podem subscrevê-lo e começar a utilizar as APIs do mesmo.  

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar e publicar um produto
> * Adicionar uma API ao produto

![Adicionar o tutorial de produto](media/api-management-howto-add-products/added-product.png)

## <a name="prerequisites"></a>Pré-requisitos

+ Conhecer a [terminologia da Gestão de API do Azure](api-management-terminology.md).
+ Conclua o guia de início rápido seguinte: [Criar uma instância da Gestão de API do Azure](get-started-create-service-instance.md).
+ Conclua também o tutorial seguinte: [Importar e publicar a sua primeira API](import-and-publish.md).

## <a name="create-and-publish-a-product"></a>Criar e publicar um produto

![Adicionar produto](media/api-management-howto-add-products/02-create-publish-product-01.png)

1. Clique em **Produtos** no menu à esquerda para apresentar a página **Produtos**.
2. Clique em **+ Adicionar**.

    Ao adicionar um produto, tem de fornecer as seguintes informações: 

    | Nome                     | Descrição                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Nome a apresentar             | O nome, como pretende que seja apresentado no **Portal do programador**.                                                                                                                                                                                                                                                        |
    | Nome                     | Um nome descritivo do produto.                                                                                                                                                                                                                                                                                      |
    | Descrição              | O campo **Descrição** permite-lhe fornecer informações detalhadas sobre o produto, como a respetiva finalidade, as APIs às quais fornece acesso e outras informações úteis.                                                                                                                                               |
    | Estado                    | Prima **Publicado** se pretender publicar o produto. Antes de as APIs num produto poderem ser chamadas, o produto tem de ser publicado. Por predefinição, os novos produtos não são publicados, e são visíveis apenas para o grupo **Administradores**.                                                                                      |
    | Exige subscrição    | Selecione **Exigir subscrição** se um utilizador precisar de subscrever para utilizar o produto.                                                                                                                                                                                                                                   |
    | Requer aprovação        | Selecione **Exigir aprovação**, se pretender que um administrador reveja e aceite ou rejeite as tentativas de subscrição deste produto. Se a caixa estiver desmarcada, as tentativas de subscrição são aprovadas automaticamente.                                                                                                                         |
    | Limite da contagem de subscrições | Para limitar a contagem de várias subscrições simultâneas, introduza o limite de subscrições.                                                                                                                                                                                                                                |
    | Termos legais              | Pode incluir os termos de utilização do produto, que os subscritores têm de aceitar para poderem utilizar o produto.                                                                                                                                                                                                             |
    | APIs                     | Os produtos são associações de uma ou mais APIs. Pode incluir um número de APIs e disponibilizá-las para os programadores através do portal do programador. <br/> Pode adicionar uma API existente durante a criação de produtos. Pode adicionar uma API ao produto mais tarde, quer a partir da página **Definições** do Produto, quer enquanto cria uma API. |

3. Clique em **Criar** para criar o novo produto.

### <a name="add-more-configurations"></a>Adicionar mais configurações

Pode continuar a configurar o produto após guardá-lo, ao escolher o separador **Definições**. 

Veja/adicione subscritores ao produto, a partir do separador **Subscrições**.

Desaprote a visibilidade de um produto para programadores ou hóspedes a partir do **separador de controlo Access.**

## <a name="add-apis-to-a-product"></a><a name="add-apis"> </a>Adicionar APIs a um produto

Os produtos são associações de uma ou mais APIs. Pode incluir um número de APIs e disponibilizá-las para os programadores através do portal do programador. Pode adicionar uma API existente durante a criação de produtos. Pode adicionar uma API ao produto mais tarde, a partir da página **Definições** de Produtos ou ao criar uma API.

Os programadores têm de subscrever primeiro um produto para obter acesso à API. Quando subscrevem, recebem uma chave de subscrição que é válida para qualquer API nesse produto. Se tiver criado a instância APIM, já é um administrador, pelo que tem todos os produtos subscritos por predefinição.

### <a name="add-an-api-to-an-existing-product"></a>Adicionar uma API a um produto existente

![adicionar API de produto](media/api-management-howto-add-products/02-create-publish-product-02.png)

1. No separador **Produtos**, selecione um produto.
2. Navegue para o separador **APIs**.
3. Clique em **+ Adicionar**.
4. Escolha uma API e clique em **Selecionar**.

> [!TIP]
> Pode criar ou atualizar a subscrição do utilizador para um *Produto*, com chaves de subscrição personalizadas [através da API REST](/rest/api/apimanagement/2019-12-01/subscription/createorupdate) ou do comando do PowerShell.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar e publicar um produto
> * Adicionar uma API ao produto

Avance para o tutorial seguinte:

> [!div class="nextstepaction"]
> [Criar API em branco e simular respostas da API](mock-api-responses.md)
