---
title: 'Azure AD Connect sync: Understanding Users, Groups e Contacts Microsoft Docs'
description: Explica os utilizadores, grupos e contactos na sincronização Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 8d204647-213a-4519-bd62-49563c421602
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e015f7937db6788aa4473a8a04434121299901e9
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861787"
---
# <a name="azure-ad-connect-sync-understanding-users-groups-and-contacts"></a>Azure AD Connect sync: Understanding Users, Groups e Contacts
Existem várias razões diferentes para que você tenha várias florestas de Diretório Ativo e existem várias topologias de implantação diferentes. Os modelos comuns incluem uma implantação de recursos de conta e florestas de sincronização gal após uma fusão & aquisição. Mas mesmo que existam modelos puros, os modelos híbridos também são comuns. A configuração padrão no Azure AD Connect não assume nenhum modelo em particular, mas dependendo da forma como a correspondência do utilizador foi selecionada no guia de instalação, podem ser observados diferentes comportamentos.

Neste tópico, vamos analisar como a configuração padrão se comporta em certas topologias. Vamos analisar a configuração e o Editor de Regras de Sincronização pode ser usado para olhar para a configuração.

Existem algumas regras gerais que a configuração assume:
* Independentemente da ordem que importamos da fonte Ative Directies, o resultado final deve ser sempre o mesmo.
* Uma conta ativa irá sempre contribuir com informações de inscrição, incluindo **o UserPrincipalName** e **a sourceAnchor**.
* Uma conta desativada contribuirá com o Nome e fonte DoMe e fonteAnchor, a menos que seja uma caixa de correio ligada, se não houver uma conta ativa a ser encontrada.
* Uma conta com uma caixa de correio ligada nunca será usada para o utilizadorPrincipalName e sourceAnchor. Presume-se que uma conta ativa será encontrada mais tarde.
* Um objeto de contacto pode ser a provisionado à Azure AD como contacto ou como utilizador. Não se sabe até que todas as florestas de Diretório Ativo tenham sido processadas.

## <a name="groups"></a>Grupos
Pontos importantes a ter em conta quando sincronizar grupos do Ative Directory para Azure AD:

* O Azure AD Connect exclui grupos de segurança incorporados da sincronização do diretório.

* O Azure AD Connect não suporta a sincronização [de membros do Grupo Primário](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771489(v=ws.11)) para a Azure AD.

* O Azure AD Connect não suporta [associações](/Exchange/recipients/dynamic-distribution-groups/dynamic-distribution-groups) sincronizadas do Dynamic Distribution Group ao Azure AD.

* Para sincronizar um grupo de Diretório Ativo para Azure AD como um grupo habilitado por correio:

    * Se o atributo *proxyAddress* do grupo estiver vazio, o seu atributo *de correio* deve ter um valor

    * Se o atributo *proxyAddress* do grupo não estiver vazio, deve conter pelo menos um valor de endereço de procuração SMTP. Eis alguns exemplos:
    
      * Um grupo de Diretório Ativo cujo atributo ProxyAddress tem valor *{"X500:/0=contoso.com/ou=users/cn=testgroup"}* não será ativado por correio em Azure AD. Não tem endereço SMTP.
      
      * Um grupo de Diretório Ativo cujo atributo ProxyAddress tem valores *{"X500:/0=contoso.com/ou=users/cn=testgroup","SMTP:johndoe \@ contoso.com"}* será ativado por correio em Azure AD.
      
      * Um grupo de Diretório Ativo cujo atributo ProxyAddress tem valores *{"X500:/0=contoso.com/ou=users/cn=testgroup", "smtp:johndoe \@ contoso.com"}* também será ativado por correio em Azure AD.

