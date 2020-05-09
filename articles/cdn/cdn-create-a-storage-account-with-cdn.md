---
title: Quickstart - Integre uma conta de Armazenamento Azure com o Azure CDN
description: Saiba como utilizar a Rede de Entrega de Conteúdos (CDN) do Azure para entregar conteúdo de largura de banda elevada ao colocar blobs do Armazenamento do Microsoft Azure em cache.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/30/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 4086a8f354e5e906325d9c324410f3546a32f658
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996182"
---
# <a name="quickstart-integrate-an-azure-storage-account-with-azure-cdn"></a>Quickstart: Integrar uma conta de Armazenamento Azure com o Azure CDN

Neste arranque rápido, permite que a Rede de Entrega de [Conteúdos Azure (CDN)](cdn-overview.md) cache conteúdo a partir do Armazenamento Azure. A CDN do Azure oferece aos programadores uma solução global para entregar conteúdo de largura de banda elevada. Pode colocar blobs e conteúdo estático em cache de instâncias de computação em nós físicos nos Estados Unidos da América, Europa, Ásia, Austrália e América do Sul.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inscreva-se no [portal Azure](https://portal.azure.com) com a sua conta Azure.

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Uma conta de armazenamento dá acesso a serviços de Armazenamento do Microsoft Azure. A conta de armazenamento representa o nível mais elevado de espaço de nomes para aceder a cada componente de serviços de Armazenamento do Microsoft Azure: armazenamento de Blobs, Filas e Tabelas do Azure. Para mais informações, consulte [Introdução ao Armazenamento do Microsoft Azure](../storage/common/storage-introduction.md).

Para criar uma conta de armazenamento, tem de ser o administrador de serviços ou um coadministrador da subscrição associada.

1. No portal Azure, selecione **Criar um recurso** na parte superior esquerda. O painel **Novo** é apresentado.

1. Procure a **conta de Armazenamento** e selecione conta de armazenamento - **blob, file, table, fila** da lista de lançamentos. Em seguida, selecione **Criar:**
    
    ![Selecione o recurso de armazenamento](./media/cdn-create-a-storage-account-with-cdn/cdn-select-new-storage-account.png)

1. No painel de **conta de armazenamento Criar,** introduza os seguintes detalhes:

    | Definição | Valor | 
    | --- | --- |
    | Detalhes do projeto > Grupo de Recursos | Selecione **Criar novo** e use o nome *CDNQuickstart-rg*. Também pode utilizar um grupo de recursos existente, se preferir. |
    | Detalhes da > nome da conta de armazenamento | Introduza um nome para a conta usando apenas 3-24 letras minúsculas e números. O nome deve ser único em todo o Azure, e torna-se o nome de anfitrião no URL que é usado para abordar blob, fila ou recursos de mesa para a subscrição. Para abordar um recurso de contentor no armazenamento blob, utilize um URI no seguinte formato: http://*&lt;nome&gt;* de conta de armazenamento .blob.core.windows.net/*&lt;&gt;nome do contentor*.
    | Detalhes da instância > Localização | Selecione uma região Azure perto de si da lista de lançamentos. |
    
    Deixe todos os outros detalhes definidos para as predefinições e, em seguida, selecione **Rever + criar**.

1. A criação da conta de armazenamento pode levar vários minutos a ser concluída. Uma vez concluída a criação, selecione **Recorrer para** abrir a página da conta de armazenamento para o próximo passo.

## <a name="enable-azure-cdn-for-the-storage-account"></a>Ativar a CDN do Azure para a conta de armazenamento

1. Na página da sua conta de armazenamento, selecione **O serviço** > Blob**Azure CDN** a partir do menu esquerdo. A página **CDN do Azure** é apresentada.

    ![Criar ponto final da CDN](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-endpoint-configuration.png)
    
1. Na secção **New endpoint,** introduza as seguintes informações:

    | Definição  | Valor |
    | -------- | ----- |
    | **Perfil da CDN** | Selecione **Criar novo** e introduza o nome do seu perfil, por exemplo, *perfil cdn-123*. Um perfil é uma coleção de pontos finais. |
    | **Nível de preços** | Selecione uma das opções **Standard,** como a **Standard Microsoft**. |
    | **Nome do ponto final da CDN** | Introduza o seu nome de anfitrião de ponto final, tal como *cdn-endpoint-123*. Este nome deve ser globalmente único em todo o Azure porque é para aceder aos seus recursos em cache no _ &lt;nome&gt;final_do URL .azureedge.net. |
    | **Nome de anfitrião da origem** | Por predefinição, um novo ponto final da CDN utiliza o nome do anfitrião da conta de armazenamento com o servidor de origem. |

1. Selecione **Criar**. Depois da criação do ponto final, este é apresentado na lista de pontos finais.

    ![Armazenar novo ponto final da CDN](./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-list.png)

> [!TIP]
> Se quiser especificar definições de configuração avançadas para o ponto final da CDN, como a [otimização da transferência de ficheiros grandes](cdn-optimization-overview.md#large-file-download), pode utilizar a [extensão da CDN do Azure](cdn-create-new-endpoint.md) para criar um perfil e um ponto final de CDN.


## <a name="enable-additional-cdn-features"></a>Ativar funcionalidades da CDN adicionais

Na página **CDN do Azure** da conta de armazenamento, selecione o ponto de final da CDN na lista para abrir a página de configuração do ponto final da CDN.

A partir desta página, pode ativar funcionalidades adicionais da CDN para a entrega, como [compressão](cdn-improve-performance.md), [colocação em cache de cadeias de consulta](cdn-query-string.md) e [filtragem geográfica](cdn-restrict-access-by-country.md). 
    
## <a name="enable-sas"></a>Ativar a SAS

Se pretender conceder acesso limitado a recipientes de armazenamento privados, pode utilizar a funcionalidade Signature de Acesso Partilhado (SAS) da sua conta de Armazenamento Azure. Uma SAS é um URI que concede direitos de acesso restritos aos recursos de Armazenamento do Azure sem expor a sua chave de conta. Para obter mais informações, veja [Utilizar a CDN do Azure com a SAS](cdn-sas-storage-support.md).

## <a name="access-cdn-content"></a>Aceder ao conteúdo da CDN

Para aceder a conteúdo em cache na CDN, utilize o URL da CDN fornecido no portal. O endereço de um blob em cache tem o seguinte formato:

http://<*endpoint-name*\>.azureedge.net/<*myPublicContainer*\>/<*BlobName*\>

> [!NOTE]
> Depois de ativar o acesso da CDN do Azure a uma conta de armazenamento, todos os objetos publicamente disponíveis são elegíveis para colocação em cache de POP da CDN. Se modificar um objeto que esteja atualmente em cache na CDN, o novo conteúdo não estará disponível através da CDN do Azure até a CDN do Azure atualizar o conteúdo depois do período TTL do conteúdo em cache expirar.

## <a name="remove-content-from-azure-cdn"></a>Remover conteúdo da CDN do Azure

Se já não pretender ter um objeto em cache na CDN do Azure, poderá efetuar um dos seguintes passos:

- Torne o contentor privado em vez de público. Para obter mais informações, veja [Manage anonymous read access to containers and blobs](../storage/blobs/storage-manage-access-to-resources.md) (Gerir o acesso de leitura anónima a contentores e blobs).
- Desative ou elimine o ponto final da CDN ao utilizar o portal do Azure.
- Modifique o serviço alojado para deixar de responder a pedidos para o objeto.

Um objeto que já esteja em cache na CDN do Azure permanece em cache até o período TTL do objeto expirar ou até o ponto final ser [removido](cdn-purge-endpoint.md). Quando o período TTL expirar, a CDN do Azure determina se o ponto final da CDN ainda é válido e se o objeto ainda é acessível anonimamente. Se não forem, o objeto não já estará em cache.

## <a name="clean-up-resources"></a>Limpar recursos

Nos passos anteriores, criou um perfil e um ponto final de CDN num grupo de recursos. Guarde estes recursos, se pretender aceder aos [Passos seguintes](#next-steps) e aprender a adicionar um domínio personalizado ao ponto final. No entanto, se não pretende utilizar estes recursos no futuro pode eliminá-los, ao eliminar o grupo de recursos, evitando assim encargos adicionais:

1. A partir do menu à esquerda no portal Azure, selecione **grupos de recursos** e, em seguida, selecione *CDNQuickstart-rg**.

2. Na página do **grupo Recurso,** selecione **Eliminar o grupo de recursos**, introduza *cDNQuickstart-rg* na caixa de texto e, em seguida, selecione **Delete**.

    Esta ação irá eliminar o grupo de recursos, o perfil e o ponto final que criou neste início rápido.

3. Para eliminar a conta de armazenamento, selecione-a no dashboard e, em seguida, selecione **Eliminar** no menu superior.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar um perfil e ponto final de CDN Azure](cdn-create-new-endpoint.md)

> [!div class="nextstepaction"]
> [Tutorial: Use cDN para servidor conteúdo estático a partir de uma aplicação web](cdn-add-to-web-app.md)
