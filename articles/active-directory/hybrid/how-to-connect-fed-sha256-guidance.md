---
title: Alterar algoritmo de hash de assinatura para o Office 365 confiando na confiança do partido - Azure
description: Esta página fornece diretrizes para alterar algoritmo SHA para a confiança da federação com o Office 365
keywords: SHA1,SHA256,O365,federação,aadconnect,adfs,ad fs, change sha, federation trust, trust do partido confiando na confiança do partido
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
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2233b434fda628dcf812a62f06541fc4b0296aba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897347"
---
# <a name="change-signature-hash-algorithm-for-office-365-relying-party-trust"></a>Alterar algoritmo de hash de assinatura para o Office 365 confiando na confiança do partido
## <a name="overview"></a>Descrição geral
Os Serviços da Federação de Diretórios Ativos (AD FS) assinam os seus tokens ao Microsoft Azure Ative Directory para garantir que não podem ser adulterados. Esta assinatura pode ser baseada em SHA1 ou SHA256. O Azure Ative Directory agora suporta fichas assinadas com um algoritmo SHA256, e recomendamos que o algoritmo de assinatura de token para SHA256 seja o mais alto nível de segurança. Este artigo descreve os passos necessários para definir o algoritmo de assinatura de símbolos para o nível SHA256 mais seguro.

>[!NOTE]
>A Microsoft recomenda o uso do SHA256 como algoritmo para assinar fichas, uma vez que é mais seguro do que sha1, mas sha1 continua a ser uma opção suportada.

## <a name="change-the-token-signing-algorithm"></a>Mude o algoritmo de assinatura de símbolos
Depois de definir o algoritmo de assinatura com um dos dois processos abaixo, a AD FS assina os tokens para o Office 365 confiando na confiança do partido com SHA256. Não é necessário fazer alterações de configuração extra, e esta alteração não tem impacto na sua capacidade de aceder ao Office 365 ou a outras aplicações da Azure AD.

### <a name="ad-fs-management-console"></a>Consola de gestão AD FS
1. Abra a consola de gestão AD FS no servidor Principal AD FS.
2. Expanda o nó AD FS e clique em **Trusts de Partido Sindo.**
3. Clique à direita no seu Office 365/Azure confiando na confiança do partido e selecione **Propriedades**.
4. Selecione o separador **Advanced** e selecione o algoritmo de hash seguro SHA256.
5. Clique em **OK**.

![SHA256 assinando algoritmo -- MMC](./media/how-to-connect-fed-sha256-guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>Cmdlets AD FS PowerShell
1. Em qualquer servidor AD FS, abra powerShell sob privilégios de administrador.
2. Defina o algoritmo de hash seguro utilizando o cmdlet **Set-AdfsRelyingPartyTrust.**
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'https://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Leia Também
* [Serviço de Reparação 365 confiança com Azure AD Connect](how-to-connect-fed-management.md#repairthetrust)

