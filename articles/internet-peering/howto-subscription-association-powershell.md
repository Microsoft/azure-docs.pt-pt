---
title: Associar o ASN do par à assinatura do Azure usando o PowerShell
titleSuffix: Azure
description: Associar o ASN do par à assinatura do Azure usando o PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e7239fdedafedc96a382de6c3c2f90b5da4df00c
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774253"
---
# <a name="associate-peer-asn-to-azure-subscription-using-powershell"></a>Associar o ASN do par à assinatura do Azure usando o PowerShell

Antes de enviar uma solicitação de emparelhamento, você deve primeiro associar seu ASN à assinatura do Azure usando as etapas abaixo.

Se preferir, você pode concluir este guia usando o [portal](howto-subscription-association-portal.md).

### <a name="working-with-azure-powershell"></a>Trabalhar com o Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Criar PeerASN para associar seu ASN à assinatura do Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Inicie sessão na sua conta do Azure e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="update-the-peer-information-associated-with-this-subscription"></a>Atualizar as informações de pares associadas a esta assinatura

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> -Name corresponde ao nome do recurso e pode ser qualquer coisa que você escolher. No entanto,-peerName corresponde ao nome da sua empresa e precisa ser o mais próximo possível do seu perfil do PeeringDB. Observe que o valor para-peerName dá suporte apenas a caracteres de a-z, A-Z e a espaço.

Uma assinatura pode ter vários ASNs. Atualize as informações de emparelhamento para cada ASN. Certifique-se de que "nome" seja exclusivo para cada ASN.

Espera-se que os pares tenham um perfil completo e atualizado em [PeeringDB](https://www.peeringdb.com). Usamos essas informações durante o registro para validar os detalhes do par, como informações do NOC, informações de contato técnico e sua presença nos recursos de emparelhamento, etc.

Observe que no lugar de **{SubscriptionId}** na saída acima, a ID da assinatura real será exibida.

## <a name="view-status-of-a-peerasn"></a>Exibir o status de um PeerASN

Verifique o estado de validação ASN usando o comando a seguir:

```powershell
Get-AzPeerAsn
```

Veja abaixo um exemplo de resposta:
```powershell
PeerContactInfo : Microsoft.Azure.PowerShell.Cmdlets.Peering.Models.PSContactInfo
PeerName        : Contoso
ValidationState : Approved
PeerAsnProperty : 1234
Name            : Contoso_1234
Id              : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/Contoso_1234
Type            : Microsoft.Peering/peerAsns
```

> [!IMPORTANT]
> Aguarde até que o ValidationState ative "aprovado" antes de enviar uma solicitação de emparelhamento. Pode levar até 12 horas para essa aprovação.

## <a name="modify-peerasn"></a>Modificar PeerAsn
Você pode modificar as informações de contato do NOC a qualquer momento.

Abaixo está um exemplo:

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## <a name="delete-peerasn"></a>Excluir PeerAsn
Atualmente, não há suporte para a exclusão de um PeerASN. Se você precisar excluir PeerASN, entre em contato com [o emparelhamento da Microsoft](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar um emparelhamento direto](howto-direct-powershell.md)
* [Converter um emparelhamento direto herdado para o recurso do Azure](howto-legacy-direct-powershell.md)
* [Criar ou modificar o emparelhamento do Exchange](howto-exchange-powershell.md)
* [Converter um emparelhamento do Exchange herdado para o recurso do Azure](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, visite [perguntas frequentes sobre emparelhamento da Internet](faqs.md)
