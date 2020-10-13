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
ms.openlocfilehash: 12073a75cd248c9226c7ce5ecc21b64617823b32
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89279640"
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Sincronização Azure AD Connect: Ativar o caixote de reciclagem AD
Recomenda-se que ative a funcionalidade AD Recycle Bin para os seus Diretórios Ativos no local, que são sincronizados com Azure AD. 

Se acidentalmente eliminar um objeto de utilizador AD no local e restaurá-lo utilizando a funcionalidade, a Azure AD restaura o objeto de utilizador Azure AD correspondente.  Para obter informações sobre a função DeCiclo de Reciclagem de AD, consulte a [visão geral do artigo para restaurar objetos de diretório ativo apagados](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd379542(v=ws.10)).

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>Benefícios de permitir o caixote de reciclagem AD
Esta funcionalidade ajuda a restaurar os objetos do utilizador Azure AD, fazendo o seguinte:

* Se eliminar acidentalmente um objeto de utilizador AD no local, o objeto de utilizador AZure AD correspondente será eliminado no ciclo de sincronização seguinte. Por padrão, o Azure AD mantém o objeto de utilizador Azure AD eliminado em estado de apagamento suave durante 30 dias.

* Se tiver no local a função AD Recycle Bin ativada, pode restaurar o objeto de utilizador AD eliminado no local sem alterar o seu valor de Âncora de Origem. Quando o objeto de utilizador AD recuperado no local for sincronizado com Azure AD, a Azure AD restaurará o correspondente objeto de utilizador Azure AD eliminado. Para obter informações sobre o atributo Source Anchor, consulte o artigo [Azure AD Connect: Design concepts](./plan-connect-design-concepts.md#sourceanchor).

* Se não tiver no local a função AD Recycle Bin ativada, poderá ser-lhe exigido que crie um objeto de utilizador AD para substituir o objeto eliminado. Se o Serviço de Sincronização AD Connect AD estiver configurado para utilizar o atributo AD gerado pelo sistema (tal como o ObjectGuid) para o atributo Source Anchor, o objeto de utilizador AD recém-criado não terá o mesmo valor de Âncora de Origem que o objeto de utilizador AD eliminado. Quando o objeto de utilizador AD recém-criado é sincronizado com Azure AD, a Azure AD cria um novo objeto de utilizador AD Azure em vez de restaurar o objeto de utilizador Azure AD eliminado suavemente.

> [!NOTE]
> Por predefinição, a Azure AD mantém os objetos de utilizador Azure AD apagados em estado de apagamento suave durante 30 dias antes de serem permanentemente eliminados. No entanto, os administradores podem acelerar a eliminação de tais objetos. Uma vez eliminados permanentemente os objetos, já não podem ser recuperados, mesmo que a função de Caixote do Lixo AD no local esteja ativada.

## <a name="next-steps"></a>Passos seguintes
**Tópicos de visão geral**

* [Azure AD Connect sync: Entenda e personalize a sincronização](how-to-connect-sync-whatis.md)

* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)