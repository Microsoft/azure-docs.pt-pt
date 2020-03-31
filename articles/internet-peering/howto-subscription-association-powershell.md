---
title: Peer asn à subscrição do Azure usando powerShell
titleSuffix: Azure
description: Peer asn à subscrição do Azure usando powerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 77cc4732e017d95cbae19578cf26b1111b08fdde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75908994"
---
# <a name="associate-peer-asn-to-azure-subscription-using-powershell"></a>Peer asn à subscrição do Azure usando powerShell

Antes de submeter um pedido de peering, deve primeiro associar o seu ASN à subscrição do Azure utilizando os passos abaixo.

Se preferir, pode completar este guia utilizando o [portal](howto-subscription-association-portal.md).

### <a name="working-with-azure-powershell"></a>Trabalhar com a Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Crie peerASN para associar o seu ASN à Subscrição Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Inscreva-se na sua conta Azure e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="register-for-peering-resource-provider"></a>Registe-se para o fornecedor de recursos de peering
Registe-se para o fornecedor de recursos de peering na sua subscrição utilizando o comando abaixo. Se não o executar, os recursos do Azure necessários para configurar o peering não são acessíveis.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

Pode verificar o estado de registo utilizando os comandos abaixo:
```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

> [!IMPORTANT]
> Aguarde que *o Estado de Registos* transforme "Registrado" antes de prosseguir. Pode levar 5 a 30 minutos depois de executar o comando.

### <a name="update-the-peer-information-associated-with-this-subscription"></a>Atualize as informações dos pares associadas a esta subscrição

Abaixo está um exemplo para atualizar informações dos pares.

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> -O nome corresponde ao nome do recurso e pode ser tudo o que escolher. No entanto, o nome de pares corresponde ao nome da sua empresa e precisa estar o mais próximo possível do seu perfil PeeringDB. Note que o valor para -peerName suporta apenas caracteres a-z, A-Z e espaço.

Uma subscrição pode ter várias ASNs. Atualize a informação de observação de cada ASN. Certifique-se de que o "nome" é único para cada ASN.

Espera-se que os pares tenham um perfil completo e atualizado no [PeeringDB](https://www.peeringdb.com). Utilizamos estas informações durante o registo para validar os dados do par, tais como informações noC, informações técnicas de contacto e sua presença nas instalações de observação, etc.

Note que, no lugar de **{subscriçãoId}** na saída acima, será apresentado o ID de subscrição real.

## <a name="view-status-of-a-peerasn"></a>Ver estado de um PeerASN

Verifique se o estado de validação ASN utiliza o comando abaixo:

```powershell
Get-AzPeerAsn
```

Abaixo está uma resposta exemplo:
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
> Aguarde que o Estado de Validação vire "Aprovado" antes de apresentar um pedido de peering. Pode levar até 12 horas para esta aprovação.

## <a name="modify-peerasn"></a>Modificar peerasn
Pode modificar as informações de contacto da NOC a qualquer momento.

Abaixo está um exemplo:

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## <a name="delete-peerasn"></a>Eliminar PeerAsn
A desposição de um PeerASN não é atualmente apoiada. Se precisar de eliminar o PeerASN, contacte [o peering da Microsoft](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar um peering Direto](howto-direct-powershell.md)
* [Converter um peering Direto legado para o recurso do Azure](howto-legacy-direct-powershell.md)
* [Criar ou modificar o peering de troca](howto-exchange-powershell.md)
* [Converter um peering do Exchange legado para o recurso do Azure](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Recursos adicionais

Para mais informações, visite [faQs de observação de Internet](faqs.md)
