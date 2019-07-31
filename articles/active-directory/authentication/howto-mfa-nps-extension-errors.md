---
title: Solucionar problemas de códigos de erro para a extensão NPS do Azure MFA-Azure Active Directory
description: Obter ajuda para resolver problemas com a extensão NPS para a autenticação multifator do Azure
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
ms.openlocfilehash: c0d04db6e9ccedc1e67ed0cdfd914ab42ebea0b1
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "67536951"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-multi-factor-authentication"></a>Resolver mensagens de erro da extensão do NPS para a autenticação multifator do Azure

Se você encontrar erros com a extensão do NPS para a autenticação multifator do Azure, use este artigo para alcançar uma resolução mais rápida. Os logs de extensão do NPS são encontrados > em Visualizador de eventos em exibições personalizadas**funções** > **de servidor política de rede e serviços de acesso** no servidor em que a extensão NPS está instalada.

## <a name="troubleshooting-steps-for-common-errors"></a>Etapas de solução de problemas para erros comuns

| Código de erro | Passos de resolução de problemas |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [Contate o suporte](#contact-microsoft-support)e mencione a lista de etapas para coletar logs. Forneça o máximo possível de informações sobre o que aconteceu antes do erro, incluindo a ID do locatário e o UPN (nome principal do usuário). |
| **CLIENT_CERT_INSTALL_ERROR** | Pode haver um problema com o modo como o certificado do cliente foi instalado ou associado ao seu locatário. Siga as instruções em [solução de problemas de extensão de NPS do MFA](howto-mfa-nps-extension.md#troubleshooting) para investigar problemas de certificado de cliente. |
| **ESTS_TOKEN_ERROR** | Siga as instruções em [solução de problemas de extensão de NPS do MFA](howto-mfa-nps-extension.md#troubleshooting) para investigar problemas de certificado de cliente e token de Adal. |
| **HTTPS_COMMUNICATION_ERROR** | O servidor NPS não pode receber respostas do Azure MFA. Verifique se os firewalls estão abertos bidirecionalmente para o tráfego de e para https://adnotifications.windowsazure.com |
| **HTTP_CONNECT_ERROR** | No servidor que executa a extensão NPS, verifique se você pode acessar https://adnotifications.windowsazure.com e. https://login.microsoftonline.com/ Se esses sites não forem carregados, solucione problemas de conectividade nesse servidor. |
| **Extensão do NPS para o Azure MFA:** <br> A extensão do NPS para o Azure MFA só executa autenticação secundária para solicitações RADIUS no estado AccessAccept. Solicitação recebida para o usuário username com o estado de resposta AccessReject, ignorando a solicitação. | Esse erro geralmente reflete uma falha de autenticação no AD ou que o servidor NPS não consegue receber respostas do Azure AD. Verifique se os firewalls estão abertos bidirecionalmente para o tráfego de e https://adnotifications.windowsazure.com https://login.microsoftonline.com para o usando as portas 80 e 443. Também é importante verificar isso na guia DISCAgem de permissões de acesso à rede, a configuração é definida como "controlar o acesso por meio da política de rede do NPS". Esse erro também pode disparar se o usuário não tiver uma licença atribuída. |
| **REGISTRY_CONFIG_ERROR** | Falta uma chave no registro para o aplicativo, o que pode ser porque o [script do PowerShell](howto-mfa-nps-extension.md#install-the-nps-extension) não foi executado após a instalação. A mensagem de erro deve incluir a chave ausente. Verifique se você tem a chave em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa. |
| **REQUEST_FORMAT_ERROR** <br> Solicitação RADIUS ausente atributo userName\Identifier de RADIUS obrigatório. Verifique se o NPS está recebendo solicitações RADIUS | Esse erro geralmente reflete um problema de instalação. A extensão do NPS deve ser instalada em servidores NPS que podem receber solicitações RADIUS. Os servidores NPS instalados como dependências de serviços como RDG e RRAS não recebem solicitações RADIUS. A extensão do NPS não funciona quando instalada em tais instalações e erros, pois não pode ler os detalhes da solicitação de autenticação. |
| **REQUEST_MISSING_CODE** | Certifique-se de que o protocolo de criptografia de senha entre os servidores NPS e NAS oferece suporte ao método de autenticação secundário que você está usando. O **PAP** dá suporte a todos os métodos de autenticação do Azure MFA na nuvem: chamada telefônica, mensagem de texto unidirecional, notificação de aplicativo móvel e código de verificação de aplicativo móvel. **CHAPv2** e **EAP** dão suporte a chamada telefônica e notificação de aplicativo móvel. |
| **USERNAME_CANONICALIZATION_ERROR** | Verifique se o usuário está presente em sua instância de Active Directory local e se o serviço NPS tem permissões para acessar o diretório. Se você estiver usando relações de confiança entre florestas, [entre em contato com o suporte](#contact-microsoft-support) para obter mais ajuda. |

### <a name="alternate-login-id-errors"></a>Erros de ID de logon alternativo

| Código de erro | Mensagem de erro | Passos de resolução de problemas |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | Erro: falha na pesquisa de userobjectid | Verifique se o usuário existe em sua instância de Active Directory local. Se você estiver usando relações de confiança entre florestas, [entre em contato com o suporte](#contact-microsoft-support) para obter mais ajuda. |
| **ALTERNATE_LOGIN_ID_ERROR** | Erro: Falha na pesquisa de LoginID alternativa | Verifique se LDAP_ALTERNATE_LOGINID_ATTRIBUTE está definido como um [atributo válido do Active Directory](https://msdn.microsoft.com/library/ms675090(v=vs.85).aspx). <br><br> Se LDAP_FORCE_GLOBAL_CATALOG for definido como true ou LDAP_LOOKUP_FORESTS estiver configurado com um valor não vazio, verifique se você configurou um catálogo global e se o atributo AlternateLoginId foi adicionado a ele. <br><br> Se LDAP_LOOKUP_FORESTS estiver configurado com um valor não vazio, verifique se o valor está correto. Se houver mais de um nome de floresta, os nomes deverão ser separados por ponto e vírgula, não espaços. <br><br> Se essas etapas não corrigirem o problema, [contate o suporte](#contact-microsoft-support) para obter mais ajuda. |
| **ALTERNATE_LOGIN_ID_ERROR** | Erro: O valor de LoginID alternativo está vazio | Verifique se o atributo AlternateLoginId está configurado para o usuário. |

## <a name="errors-your-users-may-encounter"></a>Erros que os usuários podem encontrar

| Código de erro | Mensagem de erro | Passos de resolução de problemas |
| ---------- | ------------- | --------------------- |
| **AccessDenied** | O locatário do chamador não tem permissões de acesso para fazer a autenticação para o usuário | Verifique se o domínio do locatário e o domínio do nome principal do usuário (UPN) são os mesmos. Por exemplo, verifique user@contoso.com se o está tentando se autenticar no locatário da contoso. O UPN representa um usuário válido para o locatário no Azure. |
| **AuthenticationMethodNotConfigured** | O método de autenticação especificado não foi configurado para o usuário | Faça com que o usuário adicione ou verifique seus métodos de verificação de acordo com as instruções em [gerenciar suas configurações para verificação em duas etapas](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **AuthenticationMethodNotSupported** | Não há suporte para o método de autenticação especificado. | Colete todos os logs que incluem esse erro e [contate o suporte](#contact-microsoft-support). Quando você entrar em contato com o suporte, forneça o nome de usuário e o método de verificação secundário que disparou o erro. |
| **BecAccessDenied** | Chamada MSODS Bec retornou acesso negado, provavelmente o nome de usuário não está definido no locatário | O usuário está presente no Active Directory local, mas não é sincronizado no Azure AD pelo AD Connect. Ou o usuário está ausente para o locatário. Adicione o usuário ao Azure AD e faça com que eles adicionem seus métodos de verificação de acordo com as instruções em [gerenciar suas configurações para a verificação em duas etapas](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **InvalidFormat** ou **StrongAuthenticationServiceInvalidParameter** | O número de telefone está em um formato irreconhecível | Faça com que o usuário corrija seus números de telefone de verificação. |
| **InvalidSession** | A sessão especificada é inválida ou pode ter expirado | A sessão levou mais de três minutos para ser concluída. Verifique se o usuário está inserindo o código de verificação ou respondendo à notificação do aplicativo em até três minutos após iniciar a solicitação de autenticação. Se isso não corrigir o problema, verifique se não há nenhuma latência de rede entre o cliente, o servidor NAS, o servidor NPS e o ponto de extremidade do Azure MFA.  |
| **NoDefaultAuthenticationMethodIsConfigured** | Nenhum método de autenticação padrão foi configurado para o usuário | Faça com que o usuário adicione ou verifique seus métodos de verificação de acordo com as instruções em [gerenciar suas configurações para verificação em duas etapas](../user-help/multi-factor-authentication-end-user-manage-settings.md). Verifique se o usuário escolheu um método de autenticação padrão e configurou esse método para sua conta. |
| **OathCodePinIncorrect** | Código errado e PIN inseridos. | Esse erro não é esperado na extensão do NPS. Se o usuário encontrar isso, [entre em contato com o suporte](#contact-microsoft-support) para obter ajuda para solução de problemas. |
| **ProofDataNotFound** | Os dados de prova não foram configurados para o método de autenticação especificado. | Faça com que o usuário tente um método de verificação diferente ou adicione novos métodos de verificação de acordo com as instruções em [gerenciar suas configurações para a verificação em duas etapas](../user-help/multi-factor-authentication-end-user-manage-settings.md). Se o usuário continuar a ver esse erro depois de confirmar que o método de verificação está configurado corretamente, [contate o suporte](#contact-microsoft-support). |
| **SMSAuthFailedWrongCodePinEntered** | Código errado e PIN inseridos. (OneWaySMS) | Esse erro não é esperado na extensão do NPS. Se o usuário encontrar isso, [entre em contato com o suporte](#contact-microsoft-support) para obter ajuda para solução de problemas. |
| **TenantIsBlocked** | O locatário está bloqueado | [Contate o suporte](#contact-microsoft-support) com a ID de diretório na página de propriedades do Azure AD no portal do Azure. |
| **UserNotFound** | O usuário especificado não foi encontrado | O locatário não está mais visível como ativo no Azure AD. Verifique se sua assinatura está ativa e se você tem os aplicativos de primeira parte necessários. Verifique também se o locatário na entidade do certificado está conforme o esperado e se o certificado ainda é válido e registrado na entidade de serviço. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>Mensagens que os usuários podem encontrar que não são erros

Às vezes, os usuários podem receber mensagens da autenticação multifator porque a solicitação de autenticação falhou. Esses não são erros no produto de configuração, mas são avisos intencionais explicando por que uma solicitação de autenticação foi negada.

| Código de erro | Mensagem de erro | Passos recomendados | 
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | Código errado de entered\OATH de código incorreto | O usuário inseriu o código errado. Faça com que eles tentem novamente solicitando um novo código ou entrando novamente. | 
| **SMSAuthFailedMaxAllowedCodeRetryReached** | Repetição de código máximo permitida atingida | O usuário falhou o desafio de verificação muitas vezes. Dependendo de suas configurações, talvez eles precisem ser desbloqueados por um administrador agora.  |
| **SMSAuthFailedWrongCodeEntered** | Código errado inserido/OTP de mensagem de texto incorreto | O usuário inseriu o código errado. Faça com que eles tentem novamente solicitando um novo código ou entrando novamente. |

## <a name="errors-that-require-support"></a>Erros que exigem suporte

Se você encontrar um desses erros, recomendamos que [entre em contato com o suporte](#contact-microsoft-support) para obter ajuda de diagnóstico. Não há um conjunto padrão de etapas que possam resolver esses erros. Quando você fizer contato com o suporte, certifique-se de incluir o máximo possível de informações sobre as etapas que levaram a um erro e suas informações de locatário.

| Código de erro | Mensagem de erro |
| ---------- | ------------- |
| **InvalidParameter** | A solicitação não deve ser nula |
| **InvalidParameter** | ObjectId não deve ser nulo ou vazio para ReplicationScope:{0} |
| **InvalidParameter** | O comprimento de CompanyName \{0} \ é maior que o comprimento máximo permitido{1} |
| **InvalidParameter** | UserPrincipalName não deve ser nulo ou vazio |
| **InvalidParameter** | A Tenantid fornecida não está no formato correto |
| **InvalidParameter** | SessionId não deve ser nulo ou vazio |
| **InvalidParameter** | Não foi possível resolver nenhum ProofData da solicitação ou Msods. O ProofData é desconhecido |
| **InternalError** |  |
| **OathCodePinIncorrect** |  |
| **VersionNotSupported** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>Passos seguintes

### <a name="troubleshoot-user-accounts"></a>Solucionar problemas de contas de usuário

Se os usuários estiverem [tendo problemas com a verificação em duas etapas](../user-help/multi-factor-authentication-end-user-troubleshoot.md), ajude-os a diagnosticar problemas automaticamente.

### <a name="contact-microsoft-support"></a>Contacte o Suporte da Microsoft

Se precisar de ajuda adicional, entre em contato com um profissional de suporte por meio [do suporte do servidor de autenticação multifator do Azure](https://support.microsoft.com/oas/default.aspx?prid=14947). Ao entrar em contato conosco, será útil se você puder incluir o máximo possível de informações sobre o seu problema. As informações que você pode fornecer incluem a página em que você viu o erro, o código de erro específico, a ID de sessão específica, a ID do usuário que viu o erro e os logs de depuração.

Para coletar logs de depuração para o diagnóstico de suporte, use as seguintes etapas no servidor NPS:

1. Abra o editor do registro e navegue até HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa Set **VERBOSE_LOG** to **true**
2. Abra um prompt de comando do administrador e execute estes comandos:

   ```
   Mkdir c:\NPS
   Cd NPS
   netsh trace start Scenario=NetConnection capture=yes tracefile=c:\NPS\nettrace.etl
   logman create trace "NPSExtension" -ow -o c:\NPS\NPSExtension.etl -p {7237ED00-E119-430B-AB0F-C63360C8EE81} 0xffffffffffffffff 0xff -nb 16 16 -bs 1024 -mode Circular -f bincirc -max 4096 -ets
   logman update trace "NPSExtension" -p {EC2E6D3A-C958-4C76-8EA4-0262520886FF} 0xffffffffffffffff 0xff -ets
   ```

3. Reproduza o problema

4. Pare o rastreamento com estes comandos:

   ```
   logman stop "NPSExtension" -ets
   netsh trace stop
   wevtutil epl AuthNOptCh C:\NPS\%computername%_AuthNOptCh.evtx
   wevtutil epl AuthZOptCh C:\NPS\%computername%_AuthZOptCh.evtx
   wevtutil epl AuthZAdminCh C:\NPS\%computername%_AuthZAdminCh.evtx
   Start .
   ```

5. Abra o editor do registro e navegue até HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa Set **VERBOSE_LOG** to **false**
6. Compacte o conteúdo da pasta C:\NPS e anexe o arquivo compactado ao caso de suporte.