## <a name="contacts"></a>Contactos
Ter contactos que representem um utilizador numa floresta diferente é comum após uma fusão & aquisição em que uma solução GALSync está a fazer a ponte entre duas ou mais florestas de intercâmbio. O objeto de contacto está sempre a unir-se do espaço do conector ao metaverso utilizando o atributo de correio. Se já existir um objeto de contacto ou objeto de utilizador com o mesmo endereço de correio, os objetos são unidos. Isto está configurado na regra **In a partir da AD – Contact Join**. Existe também uma regra chamada **In from AD – Contact Common** com um fluxo de atributos para o atributo metaverso **fonteObjectType** com o **contacto** constante . Esta regra tem uma precedência muito baixa, pelo que se algum objeto de utilizador for associado ao mesmo objeto metaverso, então a regra **In from AD – User Common** contribuirá com o valor Do Utilizador para este atributo. Com esta regra, este atributo terá o valor Contacto se nenhum utilizador tiver sido associado e o valor Utilizador se pelo menos um utilizador tiver sido encontrado.

Para a provisionação de um objeto a Azure AD, a regra de saída **out to AAD – Contact Join** criará um objeto de contacto se a fonte de atributo metaversoObjectType estiver definida como **Contacto**. **sourceObjectType** Se este atributo for definido para **Utilizador,** então a regra **out to AAD – User Join** criará um objeto de utilizador.
É possível que um objeto seja promovido de Contacto para Utilizador quando mais diretórios ativos de origem são importados e sincronizados.

Por exemplo, numa topologia GALSync encontraremos objetos de contacto para todos na segunda floresta quando importamos a primeira floresta. Isto irá encenar novos objetos de contacto no Conector AAD. Quando mais tarde importarmos e sincronizarmos a segunda floresta, encontraremos os verdadeiros utilizadores e juntaremos-os aos objetos metaversos existentes. Em seguida, eliminaremos o objeto de contacto em AAD e criaremos um novo objeto de utilizador.

Se tiver uma topologia onde os utilizadores estejam representados como contactos, certifique-se de que seleciona para combinar os utilizadores no atributo de correio no guia de instalação. Se selecionar outra opção, terá uma configuração dependente de encomendas. Os objetos de contacto juntar-se-ão sempre no atributo de correio, mas os objetos do utilizador só se juntarão ao atributo de correio se esta opção tiver sido selecionada no guia de instalação. Pode então acabar com dois objetos diferentes no metaverso com o mesmo atributo de correio se o objeto de contacto for importado antes do objeto do utilizador. Durante a exportação para Azure AD, será lançado um erro. Este comportamento é por design e indicaria dados maus ou que a topologia não foi corretamente identificada durante a instalação.

## <a name="disabled-accounts"></a>Contas desativadas
As contas desativadas são sincronizadas também para a Azure AD. As contas com deficiência são comuns para representar recursos em Exchange, por exemplo salas de conferências. A exceção são os utilizadores com uma caixa de correio ligada; como mencionado anteriormente, estes nunca irão prestar uma conta à Azure AD.

O pressuposto é que se uma conta de utilizador desativada for encontrada, então não encontraremos outra conta ativa mais tarde e o objeto é a provisionado para Azure AD com o nome de utilizadorPrincipalName e fonteAnchor encontrados. No caso de outra conta ativa se juntar ao mesmo objeto metaverso, o seu nome de utilizadorPrincipalName e sourceAnchor será utilizado.

## <a name="changing-sourceanchor"></a>Mudança de fonteAnchor
Quando um objeto tiver sido exportado para Azure AD, então não é permitido mudar mais a fonteAnchor. Quando o objeto foi exportado, o atributo metaverso **cloudSourceAnchor** é definido com o valor **de origemAnchor** aceite pela Azure AD. Se **o sourceAnchor** for alterado e não corresponder **à cloudSourceAnchor**, a regra **out to AAD – User Join** lançará a fonte de erro O atributo **Desíopio mudou**. Neste caso, a configuração ou dados devem ser corrigidos de modo a que a mesma fonteAnchor esteja novamente presente no metaverso antes que o objeto possa ser novamente sincronizado.

## <a name="additional-resources"></a>Recursos Adicionais
* [Azure AD Connect Sync: Personalizar opções de sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
