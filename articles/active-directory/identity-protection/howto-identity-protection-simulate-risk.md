---
title: Simulação de deteções de riscos na Proteção de Identidade AZure AD
description: Saiba como simular deteções de risco na Proteção de Identidade
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7eafeaf59757fcda978fa89b4bc2f9882b769e48
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835905"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>Simulação de deteções de riscos na Proteção de Identidade

Os administradores podem querer simular riscos no seu ambiente, a fim de realizar os seguintes itens:

- Povoar dados no ambiente de Proteção de Identidade simulando deteções de risco e vulnerabilidades.
- Crie políticas de acesso condicional baseadas no risco e teste o impacto destas políticas.

Este artigo fornece-lhe passos para simular os seguintes tipos de deteção de risco:

- Endereço IP anónimo (fácil)
- Propriedades de inscrição desconhecidas (moderadas)
- Viagem atípica (difícil)

Outras deteções de risco não podem ser simuladas de forma segura.

Mais informações sobre cada deteção de risco podem ser encontradas no artigo, [O que é o risco](concept-identity-protection-risks.md).

## <a name="anonymous-ip-address"></a>Endereço IP anónimo

Completar o seguinte procedimento requer que utilize:

- O [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) para simular endereços IP anónimos. Poderá ser necessário utilizar uma máquina virtual se a sua organização restringir a utilização do navegador Tor.
- Uma conta de teste que ainda não está registada para autenticação multi-factor Azure AD.

**Para simular uma sindução de um IP anónimo, execute os seguintes passos:**

