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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "81680962"
---
1. Na página **'Criar um Parante',** no **separador Configuração,** preencha as caixas como mostrado.

    > [!div class="mx-imgBorder"]
    > ![Criar um tipo de peering de página de peering de página](../media/setup-exchange-conf-tab.png)

    * Para **o tipo de peering**, selecione **Exchange**.
    * Selecione **SKU** como **Basic Free**.
    * Selecione a localização **do Metrô** onde pretende configurar olhando.

        > [!NOTE]
        > Se já tiver ligações com a Microsoft na localização selecionada do **Metrô** e estiver a utilizar o portal pela primeira vez para configurar o seu espreitamento nesse local, as suas ligações de esprevação existentes serão listadas na secção **de ligações peering,** como mostrado. A Microsoft converterá automaticamente estas ligações de espreitar para um recurso Azure para que possa geri-las juntamente com as novas ligações num só local. Para obter mais informações, consulte [Converter um legado Exchange olhando para um recurso Azure utilizando o portal](../howto-legacy-exchange-portal.md).
        >

1. Em **Ligações de Peering**, selecione **Criar uma nova** linha para cada nova ligação que pretende configurar.

    * Para configurar ou modificar as definições de ligação, selecione o botão de edição para uma linha.

        > [!div class="mx-imgBorder"]
        > ![Botão Editar](../media/setup-exchange-conf-tab-edit.png)

    * Para eliminar uma linha, selecione **...**  >  **Eliminar**.

        > [!div class="mx-imgBorder"]
        > ![Eliminar botão](../media/setup-exchange-conf-tab-delete.png)

    * É-lhe exigido que forneça todas as definições para uma ligação, como mostrado aqui.

         > [!div class="mx-imgBorder"]
         > ![Trocar página de conexão de peering](../media/setup-exchange-conf-tab-connection.png)

        1. Selecione a **instalação de peering** onde a ligação precisa de ser configurada.
        1. Nas caixas **de endereço IPv4** e **IPv6,** insira os endereços IPv4 e IPv6, respectivamente, que seriam configurados nos routers da Microsoft utilizando o comando vizinho.
        1. Insira o número de prefixos IPv4 e IPv6 que irá publicitar nos **endereços IPv4 anunciados máximos** e caixas **de endereços IPv6 publicitadas máximas,** respectivamente.
        1. Selecione **OK** para guardar as definições de ligação.

1. Repita o passo para adicionar mais ligações em qualquer instalação onde a Microsoft esteja a 200 000 metros com a sua rede, dentro do **Metrô** selecionado anteriormente.

1. Depois de adicionar todas as ligações necessárias, selecione **Review + create**.

    > [!div class="mx-imgBorder"]
    > ![Separador de configuração de esprevação](../media/setup-exchange-conf-tab-final.png)

1. Note que o portal executa a validação básica da informação que introduziu. Uma fita no topo exibe a mensagem *A executar a validação final...*.

    > [!div class="mx-imgBorder"]
    > ![Separador de validação de pares](../media/setup-direct-review-tab-validation.png)

1. Após a alteração da mensagem para *validação passada,* verifique as suas informações. Submeta o pedido selecionando **Criar**. Para modificar o seu pedido, selecione **Previous** e repita os passos.

    > [!div class="mx-imgBorder"]
    > ![Submissão de pares](../media/setup-exchange-review-tab-submit.png)

1. Depois de submeter o pedido, aguarde que a colocação termine. Se a implementação falhar, contacte [a Microsoft a espreitar](mailto:peering@microsoft.com). Uma implantação bem sucedida aparece como mostrado aqui.

    > [!div class="mx-imgBorder"]
    > ![Olhando o sucesso](../media/setup-direct-success.png)
