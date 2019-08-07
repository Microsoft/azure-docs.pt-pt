---
title: 'Azure AD Connect: Migrar da Federação para o PHS para o Azure AD | Microsoft Docs'
description: Este artigo contém informações sobre como mover seu ambiente de identidade híbrida da Federação para a sincronização de hash de senha.
services: active-directory
author: billmath
manager: daveba
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b291f2243dfe28a8e866796e0b7375f94fa4f2e
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779432"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-active-directory"></a>Migrar da Federação para a sincronização de hash de senha para Azure Active Directory

Este artigo descreve como mover os domínios da sua organização de Serviços de Federação do Active Directory (AD FS) (AD FS) para a sincronização de hash de senha.

Você pode [baixar este artigo](https://aka.ms/ADFSTOPHSDPDownload).

## <a name="prerequisites-for-migrating-to-password-hash-synchronization"></a>Pré-requisitos para migrar para a sincronização de hash de senha

Os pré-requisitos a seguir são necessários para migrar do usando AD FS para usar a sincronização de hash de senha.

### <a name="update-azure-ad-connect"></a>Atualizar Azure AD Connect

Como um mínimo para executar com êxito as etapas para migrar para a sincronização de hash de senha, você deve ter o [Azure ad Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0. Esta versão contém alterações significativas na forma como a conversão de entrada é executada e reduz o tempo geral para migrar da Federação para a autenticação de nuvem de horas potencialmente para minutos.


> [!IMPORTANT]
> Você pode ler a documentação, as ferramentas e os Blogs desatualizados que a conversão do usuário é necessária quando você converte domínios de identidade federada para identidade gerenciada. A *conversão de usuários* não é mais necessária. A Microsoft está trabalhando para atualizar a documentação e as ferramentas para refletir essa alteração.

Para atualizar Azure ad Connect, conclua as etapas [em Azure ad Connect: Atualize para a versão](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version)mais recente.

### <a name="password-hash-synchronization-required-permissions"></a>Permissões necessárias de sincronização de hash de senha

Você pode configurar Azure AD Connect usando as configurações expressas ou uma instalação personalizada. Se você usou a opção de instalação personalizada, as [permissões necessárias](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-accounts-permissions) para a sincronização de hash de senha podem não estar em vigor.

A conta de serviço do Azure AD Connect Active Directory Domain Services (AD DS) requer as seguintes permissões para sincronizar os hashes de senha:

* Replicar alterações de diretório
* Replicar todas as alterações de diretório

Agora é um bom momento para verificar se essas permissões estão em vigor para todos os domínios na floresta.

### <a name="plan-the-migration-method"></a>Planejar o método de migração

Você pode escolher entre dois métodos para migrar do gerenciamento de identidades federadas para a sincronização de hash de senha e SSO (logon único) contínuo. O método usado depende de como sua instância de AD FS foi originalmente configurada.

* **Azure AD Connect**. Se você tiver configurado originalmente AD FS usando Azure AD Connect, será *necessário* alterar para sincronização de hash de senha usando o assistente de Azure ad Connect.

   Azure AD Connect executa automaticamente o cmdlet **set-MsolDomainAuthentication** quando você altera o método de entrada do usuário. Azure AD Connect automaticamente desfedera todos os domínios federados verificados em seu locatário do Azure AD.

   > [!NOTE]
   > No momento, se você usou originalmente Azure AD Connect para configurar AD FS, não será possível evitar a não Federação de todos os domínios em seu locatário quando você alterar a entrada do usuário para sincronização de hash de senha. ‎
* **Azure ad Connect com o PowerShell**. Você poderá usar esse método somente se não tiver configurado originalmente AD FS usando Azure AD Connect. Para essa opção, você ainda deve alterar o método de entrada do usuário por meio do assistente de Azure AD Connect. A principal diferença com essa opção é que o assistente não executa automaticamente o cmdlet **set-MsolDomainAuthentication** . Com essa opção, você tem controle total sobre quais domínios são convertidos e em qual ordem.

Para entender qual método deve ser usado, conclua as etapas nas seções a seguir.

#### <a name="verify-current-user-sign-in-settings"></a>Verificar as configurações de entrada do usuário atual

Para verificar as configurações de entrada do usuário atual:

1. Entre no [portal do AD do Azure](https://aad.portal.azure.com/) usando uma conta de administrador global.
2. Na seção de **entrada do usuário** , verifique as seguintes configurações:
   * A **Federação** está definida como **habilitada**.
   * O **logon único contínuo** está definido como **desabilitado**.
   * A **autenticação de passagem** está definida como **desabilitada**.

   ![Captura de tela das configurações na seção de entrada do usuário Azure AD Connect](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-the-azure-ad-connect-configuration"></a>Verificar a configuração de Azure AD Connect

1. No servidor de Azure AD Connect, abra Azure AD Connect. Selecione **Configurar**.
2. Na página **tarefas adicionais** , selecione **Exibir configuração atual**e, em seguida, selecione **Avançar**.<br />

   ![Captura de tela da opção Exibir configuração atual selecionada na página tarefas adicionais](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)<br />
3. Na página **examinar sua solução** , observe o status da **sincronização de hash de senha** .<br /> 

   * Se a **sincronização de hash de senha** estiver definida como **desabilitada**, conclua as etapas neste artigo para habilitá-la.
   * Se a **sincronização de hash de senha** estiver definida como habilitada, você **poderá ignorar a seção etapa 1: Habilitar a sincronização** de hash de senha neste artigo.
4. Na página **examinar sua solução** , role até **serviços de Federação do Active Directory (AD FS) (AD FS)** .<br />

   * Se a configuração de AD FS aparecer nesta seção, você poderá supor com segurança que AD FS foi originalmente configurado usando Azure AD Connect. Você pode converter seus domínios de identidade federada para identidade gerenciada usando a opção Azure AD Connect **alterar entrada do usuário** . O processo é detalhado na opção de **seção a: Alterne da Federação para a sincronização de hash de senha**usando Azure ad Connect.
   * Se AD FS não estiver listado nas configurações atuais, você deverá converter manualmente os domínios da identidade federada para a identidade gerenciada usando o PowerShell. Para obter mais informações sobre esse processo, consulte a **seção opção B: Alterne da Federação para a sincronização de hash de senha usando o**Azure ad Connect e o PowerShell.

### <a name="document-current-federation-settings"></a>Documentar configurações de Federação atuais

Para localizar suas configurações atuais de Federação, execute o cmdlet **Get-MsolDomainFederationSettings** :

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Exemplo:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

Verifique as configurações que podem ter sido personalizadas para seu design de Federação e documentação de implantação. Especificamente, procure personalizações em **PreferredAuthenticationProtocol**, **SupportsMfa**e **PromptLoginBehavior**.

Para obter mais informações, veja estes artigos:

* [Prompt de AD FS = suporte a parâmetros de logon](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Se **SupportsMfa** for definido como **true**, você estará usando uma solução de autenticação multifator local para injetar um desafio de segundo fator no fluxo de autenticação do usuário. Essa configuração não funciona mais para cenários de autenticação do Azure AD depois de converter esse domínio de autenticação federada para gerenciada. Depois de desabilitar a Federação, você separa a relação para a sua Federação local e inclui adaptadores MFA locais. 
>
> Em vez disso, use o serviço baseado em nuvem da autenticação multifator do Azure para executar a mesma função. Avalie cuidadosamente seus requisitos de autenticação multifator antes de continuar. Antes de converter seus domínios, certifique-se de que você entende como usar a autenticação multifator do Azure, as implicações de licenciamento e o processo de registro do usuário.

#### <a name="back-up-federation-settings"></a>Fazer backup das configurações de Federação

Embora nenhuma alteração seja feita em outras partes confiáveis no farm de AD FS durante os processos descritos neste artigo, recomendamos que você tenha um backup válido atual do farm de AD FS do qual você pode restaurar. Você pode criar um backup válido atual usando a ferramenta gratuita de [restauração rápida](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool)da Microsoft AD FS. Você pode usar a ferramenta para fazer backup de AD FS e restaurar um farm existente ou criar um novo farm.

Se você optar por não usar a ferramenta de restauração rápida AD FS, no mínimo, deverá exportar a terceira parte confiável da plataforma de identidade Microsoft Office 365 e quaisquer regras de declaração personalizada associadas que você adicionou. Você pode exportar a relação de confiança de terceira parte confiável e as regras de declaração associadas usando o seguinte exemplo do PowerShell:

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>Considerações sobre implantação e uso de AD FS

Esta seção descreve as considerações de implantação e detalhes sobre como usar AD FS.

### <a name="current-ad-fs-use"></a>Uso atual de AD FS

Antes de converter a identidade federada para a identidade gerenciada, observe de maneira mais detalhada como você usa AD FS para o Azure AD, o Office 365 e outros aplicativos (confianças de terceira parte confiável). Especificamente, considere os cenários descritos na tabela a seguir:

| Se | Clique |
|-|-|
| Você planeja continuar usando AD FS com outros aplicativos (além do Azure AD e do Office 365). | Depois de converter seus domínios, você usará o AD FS e o Azure AD. Considere a experiência do usuário. Em alguns cenários, os usuários podem ser solicitados a autenticar duas vezes: uma vez ao Azure AD (em que um usuário obtém acesso SSO a outros aplicativos, como o Office 365), e novamente para todos os aplicativos que ainda estão associados a AD FS como uma relação de confiança de terceira parte confiável. |
| Sua instância de AD FS é bastante personalizada e depende de configurações de personalização específicas no arquivo OnLoad. js (por exemplo, se você alterou a experiência de entrada para que os usuários usem apenas um formato **sAMAccountName** para seu nome de usuário em vez de uma entidade Nome (UPN) ou sua organização tem uma marca intensamente a experiência de entrada). O arquivo OnLoad. js não pode ser duplicado no Azure AD. | Antes de continuar, você deve verificar se o Azure AD pode atender aos seus requisitos de personalização atuais. Para obter mais informações e diretrizes, consulte as seções em AD FS identidade visual e AD FS personalização.|
| Você usa AD FS para bloquear versões anteriores de clientes de autenticação.| Considere substituir os controles de AD FS que bloqueiam versões anteriores de clientes de autenticação usando uma combinação de [controles de acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) e [regras de acesso para cliente do Exchange Online](https://aka.ms/EXOCAR). |
| Você exige que os usuários executem a autenticação multifator em uma solução de servidor de autenticação multifator local quando os usuários se autenticam no AD FS.| Em um domínio de identidade gerenciada, você não pode injetar um desafio de autenticação multifator por meio da solução de autenticação multifator local no fluxo de autenticação. No entanto, você pode usar o serviço de autenticação multifator do Azure para autenticação multifator depois que o domínio é convertido.<br /><br /> Se os usuários não usarem atualmente a autenticação multifator do Azure, uma etapa de registro de usuário OneTime será necessária. Você deve se preparar e comunicar o registro planejado para seus usuários. |
| Atualmente, você usa políticas de controle de acesso (regras de AuthZ) em AD FS para controlar o acesso ao Office 365.| Considere substituir as políticas com as [políticas de acesso condicional](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) do Azure ad equivalentes e as [regras de acesso para cliente do Exchange Online](https://aka.ms/EXOCAR).|

### <a name="common-ad-fs-customizations"></a>Personalizações comuns de AD FS

Esta seção descreve as personalizações AD FS comuns.

#### <a name="insidecorporatenetwork-claim"></a>Declaração de InsideCorporateNetwork

AD FS emitirá a Declaração **InsideCorporateNetwork** se o usuário que está Autenticando estiver dentro da rede corporativa. Essa declaração pode então ser passada para o Azure AD. A declaração é usada para ignorar a autenticação multifator com base no local de rede do usuário. Para saber como determinar se essa funcionalidade está habilitada no momento no AD FS, confira [IPs confiáveis para usuários federados](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud).

A Declaração **InsideCorporateNetwork** não está disponível depois que os domínios são convertidos para a sincronização de hash de senha. Você pode usar [locais nomeados no Azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) para substituir essa funcionalidade.

Depois de configurar os locais nomeados, você deve atualizar todas as políticas de acesso condicional que foram configuradas para incluir ou excluir a rede **todos os locais confiáveis** ou valores de **IPs confiáveis MFA** para refletir os novos locais nomeados.

Para obter mais informações sobre a condição de **local** no acesso condicional, consulte [Active Directory locais de acesso condicional](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

#### <a name="hybrid-azure-ad-joined-devices"></a>Dispositivos ingressados no Azure AD híbrido

Ao unir um dispositivo ao Azure AD, você pode criar regras de acesso condicional que impõem que os dispositivos atendam aos seus padrões de acesso para segurança e conformidade. Além disso, os usuários podem entrar em um dispositivo usando uma conta corporativa ou de estudante em vez de uma conta pessoal. Ao usar dispositivos ingressados no Azure AD híbrido, você pode ingressar seus Active Directory dispositivos ingressados no domínio no Azure AD. Seu ambiente federado pode ter sido configurado para usar esse recurso.

Para garantir que a junção híbrida continue funcionando para todos os dispositivos que ingressaram no domínio depois que os domínios são convertidos para a sincronização de hash de senha, para clientes do Windows 10, você deve usar Azure AD Connect para sincronizar Active Directory contas de computador com o Azure AD. 

Para contas de computador com Windows 8 e Windows 7, a junção híbrida usa o SSO contínuo para registrar o computador no Azure AD. Você não precisa sincronizar contas de computador com Windows 8 e Windows 7 como você faz para dispositivos Windows 10. No entanto, você deve implantar um arquivo workplacejoin. exe atualizado (por meio de um arquivo. msi) em clientes do Windows 8 e do Windows 7 para que eles possam se registrar usando o SSO contínuo. [Baixe o arquivo. msi](https://www.microsoft.com/download/details.aspx?id=53554).

Para obter mais informações, consulte [configurar dispositivos ingressados no Azure ad híbrido](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup).

#### <a name="branding"></a>Personalização

Se sua organização [personalizou suas páginas de entrada AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) para exibir informações mais pertinentes à organização, considere fazer personalizações semelhantes na [página de entrada do Azure ad](https://docs.microsoft.com/azure/active-directory/customize-branding).

Embora sejam disponibilizadas personalizações semelhantes, algumas alterações visuais nas páginas de entrada devem ser esperadas após a conversão. Talvez você queira fornecer informações sobre as alterações esperadas em suas comunicações para os usuários.

> [!NOTE]
> A identidade visual da organização estará disponível somente se você comprar a licença Premium ou básica para Azure Active Directory ou se tiver uma licença do Office 365.

## <a name="plan-deployment-and-support"></a>Planejar a implantação e o suporte

Conclua as tarefas descritas nesta seção para ajudá-lo a planejar a implantação e o suporte.

### <a name="plan-the-maintenance-window"></a>Planejar a janela de manutenção

Embora o processo de conversão de domínio seja relativamente rápido, o Azure AD pode continuar enviando algumas solicitações de autenticação para seus servidores de AD FS por até quatro horas após a conclusão da conversão de domínio. Durante essa janela de quatro horas e, dependendo de vários caches do lado de serviço, o Azure AD pode não aceitar essas autenticações. Os usuários podem receber um erro. O usuário ainda pode se autenticar com êxito no AD FS, mas o Azure AD não aceita mais o token emitido pelo usuário porque a confiança da Federação agora é removida.

Somente os usuários que acessam os serviços por meio de um navegador da Web durante essa janela de pós-conversão antes da limpeza do cache do lado do serviço são afetados. Os clientes herdados (Exchange ActiveSync, Outlook 2010/2013) não devem ser afetados porque o Exchange Online mantém um cache de suas credenciais por um determinado período de tempo. O cache é usado para autenticar silenciosamente o usuário. O usuário não precisa retornar para AD FS. As credenciais armazenadas no dispositivo para esses clientes são usadas para se autenticar silenciosamente após a limpeza desse cache. Os usuários não devem receber prompts de senha como resultado do processo de conversão de domínio. 

Clientes de autenticação moderna (aplicativos do Office 2016 e do Office 2013, iOS e Android) usam um token de atualização válido para obter novos tokens de acesso para acesso contínuo aos recursos em vez de retornar para AD FS. Esses clientes estão imunes a quaisquer prompts de senha resultantes do processo de conversão de domínio. Os clientes continuarão a funcionar sem configuração adicional.

> [!IMPORTANT]
> Não desligue seu ambiente de AD FS ou remova a relação de confiança de terceira parte confiável do Office 365 até ter verificado que todos os usuários podem se autenticar com êxito usando a autenticação de nuvem.

### <a name="plan-for-rollback"></a>Planejar a reversão

Se você encontrar um problema importante que não pode ser resolvido rapidamente, poderá decidir reverter a solução para a Federação. É importante planejar o que fazer se sua implantação não for distribuída conforme o esperado. Se a conversão do domínio ou dos usuários falhar durante a implantação, ou se você precisar reverter para a Federação, você deve entender como atenuar qualquer interrupção e reduzir o efeito sobre os usuários.

#### <a name="to-roll-back"></a>Para reverter

Para planejar a reversão, verifique o design da Federação e a documentação de implantação para obter detalhes específicos da implantação. O processo deve incluir estas tarefas:

* Convertendo domínios gerenciados em domínios federados usando o cmdlet **Convert-MSOLDomainToFederated** .
* Se necessário, Configurando regras de declarações adicionais.

### <a name="plan-communications"></a>Planejar comunicações

Uma parte importante do planejamento da implantação e do suporte é garantir que seus usuários sejam informados proativamente sobre alterações futuras. Os usuários devem saber com antecedência o que podem experimentar e o que é necessário. 

Depois que a sincronização de hash de senha e o SSO contínuo são implantados, a experiência de entrada do usuário para acessar o Office 365 e outros recursos que são autenticados por meio de alterações no Azure AD. Os usuários que estão fora da rede veem apenas a página de entrada do Azure AD. Esses usuários não são redirecionados para a página baseada em formulários que é apresentada por servidores proxy de aplicativo Web voltados para o público.

Inclua os seguintes elementos em sua estratégia de comunicação:

* Notifique os usuários sobre as funcionalidades futuras e lançadas usando:
   * Email e outros canais de comunicação internos.
   * Elementos visuais, como cartazes.
   * Executive, Live ou outras comunicações.
* Determine quem irá personalizar as comunicações e quem enviará as comunicações e quando.

## <a name="implement-your-solution"></a>Implementar sua solução

Você planejou sua solução. Agora, agora você pode implementá-lo. A implementação envolve os seguintes componentes:

* Habilitando a sincronização de hash de senha.
* Preparando para o SSO contínuo.
* Alterar o método de entrada para a sincronização de hash de senha e habilitar o SSO contínuo.

### <a name="step-1-enable-password-hash-synchronization"></a>Passo 1: Ativar a sincronização de hash de palavra-passe

A primeira etapa para implementar essa solução é habilitar a sincronização de hash de senha usando o assistente de Azure AD Connect. A sincronização de hash de senha é um recurso opcional que você pode habilitar em ambientes que usam a Federação. Não há nenhum efeito no fluxo de autenticação. Nesse caso, Azure AD Connect começará a sincronizar os hashes de senha sem afetar os usuários que entram usando a Federação.

Por esse motivo, recomendamos que você conclua esta etapa como uma tarefa de preparação bem antes de alterar o método de entrada do seu domínio. Em seguida, você terá bastante tempo para verificar se a sincronização de hash de senha funciona corretamente.

Para habilitar a sincronização de hash de senha:

1. No Azure AD Connect Server, abra o assistente de Azure AD Connect e, em seguida, selecione **Configurar**.
2. Selecione **Personalizar opções de sincronização**e, em seguida, selecione **Avançar**.
3. Na página **conectar ao Azure ad** , insira o nome de usuário e a senha de uma conta de administrador global.
4. Na página **conectar seus diretórios** , selecione **Avançar**.
5. Na página **filtragem de domínio e UO** , selecione **Avançar**.
6. Na página **recursos opcionais** , selecione **sincronização de senha**e, em seguida, selecione **Avançar**.
 
   ![Captura de tela da opção de sincronização de senha selecionada na página recursos opcionais](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)<br />
7. Selecione **Avançar** nas páginas restantes. Na última página, selecione **Configurar**.
8. Azure AD Connect começa a sincronizar os hashes de senha na próxima sincronização.

Depois que a sincronização de hash de senha estiver habilitada, os hashes de senha para todos os usuários no escopo de sincronização Azure AD Connect serão rehash e gravados no Azure AD. Dependendo do número de usuários, essa operação pode levar minutos ou várias horas.

Para fins de planejamento, você deve estimar que aproximadamente 20.000 usuários são processados em 1 hora.

Para verificar se a sincronização de hash de senha funciona corretamente, conclua a tarefa de **solução de problemas** no assistente de Azure ad Connect:

1. Abra uma nova sessão do Windows PowerShell no seu Azure AD Connect Server usando a opção Executar como administrador.
2. Execute `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted`.
3. Inicie o assistente de Azure AD Connect.
4. Vá para a página **tarefas adicionais** , selecione **solucionar problemas**e, em seguida, selecione **Avançar**.
5. Na página de **solução de problemas** , selecione **Iniciar** para iniciar o menu de solução de problemas no PowerShell.
6. No menu principal, selecione **solucionar problemas de sincronização de hash de senha**.
7. No submenu, selecione **a sincronização de hash de senha não funciona**.

Para solucionar problemas, consulte solucionar problemas de [sincronização de hash de senha com sincronização Azure ad Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization).

### <a name="step-2-prepare-for-seamless-sso"></a>Passo 2: Preparar para SSO contínuo

Para que seus dispositivos usem o SSO contínuo, você deve adicionar uma URL do Azure AD às configurações de zona da intranet do usuário usando uma política de grupo no Active Directory.

Por padrão, os navegadores da Web calculam automaticamente a zona correta, a Internet ou a intranet, a partir de uma URL. Por exemplo, **http:\/\/contoso/** Maps para a zona da intranet e **http\/:\/intranet.contoso.com** mapeia para a zona da Internet (porque a URL contém um ponto). Os navegadores enviam tíquetes Kerberos para um ponto de extremidade de nuvem, como a URL do Azure AD, somente se você adicionar explicitamente a URL à zona de intranet do navegador.

Conclua as etapas para [distribuir](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start) as alterações necessárias em seus dispositivos.

> [!IMPORTANT]
> Fazer essa alteração não modifica a maneira como os usuários entram no Azure AD. No entanto, é importante que você aplique essa configuração a todos os seus dispositivos antes de continuar. Os usuários que entram em dispositivos que não receberam essa configuração simplesmente são necessários para inserir um nome de usuário e senha para entrar no Azure AD.

### <a name="step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso"></a>Passo 3: Alterar o método de entrada para a sincronização de hash de senha e habilitar o SSO contínuo

Você tem duas opções para alterar o método de entrada para a sincronização de hash de senha e habilitar o SSO contínuo.

#### <a name="option-a-switch-from-federation-to-password-hash-synchronization-by-using-azure-ad-connect"></a>Opção A: Alternar da Federação para a sincronização de hash de senha usando Azure AD Connect

Use esse método se você configurou inicialmente seu ambiente de AD FS usando Azure AD Connect. Você não poderá usar esse método se *não* tiver configurado originalmente seu ambiente de AD FS usando Azure ad Connect.

Primeiro, altere o método de entrada:

1. No Azure AD Connect Server, abra o assistente de Azure AD Connect.
2. Selecione **alterar entrada do usuário**e, em seguida, selecione **Avançar**. 

   ![Captura de tela da opção alterar entrada do usuário na página tarefas adicionais](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)<br />
3. Na página **conectar ao Azure ad** , insira o nome de usuário e a senha de uma conta de administrador global.
4. Na página de **entrada do usuário** , selecione o **botão sincronização de hash de senha**. Certifique-se de marcar a caixa de seleção não **converter contas de usuário** . A opção foi preterida. Selecione **habilitar logon único**e, em seguida, selecione **Avançar**.

   ![Captura de tela da página habilitar logon único](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)<br />

   > [!NOTE]
   > A partir do Azure AD Connect versão 1.1.880.0, a caixa de seleção **logon único contínuo** é marcada por padrão.

   > [!IMPORTANT]
   > Você pode ignorar com segurança os avisos que indicam que a conversão de usuário e a sincronização de hash de senha completa são etapas necessárias para converter de Federação para autenticação na nuvem. Observe que essas etapas não são mais necessárias. Se você ainda vir esses avisos, verifique se está executando a versão mais recente do Azure AD Connect e se está usando a versão mais recente deste guia. Para obter mais informações, consulte a seção [atualizar Azure ad Connect](#update-azure-ad-connect).

5. Na página **habilitar logon único** , insira as credenciais da conta de administrador de domínio e, em seguida, selecione **Avançar**.

   ![Captura de tela da página habilitar logon único](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)<br />

   > [!NOTE]
   > As credenciais de conta de administrador de domínio são necessárias para habilitar o SSO contínuo. O processo conclui as seguintes ações, que exigem essas permissões elevadas. As credenciais da conta de administrador de domínio não são armazenadas no Azure AD Connect ou no Azure AD. As credenciais da conta de administrador de domínio são usadas apenas para ativar o recurso. As credenciais são descartadas quando o processo é concluído com êxito.
   >
   > 1. Uma conta de computador chamada AZUREADSSOACC (que representa o Azure AD) é criada em sua instância de Active Directory local.
   > 2. A chave de descriptografia Kerberos da conta do computador é compartilhada com segurança com o Azure AD.
   > 3. Dois SPNs (nomes de entidade de serviço) Kerberos são criados para representar duas URLs que são usadas durante a entrada do Azure AD.

6. Na página **pronto para configurar** , verifique se a caixa de seleção **iniciar o processo de sincronização quando a configuração for concluída** está marcada. Em seguida, selecione **Configurar**.

      ![Captura de tela da página pronto para configurar](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)<br />

   > [!IMPORTANT]
   > Neste ponto, todos os seus domínios federados serão alterados para a autenticação gerenciada. A sincronização de hash de senha é o novo método de autenticação.

7. No portal do AD do Azure, selecione **Azure Active Directory** > **Azure ad Connect**.
8. Verifique estas configurações:
   * A **Federação** está definida como **desabilitada**.
   * O **logon único contínuo** está definido como **habilitado**.
   * A **sincronização de senha** está definida como **habilitada**.<br /> 

   ![Captura de tela que mostra as configurações na seção de entrada do usuário](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)<br />

Pule para o [teste e as próximas etapas](#testing-and-next-steps).

   > [!IMPORTANT]
   > Ignore a seção **opção B: Alterne da Federação para a sincronização de hash de senha usando o**Azure ad Connect e o PowerShell. As etapas dessa seção não se aplicam se você escolher a opção A para alterar o método de entrada para a sincronização de hash de senha e habilitar o SSO contínuo.

#### <a name="option-b-switch-from-federation-to-password-hash-synchronization-using-azure-ad-connect-and-powershell"></a>Opção B: Alternar da Federação para a sincronização de hash de senha usando o Azure AD Connect e o PowerShell

Use esta opção se você não configurou inicialmente seus domínios federados usando Azure AD Connect. Durante esse processo, você habilita o SSO contínuo e muda seus domínios de federados para gerenciado.

1. No Azure AD Connect Server, abra o assistente de Azure AD Connect.
2. Selecione **alterar entrada do usuário**e, em seguida, selecione **Avançar**.
3. Na página **conectar ao Azure ad** , insira o nome de usuário e a senha de uma conta de administrador global.
4. Na página de **entrada do usuário** , selecione o botão **sincronização de hash de senha** . Selecione **habilitar logon único**e, em seguida, selecione **Avançar**.

   Antes de habilitar a sincronização de hash de senha: ![Captura de tela que mostra a opção não configurar na página de entrada do usuário](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)<br />

   Depois de habilitar a sincronização de hash de senha: ![Captura de tela que mostra as novas opções na página de entrada do usuário](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)<br />
   
   > [!NOTE]
   > A partir do Azure AD Connect versão 1.1.880.0, a caixa de seleção **logon único contínuo** é marcada por padrão.

5. Na página **habilitar logon único** , insira as credenciais para uma conta de administrador de domínio e, em seguida, selecione **Avançar**.

   > [!NOTE]
   > As credenciais de conta de administrador de domínio são necessárias para habilitar o SSO contínuo. O processo conclui as seguintes ações, que exigem essas permissões elevadas. As credenciais da conta de administrador de domínio não são armazenadas no Azure AD Connect ou no Azure AD. As credenciais da conta de administrador de domínio são usadas apenas para ativar o recurso. As credenciais são descartadas quando o processo é concluído com êxito.
   >
   > 1. Uma conta de computador chamada AZUREADSSOACC (que representa o Azure AD) é criada em sua instância de Active Directory local.
   > 2. A chave de descriptografia Kerberos da conta do computador é compartilhada com segurança com o Azure AD.
   > 3. Dois SPNs (nomes de entidade de serviço) Kerberos são criados para representar duas URLs que são usadas durante a entrada do Azure AD.

6. Na página **pronto para configurar** , verifique se a caixa de seleção **iniciar o processo de sincronização quando a configuração for concluída** está marcada. Em seguida, selecione **Configurar**.

   ![Captura de tela que mostra o botão configurar na página pronto para configurar](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)<br />
   Quando você seleciona o botão **Configurar** , o SSO contínuo é configurado conforme indicado na etapa anterior. A configuração de sincronização de hash de senha não é modificada porque foi habilitada anteriormente.

   > [!IMPORTANT]
   > Nenhuma alteração é feita na maneira como os usuários entram no momento.

7. No portal do AD do Azure, verifique estas configurações:
   * A **Federação** está definida como **habilitada**.
   * O **logon único contínuo** está definido como **habilitado**.
   * A **sincronização de senha** está definida como **habilitada**.

   ![Captura de tela que mostra as configurações na seção de entrada do usuário](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>Converter domínios de federado para gerenciado

Neste ponto, a Federação ainda está habilitada e operacional para seus domínios. Para continuar com a implantação, cada domínio precisa ser convertido de federado para gerenciado para forçar a autenticação do usuário por meio da sincronização de hash de senha.

> [!IMPORTANT]
> Você não precisa converter todos os domínios ao mesmo tempo. Você pode optar por começar com um domínio de teste em seu locatário de produção ou começar com seu domínio que tem o número mais baixo de usuários.

Conclua a conversão usando o módulo do PowerShell do Azure AD:

1. No PowerShell, entre no Azure AD usando uma conta de administrador global.
2. Para converter o primeiro domínio, execute o seguinte comando:

   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```

3. No portal do AD do Azure, selecione **Azure Active Directory** > **Azure ad Connect**.
4. Verifique se o domínio foi convertido para gerenciado executando o seguinte comando:

   ``` PowerShell
   Get-MsolDomain -DomainName <domain name>
   ```

## <a name="testing-and-next-steps"></a>Testes e próximas etapas

Conclua as tarefas a seguir para verificar a sincronização de hash de senha e concluir o processo de conversão.

### <a name="test-authentication-by-using-password-hash-synchronization"></a>Testar a autenticação usando a sincronização de hash de senha 

Quando seu locatário usava a identidade federada, os usuários foram redirecionados da página de entrada do Azure AD para seu ambiente de AD FS. Agora que o locatário está configurado para usar a sincronização de hash de senha em vez da autenticação federada, os usuários não são redirecionados para AD FS. Em vez disso, os usuários entram diretamente na página de entrada do Azure AD.

Para testar a sincronização de hash de senha:

1. Abra o Internet Explorer no modo InPrivate para que o SSO contínuo não o conecte automaticamente.
2. Vá para a página de entrada do Office 365 ([https://portal.office.com](https://portal.office.com/)).
3. Insira um UPN de usuário e, em seguida, selecione **Avançar**. Certifique-se de inserir o UPN de um usuário híbrido que foi sincronizado a partir de sua instância de Active Directory local e quem usou anteriormente a autenticação federada. É exibida uma página na qual você insere o nome de usuário e a senha:

   ![Captura de tela que mostra a página de entrada na qual você insere um nome de usuário](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

   ![Captura de tela que mostra a página de entrada na qual você insere uma senha](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

4. Depois de inserir a senha e selecionar **entrar**, você será redirecionado para o portal do Office 365.

   ![Captura de tela que mostra o portal do Office 365](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)


### <a name="test-seamless-sso"></a>Testar SSO contínuo

1. Entre em um computador ingressado no domínio que esteja conectado à rede corporativa.
2. No Internet Explorer ou Chrome, vá para uma das seguintes URLs (substitua "contoso" pelo seu domínio):

   * https:\/\/myapps.Microsoft.com/contoso.com
   * https:\/\/myapps.Microsoft.com/contoso.onmicrosoft.com

   O usuário é redirecionado brevemente para a página de entrada do Azure AD, que mostra a mensagem "tentando conectá-lo". O usuário não é solicitado a fornecer um nome de usuários ou senha.<br />

   ![Captura de tela que mostra a página de entrada e a mensagem do Azure AD](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)<br />
3. O usuário é redirecionado e conectado com êxito ao painel de acesso:

   > [!NOTE]
   > O SSO contínuo funciona nos serviços do Office 365 que dão suporte à dica de domínio (por exemplo, myapps.microsoft.com/contoso.com). Atualmente, o portal do Office 365 (portal.office.com) não dá suporte a dicas de domínio. Os usuários são obrigados a inserir um UPN. Depois que um UPN é inserido, o SSO contínuo recupera o tíquete Kerberos em nome do usuário. O usuário está conectado sem inserir uma senha.

   > [!TIP]
   > Considere implantar a [junção híbrida do Azure AD no Windows 10](https://docs.microsoft.com/azure/active-directory/device-management-introduction) para uma experiência de SSO aprimorada.

### <a name="remove-the-relying-party-trust"></a>Remover a terceira parte confiável

Depois de validar que todos os usuários e clientes são autenticados com êxito por meio do Azure AD, é seguro remover a terceira parte confiável do Office 365.

Se você não usar AD FS para outras finalidades (ou seja, para outras relações de confiança de terceira parte confiável), será seguro descomissionar AD FS neste ponto.

### <a name="rollback"></a>Reversão

Se você descobrir um problema importante e não puder resolvê-lo rapidamente, poderá optar por reverter a solução para a Federação.

Consulte a documentação de design e implantação de Federação para obter detalhes específicos de sua implantação. O processo deve envolver estas tarefas:

* Converta domínios gerenciados para autenticação federada usando o cmdlet **Convert-MSOLDomainToFederated** .
* Se necessário, configure regras de declarações adicionais.

### <a name="sync-userprincipalname-updates"></a>Sincronizar atualizações userPrincipalName

Historicamente, as atualizações para o atributo **userPrincipalName** , que usa o serviço de sincronização do ambiente local, são bloqueadas, a menos que essas duas condições sejam verdadeiras:

* O usuário está em um domínio de identidade gerenciado (não federado).
* Não foi atribuída uma licença ao usuário.

Para saber como verificar ou ativar esse recurso, consulte [sincronizar as atualizações userPrincipalName](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features).

### <a name="troubleshooting"></a>Resolução de problemas

Sua equipe de suporte deve entender como solucionar problemas de autenticação que surgem durante ou após a alteração da Federação para gerenciada. Use a seguinte documentação de solução de problemas para ajudar sua equipe de suporte a se familiarizar com as etapas de solução de problemas comuns e as ações apropriadas que podem ajudar a isolar e resolver o problema.

[Solucionar problemas Azure Active Directory sincronização de hash de senha](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)

[Solucionar problemas Azure Active Directory logon único contínuo](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sso)

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Sobrepor a chave de descriptografia de Kerberos do SSO contínuo

É importante sobrepor frequentemente a chave de descriptografia Kerberos da conta de computador AZUREADSSOACC (que representa o Azure AD). A conta de computador AZUREADSSOACC é criada na floresta Active Directory local. É altamente recomendável que você sobreponha a chave de descriptografia Kerberos pelo menos a cada 30 dias para se alinhar com o modo como Active Directory membros do domínio enviam alterações de senha. Não há nenhum dispositivo associado anexado ao objeto de conta de computador AZUREADSSOACC, portanto, você deve executar a substituição manualmente.

Inicie a substituição da chave de descriptografia de Kerberos do SSO contínuo no servidor local que está executando o Azure AD Connect.

Para obter mais informações, consulte [como fazer sobrepor a chave de descriptografia Kerberos da conta de computador AZUREADSSOACC?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre os [conceitos de design de Azure ad Connect](plan-connect-design-concepts.md).
* Escolha a [autenticação correta](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn).
* Saiba mais sobre as [topologias com suporte](plan-connect-design-concepts.md).
