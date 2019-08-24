---
title: O que é Azure Active Directory Identity Protection (atualizado)? | Microsoft Docs
description: O que é Azure Active Directory Identity Protection (atualizado)?
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 10/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2f7e26e2f32ba02db9afc0676a99003717ff585
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991084"
---
# <a name="what-is-azure-active-directory-identity-protection-refreshed"></a>O que é Azure Active Directory Identity Protection (atualizado)?

A experiência de proteção de identidade foi atualizada para proteger melhor as identidades da sua organização. Essa experiência atualizada fornece:

- Experiência de administração reprojetada que gira em relação ao risco mais relevante para o risco do usuário e o risco de entrada
- Experiência de investigações poderosas com suporte para filtragem, classificação e downloads inteligentes
- Melhor cálculo de risco do usuário para ajudá-lo a priorizar seus esforços em direção aos usuários que têm maior probabilidade de serem comprometidos
- Novo suporte à API para habilitar o acesso programático a dados de risco
- Processo de comentários de administrador simplificado que permite que você proteja seus usuários imediatamente
- Novo aprendizado de máquina supervisionado para melhorar a precisão das avaliações de risco

A segurança é uma das principais preocupações para as organizações atuais. A maioria das violações de segurança ocorre quando os invasores têm acesso a um ambiente, roubando a identidade de um usuário. Ao longo dos anos, os invasores se tornaram cada vez mais eficazes em aproveitar as violações de terceiros e usar ataques de phishing sofisticados. Assim que os invasores obtiverem acesso a contas de usuário com privilégios baixos, será relativamente fácil obter acesso a recursos importantes da empresa por meio da movimentação lateral. 

Para responder a essas ameaças, Azure AD Identity Protection capacita você a: 

- Impedir proativamente que identidades comprometidas sejam abusos 
- Mitigar riscos automaticamente quando uma atividade suspeita for detectada 
- Investigue usuários arriscados e entradas para resolver possíveis vulnerabilidades  
- Ser alertado quando o risco de um usuário atingir um limite especificado 

Azure AD Identity Protection é um recurso do Azure Active Directory Premium P2 que permite que você configure políticas para responder automaticamente quando a identidade de um usuário é comprometida ou quando alguém que não seja o proprietário da conta está tentando entrar usando seu identidade. Essas políticas, além de outros controles de acesso condicional fornecidos pelo Azure AD, podem bloquear automaticamente o acesso ou iniciar ações de mitigação, como a redefinição de senha ou a imposição de autenticação multifator. Além disso, a proteção de identidade fornece recursos de monitoramento e relatório para obter uma visão mais profunda do risco e comprometeções potenciais em sua organização. 

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWsS6Q]

## <a name="risk-events"></a>Eventos de risco

Azure AD Identity Protection detecta os seguintes eventos de risco: 

| Tipo de evento de risco | Descrição | Tipo de deteção |
| --- | --- | --- |
| Percurso atípico | Entre em um local atípicos com base nas entradas recentes do usuário. | Offline |
| Endereços IP anónimos | Entre em um endereço IP anônimo (por exemplo: Tor browser, Anonymizer VPNs). | Tempo real |
| Propriedades de entrada desconhecidas | Entre com propriedades que não vimos recentemente para o usuário determinado. | Tempo real |
| Endereço IP ligado a software maligno | Entrar de um endereço IP vinculado por malware | Offline |
| Credenciais vazadas | Esse evento de risco indica que as credenciais válidas do usuário foram vazadas | Offline |

## <a name="types-of-risk"></a>Tipos de risco 

A proteção de identidade baseia-se em dois tipos de risco:

- Risco de entrada
- Risco de utilizador

### <a name="sign-in-risk"></a>Risco de entrada

Um risco de entrada representa a probabilidade de que uma determinada solicitação de autenticação não seja autorizada pelo proprietário da identidade.

Há duas avaliações de risco de entrada: 

- **Risco de entrada (em tempo real)** – o risco de entrada (em tempo real) se baseia em todas as detecções em tempo real que são disparadas durante o processamento da entrada.  
- **Risco de entrada (agregação)** – o risco de entrada (agregação) é o risco total de uma entrada. Ele é calculado por um modelo de aprendizado de máquina que considera:
   - Detecções em tempo real (descritas acima)
   - Detecções offline (que são acionadas após a entrada ter ocorrido) 
   - Todos os outros recursos da entrada

