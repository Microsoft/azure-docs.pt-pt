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
ms.openlocfilehash: 2a42ba8809e6895c9eea9f8762513b7fcaa9eb3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775280"
---
1. Na página **Criar uma página De Espreitar,** sob o separador **Configuração,** preencha os campos como mostrado abaixo.

    * Para **o tipo de peering,** selecione *Direct*.
    * Para **a rede Microsoft,** escolha *AS8075*. Não crie o peering com asn 8069. É reservado para aplicações especiais e apenas utilizado pelo [peering](mailto:peering@microsoft.com)da Microsoft .
    * Selecione **SKU** como *Basic Free*. Não selecione *Premium Free* uma vez que está reservado para aplicações especiais.
    * Escolha a localização do **Metro** para onde pretende configurar o seu olhar.

        > [!NOTE]
        > Se já tiver ligações com a Microsoft na localização selecionada do **Metro,** e estiver a usar o portal pela primeira vez para configurar o peering nesse local, então as suas ligações de pares existentes serão listadas na secção de **ligações Peering,** como mostrado abaixo. A Microsoft converterá automaticamente estas ligações de observação para o recurso Azure para que possa geri-las juntamente com as novas ligações, num só local. Consulte Converter um legado Direto para o [recurso Azure usando o portal](../howto-legacy-direct-portal.md) para mais informações.
        >

1. Em **conexões De Peering,** clique **em Criar nova** para adicionar uma linha para cada nova ligação que pretende configurar.

    * Para configurar/modificar as definições de ligação, clique no botão de edição para uma linha.

        > [!div class="mx-imgBorder"]
        > ![Configuração de peering - Edição Direta](../media/setup-direct-conf-tab-edit.png)
    
    * Para eliminar uma linha, clique em **...** botão > **Apagar**.

        > [!div class="mx-imgBorder"]
        > ![Configuração de peering - Edição Direta](../media/setup-direct-conf-tab-delete.png)

    * É necessário fornecer todas as definições para uma ligação, como mostrado abaixo.

         > [!div class="mx-imgBorder"]
         > ![Configuração de peering - Conexão direta](../media/setup-direct-conf-tab-connection.png)

        1. Selecione a **instalação de peering** onde a ligação precisa de ser configurada.
        1. O Fornecedor de Endereços de **Sessão** é utilizado para determinar quem fornece a subnet necessária para configurar a sessão de BGP entre a sua rede e a Microsoft. Se conseguir fornecer sub-rede, escolha *Peer*. Caso contrário, escolha o [peering](mailto:peering@microsoft.com) **da Microsoft** e da Microsoft entrará em contacto consigo. Note que, optando por isso, demoraria mais tempo para a Microsoft processar o pedido de peering. Em alguns casos, a Microsoft pode não ser capaz de fornecer subredes que resultarão em recusa de pedido.
        1. Se escolheu o Fornecedor de Endereços de **Sessão** como *Peer,* introduza o endereço IPv4 e IPv6 juntamente com a máscara de prefixo nos campos **Session IPv4 Prefix** e **Session IPv6 Prefix** respectivamente.
        1. Insira o número de prefixos IPv4 e IPv6 que irá anunciar nos campos **Endereços IPv4 máximos anunciados** e **endereços IPv6 máximos anunciados,** respectivamente.
        1. Ajuste o slider total de **largura de banda** para refletir a largura de banda para a ligação.
        1. Clique **em OK** para guardar as definições de ligação.

1. Repita acima do passo para adicionar mais ligações em qualquer instalação onde a Microsoft esteja colocalizada com a sua rede, dentro do **Metro** selecionado anteriormente.

1. Depois de adicionar todas as ligações necessárias, clique em **Rever + criar**.

    > [!div class="mx-imgBorder"]
    > ![Peering Configuração Tab Final](../media/setup-direct-conf-tab-final.png)

1. Observe que o portal executa a validação básica da informação que inseriu. Isto é exibido numa fita no topo, como *validação final de execução...*.

    > [!div class="mx-imgBorder"]
    > ![Separador de validação de observação](../media/setup-direct-review-tab-validation.png)

1. Depois de se recorrer a *Validação Passada,* verifique as suas informações e submeta o pedido clicando em **Criar**. Se precisar modificar o seu pedido, clique em **Anterior e** repita os passos acima.

    > [!div class="mx-imgBorder"]
    > ![Submeter-se](../media/setup-direct-review-tab-submit.png)

1. Assim que submeter o pedido, aguarde que termine a implantação. Se a implementação falhar, contacte [o peering](mailto:peering@microsoft.com)da Microsoft . Uma implantação bem sucedida aparecerá como abaixo.

    > [!div class="mx-imgBorder"]
    > ![O sucesso do peering](../media/setup-direct-success.png)
