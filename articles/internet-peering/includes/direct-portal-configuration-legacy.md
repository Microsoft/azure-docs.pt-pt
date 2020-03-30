---
title: incluir ficheiro
titleSuffix: Azure
description: incluir ficheiro
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 16909e0a5d59b0ae019d91aad00e0168b0c5f433
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775072"
---
1. Na página **Criar uma página De Espreitar,** sob o separador **Configuração,** preencha os campos como mostrado abaixo.

    > [!div class="mx-imgBorder"]
    > ![Configuração de observação - Direto](../media/setup-direct-conf-tab.png)

    * Para **o tipo de peering,** selecione *Direct*.
    * Para **a rede Microsoft,** escolha *AS8075*. Não selecione ASN 8069. É reservado para aplicações especiais e apenas utilizado pelo [peering](mailto:peering@microsoft.com)da Microsoft .
    * Selecione **SKU** como *Basic Free*. Não selecione *Premium Free* uma vez que está reservado para aplicações especiais.
    * Escolha a localização do **Metro** para onde pretende converter o seu olhar para o recurso Azure. Se tiver ligações com a Microsoft na localização do **Metro** selecionada que não sejam convertidas para recurso Azure, então tais ligações serão listadas na secção de **ligações Peering,** como mostrado abaixo. Agora pode converter estas ligações de observação para o recurso Azure.

        > [!div class="mx-imgBorder"]
        > ![Configuração de peering - Conexões Diretas - Legacy](../media/setup-directlegacy-conf-tab.png)

1. Se precisar de atualizar a largura de banda, clique no botão de edição para uma linha, conforme realçado abaixo, para modificar as definições de ligação.

    > [!div class="mx-imgBorder"]
    > ![Configuração de peering - Edição Direta](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > Se pretender adicionar ligações adicionais de pares com a Microsoft na localização do **Metro** selecionada, pode fazê-lo clicando em **Criar um novo** botão. Consulte [Criar ou modificar um 'peering' direto utilizando o portal](../howto-direct-portal.md) para obter mais informações.
    >

1. Clique em **Rever + criar**. Observe que o portal executa a validação básica da informação que inseriu. Isto é exibido numa fita no topo, como *validação final de execução...*.

    > [!div class="mx-imgBorder"]
    > ![Separador de validação de observação](../media/setup-direct-review-tab-validation.png)

1. Depois de se recorrer a *Validação Passada,* verifique as suas informações e submeta o pedido clicando em **Criar**. Se precisar modificar o seu pedido, clique em **Anterior e** repita os passos acima.

    > [!div class="mx-imgBorder"]
    > ![Submeter-se](../media/setup-direct-review-tab-submit.png)

1. Assim que submeter o pedido, aguarde que termine a implantação. Se a implementação falhar, contacte [o peering](mailto:peering@microsoft.com)da Microsoft . Uma implantação bem sucedida aparecerá como abaixo.

    > [!div class="mx-imgBorder"]
    > ![O sucesso do peering](../media/setup-direct-success.png)
