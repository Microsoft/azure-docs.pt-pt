---
title: 'Sincronização Azure AD Connect: Compreender utilizadores, grupos e contactos Microsoft Docs'
description: Explica utilizadores, grupos e contactos na sincronização Azure AD Connect.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "60245482"
---
# <a name="azure-ad-connect-sync-understanding-users-groups-and-contacts"></a>Sincronização azure AD Connect: Compreender utilizadores, grupos e contactos
Existem várias razões diferentes para ter várias florestas de Diretório Ativo e existem várias topoologias de implantação diferentes. Os modelos comuns incluem uma implantação de recursos de conta e florestas sincronizadas de GAL após uma fusão & aquisição. Mas mesmo que existam modelos puros, os modelos híbridos também são comuns. A configuração predefinida no sincronizado Azure AD Connect não assume nenhum modelo específico, mas dependendo da forma como a correspondência do utilizador foi selecionada no guia de instalação, podem ser observados diferentes comportamentos.

Neste tópico, vamos analisar como a configuração padrão se comporta em determinadas topoologias. Vamos passar pela configuração e o Editor de Regras de Sincronização pode ser usado para olhar para a configuração.

Existem algumas regras gerais que a configuração assume:
* Independentemente da ordem que importamos da fonte Diretórios Ativos, o resultado final deve ser sempre o mesmo.
* Uma conta ativa irá sempre contribuir com informações de inscrição, incluindo **o userPrincipalName** e **sourceAnchor**.
* Uma conta desativada contribuirá com o userPrincipalName e fonteAnchor, a menos que seja uma caixa de correio ligada, se não houver uma conta ativa a ser encontrada.
* Uma conta com uma caixa de correio ligada nunca será utilizada para o userPrincipalName e sourceAnchor. Presume-se que uma conta ativa será encontrada mais tarde.
* Um objeto de contacto pode ser provisionado à AD Azure como contacto ou como utilizador. Não se sabe até que todas as florestas de Diretório Ativo tenham sido processadas.

## <a name="groups"></a>Grupos
Pontos importantes a ter em conta ao sincronizar grupos do Ative Directory para o Azure AD:

* O Azure AD Connect exclui grupos de segurança incorporados da sincronização do diretório.

