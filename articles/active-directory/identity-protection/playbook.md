---
title: Guia estratégico de Azure Active Directory Identity Protection | Microsoft Docs
description: Saiba como Azure AD Identity Protection permite limitar a capacidade de um invasor explorar uma identidade ou um dispositivo comprometido e proteger uma identidade ou um dispositivo que foi suspeito ou comprometido anteriormente.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 273a6aca2050676650b955ec078b47b2ffcfe319
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333922"
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Guia estratégico de Azure Active Directory Identity Protection

Este guia estratégico ajuda você a:

* Popular dados no ambiente de proteção de identidade por meio da simulação de eventos e vulnerabilidades de risco
* Configurar políticas de acesso condicional com base em risco e testar o impacto dessas políticas

## <a name="simulating-risk-events"></a>Simulando eventos de risco

Esta seção fornece etapas para simular os seguintes tipos de evento de risco:

* Entradas de endereços IP anônimos (fácil)
* Entradas de locais desconhecidos (moderado)
* Viagem impossível a locais atípicoss (difícil)

Outros eventos de risco não podem ser simulados de maneira segura.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Inícios de sessão de endereços IP anónimos

Para obter mais informações sobre esse evento de risco, consulte [entradas de endereços IP anônimos](../reports-monitoring/concept-risk-events.md#sign-ins-from-anonymous-ip-addresses). 

A conclusão do procedimento a seguir exige que você use:

- O [navegador Tor](https://www.torproject.org/projects/torbrowser.html.en) para simular endereços IP anônimos. Talvez seja necessário usar uma máquina virtual se sua organização restringir o uso do navegador Tor.
- Uma conta de teste que ainda não está registrada para autenticação multifator.

**Para simular uma entrada de um IP anônimo, execute as seguintes etapas**:

1. Usando o [navegador Tor](https://www.torproject.org/projects/torbrowser.html.en), navegue até [https://myapps.microsoft.com](https://myapps.microsoft.com).   
2. Insira as credenciais da conta que você deseja exibir no relatório **entradas de endereços IP anônimos** .

A entrada aparece no painel do Identity Protection dentro de 10-15 minutos. 

### <a name="sign-ins-from-unfamiliar-locations"></a>Inícios de sessão de localizações desconhecidas

Para obter mais informações sobre esse evento de risco, consulte [entradas de locais](../reports-monitoring/concept-risk-events.md#sign-in-from-unfamiliar-locations)desconhecidos. 

Para simular locais desconhecidos, você precisa entrar de um local e dispositivo no qual sua conta de teste não tenha se conectado antes.

O procedimento a seguir usa um recém-criado:

- Conexão VPN, para simular novo local.
- Máquina virtual, para simular um novo dispositivo.

A conclusão do procedimento a seguir exige que você use uma conta de usuário que tenha:

- Pelo menos um histórico de entrada de 30 dias.
- Autenticação multifator habilitada.

**Para simular uma entrada de um local desconhecido, execute as seguintes etapas**:

1. Ao entrar com sua conta de teste, reprovará o desafio de MFA não passando o desafio de MFA.
2. Usando sua nova VPN, navegue até [https://myapps.microsoft.com](https://myapps.microsoft.com) e insira as credenciais da sua conta de teste.

A entrada aparece no painel do Identity Protection dentro de 10-15 minutos.

### <a name="impossible-travel-to-atypical-location"></a>Viagem impossível para o local atípicos

Para obter mais informações sobre esse evento de risco, consulte [viagem impossível para o local atípicos](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations). 

A simulação da condição de viagem impossível é difícil porque o algoritmo usa o aprendizado de máquina para eliminar falsos positivos, como viagens impossíveis de dispositivos conhecidos ou entradas de VPNs que são usadas por outros usuários no diretório. Além disso, o algoritmo requer um histórico de entrada de 14 dias e 10 logons do usuário antes de começar a gerar eventos de risco. Devido aos modelos complexos de aprendizado de máquina e às regras acima, há a possibilidade de que as etapas a seguir não levem a um evento de risco. Talvez você queira replicar essas etapas para várias contas do Azure AD para publicar este evento de risco.

**Para simular uma viagem impossível para o local atípicos, execute as seguintes etapas**:

1. Usando seu navegador padrão, navegue até [https://myapps.microsoft.com](https://myapps.microsoft.com).  
2. Insira as credenciais da conta para a qual você deseja gerar um evento de risco de viagem impossível.
3. Altere o agente do usuário. Você pode alterar o agente do usuário no Internet Explorer de Ferramentas para Desenvolvedores ou alterar o agente do usuário no Firefox ou no Chrome usando um complemento de seletor de agente do usuário.
4. Altere seu endereço IP. Você pode alterar seu endereço IP usando uma VPN, um complemento Tor ou girando um novo computador no Azure em um data center diferente.
5. Entre no [https://myapps.microsoft.com](https://myapps.microsoft.com) usando as mesmas credenciais de antes e em alguns minutos após a entrada anterior.

A entrada aparece no painel do Identity Protection dentro de 2-4 horas.

## <a name="simulating-vulnerabilities"></a>Simulando vulnerabilidades
Vulnerabilidades são pontos fracos em um ambiente do Azure AD que podem ser explorados por um ator inadequado. Atualmente, três tipos de vulnerabilidades são exibidos em Azure AD Identity Protection que aproveitam outros recursos do Azure AD. Essas vulnerabilidades serão exibidas no painel de proteção de identidade automaticamente depois que esses recursos forem configurados.

* [Autenticação](../authentication/multi-factor-authentication.md) multifator do Azure AD
* [Cloud Discovery](https://docs.microsoft.com/cloud-app-security/)do Azure AD.
* [Privileged Identity Management](../privileged-identity-management/pim-configure.md)do Azure AD. 

## <a name="testing-security-policies"></a>Testando políticas de segurança

Esta seção fornece etapas para testar o risco do usuário e a política de segurança de risco de entrada.

### <a name="user-risk-security-policy"></a>Política de segurança de risco do usuário

Para obter mais informações, veja [Como configurar a política de risco de utilizador](howto-user-risk-policy.md).

![Risco do usuário](./media/playbook/02.png "Guia estratégico")

**Para testar uma política de segurança de risco do usuário, execute as seguintes etapas**:

1. Entre no [https://portal.azure.com](https://portal.azure.com) com as credenciais de administrador global para seu locatário.
2. Navegue até **identidade proteção**. 
3. Na página **Azure ad Identity Protection** , clique em **política de risco do usuário**.
4. Na seção **atribuições** , selecione os usuários desejados (e grupos) e o nível de risco do usuário.

    ![Risco do usuário](./media/playbook/03.png "Guia estratégico")

5. Na seção controles, selecione o controle de acesso desejado (por exemplo, exigir alteração de senha).
5. Como **impor política**, selecione **desativado**.
6. Eleve o risco do usuário de uma conta de teste, por exemplo, simulando um dos eventos de risco algumas vezes.
7. Aguarde alguns minutos e verifique se o nível do usuário para o seu usuário é médio. Caso contrário, simule mais eventos de risco para o usuário.
8. Como **impor política**, selecione **ativado**.
9. Agora você pode testar o acesso condicional baseado em risco do usuário entrando usando um usuário com um nível de risco elevado.

### <a name="sign-in-risk-security-policy"></a>Política de segurança de risco de entrada

Para obter mais informações, veja [Como configurar a política de risco de início de sessão](howto-sign-in-risk-policy.md).

![Risco de entrada](./media/playbook/01.png "Guia estratégico")

**Para testar uma política de risco de entrada, execute as seguintes etapas:**

1. Entre no [https://portal.azure.com](https://portal.azure.com) com as credenciais de administrador global para seu locatário.
2. Navegue até **Azure ad Identity Protection**.
3. Na página principal do **Azure ad Identity Protection** , clique em **política de risco de entrada**. 
4. Na seção **atribuições** , selecione os usuários desejados (e grupos) e o nível de risco de entrada.

    ![Risco de entrada](./media/playbook/04.png "Guia estratégico")

5. Na seção **controles** , selecione o controle de acesso desejado (por exemplo, **exigir autenticação**multifator). 
6. Como **impor política**, selecione **ativado**.
7. Clique em **Guardar**.
8. Agora você pode testar o acesso condicional com base em risco de entrada, entrando usando uma sessão arriscada (por exemplo, usando o navegador Tor). 

## <a name="see-also"></a>Consulte também

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
