---
title: 'Azure AD Connect: Quando já tem Azure AD | Microsoft Docs'
description: Este tópico descreve como usar o Connect quando tem um inquilino AZure AD existente.
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
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68251270b6273f5a07391138e5c7210f1c46ba5a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420534"
---
# <a name="azure-ad-connect-when-you-have-an-existing-tenant"></a>Azure AD Connect: Quando tiver um inquilino existente
A maioria dos tópicos para como usar o Azure AD Connect assume que você começa com um novo inquilino AD AZure e que não há utilizadores ou outros objetos lá. Mas se você começou com um inquilino AZure AD, povoou-o com utilizadores e outros objetos, e agora quer usar Connect, então este tópico é para você.

## <a name="the-basics"></a>Noções básicas
Um objeto em Azure AD é dominado na nuvem (Azure AD) ou no local. Para um único objeto, não é possível gerir alguns atributos no local e outros atributos em AZure AD. Cada objeto tem uma bandeira indicando onde o objeto é gerido.

Pode gerir alguns utilizadores no local e outros na nuvem. Um cenário comum para esta configuração é uma organização com uma mistura de trabalhadores de contabilidade e trabalhadores de vendas. Os trabalhadores contabilísticos têm uma conta AD no local, mas os vendedores não têm, têm uma conta na Azure AD. Geriria alguns utilizadores no local e alguns em Azure AD.

Se começou a gerir utilizadores em Azure AD que também estão no local AD e mais tarde querem usar o Connect, então existem algumas preocupações adicionais que deve considerar.

## <a name="sync-with-existing-users-in-azure-ad"></a>Sincronizar com os utilizadores existentes no Azure AD
Quando instala o Azure AD Connect e começa a sincronizar, o serviço de sincronização AZure AD (em Azure AD) verifica cada novo objeto e tenta encontrar um objeto existente para combinar. Existem três atributos utilizados para este processo: **userPrincipalName,** **proxyAddresses** e **sourceAnchor** / **imuttableID**. Uma correspondência no **userPrincipalName** e **proxyAddresses** é conhecida como uma **combinação suave**. Uma correspondência na **fonteAnchor** é conhecida como **hard match**. Para o **proxyAddresses** atribuem apenas o valor com **SMTP:**, este é o endereço de e-mail primário, é usado para a avaliação.

A partida só é avaliada para novos objetos vindos do Connect. Se alterar um objeto existente, por isso corresponde a qualquer um destes atributos, então vê um erro.

Se a Azure AD encontrar um objeto onde os valores do atributo são os mesmos para um objeto proveniente do Connect e que já está presente no Azure AD, então o objeto em Azure AD é assumido pela Connect. O objeto anteriormente gerido pela nuvem é sinalizado como gerido no local. Todos os atributos em AD Azure com um valor em AD no local são substituídos com o valor no local.

> [!WARNING]
> Uma vez que todos os atributos em AD Azure serão substituídos pelo valor no local, certifique-se de que tem bons dados no local. Por exemplo, se apenas geriu o endereço de e-mail no Microsoft 365 e não o manteve atualizado em DS AD no local, então perde quaisquer valores em Azure AD/Microsoft 365 que não esteja presente em DS AD.

> [!IMPORTANT]
> Se utilizar a sincronização de palavra-passe, que é sempre utilizada por definições expressas, então a palavra-passe em Azure AD é substituída com a palavra-passe no AD no local. Se os seus utilizadores estiverem habituados a gerir diferentes palavras-passe, então tem de informá-los de que devem utilizar a palavra-passe no local quando tiver instalado o Connect.

A secção anterior e o aviso devem ser considerados no seu planeamento. Se fez muitas alterações no AD Azure não refletido em AD DS no local, então precisa planear como povoar O DS AD com os valores atualizados antes de sincronizar os seus objetos com Azure AD Connect.

Se combinar os seus objetos com um soft-match, então a **fonteAnchor** é adicionada ao objeto em Azure AD para que uma combinação dura possa ser usada mais tarde.

>[!IMPORTANT]
> A Microsoft recomenda veementemente contra a sincronização de contas no local com contas administrativas pré-existentes no Azure Ative Directory.

### <a name="hard-match-vs-soft-match"></a>Hard-match vs Soft-match
Para uma nova instalação do Connect, não há diferença prática entre um soft e um hard-match. A diferença está numa situação de recuperação de desastres. Se perdeu o seu servidor com o Azure AD Connect, pode reinstalar uma nova instância sem perder quaisquer dados. Um objeto com uma fonteAnchor é enviado para Connect durante a instalação inicial. A partida pode então ser avaliada pelo cliente (Azure AD Connect), que é muito mais rápido do que fazer o mesmo em Azure AD. Um jogo difícil é avaliado tanto pela Connect como pela Azure AD. Um jogo suave só é avaliado pela Azure AD.

### <a name="other-objects-than-users"></a>Outros objetos que não os utilizadores
Para grupos e contactos ativados por correio, pode combinar com base em proxyAddresses. O hard-match não é aplicável, uma vez que só é possível atualizar a fonteAnchor/imutávelID (usando o PowerShell) apenas nos Utilizadores. Para grupos que não estão habilitados por correio, não existe atualmente suporte para soft-match ou hard-match.

### <a name="admin-role-considerations"></a>Considerações de função de administração
Para evitar que os utilizadores não fidedignidades no local correspondam a um utilizador em nuvem que tenha qualquer papel de administração, o Azure AD Connect não corresponderá a objetos de utilizador no local com objetos que tenham uma função de administração. Isto é por defeito. Para contornar este comportamento pode fazer o seguinte:

1.  Remova as funções de diretório do objeto do utilizador apenas na nuvem.
2.  Se houve uma tentativa falhada de sincronização do utilizador, elimine duramente o objeto quarentena na nuvem.
3.  Desencadeie uma sincronização.
4.  Adicione opcionalmente as funções do diretório de volta ao objeto do utilizador na nuvem uma vez que a correspondência tenha ocorrido.



## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Criar um novo Ative Directory no local a partir de dados em Azure AD
Alguns clientes começam com uma solução apenas em nuvem com Azure AD e não têm um AD no local. Mais tarde, querem consumir recursos no local e querem construir um AD no local com base em dados AD Azure. O Azure AD Connect não pode ajudá-lo neste cenário. Não cria utilizadores no local e não tem qualquer capacidade de definir a palavra-passe no local da mesma forma que no Azure AD.

Se a única razão pela qual planeia adicionar AD no local é para suportar LOBs (aplicações Line-of-Business), então talvez deva considerar usar os serviços de [domínio AD AZure](../../active-directory-domain-services/index.yml) em vez disso.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
