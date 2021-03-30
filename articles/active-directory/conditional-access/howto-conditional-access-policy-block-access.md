---
title: Acesso Condicional - Acesso ao bloco - Diretório Ativo Azure
description: Criar uma política de acesso condicional personalizado para
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb,
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e0801daa5bf83889be87987d440e377287b5ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92366195"
---
# <a name="conditional-access-block-access"></a>Acesso Condicional: Acesso ao bloco

Para organizações com uma abordagem conservadora de migração em nuvem, a política do bloco é uma opção que pode ser usada. 

> [!CAUTION]
> A configuração errada de uma política de blocos pode levar a que as organizações sejam bloqueadas fora do portal Azure.

Políticas como estas podem ter efeitos colaterais não intencionais. Os testes e validações adequados são vitais antes de permitir. Os administradores devem utilizar ferramentas como [o modo de relatório de acesso condicional](concept-conditional-access-report-only.md) e a ferramenta What If in [Conditional Access](what-if-tool.md) ao eseme fazer alterações.

## <a name="user-exclusions"></a>Exclusões de utilizadores

As políticas de acesso condicional são ferramentas poderosas, recomendamos excluir as seguintes contas da sua política:

* **Acesso de emergência** ou contas **de break-glass** para evitar o bloqueio de conta em todo o inquilino. No cenário improvável de todos os administradores estarem bloqueados fora do seu inquilino, a sua conta administrativa de acesso de emergência pode ser usada para entrar no arrendatário tomar medidas para recuperar o acesso.
   * Mais informações podem ser encontradas no artigo, [Gerir contas de acesso de emergência em Azure AD](../roles/security-emergency-access.md).
* **Contas de serviço** e **principais serviços**, como a Conta Azure AD Connect Sync. As contas de serviço são contas não interativas que não estão ligadas a nenhum utilizador em particular. Normalmente são utilizados por serviços back-end que permitem o acesso programático a aplicações, mas também são usados para iniciar seduções em sistemas para fins administrativos. Contas de serviço como estas devem ser excluídas, uma vez que o MFA não pode ser concluído programáticamente. As chamadas efetuadas pelos principais serviços não são bloqueadas pelo Acesso Condicional.
   * Se a sua organização tiver estas contas em uso em scripts ou código, considere substituí-las por [identidades geridas.](../managed-identities-azure-resources/overview.md) Como solução temporária, pode excluir estas contas específicas da política de base.

## <a name="create-a-conditional-access-policy"></a>Criar uma política de acesso condicional

As seguintes medidas ajudarão a criar políticas de Acesso Condicional para bloquear o acesso a todas as aplicações, com exceção [do Office 365,](concept-conditional-access-cloud-apps.md#office-365) se os utilizadores não estiverem numa rede de confiança. Estas políticas são colocadas no [modo report-only](howto-conditional-access-insights-reporting.md) para iniciar para que os administradores possam determinar o impacto que terão nos utilizadores existentes. Quando os administradores se sentem confortáveis em que as políticas se apliquem como pretendem, podem mudá-las para **On**.

A primeira política bloqueia o acesso a todas as aplicações, com exceção das aplicações da Microsoft 365, caso não se afirmem numa localização fidedigna.

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue pelo Acesso Condicional de Segurança **do Diretório Ativo Azure**  >    >  .
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**.
   1. Em **Incluir**, selecione **Todos os utilizadores**.
   1. Em **'Excluir',** selecione **Utilizadores e grupos** e escolha as contas de acesso de emergência ou break-glass da sua organização. 
   1. Selecione **Concluído**.
1. Em **aplicativos ou ações cloud,** selecione as seguintes opções:
   1. Em **Incluir**, selecione **Todas as aplicações em nuvem**.
   1. Under **Exclude**, selecione **Office 365**, selecione **Select**, em seguida, selecione **Fazer**.
1. Em **Condições:**
   1. Em Localização **de Condições**  >  .
      1. Definir **Configurar** para **Sim**
      1. Em **Incluir**, selecione **Qualquer local**.
      1. Em **'Excluir'** selecione **Todas as localizações fidedignas**.
      1. Selecione **Concluído**.
   1. Sob **as aplicações do Cliente (Preview)**, desconfigure para **Sim**, e selecione **Feito**, em seguida, **Feito**. 
1. Sob **controlos de acesso**  >  **Grant**, selecione **o acesso ao Bloco** e, em seguida, selecione **Select**.
1. Confirme as suas definições e defina **Ativar** a política **apenas para reportar**.
1. Selecione **Criar** para criar para ativar a sua política.

Uma segunda política é criada abaixo para exigir a autenticação de vários fatores ou um dispositivo em conformidade para os utilizadores da Microsoft 365.

1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**.
   1. Em **Incluir**, selecione **Todos os utilizadores**.
   1. Em **'Excluir',** selecione **Utilizadores e grupos** e escolha as contas de acesso de emergência ou break-glass da sua organização. 
   1. Selecione **Concluído**.
1. No **âmbito das aplicações ou ações cloud**  >  **Inclua**, **selecione selecione apps**, escolha Office **365**, e selecione , em seguida, **Feito**. 
1. Sob **controlos de acesso**  >  **Grant**, selecione **Grant access**.
   1. **Selecione Exigir a autenticação de vários fatores** e **exigir que o dispositivo seja marcado como compatível** **Selecione**.
   1. Certifique-se de que é selecionado **todos os controlos selecionados.**
   1. Selecione **Selecionar**.
1. Confirme as suas definições e defina **Ativar** a política **apenas para reportar**.
1. Selecione **Criar** para criar para ativar a sua política.

## <a name="next-steps"></a>Passos seguintes

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Determinar o impacto utilizando o modo de relatório de acesso condicional](howto-conditional-access-insights-reporting.md)

[Simular sinal no comportamento usando o acesso condicional E se a ferramenta](troubleshoot-conditional-access-what-if.md)
