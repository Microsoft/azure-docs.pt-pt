---
title: Versões de fluxo de utilizador em Azure Ative Directory B2C | Microsoft Docs
description: Conheça as versões dos fluxos de utilizador disponíveis no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1c05ff1bf1956943230bf523584025787495d57f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102517859"
---
# <a name="user-flow-versions-in-azure-active-directory-b2c"></a>Versões de fluxo de utilizador em Azure Ative Directory B2C

Os fluxos de utilizador no Azure Ative Directory B2C (Azure AD B2C) ajudam-no a definir [políticas](user-flow-overview.md) comuns que descrevem plenamente as experiências de identidade do cliente. Estas experiências incluem inscrição, inscrição, reset de palavra-passe ou edição de perfis. As tabelas abaixo descrevem os fluxos de utilizador que estão disponíveis no Azure AD B2C.

> [!IMPORTANT]
> Mudámos a forma como fazemos referência às versões de fluxos de utilizador. Anteriormente, disponibilizávamos versões V1 (prontas para produção) e versões V1.1 e V2 (pré-visualização). Agora, consolidamos fluxos de utilizadores em duas versões:
>
>- Os fluxos **recomendados** para o utilizador são as novas versões de pré-visualização dos fluxos dos utilizadores. São completamente testados e combinam todas as características das versões **legados V2** e **V1.1.** Daqui para a frente, os novos fluxos recomendados de utilizador serão mantidos e atualizados. Assim que se deslocar para estes novos fluxos recomendados de utilizador, terá acesso a novas funcionalidades à medida que forem lançadas.
>- Os fluxos **padrão** do utilizador, anteriormente conhecidos como **V1,** estão geralmente disponíveis, fluxos de utilizador prontos para a produção. Se os fluxos dos seus utilizadores forem críticos da missão e dependerem de versões altamente estáveis, pode continuar a utilizar fluxos padrão de utilizador, percebendo que estas versões não serão mantidas e atualizadas.
>
>Todos os fluxos de pré-visualização legado (V1.1 e V2) estão em vias de depreciação até 1 de agosto de **2021**. Sempre que possível, recomendamos vivamente que [mude para as novas versões **Recomendadas** o](#how-to-switch-to-a-new-recommended-user-flow) mais rapidamente possível para que possa sempre tirar partido das funcionalidades e atualizações mais recentes. *Estas alterações aplicam-se apenas à nuvem pública do Azure. Outros ambientes continuarão a utilizar a [versão de fluxo de utilizador legado](user-flow-versions-legacy.md).*

## <a name="recommended-user-flows"></a>Fluxos recomendados do utilizador

Os fluxos recomendados para o utilizador são versões de pré-visualização que combinam novas funcionalidades com as capacidades de V2 e V1.1. Daqui para a frente, os fluxos recomendados do utilizador serão mantidos e atualizados.

| Fluxo de utilizador | Description |
| --------- | ----------- |
| Reset da palavra-passe (pré-visualização) | Permite que um utilizador escolha uma nova palavra-passe depois de verificar o seu e-mail. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>Definições de compatibilidade token</li><li>[Controlo de idade](age-gating.md)</li><li>[requisitos de complexidade de palavras-passe](password-complexity.md)</li></ul> |
| Edição de perfis (pré-visualização) | Permite que um utilizador configuure os seus atributos de utilizador. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Vida útil simbólica](tokens-overview.md)</li><li>Definições de compatibilidade token</li><li>Comportamento da sessão</li></ul> |
| Iniciar sção (pré-visualização) | Permite que um utilizador entre na sua conta. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>[Vida útil simbólica](tokens-overview.md)</li><li>Definições de compatibilidade token</li><li>Comportamento da sessão</li><li>[Controlo de idade](age-gating.md)</li><li>Personalização de página de inscrição</li></ul> |
| Inscreva-se (pré-visualização) | Permite que um utilizador crie uma conta. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>[Vida útil simbólica](tokens-overview.md)</li><li>Definições de compatibilidade token</li><li>Comportamento da sessão</li><li>[Controlo de idade](age-gating.md)</li><li>[Requisitos de complexidade de palavras-passe](password-complexity.md)</li></ul> |
| Inscreva-se e inscreva-se (pré-visualização) | Permite que um utilizador crie uma conta ou assine na sua conta. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>[Controlo de idade](age-gating.md)</li><li>[Requisitos de complexidade de palavras-passe](password-complexity.md)</li></ul> |

## <a name="standard-user-flows"></a>Fluxos padrão do utilizador

