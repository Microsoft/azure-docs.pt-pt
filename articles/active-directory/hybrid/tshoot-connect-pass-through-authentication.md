---
title: 'Azure AD Connect: Troubleshoot Pass-through Authentication / Microsoft Docs'
description: Este artigo descreve como resolver problemas com a autenticação pass-through do Azure Ative Directory (Azure AD).
services: active-directory
keywords: Resolução de problemas Ad Connect Authentication, instalar Ative Directory, componentes necessários para Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/27/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99da9b787bfe06bece8b8dafdafc257336dddf63
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96176193"
---
# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>Resolver problemas da Autenticação Pass-through do Azure Active Directory

Este artigo ajuda-o a encontrar informações sobre questões comuns relativas à autenticação pass-through Ad Ad do Azure.

>[!IMPORTANT]
>Se estiver a enfrentar problemas de entrada no utilizador com a Autenticação Pass-through, não desative a funcionalidade ou desinstale agentes de autenticação pass-through sem ter uma conta de Administrador Global apenas na nuvem para voltar a ser apoiada. Saiba mais [sobre a adição de uma conta de Administrador Global apenas na nuvem.](../fundamentals/add-users-azure-active-directory.md) Fazer este passo é fundamental e garante que não fique trancado fora do seu inquilino.

## <a name="general-issues"></a>Problemas gerais

### <a name="check-status-of-the-feature-and-authentication-agents"></a>Verificar o estado da funcionalidade e dos agentes de autenticação

