---
title: Acesso Condicional - Acesso por bloco por localização - Diretório Ativo Azure
description: Criar uma política personalizada de acesso condicional para bloquear o acesso aos recursos por localização IP
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
ms.openlocfilehash: 5350e728ad9c30a6e9258ce9c7615baf020473a8
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83995127"
---
# <a name="conditional-access-block-access-by-location"></a>Acesso Condicional: Bloquear acesso por localização

Com a condição de localização no Acesso Condicional, pode controlar o acesso às suas aplicações na nuvem com base na localização da rede de um utilizador. A condição de localização é comumente usada para bloquear o acesso de países/regiões de onde a sua organização sabe que o tráfego não deve vir.

## <a name="define-locations"></a>Definir localizações

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou administrador de Acesso Condicional.
1. Navegue até **azure Ative Directy**  >  **Security**  >  **Conditional Access**  >  **Locals nomeados**.
1. Escolha **a nova localização**.
1. Dê um nome à sua localização.
1. Escolha **intervalos IP** se conhecer as gamas específicas de endereços IPv4 acessíveis externamente que compõem esse local ou **países/regiões**.
   1. Forneça as **gamas IP** ou selecione os **Países/Regiões** para a localização que está a especificar.
      * Se escolher Países/Regiões, pode optar opcionalmente por incluir áreas desconhecidas.
1. Escolha **Salvar**

Mais informações sobre a condição de localização no Acesso Condicional podem ser encontradas no artigo, [Qual é a condição de localização no Acesso Condicional do Diretório Ativo Azure](location-condition.md)

## <a name="create-a-conditional-access-policy"></a>Criar uma política de acesso condicional

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou administrador de Acesso Condicional.
1. Navegue até ao Acesso Condicional de Segurança **do Diretório Ativo do Azure.**  >  **Security**  >  **Conditional Access**
1. Selecione **Nova política.**
1. Dê um nome à sua apólice. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**
   1. Em **Incluir,** selecione **Todos os utilizadores**.
   1. Selecione **Done** (Concluído).
1. Em **aplicativos ou ações cloud**  >  **Inclua**, selecione todas as **aplicações em nuvem**, e selecione **Done**.
1. Sob **Conditions**  >  **condições de localização**.
   1. **Definir configurar** para **sim**
   1. **Incluir** **locais selecionados selecionados**
   1. Selecione a localização bloqueada que criou para a sua organização.
   1. Clique em **Selecionar**  >  **Feito**  >  **.**
1. Em **condições,**  >  **as aplicações do Cliente (Pré-visualização)**, definir **Configurar** para **Sim,** e selecionar **Feito**.
1. Sob **controlos de acesso**  >  **Bloco**, e **selecione Selecione**.
1. Confirme as suas definições e ajuste **a política de ativação** para **On**.
1. Selecione **Criar** para criar para ativar a sua política.

## <a name="next-steps"></a>Próximos passos

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Determine o impacto utilizando o modo apenas de relatório de acesso condicional](howto-conditional-access-report-only.md)

[Simular o sinal de comportamento usando a ferramenta de acesso condicional O que se a ferramenta](troubleshoot-conditional-access-what-if.md)
