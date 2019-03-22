---
title: Utilize os servidores NPS existentes para fornecer capacidades de MFA do Azure - Azure Active Directory
description: Adicionar capacidades de verificação de dois passos com base na cloud à sua infraestrutura existente de autenticação
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b36b6e513e382e25f7d7038f49e7467a21686a0f
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311735"
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>Integrar a infraestrutura NPS existente com o Azure multi-factor Authentication

A extensão de servidor de políticas de rede (NPS) para o MFA do Azure adiciona capacidades MFA com base na cloud à sua infraestrutura de autenticação através de seus servidores existentes. Com a extensão NPS, pode adicionar chamada telefónica, mensagem de texto ou verificação de aplicação do telefone para o fluxo de autenticação existentes sem ter de instalar, configurar e manter novos servidores. 

Esta extensão foi criada para organizações que pretendem proteger ligações VPN sem ter de implementar o servidor MFA do Azure. A extensão NPS age como um adaptador entre RADIUS e baseado na nuvem do MFA do Azure para fornecer um segundo fator de autenticação para federada ou utilizadores sincronizados.

Ao utilizar a extensão NPS da MFA do Azure, o fluxo de autenticação inclui os seguintes componentes: 

1. **Servidor NAS/VPN** recebe pedidos de clientes VPN e converte-os em pedidos RADIUS para servidores NPS. 
2. **Servidor NPS** conectado ao Active Directory para efetuar a autenticação primária para as solicitações RADIUS e, após a conclusão bem-sucedida, transmite o pedido para quaisquer extensões instaladas.  
3. **Extensão de NPS** aciona uma solicitação para o MFA do Azure para a autenticação secundária. Depois da extensão recebe a resposta e, se a submissão da MFA for bem-sucedida, concluir o pedido de autenticação ao fornecer o servidor NPS com tokens de segurança que incluem uma afirmação de MFA, emitidos pelo STS do Azure.  
4. **MFA do Azure** comunica com o Azure Active Directory para obter os detalhes do utilizador e efetua a autenticação secundária através de um método de verificação configurado para o utilizador.

O diagrama seguinte ilustra esse fluxo de pedido de autenticação de alto nível: 

