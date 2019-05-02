---
title: 'Azure AD Connect: Se já tiver o AD do Azure | Documentos da Microsoft'
description: Este tópico descreve como utilizar o Connect quando tiver um inquilino do Azure AD existente.
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
ms.openlocfilehash: 1495c14ae4c588661452aa3696019da00be47548
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64571375"
---
# <a name="azure-ad-connect-when-you-have-an-existent-tenant"></a>Azure AD Connect: Quando tiver um inquilino existente
A maioria dos tópicos para saber como utilizar o Azure AD Connect parte do princípio de começar com um Azure novo inquilino do AD e o que há nenhum usuário ou outros objetos. Mas se tiver iniciado com um inquilino do Azure AD, preenchido este com utilizadores e outros objetos e agora pretende utilizar Connect, em seguida, este tópico é para si.

## <a name="the-basics"></a>Noções básicas
Um objeto no Azure AD ou é controlado na cloud (Azure AD) ou no local. Para um único objeto, não é possível gerir alguns atributos no local e alguns outros atributos no Azure AD. Cada objeto tem um sinalizador que indica em que o objeto é gerenciado.

Pode gerir alguns utilizadores no local e outros na cloud. Um cenário comum para esta configuração é uma organização com uma combinação de funções de trabalho de gestão de contas e funções de trabalho de vendas. Os operadores de contabilidade tem uma local conta AD, mas os operadores de vendas não o fizer, têm uma conta no Azure AD. Iria gerir alguns utilizadores no local e alguns no Azure AD.

Se começou a gerir os utilizadores no Azure AD que também estejam no AD no local e posteriormente, pretender utilizar o Connect, haverá algumas preocupações adicionais que precisa considerar.

## <a name="sync-with-existing-users-in-azure-ad"></a>Sincronizar com os utilizadores existentes no Azure AD
Quando instalar o Azure AD Connect e iniciá-la, o serviço de sincronização do Azure AD (no Azure AD) faz uma verificação em cada novo objeto e tentar localizar um objeto existente para corresponder. Existem três atributos utilizados para executar este processo: **userPrincipalName**, **proxyAddresses**, e **sourceAnchor**/**immutableID** . Uma correspondência no **userPrincipalName** e **proxyAddresses** é conhecido como um **correspondência de forma recuperável**. Uma correspondência no **sourceAnchor** é conhecido como **correspondência de disco rígida**. Para o **proxyAddresses** apenas o valor com o atributo **SMTP:**, que é o endereço de e-mail principal, é utilizado para a avaliação.

A correspondência é avaliada apenas para novos objetos provenientes do Connect. Se alterar um objeto existente para que ele é correspondência de qualquer um destes atributos, em seguida, verá um erro em vez disso.

Se o Azure AD encontrar um objeto em que os valores de atributos são os mesmos para um objeto provenientes do Connect e que já está presente no Azure AD, em seguida, o objeto no Azure AD for retirado por Connect. O objeto anteriormente gerida na cloud é sinalizado como no local geridas. Todos os atributos no Azure AD com um valor em locais AD são substituídos com o valor no local. A exceção é quando um atributo tem um **nulo** valor no local. Neste caso, o valor do Azure AD permanece, mas ainda só pode alterá-lo no local para outro local.

> [!WARNING]
> Uma vez que todos os atributos no Azure AD vai ser substituída pelo valor no local, certificar-se de que tem boa dados no local. Por exemplo, se só tiver gerido endereço de e-mail do Office 365 e não são mantidas atualizadas no locais AD DS, em seguida, perde os valores existentes no Azure AD/Office 365 não está presente no AD DS.

> [!IMPORTANT]
> Se usar a sincronização de palavra-passe, que é sempre utilizada pelas definições rápidas, em seguida, a palavra-passe no Azure AD será substituída com a palavra-passe no local AD. Se os utilizadores são usados para gerenciar senhas diferentes, em seguida, terá da informá-los de que devem utilizar a palavra-passe no local quando tiver instalado o Connect.

A secção anterior e o aviso devem ser considerados em seus planos. Se fez muitas alterações no Azure AD não serão refletido na locais AD DS, tem de planear a forma preencher o AD DS com valores atualizados antes de poder sincronizar seus objetos com o Azure AD Connect.

Se correspondido seus objetos com uma correspondência de forma recuperável, em seguida, o **sourceAnchor** é adicionada ao objeto no Azure AD para que uma correspondência de disco rígida pode ser utilizada mais tarde.

>[!IMPORTANT]
> A Microsoft recomenda vivamente em relação a sincronização de contas no local com as contas administrativas já existentes no Azure Active Directory.

### <a name="hard-match-vs-soft-match"></a>Disco rígido-match vs correspondência de forma recuperável
Para uma nova instalação do Connect, não existe nenhum prática a diferença entre uma configuração soft - e um disco rígido-match. A diferença está numa situação de recuperação após desastre. Se tiver perdido o seu servidor com o Azure AD Connect, pode reinstalar uma nova instância sem perder nenhum dado. Um objeto com um sourceAnchor é enviado para o Connect durante a instalação inicial. A correspondência, em seguida, pode ser avaliada pelo cliente (Azure AD Connect), que é muito mais rápido do que os mesmos no Azure AD. Uma correspondência de disco rígida é avaliada pelo Connect e pelo Azure AD. Uma correspondência de forma recuperável é avaliada apenas pelo Azure AD.

### <a name="other-objects-than-users"></a>Outros objetos que os utilizadores
Para grupos com capacidade de correio e contatos, pode soft-correspondência com base em proxyAddresses. Disco rígido-match não é aplicável, uma vez que só pode atualizar o sourceAnchor/immutableID (com o PowerShell) os utilizadores apenas. Para os grupos que não são habilitados para email, atualmente não há suporte para a correspondência de forma recuperável ou de disco rígido.

### <a name="admin-role-considerations"></a>Considerações de função de administrador
Para impedir que utilizadores não fidedignos no local de correspondência com um utilizador na cloud com qualquer função de administrador, o Azure AD Connect não serão iguais aos objetos de utilizador no local com objetos que têm uma função de administrador. Trata-se por predefinição. Para resolver esse comportamento pode fazer o seguinte:

1.  Remover as funções de diretório do objeto de utilizador apenas na cloud
2.  Acionar uma sincronização
3.  Opcionalmente, adicione as funções de diretório para o objeto de utilizador na cloud, assim que a correspondência ocorreu.



## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Criar um novo do Active Directory no local a partir de dados no Azure AD
Alguns clientes começam com uma solução apenas na cloud com o Azure AD e não têm uma local do AD. Mais tarde pretende consumir recursos no local e quer criar uma local do AD com base nos dados do Azure AD. O Azure AD Connect não pode ajudá-lo para este cenário. Não cria os utilizadores no local e não tem qualquer capacidade de definir a palavra-passe no local para os mesmos que no Azure AD.

Se o único motivo por que planeia adicionar AD no local é oferecer suporte a LOBs (aplicações de linha de negócio), em seguida, talvez deve considerar usar [serviços de domínio do Azure AD](../../active-directory-domain-services/index.yml) em vez disso.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
