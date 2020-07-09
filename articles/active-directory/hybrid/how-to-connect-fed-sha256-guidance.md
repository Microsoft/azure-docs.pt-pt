---
title: Mude o algoritmo de haxixe de assinatura para o Office 365 confiando na confiança do partido - Azure
description: Esta página fornece diretrizes para mudar algoritmo SHA para a confiança da federação com o Office 365
keywords: SHA1,SHA256,O365,federação,aadconnect,adfs,ad fs,change sha,federation trust,confiando na confiança do partido
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: db4327f77d466ee66ef7fdbdaa60ef7ebd51f2d0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85359663"
---
# <a name="change-signature-hash-algorithm-for-office-365-relying-party-trust"></a>Mude o algoritmo de hash de assinatura para o Office 365 confiando na confiança do partido
## <a name="overview"></a>Descrição geral
Os Serviços da Federação de Diretório Ativo (AD FS) assinam os seus tokens no Microsoft Azure Ative Directory para garantir que não podem ser adulterados. Esta assinatura pode basear-se em SHA1 ou SHA256. O Azure Ative Directory agora suporta fichas assinadas com um algoritmo SHA256, e recomendamos definir o algoritmo de assinatura de fichas para SHA256 para o mais alto nível de segurança. Este artigo descreve os passos necessários para definir o algoritmo de assinatura simbólica para o nível SHA256 mais seguro.

>[!NOTE]
>A Microsoft recomenda o uso de SHA256 como algoritmo para a assinatura de tokens, uma vez que é mais seguro do que SHA1, mas SHA1 continua a ser uma opção suportada.

## <a name="change-the-token-signing-algorithm"></a>Alterar o algoritmo de assinatura de fichas simbólicas
Depois de definir o algoritmo de assinatura com um dos dois processos abaixo, a AD FS assina os tokens para Office 365 confiando a confiança do partido com SHA256. Não precisa de fazer alterações de configuração extra, e esta alteração não tem qualquer impacto na sua capacidade de aceder ao Office 365 ou a outras aplicações AD do Azure.

### <a name="ad-fs-management-console"></a>Consola de gestão de FS AD
1. Abra a consola de gestão AD FS no servidor principal do AD FS.
2. Expanda o nó AD FS e clique em **Relying Party Trusts**.
3. Clique com o botão direito no seu Office 365/Azure confiando na confiança do partido e selecione **Propriedades**.
4. Selecione o separador **Advanced** e selecione o algoritmo de haxixe seguro SHA256.
5. Clique em **OK**.

![Algoritmo de assinatura SHA256-MMC](./media/how-to-connect-fed-sha256-guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>Cmdlets AD FS PowerShell
1. Em qualquer servidor AD FS, abra o PowerShell sob privilégios de administrador.
2. Desfique o algoritmo de haxixe seguro utilizando o **cmdlet Set-AdfsRelyingPartyTrust.**
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'https://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Leia também
* [Escritório de Reparação 365 confia com Azure AD Connect](how-to-connect-fed-management.md#repairthetrust)

