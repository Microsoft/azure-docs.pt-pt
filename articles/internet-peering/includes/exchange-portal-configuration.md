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
ms.openlocfilehash: cd51eca0ea4563e1b56f74677df0829669d9e177
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774500"
---
1. Na página **Criar uma página De Espreitar,** sob o separador **Configuração,** preencha os campos como mostrado abaixo.

    > [!div class="mx-imgBorder"]
    > ![Configuração de peering - Troca](../media/setup-exchange-conf-tab.png)

    * Para **o tipo de peering,** selecione *Exchange*.
    * Selecione **SKU** como *Basic Free*.
    * Escolha a localização do **Metro** para onde pretende configurar o seu olhar.

        > [!NOTE]
        > Se já tiver ligações com a Microsoft na localização selecionada do **Metro,** e estiver a usar o portal pela primeira vez para configurar o peering nesse local, então as suas ligações de pares existentes serão listadas na secção de **ligações Peering,** como mostrado abaixo. A Microsoft converterá automaticamente estas ligações de observação para o recurso Azure para que possa geri-las juntamente com as novas ligações, num só local. Ver [Converter um legado Exchange peering para o recurso Azure usando o portal](../howto-legacy-exchange-portal.md) para mais informações.
        >

1. Em **conexões De Peering,** clique **em Criar nova** para adicionar uma linha para cada nova ligação que pretende configurar.

    * Para configurar/modificar as definições de ligação, clique no botão de edição para uma linha.

        > [!div class="mx-imgBorder"]
        > ![Configuração de peering - Edição de troca](../media/setup-exchange-conf-tab-edit.png)

    * Para eliminar uma linha, clique em **...** botão > **Apagar**.

        > [!div class="mx-imgBorder"]
        > ![Configuração de peering - Edição de troca](../media/setup-exchange-conf-tab-delete.png)

    * É necessário fornecer todas as definições para uma ligação, como mostrado abaixo.

         > [!div class="mx-imgBorder"]
         > ![Configuração de peering - Conexão de troca](../media/setup-exchange-conf-tab-connection.png)

        1. Selecione a **instalação de peering** onde a ligação precisa de ser configurada.
        1. Nos campos **iPv4** endereço e **endereço IPv6**, introduza o endereço IPv4 e IPv6 respectivamente que seria configurado em routers microsoft utilizando o comando do vizinho.
        1. Insira o número de prefixos IPv4 e IPv6 que irá anunciar nos campos **Endereços IPv4 máximos anunciados** e **endereços IPv6 máximos anunciados,** respectivamente.
        1. Clique **em OK** para guardar as definições de ligação.

1. Repita acima do passo para adicionar mais ligações em qualquer instalação onde a Microsoft esteja colocalizada com a sua rede, dentro do **Metro** selecionado anteriormente.

1. Depois de adicionar todas as ligações necessárias, clique em **Rever + criar**.

    > [!div class="mx-imgBorder"]
    > ![Peering Configuração Tab Final](../media/setup-exchange-conf-tab-final.png)

1. Observe que o portal executa a validação básica da informação que inseriu. Isto é exibido numa fita no topo, como *validação final de execução...*.

    > [!div class="mx-imgBorder"]
    > ![Separador de validação de observação](../media/setup-direct-review-tab-validation.png)

1. Depois de se recorrer a *Validação Passada,* verifique as suas informações e submeta o pedido clicando em **Criar**. Se precisar modificar o seu pedido, clique em **Anterior e** repita os passos acima.

    > [!div class="mx-imgBorder"]
    > ![Submeter-se](../media/setup-exchange-review-tab-submit.png)

1. Assim que submeter o pedido, aguarde que termine a implantação. Se a implementação falhar, contacte [o peering](mailto:peering@microsoft.com)da Microsoft . Uma implantação bem sucedida aparecerá como abaixo.

    > [!div class="mx-imgBorder"]
    > ![O sucesso do peering](../media/setup-direct-success.png)