### <a name="user-risk"></a>Risco de utilizador

Um risco de usuário representa a probabilidade de que uma determinada identidade seja comprometida. 

O risco do usuário é calculado Considerando todos os riscos associados ao usuário:

- Todas as entradas arriscadas
- Todos os eventos de risco não vinculados a uma entrada
- O risco do usuário atual
- Qualquer correção de risco ou ações de descarte executadas na data do usuário até o momento

## <a name="how-identity-protection-detects-risk"></a>Como a proteção de identidade detecta riscos  

O Azure AD usa o aprendizado de máquina para detectar anomalias e atividades suspeitas, usando os dois sinais detectados em tempo real durante entradas, bem como sinais não reais relacionados a usuários e suas atividades de entrada. Usando esses dados, a proteção de identidade calcula um risco de entrada em tempo real sempre que um usuário é autenticado, bem como determina um nível geral de risco do usuário para cada usuário. A proteção de identidade permite que você realize ações automaticamente sobre essas detecções de risco configurando as políticas de risco de usuário e de entrada da proteção de identidade.  

Para entender como a proteção de identidade detecta riscos, há dois conceitos importantes: risco de usuário e risco de entrada. O risco de entrada reflete a probabilidade de que uma determinada solicitação de autenticação não seja autorizada pelo proprietário da identidade. Há dois tipos de riscos de entrada: em tempo real e total. O risco de entrada em tempo real é detectado no momento da tentativa de entrada fornecida (como entradas de endereços IP anônimos). O risco de entrada total é a agregação de riscos de entrada em tempo real detectados, bem como quaisquer eventos de risco não em tempo real subsequentes associados às entradas do usuário (como viagens impossíveis). O risco do usuário reflete a probabilidade geral de que um ator inadequado tenha comprometido uma determinada identidade. O risco do usuário contém todas as atividades de risco de um determinado usuário, incluindo:

- Risco de entrada em tempo real
- Risco de entrada subsequente
- Detecções de usuário arriscadas.   

 ![Fluxo](./media/overview-v2/01.png)

O fluxo de linha de base para a detecção de risco da proteção de identidade e a resposta para qualquer entrada fornecida é resumido no gráfico acima.  

## <a name="common-scenarios"></a>Cenários comuns 

Vejamos o exemplo de um funcionário da contoso. 

1. Um funcionário tenta entrar no Exchange Online por meio do navegador Tor. No momento da entrada, o Azure AD detecta eventos de risco em tempo real. 
2. O Azure AD detecta que o funcionário está entrando a partir de um endereço IP anônimo, disparando um nível de risco de entrada médio. 
3. O funcionário é desafiado por um prompt de MFA, pois o administrador de ti da Contoso configurou a política de acesso condicional de risco de entrada da proteção de identidade. A política requer MFA para um risco de entrada de médio ou superior. 
4. O funcionário passa o prompt do MFA e acessa o Exchange Online, e o nível de risco do usuário não é alterado. 

O que aconteceu em segundo plano? A tentativa de entrada do navegador Tor disparou um risco de entrada em tempo real no Azure AD para o endereço IP anônimo. Como o Azure AD processou a solicitação, ela aplicou a política de risco de entrada configurada na proteção de identidade porque o nível de risco de entrada do funcionário atendeu ao limite (médio). Como o funcionário se registrou anteriormente para MFA, ele conseguiu responder e passar o desafio de MFA. Sua capacidade de passar com êxito o desafio de MFA sinalizado para o Azure AD que era provavelmente o proprietário da identidade legítima, e o nível de risco do usuário não aumenta. 

Mas e se o funcionário não fosse aquele que está tentando entrar? 

