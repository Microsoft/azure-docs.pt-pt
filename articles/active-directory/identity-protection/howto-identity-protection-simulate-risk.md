---
title: Simulando as detecções de risco no Azure AD Identity Protection
description: Saiba como simular as detecções de riscos na proteção de identidade
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
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72886938"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>Simulando detecções de riscos na proteção de identidades

Os administradores podem querer simular riscos em seu ambiente para realizar os seguintes itens:

- Preencha os dados no ambiente de proteção de identidade, simulando as detecções e as vulnerabilidades de risco.
- Configure políticas de acesso condicional com base em risco e teste o impacto dessas políticas.

Este artigo fornece etapas para simular os seguintes tipos de detecção de risco:

- Endereço IP anônimo (fácil)
- Propriedades de entrada desconhecidas (moderada)
- Viagem atípicos (difícil)

Outras detecções de risco não podem ser simuladas de maneira segura.

Mais informações sobre cada detecção de risco podem ser encontradas no artigo [o que é risco](concept-identity-protection-risks.md).

## <a name="anonymous-ip-address"></a>Endereço IP anônimo

A conclusão do procedimento a seguir exige que você use:

- O [navegador Tor](https://www.torproject.org/projects/torbrowser.html.en) para simular endereços IP anônimos. Talvez seja necessário usar uma máquina virtual se sua organização restringir o uso do navegador Tor.
- Uma conta de teste que ainda não está registrada para a autenticação multifator do Azure.

**Para simular uma entrada de um IP anônimo, execute as seguintes etapas**:

1. Usando o [navegador Tor](https://www.torproject.org/projects/torbrowser.html.en), navegue até [https://myapps.microsoft.com](https://myapps.microsoft.com).   
2. Insira as credenciais da conta que você deseja exibir no relatório **entradas de endereços IP anônimos** .

A entrada aparece no painel do Identity Protection dentro de 10-15 minutos. 

## <a name="unfamiliar-sign-in-properties"></a>Propriedades de entrada desconhecidas

Para simular locais desconhecidos, você precisa entrar de um local e dispositivo no qual sua conta de teste não tenha se conectado antes.

O procedimento a seguir usa um recém-criado:

- Conexão VPN, para simular novo local.
- Máquina virtual, para simular um novo dispositivo.

A conclusão do procedimento a seguir exige que você use uma conta de usuário que tenha:

- Pelo menos um histórico de entrada de 30 dias.
- Autenticação multifator do Azure habilitada.

**Para simular uma entrada de um local desconhecido, execute as seguintes etapas**:

1. Ao entrar com sua conta de teste, reprovar o desafio da autenticação multifator (MFA) não passando o desafio de MFA.
2. Usando sua nova VPN, navegue até [https://myapps.microsoft.com](https://myapps.microsoft.com) e insira as credenciais da sua conta de teste.

A entrada aparece no painel do Identity Protection dentro de 10-15 minutos.

## <a name="atypical-travel"></a>Viagem de atípicos

A simulação da condição de viagem atípicos é difícil porque o algoritmo usa o aprendizado de máquina para eliminar falsos positivos, como o atípicos Travel de dispositivos conhecidos, ou entradas de VPNs que são usadas por outros usuários no diretório. Além disso, o algoritmo requer um histórico de entrada de 14 dias e 10 logons do usuário antes de começar a gerar as detecções de risco. Devido aos modelos complexos de aprendizado de máquina e às regras acima, há a possibilidade de que as etapas a seguir não levem a uma detecção de risco. Talvez você queira replicar essas etapas para várias contas do Azure AD para simular essa detecção.

**Para simular uma detecção de risco do atípicos Travel, execute as seguintes etapas**:

1. Usando seu navegador padrão, navegue até [https://myapps.microsoft.com](https://myapps.microsoft.com).  
2. Insira as credenciais da conta para a qual você deseja gerar uma detecção de risco de viagem de atípicos.
3. Altere o agente do usuário. Você pode alterar o agente do usuário no Microsoft Edge de Ferramentas para Desenvolvedores (F12).
4. Altere seu endereço IP. Você pode alterar seu endereço IP usando uma VPN, um complemento do Tor ou criando uma nova máquina virtual no Azure em um data center diferente.
5. Entre em [https://myapps.microsoft.com](https://myapps.microsoft.com) usando as mesmas credenciais de antes e dentro de alguns minutos após a entrada anterior.

A entrada aparece no painel do Identity Protection dentro de 2-4 horas.

## <a name="testing-risk-policies"></a>Testando políticas de risco

Esta seção fornece etapas para testar o usuário e as políticas de risco de entrada criadas no artigo [como: configurar e habilitar políticas de risco](howto-identity-protection-configure-risk-policies.md).

### <a name="user-risk-policy"></a>Política de risco do usuário

Para testar uma política de segurança de risco do usuário, execute as seguintes etapas:

1. Navegue para o [portal do Azure](https://portal.azure.com).
1. Navegue até **Azure Active Directory** > **segurança** > **visão geral**.
1. Selecione **Configurar política de risco do usuário**.
   1. Em **atribuições**
      1. **Usuários** – escolha **todos os usuários** ou **selecione indivíduos e grupos** se limitar a distribuição.
         1. Opcionalmente, você pode optar por excluir usuários da política.
      1. **Condições** - o **risco do usuário** que a recomendação da Microsoft é definir essa opção como **alta**.
   1. Em **controles**
      1. **Acesso** -a recomendação da Microsoft é **permitir o acesso** e **exigir alteração de senha**.
   1. **Impor** - de política **desativado**
   1. **Salvar** – esta ação retornará à página **visão geral** .
1. Eleve o risco do usuário de uma conta de teste, por exemplo, simulando uma das detecções de risco algumas vezes.
1. Aguarde alguns minutos e verifique se o risco foi elevado para o usuário. Caso contrário, simule mais detecções de risco para o usuário.
1. Retorne à sua política de risco e defina **aplicar política** como **ativado** e **salve** a alteração da política.
1. Agora você pode testar o acesso condicional baseado em risco do usuário entrando usando um usuário com um nível de risco elevado.

### <a name="sign-in-risk-security-policy"></a>Política de segurança de risco de entrada

Para testar uma política de risco de entrada, execute as seguintes etapas:

1. Navegue para o [portal do Azure](https://portal.azure.com).
1. Navegue até **Azure Active Directory** > **segurança** > **visão geral**.
1. Selecione **Configurar política de risco de entrada**.
   1. Em **atribuições**
      1. **Usuários** – escolha **todos os usuários** ou **selecione indivíduos e grupos** se limitar a distribuição.
         1. Opcionalmente, você pode optar por excluir usuários da política.
      1. **Condições** - **risco de entrada** a recomendação da Microsoft é definir essa opção como **média e superior**.
   1. Em **controles**
      1. **Acesso** -a recomendação da Microsoft é **permitir o acesso** e **exigir a autenticação multifator**.
   1. **Impor** - **de política em**
   1. **Salvar** – esta ação retornará à página **visão geral** .
1. Agora você pode testar o acesso condicional com base em risco de entrada, entrando usando uma sessão arriscada (por exemplo, usando o navegador Tor). 

## <a name="next-steps"></a>Passos seguintes

- [O que é risco?](concept-identity-protection-risks.md)

- [Como configurar e habilitar políticas de risco](howto-identity-protection-configure-risk-policies.md)

- [Azure Active Directory Identity Protection](overview-identity-protection.md)
