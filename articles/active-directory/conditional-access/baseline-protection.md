---
title: O que é uma proteção de linha de base no acesso condicional do Azure Active Directory? - pré-visualização | Microsoft Docs
description: Saiba como a proteção de linha de base garante que tem, pelo menos, o nível de linha de base de segurança ativada no seu ambiente do Azure Active Directory.
services: active-directory
keywords: acesso condicional a aplicações, acesso condicional com o Azure AD, acesso seguro a recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/08/2018
ms.author: joflore
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b3d8b1daef9f780bbba53f33ae451c63f80249b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60355030"
---
# <a name="what-is-baseline-protection-preview"></a>O que é a proteção de linha de base (pré-visualização)?  

No último ano, ataques de identidade tem aumentado em 300%. Para proteger o seu ambiente contra os ataques cada vez, o Azure Active Directory (Azure AD) apresenta um novo recurso chamado proteção de linha de base. Proteção de linha de base é um conjunto de predefinidos [políticas de acesso condicional](../active-directory-conditional-access-azure-portal.md). O objetivo dessas diretivas é para se certificar de que tem, pelo menos, o nível de linha de base de segurança ativada em todas as edições do Azure AD. 

Este artigo fornece uma descrição geral de proteção de linha de base no Azure Active Directory.


 
## <a name="require-mfa-for-admins"></a>Exigir a MFA para os administradores

Os utilizadores com acesso a contas com privilégios têm acesso irrestrito ao seu ambiente. Devido ao poder que essas contas têm, deve tratá-los com cuidado especial. Métodos comuns para melhorar a proteção de contas com privilégios é exigir um formulário mais forte de verificação de conta quando são utilizadas para início de sessão. No Azure Active Directory, pode obter uma verificação de conta mais forte ao exigir autenticação multifator (MFA).  

**Exigir a MFA para os administradores** é uma política de linha de base que exija a MFA para as seguintes funções de diretório: 

- Administrador global  

- Administrador do SharePoint  

- Administrador do Exchange  

- Administrador de acesso condicional  

- Administrador de segurança  


![Azure Active Directory](./media/baseline-protection/01.png)

Esta política de linha de base fornece-lhe a opção para excluir usuários. Pode querer excluir uma *[conta de administrador de acesso de emergência](../users-groups-roles/directory-emergency-access.md)* para garantir que não é bloqueado ao inquilino.


## <a name="enable-a-baseline-policy"></a>Ativar uma política de linha de base 

Embora as políticas de linha de base estiverem em pré-visualização, são por predefinição não ativada. Tem de ativar manualmente uma política para ativá-lo. Se ativar explicitamente as políticas de linha de base a fase de pré-visualização, eles permanecerá ativos quando esta funcionalidade atinge disponibilidade geral. A alteração de comportamento planeada é o motivo por que, além do mais para ativar e desativar, tem uma terceira opção para definir o estado de uma política: **Ativar política automaticamente no futuro**. Ao selecionar esta opção, pode deixar as políticas desativadas durante a pré-visualização, mas tem de ativá-las automaticamente quando esta funcionalidade atinge disponibilidade geral do Microsoft. Se não explicitamente ativar políticas de linha de base agora e não selecionar a **ativar política automaticamente no futuro** opção, as políticas permanecerá desativado quando esta funcionalidade atinge disponibilidade geral.


**Para ativar uma política de linha de base:**  

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) como administrador global, administrador de segurança ou administrador de acesso condicional.

2. Na **portal do Azure**, na barra de navegação esquerda, clique em **Azure Active Directory**.

    ![Azure Active Directory](./media/baseline-protection/02.png)

3. Na **do Azure Active Directory** página, além do **segurança** secção, clique em **acesso condicional**.

    ![Acesso condicional](./media/baseline-protection/05.png)

4. Na lista de políticas, clique numa política que começa com **política de linha de base:**. 

5. Para ativar a política, clique em **utilizar a política imediatamente**.

6. Clique em **Guardar**. 
 
  
 

## <a name="what-you-should-know"></a>O que deve saber 

Embora a gestão de políticas de acesso condicional personalizada requer uma licença do Azure AD Premium, as políticas de linha de base estão disponíveis em todas as edições do Azure AD.     

As funções de diretório que estão incluídas na política de linha de base são as funções do Azure AD com mais privilégios. 

Se o ter privilegiado contas que são utilizadas nos seus scripts, deve substitui-los com [geridos identidades para recursos do Azure](../managed-identities-azure-resources/overview.md) ou [principais com certificados de serviço](../develop/howto-authenticate-service-principal-powershell.md). Como solução temporária, pode excluir contas de usuários específicos da política de linha de base. 

Aplicam políticas de linha de base para fluxos de autenticação legada como POP, IMAP, cliente de ambiente de trabalho do Office mais antiga. 




## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte:

- [Cinco etapas para proteger a sua infraestrutura de identidade](https://docs.microsoft.com/azure/security/azure-ad-secure-steps)

- [O que é o acesso condicional no Azure Active Directory?](overview.md) 

