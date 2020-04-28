---
title: 'Azure AD Connect: Funcionalidades na pré-visualização Microsoft Docs'
description: Este tópico descreve em mais detalhes as funcionalidades que estão em pré-visualização no Azure AD Connect.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79261284"
---
# <a name="more-details-about-features-in-preview"></a>Mais detalhes sobre funcionalidades na pré-visualização
Este tópico descreve como usar funcionalidades atualmente em pré-visualização.

## <a name="group-writeback"></a>Repetição de escrita do grupo
A opção de reescrita em grupo em funcionalidades opcionais permite-lhe reescrever o **Office 365 Groups** para uma floresta com Exchange instalado. Este é um grupo que é sempre dominado na nuvem. Se tiver exchange on-local, então pode reescrever estes grupos para as instalações para que os utilizadores com uma caixa de correio de troca no local possam enviar e receber e-mails destes grupos.

Mais informações sobre os Grupos 365 do Office e como utilizá-los podem ser consultados [aqui](https://aka.ms/O365g).

Um grupo do Office 365 está representado como um grupo de distribuição no ad DS no local. O seu servidor de intercâmbio no local deve estar na Exchange 2013 atualização cumulativa 8 (lançada em março de 2015) ou Exchange 2016 para reconhecer este novo tipo de grupo.

**Notas durante a pré-visualização**

* O atributo do livro de endereços não está atualmente povoado na pré-visualização. Sem este atributo, o grupo não é visível na GAL. A maneira mais fácil de povoar este atributo `update-recipient`é usar o cmdlet Exchange PowerShell .
* Apenas as florestas com o esquema de troca são alvos válidos para grupos. Se não foi detetada nenhuma Troca, então a redação em grupo não é possível de ativar.
* Apenas os destacamentos de organização de intercâmbio uniflorestal são atualmente apoiados. Se você tem mais do que uma organização exchange no local, então você precisa de uma solução GALSync no local para que estes grupos apareçam nas suas outras florestas.
* A funcionalidade de redação do Grupo não trata de grupos de segurança ou de grupos de distribuição.

> [!NOTE]
> É necessária uma subscrição do Azure AD Premium para reprodução em grupo.
> 
>

## <a name="user-writeback"></a>Redação do utilizador
> [!IMPORTANT]
> A funcionalidade de pré-visualização de reprodução do utilizador foi removida na atualização de agosto de 2015 para o Azure AD Connect. Se o ativou, deverá desativar esta funcionalidade.
>
>

## <a name="next-steps"></a>Passos seguintes
Continue a [instalação personalizada de Azure AD Connect](how-to-connect-install-custom.md).

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
