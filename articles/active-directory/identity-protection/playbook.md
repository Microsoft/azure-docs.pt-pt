---
title: Playbook do Azure Active Directory Identity Protection | Documentos da Microsoft
description: Saiba como o Azure AD Identity Protection permite-lhe limitar a capacidade de um atacante explore uma identidade comprometida ou dispositivo e para proteger uma identidade ou um dispositivo que foi anteriormente suspeita ou conhecido por ser comprometido.
services: active-directory
keywords: proteção de identidade do Azure Active Directory, da cloud discovery, gerenciamento de aplicativos, a segurança, a risco, a nível de risco, a vulnerabilidade, a política de segurança
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: 60836abf-f0e9-459d-b344-8e06b8341d25
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: markvi
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc2f2f90029918955f00771acaac10b3cf5f4416
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56868277"
---
# <a name="azure-active-directory-identity-protection-playbook"></a>Playbook do Azure Active Directory Identity Protection

Este playbook ajuda-o a:

* Preencher os dados no ambiente do Identity Protection ao simular eventos de risco e vulnerabilidades
* Definir políticas de acesso condicional baseado no risco e teste o impacto destas políticas


## <a name="simulating-risk-events"></a>Simular eventos de risco

Esta secção fornece passos para simular os seguintes tipos de eventos de risco:

* Inícios de sessão de endereços IP anónimos (simples)
* Inícios de sessão de localizações desconhecidas (moderado)
* Deslocação impossível para localizações atípicas (difícil)

Não podem ser simulados outros eventos de risco de forma segura.

### <a name="sign-ins-from-anonymous-ip-addresses"></a>Inícios de sessão de endereços IP anónimos

