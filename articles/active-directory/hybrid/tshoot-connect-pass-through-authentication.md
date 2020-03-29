---
title: 'Azure AD Connect: Troubleshoot Pass-through Authentication [ Microsoft Docs'
description: Este artigo descreve como resolver problemas azure Ative Directory (Azure AD) Pass-through Authentication.
services: active-directory
keywords: Troubleshoot Azure AD Connect Pass-through Authentication, instale Ative Directory, componentes necessários para Azure AD, SSO, Single Sign-on
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60456178"
---
# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>Resolver problemas da Autenticação Pass-through do Azure Active Directory

Este artigo ajuda-o a encontrar informações sobre questões comuns relativas à autenticação de passes ad ad.de Azure.

>[!IMPORTANT]
>Se estiver a enfrentar problemas de sessão de utilizador com autenticação pass-through, não desative a funcionalidade ou desinstale agentes de autenticação Pass-through sem ter uma conta global de administrador apenas na nuvem para recorrer. Saiba adicionar uma conta global [de administrador apenas](../active-directory-users-create-azure-portal.md)em nuvem. Fazer este passo é fundamental e garante que não se fica trancado fora do seu inquilino.

## <a name="general-issues"></a>Problemas gerais

### <a name="check-status-of-the-feature-and-authentication-agents"></a>Verifique o estado da funcionalidade e dos agentes de autenticação

