---
title: Associar o ASN do elemento de rede à subscrição do Azure com o portal
titleSuffix: Azure
description: Associar o ASN do elemento de rede à subscrição do Azure com o portal
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: how-to
ms.date: 5/18/2020
ms.author: derekol
ms.openlocfilehash: 22cb179925f95fd0762371cc904fcbd02675339a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540273"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>Associar o ASN do elemento de rede à subscrição do Azure com o portal

Como Fornecedor de Serviços de Internet ou Fornecedor de Troca de Internet, antes de submeter um pedido de observação, deve primeiro associar a sua ASN a uma subscrição do Azure utilizando os passos abaixo.

Se preferir, pode completar este guia utilizando o [PowerShell](howto-subscription-association-powershell.md).

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Crie o PeerAsn para associar a sua ASN à Subscrição Azure

### <a name="sign-in-to-the-portal"></a>Inscreva-se no portal
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="register-for-peering-resource-provider"></a>Registre-se para prestador de recursos de esprevação
Registe-se para o fornecedor de recursos de espreguiçadamento na sua subscrição seguindo os passos abaixo. Se não executar isto, os recursos Azure necessários para configurar o seu espreitamento não são acessíveis.

1. Clique em **Assinaturas** no canto superior esquerdo do portal. Se não o vir, clique em **Mais serviços** e procure-o.

    > [!div class="mx-imgBorder"]
    > ![Assinaturas abertas](./media/rp-subscriptions-open.png)

1. Clique na subscrição que pretende utilizar para espreitar.

    > [!div class="mx-imgBorder"]
    > ![Assinatura de lançamento](./media/rp-subscriptions-launch.png)

1. Assim que a subscrição abrir, à esquerda, clique nos **fornecedores de Recursos**. Em seguida, no painel direito, procure *espreitar* na janela de pesquisa, ou use a barra de deslocamento para encontrar **microsoft.Peering** e olhar para o **Estado**. Se o estado estiver ***registado,*** salte os passos abaixo e proceda à secção **Criar PeerAsn**. Se o estado não estiver ***registado,*** selecione **Microsoft.Peering** e clique em **Registar**.

    > [!div class="mx-imgBorder"]
    > ![Início das inscrições](./media/rp-register-start.png)

1. Observe que o estado muda para ***registar***.

    > [!div class="mx-imgBorder"]
    > ![Registo em curso](./media/rp-register-progress.png)

1. Aguarde um minuto ou mais para que complete o registo. Em seguida, clique em **Refresh** e verifique se o estado está ***registado***.

    > [!div class="mx-imgBorder"]
    > ![Inscrição concluída](./media/rp-register-completed.png)

### <a name="create-peerasn"></a>Criar PeerAsn
Como Fornecedor de Serviços de Internet ou Fornecedor de Troca de Internet, pode criar um novo recurso PeerAsn para associar um Número de Sistema Autónomo (ASN) com a subscrição Azure na [página Associate a Peer ASN](https://go.microsoft.com/fwlink/?linkid=2129592) . Pode associar várias ASNs a uma subscrição criando um **PeerAsn** para cada ASN que precisa para associar.

1. Na página **Peer ASN,** no separador **Basics,** preencha os campos como mostrado abaixo.

    > [!div class="mx-imgBorder"]
    > ![Separador De Bases PeerAsn](./media/peerasn-basics-tab.png)

    * **O nome** corresponde ao nome do recurso e pode ser o que quiser.  
    * Escolha a **Subscrição** com a qual necessita para associar a ASN.
    * **O nome de pares** corresponde ao nome da sua empresa e precisa de estar o mais próximo possível do seu perfil PeeringDB. Note que o valor suporta apenas caracteres a-z, A-Z e espaço
    * Insira o seu ASN no campo **Peer ASN.**
    * Clique em **Criar novo** e insira endereço **de e-mail** e **NÚMERO DE TELEFONE** para o seu Centro de Operações de Rede (NOC)
1. Em seguida, clique em **Rever + criar** e observar que o portal executa a validação básica da informação que introduziu. Isto é exibido numa fita em cima, como *a validação final de Running...*.

    > [!div class="mx-imgBorder"]
    > ![A screenshot mostra ao Associado um separador Peer A S N Basics.](./media/peerasn-review-tab-validation.png)

1. Assim que a mensagem na fita se voltar para *Validação Passada,* verifique as suas informações e envie o pedido clicando em **Criar**. Se a validação não passar, clique em **Anterior** e repita os passos acima para modificar o seu pedido e garantir que os valores introduzidos não têm erros.

    > [!div class="mx-imgBorder"]
    > ![A screenshot mostra o separador Peer A S N Basics com validação passada.](./media/peerasn-review-tab.png)

1. Depois de submeter o pedido, aguarde que esteja concluído. Se a implementação falhar, contacte [a Microsoft a espreitar](mailto:peering@microsoft.com). Uma implementação bem sucedida aparecerá como abaixo.

    > [!div class="mx-imgBorder"]
    > ![Sucesso PeerAsn](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>Ver estado de um PeerAsn
Uma vez implementado o recurso PeerAsn com sucesso, terá de esperar que a Microsoft aprove o pedido de associação. Pode levar até 12 horas para aprovação. Uma vez aprovado, receberá uma notificação para o endereço de e-mail inserido na secção acima.

> [!IMPORTANT]
> Aguarde que o Estado de Validação transforme "Aprovado" antes de apresentar um pedido de observação. Pode levar até 12 horas para esta aprovação.

## <a name="modify-peerasn"></a>Modificar peerAsn
Modificar o PeerAsn não é suportado atualmente. Se precisar de modificar, contacte [a Microsoft a espreitar.](mailto:peering@microsoft.com)

## <a name="delete-peerasn"></a>Eliminar PeerAsn
A eliminação de um PeerAsn não é atualmente apoiada. Se precisar de eliminar o PeerAsn, contacte [a Microsoft a espreitar](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar um peering Direto com o portal](howto-direct-portal.md)
* [Converter um peering Direto legado para o recurso do Azure com o portal](howto-legacy-direct-portal.md)
* [Criar ou modificar o persto cambial usando o portal](howto-exchange-portal.md)
* [Converter um peering do Exchange legado para o recurso do Azure com o portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Recursos adicionais

Para mais informações, visite [as PERGUNTAS Frequentes de Observação da Internet](faqs.md)
