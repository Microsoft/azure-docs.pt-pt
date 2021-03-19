---
title: Atributos de sombra de sincronização Azure AD Connect | Microsoft Docs
description: Descreve como os atributos de sombra funcionam no serviço de sincronização Azure AD Connect.
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
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 128303cb51b39db8442fdda71f949db17923bfa2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90088975"
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Atributos de sombra de sincronização Azure AD Connect
A maioria dos atributos são representados da mesma forma em Azure AD como estão no seu Ative Directory no local. Mas alguns atributos têm algum manuseamento especial e o valor do atributo em Azure AD pode ser diferente do que o Azure AD Connect sincroniza.

## <a name="introducing-shadow-attributes"></a>Introduzindo atributos de sombra
Alguns atributos têm duas representações em Azure AD. Tanto o valor no local como o valor calculado são armazenados. Estes atributos extras são chamados atributos de sombra. Os dois atributos mais comuns onde vê este comportamento são **userPrincipalName** e **proxyAddress**. A alteração dos valores de atributos ocorre quando há valores nestes atributos que representam domínios não verificados. Mas o motor de sincronização em Connect lê o valor no atributo sombra assim, na sua perspetiva, o atributo foi confirmado pelo Azure AD.

Não é possível ver os atributos de sombra utilizando o portal Azure ou com o PowerShell. Mas compreender o conceito ajuda-o a resolver determinados cenários em que o atributo tem valores diferentes no local e na nuvem.

Para entender melhor o comportamento, veja este exemplo de Fabrikam:  
![O Screenshot mostra o Sufixo upn do Ative Directory para vários exemplos com um valor correspondente de domínio AD AD de Não Adicionado, Não Verificado e Verificado.](./media/how-to-connect-syncservice-shadow-attributes/domains.png)  
Têm vários sufixos UPN nos seus locais de Diretório Ativo, mas só verificaram um.

### <a name="userprincipalname"></a>userPrincipalName
Um utilizador tem os seguintes valores de atributos num domínio não verificado:

| Atributo | Valor |
| --- | --- |
| utilizador no localPrincipalName | lee.sperry@fabrikam.com |
| Azure AD shadowUserPrincipalName | lee.sperry@fabrikam.com |
| Utilizador da Azure ADPrincipalName | lee.sperry@fabrikam.onmicrosoft.com |

O atributo UserPrincipalName é o valor que se vê ao utilizar o PowerShell.

Uma vez que o valor real do atributo no local é armazenado em Azure AD, quando verifica o domínio fabrikam.com, o Azure AD atualiza o atributo userPrincipalName com o valor do shadowUserPrincipalName. Não é necessário sincronizar quaisquer alterações a partir do Azure AD Connect para que estes valores sejam atualizados.

### <a name="proxyaddresses"></a>proxyAddresses
O mesmo processo para incluir apenas domínios verificados também ocorre para proxyAddresses, mas com alguma lógica adicional. A verificação de domínios verificados só acontece para os utilizadores da caixa de correio. Um utilizador ou contacto via correio representa um utilizador noutra organização do Exchange e pode adicionar quaisquer valores em proxyAddresses a estes objetos.

Para um utilizador de caixa de correio, no local ou no Exchange Online, apenas aparecem valores para domínios verificados. Pode parecer assim:

| Atributo | Valor |
| --- | --- |
| no local proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| Exchange Online proxyAddresses | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

Neste **caso, o \@ fabrikam.com smtp:abbie.spencer** foi removido uma vez que esse domínio não foi verificado. Mas Exchange também acrescentou **SIP:abbie.spencer \@ fabrikamonline.com**. A Fabrikam não usou o Lync/Skype no local, mas a Azure AD e a Exchange Online preparam-se para isso.

Esta lógica para proxyAddresses é referida como **ProxyCalc**. O ProxyCalc é invocado com todas as alterações de um utilizador quando:

- Foi atribuído ao utilizador um plano de serviço que inclui o Exchange Online mesmo que o utilizador não tenha sido licenciado para o Exchange. Por exemplo, se o utilizador for designado o Office E3 SKU, mas apenas foi atribuído o SharePoint Online. Isto é verdade mesmo que a sua caixa de correio ainda esteja no local.
- O atributo msExchRecipientTypeDetails tem um valor.
- Faz uma alteração para proxyAddresses ou userPrincipalName.

O ProxyCalc pode demorar algum tempo a processar uma mudança num utilizador e não é sincronizado com o processo de exportação Azure AD Connect.

> [!NOTE]
> A lógica ProxyCalc tem alguns comportamentos adicionais para cenários avançados não documentados neste tópico. Este tópico é fornecido para que você entenda o comportamento e não documente toda a lógica interna.

### <a name="quarantined-attribute-values"></a>Valores de atributos em quarentena
Os atributos de sombra também são usados quando existem valores de atributos duplicados. Para obter mais informações, consulte [a duplicação da resiliência do atributo](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="see-also"></a>Ver também
* [Sincronização Azure Ad Connect](how-to-connect-sync-whatis.md)
* [Integrando as suas identidades no local com o Azure Ative Directory](whatis-hybrid-identity.md).