1. Um ator mal-intencionado com as credenciais do funcionário tenta entrar em sua conta do Exchange Online por meio do navegador Tor, pois eles estão tentando ocultar seu endereço IP. 
2. O Azure AD detecta que a tentativa de entrada é de um endereço IP anônimo, disparando um risco de entrada em tempo real. 
3. O ator mal-intencionado é desafiado por um prompt de MFA, pois o administrador de ti da Contoso configurou a política de acesso condicional de risco de entrada da proteção de identidade para exigir MFA quando o risco de entrada é médio ou superior. 
4. O ator mal-intencionado falha no desafio de MFA e não pode acessar a conta do Exchange Online do funcionário. 
5. O prompt de MFA com falha disparou um evento de risco a ser registrado, elevando o risco do usuário para logons futuros. 

Agora que um ator mal-intencionado tentou acessar a conta do Sarah, vamos ver o que acontece na próxima vez que o funcionário tentar entrar. 

1. O funcionário tenta entrar no Exchange Online por meio do Outlook. No momento da entrada, o Azure AD detecta eventos de risco em tempo real, bem como qualquer risco de usuário anterior. 
2. O Azure AD não detecta nenhum risco de entrada em tempo real, mas detecta alto risco de usuário devido à atividade arriscada anterior nos cenários anteriores.  
3. O funcionário é desafiado por um prompt de redefinição de senha, pois o administrador de ti da Contoso configurou a política de risco do usuário do Identity Protection para exigir alteração de senha quando um usuário com logs de alto risco no. 
4. Como o funcionário está registrado para SSPR e MFA, ele redefiniu sua senha com êxito. 
5. Ao redefinir a senha, as credenciais do funcionário não são mais comprometidas e sua identidade retorna para um estado seguro. 
6. Os eventos de risco anteriores do funcionário são resolvidos e o nível de risco do usuário é redefinido automaticamente como uma resposta para atenuar o comprometimento das credenciais. 

## <a name="how-do-i-configure-identity-protection"></a>Como fazer configurar a proteção de identidade? 

Para começar a usar a proteção de identidade, primeiro configure uma política de risco de usuário e uma política de risco de entrada. Depois que essas políticas são configuradas e aplicadas a um grupo de teste, você pode simular eventos de risco para entender como a proteção de identidade responderá em seu ambiente. Os guias de início rápido abaixo fornecem instruções sobre como configurar as políticas e os testes mencionados acima em seu ambiente. 

A proteção de identidade dá suporte a três funções no Azure AD para balancear as atividades de gerenciamento em relação à sua implantação: 

| Role | Pode fazer | Não é possível fazer |
| --- | --- | --- |
| Administrador Global | Acesso completo à proteção de identidade, proteção de identidade integrada | |
| Administrador de segurança | Acesso completo à proteção de identidade | Integração da proteção de identidade, redefinição de senhas para um usuário |
| Leitor de segurança | Acesso somente leitura à proteção de identidade | Integração da proteção de identidade, correção de usuários, configuração de políticas, redefinição de senhas| 

Para obter mais informações, consulte [atribuindo funções de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)
 
## <a name="licensing"></a>Licenciamento

>[!NOTE]
> Durante a visualização pública da proteção de identidade (atualizada), somente os clientes Azure AD Premium P2 terão acesso ao relatório de usuários arriscados e de entradas arriscadas.

| Funcionalidade | Detalhes | Azure AD Premium P2 | Azure AD Premium P1 | Azure AD Basic/gratuito |
| --- | --- | --- | --- | --- |
| Políticas de risco | Política de risco do usuário (por meio da proteção de identidade) | Sim | Não | Não |
| Políticas de risco | Política de risco de entrada (por meio da proteção de identidade ou acesso condicional) | Sim | Não | Não |
| Relatórios de segurança | Descrição geral | Sim | Não | Não |
| Relatórios de segurança | Usuários arriscados | Acesso total | Informações limitadas | Informações limitadas |
| Relatórios de segurança | Inícios de sessão de risco | Acesso total | Informações limitadas | Informações limitadas |
| Relatórios de segurança | Detecções de risco | Acesso total | Informações limitadas | Não |
| Notificações | Alertas de usuários em risco detectados | Sim | Não | Não |
| Notificações | Resumo semanal | Sim | Não | Não |
| | Política de registo MFA | Sim | Não | Não |

## <a name="next-steps"></a>Passos Seguintes 

Para começar a usar a proteção de identidade, consulte [Configurar política de risco de entrada](quickstart-sign-in-risk-policy.md). 
