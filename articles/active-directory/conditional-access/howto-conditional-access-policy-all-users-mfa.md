---
title: Acesso Condicional - Exigir MFA para todos os utilizadores - Diretório Ativo Azure
description: Criar uma política personalizada de acesso condicional para exigir que todos os utilizadores realizem a autenticação de vários fatores
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7af1347ac57e1a5c5ae99744924ea04fe9757581
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83995353"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>Acesso Condicional: Exigir MFA para todos os utilizadores

Como alex Weinert, o Diretório de Segurança De Identidade da Microsoft, menciona na sua publicação de blogue [Your Pa$$word não importa:](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)

> A tua senha não importa, mas a MFA importa! Com base nos nossos estudos, a sua conta tem mais de 99,9% menos probabilidade de ser comprometida se utilizar o MFA.

A orientação neste artigo ajudará a sua organização a criar uma política de MFA equilibrada para o seu ambiente.

## <a name="user-exclusions"></a>Exclusões de utilizadores

As políticas de Acesso Condicional são ferramentas poderosas, recomendamos excluir as seguintes contas da sua política:

* **Acesso de emergência** ou contas **de vidro para** evitar o bloqueio da conta em todo o inquilino. No cenário improvável, todos os administradores estão bloqueados fora do seu inquilino, a sua conta administrativa de acesso de emergência pode ser usada para iniciar sessão no inquilino, tomando medidas para recuperar o acesso.
   * Mais informações podem ser encontradas no artigo, Gerir contas de [acesso de emergência em Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Contas** de serviço e diretores de **serviço,** como a Conta De Sincronização De Ligação AD Azure. As contas de serviço são contas não interativas que não estão ligadas a nenhum utilizador em particular. São normalmente utilizados por serviços de back-end que permitem o acesso programático a aplicações, mas também são usados para iniciar sessão em sistemas para fins administrativos. Contas de serviço como estas devem ser excluídas, uma vez que o MFA não pode ser concluído programáticamente. As chamadas feitas pelos diretores de serviço não são bloqueadas pelo Acesso Condicional.
   * Se a sua organização tiver estas contas em uso em scripts ou código, considere substituí-las por [identidades geridas](../managed-identities-azure-resources/overview.md). Como uma sutição temporária, pode excluir estas contas específicas da política de base.

## <a name="application-exclusions"></a>Exclusões de candidaturas

As organizações podem ter muitas aplicações em nuvem em uso. Nem todas essas aplicações podem requerer igual segurança. Por exemplo, os pedidos de folha de pagamento e de atendimento podem exigir MFA, mas o refeitório provavelmente não. Os administradores podem optar por excluir aplicações específicas da sua política.

## <a name="create-a-conditional-access-policy"></a>Criar uma política de acesso condicional

Os seguintes passos ajudarão a criar uma política de Acesso Condicional para exigir que todos os utilizadores realizem a autenticação de vários fatores.

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou administrador de Acesso Condicional.
1. Navegue até ao Acesso Condicional de Segurança **do Diretório Ativo do Azure.**  >  **Security**  >  **Conditional Access**
1. Selecione **Nova política.**
1. Dê um nome à sua apólice. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**
   1. Sob **incluir,** **selecione Todos os utilizadores**
   1. Em **Excluir,** selecione **Utilizadores e grupos** e escolha as contas de acesso de emergência ou de vidro de emergência da sua organização. 
   1. Selecione **Done** (Concluído).
1. Em **aplicativos ou ações cloud**  >  **Inclua**, selecione todas as **aplicações em nuvem**.
   1. Em **Excluir,** selecione quaisquer aplicações que não exijam autenticação multifactor.
1. Em **Condições**  >  **Aplicações do Cliente (Pré-visualização)**, definir **Configurar** para **Sim**. Em **Select as aplicações do cliente esta política aplicar-se-á para** deixar todos os predefinições selecionados e **selecionados Feito**.
1. Sob **controlos de acesso**  >  **Grant**, selecione Acesso ao **Grant,** **exija a autenticação de vários fatores,** e selecione **Select**.
1. Confirme as suas definições e ajuste **a política de ativação** para **On**.
1. Selecione **Criar** para criar para ativar a sua política.

### <a name="named-locations"></a>Localizações com nome

As organizações podem optar por incorporar locais de rede conhecidos conhecidos como **localizações nomeadas** para as suas políticas de Acesso Condicional. Estes locais nomeados podem incluir redes IPv4 fidedignas como as para uma localização principal do escritório. Para mais informações sobre a configuração de locais nomeados, consulte o artigo Qual é a condição de localização no Acesso Condicional do [Diretório Ativo azure?](location-condition.md)

Na política de exemplo acima, uma organização pode optar por não exigir a autenticação de vários fatores se aceder a uma aplicação em nuvem a partir da sua rede corporativa. Neste caso, poderiam adicionar a seguinte configuração à política:

1. Em **Atribuições,** selecione **Locais**de  >  **Condições**.
   1. Configurar **Sim.**
   1. Incluir **qualquer local**.
   1. Excluir **todos os locais fidedignos.**
   1. Selecione **Done** (Concluído).
1. Selecione **Done** (Concluído).
1. **Guarde** as suas mudanças de política.

## <a name="next-steps"></a>Próximos passos

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Determine o impacto utilizando o modo apenas de relatório de acesso condicional](howto-conditional-access-report-only.md)

[Simular o sinal de comportamento usando a ferramenta de acesso condicional O que se a ferramenta](troubleshoot-conditional-access-what-if.md)
