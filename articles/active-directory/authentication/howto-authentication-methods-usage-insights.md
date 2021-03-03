---
title: Atividade dos Métodos de Autenticação - Diretório Ativo Azure
description: Visão geral dos métodos de autenticação registados e utilizados pela sua organização para iniciar sposição e efetuar o reset da palavra-passe.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: sopand
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6566ee7e744411fc3b7005938f95181e5c5ec94d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645184"
---
# <a name="authentication-methods-activity"></a>Atividade dos Métodos de Autenticação 

O novo painel de atividade dos métodos de autenticação permite aos administradores monitorizar o registo e utilização do método de autenticação em toda a sua organização. Esta capacidade de reporte fornece à sua organização os meios para entender que métodos estão a ser registados e como estão a ser utilizados.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="permissions-and-licenses"></a>Permissões e licenças

As seguintes funções podem aceder ao uso e insights:

- Leitor de Relatórios
- Leitor de Segurança
- Administrador de Segurança
- Administrador Global

 Uma licença Azure AD Premium P1 ou P2 é necessária para aceder ao uso e insights. As informações de autenticação multi-factor AD E autosserviço de autosserviço podem ser encontradas no site de preços do [Azure Ative Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Como funciona

Para aceder à utilização e insights do método de autenticação:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Clique **em Azure Ative**  >  **Directory Security**  >  **Authentication Methods**  >  **Activity**.
1. Existem dois separadores no relatório: **Registo** e **Utilização.**

   ![Visão geral da atividade dos métodos de autenticação](media/how-to-authentication-methods-usage-insights/registration-usage-tabs.png)

## <a name="registration-details"></a>Detalhes de inscrição

Pode aceder ao [**separador Registo**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) para mostrar o número de utilizadores capazes de autenticação multi-fator, autenticação sem palavras e redefinição da palavra-passe de autosserviço. 

Clique **em Utilizadores capazes de autenticação multi-factor Azure,** **Utilizadores capazes de autenticação sem palavras-passe,** ou **Utilizadores capazes de reiniciar a palavra-passe de autosserviço,** ou insights para pré-filtrar uma lista de dados de registo do utilizador.

- **Utilizadores Capazes de Autenticação Multi-Factor Azure** mostra a desagregação de utilizadores capazes de MFA em Azure AD. Os utilizadores são considerados capazes se ambos estiverem registados para um método de autenticação forte e habilitados pela política a utilizar esse método para executar MFA. Este número não reflete os utilizadores registados para MFA fora do Azure AD. 
- **Os utilizadores capazes de autenticação sem palavras-passe** mostram a avaria dos utilizadores capazes de iniciar sessão sem senha. Isto inclui utilizadores registados para FIDO2, Windows Hello for Business e acesso ao telefone sem palavras-passe com a aplicação Microsoft Authenticator. 
- **Os utilizadores capazes de reiniciar a palavra-passe de autosserviço** mostram a desagregação de utilizadores capazes de reiniciar a palavra-passe de autosserviço. Os utilizadores são considerados capazes de SSPR se ambos estiverem registados para métodos suficientes para satisfazer a política de SSPR de uma organização e habilitados a executar SSPR. 

  ![Screenshot dos utilizadores capazes de registar](media/how-to-authentication-methods-usage-insights/users-capable.png)

**Os utilizadores registados por método de autenticação** mostram quantos utilizadores estão registados para cada método de autenticação. Clique num método de autenticação para ver quais os utilizadores que estão registados para este método. 

![Screenshot dos Utilizadores Registados](media/how-to-authentication-methods-usage-insights/users-registered.png)

**O registo recente por método de autenticação** mostra o número de registos de métodos de autenticação bem sucedidos e falhados por método de autenticação. Clique num método de autenticação para ver eventos de registo recentes para este método.

![Screenshot de Recentemente Registado](media/how-to-authentication-methods-usage-insights/recently-registered.png)

## <a name="usage-details"></a>Detalhes de utilização

O relatório **de utilização** mostra quais os métodos de autenticação que os utilizadores estão a usar para iniciar sôm e redefinir as suas palavras-passe.

![Screenshot da página de uso](media/how-to-authentication-methods-usage-insights/usage-page.png)

**Os acessos por requisito de autenticação** mostram o número de inserções interativas bem sucedidas do utilizador que foram necessárias para executar a autenticação de fator único contra multi-factor em Azure AD. Isto não reflete as inscrições em que o MFA foi aplicado por um fornecedor de MFA de terceiros.

![Screenshot de ins de sinal por requisito de autenticação](media/how-to-authentication-methods-usage-insights/sign-ins-protected.png)

**As entradas por método de autenticação** mostram o número de inserções interativas do utilizador (sucesso e falha) por método de autenticação utilizado. Não inclui inscrições em que o requisito de autenticação tenha sido preenchido por uma reclamação no token.

![Screenshot de ins de sinal por método](media/how-to-authentication-methods-usage-insights/sign-ins-by-method.png)

**O número de resets de palavra-passe e desbloqueio de conta** mostra o número de alterações de palavras-passe bem sucedidas e resets de palavra-passe (self-service e por administrador) ao longo do tempo.

![Screenshot de resets e desbloqueios](media/how-to-authentication-methods-usage-insights/password-changes.png)

**A palavra-passe é reiniciada por método de autenticação** mostra o número de autenticações bem sucedidas e falhadas durante o fluxo de reset da palavra-passe por método de autenticação.

![Screenshot de Resets por método](media/how-to-authentication-methods-usage-insights/resets-by-method.png)

## <a name="user-registration-details"></a>Detalhes do registo do utilizador 

Utilizando os controlos no topo da lista, pode procurar um utilizador e filtrar a lista de utilizadores com base nas colunas mostradas.

O relatório de dados de registo mostra as seguintes informações para cada utilizador:

- Nome principal do utilizador
- Name
- MFA Capaz (Capaz, Não Capaz)
- Sem palavras-passe capaz (capaz, não capaz)
- SSPR Registado (Registado, Não Registado)
- SSPR Ativado (Ativado, Não Ativado)
- SSPR Capaz (Capaz, Não Capaz) 
- Métodos registados (Email, Mobile Phone, Alternative Mobile Phone, Office Phone, Microsoft Authenticator Push, Software One Time Passcode, FIDO2, Security Key, Questões de Segurança)

  ![Screenshot dos dados do registo do utilizador](media/how-to-authentication-methods-usage-insights/registration-details.png)

## <a name="registration-and-reset-events"></a>Eventos de registo e reposição 

**Os eventos de registo e reposição** mostram eventos de registo e reposição das últimas 24 horas, duram sete dias ou duram 30 dias, incluindo:

- Data
- Nome de utilizador
- User 
- Recurso (Registo, Reset)
- Método utilizado (notificação de aplicação, código de aplicação, chamada telefónica, chamada de escritório, chamada móvel alternativa, SMS, Email, Questões de Segurança)
- Estado (Sucesso, Fracasso)
- Razão para o fracasso (explicação)

  ![Screenshot da página de uso](media/how-to-authentication-methods-usage-insights/registration-and-reset-logs.png)

## <a name="limitations"></a>Limitações

As inscrições do Passe de Acesso Temporário (TAP) não se refletem no separador de registo do relatório, uma vez que são válidas apenas por um curto período de tempo.

## <a name="next-steps"></a>Passos seguintes

- [Trabalhar com os métodos de autenticação relatório de utilização API](/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Escolher métodos de autenticação para a sua organização](concept-authentication-methods.md)
- [Experiência de registo combinado](concept-registration-mfa-sspr-combined.md)