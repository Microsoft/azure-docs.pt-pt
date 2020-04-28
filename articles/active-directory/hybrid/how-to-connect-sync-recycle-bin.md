---
title: 'Sincronização azure AD Connect: Ativar o caixote do ciclo ad / Microsoft Docs'
description: Este tópico recomenda a utilização da funcionalidade AD Recycle Bin com o Azure AD Connect.
services: active-directory
keywords: Caixa de reciclagem ad, supressão acidental, âncora de origem
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: afec4207-74f7-4cdd-b13a-574af5223a90
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/17/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fe7d3ea7d4f6d648438efc1a484d5909ade2f23
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "60382900"
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Sincronização azure AD Connect: Ativar o recipiente de reciclagem de AD
Recomenda-se que ative a funcionalidade AD Recycle Bin para os seus Diretórios Ativos no local, que são sincronizados com a AD Azure. 

Se tiver eliminado acidentalmente um objeto de utilizador ad's no local e o restaurar utilizando a funcionalidade, o Azure AD restaura o objeto de utilizador da AD Azure correspondente.  Para obter informações sobre a funcionalidade AD Recycle Bin, consulte a visão geral do artigo [Cenário para restaurar objetos de diretório ativo apagados](https://technet.microsoft.com/library/dd379542.aspx).

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>Benefícios de permitir o caixote do ciclo de ad
Esta funcionalidade ajuda a restaurar os objetos do utilizador da AD Azure fazendo o seguinte:

* Se tiver eliminado acidentalmente um objeto de utilizador ad-ad no local, o objeto de utilizador da AD Azure correspondente será eliminado no próximo ciclo de sincronização. Por predefinição, a Azure AD mantém o objeto de utilizador AD AD eliminado em estado de soft-deleted durante 30 dias.

* Se tiver ativada a funcionalidade AD Recycle Bin no local, pode restaurar o objeto de utilizador ad-ad eliminado no local sem alterar o seu valor de Âncora Fonte. Quando o objeto de utilizador ad-condicionado recuperado no local for sincronizado para a AD Azure, o Azure AD irá restaurar o correspondente objeto de utilizador da AD AD de eliminação suave. Para obter informações sobre o atributo Source Anchor, consulte o artigo [Azure AD Connect: Conceitos](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#sourceanchor)de design .

* Se não tiver ativada a funcionalidade AD Recycle Bin no local, poderá ser necessário criar um objeto de utilizador AD para substituir o objeto eliminado. Se o Serviço de Sincronização de Ligação AD Azure estiver configurado para utilizar o atributo AD gerado pelo sistema (como o ObjectGuid) para o atributo Source Anchor, o objeto de utilizador aD recém-criado não terá o mesmo valor de Âncora fonte que o objeto de utilizador AD eliminado. Quando o objeto de utilizador ad recém-criado é sincronizado para o Azure AD, o Azure AD cria um novo objeto de utilizador da AD Azure em vez de restaurar o objeto de utilizador da AD Azure eliminado suavemente.

> [!NOTE]
> Por predefinição, a AD Azure mantém os objetos de utilizador AD Azure eliminados em estado de soft-deleted durante 30 dias antes de serem eliminados permanentemente. No entanto, os administradores podem acelerar a eliminação de tais objetos. Uma vez eliminados permanentemente os objetos, já não podem ser recuperados, mesmo que a funcionalidade ad recycle bin aD esteja ativada.

## <a name="next-steps"></a>Passos seguintes
**Tópicos de visão geral**

* [Sincronização Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)

* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
