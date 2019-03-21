---
title: Atributos do cópias de sombra de serviço de sincronização do Azure AD Connect | Documentos da Microsoft
description: Descreve como funcionam os atributos de cópias de sombra no serviço de sincronização do Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10a4078f49abbdf431f42c6cde7cf882112e5848
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57839173"
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Atributos do cópias de sombra de serviço de sincronização do Azure AD Connect
A maioria dos atributos são representados da mesma maneira no Azure AD, como estão no Active Directory no local. Mas alguns atributos têm alguns tratamento especial e o valor do atributo no Azure AD pode ser diferente daquelas que o Azure AD Connect sincroniza.

## <a name="introducing-shadow-attributes"></a>Apresentando os atributos de cópias de sombra
Alguns atributos têm duas representações no Azure AD. O valor no local e um valor calculado são armazenados. Esses atributos adicionais são chamados de atributos de cópias de sombra. Os dois atributos mais comuns onde vir este comportamento são **userPrincipalName** e **proxyAddress**. A alteração nos valores de atributo acontece quando há valores nesses atributos que representam os domínios não verificados. Mas o motor de sincronização no Connect lê o valor no atributo de cópias de sombra, da perspectiva do WF, o atributo foi confirmado pelo Azure AD.

Não é possível ver os atributos de cópias de sombra através do Azure no portal ou com o PowerShell. Mas a compreensão do conceito ajuda-o a resolver problemas de determinados cenários em que o atributo tem valores diferentes no local e na cloud.

Para compreender melhor o comportamento, veja este exemplo do Fabrikam:  
![Domínios](./media/how-to-connect-syncservice-shadow-attributes/domains.png)  
Têm vários sufixos UPN no seu Active Directory no local, mas apenas tem verificado uma.

### <a name="userprincipalname"></a>userPrincipalName
Um utilizador tem os seguintes valores de atributo num domínio não verificado:

| Atributo | Value |
| --- | --- |
| userPrincipalName no local | lee.sperry@fabrikam.com |
| O Azure AD shadowUserPrincipalName | lee.sperry@fabrikam.com |
| UserPrincipalName do Azure AD | lee.sperry@fabrikam.onmicrosoft.com |

O atributo userPrincipalName é o valor visto quando utilizar o PowerShell.

Uma vez que o valor do atributo real no local é armazenado no Azure AD, quando verificar o domínio fabrikam.com, do Azure AD atualiza o atributo userPrincipalName com o valor da shadowUserPrincipalName. Não é necessário que sincronizar as alterações do Azure AD Connect, para esses valores sejam atualizadas.

### <a name="proxyaddresses"></a>proxyAddresses
O mesmo processo para apenas, incluindo domínios verificados também ocorre para o proxyAddresses, mas com alguma lógica adicional. A verificação de domínios verificados só acontece para usuários de caixa de correio. Um utilizador de capacidade de correio ou contacte representar um utilizador na sua organização do Exchange e pode adicionar quaisquer valores em proxyAddresses a esses objetos.

Para um utilizador de caixa de correio, no local ou no Exchange Online, são apresentados apenas os valores para domínios verificados. Ele pode ter um aspeto semelhante a esta:

| Atributo | Value |
| --- | --- |
| proxyAddresses no local | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| ProxyAddresses Exchange Online | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

Neste caso **smtp:abbie.spencer\@fabrikam.com** foi removida uma vez que esse domínio não foi verificado. Mas o Exchange também adicionada **SIP:abbie.spencer\@fabrikamonline.com**. A Fabrikam não utilizado Lync/Skype no local, mas do Azure AD e preparar o Exchange Online para o mesmo.

Essa lógica para o proxyAddresses é referida como **ProxyCalc**. ProxyCalc é invocado com cada alteração de um utilizador quando:

- O utilizador foi atribuído um plano de serviço que inclua o Exchange Online, mesmo que o utilizador não foi licenciado para o Exchange. Por exemplo, se o utilizador é atribuído o SKU de E3 do Office, mas apenas lhe foi atribuído SharePoint Online. Isso vale mesmo que sua caixa de correio ainda esteja no local.
- O atributo msExchRecipientTypeDetails tem um valor.
- Fazer uma alteração para o proxyAddresses ou userPrincipalName.

ProxyCalc poderá demorar algum tempo a processar uma alteração de um utilizador e não é síncrona com o processo de exportação do Azure AD Connect.

> [!NOTE]
> A lógica de ProxyCalc tem alguns comportamentos adicionais para cenários avançados não documentados neste tópico. Este tópico é fornecido para que possa compreender o comportamento e não o documentou lógica interna tudo.

### <a name="quarantined-attribute-values"></a>Valores de atributo em quarentena
Atributos de cópias de sombra também são utilizados quando existem valores de atributos duplicados. Para obter mais informações, consulte [resiliência de atributos duplicados](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="see-also"></a>Consulte também
* [Sincronização do Azure AD Connect](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local com o Azure Active Directory](whatis-hybrid-identity.md).
