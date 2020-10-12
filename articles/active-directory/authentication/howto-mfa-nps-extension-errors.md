---
title: Extensão NPS Azure MFA de resolução de problemas - Diretório Ativo Azure
description: Obtenha ajuda para resolver problemas com a extensão NPS para autenticação multi-factor Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: f991e38c184fe44f63af63809deb14eda22f8f4c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88716729"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-multi-factor-authentication"></a>Resolver mensagens de erro da extensão NPS para Multi-Factor Authentication do Azure

Se encontrar erros com a extensão NPS para autenticação multi-factor Azure, utilize este artigo para chegar a uma resolução mais rapidamente. Os registos de extensão NPS são encontrados no Espectador de Eventos sob a política de política de funções do servidor de **visualizações**  >  **Server Roles**  >  **personalizadas e serviços** de acesso no servidor onde a extensão NPS está instalada.

## <a name="troubleshooting-steps-for-common-errors"></a>Etapas de resolução de problemas para erros comuns

| Código de erro | Passos de resolução de problemas |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [Suporte de contato](#contact-microsoft-support), e mencione a lista de passos para a recolha de registos. Forneça o máximo de informação possível sobre o que aconteceu antes do erro, incluindo o ID do inquilino, e o nome principal do utilizador (UPN). |
| **CLIENT_CERT_INSTALL_ERROR** | Pode haver um problema com a forma como o certificado de cliente foi instalado ou associado ao seu inquilino. Siga as instruções na [resolução de problemas da extensão MFA NPS](howto-mfa-nps-extension.md#troubleshooting) para investigar os problemas do certificado do cliente. |
| **ESTS_TOKEN_ERROR** | Siga as instruções em [Resolução de Problemas da extensão MFA NPS](howto-mfa-nps-extension.md#troubleshooting) para investigar problemas de ficha de cliente e sinal de ADAL. |
| **HTTPS_COMMUNICATION_ERROR** | O servidor NPS não consegue receber respostas do Azure MFA. Verifique se as suas firewalls estão abertas bidireccionalmente para o tráfego de e para https://adnotifications.windowsazure.com |
| **HTTP_CONNECT_ERROR** | No servidor que executa a extensão NPS, verifique se pode alcançar  `https://adnotifications.windowsazure.com` e `https://login.microsoftonline.com/` . Se esses sites não carregarem, resolvam a conectividade no servidor. |
| **Extensão NPS para Azure MFA:** <br> A extensão NPS para Azure MFA apenas executa Auth Secundário para pedidos de Raio no Estado AccessAccept. Pedido recebido para nome de utilizador do utilizador com resposta estado AccessReject, ignorando o pedido. | Este erro normalmente reflete uma falha de autenticação em AD ou que o servidor NPS não consegue receber respostas do Azure AD. Verifique se as suas firewalls estão abertas bidireccionalmente para o tráfego de e para `https://adnotifications.windowsazure.com` e para as portas `https://login.microsoftonline.com` 80 e 443. É igualmente importante verificar se no separador DIAL-IN das Permissões de Acesso à Rede, a definição está definida para "controlar o acesso através da Política de Rede NPS". Este erro também pode desencadear se o utilizador não tiver uma licença. |
| **REGISTRY_CONFIG_ERROR** | Falta uma chave no registo da aplicação, o que pode ser porque o [script PowerShell](howto-mfa-nps-extension.md#install-the-nps-extension) não foi executado após a instalação. A mensagem de erro deve incluir a chave em falta. Certifique-se de ter a chave debaixo de HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa. |
| **REQUEST_FORMAT_ERROR** <br> Pedido de raio Falta o atributo obrigatório do utilizador RadiusName\Identifier. Verifique se o NPS está a receber pedidos RADIUS | Este erro normalmente reflete um problema de instalação. A extensão NPS deve ser instalada em servidores NPS que possam receber pedidos RADIUS. Os servidores NPS que são instalados como dependências de serviços como RDG e RRAS não recebem pedidos de raio. A extensão NPS não funciona quando instalada sobre tais instalações e erros, uma vez que não consegue ler os detalhes do pedido de autenticação. |
| **REQUEST_MISSING_CODE** | Certifique-se de que o protocolo de encriptação de palavra-passe entre os servidores NPS e NAS suporta o método de autenticação secundária que está a utilizar. **O PAP** suporta todos os métodos de autenticação do Azure MFA na nuvem: chamada telefónica, mensagem de texto unidirecionais, notificação de aplicações móveis e código de verificação de aplicações móveis. **CHAPV2** e **EAP** suportam chamada telefónica e notificação de aplicativos móveis. |
| **USERNAME_CANONICALIZATION_ERROR** | Verifique se o utilizador está presente no seu local de acesso ative, e que o Serviço NPS tem permissões para aceder ao diretório. Se estiver a utilizar fundos florestais, [contacte o suporte](#contact-microsoft-support) para mais ajuda. |

### <a name="alternate-login-id-errors"></a>Erros de ID de login alternativos

| Código de erro | Mensagem de erro | Passos de resolução de problemas |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | Erro: o lookup do userObjectSid falhou | Verifique se o utilizador existe no seu local de identificação do Ative Directory. Se estiver a utilizar fundos florestais, [contacte o suporte](#contact-microsoft-support) para mais ajuda. |
| **ALTERNATE_LOGIN_ID_ERROR** | Erro: O lookup alternativo do LoginId falhou | Verifique se LDAP_ALTERNATE_LOGINID_ATTRIBUTE está definido para um [atributo de diretório ativo válido](/windows/win32/adschema/attributes-all). <br><br> Se LDAP_FORCE_GLOBAL_CATALOG estiver definido para True, ou LDAP_LOOKUP_FORESTS estiver configurado com um valor não vazio, verifique se configura um Catálogo Global e que o atributo AlternateLoginId lhe é adicionado. <br><br> Se LDAP_LOOKUP_FORESTS estiver configurado com um valor não vazio, verifique se o valor está correto. Se houver mais de um nome florestal, os nomes devem ser separados com pontos e outros, não com espaços. <br><br> Se estes passos não resolverem o problema, [contacte o suporte](#contact-microsoft-support) para mais ajuda. |
| **ALTERNATE_LOGIN_ID_ERROR** | Erro: O valor alternativo do LoginId está vazio | Verifique se o atributo AlternateLoginId está configurado para o utilizador. |

## <a name="errors-your-users-may-encounter"></a>Erros que os seus utilizadores podem encontrar

| Código de erro | Mensagem de erro | Passos de resolução de problemas |
| ---------- | ------------- | --------------------- |
| **AcessoDenied** | O inquilino de chamada não tem permissões de acesso para fazer autenticação para o utilizador | Verifique se o domínio do inquilino e o domínio do nome principal do utilizador (UPN) são os mesmos. Por exemplo, certifique-se de que user@contoso.com está tentando autenticar para o inquilino Contoso. A UPN representa um utilizador válido para o arrendatário em Azure. |
| **AutenticaçãoMethodNot Configurado** | O método de autenticação especificado não foi configurado para o utilizador | Mande o utilizador adicionar ou verificar os seus métodos de verificação de acordo com as instruções na [Gestão das suas definições para verificação em duas etapas](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **AutenticaçãoMethodNotSupiada** | O método de autenticação especificado não é suportado. | Recolha todos os seus registos que incluam este erro e [suporte de contato](#contact-microsoft-support). Quando contactar o suporte, forneça o nome de utilizador e o método de verificação secundário que desencadeou o erro. |
| **BecAccessDenied** | MSODS Bec call acesso devolvido negado, provavelmente o nome de utilizador não é definido no inquilino | O utilizador está presente no Ative Directory no local, mas não está sincronizado no AZure AD por AD Connect. Ou o utilizador está desaparecido para o inquilino. Adicione o utilizador ao Azure AD e faça-o adicionar os seus métodos de verificação de acordo com as instruções na [Gestão das suas definições para verificação em duas etapas](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **InvalidFormat** ou **StrongAuthenticationServiceInvalidParameter** | O número de telefone está num formato irreconhecível | Que o utilizador corrija os seus números de telefone de verificação. |
| **Inválida Sesessão** | A sessão especificada é inválida ou pode ter expirado | A sessão demorou mais de três minutos a ser concluída. Verifique se o utilizador está a introduzir o código de verificação, ou a responder à notificação da aplicação, no prazo de três minutos após o início do pedido de autenticação. Se isso não resolver o problema, verifique se não existem latências de rede entre o cliente, o SERVIDOR NAS, o Servidor NPS e o ponto final do Azure MFA.  |
| **NoDefaultAuthenticationMethodIs Configurado** | Nenhum método de autenticação predefinido foi configurado para o utilizador | Mande o utilizador adicionar ou verificar os seus métodos de verificação de acordo com as instruções na [Gestão das suas definições para verificação em duas etapas](../user-help/multi-factor-authentication-end-user-manage-settings.md). Verifique se o utilizador escolheu um método de autenticação predefinido e configura este método para a sua conta. |
| **OathCodePinIncorrect** | Código errado e pin introduzido. | Este erro não é esperado na extensão de NPS. Se o seu utilizador encontrar isto, [contacte o suporte](#contact-microsoft-support) para a resolução de problemas. |
| **ProofDataNotFound** | Os dados de prova não foram configurados para o método de autenticação especificado. | Faça com que o utilizador experimente um método de verificação diferente ou adicione um novo método de verificação de acordo com as instruções na [Gestão das suas definições para verificação em duas etapas](../user-help/multi-factor-authentication-end-user-manage-settings.md). Se o utilizador continuar a ver este erro depois de ter confirmado que o seu método de verificação está corretamente configurado, [contacte o suporte](#contact-microsoft-support). |
| **SMSAuthFailedWrongCodePinEntered** | Código errado e pin introduzido. (OneWaysms) | Este erro não é esperado na extensão de NPS. Se o seu utilizador encontrar isto, [contacte o suporte](#contact-microsoft-support) para a resolução de problemas. |
| **InquilinoS Bloqueados** | O inquilino está bloqueado. | [Suporte de contato](#contact-microsoft-support) com o *ID* do Inquilino a partir da página de propriedades Azure AD no portal Azure. |
| **UserNotFound** | O utilizador especificado não foi encontrado | O inquilino já não é visível como ativo em Azure AD. Verifique se a sua subscrição está ativa e que tem as aplicações de primeira parte necessárias. Certifique-se também de que o arrendatário na certidão está como esperado e que o certificado ainda é válido e registado sob o comando do serviço. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>Mensagens que os seus utilizadores podem encontrar que não são erros

Por vezes, os seus utilizadores podem receber mensagens de Autenticação Multi-Factor porque o seu pedido de autenticação falhou. Não se trata de erros no produto da configuração, mas são avisos intencionais que explicam porque é que um pedido de autenticação foi negado.

| Código de erro | Mensagem de erro | Passos recomendados |
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | Código errado introduzido\Código OATH Incorreto | O utilizador introduziu o código errado. Faça-os tentar novamente, solicitando um novo código ou assinando novamente. |
| **SMSAuthFailedMaxAllowedCodeRetryReached** | Máximo permitido de redandição de código atingido | O utilizador falhou demasiado no desafio de verificação. Dependendo das suas definições, podem ter de ser desbloqueadas por um administrador agora.  |
| **SMSAuthFailedWrongCodeEntered** | Código errado introduzido/Mensagem de Texto OTP Incorreto | O utilizador introduziu o código errado. Faça-os tentar novamente, solicitando um novo código ou assinando novamente. |

## <a name="errors-that-require-support"></a>Erros que requerem suporte

Se encontrar um destes erros, recomendamos que [contacte o suporte](#contact-microsoft-support) para obter ajuda de diagnóstico. Não há um conjunto padrão de passos que possam resolver estes erros. Quando você fizer suporte de contato, certifique-se de incluir o máximo de informação possível sobre os passos que levaram a um erro, e a informação do seu inquilino.

| Código de erro | Mensagem de erro |
| ---------- | ------------- |
| **InvalidParameter** | O pedido não deve ser nulo |
| **InvalidParameter** | O ObjectId não deve ser nulo ou vazio para o ReplicationScope:{0} |
| **InvalidParameter** | O comprimento do Nome da Empresa \{ 0}\ é maior do que o comprimento máximo permitido {1} |
| **InvalidParameter** | UserPrincipalName não deve ser nula ou vazia |
| **InvalidParameter** | O TenantId fornecido não está no formato correto |
| **InvalidParameter** | SessionId não deve ser nulo ou vazio |
| **InvalidParameter** | Não foi possível resolver qualquer ProofData a partir de pedido ou Msods. O ProofData não é conhecido |
| **InternalError** |  |
| **OathCodePinIncorrect** |  |
| **VersãoNotSupportada** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>Passos seguintes

### <a name="troubleshoot-user-accounts"></a>Contas de utilizadores de resolução de problemas

Se os seus utilizadores [estiverem com problemas de verificação em duas etapas, ajude-os](../user-help/multi-factor-authentication-end-user-troubleshoot.md)a diagnosticar problemas.

### <a name="health-check-script"></a>Roteiro de verificação de saúde

O [script de verificação de saúde de extensão Azure MFA NPS](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/) realiza uma verificação de saúde básica ao resolver problemas na extensão de NPS. Executar o script e escolher a opção 3.

### <a name="contact-microsoft-support"></a>Contacte o Suporte da Microsoft

Se precisar de ajuda adicional, contacte um profissional de suporte através do [suporte do Servidor de Autenticação Multi-Factor Azure](https://support.microsoft.com/oas/default.aspx?prid=14947). Ao contactar-nos, é útil se puder incluir o máximo de informação possível sobre o seu problema. A informação que pode fornecer inclui a página onde viu o erro, o código de erro específico, o ID de sessão específico, o ID do utilizador que viu o erro e registos de depurar.

Para recolher registos de depurg para diagnósticos de suporte, utilize os seguintes passos no servidor NPS:

1. Editor de Registo Aberto e navegue para HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa definir **VERBOSE_LOG** à **TRUE**
2. Abra um pedido de comando do administrador e execute estes comandos:

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

5. Editor de Registo Aberto e navegue para HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa definir **VERBOSE_LOG** a **FALSO**
6. Feche o conteúdo da pasta C:\NPS e prenda o ficheiro com fecho à caixa de suporte.