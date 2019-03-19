---
title: 'Sincronização do Azure AD Connect: Entender utilizadores, grupos e contactos | Documentos da Microsoft'
description: Explica os utilizadores, grupos e contactos na sincronização do Azure AD Connect.
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
ms.openlocfilehash: 661747754369c17ca98ae69d477e04124b6a2942
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993377"
---
# <a name="azure-ad-connect-sync-understanding-users-groups-and-contacts"></a>Sincronização do Azure AD Connect: Entender utilizadores, grupos e contactos
Existem vários motivos diferentes por que teria de várias florestas do Active Directory e existem várias topologias de implementação diferentes. Modelos comuns incluem uma implementação de recurso de conta e GAL sync'ed florestas depois de uma fusão e aquisição. Mas, mesmo se existirem modelos puros, modelos híbridos são comuns também. A configuração predefinida do Azure AD Connect não assume qualquer modelo específico, mas dependendo de como a correspondência de utilizador tiver sido selecionada no guia de instalação, podem ser observados comportamentos diferentes.

Neste tópico, iremos percorrer como a configuração predefinida se comporta em algumas topologias. Iremos percorrer a configuração e o Editor de regras de sincronização pode ser usado para examinar a configuração.

Existem algumas regras gerais que pressupõe a configuração:
* Independentemente de qual ordem, podemos importar da origem de diretórios Active Directory, o resultado final deve ser sempre o mesmo.
* Uma conta ativa sempre contribui com informações de início de sessão, incluindo **userPrincipalName** e **sourceAnchor**.
* Uma conta desabilitada irá contribuir userPrincipalName e sourceAnchor, a menos que seja uma caixa de correio vinculada, se não existir nenhuma conta Active Directory a serem encontradas.
* Uma conta com uma caixa de correio vinculada nunca será utilizada para userPrincipalName e sourceAnchor. Pressupõe-se que uma conta ativa será encontrada mais tarde.
* Um objeto de contato pode ser aprovisionado com o Azure AD como um contacto ou como um utilizador. Não conhece verdadeiramente a até que todas as florestas do Active Directory de origem foram processadas.

## <a name="groups"></a>Grupos
Pontos importantes a ter em consideração durante a sincronização de grupos do Active Directory para o Azure AD:

* O Azure AD Connect exclui grupos de segurança incorporadas da sincronização de diretórios.