Os fluxos padrão do utilizador (anteriormente referidos como V1) estão geralmente disponíveis, fluxos de utilizador prontos para a produção. Os fluxos padrão dos utilizadores não serão atualizados daqui para a frente.

| Fluxo de utilizador | Description |
| --------- | ----------- | ----------- |
| Reposição de palavras-passe | Permite que um utilizador escolha uma nova palavra-passe depois de verificar o seu e-mail. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>Definições de compatibilidade token</li><li>[Requisitos de complexidade de palavras-passe](password-complexity.md)</li></ul> |
| Edição de perfis | Permite que um utilizador configuure os seus atributos de utilizador. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Vida útil simbólica](tokens-overview.md)</li><li>Definições de compatibilidade token</li><li>Comportamento da sessão</li></ul> |
| Iniciar sessão | Permite que um utilizador entre na sua conta. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>[Vida útil simbólica](tokens-overview.md)</li><li>Definições de compatibilidade token</li><li>Comportamento da sessão</li><li>Iniciar s-in</li><li>Redefinição da palavra-passe da força</li><li>Mantenha-me inscrito (KMSI)</ul><br>Não é possível personalizar a interface do utilizador com este fluxo de utilizador. |
| Inscrever-se | Permite que um utilizador crie uma conta. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>[Vida útil simbólica](tokens-overview.md)</li><li>Definições de compatibilidade token</li><li>Comportamento da sessão</li><li>[Requisitos de complexidade de palavras-passe](password-complexity.md)</li></ul> |
| Inscrever-se e iniciar sessão | Permite que um utilizador crie uma conta ou inscreva-se na sua conta. Utilizando este fluxo de utilizador, pode configurar: <ul><li>[Autenticação multifator](multi-factor-authentication.md)</li><li>[Vida útil simbólica](tokens-overview.md)</li><li>Definições de compatibilidade token</li><li>Comportamento da sessão</li><li>[Requisitos de complexidade de palavras-passe](password-complexity.md)</li></ul>|


## <a name="how-to-switch-to-a-new-recommended-user-flow"></a>Como mudar para um novo fluxo de utilizador recomendado

Para mudar de uma versão antiga de um fluxo de utilizador para a nova versão **recomendada de** pré-visualização, siga estes passos:

1. Crie uma nova política de fluxo de utilizador seguindo os passos em [Tutorial: Crie fluxos de utilizador no Azure Ative Directory](tutorial-create-user-flows.md). Ao criar o fluxo do utilizador, selecione a versão **Recomendada.**

3. Configure o seu novo fluxo de utilizador com as mesmas definições que foram configuradas na política do legado.

4. Atualize o URL de inscrição da sua aplicação para a política recém-criada.

5. Depois de ter testado o fluxo do utilizador e confirmado que está a funcionar, elimine o fluxo do utilizador legado seguindo estes passos:
   1. No menu geral do inquilino Azure AD B2C, selecione **fluxos de utilizador**.
   2. Encontre o fluxo do utilizador que pretende eliminar.
   3. Na última coluna, selecione o menu de contexto **(...)** e, em seguida, **selecione Delete**.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="can-i-still-create-legacy-v2-and-v11-user-flows"></a>Posso ainda criar fluxos de utilizador legados V2 e V1.1?

Não será capaz de criar novos fluxos de utilizador com base nas versões V2 e V1.1, mas pode continuar a ler, atualizar e eliminar qualquer fluxo de utilizador legado V2 e V1.1 que esteja a utilizar atualmente.

### <a name="is-there-any-reason-to-continue-using-legacy-v2-and-v11-user-flows"></a>Existe alguma razão para continuar a utilizar os fluxos de utilizador legadoS V2 e V1.1?

Nem por isso. As novas versões de pré-visualização **recomendadas** contêm a mesma funcionalidade que as versões V2 e V1.1. Nada foi removido e, na verdade, agora incluem características adicionais.

### <a name="if-i-dont-switch-from-legacy-v2-and-v11-policies-how-will-it-impact-my-application"></a>Se eu não mudar das políticas de V2 e V1.1, como é que isso vai afetar a minha aplicação?

Se estiver a utilizar um fluxo de utilizador V2 ou V1.1, a sua aplicação não será afetada por esta alteração de versão. Mas para ter acesso a novas funcionalidades ou mudanças de política, terá de mudar para as novas versões **Recomendadas.**

### <a name="will-microsoft-still-support-my-legacy-v2-or-v11-user-flow-policy"></a>A Microsoft continuará a suportar a minha política de fluxo de utilizador V2 ou V1.1?

As versões V2 e V1.1 dos fluxos dos utilizadores continuarão a ser totalmente suportadas.
