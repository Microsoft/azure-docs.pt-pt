---
title: Altere o algoritmo de hash de assinatura para o Office 365 da entidade confiadora fidedignidade de terceiros - Azure
description: Esta página fornece diretrizes para alterar o algoritmo SHA para confiança da federação com o Office 365
keywords: SHA1, SHA256, O365, Federação, aadconnect, adfs, ad fs, sha de alteração, fidedignidade de Federação, a confiança de entidade confiadora
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cf6880e2-af78-4cc9-91bc-b64de4428bbd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b6c81343d52f016dc779f9e08176a0d909684c0
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138628"
---
# <a name="change-signature-hash-algorithm-for-office-365-relying-party-trust"></a>Altere o algoritmo de hash de assinatura para o Office 365 confiança de entidade confiadora
## <a name="overview"></a>Descrição geral
Serviços de Federação do Active Directory (AD FS) se inscreve os seus tokens ao Microsoft Azure Active Directory para garantir que eles não podem ser violados. Essa assinatura pode ser baseada em SHA1 ou SHA256. O Azure Active Directory suporta agora tokens assinados com um algoritmo SHA256 e, recomendamos que defina o algoritmo de assinatura de tokens para SHA256 para o nível mais elevado de segurança. Este artigo descreve os passos necessários para definir o algoritmo de assinatura de tokens para a mais segura SHA256 nível.

>[!NOTE]
>A Microsoft recomenda a utilização de SHA256 como o algoritmo de assinatura de tokens como ele é mais seguro do que SHA1 mas SHA1 continua a ser uma opção com suporte.

## <a name="change-the-token-signing-algorithm"></a>Alterar o algoritmo de assinatura de tokens
Depois de ter de definir o algoritmo de assinatura com um dos dois processos abaixo, o AD FS assina os tokens para o Office 365 com SHA256 confiança de entidade confiadora. Não é necessário efetuar quaisquer alterações de configuração adicional e esta alteração não tem qualquer impacto sobre a capacidade de aceder ao Office 365 ou outras aplicações do Azure AD.

### <a name="ad-fs-management-console"></a>Consola de gestão do AD FS
1. Abra a consola de gestão do AD FS no servidor do AD FS primário.
2. Expanda o nó do AD FS e clique em **entidade Confiadora confianças**.
3. A confiança da entidade confiadora de terceiros do Office 365/Azure com o botão direito e selecione **propriedades**.
4. Selecione o **avançadas** separador e selecione o algoritmo de hash seguro SHA256.
5. Clique em **OK**.

![Algoritmo de assinatura SHA256 – MMC](./media/how-to-connect-fed-sha256-guidance/mmc.png)

### <a name="ad-fs-powershell-cmdlets"></a>Cmdlets do AD FS PowerShell
1. Em qualquer servidor do AD FS, abra o PowerShell com privilégios de administrador.
2. Definir o algoritmo de hash seguro com o **conjunto AdfsRelyingPartyTrust** cmdlet.
   
   <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'https://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

## <a name="also-read"></a>Leia também
* [Reparar a confiança do Office 365 com o Azure AD Connect](how-to-connect-fed-management.md#repairthetrust)

