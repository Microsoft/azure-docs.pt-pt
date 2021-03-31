---
title: O que é a sincronização de hash de palavra-passe com a Azure AD? | Microsoft Docs
description: Descreve a sincronização do hash da palavra-passe.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23f88a783ca257090fc607cc9a16ec457c7e9c8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90016593"
---
# <a name="what-is-password-hash-synchronization-with-azure-ad"></a>O que é a sincronização de hash de palavra-passe com a Azure AD?
A sincronização de hash de palavra-passe é um dos métodos de entrada usados para realizar a identidade híbrida. O Azure AD Connect sincroniza um haxixe, do haxixe, da palavra-passe de um utilizador de uma instância do Ative Directory no local para uma instância AD Azure baseada na nuvem.

A sincronização de hash de palavra-passe é uma extensão da funcionalidade de sincronização do diretório implementada pela sincronização Azure AD Connect. Pode utilizar esta funcionalidade para iniciar seduca nos serviços Azure AD, como o Microsoft 365. Inscreva-se no serviço utilizando a mesma palavra-passe que utiliza para iniciar sôm no seu local de acesso ao Ative Directory.

![O que é o Azure AD Connect](./media/how-to-connect-password-hash-synchronization/arch1.png)

A sincronização de hash de palavra-passe ajuda reduzindo o número de palavras-passe, os seus utilizadores precisam de manter apenas uma. A sincronização de hash de palavra-passe pode:

* Melhore a produtividade dos seus utilizadores.
* Reduza os custos do seu helpdesk.  

O Password Hash Sync também permite [a deteção de credenciais vazadas](../identity-protection/concept-identity-protection-risks.md#user-risk) para as suas contas híbridas. A Microsoft trabalha ao lado de pesquisadores web escuros e agências de aplicação da lei para encontrar pares de nome de utilizador/palavra-passe disponíveis ao público. Se algum destes pares corresponder aos dos nossos utilizadores, a conta associada é transferida para um risco elevado. 

>[!NOTE]
> Apenas novas credenciais vazadas encontradas depois de permitir que o PHS seja processado contra o seu inquilino. A verificação contra pares credenciais anteriormente encontrados não é realizada.


Opcionalmente, pode configurar a sincronização de hash de palavra-passe como uma cópia de segurança se decidir utilizar a [Federação com Serviços da Federação de Diretórios Ativos (AD FS)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) como método de entrada.

Para utilizar a sincronização de hash de palavra-passe no seu ambiente, tem de:

* Instale a ligação ad azure.  
* Configure a sincronização do diretório entre o seu caso de Diretório Ativo no local e a sua instância do Azure Ative Directory.
* Ativar a sincronização do hash da palavra-passe.



Para mais informações, veja [o que é identidade híbrida?](whatis-hybrid-identity.md)




## <a name="next-steps"></a>Passos Seguintes

- [O que é a identidade híbrida?](whatis-hybrid-identity.md)
- [O que são o Azure AD Connect e o Connect Health?](whatis-azure-ad-connect.md)
- [O que é a autenticação pass-through (PTA)?](how-to-connect-pta.md)
- [O que é a federação?](whatis-fed.md)
- [O que é um único sinal?](how-to-connect-sso.md)
- [Como funciona a sincronização do hash da palavra-passe](how-to-connect-password-hash-synchronization.md)
