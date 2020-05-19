---
title: Gerir o Criador de Mapas Azure
description: Neste artigo, aprenderá a gerir o Criador do Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 945c7035c2eeada3fea358489460f80c40438950
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598312"
---
# <a name="manage-azure-maps-creator"></a>Gerir o Criador de Mapas Azure

O Azure Maps Creator permite-lhe criar dados privados de mapas interiores. Utilizando o Azure Maps API e o módulo Indoor Maps, pode desenvolver aplicações web de mapas interiores interativos e dinâmicos. Atualmente, o Criador só está disponível nos Estados Unidos utilizando o nível de preços S1.

Este artigo leva-o através dos passos para criar e apagar um recurso do Criador numa conta Azure Maps.

## <a name="create-creator-resource"></a>Criar recurso criador

1. Inscreva-se no [portal Azure](https://portal.azure.com)

2. Selecione a sua conta Azure Maps. Se não consegue ver a sua conta Azure Maps sob os **recursos recentes,** navegue para o menu do portal Azure. Selecione **Todos os recursos**. Encontre e selecione a sua conta Azure Maps.

    ![Página inicial do Portal do Mapas Azure](./media/how-to-manage-creator/select-maps-account.png)

3. Uma vez na página da conta do Azure Maps, navegue para a opção **Overview** em **Criador**. Clique em **Criar** para criar um recurso Azure Maps Creator.

    ![Criar página criadora de mapas azure](./media/how-to-manage-creator/creator-blade-settings.png)

4. Insira o nome e a localização do seu recurso Criador. Atualmente, o Criador só é apoiado nos Estados Unidos. Clique em **Rever + criar**.

   ![Insira a página de informação da conta do Criador](./media/how-to-manage-creator/creator-creation-dialog.png)

5. Reveja as suas definições e clique em **Criar**.

    ![Confirmar página de definições de conta do Criador](./media/how-to-manage-creator/creator-create.PNG)

6. Quando a implementação estiver concluída, verá uma página com sucesso ou mensagem de falha.

   ![Página de estado de implementação de recursos](./media/how-to-manage-creator/creator-resource-created.png)

7. Clique em **Ir para recurso**. A página de visualização de recursos do Seu Criador mostra o estado do seu recurso Criador e a região demográfica escolhida.

    ![Página de estado do criador](./media/how-to-manage-creator/creator-resource-view.png)

   >[!NOTE]
   >A partir da página de recursos do Criador, pode navegar de volta para a conta Do Mapa Azure a que pertence clicando na Conta Azure Maps.

## <a name="delete-creator-resource"></a>Eliminar recurso do Criador

Para eliminar o recurso Criador, navegue para a sua conta Azure Maps. Selecione **visão geral** sob **criador**. Clique no botão **Eliminar**.

>[!WARNING]
>Ao eliminar o recurso Criador da sua conta Azure Maps, também eliminará os conjuntos de dados, azulejos e funcionalidades criadas utilizando serviços Do Criador.

![Página do criador com botão de eliminar](./media/how-to-manage-creator/creator-delete.png)

Clique no botão **Eliminar** e digite o nome do Criador para confirmar a eliminação. Uma vez que o recurso é apagado, você verá uma página de confirmação, como na imagem abaixo:

![Página do criador com confirmação de exclusão](./media/how-to-manage-creator/creator-confirmdelete.png)

## <a name="authentication"></a>Autenticação

Criador herda definições de Controlo de Acesso Do Azure Maps (IAM). Todas as chamadas da API para acesso a dados devem ser enviadas com regras de autenticação e autorização.

Os dados de utilização do criador estão incorporados nos gráficos de utilização do Azure Maps e no registo de atividade.  Para mais informações, consulte [Gerir a autenticação em Mapas Azure](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication).

## <a name="access-to-creator-services"></a>Acesso aos serviços do Criador

Os serviços de criador só são acessíveis a partir do local selecionado durante a criação. Se forem feitas chamadas para os serviços do Criador de fora do local selecionado, será devolvida uma mensagem de erro do utilizador. Para efazer chamadas de fora do local selecionado, o URL de serviço deve incluir o prefixo geográfico para os locais selecionados. Por exemplo, se o Criador for criado nos Estados Unidos, todas as chamadas para o serviço de conversão devem ser submetidas a `us.atlas.microsoft.com/conversion/convert` .

Além disso, todos os dados importados para o Criador devem ser enviados para a mesma localização geográfica que o recurso Criador. Por exemplo, se o Criador for aprovisionado no Estado Unidos declarado, todos os dados brutos devem ser carregados via `us.atlas.microsoft.com/mapData/upload` .

## <a name="next-steps"></a>Passos seguintes

Introdução ao Criador para mapeamento interior:

> [!div class="nextstepaction"]
> [Upload de dados](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Conversão de Dados](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Conjunto de dados](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Azulejo](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Conjunto de Estado de Recurso](creator-indoor-maps.md#feature-statesets)

Aprenda a usar o Criador para renderizar mapas interiores na sua aplicação:

> [!div class="nextstepaction"]
> [Tutorial do Criador de Mapas Azure](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Estilo dinâmico do mapa interior](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Utilize o módulo Mapas Interiores](how-to-use-indoor-module.md)