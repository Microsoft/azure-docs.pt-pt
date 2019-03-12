---
title: Pesquisa de mapas interativa com o Azure Maps | Documentos da Microsoft
description: Início rápido do Azure - criar uma pesquisa de mapas interativa de demonstração com o Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 03/07/2018
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: cc16c47f3b1764ba6887fd832656dfe2567ba0e4
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588946"
---
# <a name="create-an-interactive-search-map-by-using-azure-maps"></a>Criar um mapa de pesquisa interativa com o Azure Maps

Este artigo demonstra as capacidades do Azure Maps criar um mapa que proporciona aos utilizadores uma experiência de pesquisa interativa. Ele explica-lhe estas etapas básicas:
* Crie sua própria conta do Azure Maps.
* Obtenha a chave de conta para utilizar na aplicação web de demonstração.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-an-account-and-get-your-key"></a>Criar uma conta e obter a chave

1. No canto superior esquerdo do [portal do Azure](https://portal.azure.com), selecione **criar um recurso**.
2. Na **pesquisar no Marketplace** , introduza **Maps**.
3. Em **Resultados**, selecione **Maps**. Selecione o **criar** botão que aparece abaixo do mapa.
4. Sobre o **criar conta do Azure Maps** página, introduza os seguintes valores:
    - O **Nome** da nova conta.
    - A **Subscrição** que quer utilizar para esta conta.
    - O **Grupo de recursos** para esta conta. Pode optar por **criar novo** ou **utilizar existente** grupo de recursos.
    - Selecione o **escalão de preço** à sua escolha.
    - Leitura a **licença** e **declaração de privacidade**. Selecione a caixa de verificação para aceitar os termos.
    - Por fim, selecione o **criar** botão.

    ![Criar uma conta do Azure Maps no portal](./media/quick-demo-map-app/create-account.png)

5. Depois de sua conta for criada com êxito, abra-o e localize a secção de definições do menu de conta. Selecione **chaves** para exibir as chaves primárias e secundárias para a sua conta do Azure Maps. Copie o valor da **Chave Primária** para a área de transferência local para utilizar na secção seguinte.

## <a name="download-the-application"></a>Transferir a aplicação

1. Transfira ou copie o conteúdo do ficheiro [interactiveSearch.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html).
2. Salvar o conteúdo deste ficheiro localmente como **Azuremapdemo**. Abra-o num editor de texto.
3. Procure a cadeia `<insert-key>`. Substitua-o com o **chave primária** valor da secção anterior.

## <a name="open-the-application"></a>Abrir a aplicação

1. Abra o ficheiro **AzureMapDemo.html** num browser da sua preferência.
2. Observe o mapa mostrado da cidade de Los Angeles. Amplie e reduza para ver como o mapa é composto automaticamente com mais ou menos informações consoante o nível de zoom. 
3. Altere o centro predefinido do mapa. No ficheiro **AzureMapDemo.html**, procure a variável com o nome **center**. Substitua o valor do par longitude/latitude desta variável pelos novos valores **[-74.0060, 40.7128]**. Guarde o ficheiro e atualize o browser.
4. Usufrua da experiência de pesquisa interativa. Na caixa de pesquisa no canto superior esquerdo da aplicação web de demonstração, procure **restaurantes**.
5. Mova o rato sobre a lista de endereços e as localizações que são apresentados abaixo da caixa de pesquisa. Observe como o pin correspondente no mapa faz aparecer informações sobre essa localização. Por motivos de privacidade das empresas privadas, são apresentados nomes e endereços fictícios.

    ![Aplicação web de pesquisa interativas](./media/quick-demo-map-app/interactive-search.png)

## <a name="clean-up-resources"></a>Limpar recursos

Os tutoriais detalham como utilizar e configurar o Azure Maps com a sua conta. Não limpe os recursos criados neste início rápido se quiser continuar para os tutoriais. Se não quiser continuar, siga estes passos para limpar os recursos:

1. Fechar o navegador que executa o **Azuremapdemo** aplicação web.
2. No menu à esquerda no portal do Azure, selecione **todos os recursos**. Em seguida, selecione a sua conta do Azure Maps. Na parte superior a **todos os recursos** painel, selecione **eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou a sua conta do Azure Maps e criou uma aplicação de demonstração. Para saber como criar sua própria aplicação com as APIs de mapas do Azure, avance para o tutorial seguinte:

> [!div class="nextstepaction"]
> [Procurar pontos de interesse próximos com o Azure Maps](./tutorial-search-location.md)

Para obter mais exemplos de código e uma experiência interativa de codificação, veja estes guias:

> [!div class="nextstepaction"]
> [Localizar um endereço com o serviço de pesquisa do Azure Maps](./how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [Utilizar o controlo de mapas do Azure Maps](./how-to-use-map-control.md)
