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
ms.openlocfilehash: fda22346a44388248e37473bc7891b8a130569c4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681033"
---
1. Na página **Criar uma página De Espreitar,** no separador **Configuração,** preencha as caixas como mostrado aqui.

    * Para **o tipo de peering,** selecione **Direct**.
    * Para **a rede Microsoft,** selecione **AS8075**. Não crie olhamento com asn 8069. É reservado para aplicações especiais e apenas usado pelo peering da [Microsoft.](mailto:peering@microsoft.com)
    * Selecione **SKU** como **Basic Free**. Não selecione Premium Free porque está reservado para aplicações especiais.
    * Selecione a localização do **Metro** onde pretende configurar o seu olhar.

        > [!NOTE]
        > Se já tiver ligações com a Microsoft na localização do **Metro** selecionada, e estiver a utilizar o portal Azure pela primeira vez para configurar o peering nesse local, as suas ligações de pares existentes serão listadas na secção de **ligações peering,** como mostrado. A Microsoft converterá automaticamente estas ligações de observação num recurso Azure para que possa geri-las todas, juntamente com as novas ligações, num só local. Para mais informações, consulte [Converter um legado Direto espreitando um recurso Azure utilizando o portal](../howto-legacy-direct-portal.md).
        >

1. Sob **as ligações Peering,** selecione **Criar uma nova** linha para adicionar uma linha para cada nova ligação que pretende configurar.

    * Para configurar ou modificar as definições de ligação, selecione o botão de edição para uma linha.

        > [!div class="mx-imgBorder"]
        > ![Botão Editar](../media/setup-direct-conf-tab-edit.png)
    
    * Para apagar uma **...** linha, selecione...  >  **Apagar**.

        > [!div class="mx-imgBorder"]
        > ![Eliminar botão](../media/setup-direct-conf-tab-delete.png)

    * É-lhe exigido que forneça todas as definições para uma ligação, como mostrado aqui.

         > [!div class="mx-imgBorder"]
         > ![Página de ligação de pares diretos](../media/setup-direct-conf-tab-connection.png)

        1. Selecione a **instalação de peering** onde a ligação precisa de ser configurada.
        1. O Fornecedor de Endereços de **Sessão** é utilizado para determinar quem fornece a subnet necessária para configurar a sessão de BGP entre a sua rede e a Microsoft. Se conseguir fornecer a sub-rede, selecione **Peer**. Caso contrário, selecione **Microsoft** e [Microsoft peering](mailto:peering@microsoft.com) entrará em contato consigo. A escolha desta opção levará mais tempo para a Microsoft processar o pedido de peering. Em alguns casos, a Microsoft poderá não ser capaz de fornecer subredes, o que resultará em negação de pedidos.
        1. Se selecionou a opção fornecedor de endereços de **sessão** como **Peer,** introduza os endereços IPv4 e IPv6 juntamente com as máscaras prefixos nas caixas de prefixo da **Sessão IPv4** e nas caixas de **prefixo da Sessão IPv6,** respectivamente.
        1. Insira o número de prefixos IPv4 e IPv6 que irá anunciar nos **endereços IPv4 máximos anunciados** e caixas de **endereços IPv6 máximas anunciadas,** respectivamente.
        1. Ajuste o slider total de largura de **banda** para refletir a largura de banda para a ligação.
        1. Selecione **Guardar** para guardar as definições de ligação.

1. Repita o passo anterior para adicionar mais ligações em qualquer instalação onde a Microsoft esteja colocalizada com a sua rede, dentro do **Metro** que selecionou anteriormente.

1. Depois de adicionar todas as ligações necessárias, selecione **Review + criar**.

    > [!div class="mx-imgBorder"]
    > ![Peering Configuração final](../media/setup-direct-conf-tab-final.png)

1. Note que o portal executa a validação básica da informação que inseriu. Uma fita na parte superior exibe a mensagem *Execução de validação final...*.

    > [!div class="mx-imgBorder"]
    > ![Separador de validação de observação](../media/setup-direct-review-tab-validation.png)

1. Depois de a mensagem ter *sido aprovada,* verifique as suas informações. Envie o pedido selecionando **Criar**. Para modificar o seu pedido, selecione **Anterior** e repita os passos.

    > [!div class="mx-imgBorder"]
    > ![Submissão de pares](../media/setup-direct-review-tab-submit.png)

1. Depois de submeter o pedido, aguarde que a colocação termine. Se a implementação falhar, contacte [o peering](mailto:peering@microsoft.com)da Microsoft . Um destacamento bem sucedido aparece como mostrado aqui.

    > [!div class="mx-imgBorder"]
    > ![Olhando para o sucesso](../media/setup-direct-success.png)
