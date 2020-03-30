---
title: 'Azure AD Connect: Quando já tem Azure AD / Microsoft Docs'
description: Este tópico descreve como usar o Connect quando se tem um inquilino Azure AD existente.
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
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3636b88b14cf7e76e4fb023434316e7ee31ded04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71336810"
---
# <a name="azure-ad-connect-when-you-have-an-existent-tenant"></a>Azure AD Connect: Quando se tem um inquilino existente
A maioria dos tópicos para como usar o Azure AD Connect pressupõe que você começa com um novo inquilino Azure AD e que não há utilizadores ou outros objetos lá. Mas se começou com um inquilino da AD Azure, povoou-o com utilizadores e outros objetos, e agora quer usar o Connect, então este tópico é para si.

## <a name="the-basics"></a>Noções básicas
Um objeto em Azure AD é dominado na nuvem (Azure AD) ou no local. Para um único objeto, não é possível gerir alguns atributos no local e outros atributos em Azure AD. Cada objeto tem uma bandeira indicando onde o objeto é gerido.

Pode gerir alguns utilizadores no local e outros na nuvem. Um cenário comum para esta configuração é uma organização com uma mistura de trabalhadores de contabilidade e trabalhadores de vendas. Os trabalhadores da contabilidade têm uma conta ad,mas os trabalhadores de vendas não têm, têm uma conta na AD Azure. Você iria gerir alguns utilizadores no local e alguns em Azure AD.

Se começou a gerir utilizadores em AD Azure que também estão no local ad ad e mais tarde quer usar o Connect, então existem algumas preocupações adicionais que deve considerar.

## <a name="sync-with-existing-users-in-azure-ad"></a>Sincronizar com os utilizadores existentes em Azure AD
Quando instala o Azure AD Connect e começa a sincronizar, o serviço de sincronização Azure AD (em Azure AD) faz uma verificação em cada objeto novo e tenta encontrar um objeto existente para combinar. Existem três atributos utilizados para este processo: **userPrincipalName**, **proxyAddresss**, e **sourceAnchor**/**imutableID**. Uma correspondência no **userPrincipalName** e **proxyAddresses** é conhecida como uma **correspondência suave**. Uma correspondência na **fonteAnchor** é conhecida como **hard match**. Para os **proxyAddresss** atribuem apenas o valor com **SMTP:**, que é o endereço de e-mail primário, é usado para a avaliação.

A correspondência só é avaliada para novos objetos provenientes do Connect. Se alterar um objeto existente para que esteja a corresponder a qualquer um destes atributos, então vê um erro.

Se a Azure AD encontrar um objeto onde os valores do atributo são os mesmos para um objeto vindo do Connect e que já está presente em Azure AD, então o objeto em Azure AD é assumido pela Connect. O objeto anteriormente gerido pela nuvem é sinalizado como gerido no local. Todos os atributos em Azure AD com um valor em ad-presiter são sobreescritos com o valor no local. A exceção é quando um atributo tem um valor **NULO** no local. Neste caso, o valor em Azure AD permanece, mas ainda só pode mudá-lo no local para outra coisa.

> [!WARNING]
> Uma vez que todos os atributos em Azure AD serão substituídos pelo valor no local, certifique-se de que tem bons dados no local. Por exemplo, se só tiver gerido o endereço de e-mail no Office 365 e não o tiver mantido atualizado no local AD DS, então perde quaisquer valores em Azure AD/Office 365 não presentes em DS AD.

> [!IMPORTANT]
> Se utilizar a sincronização de palavra-passe, que é sempre utilizada por definições expressas, a palavra-passe em Azure AD é substituída com a palavra-passe no anúncio no local. Se os seus utilizadores forem utilizados para gerir diferentes palavras-passe, então tem de os informar de que devem utilizar a palavra-passe no local quando tiver instalado o Connect.

A secção e o aviso anteriordevem ser considerados no seu planeamento. Se fez muitas alterações em AD Azure não refletidas no AD DS no local, então precisa de planear como povoar AD DS com os valores atualizados antes de sincronizar os seus objetos com o Azure AD Connect.

Se combinar os seus objetos com um soft-match, então a **fonteAnchor** é adicionada ao objeto em Azure AD para que uma correspondência difícil possa ser usada mais tarde.

>[!IMPORTANT]
> A Microsoft recomenda veementemente não sincronizar contas no local com contas administrativas pré-existentes no Diretório Ativo do Azure.

### <a name="hard-match-vs-soft-match"></a>Hard-match vs Soft-match
Para uma nova instalação do Connect, não existe uma diferença prática entre um soft e um hard-match. A diferença está numa situação de recuperação de desastres. Se perdeu o servidor com o Azure AD Connect, pode reinstalar uma nova instância sem perder qualquer dado. Um objeto com uma fonte Anchor é enviado para Connect durante a instalação inicial. A partida pode então ser avaliada pelo cliente (Azure AD Connect), que é muito mais rápido do que fazer o mesmo em Azure AD. Uma partida difícil é avaliada tanto pela Connect como pela Azure AD. Um jogo suave só é avaliado pela Azure AD.

### <a name="other-objects-than-users"></a>Outros objetos que não os utilizadores
Para grupos e contactos ativados por correio, pode combinar suavemente com base em proxyAddresses. O hard-match não é aplicável, uma vez que só pode atualizar a fonteAnchor/imutávelID (utilizando powerShell) apenas nos Utilizadores. Para grupos que não estão ativados por correio, não existe atualmente suporte para soft-match ou hard-match.

### <a name="admin-role-considerations"></a>Considerações de papel de administrador
Para evitar que os utilizadores não confiáveis no local se coincidam com um utilizador de nuvem que tenha qualquer função de administrador, o Azure AD Connect não combinará com os objetos de utilizador no local com objetos que tenham uma função de administrador. Isto é por defeito. Para contornar este comportamento pode fazer o seguinte:

1.  Retire as funções de diretório do objeto de utilizador apenas em nuvem.
2.  Se houve uma tentativa falhada de sincronização do utilizador, elimine duramente o objeto de quarentena na nuvem.
3.  Desencadear uma sincronização.
4.  Adicione opcionalmente as funções de diretório de volta ao objeto do utilizador na nuvem uma vez que a correspondência tenha ocorrido.



## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Criar um novo Diretório Ativo no local a partir de dados em Azure AD
Alguns clientes começam com uma solução só para a nuvem com a Azure AD e não têm um AD no local. Mais tarde, querem consumir recursos no local e querem construir um Anúncio no local com base em dados da AD Azure. O Azure AD Connect não pode ajudá-lo neste cenário. Não cria utilizadores no local e não tem qualquer capacidade de definir a palavra-passe no local para o mesmo que em Azure AD.

Se a única razão pela qual planeia adicionar a AD no local é para suportar LOBs (aplicações Line-of-Business), então talvez deva considerar usar serviços de [domínio Azure AD](../../active-directory-domain-services/index.yml) em vez disso.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
