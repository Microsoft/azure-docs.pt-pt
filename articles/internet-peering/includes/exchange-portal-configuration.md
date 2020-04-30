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
ms.openlocfilehash: 268703081a2a40e8bcc665889eaeaf8edd673bfd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680962"
---
1. Na página **Criar uma página De Espreitar,** no separador **Configuração,** preencha as caixas conforme mostrado.

    > [!div class="mx-imgBorder"]
    > ![Criar uma página de peering Tipo de peering](../media/setup-exchange-conf-tab.png)

    * Para **o tipo de peering,** selecione **Exchange**.
    * Selecione **SKU** como **Basic Free**.
    * Selecione a localização do **Metro** onde pretende configurar o seu olhar.

        > [!NOTE]
        > Se já tiver ligações com a Microsoft na localização do **Metro** selecionada e estiver a usar o portal pela primeira vez para configurar o peering nesse local, as suas ligações de pares existentes serão listadas na secção de **ligações peering,** como mostrado. A Microsoft converterá automaticamente estas ligações de observação num recurso Azure para que possa geri-las juntamente com as novas ligações num só local. Para mais informações, consulte [Converter um legado Exchange espreitando um recurso Azure utilizando o portal](../howto-legacy-exchange-portal.md).
        >

1. Sob **as ligações Peering,** selecione **Criar uma nova** linha para adicionar uma linha para cada nova ligação que pretende configurar.

    * Para configurar ou modificar as definições de ligação, selecione o botão de edição para uma linha.

        > [!div class="mx-imgBorder"]
        > ![Botão Editar](../media/setup-exchange-conf-tab-edit.png)

    * Para apagar uma **...** linha, selecione...  >  **Apagar**.

        > [!div class="mx-imgBorder"]
        > ![Eliminar botão](../media/setup-exchange-conf-tab-delete.png)

    * É-lhe exigido que forneça todas as definições para uma ligação, como mostrado aqui.

         > [!div class="mx-imgBorder"]
         > ![Página de ligação de peering de troca](../media/setup-exchange-conf-tab-connection.png)

        1. Selecione a **instalação de peering** onde a ligação precisa de ser configurada.
        1. No **endereço IPv4** e nas caixas de **endereçoS IPv6,** introduza os endereços IPv4 e IPv6, respectivamente, que seriam configurados nos routers da Microsoft utilizando o comando do vizinho.
        1. Insira o número de prefixos IPv4 e IPv6 que irá anunciar nos **endereços IPv4 máximos anunciados** e caixas de **endereços IPv6 máximas anunciadas,** respectivamente.
        1. Selecione **OK** para guardar as definições de ligação.

1. Repita o passo para adicionar mais ligações em qualquer instalação onde a Microsoft esteja colocalizada com a sua rede, dentro do **Metro** selecionado anteriormente.

1. Depois de adicionar todas as ligações necessárias, selecione **Review + criar**.

    > [!div class="mx-imgBorder"]
    > ![Separador de configuração de observação](../media/setup-exchange-conf-tab-final.png)

1. Note que o portal executa a validação básica da informação que inseriu. Uma fita na parte superior exibe a mensagem *Execução de validação final...*.

    > [!div class="mx-imgBorder"]
    > ![Separador de validação de observação](../media/setup-direct-review-tab-validation.png)

1. Depois de a mensagem ter *sido aprovada,* verifique as suas informações. Envie o pedido selecionando **Criar**. Para modificar o seu pedido, selecione **Anterior** e repita os passos.

    > [!div class="mx-imgBorder"]
    > ![Submissão de pares](../media/setup-exchange-review-tab-submit.png)

1. Depois de submeter o pedido, aguarde que a colocação termine. Se a implementação falhar, contacte [o peering](mailto:peering@microsoft.com)da Microsoft . Um destacamento bem sucedido aparece como mostrado aqui.

    > [!div class="mx-imgBorder"]
    > ![Olhando para o sucesso](../media/setup-direct-success.png)
