---
title: 'Azure AD Connect: Resolver problemas de autenticação pass-through | Documentos da Microsoft'
description: Este artigo descreve como resolver problemas de autenticação de pass-through do Azure Active Directory (Azure AD).
services: active-directory
keywords: Resolver problemas de autenticação de pass-through ligar do Azure AD, instalar o Active Directory, os componentes necessários para o Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/15/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae83cea866367fa6a6596caa683d0287bea96c29
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456178"
---
# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>Resolver problemas de autenticação de pass-through do Azure Active Directory

Este artigo ajuda-o a localizar informações sobre problemas comuns em relação à autenticação pass-through do Azure AD de resolução de problemas.

>[!IMPORTANT]
>Se está a enfrentar problemas início de sessão do utilizador com a autenticação pass-through, não desative a funcionalidade ou desinstalar agentes de autenticação pass-through sem ter uma conta de Administrador Global apenas na cloud que recorrer. Saiba mais sobre [adicionar uma conta de Administrador Global apenas na cloud](../active-directory-users-create-azure-portal.md). Efetuar este passo é fundamental e garante que não fica bloqueado fora do seu inquilino.

## <a name="general-issues"></a>Problemas gerais

### <a name="check-status-of-the-feature-and-authentication-agents"></a>Verificar o estado da funcionalidade e os agentes de autenticação

Certifique-se de que a funcionalidade de autenticação pass-through é ainda **Enabled** no seu inquilino e o estado dos agentes de autenticação mostra **Active**e não **Inactive**. Pode verificar o estado ao aceder a **do Azure AD Connect** painel no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/).

![Centro de administração do Active Directory do Azure – painel do Azure AD Connect](./media/tshoot-connect-pass-through-authentication/pta7.png)

![Centro de administração do Active Directory do Azure – painel de autenticação pass-through](./media/tshoot-connect-pass-through-authentication/pta11.png)

### <a name="user-facing-sign-in-error-messages"></a>Mensagens de erro de sessão de utilizador com acesso à

Se o utilizador não consegue iniciar sessão utilizando a autenticação pass-through, poderá ver um dos seguintes erros destinada ao utilizador no ecrã de início de sessão do Azure AD: 

|Erro|Descrição|Resolução
| --- | --- | ---
|AADSTS80001|Não é possível ligar ao Active Directory|Certifique-se de que os servidores de agente são membros da mesma floresta do AD os utilizadores cujas palavras-passe tem de ser validado e podem ligar ao Active Directory.  
|AADSTS8002|Tempo limite excedido ao ligar ao Active Directory|Verifique para se certificar de que o Active Directory está disponível e está a responder aos pedidos dos agentes.
|AADSTS80004|O nome de utilizador passado para o agente não era válido|Certifique-se de que o utilizador está a tentar iniciar sessão com o nome de utilizador certo.
|AADSTS80005|Validação encontrou WebException imprevisível|Um erro transitório. Repita o pedido. Se continuar a falhar, contacte o suporte da Microsoft.
|AADSTS80007|Ocorreu um erro ao comunicar com o Active Directory|Verifique os registos do agente para obter mais informações e certifique-se de que do Active Directory está a funcionar conforme esperado.

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Motivos das falhas de início de sessão no Centro de administração do Azure Active Directory (necessita de licença Premium)

Se o seu inquilino tem uma licença do Azure AD Premium associada a ele, também pode ver o [relatório de atividade de início de sessão](../reports-monitoring/concept-sign-ins.md) sobre o [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/).

![Centro de administração do Active Directory do Azure – relatório de inícios de sessão](./media/tshoot-connect-pass-through-authentication/pta4.png)

Navegue para **do Azure Active Directory** -> **inícios de sessão** no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com/) e clique em atividades de início de sessão de um utilizador específico. Procure o **código de erro de início de sessão** campo. Mapear o valor deste campo a um motivo da falha e a resolução utilizando a seguinte tabela:

