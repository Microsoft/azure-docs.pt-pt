---
title: Associar o ASN do peer à assinatura do Azure usando o portal
titleSuffix: Azure
description: Associar o ASN do peer à assinatura do Azure usando o portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: cee548aff49cd5e4a57eed994b8ade2d157c6313
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75912200"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>Associar o ASN do peer à assinatura do Azure usando o portal

Antes de enviar uma solicitação de emparelhamento, você deve primeiro associar seu ASN à assinatura do Azure usando as etapas abaixo.

Se preferir, você pode concluir este guia usando o [PowerShell](howto-subscription-association-powershell.md).

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Criar PeerAsn para associar seu ASN à assinatura do Azure

### <a name="sign-in-to-the-portal"></a>Entrar no portal
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="register-for-peering-resource-provider"></a>Registrar para provedor de recursos de emparelhamento
Registre-se para provedor de recursos de emparelhamento em sua assinatura seguindo as etapas abaixo. Se você não executar isso, os recursos do Azure necessários para configurar o emparelhamento não estarão acessíveis.

1. Clique em **assinaturas** no canto superior esquerdo do Portal. Se você não o vir, clique em **mais serviços** e pesquise por ele.

    > [!div class="mx-imgBorder"]
    > ![](./media/rp-subscriptions-open.png) de assinaturas abertas

1. Clique na assinatura que você deseja usar para emparelhamento.

    > [!div class="mx-imgBorder"]
    > ](./media/rp-subscriptions-launch.png) ![iniciar assinatura

1. Quando a assinatura for aberta, à esquerda, clique em **provedores de recursos**. Em seguida, no painel direito, pesquise *emparelhamento* na janela de pesquisa ou use a barra de rolagem para localizar **Microsoft. emparelhamento** e examine o **status**. Se o status for ***registrado***, ignore as etapas abaixo e vá para a seção **criar PeerAsn**. Se o status for não ***registrado***, selecione **Microsoft. emparelhamento** e clique em **registrar**.

    > [!div class="mx-imgBorder"]
    > ](./media/rp-register-start.png) de início do registro de ![

1. Observe que o status muda para ***registro***.

    > [!div class="mx-imgBorder"]
    > ![registro em andamento](./media/rp-register-progress.png)

1. Aguarde um mínimo ou então para que ele conclua o registro. Em seguida, clique em **Atualizar** e verifique se o status é ***registrado***.

    > [!div class="mx-imgBorder"]
    > Registro de ![concluído](./media/rp-register-completed.png)

### <a name="create-peerasn"></a>Criar PeerAsn
Você pode criar um novo recurso PeerAsn para associar um número de sistema autônomo (ASN) com a assinatura do Azure. Você pode associar vários ASNs a uma assinatura criando um **PeerAsn** para cada ASN que você precisa associar.

1. Clique em **criar um recurso** > **ver tudo**.

    > [!div class="mx-imgBorder"]
    > ![de pesquisa PeerAsn](./media/peerasn-seeall.png)

1. Procure *PeerAsn* na caixa de pesquisa e pressione *Enter* no teclado. Nos resultados, clique em recurso **PeerAsn** .

    > [!div class="mx-imgBorder"]
    > ![iniciar o PeerAsn](./media/peerasn-launch.png)

1. Depois que **PeerAsn** for iniciado, clique em **criar**.

    > [!div class="mx-imgBorder"]
    > ![criar](./media/peerasn-create.png) PeerAsn

1. Na página **associar um par ASN** , na guia **noções básicas** , preencha os campos conforme mostrado abaixo.

    > [!div class="mx-imgBorder"]
    > ![guia de noções básicas do PeerAsn](./media/peerasn-basics-tab.png)

    * **Nome** corresponde ao nome do recurso e pode ser qualquer coisa que você escolher.  
    * Escolha a **assinatura** com a qual você precisa associar o ASN.
    * O **nome do par** corresponde ao nome da sua empresa e precisa ser o mais próximo possível do seu perfil do PeeringDB. Observe que o valor dá suporte apenas aos caracteres de a-z, A-Z e espaço
    * Insira seu ASN no campo **ASN par** .
    * Clique em **criar novo** e insira o **endereço de email** e o número de **telefone** do seu centro de operações de rede (NOC)
1. Em seguida, clique em **examinar + criar** e observe que o portal executa a validação básica das informações inseridas. Isso é exibido em uma faixa de forma na parte superior, como *executando a validação final...* .

    > [!div class="mx-imgBorder"]
    > ![guia de revisão de PeerAsn](./media/peerasn-review-tab-validation.png)

1. Depois que a mensagem na faixa de medida se transformar em *validação aprovada*, verifique suas informações e envie a solicitação clicando em **criar**. Se a validação não for aprovada, clique em **anterior** e repita as etapas acima para modificar sua solicitação e garantir que os valores inseridos não tenham erros.

    > [!div class="mx-imgBorder"]
    > ![guia de revisão de PeerAsn](./media/peerasn-review-tab.png)

1. Depois de enviar a solicitação, aguarde até que ela conclua a implantação. Se a implantação falhar, entre em contato com [o emparelhamento da Microsoft](mailto:peering@microsoft.com). Uma implantação bem-sucedida será exibida como a seguir.

    > [!div class="mx-imgBorder"]
    > ![PeerAsn com êxito](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>Exibir o status de um PeerAsn
Depois que o recurso PeerAsn for implantado com êxito, você precisará aguardar pela Microsoft para aprovar a solicitação de associação. Pode levar até 12 horas para aprovação. Depois de aprovado, você receberá uma notificação para o endereço de email inserido na seção acima.

> [!IMPORTANT]
> Aguarde até que o ValidationState ative "aprovado" antes de enviar uma solicitação de emparelhamento. Pode levar até 12 horas para essa aprovação.

## <a name="modify-peerasn"></a>Modificar PeerAsn
No momento, não há suporte para a modificação de PeerAsn. Se você precisar modificar, entre em contato com [o emparelhamento da Microsoft](mailto:peering@microsoft.com).

## <a name="delete-peerasn"></a>Excluir PeerAsn
Atualmente, não há suporte para a exclusão de um PeerAsn. Se você precisar excluir PeerAsn, entre em contato com [o emparelhamento da Microsoft](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar um emparelhamento direto usando o portal](howto-direct-portal.md)
* [Converter um emparelhamento direto herdado para o recurso do Azure usando o portal](howto-legacy-direct-portal.md)
* [Criar ou modificar o emparelhamento do Exchange usando o portal](howto-exchange-portal.md)
* [Converter um emparelhamento do Exchange herdado para o recurso do Azure usando o portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, visite [perguntas frequentes sobre emparelhamento da Internet](faqs.md)