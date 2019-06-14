---
title: 'Azure AD Connect: Funcionalidades em pré-visualização | Documentos da Microsoft'
description: Este tópico descreve com mais funcionalidades de detalhe que estão em pré-visualização no Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c75cd8cf-3eff-4619-bbca-66276757cc07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7def733a80aea1be77825bb9069217f5f43e003
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60347809"
---
# <a name="more-details-about-features-in-preview"></a>Obter mais detalhes sobre as funcionalidades em pré-visualização
Este tópico descreve como utilizar as funcionalidades atualmente em pré-visualização.

## <a name="group-writeback"></a>Repetição de escrita do grupo
A opção para a repetição de escrita do grupo de recursos opcionais permite-lhe a repetição de escrita **grupos do Office 365** para uma floresta com o Exchange instalado. Este é um grupo que sempre é controlado na cloud. Se tiver o Exchange no local, em seguida, pode efetuar um write back estes grupos para o local para que os utilizadores com uma caixa de correio do Exchange no local podem enviar e receber e-mails destes grupos de.

Podem encontrar mais informações sobre os grupos do Office 365 e como utilizá-los [aqui](https://aka.ms/O365g).

Um grupo do Office 365 é representado como um grupo de distribuição no local AD DS. Seu servidor do Exchange no local tem de ser no Exchange 2013 atualização cumulativa 8 (lançado em Março de 2015) ou o Exchange 2016 para reconhecer este novo tipo de grupo.

**Notas durante a pré-visualização**

* O atributo de livro de endereços não for preenchido atualmente na pré-visualização. Sem esse atributo, o grupo não é visível na GAL. A maneira mais fácil para preencher este atributo é usar o cmdlet do PowerShell do Exchange `update-recipient`.
* Apenas a florestas com o esquema do Exchange são alvos válidos para grupos. Se nenhum Exchange foi detetado, repetição de escrita do grupo não é possível ativar.
* Apenas floresta única organização implantações do Exchange são atualmente suportadas. Se tiver mais de um Exchange organização no local, terá uma solução de GALSync no local para estes grupos a aparecer na sua noutras florestas.
* A funcionalidade de repetição de escrita do grupo não manipula os grupos de segurança ou grupos de distribuição.

> [!NOTE]
> Uma subscrição do Azure AD Premium é necessária para repetição de escrita do grupo.
> 
>

## <a name="user-writeback"></a>Repetição de escrita do utilizador
> [!IMPORTANT]
> A funcionalidade de pré-visualização de repetição de escrita do utilizador foi removida na atualização de Agosto de 2015 para o Azure AD Connect. Se está ativado, deve desativar esta funcionalidade.
>
>

## <a name="next-steps"></a>Passos Seguintes
Continuar sua [instalação personalizada do Azure AD Connect](how-to-connect-install-custom.md).

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
