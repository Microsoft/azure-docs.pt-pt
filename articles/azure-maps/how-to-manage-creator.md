---
title: Gerir O Criador de Mapas Azure
description: Neste artigo, você vai aprender a gerir O Criador de Mapas Azure.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e6d8a2bfe20d0e7b52dcd60127b3666f0c21a792
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895805"
---
# <a name="manage-azure-maps-creator"></a>Gerir O Criador de Mapas Azure

O Azure Maps Creator permite criar dados de mapas interiores privados. Utilizando a API AZure Maps e o módulo Indoor Maps, pode desenvolver aplicações web de mapas internos interativos e dinâmicos. Atualmente, o Creator só está disponível nos Estados Unidos utilizando o nível de preços S1.

Este artigo leva-o através dos passos para criar e eliminar um recurso Criador numa conta do Azure Maps.

## <a name="create-creator-resource"></a>Criar recurso de criador

1. Inicie sessão no [portal do Azure](https://portal.azure.com)

2. Selecione a sua conta Azure Maps. Se não consegue ver a sua conta Azure Maps sob os **recursos recentes,** então navegue para o menu do portal Azure. Selecione **Todos os recursos** . Encontre e selecione a sua conta Azure Maps.

    ![Página inicial do Portal Azure Maps](./media/how-to-manage-creator/select-maps-account.png)

3. Assim que estiver na página da conta Azure Maps, navegue para a opção **Visão Geral** sob **o Criador** . Clique em  **Criar**  para criar um recurso Azure Maps Creator.

    ![Criar página de Criador de Mapas Azure](./media/how-to-manage-creator/creator-blade-settings.png)

4. Insira o nome e a localização do seu recurso Criador. Atualmente, o Criador só é apoiado nos Estados Unidos. Clique em **Rever + criar** .

   ![Insira a página de informação da conta do Criador](./media/how-to-manage-creator/creator-creation-dialog.png)

5. Reveja as suas definições e clique em **Criar** .

    ![Confirmar página de definições de conta do Criador](./media/how-to-manage-creator/creator-create-dialog.png)

6. Quando a implementação estiver concluída, verá uma página com um sucesso ou uma mensagem de falha.

   ![Página de estado de implementação de recursos](./media/how-to-manage-creator/creator-resource-created.png)

7. Clique em **Ir para recurso** . A página de visualização do seu recurso Criador mostra o estado do seu recurso Criador e a região demográfica escolhida.

    ![Página de estado do criador](./media/how-to-manage-creator/creator-resource-view.png)

   >[!NOTE]
   >A partir da página de recursos do Criador, pode navegar de volta para a conta Azure Maps a que pertence clicando na Conta Azure Maps.

## <a name="delete-creator-resource"></a>Eliminar o Recurso Criador

Para eliminar o recurso Criador, navegue na sua conta Azure Maps. Selecione **visão geral** sob **o Criador** . Clique no botão **Eliminar** .

>[!WARNING]
>Quando eliminar o recurso Criador da sua conta Azure Maps, também eliminará os conjuntos de dados, os azulejos e os estados de funcionalidades criados através dos serviços do Criador.

![Página do criador com botão de exclusão](./media/how-to-manage-creator/creator-delete.png)

Clique no botão **Eliminar** e digite o nome Do Criador para confirmar a eliminação. Assim que o recurso for eliminado, verá uma página de confirmação, como na imagem abaixo:

![Página do criador com confirmação de eliminação](./media/how-to-manage-creator/creator-confirmdelete.png)

## <a name="authentication"></a>Autenticação

O Criador herda as definições do Azure Maps Access Control (IAM). Todas as chamadas da API para acesso aos dados devem ser enviadas com regras de autenticação e autorização.

Os dados de utilização do criador estão incorporados nos seus gráficos de utilização do Azure Maps e no registo de atividades.  Para mais informações, consulte [Gerir a autenticação no Azure Maps.](./how-to-manage-authentication.md)

## <a name="access-to-creator-services"></a>Acesso aos serviços do Criador

Os serviços de criadores só são acessíveis a partir do local selecionado durante a criação. Se forem feitas chamadas para os serviços do Criador de fora do local selecionado, será devolvida uma mensagem de erro do utilizador. Para escamar chamadas de fora do local selecionado, o URL de serviço deve incluir o prefixo geográfico para os locais selecionados. Por exemplo, se o Criador for criado nos Estados Unidos, todas as chamadas para o serviço de Conversão devem ser submetidas a `us.atlas.microsoft.com/conversion/convert` .

Além disso, todos os dados importados para o Criador devem ser enviados para a mesma localização geográfica que o recurso Criador. Por exemplo, se o Criador for a provisionado nos Estados Unidos, todos os dados brutos devem ser enviados através `us.atlas.microsoft.com/mapData/upload` de .

## <a name="next-steps"></a>Passos seguintes

Introdução ao Criador para mapeamento interior:

> [!div class="nextstepaction"]
> [Upload de dados](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Conversão de dados](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Conjunto de dados](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Teeste](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Conjunto de estado de recurso](creator-indoor-maps.md#feature-statesets)

Saiba como usar o Criador para fazer mapas interiores na sua aplicação:

> [!div class="nextstepaction"]
> [Tutorial do Criador de Mapas Azure](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Estilo dinâmico do mapa interior](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Utilize o módulo Mapas Interiores](how-to-use-indoor-module.md)