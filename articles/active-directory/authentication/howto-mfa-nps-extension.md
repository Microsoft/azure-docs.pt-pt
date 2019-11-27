---
title: Fornecer recursos do Azure MFA usando o Azure Active Directory do NPS
description: Adicione recursos de verificação em duas etapas baseados em nuvem à sua infraestrutura de autenticação existente
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8388d5b22cddcf148c68f35758ccdf797abbcd9e
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420636"
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>Integre sua infraestrutura de NPS existente com a autenticação multifator do Azure

A extensão do NPS (servidor de políticas de rede) para o Azure MFA adiciona recursos de MFA baseados em nuvem à sua infraestrutura de autenticação usando seus servidores existentes. Com a extensão do NPS, você pode adicionar a chamada telefônica, a mensagem de texto ou a verificação do aplicativo de telefone ao seu fluxo de autenticação existente sem precisar instalar, configurar e manter novos servidores. 

Essa extensão foi criada para organizações que desejam proteger conexões VPN sem implantar o servidor Azure MFA. A extensão do NPS atua como um adaptador entre o RADIUS e o Azure MFA baseado em nuvem para fornecer um segundo fator de autenticação para usuários federados ou sincronizados.

Ao usar a extensão NPS para o Azure MFA, o fluxo de autenticação inclui os seguintes componentes: 

1. O **servidor de nas/VPN** recebe solicitações de clientes VPN e os converte em solicitações RADIUS para servidores NPS. 
2. O **servidor NPS** se conecta ao Active Directory para executar a autenticação primária para as solicitações RADIUS e, após o êxito, passa a solicitação para todas as extensões instaladas.  
3. A **extensão do NPS** dispara uma solicitação para a MFA do Azure para a autenticação secundária. Depois que a extensão recebe a resposta e, se o desafio de MFA for bem sucedido, ele concluirá a solicitação de autenticação fornecendo ao servidor NPS os tokens de segurança que incluem uma declaração MFA, emitida pelo STS do Azure.  
4. O **Azure MFA** se comunica com Azure Active Directory para recuperar os detalhes do usuário e executa a autenticação secundária usando um método de verificação configurado para o usuário.

O diagrama a seguir ilustra esse fluxo de solicitação de autenticação de alto nível: 

