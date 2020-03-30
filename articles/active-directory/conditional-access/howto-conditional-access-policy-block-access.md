---
title: Acesso Condicional - Acesso ao bloco - Diretório Ativo Azure
description: Criar uma política personalizada de acesso condicional para
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb,
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdbf4f352bc7d6874cab9c12adf5f1717d4b8f9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295731"
---
# <a name="conditional-access-block-access"></a>Acesso Condicional: Acesso ao bloco

Para organizações com uma abordagem conservadora de migração em nuvem, o bloco toda a política é uma opção que pode ser usada. 

> [!CAUTION]
> A configuração errada de uma política de blocos pode levar a que as organizações sejam bloqueadas fora do portal Azure.

Políticas como estas podem ter efeitos colaterais não intencionais. Os testes e validação adequados são vitais antes de permitir. Os administradores devem utilizar ferramentas como o [modo de relatório de acesso condicional](concept-conditional-access-report-only.md) e a ferramenta What If in Conditional [Access](what-if-tool.md) ao efazer alterações.

## <a name="user-exclusions"></a>Exclusões de utilizadores

As políticas de Acesso Condicional são ferramentas poderosas, recomendamos excluir as seguintes contas da sua política:

* **Acesso de emergência** ou contas **de vidro para** evitar o bloqueio da conta em todo o inquilino. No cenário improvável, todos os administradores estão bloqueados fora do seu inquilino, a sua conta administrativa de acesso de emergência pode ser usada para iniciar sessão no inquilino, tomando medidas para recuperar o acesso.
   * Mais informações podem ser encontradas no artigo, Gerir contas de [acesso de emergência em Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Contas** de serviço e diretores de **serviço,** como a Conta De Sincronização De Ligação AD Azure. As contas de serviço são contas não interativas que não estão ligadas a nenhum utilizador em particular. São normalmente utilizados por serviços de back-end e permitem o acesso programático a aplicações. As contas de serviço devem ser excluídas, uma vez que o MFA não pode ser concluído programáticamente.
   * Se a sua organização tiver estas contas em uso em scripts ou código, considere substituí-las por [identidades geridas](../managed-identities-azure-resources/overview.md). Como uma sutição temporária, pode excluir estas contas específicas da política de base.

## <a name="create-a-conditional-access-policy"></a>Criar uma política de acesso condicional

Os seguintes passos ajudarão a criar políticas de Acesso Condicional para bloquear o acesso a todas as aplicações, exceto o [Office 365](concept-conditional-access-cloud-apps.md#office-365-preview) se os utilizadores não estiverem numa rede de confiança. Estas políticas são colocadas no [modo apenas de Relatório](howto-conditional-access-report-only.md) para iniciar para que os administradores possam determinar o impacto que terão nos utilizadores existentes. Quando os administradores se sentem confortáveis em aplicar as políticas como pretendem, podem trocá-las para **On**.

A primeira política bloqueia o acesso a todas as aplicações, exceto as aplicações do Office 365, se não num local de confiança.

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou administrador de Acesso Condicional.
1. Navegue até ao**Acesso Condicional**de**Segurança** >  **do Diretório** > Ativo do Azure.
1. Selecione **Nova política.**
1. Dê um nome à sua apólice. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**.
   1. Em **Incluir,** selecione **Todos os utilizadores**.
   1. Em **Excluir,** selecione **Utilizadores e grupos** e escolha as contas de acesso de emergência ou de vidro de emergência da sua organização. 
   1. Selecione **Done** (Concluído).
1. Em **aplicações ou ações cloud,** selecione as seguintes opções:
   1. Em **Incluir,** selecione **Todas as aplicações em nuvem**.
   1. Em **Excluir, selecione** **Office 365 (pré-visualização)**- **selecione ,** e depois selecione **Done**.
1. Em **condições:**
   1. Sob **condições** > **de localização**.
      1. **Definir configurar** para **sim**
      1. Em **Incluir, selecione** **Qualquer local**.
      1. Em **Excluir,** selecione **Todos os locais fidedignos**.
      1. Selecione **Done** (Concluído).
   1. Em **aplicações do Cliente (Pré-visualização)**, definir **Configurar** para **Sim,** e selecionar **Feito,** em **seguida, Feito**.
1. Sob **controlos** > de**acesso,** selecione **o acesso ao bloco**e, em seguida, selecione **Selecione**.
1. Confirme as suas definições e ajuste **a política de ativação** **apenas**para reportar .
1. Selecione **Criar** para criar para ativar a sua política.

Uma segunda política é criada abaixo para exigir a autenticação de vários fatores ou um dispositivo compatível para os utilizadores do Office 365.

1. Selecione **Nova política.**
1. Dê um nome à sua apólice. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**.
   1. Em **Incluir,** selecione **Todos os utilizadores**.
   1. Em **Excluir,** selecione **Utilizadores e grupos** e escolha as contas de acesso de emergência ou de vidro de emergência da sua organização. 
   1. Selecione **Done** (Concluído).
1. Em **aplicativos ou ações** > cloud**Inclua**, **selecione aplicações, escolha**o Office **365 (pré-visualização)**, e selecione **Select**, em **seguida, Feito**.
1. Sob **controlos** > de acesso**Grant**, selecione Acesso **ao Grant**.
   1. Selecione **Exigir autenticação de vários fatores** e exija que o dispositivo seja marcado como **selecione** **compatível** .
   1. Certifique-se **de que todos os comandos selecionados** são selecionados.
   1. Selecione **Selecionar**.
1. Confirme as suas definições e ajuste **a política de ativação** **apenas**para reportar .
1. Selecione **Criar** para criar para ativar a sua política.

## <a name="next-steps"></a>Passos seguintes

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Determine o impacto utilizando o modo apenas de relatório de acesso condicional](howto-conditional-access-report-only.md)

[Simular o sinal de comportamento usando a ferramenta de acesso condicional O que se a ferramenta](troubleshoot-conditional-access-what-if.md)
