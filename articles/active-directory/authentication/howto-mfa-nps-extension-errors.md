---
title: Extensão de NPS Azure MFA - Diretório Ativo Azure
description: Obtenha ajuda na resolução de problemas com a extensão nPS para autenticação multi-factor Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3462ada0864d2d8321b1936e94f947c55c754879
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294503"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-multi-factor-authentication"></a>Resolver mensagens de erro da extensão NPS para Multi-Factor Authentication do Azure

Se encontrar erros com a extensão NPS para autenticação de multi-factores Azure, use este artigo para chegar a uma resolução mais rapidamente. Os registos de extensão nPS são encontrados no Visualizador de Eventos sob a Política de Rede de**Funções** > de Servidor de **Visualizações** > Personalizadas**e Serviços** de Acesso no servidor onde a extensão NPS está instalada.

## <a name="troubleshooting-steps-for-common-errors"></a>Passos de resolução de problemas para erros comuns

| Código de erro | Passos de resolução de problemas |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | Suporte de [contato](#contact-microsoft-support), e mencione a lista de passos para recolher registos. Forneça o máximo de informação possível sobre o que aconteceu antes do erro, incluindo id de inquilino, e nome principal do utilizador (UPN). |
| **CLIENT_CERT_INSTALL_ERROR** | Pode haver um problema com a forma como o certificado de cliente foi instalado ou associado ao seu inquilino. Siga as instruções em [Resolução de Problemas da extensão do NPS do MFA](howto-mfa-nps-extension.md#troubleshooting) para investigar problemas cert do cliente. |
| **ESTS_TOKEN_ERROR** | Siga as instruções em [Resolução de Problemas da extensão Do NPS do MFA](howto-mfa-nps-extension.md#troubleshooting) para investigar problemas de fichas do cliente cert e ADAL. |
| **HTTPS_COMMUNICATION_ERROR** | O servidor NPS não pode receber respostas do Azure MFA. Verifique se as suas firewalls estão abertas bidirecionamente para o tráfego de e parahttps://adnotifications.windowsazure.com |
| **HTTP_CONNECT_ERROR** | No servidor que executa a extensão NPS, verifique se pode alcançar `https://adnotifications.windowsazure.com` e `https://login.microsoftonline.com/`. Se esses sites não carregarem, problemas de conectividade no servidor. |
| **Extensão nps para Azure MFA:** <br> A extensão nps para O MFA Azure apenas executa Auth Secundário para pedidos de Raio no AccessAccept State. Pedido recebido para username com estado de resposta AccessReject, ignorando o pedido. | Este erro reflete geralmente uma falha de autenticação em AD ou que o servidor NPS não consegue receber respostas da AD Azure. Verifique se as suas firewalls estão abertas bidirecionamente para o tráfego de e para e para `https://adnotifications.windowsazure.com` as `https://login.microsoftonline.com` portas 80 e 443. É também importante verificar se no separador DIAL-IN das Permissões de Acesso à Rede, a definição está definida para "controlar o acesso através da Política de Rede NPS". Este erro também pode desencadear se o utilizador não for atribuído a uma licença. |
| **REGISTRY_CONFIG_ERROR** | Falta uma chave no registo da aplicação, o que pode ser porque o [script PowerShell](howto-mfa-nps-extension.md#install-the-nps-extension) não foi executado após a instalação. A mensagem de erro deve incluir a chave em falta. Certifique-se de que tem a chave sob HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa. |
| **REQUEST_FORMAT_ERROR** <br> Pedido de Raio em falta o nome de utilizador obrigatório do Raio\Identificador. Verifique se o NPS está a receber pedidos de RADIUS | Este erro geralmente reflete um problema de instalação. A extensão NPS deve ser instalada em servidores NPS que possam receber pedidos RADIUS. Os servidores NPS que são instalados como dependências de serviços como RDG e RRAS não recebem pedidos de raios. A extensão de NPS não funciona quando instalada sobre tais instalações e erros, uma vez que não consegue ler os detalhes do pedido de autenticação. |
| **REQUEST_MISSING_CODE** | Certifique-se de que o protocolo de encriptação de palavras-passe entre os servidores NPS e NAS suporta o método de autenticação secundária que está a usar. **O PAP** suporta todos os métodos de autenticação do Azure MFA na nuvem: chamada telefónica, mensagem de texto de sentido único, notificação de aplicações móveis e código de verificação de aplicações móveis. **CHAPV2** e **EAP** suportam notificação de chamada telefónica e aplicação móvel. |
| **USERNAME_CANONICALIZATION_ERROR** | Verifique se o utilizador está presente na sua instância de Diretório Ativo no local e que o Serviço NPS tem permissões para aceder ao diretório. Se estiver a usar fundos transfronteiriços, [contacte](#contact-microsoft-support) o apoio para mais ajuda. |

### <a name="alternate-login-id-errors"></a>Erros alternativos de identificação de login

| Código de erro | Mensagem de erro | Passos de resolução de problemas |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | Erro: a procura do utilizadorObjectSid falhou | Verifique se o utilizador existe na sua instância de Diretório Ativo no local. Se estiver a usar fundos transfronteiriços, [contacte](#contact-microsoft-support) o apoio para mais ajuda. |
| **ALTERNATE_LOGIN_ID_ERROR** | Erro: A procura de LoginId alternada falhou | Verifique se LDAP_ALTERNATE_LOGINID_ATTRIBUTE está definida para um [atributo de diretório ativo válido](https://msdn.microsoft.com/library/ms675090(v=vs.85).aspx). <br><br> Se LDAP_FORCE_GLOBAL_CATALOG for definido para True, ou LDAP_LOOKUP_FORESTS estiver configurado com um valor não vazio, verifique se configuraum Catálogo Global e se o atributo AlternateLoginId lhe é adicionado. <br><br> Se LDAP_LOOKUP_FORESTS estiver configurado com um valor não vazio, verifique se o valor está correto. Se houver mais de um nome florestal, os nomes devem ser separados com semi-cólons, não com espaços. <br><br> Se estes passos não resolverem o problema, [contacte](#contact-microsoft-support) o suporte para mais ajuda. |
| **ALTERNATE_LOGIN_ID_ERROR** | Erro: Valor de LoginId alternativo está vazio | Verifique se o atributo AlternateLoginId está configurado para o utilizador. |

## <a name="errors-your-users-may-encounter"></a>Erros que os seus utilizadores podem encontrar

| Código de erro | Mensagem de erro | Passos de resolução de problemas |
| ---------- | ------------- | --------------------- |
| **AcessoSNegados** | O inquilino que liga não tem permissões de acesso para fazer a autenticação para o utilizador | Verifique se o domínio do arrendatário e o domínio do nome principal do utilizador (UPN) são os mesmos. Por exemplo, certifique-se de que user@contoso.com está a tentar autenticar o inquilino contoso. A UPN representa um utilizador válido para o inquilino em Azure. |
| **AutenticaçãoMethodNotConfigured** | O método de autenticação especificado não foi configurado para o utilizador | Faça com que o utilizador adicione ou verifique os seus métodos de verificação de acordo com as instruções em Gerir as [suas definições para verificação em duas etapas](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **AutenticaçãoMethodNotSupported** | O método de autenticação especificado não é suportado. | Colete todos os seus registos que incluam este erro e [suporte de contato](#contact-microsoft-support). Quando contactar o suporte, forneça o nome de utilizador e o método de verificação secundário que desencadeou o erro. |
| **BecAccessNegado** | MSODS Bec call acesso devolvido negado, provavelmente o nome de utilizador não é definido no inquilino | O utilizador está presente no Ative Directory no local, mas não está sincronizado em Azure AD by AD Connect. Ou o utilizador está desaparecido para o inquilino. Adicione o utilizador ao Azure AD e faça com que adicione os seus métodos de verificação de acordo com as instruções em Gerir as [definições para verificação em duas etapas](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **Formato inválido** ou **StrongAuthenticationServiceInvalidvalidParameter** | O número de telefone está num formato irreconhecível | Mande o utilizador corrigir os seus números de telefone de verificação. |
| **Sessão inválida** | A sessão especificada é inválida ou pode ter expirado | A sessão demorou mais de três minutos a ser concluída. Verifique se o utilizador está a introduzir o código de verificação ou a responder à notificação da aplicação, no prazo de três minutos após o início do pedido de autenticação. Se isso não resolver o problema, verifique se não existem atrasos de rede entre o cliente, o SERVIDOR NAS, o Servidor NPS e o ponto final do Azure MFA.  |
| **NoDefaultAuthenticationMethodisConfigured** | Nenhum método de autenticação predefinido foi configurado para o utilizador | Faça com que o utilizador adicione ou verifique os seus métodos de verificação de acordo com as instruções em Gerir as [suas definições para verificação em duas etapas](../user-help/multi-factor-authentication-end-user-manage-settings.md). Verifique se o utilizador escolheu um método de autenticação predefinido e configurou esse método para a sua conta. |
| **JuramentoCódigoPinino** | Código errado e pin o pin o introduzido. | Este erro não é esperado na extensão NPS. Se o utilizador o encontrar, [contacte o suporte](#contact-microsoft-support) para ajuda para resolução de problemas. |
| **ProvaDataNotFound** | Os dados da prova não foram configurados para o método de autenticação especificado. | Peça ao utilizador que experimente um método de verificação diferente ou adicione um novo método de verificação de acordo com as instruções em Gerir as [definições para verificação em duas etapas](../user-help/multi-factor-authentication-end-user-manage-settings.md). Se o utilizador continuar a ver este erro depois de ter confirmado que o seu método de verificação está corretamente configurado, [contacte](#contact-microsoft-support)o suporte . |
| **SMSAuthFailedWrongWrongCodePinEntered** | Código errado e pin o pin o introduzido. (OnewaysMs) | Este erro não é esperado na extensão NPS. Se o utilizador o encontrar, [contacte o suporte](#contact-microsoft-support) para ajuda para resolução de problemas. |
| **InquilinoS Bloqueados** | Inquilino está bloqueado | [Suporte de contato](#contact-microsoft-support) com Id de Diretório da página de propriedades da AD Azure no portal Azure. |
| **UserNotFound** | O utilizador especificado não foi encontrado | O inquilino já não é visível como ativo em Azure AD. Verifique se a sua subscrição está ativa e tem as aplicações de primeira parte necessárias. Certifique-se também de que o arrendatário no certificado está como esperado e o certificado ainda é válido e registado no âmbito do serviço principal. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>Mensagens que os seus utilizadores podem encontrar que não são erros

Por vezes, os utilizadores podem receber mensagens da Autenticação Multi-Factor porque o seu pedido de autenticação falhou. Estes não são erros no produto da configuração, mas são avisos intencionais explicando por que um pedido de autenticação foi negado.

| Código de erro | Mensagem de erro | Passos recomendados | 
| ---------- | ------------- | ----------------- |
| **JuramentoCódigoIn** | Código errado introduzido\CÓDIGO OATH Incorreto | O utilizador introduziu o código errado. Peça-lhes que tentem novamente solicitando um novo código ou insintendo novamente. | 
| **SMSAuthFailedMaxAllowedCodeRetryReached** | Máximo permitido retry código alcançado | O utilizador falhou no desafio de verificação demasiadas vezes. Dependendo das suas definições, elas podem precisar de ser desbloqueadas por um administrador agora.  |
| **SMSAuthFailedWrongCodeEntered** | Código errado introduzido/Mensagem de Texto OTP Incorreto | O utilizador introduziu o código errado. Peça-lhes que tentem novamente solicitando um novo código ou insintendo novamente. |

## <a name="errors-that-require-support"></a>Erros que requerem apoio

Se encontrar um destes erros, recomendamos que [contacte](#contact-microsoft-support) o suporte para ajuda de diagnóstico. Não há um conjunto padrão de passos que possa resolver estes erros. Quando você fizer suporte de contato, certifique-se de incluir o máximo de informação possível sobre os passos que levaram a um erro, e a informação do seu inquilino.

| Código de erro | Mensagem de erro |
| ---------- | ------------- |
| **Parâmetro inválido** | Pedido não deve ser nulo |
| **Parâmetro inválido** | O ObjectId não deve ser nulo ou vazio para a ReplicationScope:{0} |
| **Parâmetro inválido** | O comprimento do \{Nome de Empresa 0}\ é maior do que o comprimento máximo permitido{1} |
| **Parâmetro inválido** | UserPrincipalName não deve ser nulo ou vazio |
| **Parâmetro inválido** | O TenantId fornecido não está em formato correto |
| **Parâmetro inválido** | SessionId não deve ser nulo ou vazio |
| **Parâmetro inválido** | Não foi possível resolver qualquer ProofData a pedido ou Msods. O ProofData não é conhecido |
| **InternalError** |  |
| **JuramentoCódigoPinino** |  |
| **VersãoNotSupported** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>Passos seguintes

### <a name="troubleshoot-user-accounts"></a>Conta de utilizador de resolução de problemas

Se os seus utilizadores estiverem com problemas com a [verificação em duas etapas, ajude-os](../user-help/multi-factor-authentication-end-user-troubleshoot.md)a auto-diagnosticar problemas.

### <a name="health-check-script"></a>Roteiro de verificação de saúde

O script de verificação de saúde de extensão De [extensão Do MFA Azure](https://docs.microsoft.com/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/) realiza uma verificação de saúde básica ao resolver problemas na extensão nPS. Executar o guião e escolher a opção 3.

### <a name="contact-microsoft-support"></a>Contacte o Suporte da Microsoft

Se precisar de ajuda adicional, contacte um profissional de suporte através do suporte do Servidor de [Autenticação Multi-Factor Azure](https://support.microsoft.com/oas/default.aspx?prid=14947). Ao contactar-nos, é útil se puder incluir o máximo de informação possível sobre o seu problema. A informação que pode fornecer inclui a página onde viu o erro, o código de erro específico, o ID específico da sessão, o ID do utilizador que viu o erro e depura os registos.

Para recolher registos de depuração para diagnósticos de suporte, utilize os seguintes passos no servidor NPS:

1. Open Registry Editor e navegue para HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa definido **VERBOSE_LOG** para **TRUE**
2. Abra um pedido de comando do Administrador e execute estes comandos:

   ```
   Mkdir c:\NPS
   Cd NPS
   netsh trace start Scenario=NetConnection capture=yes tracefile=c:\NPS\nettrace.etl
   logman create trace "NPSExtension" -ow -o c:\NPS\NPSExtension.etl -p {7237ED00-E119-430B-AB0F-C63360C8EE81} 0xffffffffffffffff 0xff -nb 16 16 -bs 1024 -mode Circular -f bincirc -max 4096 -ets
   logman update trace "NPSExtension" -p {EC2E6D3A-C958-4C76-8EA4-0262520886FF} 0xffffffffffffffff 0xff -ets
   ```

3. Reproduza o problema

4. Pare de rastrear com estes comandos:

   ```
   logman stop "NPSExtension" -ets
   netsh trace stop
   wevtutil epl AuthNOptCh C:\NPS\%computername%_AuthNOptCh.evtx
   wevtutil epl AuthZOptCh C:\NPS\%computername%_AuthZOptCh.evtx
   wevtutil epl AuthZAdminCh C:\NPS\%computername%_AuthZAdminCh.evtx
   Start .
   ```

5. Open Registry Editor e navegue para HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa definido **VERBOSE_LOG** para **FALSO**
6. Feche o conteúdo da pasta C:\NPS e prenda o ficheiro com fecho à caixa de suporte.
