---
title: 'Azure AD Connect Sync: Enable AD recicla contentor de reciclagem / Microsoft Docs'
description: Este tópico recomenda a utilização da funcionalidade AD Recycle Bin com Azure AD Connect.
services: active-directory
keywords: Caixote de reciclagem AD, eliminação acidental, âncora de origem
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: afec4207-74f7-4cdd-b13a-574af5223a90
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/17/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed71e53a8cedc2907ac06dd75f11f9c762a78772
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85357210"
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Sincronização Azure AD Connect: Ativar o caixote de reciclagem AD
Recomenda-se que ative a funcionalidade AD Recycle Bin para os seus Diretórios Ativos no local, que são sincronizados com Azure AD. 

Se acidentalmente eliminar um objeto de utilizador AD no local e restaurá-lo utilizando a funcionalidade, a Azure AD restaura o objeto de utilizador Azure AD correspondente.  Para obter informações sobre a função DeCiclo de Reciclagem de AD, consulte a [visão geral do artigo para restaurar objetos de diretório ativo apagados](https://technet.microsoft.com/library/dd379542.aspx).

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>Benefícios de permitir o caixote de reciclagem AD
Esta funcionalidade ajuda a restaurar os objetos do utilizador Azure AD, fazendo o seguinte:

* Se eliminar acidentalmente um objeto de utilizador AD no local, o objeto de utilizador AZure AD correspondente será eliminado no ciclo de sincronização seguinte. Por padrão, o Azure AD mantém o objeto de utilizador Azure AD eliminado em estado de apagamento suave durante 30 dias.

* Se tiver no local a função AD Recycle Bin ativada, pode restaurar o objeto de utilizador AD eliminado no local sem alterar o seu valor de Âncora de Origem. Quando o objeto de utilizador AD recuperado no local for sincronizado com Azure AD, a Azure AD restaurará o correspondente objeto de utilizador Azure AD eliminado. Para obter informações sobre o atributo Source Anchor, consulte o artigo [Azure AD Connect: Design concepts](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#sourceanchor).

* Se não tiver no local a função AD Recycle Bin ativada, poderá ser-lhe exigido que crie um objeto de utilizador AD para substituir o objeto eliminado. Se o Serviço de Sincronização AD Connect AD estiver configurado para utilizar o atributo AD gerado pelo sistema (tal como o ObjectGuid) para o atributo Source Anchor, o objeto de utilizador AD recém-criado não terá o mesmo valor de Âncora de Origem que o objeto de utilizador AD eliminado. Quando o objeto de utilizador AD recém-criado é sincronizado com Azure AD, a Azure AD cria um novo objeto de utilizador AD Azure em vez de restaurar o objeto de utilizador Azure AD eliminado suavemente.

> [!NOTE]
> Por predefinição, a Azure AD mantém os objetos de utilizador Azure AD apagados em estado de apagamento suave durante 30 dias antes de serem permanentemente eliminados. No entanto, os administradores podem acelerar a eliminação de tais objetos. Uma vez eliminados permanentemente os objetos, já não podem ser recuperados, mesmo que a função de Caixote do Lixo AD no local esteja ativada.

## <a name="next-steps"></a>Próximos passos
**Tópicos de visão geral**

* [Azure AD Connect sync: Entenda e personalize a sincronização](how-to-connect-sync-whatis.md)

* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
