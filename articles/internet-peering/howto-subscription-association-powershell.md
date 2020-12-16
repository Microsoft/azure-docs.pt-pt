---
title: Associar a subscrição do peer ASN à Azure utilizando o PowerShell
titleSuffix: Azure
description: Associar a subscrição do peer ASN à Azure utilizando o PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3b463293899dc2586404d68145943caff3105e89
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590193"
---
# <a name="associate-peer-asn-to-azure-subscription-using-powershell"></a>Associar a subscrição do peer ASN à Azure utilizando o PowerShell

Antes de submeter um pedido de espreitar, deve primeiro associar a sua ASN à subscrição Azure utilizando os passos abaixo.

Se preferir, pode completar este guia utilizando o [portal](howto-subscription-association-portal.md).

### <a name="working-with-azure-powershell"></a>Trabalhar com a Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Crie o PeerASN para associar a sua ASN à Subscrição Azure

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Inscreva-se na sua conta Azure e selecione a sua subscrição
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="register-for-peering-resource-provider"></a>Registre-se para prestador de recursos de esprevação
Registe-se para o fornecedor de recursos de esquisitura na sua assinatura utilizando o comando abaixo. Se não executar isto, os recursos Azure necessários para configurar o seu espreitamento não são acessíveis.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

Pode verificar o estado de registo utilizando os comandos abaixo:
```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

> [!IMPORTANT]
> Aguarde que *o Estado de Registo* rode "Registado" antes de prosseguir. Pode levar 5 a 30 minutos depois de executar o comando.

### <a name="update-the-peer-information-associated-with-this-subscription"></a>Atualizar as informações dos pares associadas a esta subscrição

Abaixo está um exemplo para atualizar as informações dos pares.

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> -O nome corresponde ao nome do recurso e pode ser o que quiser. No entanto, o nome -peerName corresponde ao nome da sua empresa e precisa de estar o mais próximo possível do seu perfil PeeringDB. Note que o valor para -peerName suporta apenas caracteres a-z, A-Z e espaço.

Uma subscrição pode ter várias ASNs. Atualize as informações de observação para cada ASN. Certifique-se de que o "nome" é único para cada ASN.

Espera-se que os pares tenham um perfil completo e atualizado no [PeeringDB](https://www.peeringdb.com). Utilizamos estas informações durante o registo para validar os detalhes do par, tais como informações NOC, informações técnicas de contacto, e a sua presença nas instalações de observação, etc.

Note que no lugar de **{subscriptionId}** na saída acima, será apresentado o ID de subscrição real.

## <a name="view-status-of-a-peerasn"></a>Ver estado de um PeerASN

Consulte o estado de validação DA ASN utilizando o comando abaixo:

```powershell
Get-AzPeerAsn
```

Abaixo está uma resposta de exemplo:
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
> Aguarde que o Estado de Validação transforme "Aprovado" antes de apresentar um pedido de observação. Pode levar até 12 horas para esta aprovação.

## <a name="modify-peerasn"></a>Modificar peerAsn
Pode modificar as informações de contacto da NOC a qualquer momento.

Abaixo está um exemplo:

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## <a name="delete-peerasn"></a>Eliminar PeerAsn
A eliminação de um PeerASN não é atualmente apoiada. Se precisar de eliminar o PeerASN, contacte [a Microsoft a espreitar](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Passos seguintes

* [Criar ou modificar um peering Direto](howto-direct-powershell.md)
* [Converter um peering Direto legado para o recurso do Azure](howto-legacy-direct-powershell.md)
* [Criar ou modificar o persto cambial](howto-exchange-powershell.md)
* [Converter um peering do Exchange legado para o recurso do Azure](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Recursos adicionais

Para mais informações, visite [as PERGUNTAS Frequentes de Observação da Internet](faqs.md)
