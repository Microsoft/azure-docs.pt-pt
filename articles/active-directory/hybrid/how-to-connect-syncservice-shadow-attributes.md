---
title: Ad Connect do Azure Connect atribui asinções de sombra de sincronização / Microsoft Docs
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
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10a4078f49abbdf431f42c6cde7cf882112e5848
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "60384722"
---
# <a name="azure-ad-connect-sync-service-shadow-attributes"></a>Ad AD Connect atributos de sombra de sincronização do serviço
A maioria dos atributos são representados da mesma forma em Azure AD como estão no seu Diretório Ativo no local. Mas alguns atributos têm algum manuseamento especial e o valor do atributo em Azure AD pode ser diferente do que o Azure AD Connect sincroniza.

## <a name="introducing-shadow-attributes"></a>Introduzindo atributos de sombra
Alguns atributos têm duas representações em Azure AD. Tanto o valor no local como o valor calculado são armazenados. Estes atributos extra são chamados atributos de sombra. Os dois atributos mais comuns onde vê este comportamento são **userPrincipalName** e **proxyAddress**. A alteração dos valores de atributos acontece quando há valores nestes atributos que representam domínios não verificados. Mas o motor sincronizado em Connect lê o valor no atributo sombra assim, do seu ponto de vista, o atributo foi confirmado pela Azure AD.

Não é possível ver os atributos de sombra usando o portal Azure ou com powerShell. Mas compreender o conceito ajuda-o a resolver certos cenários onde o atributo tem valores diferentes no local e na nuvem.

Para entender melhor o comportamento, olhe para este exemplo de Fabrikam:  
![Domínios](./media/how-to-connect-syncservice-shadow-attributes/domains.png)  
Eles têm vários sufixos UPN no seu Diretório Ativo no local, mas eles só verificaram um.

### <a name="userprincipalname"></a>userPrincipalName
Um utilizador tem os seguintes valores de atributo num domínio não verificado:

| Atributo | Valor |
| --- | --- |
| no local userPrincipalName | lee.sperry@fabrikam.com |
| Nome principal do shadowUserPrincipada da AD Azure | lee.sperry@fabrikam.com |
| Nome principal do utilizador azure AD | lee.sperry@fabrikam.onmicrosoft.com |

O atributo do userPrincipalName é o valor que se vê ao utilizar o PowerShell.

Uma vez que o valor real do atributo no local é armazenado no Azure AD, quando verificar o domínio fabrikam.com, o Azure AD atualiza o atributo do userPrincipalName com o valor do shadowUserPrincipalName. Não é necessário sincronizar quaisquer alterações do Azure AD Connect para que estes valores sejam atualizados.

### <a name="proxyaddresses"></a>proxyAddresses
O mesmo processo para apenas incluir domínios verificados também ocorre para proxyAddresses, mas com alguma lógica adicional. O cheque para domínios verificados só acontece para os utilizadores de caixas de correio. Um utilizador ou contacto ativado por correio representa um utilizador noutra organização do Exchange e pode adicionar quaisquer valores em proxyAddresss a estes objetos.

Para um utilizador de caixa de correio, quer no local quer no Exchange Online, apenas aparecem valores para domínios verificados. Pode parecer assim:

| Atributo | Valor |
| --- | --- |
| no local proxyAddresss | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie.spencer@fabrikam.com</br>smtp:abbie@fabrikamonline.com |
| Troca de endereços de procuração online | SMTP:abbie.spencer@fabrikamonline.com</br>smtp:abbie@fabrikamonline.com</br>SIP:abbie.spencer@fabrikamonline.com |

Neste caso, **a fabrikam.com\@smtp:abbie.spencer** foi removida, uma vez que esse domínio não foi verificado. Mas a Exchange também acrescentou **fabrikamonline.com SIP:abbie.spencer\@**. Fabrikam não usou lync/Skype no local, mas Azure AD e Exchange Online preparam-se para isso.

Esta lógica para proxyAddresses é referida como **ProxyCalc**. ProxyCalc é invocado com todas as alterações num utilizador quando:

- Foi atribuído ao utilizador um plano de serviço que inclui o Exchange Online mesmo que o utilizador não tenha sido licenciado para a Troca. Por exemplo, se o utilizador for atribuído ao Office E3 SKU, mas apenas foi atribuído o SharePoint Online. Isto é verdade mesmo que a sua caixa de correio ainda esteja no local.
- O atributo msExchRecipientTypeDetails tem um valor.
- Efetua uma alteração para proxyAddresss ou userPrincipalName.

ProxyCalc pode demorar algum tempo a processar uma mudança num utilizador e não é sincronizado com o processo de exportação azure AD Connect.

> [!NOTE]
> A lógica ProxyCalc tem alguns comportamentos adicionais para cenários avançados não documentados neste tópico. Este tópico é fornecido para que compreenda o comportamento e não documente toda a lógica interna.

### <a name="quarantined-attribute-values"></a>Valores de atributo sem quarentena
Os atributos sombra também são usados quando existem valores de atributo duplicado. Para mais informações, consulte a [resiliência do atributo duplicado.](how-to-connect-syncservice-duplicate-attribute-resiliency.md)

## <a name="see-also"></a>Consulte também
* [Sincronização azure AD Connect](how-to-connect-sync-whatis.md)
* [Integrando as suas identidades no local com o Diretório Ativo Azure.](whatis-hybrid-identity.md)
