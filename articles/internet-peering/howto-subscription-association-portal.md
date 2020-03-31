---
title: Associar o ASN do elemento de rede à subscrição do Azure com o portal
titleSuffix: Azure
description: Associar o ASN do elemento de rede à subscrição do Azure com o portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: cee548aff49cd5e4a57eed994b8ade2d157c6313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75912200"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>Associar o ASN do elemento de rede à subscrição do Azure com o portal

Antes de submeter um pedido de peering, deve primeiro associar o seu ASN à subscrição do Azure utilizando os passos abaixo.

Se preferir, pode completar este guia utilizando o [PowerShell](howto-subscription-association-powershell.md).

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Crie peerAsn para associar o seu ASN à Subscrição Azure

### <a name="sign-in-to-the-portal"></a>Inscreva-se no portal
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="register-for-peering-resource-provider"></a>Registe-se para o fornecedor de recursos de peering
Registe-se para o fornecedor de recursos de peering na sua subscrição seguindo os passos abaixo. Se não o executar, os recursos do Azure necessários para configurar o peering não são acessíveis.

1. Clique em **Assinaturas** no canto superior esquerdo do portal. Se não o vir, clique em **Mais serviços** e procure-o.

    > [!div class="mx-imgBorder"]
    > ![Subscrições abertas](./media/rp-subscriptions-open.png)

1. Clique na subscrição que pretende utilizar para espreitar.

    > [!div class="mx-imgBorder"]
    > ![Assinatura de lançamento](./media/rp-subscriptions-launch.png)

1. Assim que a subscrição abrir, à esquerda, clique nos fornecedores de **Recursos.** Em seguida, no painel certo, procure *olhando* para a janela de pesquisa, ou use a barra de pergaminho para encontrar **Microsoft.Peering** e olhe para o **Status**. Se o estado estiver ***registado,*** salte os passos abaixo e siga para a secção **Create PeerAsn**. Se o estado não estiver ***Registado***, selecione **Microsoft.Peering** e clique no **Register**.

    > [!div class="mx-imgBorder"]
    > ![Início das inscrições](./media/rp-register-start.png)

1. Observe que o estado muda para ***o Registo***.

    > [!div class="mx-imgBorder"]
    > ![Registo em curso](./media/rp-register-progress.png)

1. Aguarde um min ou assim para que complete o registo. Em seguida, clique em **Refresh** e verifique se o estado está ***registado***.

    > [!div class="mx-imgBorder"]
    > ![Inscrição concluída](./media/rp-register-completed.png)

### <a name="create-peerasn"></a>Criar peerasn
Pode criar um novo recurso PeerAsn para associar um Número de Sistema Autónomo (ASN) à subscrição do Azure. Pode associar várias ASNs a uma subscrição criando um **PeerAsn** para cada ASN que precisa de associar.

1. Clique **em Criar um recurso** > **Ver tudo.**

    > [!div class="mx-imgBorder"]
    > ![Pesquisar PeerAsn](./media/peerasn-seeall.png)

1. Procure *peerAsn* na caixa de pesquisa e bata *Enter* no seu teclado. A partir dos resultados, clique no recurso **PeerAsn.**

    > [!div class="mx-imgBorder"]
    > ![Lançar PeerAsn](./media/peerasn-launch.png)

1. Assim **que o PeerAsn** for lançado, clique **em Criar**.

    > [!div class="mx-imgBorder"]
    > ![Criar peerasn](./media/peerasn-create.png)

1. Na página **Associate a Peer ASN,** sob o separador **Basics,** preencha os campos como mostrado abaixo.

    > [!div class="mx-imgBorder"]
    > ![Separador de bases peerasn](./media/peerasn-basics-tab.png)

    * **O nome** corresponde ao nome do recurso e pode ser tudo o que escolher.  
    * Escolha a **Subscrição** com a qual precisa para associar a ASN.
    * **O nome** dos pares corresponde ao nome da sua empresa e precisa estar o mais próximo possível do seu perfil PeeringDB. Note que o valor suporta apenas caracteres a-z, A-Z e espaço
    * Insira o seu ASN no campo **Peer ASN.**
    * Clique em **Criar novo** e insira o ENDEREÇO DE **E-MAIL** e **o NÚMERO DE TELEFONE** para o seu Centro de Operações de Rede (NOC)
1. Em seguida, clique em **Rever + criar** e observar que o portal executa a validação básica da informação que inseriu. Isto é exibido numa fita no topo, como *validação final de execução...*.

    > [!div class="mx-imgBorder"]
    > ![Separador de revisão peerasn](./media/peerasn-review-tab-validation.png)

1. Assim que a mensagem na fita se virar para *validação passada,* verifique as suas informações e submeta o pedido clicando em **Criar**. Se a validação não passar, clique em **Anterior** e repita os passos acima para modificar o seu pedido e certifique-se de que os valores em que entra não têm erros.

    > [!div class="mx-imgBorder"]
    > ![Separador de revisão peerasn](./media/peerasn-review-tab.png)

1. Depois de submeter o pedido, aguarde que termine a implantação. Se a implementação falhar, contacte [o peering](mailto:peering@microsoft.com)da Microsoft . Uma implantação bem sucedida aparecerá como abaixo.

    > [!div class="mx-imgBorder"]
    > ![Sucesso PeerAsn](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>Ver estado de um PeerAsn
Assim que o recurso PeerAsn for implementado com sucesso, terá de esperar que a Microsoft aprove o pedido de associação. Pode levar até 12 horas para aprovação. Uma vez aprovado, receberá uma notificação para o endereço de e-mail introduzido na secção acima.

> [!IMPORTANT]
> Aguarde que o Estado de Validação vire "Aprovado" antes de apresentar um pedido de peering. Pode levar até 12 horas para esta aprovação.

## <a name="modify-peerasn"></a>Modificar peerasn
A modificação do PeerAsn não é atualmente suportada. Se precisar de modificar, contacte o [peering da Microsoft](mailto:peering@microsoft.com).

## <a name="delete-peerasn"></a>Eliminar PeerAsn
A apagar um PeerAsn não é atualmente apoiada. Se precisar de eliminar o PeerAsn, contacte [o peering](mailto:peering@microsoft.com)da Microsoft .

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar um peering Direto com o portal](howto-direct-portal.md)
* [Converter um peering Direto legado para o recurso do Azure com o portal](howto-legacy-direct-portal.md)
* [Criar ou modificar o peering de troca utilizando o portal](howto-exchange-portal.md)
* [Converter um peering do Exchange legado para o recurso do Azure com o portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Recursos adicionais

Para mais informações, visite [faQs de observação de Internet](faqs.md)