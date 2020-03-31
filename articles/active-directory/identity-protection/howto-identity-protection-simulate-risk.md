---
title: Simulação de deteções de risco na Proteção de Identidade da AD Azure
description: Saiba como simular deteções de risco na Proteção de Identidade
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e50e406e74c0b78f41830c4ea7c5b10830002ed3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72886938"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>Simulação de deteções de risco na Proteção de Identidade

Os administradores podem querer simular riscos no seu ambiente, a fim de realizar os seguintes itens:

- Povoeia dados no ambiente de Proteção de Identidade simulando deteções e vulnerabilidades de risco.
- Criar políticas de acesso condicional baseadas no risco e testar o impacto destas políticas.

Este artigo fornece-lhe passos para simular os seguintes tipos de deteção de risco:

- Endereço IP anónimo (fácil)
- Propriedades de inscrição desconhecidas (moderada)
- Viagem atípica (difícil)

Outras deteções de risco não podem ser simuladas de forma segura.

Mais informações sobre cada deteção de risco podem ser encontradas no artigo, [O que é risco.](concept-identity-protection-risks.md)

## <a name="anonymous-ip-address"></a>Endereço IP anónimo

A conclusão do seguinte procedimento requer que utilize:

- O [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) para simular endereços IP anónimos. Poderá precisar de utilizar uma máquina virtual se a sua organização restringir o uso do navegador Tor.
- Uma conta de teste que ainda não está registada para autenticação multi-factor Azure.

**Para simular um sessão a partir de um IP anónimo, execute os seguintes passos:**

