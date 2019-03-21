---
title: Resolver problemas com códigos de erro para a extensão NPS da MFA do Azure | Documentos da Microsoft
description: Obtenha ajuda para resolver problemas com a extensão NPS para multi-factor Authentication do Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c831a5a939e946d7189e4d35eaf051508e904a4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57999520"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-multi-factor-authentication"></a>Resolver mensagens de erro da extensão NPS para multi-factor Authentication do Azure

Se encontrar erros com a extensão NPS para multi-factor Authentication do Azure, utilize este artigo para atingir uma resolução mais rápida. Encontram-se no Visualizador de eventos em registos de extensão NPS **modos de exibição personalizados** > **funções de servidor** > **serviços de acesso e política de rede** sobre o servidor onde está instalada a extensão de NPS.

## <a name="troubleshooting-steps-for-common-errors"></a>Passos de resolução de problemas para erros comuns

| Código de erro | Passos de resolução de problemas |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [Contacte o suporte](#contact-microsoft-support)e mencionar a lista de passos para recolher registos. Fornece o máximo de informações sobre o que aconteceu antes do erro, incluindo o id de inquilino e o nome principal de utilizador (UPN). |
| **CLIENT_CERT_INSTALL_ERROR** | Pode haver um problema com a forma como o certificado de cliente foi instalado ou associado ao seu inquilino. Siga as instruções em [resolução de problemas a extensão NPS da MFA](howto-mfa-nps-extension.md#troubleshooting) para investigar problemas de certificado de cliente. |
| **ESTS_TOKEN_ERROR** | Siga as instruções em [resolução de problemas a extensão NPS da MFA](howto-mfa-nps-extension.md#troubleshooting) para investigar o certificado de cliente e a ADAL token problemas. |
| **HTTPS_COMMUNICATION_ERROR** | O servidor NPS não consegue receber respostas de MFA do Azure. Certifique-se de que as firewalls bidirecionalmente aberta para tráfego de e para https://adnotifications.windowsazure.com |
| **HTTP_CONNECT_ERROR** | No servidor que executa a extensão NPS, certifique-se de que pode entrar https://adnotifications.windowsazure.com e https://login.microsoftonline.com/. Se não carregam esses sites, resolver problemas de conectividade nesse servidor. |
| **Extensão NPS da MFA do Azure:** <br> Extensão NPS da MFA do Azure só executa a autenticação secundária pedidos Radius no Estado AccessAccept. Pedido recebido para o nome de utilizador do utilizador com o estado de resposta AccessReject, ignorar o pedido. | Este erro normalmente reflete uma falha de autenticação no AD ou que o servidor NPS não consegue receber respostas do Azure AD. Certifique-se de que as firewalls bidirecionalmente aberta para tráfego de e para https://adnotifications.windowsazure.com e https://login.microsoftonline.com utilizando as portas 80 e 443. Também é importante verificar que, na guia DIAL-IN de permissões de acesso de rede, a definição está definida para "controlar o acesso por meio da diretiva de rede NPS". |
| **REGISTRY_CONFIG_ERROR** | Uma chave está em falta no registo para o aplicativo, que pode acontecer porque o [script do PowerShell](howto-mfa-nps-extension.md#install-the-nps-extension) não era executar após a instalação. A mensagem de erro deve incluir a chave em falta. Certifique-se de que tem a chave em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa. |
| **REQUEST_FORMAT_ERROR** <br> Atributo de userName\Identifier Radius obrigatório em falta no pedido RADIUS. Certifique-se de que o NPS está a receber pedidos RADIUS | Este erro normalmente reflete um problema de instalação. A extensão NPS deve ser instalada em servidores NPS que podem receber pedidos RADIUS. Os servidores NPS que são instalados como dependências para serviços como o RDG e o RRAS não receberem pedidos radius. Extensão de NPS não funciona quando instalado sobre essas instalações e erros de saída, uma vez que ele não é possível ler os detalhes do pedido de autenticação. |
| **REQUEST_MISSING_CODE** | Certifique-se de que o protocolo de encriptação da palavra-passe entre os servidores NPS e NAS suporta o método de autenticação secundária, que está a utilizar. **PAP** suporta todos os métodos de autenticação de MFA do Azure na cloud: chamada telefónica, mensagem de texto unidirecional, notificação de aplicação móvel e o código de verificação de aplicação móvel. **CHAPV2** e **EAP** suporte a chamadas telefónicas e de notificação de aplicação móvel. |
| **USERNAME_CANONICALIZATION_ERROR** | Certifique-se de que o utilizador esteja presente na sua instância do Active Directory no local e que o serviço NPS tem permissões para aceder ao diretório. Se estiver a utilizar relações de confiança entre florestas, [contacte o suporte](#contact-microsoft-support) para obter ajuda. |

### <a name="alternate-login-id-errors"></a>Erros de ID de início de sessão alternativo

| Código de erro | Mensagem de erro | Passos de resolução de problemas |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | Erro: Falha na pesquisa de userObjectSid | Certifique-se de que o utilizador existe na sua instância do Active Directory no local. Se estiver a utilizar relações de confiança entre florestas, [contacte o suporte](#contact-microsoft-support) para obter ajuda. |
| **ALTERNATE_LOGIN_ID_ERROR** | Erro: Falha na pesquisa LoginId alternativo | Certifique-se de que LDAP_ALTERNATE_LOGINID_ATTRIBUTE está definida para um [atributo válido do Active Directory](https://msdn.microsoft.com/library/ms675090(v=vs.85).aspx). <br><br> Se LDAP_FORCE_GLOBAL_CATALOG está definido como True ou LDAP_LOOKUP_FORESTS está configurado com um valor não vazio, certifique-se de que configurou um Catálogo Global e que o atributo AlternateLoginId é adicionado a ele. <br><br> Se LDAP_LOOKUP_FORESTS estiver configurado com um valor não vazio, certifique-se de que o valor está correto. Se houver mais de um nome de floresta, os nomes têm de estar separados por ponto e vírgula, não espaços. <br><br> Se estes passos não corrigir o problema, [contacte o suporte](#contact-microsoft-support) para obter mais ajuda. |
| **ALTERNATE_LOGIN_ID_ERROR** | Erro: Valor de LoginId alternativa está vazio | Certifique-se de que o atributo AlternateLoginId está configurado para o utilizador. |

## <a name="errors-your-users-may-encounter"></a>Erros que os utilizadores podem ser encontrados

| Código de erro | Mensagem de erro | Passos de resolução de problemas |
| ---------- | ------------- | --------------------- |
| **AccessDenied** | Inquilino do autor da chamada não tem permissões de acesso para efetuar a autenticação do utilizador | Verifique se o domínio de inquilino e o domínio do nome principal de utilizador (UPN) são os mesmos. Por exemplo, certifique-se de que user@contoso.com está a tentar autenticar para o inquilino da Contoso. O UPN representa um utilizador válido para o inquilino no Azure. |
| **AuthenticationMethodNotConfigured** | O método de autenticação especificado não foi configurado para o utilizador | Peça ao utilizador que adicionar ou validar seus métodos de verificação, de acordo com as instruções em [gerir as definições de verificação de dois passos](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **AuthenticationMethodNotSupported** | Método de autenticação especificado não é suportado. | Recolher todos os seus registos que incluem este erro, e [contacte o suporte](#contact-microsoft-support). Quando contactar o suporte, forneça o nome de utilizador e o método de verificação secundária que disparou o erro. |
| **BecAccessDenied** | A chamada do MSODS Bec devolveu acesso negado, provavelmente o nome de utilizador não está definido no inquilino | O utilizador está presente no Active Directory no local, mas não está sincronizado no Azure AD por AD Connect. Em alternativa, o utilizador está em falta para o inquilino. Adicionar o utilizador para o Azure AD e tê-los a adicionar os seus métodos de verificação, de acordo com as instruções em [gerir as definições de verificação de dois passos](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **InvalidFormat** ou **StrongAuthenticationServiceInvalidParameter** | O número de telefone está num formato irreconhecível | Peça ao utilizador corrigir seus números de telefone de verificação. |
| **InvalidSession** | A sessão especificada é inválida ou pode ter expirado | A sessão demorou mais de três minutos a concluir. Certifique-se de que o utilizador é introduzir o código de verificação ou a responder à notificação da aplicação, no prazo de três minutos de iniciar o pedido de autenticação. Se o que não resolve o problema, verifique que não há nenhum latências de rede entre o cliente, servidor NAS, servidor NPS e o ponto de extremidade do MFA do Azure.  |
| **NoDefaultAuthenticationMethodIsConfigured** | Nenhum método de autenticação predefinido foi configurado para o utilizador | Peça ao utilizador que adicionar ou validar seus métodos de verificação, de acordo com as instruções em [gerir as definições de verificação de dois passos](../user-help/multi-factor-authentication-end-user-manage-settings.md). Certifique-se de que o usuário tiver escolhido um método de autenticação padrão e configurado desse método para a respetiva conta. |
| **OathCodePinIncorrect** | Código errado e pin introduzido. | Este erro não é esperado na extensão do NPS. Se o usuário o perceba isso, [contacte o suporte](#contact-microsoft-support) para resolução de problemas de ajuda. |
| **ProofDataNotFound** | Prova dados não foi configurados para o método de autenticação especificado. | Peça ao utilizador que tente um método de verificação diferente ou adicionar um novo métodos de verificação, de acordo com as instruções em [gerir as definições de verificação de dois passos](../user-help/multi-factor-authentication-end-user-manage-settings.md). Se o usuário continua a ver este erro depois de confirmar o método de verificação está configurado corretamente, [contacte o suporte](#contact-microsoft-support). |
| **SMSAuthFailedWrongCodePinEntered** | Código errado e pin introduzido. (OneWaySMS) | Este erro não é esperado na extensão do NPS. Se o usuário o perceba isso, [contacte o suporte](#contact-microsoft-support) para resolução de problemas de ajuda. |
| **TenantIsBlocked** | Inquilino está bloqueado | [Contacte o suporte](#contact-microsoft-support) com o ID de diretório da página de propriedades do Azure AD no portal do Azure. |
| **UserNotFound** | O utilizador especificado não foi encontrado | O inquilino já não é visível como o Active Directory no Azure AD. Verifique se a sua subscrição está ativa e tiver exigidas pela primeira vez aplicações de terceiros. Além disso, certifique-se de que o inquilino no requerente do certificado é conforme esperado e o certificado é ainda válido e registados no principal de serviço. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>As mensagens que os utilizadores podem ser encontrados que não são erros

Às vezes, os utilizadores podem obter mensagens de multi-factor Authentication, porque o seu pedido de autenticação não conseguiu. Essas não são erros no produto da configuração, mas são avisos intencionais explicando por que motivo foi negado um pedido de autenticação.

| Código de erro | Mensagem de erro | Passos recomendados | 
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | Código errado entered\OATH código incorreto | O utilizador introduziu o código errado. Este que tente novamente ao pedir um novo código ou volte a iniciar sessão. | 
| **SMSAuthFailedMaxAllowedCodeRetryReached** | Repetição de código permitido máximo foi atingida | O utilizador não foi possível o desafio de verificação demasiadas vezes. Consoante as suas definições, poderão ter de ser desbloqueado por um administrador agora.  |
| **SMSAuthFailedWrongCodeEntered** | Código errado introduzido/Text mensagem OTP incorreta | O utilizador introduziu o código errado. Este que tente novamente ao pedir um novo código ou volte a iniciar sessão. |

## <a name="errors-that-require-support"></a>Erros que precisam de suporte

Se tiver um desses erros, recomendamos que [contacte o suporte](#contact-microsoft-support) para obter ajuda de diagnóstico. Não existe nenhum conjunto padrão de etapas que os pode resolver estes erros. Quando contactar o suporte, certifique-se de que incluem como máximo de informações possível sobre os passos que levou a um erro e suas informações de inquilino.

| Código de erro | Mensagem de erro |
| ---------- | ------------- |
| **InvalidParameter** | Pedido não pode ser nulo |
| **InvalidParameter** | ObjectId não pode ser nulo ou vazio para ReplicationScope:{0} |
| **InvalidParameter** | O comprimento de CompanyName \{0} \ excede o comprimento máximo permitido {1} |
| **InvalidParameter** | UserPrincipalName não pode ser nulo nem estar vazio |
| **InvalidParameter** | O TenantId fornecido não está no formato correto |
| **InvalidParameter** | SessionId não pode ser nulo nem estar vazio |
| **InvalidParameter** | Não foi possível resolver qualquer ProofData pedido ou Msods. O ProofData é desconhecido |
| **InternalError** |  |
| **OathCodePinIncorrect** |  |
| **VersionNotSupported** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>Passos Seguintes

### <a name="troubleshoot-user-accounts"></a>Resolver problemas relacionados com contas de utilizador

Se os utilizadores estiverem [dificuldades com verificação de dois passos](../user-help/multi-factor-authentication-end-user-troubleshoot.md), ajudar a problemas de auto-diagnosticá-los.

### <a name="contact-microsoft-support"></a>Contacte o Suporte da Microsoft

Se precisar de mais ajuda, entre em contato com um analista de suporte por meio [suporte de servidor do Azure multi-factor Authentication](https://support.microsoft.com/oas/default.aspx?prid=14947). Quando contactar-nos, é útil se pode incluir o máximo de informações sobre o seu problema quanto possível. Pode fornecer as informações incluem a página em que viu o erro, o código de erro específico, o ID de sessão específico, o ID do utilizador que viu o erro e registos de depuração.

Para recolher os registos de depuração para diagnóstico, utilize os seguintes passos no servidor NPS:

1. Abra o Editor de registo e navegue para o conjunto HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa **VERBOSE_LOG** para **verdadeiro**
2. Abra uma linha de comandos de administrador e execute estes comandos:

   ```
   Mkdir c:\NPS
   Cd NPS
   netsh trace start Scenario=NetConnection capture=yes tracefile=c:\NPS\nettrace.etl
   logman create trace "NPSExtension" -ow -o c:\NPS\NPSExtension.etl -p {7237ED00-E119-430B-AB0F-C63360C8EE81} 0xffffffffffffffff 0xff -nb 16 16 -bs 1024 -mode Circular -f bincirc -max 4096 -ets
   logman update trace "NPSExtension" -p {EC2E6D3A-C958-4C76-8EA4-0262520886FF} 0xffffffffffffffff 0xff -ets
   ```

3. Reproduza o problema

4. Pare o rastreio com estes comandos:

   ```
   logman stop "NPSExtension" -ets
   netsh trace stop
   wevtutil epl AuthNOptCh C:\NPS\%computername%_AuthNOptCh.evtx
   wevtutil epl AuthZOptCh C:\NPS\%computername%_AuthZOptCh.evtx
   wevtutil epl AuthZAdminCh C:\NPS\%computername%_AuthZAdminCh.evtx
   Start .
   ```

5. Abra o Editor de registo e navegue para o conjunto HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa **VERBOSE_LOG** para **FALSO**
6. Zip o conteúdo da pasta C:\NPS e anexe o arquivo zipado para o caso de suporte.