1. Utilizando o [Navegador Tor,](https://www.torproject.org/projects/torbrowser.html.en)navegue para [https://myapps.microsoft.com](https://myapps.microsoft.com) .   
2. Introduza as credenciais da conta que pretende aparecer **nos relatórios de entradas anónimas de endereços IP.**

A inscrição aparece no painel de proteção de identidade dentro de 10 a 15 minutos. 

## <a name="unfamiliar-sign-in-properties"></a>Propriedades de inícios de sessão desconhecidos

Para simular locais desconhecidos, tem de iniciar scontabilidade a partir de um local e o dispositivo que a sua conta de teste não assinou antes.

O procedimento abaixo utiliza um recém-criado:

- Ligação VPN, para simular nova localização.
- Máquina virtual, para simular um novo dispositivo.

O preenchimento do seguinte procedimento requer que utilize uma conta de utilizador que tenha:

- Pelo menos 30 dias de história.
- Autenticação multi-factor Azure AD ativada.

**Para simular uma inscrição a partir de um local desconhecido, execute os seguintes passos:**

1. Ao iniciar sessão com a sua conta de teste, falhe no desafio de autenticação multi-factor (MFA) ao não passar no desafio MFA.
2. Utilizando a sua nova VPN, navegue para [https://myapps.microsoft.com](https://myapps.microsoft.com) e introduza as credenciais da sua conta de teste.

A inscrição aparece no painel de proteção de identidade dentro de 10 a 15 minutos.

## <a name="atypical-travel"></a>Viagem atípica

Simular a condição atípica de viagem é difícil porque o algoritmo usa machine learning para eliminar falsos positivos, tais como viagens atípicas de dispositivos familiares, ou inscrições de VPNs que são usadas por outros utilizadores no diretório. Além disso, o algoritmo requer um histórico de login de 14 dias e 10 de início de sessão do utilizador antes de começar a gerar deteções de risco. Devido aos complexos modelos de aprendizagem automática e acima das regras, existe a possibilidade de que os seguintes passos não conduzam a uma deteção de riscos. É melhor replicar estes passos para várias contas AD Azure para simular esta deteção.

**Para simular uma deteção atípica de risco de viagem, execute os seguintes passos:**

1. Utilizando o seu navegador padrão, navegue para [https://myapps.microsoft.com](https://myapps.microsoft.com) .  
2. Introduza as credenciais da conta para a que pretende gerar uma deteção atípica de risco de viagem.
3. Mude o seu agente de utilizador. Pode alterar o agente do utilizador no Microsoft Edge a partir de Ferramentas de Desenvolvimento (F12).
4. Altere o seu endereço IP. Pode alterar o seu endereço IP utilizando um add-on VPN, um Add-on Tor ou criando uma nova máquina virtual em Azure num centro de dados diferente.
5. Inscreva-se para [https://myapps.microsoft.com](https://myapps.microsoft.com) utilizar as mesmas credenciais de antes e poucos minutos após a sinsindução anterior.

A inscrição aparece no painel de proteção de identidade dentro de 2-4 horas.

## <a name="testing-risk-policies"></a>Testar políticas de risco

Esta secção fornece-lhe medidas para testar o utilizador e as políticas de risco de inscrição criadas no artigo, [Como: Configurar e permitir políticas](howto-identity-protection-configure-risk-policies.md)de risco .

### <a name="user-risk-policy"></a>Política de risco do utilizador

Para testar uma política de segurança de risco do utilizador, execute os seguintes passos:

1. Navegue até ao [portal Azure.](https://portal.azure.com)
1. Consulte a Visão Geral de Segurança **do Diretório Ativo Azure**  >  **Security**  >  **Overview**.
1. Selecione **Configurar a política de risco do utilizador**.
   1. Em **Atribuições**
      1. **Utilizadores** - Escolha **todos os utilizadores** ou **selecione indivíduos e grupos** se limitar o seu lançamento.
         1. Opcionalmente pode optar por excluir os utilizadores da apólice.
      1. **Condições**  -  **Risco de utilizador** A recomendação da Microsoft é definir esta opção para **High**.
   1. Sob **controlo**
      1. **Acesso** - A recomendação da Microsoft é **permitir o acesso** e exigir a alteração da **palavra-passe.**
   1. **Fazer cumprir a política**  -  **Fora**
   1. **Guardar** - Esta ação irá devolvê-lo à página **geral.**
1. Elevar o risco de utilização de uma conta de teste simulando, por exemplo, uma das deteções de risco algumas vezes.
1. Aguarde alguns minutos e, em seguida, verifique se o risco aumentou para o seu utilizador. Caso contrário, simular mais deteções de risco para o utilizador.
1. Volte à sua política de risco e desemplique a **política de** **segurança** e **poupe** a sua mudança de política.
1. Agora pode testar o Acesso Condicional baseado no risco do utilizador, assinando usando um utilizador com um nível de risco elevado.

### <a name="sign-in-risk-security-policy"></a>Política de segurança de risco de inscrição

Para testar um sinal na política de risco, execute os seguintes passos:

1. Navegue até ao [portal Azure.](https://portal.azure.com)
1. Consulte a Visão Geral de Segurança **do Diretório Ativo Azure**  >  **Security**  >  **Overview**.
1. Selecione **Configurar a política de risco de inscrição**.
   1. Em **Atribuições**
      1. **Utilizadores** - Escolha **todos os utilizadores** ou **selecione indivíduos e grupos** se limitar o seu lançamento.
         1. Opcionalmente pode optar por excluir os utilizadores da apólice.
      1. **Condições**  -  **Risco de inscrição** A recomendação da Microsoft é definir esta opção para **Medium e acima**.
   1. Sob **controlo**
      1. **Acesso** - A recomendação da Microsoft é **permitir o acesso** e exigir a **autenticação de vários fatores.**
   1. **Fazer cumprir a política**  -  **Em**
   1. **Guardar** - Esta ação irá devolvê-lo à página **geral.**
1. Agora pode testar o Acesso Condicional baseado em Risco de Acesso baseado em Sinais, assinando usando uma sessão de risco (por exemplo, utilizando o navegador Tor). 

## <a name="next-steps"></a>Passos seguintes

- [O que é o risco?](concept-identity-protection-risks.md)

- [Como: Configurar e permitir políticas de risco](howto-identity-protection-configure-risk-policies.md)

- [Proteção de Identidade do Diretório Ativo Azure](overview-identity-protection.md)