1. Utilizando o [Tor Browser,](https://www.torproject.org/projects/torbrowser.html.en)navegue para [https://myapps.microsoft.com](https://myapps.microsoft.com).   
2. Introduza as credenciais da conta que pretende aparecer nos **Sign-ins a partir de endereços IP anónimos.**

O sinal de inscrição aparece no painel de proteção de identidade dentro de 10 a 15 minutos. 

## <a name="unfamiliar-sign-in-properties"></a>Propriedades de inscrição desconhecidas

Para simular locais desconhecidos, tem de iniciar sessão a partir de um local e dispositivo que a sua conta de teste não assinou antes.

O procedimento abaixo utiliza um recém-criado:

- Ligação VPN, para simular nova localização.
- Máquina virtual, para simular um novo dispositivo.

A conclusão do seguinte procedimento requer que utilize uma conta de utilizador que tenha:

- Pelo menos um registo de 30 dias.
- Autenticação azure multi-factor ativada.

**Para simular um sessão de um local desconhecido, execute os seguintes passos:**

1. Ao iniciar sessão com a sua conta de teste, falhe no desafio de autenticação de vários fatores (MFA) ao não passar o desafio mfa.
2. Utilizando a sua nova [https://myapps.microsoft.com](https://myapps.microsoft.com) VPN, navegue para e introduza as credenciais da sua conta de teste.

O sinal de inscrição aparece no painel de proteção de identidade dentro de 10 a 15 minutos.

## <a name="atypical-travel"></a>Viagem atípica

Simular a condição de viagem atípica é difícil porque o algoritmo usa machine learning para eliminar falsos positivos, como viagens atípicas de dispositivos familiares, ou inscrições de VPNs que são usadas por outros utilizadores no diretório. Além disso, o algoritmo requer um histórico de login de 14 dias e 10 logins do utilizador antes de começar a gerar deteções de risco. Devido aos complexos modelos de aprendizagem automática e acima das regras, existe a possibilidade de que os seguintes passos não conduzam a uma deteção de riscos. É melhor replicar estes passos para várias contas da AD Azure para simular esta deteção.

**Para simular uma deteção atípica do risco de viagem, execute os seguintes passos:**

1. Usando o seu navegador [https://myapps.microsoft.com](https://myapps.microsoft.com)padrão, navegue para .  
2. Introduza as credenciais da conta para a que pretende gerar uma deteção atípica de risco de viagem.
3. Mude o seu agente de utilizador. Pode alterar o agente de utilizador no Microsoft Edge a partir de Ferramentas de Desenvolvimento (F12).
4. Altere o seu endereço IP. Pode alterar o seu endereço IP utilizando uma VPN, um addon Tor ou criando uma nova máquina virtual em Azure num centro de dados diferente.
5. Iniciar sessão [https://myapps.microsoft.com](https://myapps.microsoft.com) para utilizar as mesmas credenciais de antes e dentro de alguns minutos após o anterior inessão.

O sinal de inscrição aparece no painel de proteção de identidade dentro de 2 a 4 horas.

## <a name="testing-risk-policies"></a>Testar políticas de risco

Esta secção fornece-lhe passos para testar o utilizador e as políticas de risco de entrada criadas no artigo, [Como Configurar e ativar políticas](howto-identity-protection-configure-risk-policies.md)de risco .

### <a name="user-risk-policy"></a>Política de risco do utilizador

Para testar uma política de segurança de risco do utilizador, execute os seguintes passos:

1. Navegue para o [portal Azure.](https://portal.azure.com)
1. Navegue na**visão geral**de**segurança** > do **diretório** > ativo do Azure.
1. **Selecione Configure a política**de risco do utilizador .
   1. Em **Atribuições**
      1. **Utilizadores** - Escolha **todos os utilizadores** ou **Selecione indivíduos e grupos** se limitar o seu lançamento.
         1. Opcionalmente pode optar por excluir os utilizadores da apólice.
      1. **Condições** - **O risco** do utilizador a recomendação da Microsoft é definir esta opção para **High**.
   1. Sob **controlos**
      1. **Acesso** - A recomendação da Microsoft é **permitir o acesso** e exigir a alteração da **palavra-passe.**
   1. **Impor a política** - **fora**
   1. **Save** - Esta ação irá devolvê-lo à página **de visão geral.**
1. Elevar o risco de utilizador de uma conta de teste, por exemplo, simulando uma das deteções de risco algumas vezes.
1. Aguarde alguns minutos e verifique se o risco aumentou para o utilizador. Caso contrário, simule mais deteções de risco para o utilizador.
1. Volte à sua política de risco e desemque a Política de **Aplicação** para **On** e **Salve** a sua mudança de política.
1. Agora pode testar o Acesso Condicional baseado no risco do utilizador, assinando um utilizador com um nível de risco elevado.

### <a name="sign-in-risk-security-policy"></a>Política de segurança de risco de insarisco

Para testar um sinal na política de risco, execute os seguintes passos:

1. Navegue para o [portal Azure.](https://portal.azure.com)
1. Navegue na**visão geral**de**segurança** > do **diretório** > ativo do Azure.
1. **Selecione Configure política de risco de entrada**.
   1. Em **Atribuições**
      1. **Utilizadores** - Escolha **todos os utilizadores** ou **Selecione indivíduos e grupos** se limitar o seu lançamento.
         1. Opcionalmente pode optar por excluir os utilizadores da apólice.
      1. **Condições** - **O risco de inscrição** a recomendação da Microsoft é definir esta opção para Médio e **superior**.
   1. Sob **controlos**
      1. **Acesso** - A recomendação da Microsoft é **permitir o acesso** e exigir a **autenticação de vários fatores.**
   1. **Impor a política** - **On**
   1. **Save** - Esta ação irá devolvê-lo à página **de visão geral.**
1. Agora pode testar o Acesso Condicional baseado em Risco de Acesso baseado em Risco, assinando uma sessão de risco (por exemplo, utilizando o navegador Tor). 

## <a name="next-steps"></a>Passos seguintes

- [O que é o risco?](concept-identity-protection-risks.md)

- [Como: Configurar e ativar políticas de risco](howto-identity-protection-configure-risk-policies.md)

- [Proteção de Identidade do Diretório Ativo Azure](overview-identity-protection.md)
