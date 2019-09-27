---
title: 'Azure AD Connect: Quando você já tiver o Azure AD | Microsoft Docs'
description: Este tópico descreve como usar o Connect quando você tem um locatário existente do Azure AD.
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
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71336810"
---
# <a name="azure-ad-connect-when-you-have-an-existent-tenant"></a>Azure AD Connect: Quando você tem um locatário existente
A maioria dos tópicos sobre como usar o Azure AD Connect pressupõe que você comece com um novo locatário do Azure AD e que não haja nenhum usuário ou outro objeto lá. Mas se você tiver começado com um locatário do Azure AD, populado com usuários e outros objetos e agora quiser usar o Connect, este tópico será para você.

## <a name="the-basics"></a>Noções básicas
Um objeto no Azure AD é baseado na nuvem (Azure AD) ou no local. Para um único objeto, você não pode gerenciar alguns atributos locais e alguns outros atributos no Azure AD. Cada objeto tem um sinalizador que indica onde o objeto é gerenciado.

Você pode gerenciar alguns usuários locais e outros na nuvem. Um cenário comum para essa configuração é uma organização com uma combinação de funcionários contábeis e operadores de vendas. Os trabalhos de contabilidade têm uma conta do AD local, mas os operadores de vendas não têm uma conta no Azure AD. Você gerenciaria alguns usuários no local e alguns no Azure AD.

Se você começou a gerenciar usuários no Azure AD que também estão no AD local e, mais tarde, deseja usar o Connect, há algumas preocupações adicionais que você precisa considerar.

## <a name="sync-with-existing-users-in-azure-ad"></a>Sincronizar com usuários existentes no Azure AD
Quando você instala o Azure AD Connect e inicia a sincronização, o serviço de sincronização do AD do Azure (no Azure AD) faz uma verificação em cada novo objeto e tenta localizar um objeto existente para fazer a correspondência. Há três atributos usados para este processo: **userPrincipalName**, **proxyAddresses**e **sourceAnchor**/**imutávelid**. Uma correspondência em **userPrincipalName** e **proxyAddresses** é conhecida como uma **correspondência flexível**. Uma correspondência em **sourceAnchor** é conhecida como **correspondência rígida**. Para o atributo **proxyAddresses** , somente o valor com **SMTP:** , que é o endereço de email principal, é usado para a avaliação.

A correspondência só é avaliada para novos objetos provenientes do Connect. Se você alterar um objeto existente para que ele corresponda a qualquer um desses atributos, você verá um erro em vez disso.

Se o Azure AD encontrar um objeto em que os valores de atributo são os mesmos para um objeto proveniente do Connect e que ele já está presente no Azure AD, o objeto no Azure AD será obtido pelo Connect. O objeto gerenciado anteriormente em nuvem é sinalizado como gerenciado no local. Todos os atributos no Azure AD com um valor no AD local são substituídos pelo valor local. A exceção é quando um atributo tem um valor **nulo** no local. Nesse caso, o valor no Azure AD permanece, mas você ainda pode alterá-lo localmente para outra coisa.

> [!WARNING]
> Como todos os atributos no Azure AD serão substituídos pelo valor local, verifique se você tem dados bons no local. Por exemplo, se você tiver apenas o endereço de email gerenciado no Office 365 e não o tiver mantido atualizado no local AD DS, você perderá quaisquer valores no Azure AD/Office 365 que não estejam presentes no AD DS.

> [!IMPORTANT]
> Se você usar a sincronização de senha, que é sempre usada pelas configurações expressas, a senha no Azure AD será substituída pela senha no AD local. Se os usuários forem usados para gerenciar senhas diferentes, você precisará informá-los de que eles devem usar a senha local quando você tiver instalado o Connect.

A seção e o aviso anteriores devem ser considerados no planejamento. Se você tiver feito muitas alterações no Azure AD não refletidas no AD DS local, será necessário planejar como preencher AD DS com os valores atualizados antes de sincronizar os objetos com Azure AD Connect.

Se você coincidiu com os objetos com uma correspondência flexível, o **sourceAnchor** é adicionado ao objeto no Azure ad para que uma correspondência rígida possa ser usada posteriormente.

>[!IMPORTANT]
> A Microsoft recomenda enfaticamente a sincronização de contas locais com contas administrativas já existentes no Azure Active Directory.

### <a name="hard-match-vs-soft-match"></a>Correspondência rígida vs Soft-Match
Para uma nova instalação do Connect, não há nenhuma diferença prática entre um disco rígido e uma correspondência fixa. A diferença está em uma situação de recuperação de desastre. Se você perdeu o servidor com Azure AD Connect, poderá reinstalar uma nova instância sem perder nenhum dado. Um objeto com um sourceAnchor é enviado para conectar durante a instalação inicial. A correspondência pode ser avaliada pelo cliente (Azure AD Connect), o que é muito mais rápido do que fazer o mesmo no Azure AD. Uma correspondência rígida é avaliada pelo Connect e pelo Azure AD. Uma correspondência flexível só é avaliada pelo Azure AD.

### <a name="other-objects-than-users"></a>Outros objetos do que os usuários
Para grupos e contatos habilitados para email, você pode fazer uma correspondência flexível com base em proxyAddresses. A correspondência fixa não é aplicável, pois você só pode atualizar sourceAnchor/imutável (usando o PowerShell) em usuários. Para grupos que não são habilitados para email, atualmente não há suporte para correspondência flexível ou correspondência fixa.

### <a name="admin-role-considerations"></a>Considerações de função de administrador
Para impedir que usuários locais não confiáveis façam a correspondência com um usuário de nuvem que tenha qualquer função de administrador, Azure AD Connect não corresponderá a objetos de usuário locais com objetos que têm uma função de administrador. Isso é por padrão. Para solucionar esse comportamento, você pode fazer o seguinte:

1.  Remova as funções de diretório do objeto de usuário somente em nuvem.
2.  Se houvesse uma falha na tentativa de sincronização do usuário, exclua o objeto em quarentena na nuvem.
3.  Disparar uma sincronização.
4.  Opcionalmente, adicione as funções de diretório de volta ao objeto de usuário na nuvem assim que a correspondência tiver ocorrido.



## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Criar uma nova Active Directory local a partir de dados no Azure AD
Alguns clientes começam com uma solução somente em nuvem com o Azure AD e não têm um AD local. Posteriormente, eles desejam consumir recursos locais e desejam criar um AD local com base nos dados do Azure AD. Azure AD Connect não pode ajudá-lo nesse cenário. Ele não cria usuários no local e não tem nenhuma capacidade de definir a senha local para o mesmo que no Azure AD.

Se o único motivo pelo qual você planeja adicionar o AD local é dar suporte a LOBs (aplicativos de linha de negócios), talvez você considere usar os [serviços de domínio do Azure ad](../../active-directory-domain-services/index.yml) em vez disso.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