|Código de erro de início de sessão|Motivo da falha de início de sessão|Resolução
| --- | --- | ---
| 50144 | A palavra-passe do Active Directory do utilizador expirou. | Redefinir a senha do usuário no Active Directory no local.
| 80001 | Não existe nenhum Agente de Autenticação disponível. | Instale e Registre um agente de autenticação.
| 80002 | O pedido de validação da palavra-passe do Agente de Autenticação atingiu o tempo limite. | Verifique se o Active Directory está acessível a partir do agente de autenticação.
| 80003 | O Agente de Autenticação recebeu uma resposta inválida. | Se o problema possa ser reproduzido consistentemente em vários usuários, verifique a configuração do Active Directory.
| 80004 | Foi utilizado um Nome Principal de Utilizador (UPN) no pedido de início de sessão. | Pedir ao utilizador para iniciar sessão com o nome de utilizador correto.
| 80005 | Agente de autenticação: Ocorreu um erro. | Erro transitório. Tente novamente mais tarde.
| 80007 | O Agente de Autenticação não se consegue ligar ao Active Directory. | Verifique se o Active Directory está acessível a partir do agente de autenticação.
| 80010 | O Agente de Autenticação não conseguiu desencriptar a palavra-passe. | Se o problema for consistentemente reproduzível, instalar e registar um novo agente de autenticação. E desinstalar atual. 
| 80011 | O Agente de Autenticação não conseguiu obter a chave de desencriptação. | Se o problema for consistentemente reproduzível, instalar e registar um novo agente de autenticação. E desinstalar atual.

>[!IMPORTANT]
>Agentes de autenticação pass-through autenticar utilizadores do Azure AD, validando seus nomes de utilizador e palavras-passe no Active Directory ao chamar o [API do Win32 LogonUser](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx). Como resultado, se tiver definido a definição de "Início de sessão para" no Active Directory para limitar o acesso de início de sessão da estação de trabalho, terá de adicionar servidores que alojam os agentes de autenticação pass-through para a lista de "Início de sessão para" servidores também. Isso não for feito irá bloquear os utilizadores de iniciar sessão no Azure AD.

## <a name="authentication-agent-installation-issues"></a>Problemas de instalação do agente de autenticação

### <a name="an-unexpected-error-occurred"></a>Ocorreu um erro inesperado

