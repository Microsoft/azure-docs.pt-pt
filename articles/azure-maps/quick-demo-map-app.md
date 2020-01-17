---
title: 'Início rápido: pesquisa de mapa interativa com mapas do Azure | Mapas do Microsoft Azure'
description: Saiba como criar um aplicativo Web de demonstração para a pesquisa de mapa interativo usando o SDK da Web do Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 1/14/2020
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 4081dd383fc0e7378bc8c8438781c13e6a34e075
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156442"
---
# <a name="quickstart-create-an-interactive-search-map-by-using-azure-maps"></a>Início rápido: criar um mapa de pesquisa interativo usando o Azure Maps

Este artigo demonstra as capacidades do Azure Maps criar um mapa que proporciona aos utilizadores uma experiência de pesquisa interativa. Ele percorre estas etapas básicas:
* Crie sua própria conta do Azure Maps.
* Obtenha sua chave de conta para usar no aplicativo Web de demonstração.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Criar uma conta com o Azure Maps

Crie uma nova conta dos Maps com os seguintes passos:

1. No canto superior esquerdo do [portal do Azure](https://portal.azure.com), clique em **Criar um recurso**.
2. Na caixa *Procurar no Marketplace*, escreva **Maps**.
3. Em *Resultados*, selecione **Maps**. Clique no botão **Criar** que aparece abaixo do mapa.
4. Na página **Criar Conta do Maps**, introduza os seguintes valores:
    * A *Subscrição* que quer utilizar para esta conta.
    * O nome do *Grupo de recursos* para esta conta. Pode optar por *Criar um grupo de recursos novo* ou *Utilizar um grupo de recursos existente*.
    * O *Nome* da nova conta.
    * O *tipo de preço* desta conta.
    * Leia a *Licença* e a *Declaração de Privacidade*, e selecione a caixa de verificação para aceitar os termos.
    * Clique no botão **Criar**.

![Criar a conta do Maps no portal](./media/quick-demo-map-app/create-account.png)

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Obter a chave primária para a sua conta

Depois de a sua conta do Maps ser criada com êxito, obtenha a chave que lhe permite consultar as APIs do Maps. É recomendável usar a chave primária da sua conta como a chave de assinatura ao chamar os serviços do Azure Maps.

1. Abra a sua conta do Maps no portal.
2. Na seção Configurações, selecione **autenticação**.
3. Copie a **Chave primária** para a área de transferência. Guarde-a localmente para a utilizar mais tarde neste tutorial.

![Obter chave primária mapas do Azure no portal do Azure](./media/quick-demo-map-app/get-key.png)

## <a name="download-the-application"></a>Transferir a aplicação

1. Acesse [interactiveSearch. html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html) e clique nele para exibir o conteúdo na interface do usuário do github. Clique com o botão direito do mouse no botão **bruto** e copie o conteúdo do arquivo ou ' salvar como ' para baixar o arquivo.
2. Salve o conteúdo desse arquivo localmente como **AzureMapDemo. html**. Abra-o em um editor de texto.
3. Procure a cadeia de caracteres `<Your Azure Maps Key>`. Substitua-o pelo valor de **chave primária** da seção anterior.

## <a name="open-the-application"></a>Abrir a aplicação

1. Abra o ficheiro **AzureMapDemo.html** num browser da sua preferência.
2. Observe o mapa mostrado da cidade de Los Angeles. Amplie e reduza para ver como o mapa é composto automaticamente com mais ou menos informações consoante o nível de zoom. 
3. Altere o centro predefinido do mapa. No ficheiro **AzureMapDemo.html**, procure a variável com o nome **center**. Substitua o valor do par longitude/latitude desta variável pelos novos valores **[-74.0060, 40.7128]** . Guarde o ficheiro e atualize o browser.
4. Usufrua da experiência de pesquisa interativa. Na caixa de pesquisa no canto superior esquerdo do aplicativo Web de demonstração, procure por **restaurantes**.
5. Mova o mouse sobre a lista de endereços e locais que aparecem abaixo da caixa de pesquisa. Observe como o PIN correspondente no mapa exibe informações sobre esse local. Por motivos de privacidade das empresas privadas, são apresentados nomes e endereços fictícios.

    ![Aplicativo Web de pesquisa de mapa interativo](./media/quick-demo-map-app/interactive-search.png)

## <a name="clean-up-resources"></a>Limpar recursos

Os tutoriais detalham como usar e configurar mapas do Azure com sua conta. Não limpe os recursos criados neste guia de início rápido se você planeja continuar com os tutoriais. Se você não planeja continuar, siga estas etapas para limpar os recursos:

1. Feche o navegador que executa o aplicativo Web **AzureMapDemo. html** .
2. No menu à esquerda na portal do Azure, selecione **todos os recursos**. Em seguida, selecione sua conta do Azure Maps. Na parte superior da folha **todos os recursos** , selecione **excluir**.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou sua conta do Azure Maps e criou um aplicativo de demonstração. Veja os tutoriais a seguir para saber mais sobre mapas do Azure:

> [!div class="nextstepaction"]
> [Pesquisar pontos de interesse próximos usando o Azure Maps](tutorial-search-location.md)

Para obter mais exemplos de código e uma experiência de codificação interativa, consulte estes guias:

> [!div class="nextstepaction"]
> [Localizar um endereço usando o serviço de pesquisa do Azure Maps](how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [Usar o Controle de Mapeamento do Azure Maps](how-to-use-map-control.md)