* O Azure AD Connect não suporta a sincronização [associações de grupo primário](https://technet.microsoft.com/library/cc771489(v=ws.11).aspx) para o Azure AD.

* O Azure AD Connect não suporta a sincronização [associações de grupo dinâmico de distribuição](https://technet.microsoft.com/library/bb123722(v=exchg.160).aspx) para o Azure AD.

* Para sincronizar um grupo do Active Directory para o Azure AD como um grupo com capacidade de correio:

    * Se o grupo *proxyAddress* está vazio, o atributo seu *correio* atributo tem de ter um valor

    * Se o grupo *proxyAddress* atributo não está vazia, tem de conter pelo menos um valor de endereço de proxy SMTP. Eis alguns exemplos:
    
      * Um grupo do Active Directory tem o valor cujo atributo proxyAddress *{"X500:/0=contoso.com/ou=users/cn=testgroup"}* não serão habilitados para email no Azure AD. Não tem um endereço SMTP.
      
      * Um grupo do Active Directory, cujo atributo proxyAddress tem valores *{"X500:/0=contoso.com/ou=users/cn=testgroup","SMTP:johndoe\@contoso.com"}* será a capacidade de correio no Azure AD.
      
      * Um grupo do Active Directory, cujo atributo proxyAddress tem valores *{"X500:/0=contoso.com/ou=users/cn=testgroup", "smtp:johndoe\@contoso.com"}* também serão habilitados para email no Azure AD.

## <a name="contacts"></a>Contactos
Ter contactos que representa um utilizador numa floresta diferente é comum depois de uma fusão e aquisição em que uma solução de GALSync é a ponte duas ou mais florestas do Exchange. O objeto de contato estiver ingressando em sempre de espaço conector do metaverso usando o atributo de correio. Se já existir um objeto de contato ou objeto de utilizador com o mesmo endereço de correio, os objetos são Unidos. Este é configurado na regra **do AD – associar contacto**. Também existe uma regra com o nome **do AD – comuns de contato** com um fluxo de atributos para o atributo do metaverso **sourceObjectType** com a constante **contacto**. Esta regra tem precedência muito baixa por isso, se qualquer objeto de utilizador está associado ao mesmo objeto de metaverso, em seguida, a regra **do AD – utilizador comuns** contribui com o valor de utilizador para este atributo. Com esta regra, este atributo terá o valor contacto se nenhum utilizador tiver sido associado e o valor de utilizador se, pelo menos, um usuário foi encontrado.

Para o aprovisionamento de um objeto para o Azure AD, a regra de saída **expansão para AAD – entre em contato com associação** irá criar um objeto de contato, se o atributo do metaverso **sourceObjectType** está definida como **entreemcontatocom**. Se este atributo estiver definido como **usuário**, em seguida, a regra **expansão para AAD – associação do utilizador** criará um objeto de usuário em vez disso.
É possível que um objeto é promovido de contacto para o utilizador quando mais diretórios Active Directory de origem são importados e sincronizados.

Por exemplo, numa topologia GALSync que descobriremos objetos de contato para todas as pessoas na segunda floresta quando importamos a primeira floresta. Isto irá testar novos objetos de contato no conector do AAD. Quando importar mais tarde e sincronizar a segunda floresta, iremos encontrar os usuários reais e associá-las para os objetos de metaverso existentes. Iremos, em seguida, elimine o objeto de contato no AAD e criar um novo objeto de utilizador em vez disso.

Se tiver uma topologia em que os utilizadores são representados como contatos, certifique-se de que selecionar para corresponder a utilizadores no atributo de correio no guia de instalação. Se selecionar outra opção, em seguida, terá uma configuração de dependente de ordem. Objetos de contato serão sempre Junte-se no atributo de correio, mas objetos de utilizador só serão associado no atributo de correio se esta opção foi selecionada no guia de instalação. Poderia, em seguida, terminar com dois objetos diferentes no metaverso com o mesmo atributo de correio se o objeto de contato tiver sido importado antes do objeto de utilizador. Durante a exportação para o Azure AD, será gerado um erro. Este comportamento é propositado e indicaria dados incorretos ou que a topologia não foi corretamente identificada durante a instalação.

## <a name="disabled-accounts"></a>Contas desativadas
Contas desabilitadas são sincronizadas, também, para o Azure AD. Contas desabilitadas são comuns para representar a recursos no Exchange, por exemplo as salas de conferência. A exceção são os utilizadores com uma caixa de correio ligada; como mencionado anteriormente, estes nunca irão aprovisionar uma conta para o Azure AD.

A suposição é que, se for encontrada uma conta de utilizador desativado, em seguida, não será possível encontrar outra conta do Active Directory mais tarde e o objeto é aprovisionado com o Azure AD com o userPrincipalName e sourceAnchor encontrado. No caso de outra conta do Active Directory irá aderir ao mesmo objeto de metaverso, em seguida, o userPrincipalName e sourceAnchor serão utilizados.

## <a name="changing-sourceanchor"></a>Alterar sourceAnchor
Quando um objeto tenha sido exportado para o Azure AD, em seguida, ele não tem permissão para alterar o sourceAnchor mais. Quando o objeto ter sido exportados o atributo do metaverso **cloudSourceAnchor** está definida com o **sourceAnchor** valor aceite pelo Azure AD. Se **sourceAnchor** é alterado e não corresponde ao **cloudSourceAnchor**, a regra **expansão para AAD – associar utilizador** lançará o erro **tem o atributo sourceAnchor alterado**. Neste caso, a configuração ou dados têm de ser corrigidos, para que o mesmo sourceAnchor está presente no metaverso novamente antes do objeto pode ser sincronizado novamente.

## <a name="additional-resources"></a>Recursos Adicionais
* [Sincronização do Azure AD Connect: Personalizando opções de sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)