[Recolher registos de agente](#collecting-pass-through-authentication-agent-logs) partir do servidor e contacte Support da Microsoft por seu problema.

## <a name="authentication-agent-registration-issues"></a>Problemas de registo do agente de autenticação

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>Falha no registo do agente de autenticação devido a portas bloqueadas

Certifique-se de que o servidor em que tenha sido instalado o agente de autenticação pode comunicar com o nosso serviço URLs e portas listadas [aqui](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>Falha no registo do agente de autenticação devido a erros de autorização de token ou uma conta

Certifique-se de que utiliza uma conta de Administrador Global apenas na cloud para o Azure AD Connect ou instalação de agente de autenticação autónoma e operações de registo. Existe um problema conhecido com contas de Administrador Global a MFA ativada; Desative temporariamente MFA (apenas para concluir as operações) como uma solução alternativa.

### <a name="an-unexpected-error-occurred"></a>Ocorreu um erro inesperado

[Recolher registos de agente](#collecting-pass-through-authentication-agent-logs) partir do servidor e contacte Support da Microsoft por seu problema.

## <a name="authentication-agent-uninstallation-issues"></a>Problemas de desinstalação de agente de autenticação

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Mensagem de aviso quando desinstalar o Azure AD Connect

Se tiver a autenticação pass-through ativada no seu inquilino e tentar desinstalar o Azure AD Connect, mostra-lhe a seguinte mensagem de aviso: "Os utilizadores não serão capazes de início de sessão para o Azure AD a menos que tenha outros agentes de autenticação pass-through instalados em outros servidores."

Certifique-se de que a configuração está [elevada disponibilidade](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) antes de desinstalar o Azure AD Connect para evitar a quebra de sessão do utilizador.

## <a name="issues-with-enabling-the-feature"></a>Problemas relacionados com a habilitação do recurso

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>A habilitação do recurso falhou porque não havia nenhum agentes de autenticação disponíveis

Tem de ter, pelo menos, um agente de autenticação para ativar a autenticação pass-through no inquilino do Active Directory. Pode instalar um agente de autenticação por instalar o Azure AD Connect ou autónomo o agente de autenticação.

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>A habilitação do recurso falhou devido a portas bloqueadas

Certifique-se de que o servidor no qual está instalado o Azure AD Connect consegue comunicar com o nosso serviço URLs e portas listadas [aqui](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>A habilitação do recurso falhou devido a erros de autorização do token ou uma conta

Certifique-se de que utilize uma conta de Administrador Global apenas na cloud quando a habilitação do recurso. Existe um problema conhecido com a autenticação multifator (MFA)-ativado as contas de Administrador Global; Desative temporariamente MFA (apenas para concluir a operação) como uma solução alternativa.

## <a name="collecting-pass-through-authentication-agent-logs"></a>Recolher registos do agente de autenticação pass-through

Dependendo do tipo de problema que pode ter, terá de ter um aspeto em locais diferentes para os registos do agente de autenticação pass-through.

### <a name="azure-ad-connect-logs"></a>Registos do Azure AD Connect

Para erros relacionados com a instalação, verifique os registos do Azure AD Connect em **%ProgramData%\AADConnect\trace-\*. log**.

### <a name="authentication-agent-event-logs"></a>Registos de eventos do agente de autenticação

Para erros relacionados com o agente de autenticação, abra a aplicação de Visualizador de eventos no servidor e verifique sob **aplicação e serviço Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

Análises detalhadas, ative o registo de "Session" (botão direito do mouse no aplicativo Visualizador de eventos para encontrar esta opção). Não execute o agente de autenticação com este registo ativado durante operações normais; Utilize apenas para resolução de problemas. Os conteúdos dos registos são visíveis apenas após o registo está desativado novamente.

### <a name="detailed-trace-logs"></a>Logs de rastreamento detalhadas

Para resolver falhas de início de sessão de utilizador, procure os registos de rastreio no **%ProgramData%\Microsoft\Azure AD ligar Agent\Trace de autenticação\\**. Estes registos incluem motivos por que um utilizador específico início de sessão falhou ao utilizar a funcionalidade de autenticação pass-through. Estes erros também são mapeados para os motivos das falhas de início de sessão mostrados na tabela de motivos de falhas de início de sessão anterior. Segue-se uma entrada de registo de exemplo:

```
    AzureADConnectAuthenticationAgentService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Pode obter descritivos detalhes do erro ("1328" no exemplo anterior) abrindo o prompt de comando e executar o seguinte comando (tenha em atenção: Substitua '1328' com o número de erro real que vê nos seus registos):

`Net helpmsg 1328`

![Autenticação pass-through](./media/tshoot-connect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Registos de controlador de domínio

Se o registo de auditoria é ativado, pode encontrar informações adicionais nos registos de segurança dos seus controladores de domínio. Uma forma simples de início de sessão pedidos enviados pelos agentes de autenticação pass-through de consulta é o seguinte:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="performance-monitor-counters"></a>Contadores de Monitor de desempenho

Outra forma para monitorizar os agentes de autenticação é controlar os contadores de Monitor de desempenho específicos em cada servidor onde está instalado o agente de autenticação. Utilize os seguintes contadores Global (**autenticações # PTA**, **#PTA falha autenticações** e **autenticações efetuadas com êxito de #PTA**) e contadores de erro (**Erros de autenticação # PTA**):

![Contadores de Monitor de desempenho de autenticação pass-through](./media/tshoot-connect-pass-through-authentication/pta12.png)

>[!IMPORTANT]
>Autenticação pass-through fornece elevada disponibilidade através de vários agentes de autenticação, e _não_ balanceamento de carga. Consoante a configuração, _não_ todos os seus agentes de autenticação de recebimento aproximadamente _igual_ número de pedidos. É possível que um agente de autenticação específico recebe tráfego não de todo.
