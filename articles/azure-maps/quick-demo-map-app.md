---
title: 'Quickstart: Pesquisa de mapas interativos com mapas Azure'
description: 'Quickstart: Aprenda a criar mapas interativos e pesquisáveis. Veja como criar uma conta Azure Maps, obter uma chave primária e usar o Web SDK para configurar aplicações de mapas'
author: anastasia-ms
ms.author: v-stharr
ms.date: 7/10/2020
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 24a834c87fe34d90dec5961bb3f8d376c6e5e62d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373220"
---
# <a name="quickstart-create-an-interactive-search-map-with-azure-maps"></a>Quickstart: Criar um mapa de pesquisa interativo com mapas Azure

Este artigo mostra-lhe como usar o Azure Maps para criar um mapa que dá aos utilizadores uma experiência de pesquisa interativa. Acompanha-o através destes passos básicos:

* Crie a sua própria conta Azure Maps.
* Obtenha a sua chave principal para usar na aplicação web de demonstração.
* Faça o download e abra a aplicação do mapa de demonstração.

Este quickstart utiliza o Azure Maps Web SDK, no entanto os serviços Azure Maps podem ser utilizados com qualquer controlo de mapas. [Aqui](open-source-projects.md#third-part-map-control-plugins) estão alguns populares controlos de mapas abertos para os que a equipa do Azure Maps criou plugin's.

## <a name="prerequisites"></a>Pré-requisitos

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Inicie sessão no [portal do Azure](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-azure-maps-account"></a>Criar uma conta do Azure Maps

Crie uma nova conta Azure Maps com os seguintes passos:

1. No canto superior esquerdo do [portal Azure,](https://portal.azure.com)clique em **Criar um recurso**.
2. Na *caixa De Pesquisa no Mercado,* escreva **Azure Maps**.
3. A partir dos *Resultados,* selecione **Azure Maps**. Clique no botão **Criar** que aparece abaixo do mapa.
4. Na página **Criar Conta do Maps**, introduza os seguintes valores:
    * A *Subscrição* que quer utilizar para esta conta.
    * O nome do *Grupo de recursos* para esta conta. Pode optar por *Criar um grupo de recursos novo* ou *Utilizar um grupo de recursos existente*.
    * O *Nome* da nova conta.
    * O *nível de preços* desta conta.
    * Leia a *Licença* e a *Declaração de Privacidade*, e selecione a caixa de verificação para aceitar os termos.
    * Clique no botão **Criar**.

    :::image type="content" source="./media/quick-demo-map-app/create-account.png" alt-text="Criar a conta do Maps no portal":::

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Obter a chave primária para a sua conta

Assim que a sua conta Maps for criada com sucesso, recupere a chave primária que lhe permite consultar as APIs do Maps.

1. Abra a sua conta do Maps no portal.
2. Na secção de definições, selecione **Autenticação**.
3. Copie a **Chave primária** para a área de transferência. Guarde-a localmente para a utilizar mais tarde neste tutorial.

>[!NOTE]
> Se utilizar a chave de subscrição em vez da chave primária, o seu mapa não renderizará corretamente. Além disso, por razões de segurança, recomenda-se que rode entre as suas teclas primária e secundária. Para rodar as teclas, atualize a sua aplicação para utilizar a tecla secundária, desloque-a e, em seguida, pressione o botão ciclo/atualização ao lado da tecla primária para gerar uma nova tecla primária. A velha chave primária será desativada. Para obter mais informações sobre a rotação da chave, consulte [Configurar o Cofre da Chave Azure com rotação e auditoria de chaves](../key-vault/secrets/tutorial-rotation-dual.md)

:::image type="content" source="./media/quick-demo-map-app/get-key.png" alt-text="Obtenha a chave principal Azure Maps no portal Azure":::

## <a name="download-the-demo-application"></a>Transferir a aplicação de demonstração

1. Vá para [interactiveSearch.html.](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html) Copie o conteúdo do ficheiro.
2. Guarde o conteúdo deste ficheiro localmente como **AzureMapDemo.html**. Abra-o num editor de texto.
3. Procure a `<Your Azure Maps Key>` corda. Substitua-a pelo valor da **chave primária** da secção anterior.

## <a name="open-the-demo-application"></a>Abra a aplicação de demonstração

1. Abra o ficheiro **AzureMapDemo.html** num browser da sua preferência.
2. Observe o mapa mostrado da cidade de Los Angeles. Amplie e reduza para ver como o mapa é composto automaticamente com mais ou menos informações consoante o nível de zoom.
3. Altere o centro predefinido do mapa. No ficheiro **AzureMapDemo.html**, procure a variável com o nome **center**. Substitua o valor do par longitude/latitude desta variável pelos novos valores **[-74.0060, 40.7128]**. Guarde o ficheiro e atualize o browser.
4. Usufrua da experiência de pesquisa interativa. Na caixa de pesquisa no canto superior esquerdo da aplicação web de demonstração, procure **restaurantes.**
5. Desloque o rato sobre a lista de endereços e locais que aparecem abaixo da caixa de pesquisa. Repare como o pino correspondente no mapa mostra informações sobre o local. Por motivos de privacidade das empresas privadas, são apresentados nomes e endereços fictícios.

    :::image type="content" source="./media/quick-demo-map-app/interactive-search.png" alt-text="Aplicação web de pesquisa de mapas interativos":::


## <a name="clean-up-resources"></a>Limpar os recursos

>[!WARNING]
>Os tutoriais listados na secção [Next Steps](#next-steps) detalham como usar e configurar mapas Azure com a sua conta. Não limpe os recursos criados neste quickstart se pretende continuar para os tutoriais.

Se não pretende continuar com os tutoriais, tome estes passos para limpar os recursos:

1. Feche o navegador que executa a **aplicação webAzureMapDemo.html.**
2. Navegue para a página do portal Azure. Selecione **Todos os recursos** da página principal do portal. Ou, clique no ícone do menu no canto superior esquerdo. Selecione **Todos os recursos**.
3. Clique na sua conta Azure Maps. No topo da página, clique em **Apagar**.

Para mais exemplos de código e uma experiência de codificação interativa, consulte estes guias:

[Encontre um endereço com o serviço de pesquisa Azure Maps](how-to-search-for-address.md)

[Use o controlo do mapa dos mapas Azure Maps](how-to-use-map-control.md)

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, criou a sua conta Azure Maps e criou uma aplicação de demonstração. Veja os seguintes tutoriais para saber mais sobre o Azure Maps:

> [!div class="nextstepaction"]
> [Procure pontos de interesse nas proximidades com a Azure Maps](tutorial-search-location.md)