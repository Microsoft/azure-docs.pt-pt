---
title: Quickstart - Criar um perfil e ponto final azure CDN
description: Este início rápido mostra como ativar a CDN do Azure através da criação de um novo perfil e ponto final da CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/30/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: af90166b688dee104e7bda18a88a2fe7c98f657b
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996314"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint"></a>Início Rápido: Criar um perfil e um ponto final da CDN do Azure

Neste arranque rápido, ativa a Rede de Entrega de Conteúdos Azure (CDN) criando um novo perfil CDN, que é uma coleção de um ou mais pontos finais da CDN. Depois de criar um perfil e um ponto final, pode começar a entrega de conteúdos aos seus clientes.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Uma conta de Armazenamento Azure chamada *cdnstorageacct123,* que utiliza para o nome de anfitrião de origem. Para completar este requisito, consulte [Integrar uma conta de Armazenamento Azure com o Azure CDN](cdn-create-a-storage-account-with-cdn.md).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inscreva-se no [portal Azure](https://portal.azure.com) com a sua conta Azure.

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Criar um novo ponto final da CDN

Depois de criar um perfil de CDN, usa-o para criar um ponto final.

1. No Portal do Azure, selecione no dashboard o perfil da CDN que criou. Se não o encontrar, pode abrir o grupo de recursos no qual o criou, ou utilizar a barra de pesquisa no topo do portal, introduzir o nome de perfil e selecionar o perfil a partir dos resultados.
   
1. Na página de perfil da CDN, **selecione + Endpoint**.
   
    ![Perfil da CDN](./media/cdn-create-new-endpoint/cdn-select-endpoint.png)
   
    O painel **Adicionar um ponto final** aparece.

3. Introduza os seguintes valores de definição:

    | Definição | Valor |
    | ------- | ----- |
    | **Nome** | Introduza *o ponto final do CDN-123* para o seu nome de anfitrião final. Este nome deve ser globalmente único em todo o Azure; se já está em uso, insira um nome diferente. Este nome é usado para aceder aos seus recursos em cache no _ &lt;nome&gt;final do_domínio .azureedge.net.|
    | **Tipo de origem** | Selecione **Armazenamento**. | 
    | **Nome de anfitrião da origem** | Selecione o nome de anfitrião da conta De armazenamento Azure que está a utilizar na lista de lançamentos, como *cdnstorageacct123.blob.core.windows.net*. |
    | **Caminho de origem** | Deixe em branco. |
    | **Cabeçalho de anfitrião de origem** | Deixe o valor predefinido (que é o nome do anfitrião da conta de armazenamento). |  
    | **Protocolo** | Deixe as opções **HTTP** e **HTTPS** predefinidas selecionadas. |
    | **Porta de origem** | Deixe os valores de porta predefinidos. | 
    | **Otimizado para** | Deixe a seleção predefinida, **Entrega geral Web**. |

    ![Painel Adicionar ponto final](./media/cdn-create-new-endpoint/cdn-add-endpoint.png)

3. Selecione **Adicionar** para criar o novo ponto final. Depois de o ponto final ser criado, aparece na lista de pontos finais para o perfil.
    
   ![Ponto final da CDN](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
   O tempo que o ponto final demora a propagar-se depende do nível de preços selecionado quando criou o perfil. **A Standard Akamai** normalmente completa dentro de um minuto, **standard Microsoft** em 10 minutos, e **Standard Verizon** e **Premium Verizon** em até 90 minutos.

## <a name="clean-up-resources"></a>Limpar recursos

Nos passos anteriores, criou um perfil e um ponto final de CDN num grupo de recursos. Guarde estes recursos, se pretender aceder aos [Passos seguintes](#next-steps) e aprender a adicionar um domínio personalizado ao ponto final. No entanto, se não pretende utilizar estes recursos no futuro pode eliminá-los, ao eliminar o grupo de recursos, evitando assim encargos adicionais:

1. A partir do menu à esquerda no portal Azure, selecione **grupos de recursos** e, em seguida, selecione **CDNQuickstart-rg**.

2. Na página do **grupo Recurso,** selecione **Eliminar o grupo de recursos**, introduza *cDNQuickstart-rg* na caixa de texto e, em seguida, selecione **Delete**. Esta ação elimina o grupo de recursos, o perfil e o ponto final que criou neste arranque rápido.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Use cDN para servidor conteúdo estático a partir de uma aplicação web](cdn-add-to-web-app.md)

> [!div class="nextstepaction"]
> [Tutorial: Adicionar um domínio personalizado ao ponto final da CDN do Azure](cdn-map-content-to-custom-domain.md)
