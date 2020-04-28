---
title: 'Azure AD Connect: Migrar da federação para phs para Azure AD Microsoft Docs'
description: Este artigo tem informações sobre como mover o seu ambiente de identidade híbrida da federação para a sincronização de hash password.
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
ms.openlocfilehash: b621c9cbc35d0e9956f6648d870102affd84c24f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76028387"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-active-directory"></a>Migrar da federação para a sincronização de hash de senha para o Diretório Ativo Azure

Este artigo descreve como mover os domínios da sua organização dos Serviços da Federação de Diretórios Ativos (AD FS) para a sincronização de hash de senha.

> [!NOTE]
> Alterar o seu método de autenticação requer planeamento, testes e potencialmente tempo de inatividade. [O lançamento encenado](how-to-connect-staged-rollout.md) fornece uma forma alternativa de testar e migrar gradualmente da federação para a autenticação em nuvem usando a sincronização de hash de senha.

## <a name="prerequisites-for-migrating-to-password-hash-synchronization"></a>Pré-requisitos para a migração para a sincronização do hash de senha

São necessários os seguintes pré-requisitos para migrar da utilização de AD FS para a sincronização de hash de palavra-passe.

### <a name="update-azure-ad-connect"></a>Atualizar ligação Azure AD

Como mínimo para executar com sucesso os passos para migrar para a sincronização de hash de senha, você deve ter [Azure AD connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0. Esta versão contém alterações significativas na forma como a conversão de sinal é realizada e reduz o tempo total para migrar da Federação para a Autenticação da Nuvem de horas para minutos.


> [!IMPORTANT]
> Pode ler em documentação, ferramentas e blogs desatualizados que a conversão do utilizador é necessária quando converte domínios de identidade federada para identidade gerida. A conversão de *utilizadores* já não é necessária. A Microsoft está a trabalhar para atualizar documentação e ferramentas para refletir esta mudança.

Para atualizar o Azure AD Connect, complete os passos no [Azure AD Connect: Upgrade para a versão mais recente](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

### <a name="password-hash-synchronization-required-permissions"></a>Sincronização de hash de palavra-passe requer permissões necessárias

Pode configurar o Azure AD Connect utilizando definições expressas ou uma instalação personalizada. Se utilizou a opção de instalação personalizada, as [permissões necessárias](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-accounts-permissions) para a sincronização de hash de senha podem não estar em vigor.

A conta de serviço Azure AD Connect Ative Directory Domain Services (AD DS) requer as seguintes permissões para sincronizar hashes de senha:

* Replicar alterações de diretório
* Replicar mudanças de diretório todos

Agora é uma boa hora para verificar se estas permissões estão em vigor para todos os domínios da floresta.

### <a name="plan-the-migration-method"></a>Planeie o método de migração

Pode escolher entre dois métodos para migrar da gestão de identidade federada para a sincronização de hash password e inscrição única sem emenda (SSO). O método que utiliza depende de como a sua instância AD FS foi originalmente configurada.

* **Ligação Azure AD**. Se configurar originalmente a AD FS utilizando o Azure AD Connect, *tem* de alterar para a sincronização de hash por palavra-passe utilizando o assistente Azure AD Connect.

   O Azure AD Connect executa automaticamente o cmdlet **set-MsolDomainAuthentication** quando altera o método de entrada do utilizador. A Azure AD Connect desfedera automaticamente todos os domínios federados verificados no seu inquilino Azure AD.

   > [!NOTE]
   > Atualmente, se usou originalmente o Azure AD Connect para configurar a AD FS, não pode evitar desfederar todos os domínios do seu inquilino quando muda o sessão de sessão do utilizador para a sincronização de hash de senha. ‎
* **Azure AD Connect com PowerShell**. Só pode utilizar este método se não configurar originalmente a AD FS utilizando o Azure AD Connect. Para esta opção, ainda deve alterar o método de entrada do utilizador através do assistente Azure AD Connect. A diferença de núcleo com esta opção é que o assistente não executa automaticamente o cmdlet **set-MsolDomainAuthentication.** Com esta opção, tem controlo total sobre quais os domínios convertidos e em que ordem.

Para entender que método deve utilizar, complete os passos nas seguintes secções.

#### <a name="verify-current-user-sign-in-settings"></a>Verifique as definições de sessão de sessão do utilizador atuais

Para verificar as definições de sessão de sessão do utilizador atuais:

1. Inscreva-se no [portal Azure AD](https://aad.portal.azure.com/) utilizando uma conta De Administrador Global.
2. Na secção de **sessão de sessão do Utilizador,** verifique as seguintes definições:
   * **A Federação** está definida para **ativar**.
   * O **único sinal insígnio está** definido para **desativado**.
   * **A autenticação pass-through** está definida para **desativado**.

   ![Screenshot das definições na secção de entrada de utilizador de ligação ad azure](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-the-azure-ad-connect-configuration"></a>Verifique a configuração Azure AD Connect

1. No seu servidor Azure AD Connect, abra o Azure AD Connect. Selecione **Configurar**.
2. Na página de **tarefas Adicionais,** selecione **Ver configuração de corrente**, e, em seguida, selecione **Next**.<br />

   ![Screenshot da opção de configuração atual do Ver selecionada na página de tarefas adicionais](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)<br />
3. Na página **Review Your Solution,** note o estado de sincronização de **hash** password.<br /> 

   * Se a sincronização de **hash** password estiver definida para **Desativar,** complete os passos deste artigo para o ativar.
   * Se a sincronização do hash password estiver definida para **ativada,** pode saltar a secção **Passo 1: Ativar** a sincronização de hash de **palavra-passe** neste artigo.
4. Na página De Rever a sua página de **soluções,** percorra os **Serviços da Federação de Diretórios Ativos (AD FS)**.<br />

   * Se a configuração AD FS aparecer nesta secção, pode assumir com segurança que o AD FS foi originalmente configurado utilizando o Azure AD Connect. Pode converter os seus domínios de identidade federada para identidade gerida utilizando a opção de entrada de utilizador azure AD Connect **Change.** O processo é detalhado na secção Opção A: Mude da federação para a sincronização de hash de **senha utilizando o Azure AD Connect**.
   * Se o AD FS não estiver listado nas definições atuais, deve converter manualmente os seus domínios de identidade federada para a identidade gerida utilizando o PowerShell. Para obter mais informações sobre este processo, consulte a secção Opção B: Mude da federação para a sincronização de hash de **senha utilizando o Azure AD Connect e o PowerShell**.

### <a name="document-current-federation-settings"></a>Documentar as definições atuais da federação

Para encontrar as definições atuais da federação, execute o **Cmdlet Get-MsolDomainFederationSettings:**

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Exemplo:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

Verifique quaisquer configurações que possam ter sido personalizadas para a sua federação de design e documentação de implementação. Especificamente, procure personalizações no **PreferredAuthenticationProtocol,** **SupportsMfa**, e **PromptLoginBehavior**.

Para obter mais informações, veja estes artigos:

* [Suporte de parâmetro de alerta aD FS=login](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Se o **SupportsMfa** estiver definido para **True,** está a utilizar uma solução de autenticação multifactor no local para injetar um desafio de segundo fator no fluxo de autenticação do utilizador. Esta configuração já não funciona para cenários de autenticação Da Azure Depois de converter este domínio de autenticação federada para gerida. Depois de desativar a federação, corta a relação com a sua federação no local e isso inclui adaptadores MFA no local. 
>
> Em vez disso, utilize o serviço baseado em nuvem de autenticação multifactor Azure para executar a mesma função. Avalie cuidadosamente os seus requisitos de autenticação de vários fatores antes de continuar. Antes de converter os seus domínios, certifique-se de que compreende como utilizar a Autenticação Multi-Factor Azure, as implicações de licenciamento e o processo de registo do utilizador.

#### <a name="back-up-federation-settings"></a>Back up definições da federação

Embora não sejam feitas alterações a outras partes que dependem da sua quinta AD FS durante os processos descritos neste artigo, recomendamos que tenha uma cópia de segurança válida atual da sua quinta AD FS da quais possa restaurar. Pode criar uma cópia de segurança válida de corrente utilizando a ferramenta gratuita de restauro rápido Microsoft [AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool). Você pode usar a ferramenta para apoiar AD FS, e restaurar uma fazenda existente ou criar uma nova fazenda.

Se optar por não utilizar a Ferramenta de Restauro Rápido AD FS, no mínimo, deverá exportar a Plataforma de Identidade Microsoft Office 365, confiando na confiança do partido e quaisquer regras de reclamação personalizadas associadas que adicionou. Pode exportar a confiança do partido dependente e as regras de reclamação associadas utilizando o seguinte exemplo powerShell:

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>Considerações de implantação e utilização de AD FS

Esta secção descreve considerações de implantação e detalhes sobre a utilização de AD FS.

### <a name="current-ad-fs-use"></a>Uso atual de AD FS

Antes de converter-se de identidade federada para identidade gerida, veja de perto como usa aTualmente AD FS para Azure AD, Office 365, e outras aplicações (trusts do partido). Especificamente, considere os cenários descritos na tabela seguinte:

| Se | Então |
|-|-|
| Planeia continuar a utilizar a AD FS com outras aplicações (com além do Azure AD e do Office 365). | Depois de converter os seus domínios, utilizará tanto a AD FS como a Azure AD. Considere a experiência do utilizador. Em alguns cenários, os utilizadores podem ser obrigados a autenticar duas vezes: uma para a AD Azure (onde um utilizador obtém acesso sSO a outras aplicações, como o Office 365), e novamente para quaisquer aplicações que ainda estejam vinculadas à AD FS como uma confiança de parte dependente. |
| A sua instância AD FS é fortemente personalizada e baseia-se em definições específicas de personalização no ficheiro onload.js (por exemplo, se alterar a experiência de inscrição para que os utilizadores utilizem apenas um formato **SamAccountName** para o seu nome de utilizador em vez de um Nome Principal do Utilizador (UPN), ou a sua organização marcou fortemente a experiência de inscrição). O ficheiro onload.js não pode ser duplicado em Azure AD. | Antes de continuar, deve verificar se o Azure AD pode satisfazer os seus atuais requisitos de personalização. Para obter mais informações e orientação, consulte as secções da marca AD FS e a personalização AD FS.|
| Usa AD FS para bloquear versões anteriores de clientes de autenticação.| Considere substituir os controlos AD FS que bloqueiam versões anteriores de clientes de autenticação utilizando uma combinação de [controlos](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) de acesso condicional e regras de [acesso ao cliente online](https://aka.ms/EXOCAR)de troca . |
| Exige que os utilizadores realizem a autenticação de vários fatores contra uma solução de servidor de autenticação multifactor no local quando os utilizadores autenticam a AD FS.| Num domínio de identidade gerido, não é possível injetar um desafio de autenticação multifactor através da solução de autenticação multifactor no local no fluxo de autenticação. No entanto, pode utilizar o serviço de autenticação multi-factor Azure para autenticação de vários fatores após a conversão do domínio.<br /><br /> Se os seus utilizadores não utilizarem atualmente a Autenticação Multifactor Azure, é necessária uma etapa de registo de um utilizador único. Deve preparar e comunicar o registo planeado aos seus utilizadores. |
| Atualmente utiliza políticas de controlo de acesso (regras AuthZ) na AD FS para controlar o acesso ao Office 365.| Considere substituir as políticas por as [políticas](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) equivalentes de Acesso Condicional Azure AD e trocar regras de acesso online ao [cliente.](https://aka.ms/EXOCAR)|

### <a name="common-ad-fs-customizations"></a>Personalizações Comuns de AD FS

Esta secção descreve personalizações comuns de AD FS.

#### <a name="insidecorporatenetwork-claim"></a>Reivindicação InsideCorporateNetwork

A AD FS emite a alegação **insideCorporateNetwork** se o utilizador que está autenticando estiver dentro da rede corporativa. Esta alegação pode então ser transmitida à Azure AD. A alegação é utilizada para contornar a autenticação de vários fatores com base na localização da rede do utilizador. Para saber determinar se esta funcionalidade está ativada atualmente em AD FS, consulte [IPs fidedignos para utilizadores federados](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud).

A alegação **InsideCorporateNetwork** não está disponível depois de os seus domínios serem convertidos para sincronização de hash de palavra-passe. Pode utilizar [localizações nomeadas em Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) para substituir esta funcionalidade.

Depois de configurar as localizações nomeadas, deve atualizar todas as políticas de Acesso Condicional que foram configuradas para incluir ou excluir a rede **Todos os locais de confiança** ou os valores de **IPs fidedignos** do MFA para refletir as novas localizações nomeadas.

Para mais informações sobre a condição de **localização** no Acesso Condicional, consulte localizações de [Acesso Condicional do Diretório Ativo](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

#### <a name="hybrid-azure-ad-joined-devices"></a>Dispositivos híbridos azure ad-join

Ao aderir a um dispositivo ao Azure AD, pode criar regras de Acesso Condicional que aplicam que os dispositivos cumprem as suas normas de acesso para segurança e conformidade. Além disso, os utilizadores podem iniciar sessão num dispositivo utilizando uma conta organizacional ou escolar em vez de uma conta pessoal. Quando utilizar dispositivos híbridos com a AD azure, pode juntar-se aos seus dispositivos de domínio Ative Directory para o Azure AD. O seu ambiente federado pode ter sido criado para usar esta funcionalidade.

Para garantir que a adesão híbrida continua a funcionar para quaisquer dispositivos que estejam ligados ao domínio depois de os seus domínios serem convertidos para sincronização de hash de palavra-passe, para clientes do Windows 10, deve utilizar opções do dispositivo Azure AD Connect para sincronizar contas de computador Ative Directory para a AD Azure. 

Para as contas de computador Do Windows 8 e Windows 7, a adesão híbrida utiliza sem emenda sem emenda sem emenda para registar o computador em Azure AD. Não é preciso sincronizar contas de computador do Windows 8 e do Windows 7, como faz para dispositivos Windows 10. No entanto, tem de implementar um ficheiro de workplacejoin.exe atualizado (através de um ficheiro .msi) para clientes windows 8 e Windows 7 para que possam registar-se utilizando SSO sem emenda. [Descarregue o ficheiro .msi](https://www.microsoft.com/download/details.aspx?id=53554).

Para mais informações, consulte os [dispositivos híbridos da Configure Azure AD](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup).

#### <a name="branding"></a>Imagem corporativa

Se a sua organização [personalizou as suas páginas de entrada aD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) para exibir informações mais pertinentes para a organização, considere fazer [personalizações semelhantes à página de entrada de ad-in do Azure AD](https://docs.microsoft.com/azure/active-directory/customize-branding).

Embora existam personalizações semelhantes, algumas alterações visuais nas páginas de inscrição devem ser esperadas após a conversão. Pode querer fornecer informações sobre as alterações esperadas nas suas comunicações aos utilizadores.

> [!NOTE]
> A marca da organização só está disponível se comprar a licença Premium ou Basic para o Azure Ative Directory ou se tiver uma licença do Office 365.

## <a name="plan-deployment-and-support"></a>Plan implantação e apoio

Complete as tarefas descritas nesta secção para ajudá-lo a planear a implementação e suporte.

### <a name="plan-the-maintenance-window"></a>Planeie a janela de manutenção

Embora o processo de conversão de domínio seja relativamente rápido, o Azure AD pode continuar a enviar alguns pedidos de autenticação para os seus servidores AD FS durante até quatro horas após a conversão do domínio. Durante esta janela de quatro horas, e dependendo de vários caches laterais de serviço, a Azure AD pode não aceitar estas autenticações. Os utilizadores podem receber um erro. O utilizador ainda pode autenticar com sucesso contra a AD FS, mas a Azure AD já não aceita o símbolo emitido pelo utilizador porque essa confiança da federação é agora removida.

Apenas os utilizadores que acedem aos serviços através de um navegador web durante esta janela de pós-conversão antes de a cache lateral do serviço ser apurado são afetados. Não se espera que os clientes legados (Exchange ActiveSync, Outlook 2010/2013) sejam afetados porque o Exchange Online mantém uma cache das suas credenciais durante um determinado período de tempo. A cache é utilizada para reautenticar silenciosamente o utilizador. O utilizador não tem de voltar ao AD FS. As credenciais armazenadas no dispositivo para estes clientes são usadas para se reautenticarem silenciosamente após este cached ser limpo. Não se espera que os utilizadores recebam qualquer aviso de senha como resultado do processo de conversão de domínio. 

Os clientes de autenticação moderna (Office 2016 e Office 2013, iOS e aplicativos Android) usam um token de atualização válido para obter novos tokens de acesso para acesso continuado aos recursos em vez de regressar em AD FS. Estes clientes são imunes a quaisquer indicações de senha resultantes do processo de conversão de domínio. Os clientes continuarão a funcionar sem configuração adicional.

> [!IMPORTANT]
> Não desligue o seu ambiente AD FS ou remova a confiança do Office 365 até verificar que todos os utilizadores podem autenticar com sucesso usando a autenticação em nuvem.

### <a name="plan-for-rollback"></a>Plano para retrocesso

Se encontrar um problema importante que não pode resolver rapidamente, pode decidir reverter a solução para a federação. É importante planear o que fazer se a sua implantação não for como pretendido. Se a conversão do domínio ou dos utilizadores falhar durante a implementação, ou se precisar de voltar para a federação, deve entender como mitigar qualquer falha e reduzir o efeito nos seus utilizadores.

#### <a name="to-roll-back"></a>Para recuar

Para planear a reversão, verifique a documentação de design e implementação da federação para obter os seus detalhes específicos de implementação. O processo deve incluir estas tarefas:

* Convertendo domínios geridos em domínios federados utilizando o cmdlet **Convert-MSOLDomainToFederated.**
* Se necessário, configurar regras adicionais de reclamações.

### <a name="plan-communications"></a>Planear as comunicações

Uma parte importante do planeamento da implementação e suporte é garantir que os seus utilizadores são proativamente informados sobre as próximas alterações. Os utilizadores devem saber antecipadamente o que podem experimentar e o que lhes é exigido. 

Após a sincronização de hash de palavra-passe e sem emenda SSO, a experiência de entrada do utilizador para aceder ao Office 365 e outros recursos que são autenticados através de alterações da AD Azure. Os utilizadores que estão fora da rede vêem apenas a página de entrada de adad Azure. Estes utilizadores não são redirecionados para a página baseada em formulários que é apresentada por servidores proxy de aplicação web virados para o exterior.

Inclua os seguintes elementos na sua estratégia de comunicação:

* Notifique os utilizadores sobre a funcionalidade que se avizinha e é lançada utilizando:
   * E-mail e outros canais de comunicação interna.
   * Visuais, como cartazes.
   * Executivo, ao vivo ou outras comunicações.
* Determine quem irá personalizar as comunicações e quem enviará as comunicações, e quando.

## <a name="implement-your-solution"></a>Implementar a sua solução

Planeou a sua solução. Agora, pode implementá-lo. A implementação envolve os seguintes componentes:

* Habilitando a sincronização de hash de senha.
* Preparando-se para sso sem emenda.
* Alterar o método de entrada para sincronização de hash de palavra-passe e permitir o SSO sem emenda.

### <a name="step-1-enable-password-hash-synchronization"></a>Passo 1: Ativar a sincronização de hash de palavra-passe

O primeiro passo para implementar esta solução é ativar a sincronização de hash de palavra-passe utilizando o assistente Azure AD Connect. A sincronização de hash de palavra-passe é uma funcionalidade opcional que pode ativar em ambientes que utilizam a federação. Não há nenhum efeito no fluxo de autenticação. Neste caso, o Azure AD Connect começará a sincronizar hashes de senha sem afetar os utilizadores que iniciam o insessão através da federação.

Por esta razão, recomendamos que complete este passo como tarefa de preparação muito antes de alterar o método de inscrição do seu domínio. Em seguida, terá tempo suficiente para verificar se a sincronização de hash de senha funciona corretamente.

Para permitir a sincronização de hash de palavra-passe:

1. No servidor Azure AD Connect, abra o assistente De Ligação AD Azure e, em seguida, **selecione Configurar**.
2. **Selecione Personalizar opções**de sincronização e, em seguida, selecione **Next**.
3. Na página **'Connect to Azure AD',** introduza o nome de utilizador e a palavra-passe de uma conta De Administrador Global.
4. Na página **Connect your Directdirecties,** selecione **Next**.
5. Na página de **filtragem de Domínio e OU,** selecione **Next**.
6. Na página **de funcionalidades Opcionais,** selecione sincronização de **palavra-passe**e, em seguida, selecione **Next**.
 
   ![Screenshot da opção de sincronização de palavra-passe selecionada na página de funcionalidades Opcionais](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)<br />
7. Selecione **Em seguida** nas páginas restantes. Na última página, **selecione Configure**.
8. O Azure AD Connect começa a sincronizar hashes de palavra-passe na sincronização seguinte.

Após a sincronização do hash da palavra-passe, a palavra-passe hashes para todos os utilizadores no âmbito de sincronização Azure AD Connect são rehasheed e escritas para Azure AD. Dependendo do número de utilizadores, esta operação pode demorar minutos ou várias horas.

Para fins de planeamento, deve estimar que aproximadamente 20.000 utilizadores são processados em 1 hora.

Para verificar se a sincronização de hash de palavra-passe funciona corretamente, complete a tarefa de **resolução de problemas** no assistente Azure AD Connect:

1. Abra uma nova sessão do Windows PowerShell no seu servidor Azure AD Connect utilizando a opção Executar como Administrador.
2. Correr `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted`ou.
3. Inicie o assistente Azure AD Connect.
4. Vá à página de **tarefas adicionais,** selecione **Troubleshoot**, e, em seguida, selecione **Next**.
5. Na página **Deresolução** de Problemas, selecione **Launch** para iniciar o menu de resolução de problemas no PowerShell.
6. No menu principal, selecione Sincronização de **hash de palavra-passe Troubleshoot**.
7. No submenu, selecione A sincronização do **hash password não funciona**de todo .

Para problemas de resolução de problemas, consulte a [sincronização de hash de palavra-passe de Troubleshoot com a sincronização Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization).

### <a name="step-2-prepare-for-seamless-sso"></a>Passo 2: Prepare-se para sso sem emenda

Para que os seus dispositivos utilizem SSO sem emenda, deve adicionar um URL Azure AD às definições da zona intranet dos utilizadores utilizando uma política de grupo em Diretório Ativo.

Por padrão, os navegadores da Web calculam automaticamente a zona correta, seja internet ou intranet, a partir de um URL. Por exemplo, **http:\/\/contoso/** mapas para a zona intranet e **http:\/\/intranet.contoso.com** mapas para a zona de internet (porque o URL contém um período). Os navegadores enviam bilhetes Kerberos para um ponto final na nuvem, como o URL DaA Azure, apenas se você adicionar explicitamente o URL à zona intranet do navegador.

Complete os passos para [lançar](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start) as alterações necessárias aos seus dispositivos.

> [!IMPORTANT]
> Fazer esta alteração não modifica a forma como os seus utilizadores entram no Azure AD. No entanto, é importante que aplique esta configuração a todos os seus dispositivos antes de prosseguir. Os utilizadores que iniciarem sessão em dispositivos que não tenham recebido esta configuração são simplesmente obrigados a introduzir um nome de utilizador e uma senha para iniciar sessão no Azure AD.

### <a name="step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso"></a>Passo 3: Alterar o método de entrada para sincronização de hash de palavra-passe e permitir o SSO sem emenda

Tem duas opções para alterar o método de entrada para sincronização de hash de senha e permitir o SSO sem emenda.

#### <a name="option-a-switch-from-federation-to-password-hash-synchronization-by-using-azure-ad-connect"></a>Opção A: Mude da federação para a sincronização de hash de senha utilizando o Azure AD Connect

Utilize este método se configurar inicialmente o seu ambiente AD FS utilizando o Azure AD Connect. Não pode utilizar este método se *não* configurar originalmente o seu ambiente AD FS utilizando o Azure AD Connect.

Primeiro, altere o método de inscrição:

1. No servidor Azure AD Connect, abra o assistente Azure AD Connect.
2. Selecione Alterar o **sessão do utilizador**e, em seguida, selecione **Next**. 

   ![Screenshot da opção de iniciar sessão do utilizador Alterar na página de tarefas adicionais](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)<br />
3. Na página **'Connect to Azure AD',** introduza o nome de utilizador e a palavra-passe de uma conta De Administrador Global.
4. Na página de entrada do **Utilizador,** selecione o botão de sincronização de **hash password**. Certifique-se de selecionar a caixa de verificação de contas de utilizador Não converter as contas de **utilizador.** A opção é depreciada. **Selecione Ativar um único sinal e,** em seguida, selecione **Next**.

   ![Screenshot da página de sinal único Enable](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)<br />

   > [!NOTE]
   > A partir da versão 1.1.880.0 do Azure AD Connect, a caixa de **verificação de sinal único Sem emenda** é selecionada por defeito.

   > [!IMPORTANT]
   > Pode ignorar com segurança os avisos que indicam que a conversão do utilizador e a sincronização completa do hash de palavra-passe são passos necessários para converter da federação para a autenticação na nuvem. Note que estes passos não são mais necessários. Se ainda vir estes avisos, certifique-se de que está a executar a versão mais recente do Azure AD Connect e que está a utilizar a versão mais recente deste guia. Para mais informações, consulte a secção [Update Azure AD Connect](#update-azure-ad-connect).

5. Na página **de entrada única enable,** introduza as credenciais da conta Do Administrador de Domínio e, em seguida, selecione **Next**.

   ![Screenshot da página de sinal único Enable](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)<br />

   > [!NOTE]
   > As credenciais de conta do Administrador de Domínio são necessárias para ativar o SSO sem emenda. O processo completa as seguintes ações, que requerem estas permissões elevadas. As credenciais de conta do Administrador de Domínio não são armazenadas no Azure AD Connect ou no Azure AD. As credenciais de conta do Administrador de Domínio são utilizadas apenas para ativar a funcionalidade. As credenciais são descartadas quando o processo termina com sucesso.
   >
   > 1. Uma conta de computador chamada AZUREADSSOACC (que representa a Azure AD) é criada na sua instância de Diretório Ativo no local.
   > 2. A chave de desencriptação Kerberos da conta de computador é partilhada de forma segura com a AD Azure.
   > 3. Dois nomes principais de serviço Kerberos (SPNs) são criados para representar dois URLs que são usados durante o sign-in Azure AD.

6. Na página **Ready to configure,** certifique-se de que o Iniciar o processo de **sincronização quando a configuração completa** a caixa de verificação é selecionada. Em seguida, **selecione Configurar**.

      ![Screenshot da página Ready to configurar](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)<br />

   > [!IMPORTANT]
   > Neste momento, todos os seus domínios federados mudarão para autenticação gerida. Sincronização de hash de palavra-passe é o novo método de autenticação.

7. No portal Azure AD, selecione **Azure Ative Directory** > **Azure AD Connect**.
8. Verifique estas definições:
   * **A Federação** está preparada para **deficientes.**
   * O **único sinal insígnio está** definido para **ativado**.
   * **O Password Sync** está definido para **Ativado**.<br /> 

   ![Screenshot que mostra as definições na secção de iniciar sessão do Utilizador](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)<br />

Saltar para [testes e próximos passos.](#testing-and-next-steps)

   > [!IMPORTANT]
   > Ignore a secção Opção B: Mude da federação para a sincronização de hash de **palavra-passe utilizando o Azure AD Connect e o PowerShell**. Os passos nessa secção não se aplicam se escolher a Opção A para alterar o método de entrada para sincronização de hash de palavra-passe e permitir o SSO sem emenda.

#### <a name="option-b-switch-from-federation-to-password-hash-synchronization-using-azure-ad-connect-and-powershell"></a>Opção B: Mude da federação para a sincronização de hash de palavra-passe utilizando o Azure AD Connect e o PowerShell

Utilize esta opção se não configurar inicialmente os seus domínios federados utilizando o Azure AD Connect. Durante este processo, permite o SSO sem emenda e muda os seus domínios de federado para gerido.

1. No servidor Azure AD Connect, abra o assistente Azure AD Connect.
2. Selecione Alterar o **sessão do utilizador**e, em seguida, selecione **Next**.
3. Na página **'Connect to Azure AD',** introduza o nome de utilizador e a palavra-passe para uma conta De Administrador Global.
4. Na página de entrada do **Utilizador,** selecione o botão de sincronização de **hash password.** **Selecione Ativar um único sinal e,** em seguida, selecione **Next**.

   Antes de ativar a sincronização ![de hash de palavra-passe: Screenshot que mostra que não configura a opção Não configurar na página de entrada do Utilizador](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)<br />

   Depois de ativar a sincronização ![de hash de palavra-passe: Screenshot que mostra novas opções na página de entrada do Utilizador](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)<br />
   
   > [!NOTE]
   > A partir da versão 1.1.880.0 do Azure AD Connect, a caixa de **verificação de sinal único Sem emenda** é selecionada por defeito.

5. Na página **de entrada única enable,** introduza as credenciais para uma conta de Administrador de Domínio e, em seguida, selecione **Next**.

   > [!NOTE]
   > As credenciais de conta do Administrador de Domínio são necessárias para ativar o SSO sem emenda. O processo completa as seguintes ações, que requerem estas permissões elevadas. As credenciais de conta do Administrador de Domínio não são armazenadas no Azure AD Connect ou no Azure AD. As credenciais de conta do Administrador de Domínio são utilizadas apenas para ativar a funcionalidade. As credenciais são descartadas quando o processo termina com sucesso.
   >
   > 1. Uma conta de computador chamada AZUREADSSOACC (que representa a Azure AD) é criada na sua instância de Diretório Ativo no local.
   > 2. A chave de desencriptação Kerberos da conta de computador é partilhada de forma segura com a AD Azure.
   > 3. Dois nomes principais de serviço Kerberos (SPNs) são criados para representar dois URLs que são usados durante o sign-in Azure AD.

6. Na página **Ready to configure,** certifique-se de que o Iniciar o processo de **sincronização quando a configuração completa** a caixa de verificação é selecionada. Em seguida, **selecione Configurar**.

   ![Screenshot que mostra o botão Configurar na página Ready to configurar](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)<br />
   Quando selecionar o botão **Configurar,** o SSO sem emenda é configurado como indicado no passo anterior. A configuração de sincronização de hash de palavra-passe não é modificada porque foi ativada mais cedo.

   > [!IMPORTANT]
   > Não são feitas alterações na forma como os utilizadores se inscrevem neste momento.

7. No portal Azure AD, verifique estas definições:
   * **A Federação** está definida para **ativar**.
   * O **único sinal insígnio está** definido para **ativado**.
   * **O Password Sync** está definido para **Ativado**.

   ![Screenshot que mostra as definições na secção de iniciar sessão do Utilizador](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>Converter domínios de federados para geridos

Neste momento, a federação ainda está habilitada e operacional para os seus domínios. Para continuar com a implementação, cada domínio precisa de ser convertido de federado para conseguido forçar a autenticação do utilizador através da sincronização de hash password.

> [!IMPORTANT]
> Não tens de converter todos os domínios ao mesmo tempo. Pode optar por começar com um domínio de teste no seu inquilino de produção ou começar com o seu domínio que tem o menor número de utilizadores.

Complete a conversão utilizando o módulo PowerShell Azure AD:

1. Na PowerShell, inscreva-se no Azure AD utilizando uma conta de Administrador Global.
2. Para converter o primeiro domínio, executar o seguinte comando:

   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```

3. No portal Azure AD, selecione **Azure Ative Directory** > **Azure AD Connect**.
4. Verifique se o domínio foi convertido para ser gerido executando o seguinte comando:

   ``` PowerShell
   Get-MsolDomain -DomainName <domain name>
   ```

## <a name="testing-and-next-steps"></a>Testes e próximos passos

Complete as seguintes tarefas para verificar a sincronização de hash de palavra-passe e para terminar o processo de conversão.

### <a name="test-authentication-by-using-password-hash-synchronization"></a>Autenticação de teste utilizando sincronização de hash de palavra-passe 

Quando o seu inquilino usou identidade federada, os utilizadores foram redirecionados da página de entrada da AD Azure para o seu ambiente AD FS. Agora que o inquilino está configurado para usar a sincronização de hash de palavra-passe em vez de autenticação federada, os utilizadores não são redirecionados para AD FS. Em vez disso, os utilizadores entram diretamente na página de entrada da AD Azure.

Para testar a sincronização de hash de palavra-passe:

1. Abra o Internet Explorer no modo InPrivate para que o SSO sem emenda não o inscreva automaticamente.
2. Vá à página de inscrição do[https://portal.office.com](https://portal.office.com/)Office 365 ().
3. Introduza um utilizador UPN e, em seguida, selecione **Next**. Certifique-se de que entra na UPN de um utilizador híbrido que foi sincronizado a partir da sua instância de Diretório Ativo no local, e que anteriormente usou a autenticação federada. Aparece uma página na qual introduz o nome de utilizador e a palavra-passe:

   ![Screenshot que mostra a página de entrada em que introduz um nome de utilizador](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

   ![Screenshot que mostra a página de entrada em que introduz uma senha](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

4. Depois de introduzir a palavra-passe e selecionar **O 'Signo'** é redirecionado para o portal Office 365.

   ![Screenshot que mostra o portal Office 365](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)


### <a name="test-seamless-sso"></a>Teste sem emenda SSO

1. Inscreva-se numa máquina unida ao domínio que está ligada à rede corporativa.
2. No Internet Explorer ou Chrome, vá a um dos seguintes URLs (substitua "contoso" pelo seu domínio):

   * https:\/\/myapps.microsoft.com/contoso.com
   * https:\/\/myapps.microsoft.com/contoso.onmicrosoft.com

   O utilizador é brevemente redirecionado para a página de entrada da AD Azure, que mostra a mensagem "Tentar inscrevê-lo". O utilizador não é solicitado para um nome de utilizador ou palavra-passe.<br />

   ![Screenshot que mostra a página de entrada e mensagem da AD Azure](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)<br />
3. O utilizador é redirecionado e é contratado com sucesso ao painel de acesso:

   > [!NOTE]
   > O SSO sem emenda trabalha em serviços do Office 365 que suportam a dica de domínio (por exemplo, myapps.microsoft.com/contoso.com). Atualmente, o portal Office 365 (portal.office.com) não suporta sugestões de domínio. Os utilizadores são obrigados a introduzir uma UPN. Depois de uma UPN ser introduzida, o SSO sem emenda recupera o bilhete Kerberos em nome do utilizador. O utilizador é inscrito sem introduzir uma senha.

   > [!TIP]
   > Considere implementar [híbridos Azure AD no Windows 10](https://docs.microsoft.com/azure/active-directory/device-management-introduction) para uma experiência SSO melhorada.

### <a name="remove-the-relying-party-trust"></a>Remova a confiança do partido que confia

Depois de validar que todos os utilizadores e clientes estão autenticando com sucesso via Azure AD, é seguro remover o Office 365 confiando na confiança do partido.

Se não utilizar a AD FS para outros fins (isto é, para outros fidedignos de partido), é seguro desativar a AD FS neste momento.

### <a name="rollback"></a>Recuo

Se descobrir um problema importante e não conseguir resolvê-lo rapidamente, pode optar por reverter a solução para a federação.

Consulte a documentação de design e implementação da federação para os seus detalhes específicos de implementação. O processo deve envolver estas tarefas:

* Converter domínios geridos para autenticação federada utilizando o cmdlet **Convert-MSOLDomainToFederated.**
* Se necessário, configure regras adicionais de reclamações.

### <a name="sync-userprincipalname-updates"></a>Sync userPrincipalName atualizações

Historicamente, as atualizações ao atributo **UserPrincipalName,** que utiliza o serviço de sincronização a partir do ambiente no local, são bloqueadas a menos que ambas as condições sejam verdadeiras:

* O utilizador encontra-se num domínio de identidade gerido (não federado).
* O utilizador não foi designado como licença.

Para saber como verificar ou ativar esta funcionalidade, consulte [as atualizações do Sync userPrincipalName](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features).

### <a name="troubleshooting"></a>Resolução de problemas

A sua equipa de apoio deve entender como resolver problemas de autenticação que surjam durante, ou após a mudança da federação para gerida. Use a seguinte documentação de resolução de problemas para ajudar a sua equipa de apoio a familiarizar-se com os passos comuns de resolução de problemas e ações apropriadas que podem ajudar a isolar e resolver o problema.

[Sincronização de palavra-passe do Diretório Ativo De Sshoot Azure](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)

[Troubleshoot Azure Ative Directory Seamless Single Sign-On](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sso)

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Rever a chave de desencriptação SSO Kerberos sem costura

É importante rolar frequentemente sobre a chave de desencriptação Kerberos da conta de computador AZUREADSSOACC (que representa a AD Azure). A conta de computador AZUREADSSOACC é criada na sua floresta de Diretório Ativo no local. Recomendamos vivamente que repasse a chave de desencriptação Kerberos pelo menos a cada 30 dias para alinhar com a forma como os membros do domínio Ative Directory apresentam alterações de palavra-passe. Não existe nenhum dispositivo associado ligado ao objeto de conta de computador AZUREADSSOACC, pelo que deve efetuar o capotamento manualmente.

Inicie o capotamento da chave de desencriptação SSO Kerberos no servidor no local que está a executar o Azure AD Connect.

Para mais informações, veja como rebordo a chave de [desencriptação Kerberos da conta de computador AZUREADSSOACC?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq)

## <a name="next-steps"></a>Passos seguintes

* Conheça os conceitos de [design azure AD Connect.](plan-connect-design-concepts.md)
* Escolha a [autenticação certa.](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)
* Saiba mais sobre [topoologias suportadas.](plan-connect-design-concepts.md)