* O Azure AD Connect não suporta sincronizar [as adesões](https://technet.microsoft.com/library/cc771489(v=ws.11).aspx) do Grupo Primário à Azure AD.

* O Azure AD Connect não suporta a [subscrição](https://technet.microsoft.com/library/bb123722(v=exchg.160).aspx) sincronizada do Grupo de Distribuição Dinâmica ao Azure AD.

* Sincronizar um grupo de Diretório Ativo para a AD Azure como um grupo ativado por correio:

    * Se o atributo *proxyAddress* do grupo estiver vazio, o seu atributo de *correio* deve ter um valor

    * Se o atributo *proxyAddress* do grupo não estiver vazio, deve conter pelo menos um valor de endereço de procuração SMTP. Eis alguns exemplos:
    
      * Um grupo de Diretório Ativo cujo atributo proxyAddress tem valor *{"X500:/0=contoso.com/ou=users/cn=testgroup"}* não será ativado por correio em Azure AD. Não tem um endereço SMTP.
      
      * Um grupo de Diretório Ativo cujo atributo proxyAddress tem valores *{"X500:/0=contoso.com/ou=users/cn=testgroup",\@"SMTP:johndoe contoso.com"}* será ativado por correio em Azure AD.
      
      * Um grupo de Diretório Ativo cujo atributo proxyAddress tem valores *{"X500:/0=contoso.com/ou=users/cn=testgroup",\@"smtp:johndoe contoso.com"}* também será ativado por correio em Azure AD.

## <a name="contacts"></a>Contactos
Ter contactos representando um utilizador numa floresta diferente é comum após uma fusão & aquisição em que uma solução GALSync está a fazer a ponte entre duas ou mais florestas de intercâmbio. O objeto de contacto está sempre a unir-se do espaço do conector ao metaverso utilizando o atributo do correio. Se já existir um objeto de contacto ou objeto de utilizador com o mesmo endereço de correio, os objetos são unidos. Isto está configurado na regra **In from AD – Contact Join**. Existe também uma regra denominada **In from AD – Contact Common** com um fluxo de atributo para a fonte metaverso do atributoObjectType com o **contacto**constante . **sourceObjectType** Esta regra tem uma precedência muito baixa, pelo que se qualquer objeto de utilizador estiver unido ao mesmo objeto metaverso, então a regra **In from AD – User Common** contribuirá com o valor utilizador para este atributo. Com esta regra, este atributo terá o valor Contacto se nenhum utilizador tiver sido unido e o valor Utilizador se pelo menos um utilizador tiver sido encontrado.

Para fornecer um objeto ao Azure AD, a regra de saída **Para AAD – Contact Join** criará um objeto de contacto se a fonte metaverso do atributoObjectType estiver definida para o **sourceObjectType** **contacto**. Se este atributo for definido para **o Utilizador**, então a regra out **para AAD – User Join** criará um objeto de utilizador.
É possível que um objeto seja promovido de Contacto para Utilizador quando mais fonte Os Diretórios Ativos são importados e sincronizados.

Por exemplo, numa topologia GALSync encontraremos objetos de contacto para todos na segunda floresta quando importarmos a primeira floresta. Isto irá encenar novos objetos de contacto no Conector AAD. Quando mais tarde importarmos e sincronizarmos a segunda floresta, encontraremos os verdadeiros utilizadores e juntar-nos-emos aos objetos metaversos existentes. Em seguida, eliminaremos o objeto de contacto em AAD e criaremos um novo objeto de utilizador.

Se tiver uma topologia onde os utilizadores estão representados como contactos, certifique-se de que se leciona para combinar os utilizadores no atributo de correio no guia de instalação. Se selecionar outra opção, terá uma configuração dependente da ordem. Os objetos de contacto juntar-se-ão sempre ao atributo do correio, mas os objetos do utilizador só se juntarão ao atributo do correio se esta opção for selecionada no guia de instalação. Em seguida, pode acabar com dois objetos diferentes no metaverso com o mesmo atributo de correio se o objeto de contacto for importado antes do objeto do utilizador. Durante a exportação para a AD Azure, será lançado um erro. Este comportamento é por design e indica dados maus ou que a topologia não foi corretamente identificada durante a instalação.

## <a name="disabled-accounts"></a>Contas com deficiência
As contas com deficiência também são sincronizadas para a AD Azure. As contas com deficiência são comuns para representar recursos em Exchange, por exemplo, salas de conferências. A exceção são os utilizadores com uma caixa de correio ligada; como mencionado anteriormente, estes nunca fornecerão uma conta à Azure AD.

O pressuposto é que, se for encontrada uma conta de utilizador desativada, não encontraremos outra conta ativa mais tarde e o objeto for fornecido ao Azure AD com o userPrincipalName e fonteAnchor encontrados. No caso de outra conta ativa se juntar ao mesmo objeto metaverso, o seu userPrincipalName e sourceAnchor serão utilizados.

## <a name="changing-sourceanchor"></a>Mudança de fonteÂncora
Quando um objeto foi exportado para a AD Azure, então já não é permitido alterar a fonteAnchor. Quando o objeto tiver sido exportado, o atributo metaverso **cloudSourceAnchor** é definido com o valor **sourceAnchor** aceite pela Azure AD. Se **a fonteAnchor** for alterada e não corresponder ao **cloudSourceAnchor**, a regra **out to AAD – User Join** lançará o atributo de origem de erro Que o Anchor **alterou**. Neste caso, a configuração ou os dados devem ser corrigidos para que a mesma fonteAnchor esteja novamente presente no metaverso antes que o objeto possa ser sincronizado novamente.

## <a name="additional-resources"></a>Recursos Adicionais
* [Azure AD Connect Sync: Opções de sincronização personalizadas](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)