Para obter mais informações sobre este evento de risco, consulte [inícios de sessão de endereços IP anónimos](../reports-monitoring/concept-risk-events.md#sign-ins-from-anonymous-ip-addresses). 

Concluir o procedimento seguinte requer a utilização:

- O [Browser de Tor](https://www.torproject.org/projects/torbrowser.html.en) para simular a endereços IP anónimos. Poderá ter de utilizar uma máquina virtual, se sua organização restringe a utilização do browser de Tor.
- Uma conta de teste que ainda não está registrada na autenticação multifator.

**Para simular um início de sessão de um IP anónimo, execute os seguintes passos**:

1. Utilizar o [Browser de Tor](https://www.torproject.org/projects/torbrowser.html.en), navegue até à [ https://myapps.microsoft.com ](https://myapps.microsoft.com).   
2. Introduza as credenciais da conta que pretende ver apresentado na **inícios de sessão de endereços IP anónimos** relatório.

O início de sessão aparece no dashboard do Identity Protection dentro de 10 a 15 minutos. 

### <a name="sign-ins-from-unfamiliar-locations"></a>Inícios de sessão de localizações desconhecidas

Para obter mais informações sobre este evento de risco, consulte [inícios de sessão de localizações desconhecidas](../reports-monitoring/concept-risk-events.md#sign-in-from-unfamiliar-locations). 

Para simular localizações desconhecidas, terá de iniciar sessão a partir de uma localização e a sua conta de teste tem não iniciou sessão a partir antes do dispositivo.

O procedimento a seguir utiliza um recentemente criado:

- Ligação de VPN, para simular a nova localização.

- Máquina virtual, para simular um dispositivo novo.

Concluir o procedimento seguinte requer a utilização de uma conta de utilizador que tem:

- Pelo menos, 30 dias-histórico de sessão.
- Autenticação multifator ativada.


**Para simular um início de sessão de uma localização desconhecida, execute os seguintes passos**:

1. Quando iniciar sessão com a sua conta de teste, efetue a ativação a submissão da MFA, não passando o desafio MFA.
2. Com a VPN novo, navegue até [ https://myapps.microsoft.com ](https://myapps.microsoft.com) e introduza as credenciais da sua conta de teste.
   

O início de sessão aparece no dashboard do Identity Protection dentro de 10 a 15 minutos.

### <a name="impossible-travel-to-atypical-location"></a>Deslocação impossível para localização atípica

Para obter mais informações sobre este evento de risco, consulte [de viagem impossível para localização atípica](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations). 

Simulando a condição de deslocação impossível é difícil, porque o algoritmo utiliza machine learning para eliminar falsos positivos, como deslocação impossível dos dispositivos familiarizados ou inícios de sessão de VPNs que são utilizados por outros utilizadores no diretório. Além disso, o algoritmo necessita um histórico de início de sessão de 14 dias e 10 inícios de sessão do utilizador, antes de ele começa a gerar eventos de risco. Devido o modelos de aprendizagem complexos e superior regras, é provável que os seguintes passos não irão causar um evento de risco. Poderá pretender replicar estes passos para várias contas do Azure AD publicar este evento de risco.


**Para simular uma deslocação impossível para localização atípica, execute os seguintes passos**:

1. Com o seu navegador padrão, navegue até [ https://myapps.microsoft.com ](https://myapps.microsoft.com).  
2. Introduza as credenciais da conta que pretende gerar um evento de risco de deslocação impossível para.
3. Altere o seu agente de utilizador. Pode alterar o agente de utilizador no Internet Explorer a partir das ferramentas de programador ou alterar o seu agente de utilizador no Firefox ou o Chrome usando um suplemento de comutador de agente do usuário.
4. Altere o seu endereço IP. Pode alterar seu endereço IP utilizando uma VPN, um complemento de Tor, ou criar uma nova máquina no Azure num centro de dados diferentes.
5. Iniciar sessão no [ https://myapps.microsoft.com ](https://myapps.microsoft.com) com as mesmas credenciais, como antes e em poucos minutos após o início de sessão-in anterior.

O início de sessão aparece no dashboard do Identity Protection dentro de 2 a 4 horas.

## <a name="simulating-vulnerabilities"></a>Simulação de vulnerabilidades
Vulnerabilidades são pontos fracos do ambiente do Azure AD que podem ser explorados por um ator indevido. Atualmente 3 tipos de vulnerabilidades aparecem no Azure AD Identity Protection, que tiram partido de outras funcionalidades do Azure AD. Essas vulnerabilidades serão exibidas no dashboard do Identity Protection automaticamente assim que estas funcionalidades são configuradas.

* O Azure AD [multi-factor Authentication](../authentication/multi-factor-authentication.md)
* O Azure AD [Cloud Discovery](https://docs.microsoft.com/cloud-app-security/).
* O Azure AD [Privileged Identity Management](../privileged-identity-management/pim-configure.md). 


## <a name="testing-security-policies"></a>Teste as políticas de segurança

Esta secção fornece passos para testar o risco de utilizador e a política de segurança de risco de início de sessão.


### <a name="user-risk-security-policy"></a>Política de segurança de risco do utilizador

Para obter mais informações, veja [Como configurar a política de risco de utilizador](howto-user-risk-policy.md).

![Risco de utilizador](./media/playbook/02.png "Playbook")


**Para testar uma política de segurança de risco do utilizador, execute os seguintes passos**:

1. Iniciar sessão no [ https://portal.azure.com ](https://portal.azure.com) com credenciais de administrador global do seu inquilino.
2. Navegue para **Identity Protection**. 
3. Sobre o **do Azure AD Identity Protection** página, clique em **política de risco do utilizador**.
4. Na **atribuições** secção, selecione os usuários desejados (e grupos) e o nível de risco do utilizador.

    ![Risco de utilizador](./media/playbook/03.png "Playbook")

5. Na secção de controles, selecione o controle de acesso desejado (por exemplo, exigir alteração de palavra-passe).
5. Como **impor a política**, selecione **desativar**.
6. Eleve o risco de utilizador de uma conta de teste por, por exemplo, simulando um dos eventos de risco algumas vezes.
7. Aguarde alguns minutos e, em seguida, certifique-se de que o nível de usuário para o utilizador é médio. Caso contrário, simular mais eventos de risco para o utilizador.
8. Como **impor a política**, selecione **no**.
9. Agora pode testar o acesso de condicional baseado no risco de utilizador ao iniciar sessão com um utilizador com um nível de risco elevado.
    
    

### <a name="sign-in-risk-security-policy"></a>Política de segurança de risco de início de sessão

Para obter mais informações, veja [Como configurar a política de risco de início de sessão](howto-sign-in-risk-policy.md).

![Início de sessão de risco](./media/playbook/01.png "Playbook")


**Para testar um início de sessão na política de risco, execute os seguintes passos:**

1. Iniciar sessão no [ https://portal.azure.com ](https://portal.azure.com) com credenciais de administrador global do seu inquilino.

2. Navegue para **do Azure AD Identity Protection**.

3. No principal **do Azure AD Identity Protection** página, clique em **política de risco de início de sessão**. 

4. Na **atribuições** secção, selecione os usuários desejados (e grupos) e início de sessão no nível de risco.

    ![Início de sessão de risco](./media/playbook/04.png "Playbook")


5. Na **controles** secção, selecione o controle de acesso desejado (por exemplo, **exigir autenticação multifator**). 

6. Como **impor a política**, selecione **no**.

7. Clique em **Guardar**.

8. Agora pode testar início de sessão baseado no risco de acesso condicional ao iniciar sessão com uma sessão arriscada (por exemplo, ao utilizar o browser de Tor). 

 




## <a name="see-also"></a>Consulte também

- [O Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

