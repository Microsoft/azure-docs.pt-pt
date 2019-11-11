---
title: 'Início rápido: pesquisa de mapa interativa com mapas do Azure'
description: Início rápido do Azure-criar uma pesquisa de mapa interativo de demonstração usando o Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 1b9d8c98391e7e2bac3492dcf696f098f5c6cf57
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903186"
---
# <a name="quickstart-create-an-interactive-search-map-by-using-azure-maps"></a>Início rápido: criar um mapa de pesquisa interativo usando o Azure Maps

Este artigo demonstra as capacidades do Azure Maps criar um mapa que proporciona aos utilizadores uma experiência de pesquisa interativa. Ele percorre estas etapas básicas:
* Crie sua própria conta do Azure Maps.
* Obtenha sua chave de conta para usar no aplicativo Web de demonstração.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Iniciar sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-an-account-and-get-your-key"></a>Criar uma conta e obter a chave

1. No canto superior esquerdo da [portal do Azure](https://portal.azure.com), selecione **criar um recurso**.
2. Na caixa **Pesquisar no Marketplace** , insira **mapas**.
3. Em **Resultados**, selecione **Maps**. Selecione o botão **criar** que aparece abaixo do mapa.
4. Na página **criar conta do Azure Maps** , insira os seguintes valores:
   - O **Nome** da nova conta.
   - A **Subscrição** que quer utilizar para esta conta.
   - O **Grupo de recursos** para esta conta. Você pode optar por **criar um novo** grupo de recursos ou **usar um existente** .
   - Selecione o **tipo de preço** de sua escolha.
   - Leia a **licença** e a **política de privacidade**. Marque a caixa de seleção para aceitar os termos.
   - Por fim, selecione o botão **criar** .

     ![Criar uma conta do Azure Maps no portal](./media/quick-demo-map-app/create-account.png)

5. Depois que sua conta for criada com êxito, abra-a e localize a seção Configurações do menu conta. Selecione **autenticação** para exibir as chaves primárias e secundárias para sua conta do Azure Maps. Copie o valor da **Chave Primária** para a área de transferência local para utilizar na secção seguinte.

## <a name="download-the-application"></a>Transferir a aplicação

1. Transfira ou copie o conteúdo do ficheiro [interactiveSearch.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html).
2. Salve o conteúdo desse arquivo localmente como **AzureMapDemo. html**. Abra-o em um editor de texto.
3. Procure a cadeia de caracteres `<Your Azure Maps Key>`. Substitua-o pelo valor de **chave primária** da seção anterior.

## <a name="open-the-application"></a>Abrir a aplicação

1. Abra o ficheiro **AzureMapDemo.html** num browser da sua preferência.
2. Observe o mapa mostrado da cidade de Los Angeles. Amplie e reduza para ver como o mapa é composto automaticamente com mais ou menos informações consoante o nível de zoom. 
3. Altere o centro predefinido do mapa. No ficheiro **AzureMapDemo.html**, procure a variável com o nome **center**. Substitua o valor do par longitude/latitude desta variável pelos novos valores **[-74.0060, 40.7128]** . Guarde o ficheiro e atualize o browser.
4. Usufrua da experiência de pesquisa interativa. Na caixa de pesquisa no canto superior esquerdo do aplicativo Web de demonstração, procure por **restaurantes**.
5. Mova o mouse sobre a lista de endereços e locais que aparecem abaixo da caixa de pesquisa. Observe como o PIN correspondente no mapa exibe informações sobre esse local. Por motivos de privacidade das empresas privadas, são apresentados nomes e endereços fictícios.

    ![Aplicativo Web de pesquisa interativa](./media/quick-demo-map-app/interactive-search.png)

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