![Diagrama de fluxo de autenticação](./media/howto-mfa-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Planear a sua implementação

A extensão NPS processa automaticamente a redundância, pelo que não tem uma configuração especial.

Pode criar quantos servidores de ativado o Azure MFA NPS conforme necessário. Se instalar vários servidores, deve usar um certificado de cliente de diferença para cada um deles. Criar um certificado para cada servidor significa que pode atualizar cada certificado individualmente e não se preocupar sobre o tempo de inatividade em todos os seus servidores.

Servidores VPN de encaminham os pedidos de autenticação, para que eles precisam estar cientes dos novos servidores NPS habilitados para o MFA do Azure.

## <a name="prerequisites"></a>Pré-requisitos

A extensão NPS é destinada a trabalhar com a infraestrutura existente. Certifique-se de que tem os seguintes pré-requisitos antes de começar.

### <a name="licenses"></a>Licenças

A extensão NPS da MFA do Azure está disponível para clientes com [licenças para o Azure multi-factor Authentication](multi-factor-authentication.md) (incluído com o Azure AD Premium, EMS ou uma licença do MFA autónoma). Baseado no consumo licenças do MFA do Azure, como por utilizador ou por licenças de autenticação não são compatíveis com a extensão NPS. 

### <a name="software"></a>Software

Windows Server 2008 R2 SP1 ou superior.

### <a name="libraries"></a>Bibliotecas

Essas bibliotecas são instaladas automaticamente com a extensão.

- [Visual C++ Redistributable Packages para Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [Microsoft módulo Azure Active Directory para Windows PowerShell versão 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

A Microsoft módulo Azure Active Directory para Windows PowerShell está instalado, se ainda não estiver presente, através de um script de configuração, executar como parte do processo de configuração. Não é necessário para instalar este módulo antes do tempo, se não estiver já instalado.

### <a name="azure-active-directory"></a>Azure Active Directory

Todos os utilizadores com a extensão NPS têm de ser sincronizados para o Azure Active Directory com o Azure AD Connect e tem de estar registado para MFA.

Quando instalar a extensão, tem das credenciais de ID e a administração de diretório para o seu inquilino do Azure AD. Pode encontrar o seu ID de diretório no [portal do Azure](https://portal.azure.com). Inicie sessão como administrador, selecione o **do Azure Active Directory** ícone à esquerda, em seguida, selecione **propriedades**. Copie o GUID no **ID de diretório** caixa e guarde-o. Vai utilizar este GUID como o ID de inquilino, quando instalar a extensão NPS.

![Encontrar o seu ID de diretório nas propriedades do Azure Active Directory](./media/howto-mfa-nps-extension/find-directory-id.png)

### <a name="network-requirements"></a>Requisitos da rede

O servidor NPS tem de conseguir comunicar com os seguintes URLs através das portas 80 e 443.

* https://adnotifications.windowsazure.com  
* https://login.microsoftonline.com

## <a name="prepare-your-environment"></a>Preparar o ambiente

Antes de instalar a extensão NPS, que pretende preparar o ambiente para processar o tráfego de autenticação.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Ativar a função NPS num servidor associado a um domínio

O servidor NPS estabelece ligação ao Azure Active Directory e autentica os pedidos MFA. Escolha um servidor para esta função. Recomendamos que escolha um servidor que não lida com pedidos de outros serviços, porque a extensão NPS lança os erros para todos os pedidos que não são RADIUS. O servidor NPS tem de ser definido como o servidor de autenticação primária e secundária para seu ambiente; Não é possível pedidos de proxy de RADIUS para outro servidor.

1. No seu servidor, abra a **Assistente Adicionar funções e funcionalidades** no menu de início rápido do Gestor de servidores.
2. Escolher **instalação baseada em funções ou baseada em recursos** para o seu tipo de instalação.
3. Selecione o **serviços de acesso e política de rede** função de servidor. Uma janela poderá ser exibido para informar sobre as funcionalidades necessárias para executar esta função.
4. Continue através do assistente até a página de confirmação. Selecione **Instalar**.

Agora que tem um servidor designado para NPS, também deve configurar este servidor para processar os pedidos recebidos de RADIUS da solução de VPN.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Configurar a sua solução VPN para comunicar com o servidor NPS

Dependendo de qual solução VPN que utilizar, os passos para configurar a política de autenticação RADIUS variam. Configure esta política para apontar para o servidor NPS de RADIUS.

### <a name="sync-domain-users-to-the-cloud"></a>Sincronização de utilizadores de domínio para a cloud

Este passo pode já estar concluído no seu inquilino, mas é bom verificar que o Azure AD Connect foi sincronizado seus bancos de dados recentemente.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Selecione **do Azure Active Directory** > **do Azure AD Connect**
3. Verifique se o estado de sincronização **ativado** e que foi de sua última sincronização há menos de uma hora.

Se precisar de iniciar uma nova rodada de sincronização,-nas instruções no [do Azure AD Connect: Scheduler](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler) (Sincronização do Azure AD Connect: Scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Determinar quais métodos de autenticação, podem utilizar os seus utilizadores

Existem dois fatores que afetam os métodos de autenticação estão disponíveis com uma implementação de extensão NPS:

1. O algoritmo de encriptação da palavra-passe utilizado entre o cliente RADIUS (VPN, o servidor do Netscaler, ou outros) e os servidores NPS.
   - **PAP** suporta todos os métodos de autenticação de MFA do Azure na cloud: chamada telefónica, mensagem de texto unidirecional, notificação de aplicação móvel e o código de verificação de aplicação móvel.
   - **CHAPV2** e **EAP** suporte a chamadas telefónicas e de notificação de aplicação móvel.
2. Os métodos de entrada que o aplicativo de cliente (VPN, o servidor do Netscaler, ou outros) pode processar. Por exemplo, o que o cliente VPN tem algumas formas de permitir que o usuário digite um código de verificação a partir de um texto ou aplicação móvel?

Ao implementar a extensão NPS, utilize estes fatores para avaliar a quais métodos estão disponíveis para os seus utilizadores. Se o cliente RADIUS suporta PAP, mas o experiência do Usuário do cliente não tem campos de entrada para um código de verificação, em seguida, chamadas telefónicas e de notificação de aplicação móvel são as duas opções suportadas.

Pode [desativar métodos de autenticação não suportado](howto-mfa-mfasettings.md#verification-methods) no Azure.

### <a name="register-users-for-mfa"></a>Registar os utilizadores na MFA

Antes de implementar e utilizar a extensão NPS, os utilizadores que são necessários para efetuar a verificação de dois passos tem de ser registado para MFA. Mais imediatamente, para testar a extensão, como implementá-lo, terá de, pelo menos, uma conta de teste que totalmente está registada para o multi-factor Authentication.

Utilize estes passos para obter uma conta de teste iniciada:
1. Inicie sessão no [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup) com uma conta de teste. 
2. Siga as instruções para configurar um método de verificação.
3. Cria uma política de acesso condicional ou [alterar o estado do utilizador](howto-mfa-userstates.md) para exigir verificação de dois passos para a conta de teste. 

Os utilizadores também tem de seguir estes passos para se inscrever antes de eles podem autenticar com a extensão NPS.

## <a name="install-the-nps-extension"></a>Instalar a extensão NPS

> [!IMPORTANT]
> Instale a extensão NPS num servidor diferente do que o ponto de acesso VPN.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Transferir e instalar a extensão NPS da MFA do Azure

1. [Transfira a extensão NPS](https://aka.ms/npsmfa) do Centro de Download da Microsoft.
2. Copie o binário para o servidor de políticas de rede que pretende configurar.
3. Execute *setup.exe* e siga as instruções de instalação. Se encontrar erros, verifique novamente que as duas bibliotecas da secção de pré-requisitos foram instaladas com êxito.

### <a name="run-the-powershell-script"></a>Executar o script do PowerShell

O instalador cria um script do PowerShell nesta localização: `C:\Program Files\Microsoft\AzureMfa\Config` (em que C:\ é sua unidade de instalação). Este script do PowerShell executa as seguintes ações de cada vez que for executada:

- Crie um certificado autoassinado.
- Associe a chave pública do certificado para o serviço principal no Azure AD.
- Store o certificado no arquivo de certificados do computador local.
- Conceder acesso, a chave privada do certificado para o utilizador de rede.
- Reinicie o NPS.

A menos que queira utilizar seus próprios certificados (em vez dos certificados autoassinados, que gera o script do PowerShell), execute o Script do PowerShell para concluir a instalação. Se instalar a extensão em vários servidores, cada um deles deve ter seu próprio certificado.

1. Execute o Windows PowerShell como administrador.
2. Altere os diretórios.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. Execute o script do PowerShell criado pelo instalador.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. Inicie sessão no Azure AD como um administrador.
5. Pedidos do PowerShell para o ID de inquilino. Utilize o GUID de ID de diretório que copiou do portal do Azure, na secção pré-requisitos.
6. PowerShell mostra uma mensagem de êxito quando o script estiver concluído.  

Repita estes passos em todos os servidores adicionais de NPS que pretende configurar para balanceamento de carga.

> [!NOTE]
> Se usar seus próprios certificados em vez de gerar certificados com o script do PowerShell, certifique-se de que eles se alinham com a Convenção de nomenclatura de NPS. O nome do requerente tem de ser **CN =\<TenantID\>, UO = extensão NPS da Microsoft**. 

## <a name="configure-your-nps-extension"></a>Configurar a sua extensão NPS

Esta secção inclui considerações de design e sugestões para Implantações bem-sucedidas de extensão NPS.

### <a name="configuration-limitations"></a>Limitações de configuração

- A extensão NPS da MFA do Azure não inclui ferramentas para migrar utilizadores e as definições do servidor MFA para a cloud. Por esse motivo, sugerimos que utilize a extensão para novas Implantações, em vez de implementação existente. Se utilizar a extensão numa implementação existente, os utilizadores têm que realizar uma prova de segurança novamente para preencher os detalhes da MFA na cloud.  
- A extensão NPS utiliza o UPN do Active directory no local para identificar o utilizador no MFA do Azure para efetuar a autenticação secundária A extensão pode ser configurada para utilizar um identificador de diferente, como o ID de início de sessão alternativo ou de campo personalizado do Active Directory que não seja o UPN. Para obter mais informações, consulte o artigo [opções de configuração para a extensão NPS para multi-factor Authentication avançadas](howto-mfa-nps-extension-advanced.md).
- Nem todos os protocolos de criptografia suportam todos os métodos de verificação.
   - **PAP** oferece suporte a chamada telefónica, mensagem de texto unidirecional, notificação de aplicação móvel e o código de verificação de aplicação móvel
   - **CHAPV2** e **EAP** suporte a chamadas telefónicas e de notificação de aplicação móvel

### <a name="control-radius-clients-that-require-mfa"></a>Clientes RADIUS de controle que exigem a MFA

Depois de ativar a MFA para um cliente RADIUS utilizando a extensão de NPS, todas as autenticações para este cliente são necessários para executar a MFA. Se pretender ativar a MFA para alguns clientes RADIUS, mas não para outros, pode configurar dois servidores NPS e instalar a extensão em apenas um deles. Configure clientes RADIUS que deseja exigir a MFA enviar pedidos para o servidor NPS configurado com a extensão e outros clientes RADIUS para o servidor NPS não configurada com a extensão.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Preparar para os utilizadores que não estejam inscritos para a MFA

Se tiver utilizadores que não estejam inscritos para a MFA, pode determinar o que acontece quando o utilizador tentar autenticar. Utilize a definição de registo *REQUIRE_USER_MATCH* no caminho do registo *HKLM\Software\Microsoft\AzureMFA* para controlar o comportamento de funcionalidade. Esta definição não tem uma opção de configuração única:

| Chave | Value | Predefinição |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | VERDADEIRO/FALSO | Não definido (equivalente ao verdadeiro) |

O objetivo desta definição é determinar o que fazer quando um utilizador não estiver inscrito para a MFA. Quando a chave não existe, não está definida ou é definido como TRUE e o utilizador não estiver inscrito e, em seguida, a extensão de falha na submissão da MFA. Quando a chave está definida como FALSE e o utilizador não estiver inscrito, a autenticação continua sem executar a MFA. Se um utilizador estiver inscrito no MFA, eles tem de autenticar com a MFA, mesmo se REQUIRE_USER_MATCH é definido como FALSE.

Pode optar por criar esta chave e defini-lo como FALSE, enquanto os seus utilizadores estiverem a integração e talvez nem todos ser inscritos para MFA do Azure ainda. No entanto, uma vez que definir a chave permite aos utilizadores que não estejam inscritos para a MFA iniciar sessão, deve remover esta chave antes de entrar em produção.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Como posso verificar que o certificado de cliente é instalado conforme o esperado?

Procure o certificado autoassinado criado pelo instalador no arquivo de certificados e verifique se a chave privada tem as permissões concedidas ao utilizador **serviço de rede**. O certificado tem um nome de requerente dos **CN \<tenantid\>, UO = extensão NPS da Microsoft**

-------------------------------------------------------------

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Como eu posso verificar que o meu certificado de cliente é associado ao inquilino no Azure Active Directory?

Abra a linha de comandos do PowerShell e execute os seguintes comandos:

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Estes comandos imprimir todos os certificados de associar o seu inquilino com a instância da extensão NPS na sessão do PowerShell. Procure o certificado ao exportar o certificado de cliente como um ficheiro de "x.509 com codificação Base 64" sem a chave privada e compará-lo com a lista a partir do PowerShell.

O comando seguinte criará um arquivo chamado "npscertificate" na sua unidade de "C:" no. cer de formato.

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertficicate.cer
```

Depois de executar este comando, vá para a unidade C, localize o ficheiro e faça duplo clique no mesmo. Ir para os detalhes e desloque-se para baixo até "thumbprint", comparar a thumbprint do certificado instalado no servidor a este. Os thumbprints de certificado devem corresponder.

Válido-do e válido-até que os carimbos de data /, que são em formato legível por humanos, pode ser utilizado para filtrar os misfits óbvios, se o comando devolve mais de um certificado.

-------------------------------------------------------------

### <a name="why-cant-i-sign-in"></a>Por que não pode entrar nos?

Verifique se a palavra-passe ainda não tiver expirado. A extensão NPS não suporta a alteração de palavras-passe como parte do fluxo de trabalho início de sessão. Entre em contato com a equipe de TI da sua organização para obter mais assistência.

-------------------------------------------------------------

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Por que motivo são os meus pedidos falhar com o erro de token da ADAL?

Este erro pode ser devido a um dos vários motivos. Utilize estes passos para ajudar a resolver:

1. Reinicie o servidor NPS.
2. Certifique-se de que esse certificado de cliente está instalado corretamente.
3. Certifique-se de que o certificado é associado ao seu inquilino no Azure AD.
4. Certifique-se de que https://login.microsoftonline.com/ está acessível a partir do servidor que executa a extensão.

-------------------------------------------------------------

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Por que a autenticação falhar com um erro nos registos HTTP que diz que o utilizador não está?

Certifique-se de que o AD Connect está em execução e que o utilizador esteja presente no Active Directory do Windows e no Azure Active Directory.

-------------------------------------------------------------

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Por que vejo HTTP os erros nos registos de ligação com todas as minhas autenticações falhar?

Certifique-se de que https://adnotifications.windowsazure.com está acessível a partir do servidor que executa a extensão NPS.

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Gerir os Protocolos TLS/SSL e Conjuntos de Cifras

Recomenda-se que conjuntos de cifras mais antigas e mais fraco desativados ou removidos requerida pela sua organização. Pode encontrar informações sobre como concluir esta tarefa no artigo [Gerir Protocolos SSL/TLS e Conjuntos de Cifras para o AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)

## <a name="next-steps"></a>Passos Seguintes

- Configurar IDs alternativos para o início de sessão ou configurar uma lista de exceções para IPs que não deve realizar a verificação de dois passos no [avançado de opções de configuração para a extensão NPS para multi-factor Authentication](howto-mfa-nps-extension-advanced.md)

- Saiba como integrar [Gateway de ambiente de trabalho remoto](howto-mfa-nps-extension-rdg.md) e [servidores VPN](howto-mfa-nps-extension-vpn.md) usando a extensão NPS

- [Resolver mensagens de erro da extensão NPS para Multi-Factor Authentication do Azure](howto-mfa-nps-extension-errors.md)