Certifique-se de que a funcionalidade de autenticação pass-through ainda está **ativada** no seu inquilino e o estado dos Agentes de Autenticação mostra **Ativo**, e não **Inativo**. Pode verificar o estado indo para a lâmina **Azure AD Connect** no centro de [administração do Diretório Ativo Azure](https://aad.portal.azure.com/).

![Centro de administração de diretório sonâmático Azure - Lâmina Azure AD Connect](./media/tshoot-connect-pass-through-authentication/pta7.png)

![Centro de administração de Diretório Ativo Azure - lâmina de autenticação pass-through](./media/tshoot-connect-pass-through-authentication/pta11.png)

### <a name="user-facing-sign-in-error-messages"></a>Mensagens de erro de sessão viradas para o utilizador

Se o utilizador não conseguir iniciar a utilização da Autenticação Pass-through, poderá ver um dos seguintes erros virados para o utilizador no ecrã de entrada de AD Azure: 

|Erro|Descrição|Resolução
| --- | --- | ---
|AADSTS80001|Incapaz de ligar ao Diretório Ativo|Certifique-se de que os servidores do agente são membros da mesma floresta AD que os utilizadores cujas palavras-passe precisam de ser validadas e que são capazes de se conectar ao Ative Directory.  
|AADSTS8002|Ocorreu um intervalo de tempo que liga vadia a Diretório Ativo|Verifique para se o Diretório Ativo está disponível e está a responder aos pedidos dos agentes.
|AADSTS80004|O nome de utilizador passado para o agente não era válido|Certifique-se de que o utilizador está a tentar iniciar sessão com o nome de utilizador certo.
|AADSTS80005|Validação encontrou WebException imprevisível|Um erro transitório. Tente o pedido. Se continuar a falhar, contacte o suporte da Microsoft.
|AADSTS800007|Ocorreu um erro de comunicação com o Diretório Ativo|Verifique se o agente regista mais informações e verifique se o Diretório Ativo está a funcionar como esperado.

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Razões de insucesso no centro de administração do Diretório Ativo Azure (precisa de licença Premium)

Se o seu inquilino tiver uma licença Azure AD Premium associada a ela, também pode consultar o [relatório de atividades](../reports-monitoring/concept-sign-ins.md) de entrada no centro de [administração do Azure Ative Directory](https://aad.portal.azure.com/).

![Centro de Administração de Diretórios Ativos azure - Relatório de inscrições](./media/tshoot-connect-pass-through-authentication/pta4.png)

Navegue para **o Azure Ative Directory** -> **Sign-ins** no centro de administração do [Azure Ative Directory](https://aad.portal.azure.com/) e clique na atividade de entrada de um utilizador específico. Procure o campo CÓDIGO DE **ERRO SIGN-IN.** Mapeie o valor desse campo para uma razão de falha e resolução utilizando a seguinte tabela:

|Código de erro de insessão|Razão de falha de inscrição|Resolução
| --- | --- | ---
| 50144 | A palavra-passe do Active Directory do utilizador expirou. | Reponha a palavra-passe do utilizador no seu Diretório Ativo no local.
| 80001 | Não existe nenhum Agente de Autenticação disponível. | Instale e registe um Agente de Autenticação.
| 80002 | O pedido de validação da palavra-passe do Agente de Autenticação atingiu o tempo limite. | Verifique se o seu Diretório Ativo está acessível a partir do Agente de Autenticação.
| 80003 | O Agente de Autenticação recebeu uma resposta inválida. | Se o problema for consistentemente reprodutível em vários utilizadores, verifique a configuração do Diretório Ativo.
| 80004 | Foi utilizado um Nome Principal de Utilizador (UPN) no pedido de início de sessão. | Peça ao utilizador que faça o seu insto com o nome de utilizador correto.
| 80005 | Agente de Autenticação: ocorreu um erro. | Erro transitório. Tente novamente mais tarde.
| 80007 | O Agente de Autenticação não se consegue ligar ao Active Directory. | Verifique se o seu Diretório Ativo está acessível a partir do Agente de Autenticação.
| 80010 | O Agente de Autenticação não conseguiu desencriptar a palavra-passe. | Se o problema for consistentemente reprodutível, instale e registe um novo Agente de Autenticação. E desinstalar o atual. 
| 80011 | O Agente de Autenticação não conseguiu obter a chave de desencriptação. | Se o problema for consistentemente reprodutível, instale e registe um novo Agente de Autenticação. E desinstalar o atual.

>[!IMPORTANT]
>Os agentes de autenticação pass-through autenticam os utilizadores de AD Azure validando os seus nomes de utilizador e palavras-passe contra o Diretório Ativo, ligando para a [Win32 LogonUser API](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx). Como resultado, se tiver definido a definição "Logon To" no Ative Directory para limitar o acesso ao logon da estação de trabalho, terá de adicionar servidores que hospedam agentes de autenticação pass-through na lista de servidores "Logon To". Se não o fizer, os seus utilizadores vão impedir que os seus utilizadores se inscrevam no Azure AD.

## <a name="authentication-agent-installation-issues"></a>Problemas de instalação do Agente de Autenticação

### <a name="an-unexpected-error-occurred"></a>Ocorreu um erro inesperado

[Recolher registos](#collecting-pass-through-authentication-agent-logs) de agentes no servidor e contactar o Microsoft Support com o seu problema.

## <a name="authentication-agent-registration-issues"></a>Problemas de registo do Agente de Autenticação

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>Registo do Agente de Autenticação falhou devido a portas bloqueadas

Certifique-se de que o servidor no qual o Agente de Autenticação foi instalado pode comunicar com os URLs de serviço e portas [aqui](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)listadas .

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>Registo do Agente de Autenticação falhou devido a erros de autorização de conta ou de token

Certifique-se de que utiliza uma conta de Administrador Global apenas em nuvem para todas as operações de instalação e registo do Agente de Autenticação Azure AD Ou autónoma. Existe uma questão conhecida com contas de Administrador Global ativadas pelo MFA; desligar o MFA temporariamente (apenas para completar as operações) como uma suver.

### <a name="an-unexpected-error-occurred"></a>Ocorreu um erro inesperado

[Recolher registos](#collecting-pass-through-authentication-agent-logs) de agentes no servidor e contactar o Microsoft Support com o seu problema.

## <a name="authentication-agent-uninstallation-issues"></a>Problemas de instalação do Agente de Autenticação

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Mensagem de aviso ao desinstalar o Azure AD Connect

Se tiver a Autenticação Pass-through ativada no seu inquilino e tentar desinstalar o Azure AD Connect, mostra-lhe a seguinte mensagem de aviso: "Os utilizadores não poderão iniciar sessão no Azure AD a menos que tenha outros agentes de autenticação pass-through instalados em outros servidores.

Certifique-se de que a sua configuração está [altamente disponível](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) antes de desinstalar o Azure AD Connect para evitar a quebra de entrada do utilizador.

## <a name="issues-with-enabling-the-feature"></a>Problemas com a ativação da funcionalidade

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>Ativar a funcionalidade falhou porque não havia agentes de autenticação disponíveis

Você precisa ter pelo menos um Agente de Autenticação ativo para ativar a autenticação pass-through no seu inquilino. Pode instalar um Agente de Autenticação instalando o Azure AD Connect ou um Agente de Autenticação autónomo.

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>Permitir que a funcionalidade falhou devido a portas bloqueadas

Certifique-se de que o servidor no qual o Azure AD Connect está instalado pode comunicar com os urLs de serviço e portas listados [aqui](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>Permitir que a funcionalidade falhou devido a erros de autorização de token ou conta

Certifique-se de que utiliza uma conta global de administrador apenas na nuvem ao ativar a funcionalidade. Existe uma questão conhecida com contas de autenticação global ativadas por multifactor (MFA); desligar o MFA temporariamente (apenas para completar a operação) como uma suver.

## <a name="collecting-pass-through-authentication-agent-logs"></a>Recolha de registos do Agente de Autenticação Pass-through

Dependendo do tipo de problema que possa ter, precisa procurar em diferentes locais para registos de agentes de autenticação pass-through.

### <a name="azure-ad-connect-logs"></a>Registos azure AD Connect

Para detetar erros relacionados com a instalação, verifique os registos azure AD Connect em **%ProgramData%\AADConnect\trace-\*.log**.

### <a name="authentication-agent-event-logs"></a>Registos de eventos do Agente de Autenticação

Para erros relacionados com o Agente de Autenticação, abra a aplicação do Espectador de Eventos no servidor e verifique em Registos de **Aplicação e Serviço\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

Para análises detalhadas, ative o registo "Session" (clique à direita dentro da aplicação Do Espectador do Evento para encontrar esta opção). Não execute o Agente de Autenticação com este registo ativado durante as operações normais; usar apenas para resolução de problemas. O conteúdo do registo só é visível depois de o registo ser desativado novamente.

### <a name="detailed-trace-logs"></a>Registos de vestígios detalhados

Para resolver falhas de registo do utilizador, procure registos de rastreio em **%ProgramData%\Microsoft\Azure AD Connect Authentication Agent\Trace\\**. Estes registos incluem razões pelas quais um utilizador específico falhou na utilização da funcionalidade de Autenticação Pass-through. Estes erros também estão mapeados para as razões de falha de inscrição apresentadas na tabela de razões de falha de inscrição anteriores. Segue-se uma entrada de registo de exemplo:

```
    AzureADConnectAuthenticationAgentService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Pode obter detalhes descritivos do erro ('1328' no exemplo anterior) abrindo o pedido de comando e executando o seguinte comando (Nota: Substitua '1328' pelo número real de erro que vê nos seus registos):

`Net helpmsg 1328`

![Autenticação pass-through](./media/tshoot-connect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Registos do Controlador de Domínio

Se o registo de auditoria estiver ativado, podem ser encontradas informações adicionais nos registos de segurança dos seus Controladores de Domínio. Uma forma simples de consultar pedidos de inscrição enviados por Agentes de Autenticação Pass-through é a seguinte:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="performance-monitor-counters"></a>Contadores de Monitor de Desempenho

Outra forma de monitorizar os Agentes de Autenticação é rastrear contadores específicos do Monitor de Desempenho em cada servidor onde o Agente de Autenticação está instalado. Utilize os seguintes contadores Globais (**# autenticações PTA,** **#PTA autenticações falhadas** e **#PTA autenticações bem sucedidas)** e contadores de erros (# erros de**autenticação PTA):**

![Contadores de monitores de desempenho de autenticação pass-through](./media/tshoot-connect-pass-through-authentication/pta12.png)

>[!IMPORTANT]
>A Autenticação Pass-through proporciona uma elevada disponibilidade utilizando vários Agentes de Autenticação e não o equilíbrio _de_ carga. Dependendo da sua configuração, _nem_ todos os seus Agentes de Autenticação recebem aproximadamente o _mesmo_ número de pedidos. É possível que um Agente de Autenticação específico não receba qualquer tráfego.