![Diagrama de fluxo de autenticação](./media/howto-mfa-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>Planear a sua implementação

A extensão NPS manipula automaticamente a redundância, de modo que você não precisa de uma configuração especial.

Você pode criar quantos servidores NPS habilitados para MFA do Azure forem necessários. Se você instalar vários servidores, deverá usar um certificado de cliente de diferença para cada um deles. A criação de um certificado para cada servidor significa que você pode atualizar cada certificado individualmente e não se preocupe com o tempo de inatividade em todos os servidores.

Os servidores VPN roteiam solicitações de autenticação, portanto, precisam estar cientes dos novos servidores NPS habilitados para MFA do Azure.

## <a name="prerequisites"></a>Pré-requisitos

A extensão do NPS é destinada a trabalhar com sua infraestrutura existente. Verifique se você tem os pré-requisitos a seguir antes de começar.

### <a name="licenses"></a>Las

A extensão do NPS para o Azure MFA está disponível para clientes com [licenças para a autenticação multifator do Azure](multi-factor-authentication.md) (incluída com Azure ad Premium, EMS ou uma licença do MFA autônoma). As licenças baseadas em consumo para o Azure MFA, como licenças por usuário ou por autenticação, não são compatíveis com a extensão do NPS. 

### <a name="software"></a>Software

Windows Server 2008 R2 SP1 ou superior.

### <a name="libraries"></a>Bibliotecas

Essas bibliotecas são instaladas automaticamente com a extensão.

- [Pacotes C++ redistribuíveis do Visual para Visual Studio 2013 (x64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [Módulo Microsoft Azure Active Directory para Windows PowerShell versão 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

O Módulo Microsoft Azure Active Directory para Windows PowerShell é instalado, se ainda não estiver presente, por meio de um script de configuração executado como parte do processo de instalação. Não há necessidade de instalar esse módulo antecipadamente se ele ainda não estiver instalado.

### <a name="azure-active-directory"></a>Azure Active Directory

Todos que usam a extensão NPS devem ser sincronizados para Azure Active Directory usando Azure AD Connect e devem ser registrados para MFA.

Ao instalar a extensão, você precisa da ID de diretório e das credenciais de administrador para seu locatário do Azure AD. Você pode encontrar a ID do diretório na [portal do Azure](https://portal.azure.com). Entre como administrador. Procure e selecione a **Azure Active Directory**e, em seguida, selecione **Propriedades**. Copie o GUID na caixa **ID de diretório** e salve-o. Você usa esse GUID como a ID de locatário ao instalar a extensão NPS.

![Localize sua ID de diretório em Propriedades de Azure Active Directory](./media/howto-mfa-nps-extension/properties-directory-id.png)

### <a name="network-requirements"></a>Requisitos da rede

O servidor NPS precisa ser capaz de se comunicar com as URLs a seguir pelas portas 80 e 443.

- https:\//adnotifications.windowsazure.com
- https:\//login.microsoftonline.com

Além disso, a conectividade com as URLs a seguir é necessária para concluir a [configuração do adaptador usando o script do PowerShell fornecido](#run-the-powershell-script)

- https:\//login.microsoftonline.com
- https:\//provisioningapi.microsoftonline.com
- https:\//aadcdn.msauth.net

## <a name="prepare-your-environment"></a>Preparar o ambiente

Antes de instalar a extensão NPS, você deseja preparar seu ambiente para lidar com o tráfego de autenticação.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Habilitar a função NPS em um servidor ingressado no domínio

O servidor NPS conecta-se a Azure Active Directory e autentica as solicitações de MFA. Escolha um servidor para essa função. É recomendável escolher um servidor que não trate as solicitações de outros serviços, pois a extensão do NPS gera erros para quaisquer solicitações que não são RADIUS. O servidor NPS deve ser configurado como o servidor de autenticação primário e secundário para o seu ambiente; Ele não pode fazer o proxy de solicitações RADIUS para outro servidor.

1. No servidor, abra o **Assistente para adicionar funções e recursos** no menu Gerenciador do servidor início rápido.
2. Escolha **instalação baseada em função ou recurso** para o tipo de instalação.
3. Selecione a função de servidor **serviços de acesso e política de rede** . Uma janela pode ser exibida para informá-lo sobre os recursos necessários para executar essa função.
4. Continue no assistente até a página de confirmação. Selecione **Instalar**.

Agora que você tem um servidor designado para NPS, você também deve configurar esse servidor para tratar as solicitações RADIUS de entrada da solução de VPN.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Configurar sua solução de VPN para se comunicar com o servidor NPS

Dependendo da solução de VPN que você usa, as etapas para configurar sua política de autenticação RADIUS variam. Configure essa política para apontar para o servidor NPS do RADIUS.

### <a name="sync-domain-users-to-the-cloud"></a>Sincronizar usuários de domínio com a nuvem

Esta etapa já pode estar completa em seu locatário, mas é bom verificar se Azure AD Connect sincronizou seus bancos de dados recentemente.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador.
2. Selecione **Azure Active Directory** > **Azure ad Connect**
3. Verifique se o status de sincronização está **habilitado** e se a última sincronização foi menor que uma hora atrás.

Se você precisar disparar uma nova rodada de sincronização, nós as instruções em [Azure ad Connect sincronização: Agendador](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Determinar quais métodos de autenticação seus usuários podem usar

Há dois fatores que afetam quais métodos de autenticação estão disponíveis com uma implantação de extensão do NPS:

1. O algoritmo de criptografia de senha usado entre o cliente RADIUS (VPN, servidor Netscaler ou outro) e os servidores NPS.
   - O **PAP** dá suporte a todos os métodos de autenticação do Azure MFA na nuvem: chamada telefônica, mensagem de texto unidirecional, notificação de aplicativo móvel, tokens de hardware OATH e código de verificação de aplicativo móvel.
   - **CHAPv2** e **EAP** dão suporte a chamada telefônica e notificação de aplicativo móvel.

      > [!NOTE]
      > Ao implantar a extensão NPS, use esses fatores para avaliar quais métodos estão disponíveis para seus usuários. Se o cliente RADIUS oferecer suporte a PAP, mas o UX do cliente não tiver campos de entrada para um código de verificação, a chamada telefônica e a notificação de aplicativo móvel serão as duas opções com suporte.
      >
      > Além disso, se o seu UX de cliente VPN der suporte ao campo de entrada e você tiver configurado a política de acesso à rede-a autenticação pode ter sucesso, mas nenhum dos atributos RADIUS configurados na política de rede será aplicado ao dispositivo de acesso à rede, como o servidor RRAS, nem o cliente VPN. Como resultado, o cliente VPN pode ter mais acesso do que o desejado ou menos para nenhum acesso.
      >

2. Os métodos de entrada que o aplicativo cliente (VPN, servidor Netscaler ou outro) podem manipular. Por exemplo, o cliente VPN tem algum meio de permitir que o usuário digite um código de verificação de um aplicativo móvel ou de texto?

Você pode [desabilitar métodos de autenticação sem suporte](howto-mfa-mfasettings.md#verification-methods) no Azure.

### <a name="register-users-for-mfa"></a>Registrar usuários para MFA

Antes de implantar e usar a extensão NPS, os usuários necessários para executar a verificação em duas etapas precisam ser registrados para MFA. Mais imediatamente, para testar a extensão ao implantá-la, você precisa de pelo menos uma conta de teste totalmente registrada para autenticação multifator.

Use estas etapas para obter uma conta de teste iniciada:

1. Entre no [https://aka.ms/mfasetup](https://aka.ms/mfasetup) com uma conta de teste.
2. Siga os prompts para configurar um método de verificação.
3. [Crie uma política de acesso condicional](howto-mfa-getstarted.md#create-conditional-access-policy) para exigir a autenticação multifator para a conta de teste.

## <a name="install-the-nps-extension"></a>Instalar a extensão NPS

> [!IMPORTANT]
> Instale a extensão NPS em um servidor diferente do ponto de acesso VPN.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Baixar e instalar a extensão do NPS para o Azure MFA

1. [Baixe a extensão NPS](https://aka.ms/npsmfa) do centro de download da Microsoft.
2. Copie o binário para o servidor de políticas de rede que você deseja configurar.
3. Execute *Setup. exe* e siga as instruções de instalação. Se você encontrar erros, verifique se as duas bibliotecas da seção de pré-requisitos foram instaladas com êxito.

#### <a name="upgrade-the-nps-extension"></a>Atualizar a extensão do NPS

Ao atualizar uma instalação de extensão de NPS existente, para evitar uma reinicialização do servidor subjacente, conclua as seguintes etapas:

1. Desinstalar a versão existente
1. Executar o novo instalador
1. Reiniciar o serviço do servidor de políticas de rede (IAS)

### <a name="run-the-powershell-script"></a>Executar o script do PowerShell

O instalador cria um script do PowerShell neste local: `C:\Program Files\Microsoft\AzureMfa\Config` (em que C:\ é a unidade de instalação do). Esse script do PowerShell executa as seguintes ações cada vez que é executado:

- Crie um certificado autoassinado.
- Associe a chave pública do certificado à entidade de serviço no Azure AD.
- Armazene o certificado no repositório de certificados do computador local.
- Conceda acesso à chave privada do certificado ao usuário da rede.
- Reinicie o NPS.

A menos que você queira usar seus próprios certificados (em vez dos certificados autoassinados gerados pelo script do PowerShell), execute o script do PowerShell para concluir a instalação. Se você instalar a extensão em vários servidores, cada um deve ter seu próprio certificado.

1. Execute o Windows PowerShell como administrador.
2. Altere os diretórios.

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. Execute o script do PowerShell criado pelo instalador.

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. Entre no Azure AD como um administrador.
5. O PowerShell solicita sua ID de locatário. Use o GUID de ID de diretório que você copiou do portal do Azure na seção pré-requisitos.
6. O PowerShell mostra uma mensagem de êxito quando o script é concluído.  

Repita essas etapas em todos os servidores NPS adicionais que você deseja configurar para o balanceamento de carga.

Se o certificado do computador anterior tiver expirado e um novo certificado tiver sido gerado, você deverá excluir todos os certificados expirados. Ter certificados expirados pode causar problemas com a extensão do NPS iniciando.

> [!NOTE]
> Se você usar seus próprios certificados em vez de gerar certificados com o script do PowerShell, certifique-se de que eles se alinhem à Convenção de nomenclatura do NPS. O nome da entidade deve ser **CN =\<tenantid\>, ou = extensão NPS da Microsoft**. 

### <a name="certificate-rollover"></a>Substituição de certificado

Com a versão 1.0.1.32 da extensão do NPS, agora há suporte para ler vários certificados. Essa funcionalidade ajudará a facilitar as atualizações de certificado anteriores à expiração. Se sua organização estiver executando uma versão anterior da extensão do NPS, você deverá atualizar para a versão 1.0.1.32 ou superior.

Os certificados criados pelo script de `AzureMfaNpsExtnConfigSetup.ps1` são válidos por 2 anos. As organizações de ti devem monitorar certificados para expiração. Os certificados para a extensão NPS são colocados no repositório de certificados do computador local em pessoal e são emitidos para a ID de locatário fornecida ao script.

Quando um certificado está se aproximando da data de validade, um novo certificado deve ser criado para substituí-lo.  Esse processo é realizado executando o `AzureMfaNpsExtnConfigSetup.ps1` novamente e mantendo a mesma ID de locatário quando solicitado. Esse processo deve ser repetido em cada servidor NPS em seu ambiente.

## <a name="configure-your-nps-extension"></a>Configurar sua extensão NPS

Esta seção inclui considerações de design e sugestões para implantações bem-sucedidas de extensão do NPS.

### <a name="configuration-limitations"></a>Limitações de configuração

- A extensão do NPS para o Azure MFA não inclui ferramentas para migrar usuários e configurações do servidor MFA para a nuvem. Por esse motivo, sugerimos o uso da extensão para novas implantações, em vez da implantação existente. Se você usar a extensão em uma implantação existente, os usuários terão que executar a verificação novamente para preencher os detalhes de MFA na nuvem.  
- A extensão do NPS usa o UPN do Active Directory local para identificar o usuário no Azure MFA para executar a autenticação secundária. A extensão pode ser configurada para usar um identificador diferente, como a ID de logon alternativa ou o campo de Active Directory personalizado diferente do UPN. Para obter mais informações, consulte o artigo [Opções de configuração avançadas para a extensão do NPS para autenticação multifator](howto-mfa-nps-extension-advanced.md).
- Nem todos os protocolos de criptografia oferecem suporte a todos os métodos de verificação.
   - O **PAP** dá suporte a chamada telefônica, mensagem de texto unidirecional, notificação de aplicativo móvel e código de verificação de aplicativo móvel
   - **CHAPv2** e **EAP** dão suporte a chamada telefônica e notificação de aplicativo móvel

### <a name="control-radius-clients-that-require-mfa"></a>Controlar clientes RADIUS que exigem MFA

Depois de habilitar a MFA para um cliente RADIUS usando a extensão NPS, todas as autenticações desse cliente serão necessárias para executar a MFA. Se você quiser habilitar a MFA para alguns clientes RADIUS, mas não para outros, poderá configurar dois servidores NPS e instalar a extensão em apenas um deles. Configure clientes RADIUS que você deseja exigir que o MFA envie solicitações ao servidor NPS configurado com a extensão e outros clientes RADIUS para o servidor NPS não configurados com a extensão.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Preparar para usuários que não estão registrados para MFA

Se você tiver usuários que não estão registrados para MFA, poderá determinar o que acontece quando eles tentam se autenticar. Use a configuração do registro *REQUIRE_USER_MATCH* no caminho do registro *HKLM\Software\Microsoft\AzureMFA* para controlar o comportamento do recurso. Essa configuração tem uma única opção de configuração:

| Chave | Valor | Predefinição |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | VERDADEIRO/FALSO | Não definido (equivalente a TRUE) |

A finalidade dessa configuração é determinar o que fazer quando um usuário não está registrado para MFA. Quando a chave não existir, não estiver definida ou estiver definida como TRUE e o usuário não estiver registrado, a extensão falhará no desafio de MFA. Quando a chave é definida como FALSE e o usuário não está registrado, a autenticação prossegue sem executar a MFA. Se um usuário estiver registrado no MFA, ele deverá se autenticar com o MFA, mesmo se REQUIRE_USER_MATCH estiver definido como FALSE.

Você pode optar por criar essa chave e defini-la como falsa enquanto os usuários estão integrados e talvez nem todos sejam registrados para o Azure MFA ainda. No entanto, como a configuração da chave permite que os usuários não registrados para o MFA entrem, você deve remover essa chave antes de ir para a produção.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="nps-extension-health-check-script"></a>Script de verificação de integridade da extensão do NPS

O script a seguir está disponível na galeria do TechNet para executar as etapas básicas de verificação de integridade ao solucionar problemas de extensão do NPS.

[MFA_NPS_Troubleshooter. ps1](https://gallery.technet.microsoft.com/Azure-MFA-NPS-Extension-648de6bb)

---

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Como fazer verificar se o certificado do cliente está instalado conforme o esperado?

Procure o certificado autoassinado criado pelo instalador no repositório de certificados e verifique se a chave privada tem permissões concedidas ao **serviço de rede**do usuário. O certificado tem um nome de assunto de **CN \<tenantid\>, ou = extensão NPS da Microsoft**

Os certificados autoassinados gerados pelo script *AzureMfaNpsExtnConfigSetup. ps1* também têm um tempo de vida útil de dois anos. Ao verificar se o certificado está instalado, você também deve verificar se o certificado não expirou.

---

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>Como verifico se meu certificado de cliente está associado ao meu locatário no Azure Active Directory?

Abra o prompt de comando do PowerShell e execute os seguintes comandos:

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Esses comandos imprimem todos os certificados que associam seu locatário à sua instância da extensão do NPS em sua sessão do PowerShell. Procure seu certificado exportando o certificado do cliente como um arquivo "base-64 codificado X. 509 (. cer)" sem a chave privada e compare-o com a lista do PowerShell.

O comando a seguir criará um arquivo chamado "npscertificate" na unidade "C:" no formato. cer.

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertficicate.cer
```

Depois de executar esse comando, vá para a unidade C, localize o arquivo e clique duas vezes nele. Vá para detalhes e role para baixo até "impressão digital", compare a impressão digital do certificado instalado no servidor com esta. As impressões digitais do certificado devem corresponder.

Válidos-de e válido-até os carimbos de data/hora, que estão no formato legível, podem ser usados para filtrar inadequadamente os inadequados se o comando retornar mais de um certificado.

---

### <a name="why-cant-i-sign-in"></a>Por que eu não consigo entrar?

Verifique se sua senha não expirou. A extensão do NPS não dá suporte à alteração de senhas como parte do fluxo de trabalho de entrada. Entre em contato com a equipe de ti da sua organização para obter mais assistência.

---

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Por que minhas solicitações estão falhando com o erro de token ADAL?

Esse erro pode ser devido a uma das várias razões. Use estas etapas para ajudar a solucionar problemas:

1. Reinicie o servidor NPS.
2. Verifique se o certificado do cliente está instalado conforme o esperado.
3. Verifique se o certificado está associado ao seu locatário no Azure AD.
4. Certifique-se de que https://login.microsoftonline.com/ está acessível a partir do servidor que executa a extensão.

---

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Por que a autenticação falha com um erro nos logs HTTP informando que o usuário não foi encontrado?

Verifique se o AD Connect está em execução e se o usuário está presente no Windows Active Directory e Azure Active Directory.

---

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Por que vejo erros de conexão HTTP em logs com todas as minhas autenticações falhando?

Certifique-se de que https://adnotifications.windowsazure.com está acessível a partir do servidor que executa a extensão NPS.

---

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Por que a autenticação não está funcionando, apesar de um certificado válido estar presente?

Se o certificado do computador anterior tiver expirado e um novo certificado tiver sido gerado, você deverá excluir todos os certificados expirados. Ter certificados expirados pode causar problemas com a extensão do NPS iniciando.

Para verificar se você tem um certificado válido, verifique o repositório de certificados da conta do computador local usando o MMC e certifique-se de que o certificado não tenha passado sua data de expiração. Para gerar um certificado recentemente válido, execute novamente as etapas na seção "[executar o script do PowerShell](#run-the-powershell-script)"

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Gerir os Protocolos TLS/SSL e Conjuntos de Cifras

É recomendável que pacotes de codificação antigos e mais fracos sejam desabilitados ou removidos, a menos que sejam exigidos pela sua organização. Pode encontrar informações sobre como concluir esta tarefa no artigo [Gerir Protocolos SSL/TLS e Conjuntos de Cifras para o AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)

### <a name="additional-troubleshooting"></a>Solução de problemas adicional

Diretrizes de solução de problemas adicionais e possíveis soluções podem ser encontradas no artigo [resolver mensagens de erro da extensão do NPS para a autenticação multifator do Azure](howto-mfa-nps-extension-errors.md).

## <a name="next-steps"></a>Passos seguintes

- Configurar IDs alternativas para logon ou configurar uma lista de exceções para IPs que não devem executar a verificação em duas etapas nas [Opções de configuração avançadas para a extensão do NPS para autenticação multifator](howto-mfa-nps-extension-advanced.md)

- Saiba como integrar [área de trabalho remota gateway](howto-mfa-nps-extension-rdg.md) e [servidores VPN](howto-mfa-nps-extension-vpn.md) usando a extensão NPS

- [Resolver mensagens de erro da extensão NPS para Multi-Factor Authentication do Azure](howto-mfa-nps-extension-errors.md)