Certifique-se de que a função de autenticação pass-through ainda está **ativada** no seu inquilino e o estado dos Agentes de Autenticação mostra **ativo**, e não **Inativo**. Pode verificar o estado indo para a lâmina **Azure AD Connect** no [centro de administração Azure Ative.](https://aad.portal.azure.com/)

![Centro de administração Azure Ative Directory - Lâmina Azure AD Connect](./media/tshoot-connect-pass-through-authentication/pta7.png)

![Centro de administração Azure Ative Directory - Lâmina de autenticação pass-through](./media/tshoot-connect-pass-through-authentication/pta11.png)

### <a name="user-facing-sign-in-error-messages"></a>Mensagens de erro de inscrição orientadas para o utilizador

Se o utilizador não conseguir assinar para utilizar a Autenticação Pass-through, poderá ver um dos seguintes erros virados para o utilizador no ecrã de entrada AD Azure: 

|Erro|Descrição|Resolução
| --- | --- | ---
|AADSTS80001|Incapaz de ligar ao Ative Directory|Certifique-se de que os servidores de agentes são membros da mesma floresta de AD que os utilizadores cujas palavras-passe precisam de ser validadas e que são capazes de se conectar ao Ative Directory.  
|AADSTS8002|Ocorreu um tempo limite de ligação ao Ative Directory|Verifique se o Ative Directory está disponível e está a responder aos pedidos dos agentes.
|AADSTS80004|O nome de utilizador passado para o agente não era válido|Certifique-se de que o utilizador está a tentar iniciar sing com o nome de utilizador certo.
|AADSTS80005|Validação encontrou webExcepção imprevisível|Um erro transitório. Recandidutar o pedido. Se continuar a falhar, contacte o suporte da Microsoft.
|AADSTS80007|Ocorreu um erro na comunicação com o Ative Directory|Verifique os registos do agente para obter mais informações e verifique se o Ative Directory está a funcionar como esperado.

### <a name="users-get-invalid-usernamepassword-error"></a>Os utilizadores obtêm um erro de nome de utilizador/palavra-passe inválido 

Isto pode acontecer quando o Nome Do Utilizador no Local (UPN) é diferente da nuvem do utilizador UPN.

Para confirmar que este é o problema, primeiro teste que o agente de autenticação pass-through está a funcionar corretamente:


1. Criar uma conta de teste.  
2. Importar o módulo PowerShell na máquina do agente:
 
 ```powershell
 Import-Module "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\PassthroughAuthPSModule\PassthroughAuthPSModule.psd1"
 ```
3. Executar o comando Invoke PowerShell: 

 ```powershell
 Invoke-PassthroughAuthOnPremLogonTroubleshooter 
 ``` 
4. Quando lhe for pedido que introduza credenciais, introduza o mesmo nome de utilizador e palavra-passe que são utilizados para iniciar seduções em ( https://login.microsoftonline.com) .

Se obtém o mesmo erro de nome de utilizador/palavra-passe, isto significa que o agente de autenticação Pass-through está a funcionar corretamente e o problema pode ser que a UPN no local não seja encaminhável. Para saber mais, consulte [o ID de Login Alternativo Configurado.](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)

> [!IMPORTANT]
> Se o servidor AZure AD Connect não estiver ligado ao domínio, ocorre um requisito mencionado no [Azure AD Connect: Pré-requisitos](./how-to-connect-install-prerequisites.md#installation-prerequisites), ocorre o número de nome de utilizador/palavra-passe inválido.

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Razões de falha de inscrição no centro de administração do Azure Ative (precisa de licença Premium)

Se o seu inquilino tiver uma licença Azure AD Premium associada a ela, também pode consultar o [relatório de atividades de inscrição](../reports-monitoring/concept-sign-ins.md) no [centro de administração Azure Ative.](https://aad.portal.azure.com/)

![Centro de administração Azure Ative Directory - Relatório de inscrições](./media/tshoot-connect-pass-through-authentication/pta4.png)

Navegue para **Azure Ative Directory**  ->  **Ins** no centro de administração do [Azure Ative Directory](https://aad.portal.azure.com/) e clique na atividade de inscrição de um utilizador específico. Procure o campo **CÓDIGO DE ERRO SIGN-IN.** Mapear o valor desse campo para uma razão de falha e resolução utilizando a seguinte tabela:

|Código de erro de inscrição|Razão de falha de inscrição|Resolução
| --- | --- | ---
| 50144 | A palavra-passe do Active Directory do utilizador expirou. | Repor a palavra-passe do utilizador no seu Ative Directory no local.
| 80001 | Não existe nenhum Agente de Autenticação disponível. | Instale e registe um Agente de Autenticação.
| 80002 | O pedido de validação da palavra-passe do Agente de Autenticação atingiu o tempo limite. | Verifique se o seu Diretório Ativo está acessível a partir do Agente de Autenticação.
| 80003 | O Agente de Autenticação recebeu uma resposta inválida. | Se o problema for consistentemente reprodutível em vários utilizadores, verifique a configuração do Ative Directory.
| 80004 | Foi utilizado um Nome Principal de Utilizador (UPN) no pedido de início de sessão. | Peça ao utilizador para iniciar sedução com o nome de utilizador correto.
| 80005 | Agente de Autenticação: ocorreu um erro. | Erro transitório. Tente novamente mais tarde.
| 80007 | O Agente de Autenticação não se consegue ligar ao Active Directory. | Verifique se o seu Diretório Ativo está acessível a partir do Agente de Autenticação.
| 80010 | O Agente de Autenticação não conseguiu desencriptar a palavra-passe. | Se o problema for consistentemente reprodutível, instale e registe um novo Agente de Autenticação. E desinstalar o atual. 
| 80011 | O Agente de Autenticação não conseguiu obter a chave de desencriptação. | Se o problema for consistentemente reprodutível, instale e registe um novo Agente de Autenticação. E desinstalar o atual.
| 80014 | O pedido de validação respondeu após o tempo máximo decorrido. | Agente de autenticação cronometrado. Abra um bilhete de suporte com o código de erro, iD de correlação e cartão de tempo para obter mais detalhes sobre este erro

>[!IMPORTANT]
>Os Agentes de Autenticação Pass-through autenticam os utilizadores Azure AD, validando os seus nomes de utilizador e palavras-passe contra o Ative Directory, chamando a [API do LogonUser Win32](/windows/win32/api/winbase/nf-winbase-logonusera). Como resultado, se definiu a definição "Logon To" no Ative Directory para limitar o acesso à estação de trabalho, terá de adicionar servidores que hospedam Agentes de Autenticação Pass-through na lista de servidores "Logon To". Se não o fizer, impedirá os seus utilizadores de se inscreverem no Azure AD.

## <a name="authentication-agent-installation-issues"></a>Problemas de instalação do Agente de Autenticação

### <a name="an-unexpected-error-occurred"></a>Ocorreu um erro inesperado

[Recolher registos](#collecting-pass-through-authentication-agent-logs) de agentes a partir do servidor e contactar o Microsoft Support com o seu problema.

## <a name="authentication-agent-registration-issues"></a>Emissões de registo do Agente de Autenticação

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>Registo do Agente de Autenticação falhou devido a portas bloqueadas

Certifique-se de que o servidor em que o Agente de Autenticação foi instalado pode comunicar com os nossos URLs de serviço e portas listadas [aqui.](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>O registo do Agente de Autenticação falhou devido a erros de autorização de ficha ou conta

Certifique-se de que utiliza uma conta global de administrador apenas na nuvem para todas as operações de instalação e registo do Agente de Autenticação Azure ou autónoma. Existe um problema conhecido com as contas de Administrador Global habilitados para o MFA; desligar temporariamente o MFA (apenas para completar as operações) como uma solução alternativa.

### <a name="an-unexpected-error-occurred"></a>Ocorreu um erro inesperado

[Recolher registos](#collecting-pass-through-authentication-agent-logs) de agentes a partir do servidor e contactar o Microsoft Support com o seu problema.

## <a name="authentication-agent-uninstallation-issues"></a>Problemas de desinstalação do Agente de Autenticação

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Mensagem de aviso ao desinstalar Azure AD Connect

Se tiver a Autenticação Pass-through ativada no seu inquilino e tentar desinstalar o Azure AD Connect, mostra-lhe a seguinte mensagem de aviso: "Os utilizadores não poderão iniciar sação no AD Azure a menos que tenha outros agentes de autenticação Pass-through instalados noutros servidores."

Certifique-se de que a sua configuração está [altamente disponível](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) antes de desinstalar o Azure AD Connect para evitar que o utilizador insinuse.

## <a name="issues-with-enabling-the-feature"></a>Problemas com a ativação da funcionalidade

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>Habilitar a funcionalidade falhou porque não havia agentes de autenticação disponíveis

Precisa de ter pelo menos um Agente de Autenticação Ativo para ativar a Autenticação Pass-through no seu inquilino. Pode instalar um Agente de Autenticação instalando o Azure AD Connect ou um Agente de Autenticação Autónomo.

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>Permitir que a funcionalidade falhou devido a portas bloqueadas

Certifique-se de que o servidor no qual o Azure AD Connect está instalado pode comunicar com os nossos URLs de serviço e portas listadas [aqui.](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>Permitir que a funcionalidade falhou devido a erros de token ou de autorização de conta

Certifique-se de que utiliza uma conta de Administrador Global apenas na nuvem ao ativar a funcionalidade. Existe um problema conhecido com as contas de administrador global ativadas por vários fatores (MFA); desligue o MFA temporariamente (apenas para completar a operação) como uma solução alternativa.

## <a name="collecting-pass-through-authentication-agent-logs"></a>Recolha de registos de agente de autenticação pass-through

Dependendo do tipo de problema que possa ter, precisa de procurar em diferentes locais para registos de Agente de Autenticação Pass-through.

### <a name="azure-ad-connect-logs"></a>Registos de ligação Azure AD

Para obter erros relacionados com a instalação, verifique os registos Azure AD Connect em **%ProgramData%\\AADConnect\trace- \* .log**.

### <a name="authentication-agent-event-logs"></a>Registos de eventos do Agente de Autenticação

Para erros relacionados com o Agente de Autenticação, abra a aplicação do Observador de Eventos no servidor e verifique em **Registos de Aplicação e Serviço\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

Para análises detalhadas, ative o registo "Session" (clique à direita dentro da aplicação 'Espectador de Eventos' para encontrar esta opção). Não execute o Agente de Autenticação com este registo ativado durante as operações normais; utilizar apenas para resolução de problemas. O conteúdo do registo só é visível depois de o registo ser novamente desativado.

O manifesto do evento do agente PTA pode ser encontrado [aqui.](https://msazure.visualstudio.com/One/_git/AD-AppProxy?path=%2Fsrc%2FProduct%2FMUC%2FPTADiagnosticsResource%2FPTADiagnosticsResource%2FPTAConnectorDiagnosticsResource%2FPTAConnectorEventManifest.man&_a=contents&version=GBmaster)

### <a name="detailed-trace-logs"></a>Registos de vestígios detalhados

Para resolver problemas de falhas de registo do utilizador, procure registos de vestígios em **%ProgramData%\Microsoft\Microsoft\Azure AD Connect Authentication Agent\Trace \\**. Estes registos incluem razões pelas quais um registo específico do utilizador falhou na utilização da função De autenticação Pass-through. Estes erros são também mapeados para as razões de falha de inscrição apresentadas na tabela de razões de insucesso de inscrição anterior. Segue-se uma entrada de registo de exemplo:

```
    AzureADConnectAuthenticationAgentService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Pode obter detalhes descritivos do erro ('1328' no exemplo anterior) abrindo a solicitação de comando e executando o seguinte comando (Nota: Substitua '1328' pelo número de erro real que vê nos seus registos):

`Net helpmsg 1328`

![Autenticação pass-through](./media/tshoot-connect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Registos do controlador de domínio

Se o registo de auditoria estiver ativado, podem ser encontradas informações adicionais nos registos de segurança dos seus Controladores de Domínio. Uma forma simples de consultar os pedidos de inscrição enviados pelos Agentes de Autenticação Pass-through é a seguinte:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="performance-monitor-counters"></a>Balcões do Monitor de Desempenho

Outra forma de monitorizar os Agentes de Autenticação é rastrear contadores específicos do Monitor de Desempenho em cada servidor onde o Agente de Autenticação está instalado. Utilize os seguintes contadores Global **(# autenticações PTA,** **#PTA autenticações falhadas** e **#PTA autenticações bem sucedidas)** e contadores de erros **(erros de autenticação de PTA**):

![Balcões do Monitor de Desempenho de Autenticação De passagem](./media/tshoot-connect-pass-through-authentication/pta12.png)

>[!IMPORTANT]
>A autenticação pass-through proporciona uma elevada disponibilidade utilizando vários Agentes de Autenticação e _não_ um equilíbrio de carga. Dependendo da sua configuração, _nem_ todos os seus Agentes de Autenticação recebem aproximadamente _igual_ número de pedidos. É possível que um Agente de Autenticação específico não receba qualquer tráfego.
