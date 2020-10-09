---
title: Publicar versões da sua API com a Gestão de API do Azure | Microsoft Docs
description: Siga os passos deste tutorial para aprender a publicar várias versões na Gestão de API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: b683910180e597cb8cbfa642bb2d9ac3200b42ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86255035"
---
# <a name="publish-multiple-versions-of-your-api"></a>Publicar várias versões da sua API 

Quando não for prático ter todos os autores de chamadas direcionados para a sua API, utilize exatamente a mesma versão. Quando os autores de chamadas quiserem atualizar para uma versão posterior, querem fazê-lo com uma abordagem de fácil compreensão. É possível fazê-lo com **versões** na Gestão de API do Azure. Para obter mais informações, veja [Versões e revisões](https://azure.microsoft.com/blog/versions-revisions/).

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar uma nova versão a uma API existente
> * Escolher um esquema de versão
> * Adicionar a versão a um produto
> * Navegar até ao portal do programador para ver a versão

![A versão é apresentada no portal do programador](media/api-management-getstarted-publish-versions/azure_portal.PNG)

## <a name="prerequisites"></a>Pré-requisitos

+ Conhecer a [terminologia da Gestão de API do Azure](api-management-terminology.md).
+ Conclua o guia de início rápido seguinte: [Criar uma instância da Gestão de API do Azure](get-started-create-service-instance.md).
+ Conclua também o tutorial seguinte: [Importar e publicar a sua primeira API](import-and-publish.md).

## <a name="add-a-new-version"></a>Adicionar uma nova versão

![Menu de contexto da API - adicionar versão](media/api-management-getstarted-publish-versions/AddVersionMenu.png)

1. Selecione **Demo Conference API** na lista de APIs.
2. Selecione o menu de contexto (**...**) junto à mesma.
3. Selecione **+ Adicionar Versão**.

> [!TIP]
> As versões também podem ser ativadas quando criar uma nova API. Selecione **Controlar a versão desta API?** no ecrã **Adicionar API**.

## <a name="choose-a-versioning-scheme"></a>Escolher um esquema de versões

A Gestão de API do Azure permite-lhe escolher a forma como permite aos autores de chamadas especificar a versão da API que querem. Especifica a versão da API a utilizar ao selecionar um **esquema de versões**. Este esquema pode ser um **caminho, um cabeçalho ou uma cadeira de consulta**. No exemplo seguinte, é utilizado o caminho para selecionar o esquema de versões.

![Adicionar ecrã de versão](media/api-management-getstarted-publish-versions/AddVersion.PNG)

1. Mantenha **caminho** selecionado como **esquema de versões**.
2. Escreva **demo-conference-api-v1** no campo **Nome**.

    > [!NOTE]
    > A versão é, na verdade, uma nova API com base na revisão de uma API. O **Nome** é o nome da nova API e tem de ser exclusivo em toda a instância de Gestão de API.

3. Escreva **v1** no campo **Identificador da Versão**.

    > [!TIP]
    > Se selecionar **cabeçalho** ou **cadeia de consulta** como esquema de versões, tem de fornecer um valor adicional, o nome do cabeçalho ou o parâmetro da cadeia de consulta.

4. Selecione **Criar** para configurar a nova versão.
5. Abaixo da **API da Conferência de Demonstração**, na Lista de APIs, pode ver agora duas APIs distintas: **Original** e **v1**.

    ![Versões listadas numa API no portal do Azure](media/api-management-getstarted-publish-versions/VersionList.PNG)

    > [!Note]
    > Se adicionar uma versão a uma API sem versões, será criada automaticamente uma **Original**, que responde no URL predefinido. Isto garante que todos os autores de chamadas existentes não são divididos pelo processo de adição de uma versão. Se criar uma nova API com versões ativadas no início, não é criada uma Original.

6. Agora, pode editar e configurar **v1** como uma API separada da **Original**. As alterações a uma versão não afetam a outra.

## <a name="add-the-version-to-a-product"></a>Adicionar a versão a um produto

Para os autores de chamadas verem a nova versão, esta tem de ser adicionada a um **produto**.

![Produtos da Gestão de API](media/api-management-getstarted-publish-versions/08-AddMultipleVersions-03-AddVersionToProduct.png)

1. Selecione **Produtos** na página do modelo de implementação clássica.
2. Selecione **Ilimitados**.
3. Selecione **APIs**.
4. Selecione **Adicionar**.
5. Selecione **API da Conferência de Demonstração, Versão v1**.
6. Clique em **Selecionar**.

## <a name="browse-the-developer-portal-to-see-the-version"></a>Navegar até ao portal do programador para ver a versão

1. Selecione **Portal do Programador** no menu superior.
2. Selecione **APIs** e clique na **API da Conferência de Demonstração**.
3. Você deve ver um dropdown com várias versões ao lado do nome API.
4. Selecione **v1**.
5. Repare no **URL do pedido** da primeira operação na lista. Indica que o caminho do URL da API inclui **v1**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Adicionar uma nova versão a uma API existente
> * Escolher um esquema de versão 
> * Adicionar a versão a um produto
> * Navegar até ao portal do programador para ver a versão

Avance para o tutorial seguinte:

> [!div class="nextstepaction"]
> [Personalizar o estilo das páginas de portal do Programador](api-management-customize-styles.md)
