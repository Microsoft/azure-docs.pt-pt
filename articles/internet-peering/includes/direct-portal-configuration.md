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
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "81681033"
---
1. Na página **'Criar um Peering',** no **separador Configuração,** preencha as caixas como mostrado aqui.

    * Para **o tipo de peering**, selecione **Direct**.
    * Para **a rede Microsoft**, selecione **AS8075**. Não crie espreitar com ASN 8069. É reservado para aplicações especiais e só é usado pelo [microsoft peering](mailto:peering@microsoft.com).
    * Selecione **SKU** como **Basic Free**. Não selecione Premium Free porque está reservado para aplicações especiais.
    * Selecione a localização **do Metrô** onde pretende configurar olhando.

        > [!NOTE]
        > Se já tiver ligações com a Microsoft na localização selecionada do **Metrô** e estiver a utilizar o portal Azure pela primeira vez para configurar o seu espreitê-lo nesse local, as suas ligações de observação existentes serão listadas na secção **de ligações peering,** como mostrado. A Microsoft converterá automaticamente estas ligações de espreitar para um recurso Azure para que possa geri-las todas, juntamente com as novas ligações, num só local. Para obter mais informações, consulte [Converter um legado Espreitar diretamente para um recurso Azure utilizando o portal](../howto-legacy-direct-portal.md).
        >

1. Em **Ligações de Peering**, selecione **Criar uma nova** linha para cada nova ligação que pretende configurar.

    * Para configurar ou modificar as definições de ligação, selecione o botão de edição para uma linha.

        > [!div class="mx-imgBorder"]
        > ![Botão Editar](../media/setup-direct-conf-tab-edit.png)
    
    * Para eliminar uma linha, selecione **...**  >  **Eliminar**.

        > [!div class="mx-imgBorder"]
        > ![Eliminar botão](../media/setup-direct-conf-tab-delete.png)

    * É-lhe exigido que forneça todas as definições para uma ligação, como mostrado aqui.

         > [!div class="mx-imgBorder"]
         > ![Página de conexão de peering direto](../media/setup-direct-conf-tab-connection.png)

        1. Selecione a **instalação de peering** onde a ligação precisa de ser configurada.
        1. **O Session Address Provider** é utilizado para determinar quem fornece a sub-rede necessária para configurar a sessão BGP entre a sua rede e a Microsoft. Se conseguir fornecer a sub-rede, selecione **Peer**. Caso contrário, selecione **Microsoft** e [Microsoft peering](mailto:peering@microsoft.com) entrará em contato consigo. A escolha desta opção demorará mais tempo para a Microsoft processar o pedido de espreitar. Em alguns casos, a Microsoft pode não ser capaz de fornecer sub-redes, o que resultará em negação de pedido.
        1. Se selecionou a opção **'Provedor de Endereços de Sessão'** como **Peer,** insira os endereços IPv4 e IPv6 juntamente com as máscaras de prefixo prefixo **IPv4** da sessão e as caixas **de prefixo IPv6 da Sessão,** respectivamente.
        1. Insira o número de prefixos IPv4 e IPv6 que irá publicitar nos **endereços IPv4 anunciados máximos** e caixas **de endereços IPv6 publicitadas máximas,** respectivamente.
        1. Ajuste o slider **total da largura de banda** para refletir a largura de banda para a ligação.
        1. **Selecione Guardar** para guardar as definições de ligação.

1. Repita o passo anterior para adicionar mais ligações em qualquer instalação onde a Microsoft esteja a 30 000 metros no **Metro** que selecionou anteriormente.

1. Depois de adicionar todas as ligações necessárias, selecione **Review + create**.

    > [!div class="mx-imgBorder"]
    > ![Final do separador de configuração de espreitamento](../media/setup-direct-conf-tab-final.png)

1. Note que o portal executa a validação básica da informação que introduziu. Uma fita no topo exibe a mensagem *A executar a validação final...*.

    > [!div class="mx-imgBorder"]
    > ![Separador de validação de pares](../media/setup-direct-review-tab-validation.png)

1. Após a alteração da mensagem para *validação passada,* verifique as suas informações. Submeta o pedido selecionando **Criar**. Para modificar o seu pedido, selecione **Previous** e repita os passos.

    > [!div class="mx-imgBorder"]
    > ![Submissão de pares](../media/setup-direct-review-tab-submit.png)

1. Depois de submeter o pedido, aguarde que a implantação termine. Se a implementação falhar, contacte [a Microsoft a espreitar](mailto:peering@microsoft.com). Uma implantação bem sucedida aparece como mostrado aqui.

    > [!div class="mx-imgBorder"]
    > ![Olhando o sucesso](../media/setup-direct-success.png)
