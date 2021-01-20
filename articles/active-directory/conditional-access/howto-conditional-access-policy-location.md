---
title: Acesso Condicional - Acesso por bloco por localização - Diretório Ativo Azure
description: Crie uma política de acesso condicional personalizado para bloquear o acesso aos recursos através da localização IP
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
ms.openlocfilehash: e257ab39257b23c52aaadbe32f0325e8d71a8409
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98597983"
---
# <a name="conditional-access-block-access-by-location"></a>Acesso Condicional: Bloquear acesso por localização

Com a condição de localização no Acesso Condicional, pode controlar o acesso às suas aplicações na nuvem com base na localização da rede de um utilizador. A condição de localização é geralmente usada para bloquear o acesso de países/regiões de onde a sua organização sabe que o tráfego não deve vir.

## <a name="define-locations"></a>Definir localizações

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue para a **Azure Ative Directy**  >    >  **Security Conditional Access**  >  **Locals nomeados**.
1. Escolha **nova localização.**
1. Dê um nome à sua localização.
1. Escolha **as gamas IP** se conhecer as gamas específicas de endereços IPv4 acessíveis externamente que compõem essa localização ou **Países/Regiões.**
   1. Forneça as **gamas IP** ou selecione os **Países/Regiões** para a localização que está a especificar.
      * Se escolher Países/Regiões, pode optar opcionalmente por incluir áreas desconhecidas.
1. Escolha **Guardar**

Mais informações sobre a condição de localização em Acesso Condicional podem ser encontradas no artigo, [Qual é a condição de localização no Azure Ative Directy Acesso Condicional](location-condition.md)

## <a name="create-a-conditional-access-policy"></a>Criar uma política de acesso condicional

1. Inscreva-se no **portal Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Navegue pelo Acesso Condicional de Segurança **do Diretório Ativo Azure**  >    >  .
1. Selecione **Nova política**.
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes das suas políticas.
1. Em **Atribuições**, selecione **Utilizadores e grupos**
   1. Em **Incluir**, selecione **Todos os utilizadores**.
   1. Em **'Excluir',** selecione **Utilizadores e grupos** e escolha as contas de acesso de emergência ou break-glass da sua organização. 
   1. Selecione **Concluído**.
1. Em **aplicativos ou ações cloud**  >  **Inclua**, e selecione **todas as aplicações em nuvem**.
1. Em Localização **de Condições**  >  .
   1. Definir **Configurar** para **Sim**
   1. Em **Incluir**, selecione **locais selecionados**
   1. Selecione o local bloqueado que criou para a sua organização.
   1. Clique em **Selecionar**.
1. Nos **controlos de acesso** > selecione o Acesso ao **Bloco** e selecione **Select**.
1. Confirme as suas definições e defina **Ativar** a política para **on**.
1. Selecione **Criar** para criar a Política de Acesso Condicional.

## <a name="next-steps"></a>Passos seguintes

[Políticas comuns de acesso condicional](concept-conditional-access-policy-common.md)

[Determinar o impacto utilizando o modo de relatório de acesso condicional](howto-conditional-access-insights-reporting.md)

[Simular sinal no comportamento usando o acesso condicional E se a ferramenta](troubleshoot-conditional-access-what-if.md)
