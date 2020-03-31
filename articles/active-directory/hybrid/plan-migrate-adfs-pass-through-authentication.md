---
title: 'Azure AD Connect: Migrar da federação para ptA para a AD Azure'
description: Este artigo tem informações sobre como mover o seu ambiente de identidade híbrida da federação para a autenticação pass-through.
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
ms.openlocfilehash: 13a5fc216abc890c19ce3a2d75335431fe2a6799
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528647"
---
# <a name="migrate-from-federation-to-pass-through-authentication-for-azure-active-directory"></a>Migrar da federação para a autenticação pass-through para o Diretório Ativo Azure

Este artigo descreve como mover os domínios da sua organização dos Serviços da Federação de Diretórios Ativos (AD FS) para a autenticação pass-through.

> [!NOTE]
> Alterar o seu método de autenticação requer planeamento, testes e potencialmente tempo de inatividade. [O lançamento encenado](how-to-connect-staged-rollout.md) fornece uma forma alternativa de testar e migrar gradualmente da federação para a autenticação em nuvem usando a autenticação pass-through.

## <a name="prerequisites-for-migrating-to-pass-through-authentication"></a>Pré-requisitos para a migração para a autenticação pass-through

São necessários os seguintes pré-requisitos para migrar da utilização de AD FS para a autenticação pass-through.

### <a name="update-azure-ad-connect"></a>Atualizar ligação Azure AD

Para completar com sucesso os passos necessários para migrar para a autenticação pass-through, deve ter [o Azure Ative Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) (Azure AD Connect) 1.1.819.0 ou uma versão posterior. Em Azure AD Connect 1.1.819.0, a forma como a conversão de entrada é realizada muda significativamente. O tempo total para migrar de AD FS para a autenticação em nuvem nesta versão é reduzido de horas para minutos.

> [!IMPORTANT]
> Pode ler em documentação, ferramentas e blogs desatualizados que a conversão do utilizador é necessária quando converte domínios de identidade federada para identidade gerida. A conversão de *utilizadores* já não é necessária. A Microsoft está a trabalhar para atualizar documentação e ferramentas para refletir esta mudança.

Para atualizar o Azure AD Connect, complete os passos no [Azure AD Connect: Upgrade para a versão mais recente](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

### <a name="plan-authentication-agent-number-and-placement"></a>Número e colocação do agente de autenticação do plano

A autenticação pass-through requer a implementação de agentes leves no servidor Azure AD Connect e no seu computador no local que está a executar o Windows Server. Para reduzir a latência, instale os agentes o mais próximo possível dos seus controladores de domínio Ative Directory.

Para a maioria dos clientes, dois ou três agentes de autenticação são suficientes para fornecer elevada disponibilidade e capacidade necessária. Um inquilino pode ter um máximo de 12 agentes registados. O primeiro agente está sempre instalado no próprio servidor Azure AD Connect. Para conhecer as limitações do agente e as opções de implementação do agente, consulte a [autenticação pass-through da Azure AD: Limitações atuais](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-current-limitations).

### <a name="plan-the-migration-method"></a>Planeie o método de migração

Pode escolher entre dois métodos para migrar da gestão de identidade federada para a autenticação pass-through e um único sign-on sem emenda (SSO). O método que utiliza depende de como a sua instância AD FS foi originalmente configurada.

* **Azure AD Connect**. Se configurar originalmente a AD FS utilizando o Azure AD Connect, *tem* de alterar para autenticação pass-through utilizando o assistente Azure AD Connect.

   O Azure AD Connect executa automaticamente o cmdlet **set-MsolDomainAuthentication** quando altera o método de entrada do utilizador. A Azure AD Connect desfedera automaticamente todos os domínios federados verificados no seu inquilino Azure AD.

   > [!NOTE]
   > Atualmente, se usou originalmente o Azure AD Connect para configurar a AD FS, não pode evitar desfederar todos os domínios do seu inquilino quando muda o início de sessão do utilizador para a autenticação pass-through.
‎
* **Azure AD Connect com PowerShell**. Só pode utilizar este método se não configurar originalmente a AD FS utilizando o Azure AD Connect. Para esta opção, ainda deve alterar o método de entrada do utilizador através do assistente Azure AD Connect. A diferença de núcleo com esta opção é que o assistente não executa automaticamente o cmdlet **set-MsolDomainAuthentication.** Com esta opção, tem controlo total sobre quais os domínios convertidos e em que ordem.

Para entender que método deve utilizar, complete os passos nas seguintes secções.

#### <a name="verify-current-user-sign-in-settings"></a>Verifique as definições de sessão de sessão do utilizador atuais

1. Inscreva-se no [portal Azure AD](https://aad.portal.azure.com/) utilizando uma conta De Administrador Global.
2. Na secção de **sessão de sessão do Utilizador,** verifique as seguintes definições:
   * **A Federação** está definida para **ativar**.
   * O **único sinal insígnio está** definido para **desativado**.
   * **A autenticação pass-through** está definida para **desativado**.

   ![Screenshot das definições na secção de entrada de utilizador de ligação ad azure](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image1.png)

#### <a name="verify-how-federation-was-configured"></a>Verifique como a federação foi configurada

1. No seu servidor Azure AD Connect, abra o Azure AD Connect. Selecione **Configurar**.
2. Na página de **tarefas Adicionais,** selecione **Ver configuração de corrente**, e, em seguida, selecione **Next**.<br />
 
   ![Screenshot da opção de configuração atual do Ver na página de tarefas adicionais](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image2.png)<br />
3. No âmbito **de tarefas adicionais > Gerir a Federação,** percorra os Serviços da Federação de **DirectórioActivo (AD FS)**.<br />

   * Se a configuração AD FS aparecer nesta secção, pode assumir com segurança que o AD FS foi originalmente configurado utilizando o Azure AD Connect. Pode converter os seus domínios de identidade federada para identidade gerida utilizando a opção de entrada de utilizador azure AD Connect **Change.** Para mais informações sobre o processo, consulte a secção **Opção A: Configurar a autenticação pass-through utilizando o Azure AD Connect**.
   * Se o AD FS não estiver listado nas definições atuais, deve converter manualmente os seus domínios de identidade federada para a identidade gerida utilizando o PowerShell. Para mais informações sobre este processo, consulte a secção **Opção B: Mude da federação para a autenticação pass-through utilizando o Azure AD Connect e o PowerShell**.

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
> Se o **SupportsMfa** estiver definido para **True,** está a utilizar uma solução de autenticação multifactor no local para injetar um desafio de segundo fator no fluxo de autenticação do utilizador. Esta configuração já não funciona para cenários de autenticação da AD Azure. 
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

A AD FS emite a alegação **insideCorporateNetwork** se o utilizador que está autenticando estiver dentro da rede corporativa. Esta alegação pode então ser transmitida à Azure AD. A alegação é utilizada para contornar a autenticação de vários fatores com base na localização da rede do utilizador. Para saber determinar se esta funcionalidade está atualmente disponível em AD FS, consulte [IPs fidedignos para utilizadores federados](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud).

A alegação **InsideCorporateNetwork** não está disponível depois de os seus domínios serem convertidos para autenticação pass-through. Pode utilizar [localizações nomeadas em Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) para substituir esta funcionalidade.

Depois de configurar as localizações nomeadas, deve atualizar todas as políticas de Acesso Condicional que foram configuradas para incluir ou excluir a rede **Todos os locais de confiança** ou os valores de **IPs fidedignos** do MFA para refletir as novas localizações nomeadas.

Para mais informações sobre a condição de **localização** no Acesso Condicional, consulte localizações de [Acesso Condicional do Diretório Ativo](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

#### <a name="hybrid-azure-ad-joined-devices"></a>Dispositivos híbridos azure ad-join

Ao aderir a um dispositivo ao Azure AD, pode criar regras de Acesso Condicional que aplicam que os dispositivos cumprem as suas normas de acesso para segurança e conformidade. Além disso, os utilizadores podem iniciar sessão num dispositivo utilizando uma conta organizacional ou escolar em vez de uma conta pessoal. Quando utilizar dispositivos híbridos com a AD azure, pode juntar-se aos seus dispositivos de domínio Ative Directory para o Azure AD. O seu ambiente federado pode ter sido criado para usar esta funcionalidade.

Para garantir que a adesão híbrida continua a funcionar para quaisquer dispositivos que estejam ligados ao domínio depois de os seus domínios serem convertidos para autenticação pass-through, para clientes do Windows 10, deve utilizar o Azure AD Connect para sincronizar contas de computador Ative Directory para a Azure AD.

Para as contas de computador Do Windows 8 e Windows 7, a adesão híbrida utiliza sem emenda sem emenda sem emenda para registar o computador em Azure AD. Não é preciso sincronizar contas de computador do Windows 8 e do Windows 7, como faz para dispositivos Windows 10. No entanto, tem de implementar um ficheiro de workplacejoin.exe atualizado (através de um ficheiro .msi) para clientes windows 8 e Windows 7 para que possam registar-se utilizando SSO sem emenda. [Descarregue o ficheiro .msi](https://www.microsoft.com/download/details.aspx?id=53554).

Para mais informações, consulte os [dispositivos híbridos da Configure Azure AD](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup).

#### <a name="branding"></a>Imagem corporativa

Se a sua organização [personalizou as suas páginas de entrada aD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) para exibir informações mais pertinentes para a organização, considere fazer [personalizações semelhantes à página de entrada de ad-in do Azure AD](https://docs.microsoft.com/azure/active-directory/customize-branding).

Embora existam personalizações semelhantes, algumas alterações visuais nas páginas de inscrição devem ser esperadas após a conversão. Pode querer fornecer informações sobre as alterações esperadas nas suas comunicações aos utilizadores.

> [!NOTE]
> A marca da organização só está disponível se comprar a licença Premium ou Basic para o Azure Ative Directory ou se tiver uma licença do Office 365.

## <a name="plan-for-smart-lockout"></a>Plano para bloqueio inteligente

O bloqueio inteligente da AD azure protege contra ataques de senha seletiva. O bloqueio inteligente impede que uma conta de Diretório Ativo no local seja bloqueada quando a autenticação de passagem está a ser utilizada e uma política de grupo de bloqueio de conta é definida no Diretório Ativo.

Para mais informações, consulte [o bloqueio inteligente do Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout).

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

Após a autenticação pass-through e sem emenda SSO, a experiência de entrada do utilizador para aceder ao Office 365 e outros recursos que são autenticados através de alterações da AD Azure. Os utilizadores que estão fora da rede vêem apenas a página de entrada de adad Azure. Estes utilizadores não são redirecionados para a página baseada em formulários que é apresentada por servidores proxy de aplicação web virados para o exterior.

Inclua os seguintes elementos na sua estratégia de comunicação:

* Notifique os utilizadores sobre a funcionalidade que se avizinha e é lançada utilizando:
   * E-mail e outros canais de comunicação interna.
   * Visuais, como cartazes.
   * Executivo, ao vivo ou outras comunicações.
* Determine quem irá personalizar as comunicações e quem enviará as comunicações, e quando.

## <a name="implement-your-solution"></a>Implementar a sua solução

Planeou a sua solução. Agora, pode implementá-lo. A implementação envolve os seguintes componentes:

* Preparando-se para sso sem emenda.
* Alterar o método de inscrição para a autenticação pass-through e permitir o SSO sem emenda.

### <a name="step-1-prepare-for-seamless-sso"></a>Passo 1: Prepare-se para sso sem emenda

Para que os seus dispositivos utilizem SSO sem emenda, deve adicionar um URL Azure AD às definições da zona intranet dos utilizadores utilizando uma política de grupo em Diretório Ativo.

Por padrão, os navegadores da Web calculam automaticamente a zona correta, seja internet ou intranet, a partir de um URL. Por exemplo, **http:\/\/contoso/** mapas para a zona intranet e **http:\/\/intranet.contoso.com** mapas para a zona de internet (porque o URL contém um período). Os navegadores enviam bilhetes Kerberos para um ponto final na nuvem, como o URL DaA Azure, apenas se você adicionar explicitamente o URL à zona intranet do navegador.

Complete os passos para [lançar](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start) as alterações necessárias aos seus dispositivos.

> [!IMPORTANT]
> Fazer esta alteração não modifica a forma como os seus utilizadores entram no Azure AD. No entanto, é importante que aplique esta configuração a todos os seus dispositivos antes de prosseguir. Os utilizadores que iniciarem sessão em dispositivos que não tenham recebido esta configuração são simplesmente obrigados a introduzir um nome de utilizador e uma senha para iniciar sessão no Azure AD.

### <a name="step-2-change-the-sign-in-method-to-pass-through-authentication-and-enable-seamless-sso"></a>Passo 2: Alterar o método de inscrição para passar a autenticação e permitir o SSO sem emenda

Tem duas opções para alterar o método de inscrição para passar a autenticação e permitir o SSO sem emenda.

#### <a name="option-a-configure-pass-through-authentication-by-using-azure-ad-connect"></a>Opção A: Configure a autenticação pass-through utilizando o Azure AD Connect

Utilize este método se configurar inicialmente o seu ambiente AD FS utilizando o Azure AD Connect. Não pode utilizar este método se *não* configurar originalmente o seu ambiente AD FS utilizando o Azure AD Connect.

> [!IMPORTANT]
> Depois de completar os seguintes passos, todos os seus domínios são convertidos de identidade federada para identidade gerida. Para mais informações, [reveja o método de migração.](#plan-the-migration-method)

Primeiro, altere o método de inscrição:

1. No servidor Azure AD Connect, abra o assistente Azure AD Connect.
2. Selecione Alterar o **sessão do utilizador**e, em seguida, selecione **Next**. 
3. Na página **'Connect to Azure AD',** introduza o nome de utilizador e a palavra-passe de uma conta De Administrador Global.
4. Na página **de iniciar sessão** do Utilizador, selecione o botão de **autenticação Pass-through,** selecione **Ativar um único sinal**, e, em **seguida,** selecione Next .
5. Na página **de entrada única enable,** introduza as credenciais de uma conta de Administrador de Domínio e, em seguida, selecione **Next**.

   > [!NOTE]
   > As credenciais de conta do Administrador de Domínio são necessárias para ativar o SSO sem emenda. O processo completa as seguintes ações, que requerem estas permissões elevadas. As credenciais de conta do Administrador de Domínio não são armazenadas no Azure AD Connect ou no Azure AD. As credenciais de conta do Administrador de Domínio são utilizadas apenas para ativar a funcionalidade. As credenciais são descartadas quando o processo termina com sucesso.
   >
   > 1. Uma conta de computador chamada AZUREADSSOACC (que representa a Azure AD) é criada na sua instância de Diretório Ativo no local.
   > 2. A chave de desencriptação Kerberos da conta de computador é partilhada de forma segura com a AD Azure.
   > 3. Dois nomes principais de serviço Kerberos (SPNs) são criados para representar dois URLs que são usados durante o sign-in Azure AD.

6. Na página **Ready to configure,** certifique-se de que o Iniciar o processo de **sincronização quando a configuração completa** a caixa de verificação é selecionada. Em seguida, **selecione Configurar**.<br />

   ![Screenshot da página Ready to configurar](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image8.png)<br />
7. No portal Azure AD, selecione **Azure Ative Directory**, e, em seguida, selecione **Azure AD Connect**.
8. Verifique estas definições:
   * **A Federação** está preparada para **deficientes.**
   * O **único sinal insígnio está** definido para **ativado**.
   * A **autenticação pass-through** está definida para **Ativar**.<br />

   ![Screenshot que mostra as definições na secção de iniciar sessão do Utilizador](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image9.png)<br />

Seguinte. implementar métodos adicionais de autenticação:

1. No portal Azure, vá ao **Azure Ative Directory** > **Azure AD Connect**, e depois selecione a **autenticação Pass-through**.
2. Na página de **autenticação Pass-through,** selecione o botão **Descarregar.**
3. Na página do **agente Descarregamento,** selecione **Aceitar termos e baixar**.

   Agentes de autenticação adicionais começam a descarregar. Instale o agente de autenticação secundário num servidor unido ao domínio. 

   > [!NOTE]
   > O primeiro agente está sempre instalado no próprio servidor Azure AD Connect como parte das alterações de configuração efetuadas na secção de entrada do **Utilizador** da ferramenta Azure AD Connect. Instale quaisquer agentes de autenticação adicionais num servidor separado. Recomendamos que tenha disponíveis dois ou três agentes de autenticação adicionais. 

4. Executar a instalação do agente de autenticação. Durante a instalação, deve introduzir as credenciais de uma conta De Administrador Global.

   ![Screenshot que mostra o botão instalar na página pacote do agente de autenticação Do Microsoft Azure AD Connect](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image11.png)

   ![Screenshot que mostra a página de inscrição](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image12.png)

5. Quando o agente de autenticação estiver instalado, pode voltar à página de saúde do agente de autenticação pass-through para verificar o estado dos agentes adicionais.

Saltar para [testes e próximos passos.](#testing-and-next-steps)

> [!IMPORTANT]
> Ignore a secção **Opção B: Mude da federação para a autenticação pass-through utilizando o Azure AD Connect e o PowerShell**. Os passos nessa secção não se aplicam se escolher a Opção A para alterar o método de inscrição para passar a autenticação e permitir o SSO sem emenda. 

#### <a name="option-b-switch-from-federation-to-pass-through-authentication-by-using-azure-ad-connect-and-powershell"></a>Opção B: Mudar da federação para autenticação pass-through utilizando o Azure AD Connect e o PowerShell

Utilize esta opção se não configurar inicialmente os seus domínios federados utilizando o Azure AD Connect.

Primeiro, permitir a autenticação pass-through:

1. No Servidor de Ligação AD Azure, abra o assistente Azure AD Connect.
2. Selecione Alterar o **sessão do utilizador**e, em seguida, selecione **Next**.
3. Na página **'Connect to Azure AD',** introduza o nome de utilizador e a palavra-passe de uma conta De Administrador Global.
4. Na página **de sessão de sessão do Utilizador,** selecione o botão **de autenticação Pass-through.** **Selecione Ativar um único sinal e,** em seguida, selecione **Next**.
5. Na página **de entrada única enable,** introduza as credenciais de uma conta de Administrador de Domínio e, em seguida, selecione **Next**.

   > [!NOTE]
   > As credenciais de conta do Administrador de Domínio são necessárias para ativar o SSO sem emenda. O processo completa as seguintes ações, que requerem estas permissões elevadas. As credenciais de conta do Administrador de Domínio não são armazenadas no Azure AD Connect ou no Azure AD. As credenciais de conta do Administrador de Domínio são utilizadas apenas para ativar a funcionalidade. As credenciais são descartadas quando o processo termina com sucesso.
   > 
   > 1. Uma conta de computador chamada AZUREADSSOACC (que representa a Azure AD) é criada na sua instância de Diretório Ativo no local.
   > 2. A chave de desencriptação Kerberos da conta de computador é partilhada de forma segura com a AD Azure.
   > 3. Dois nomes principais de serviço Kerberos (SPNs) são criados para representar dois URLs que são usados durante o sign-in Azure AD.

6. Na página **Ready to configure,** certifique-se de que o Iniciar o processo de **sincronização quando a configuração completa** a caixa de verificação é selecionada. Em seguida, **selecione Configurar**.<br />

   ![Screenshot que mostra a página Ready to configurar e o botão Configurar](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image18.png)<br />
   Os seguintes passos ocorrem quando seleciona **Configurar:**

   1. O primeiro agente de autenticação pass-through está instalado.
   2. A funcionalidade de passagem está ativada.
   3. O SSO sem emenda está ativado.

7. Verifique estas definições:
   * **A Federação** está definida para **ativar**.
   * O **único sinal insígnio está** definido para **ativado**.
   * A **autenticação pass-through** está definida para **Ativar**.
   
   ![Screenshot que mostra as definições na secção de iniciar sessão do Utilizador](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image19.png)
8. **Selecione a autenticação pass-through** e verifique se o estado está **Ativo**.<br />
   
   Se o agente de autenticação não estiver ativo, complete alguns passos de resolução de [problemas](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication) antes de continuar com o processo de conversão de domínio no próximo passo. Arrisca-se a causar uma interrupção de autenticação se converter os seus domínios antes de validar que os seus agentes de autenticação pass-through estão instalados com sucesso e que o seu estado **Ativo** no portal Azure.

Em seguida, implementar agentes de autenticação adicionais:

1. No portal Azure, vá ao **Azure Ative Directory** > **Azure AD Connect**, e depois selecione a **autenticação Pass-through**.
2. Na página de **autenticação Pass-through,** selecione o botão **Descarregar.** 
3. Na página do **agente Descarregamento,** selecione **Aceitar termos e baixar**.
 
   O agente de autenticação começa a descarregar. Instale o agente de autenticação secundário num servidor unido ao domínio.

   > [!NOTE]
   > O primeiro agente está sempre instalado no próprio servidor Azure AD Connect como parte das alterações de configuração efetuadas na secção de entrada do **Utilizador** da ferramenta Azure AD Connect. Instale quaisquer agentes de autenticação adicionais num servidor separado. Recomendamos que tenha disponíveis dois ou três agentes de autenticação adicionais.
 
4. Executar a instalação do agente de autenticação. Durante a instalação, deve introduzir as credenciais de uma conta De Administrador Global.<br />

   ![Screenshot que mostra o botão instalar na página pacote do agente de autenticação Do Microsoft Azure AD Connect](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image23.png)<br />
   ![Screenshot que mostra a página de inscrição](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image24.png)<br />
5. Após a instalação do agente de autenticação, pode voltar à página de saúde do agente de autenticação pass-through para verificar o estado dos agentes adicionais.

Neste momento, a autenticação federada ainda está ativa e operacional para os seus domínios. Para continuar com a implementação, deve converter cada domínio de identidade federada para identidade gerida para que a autenticação pass-through comece a servir pedidos de autenticação para o domínio.

Não tens de converter todos os domínios ao mesmo tempo. Pode optar por começar com um domínio de teste no seu inquilino de produção ou começar com o seu domínio que tem o menor número de utilizadores.

Complete a conversão utilizando o módulo PowerShell Azure AD:

1. Na PowerShell, inscreva-se no Azure AD utilizando uma conta de Administrador Global.
2. Para converter o primeiro domínio, executar o seguinte comando:
 
   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```
 
3. No portal Azure AD, selecione **Azure Ative Directory** > **Azure AD Connect**.
4. Depois de converter todos os seus domínios federados, verifique estas definições:
   * **A Federação** está preparada para **deficientes.**
   * O **único sinal insígnio está** definido para **ativado**.
   * A **autenticação pass-through** está definida para **Ativar**.<br />

   ![Screenshot que mostra as definições na secção de iniciar sessão do Utilizador](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image26.png)<br />

## <a name="testing-and-next-steps"></a>Testes e próximos passos

Complete as seguintes tarefas para verificar a autenticação pass-through e para terminar o processo de conversão.

### <a name="test-pass-through-authentication"></a>Autenticação pass-through de teste 

Quando o seu inquilino usou identidade federada, os utilizadores foram redirecionados da página de entrada da AD Azure para o seu ambiente AD FS. Agora que o inquilino está configurado para usar a autenticação pass-through em vez de autenticação federada, os utilizadores não são redirecionados para AD FS. Em vez disso, os utilizadores entram diretamente na página de entrada da AD Azure.

Para testar a autenticação pass-through:

1. Abra o Internet Explorer no modo InPrivate para que o SSO sem emenda não o inscreva automaticamente.
2. Vá à página de inscrição do[https://portal.office.com](https://portal.office.com/)Office 365 ().
3. Introduza um utilizador UPN e, em seguida, selecione **Next**. Certifique-se de que entra na UPN de um utilizador híbrido que foi sincronizado a partir da sua instância de Diretório Ativo no local, e que anteriormente usou a autenticação federada. Aparece uma página na qual introduz o nome de utilizador e a palavra-passe:

   ![Screenshot que mostra a página de entrada em que introduz um nome de utilizador](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image27.png)

   ![Screenshot que mostra a página de entrada em que introduz uma senha](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image28.png)

4. Depois de introduzir a palavra-passe e selecionar **O 'Signo'** é redirecionado para o portal Office 365.

   ![Screenshot que mostra o portal Office 365](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image29.png)

### <a name="test-seamless-sso"></a>Teste sem emenda SSO

Para testar sso sem costura:

1. Inscreva-se numa máquina unida ao domínio que está ligada à rede corporativa.
2. No Internet Explorer ou Chrome, vá a um dos seguintes URLs (substitua "contoso" pelo seu domínio):

   * https:\/\/myapps.microsoft.com/contoso.com
   * https:\/\/myapps.microsoft.com/contoso.onmicrosoft.com

   O utilizador é brevemente redirecionado para a página de entrada da AD Azure, que mostra a mensagem "Tentar inscrevê-lo". O utilizador não é solicitado para um nome de utilizador ou palavra-passe.<br />

   ![Screenshot que mostra a página de entrada e mensagem da AD Azure](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image30.png)<br />
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

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Rever a chave de desencriptação SSO Kerberos sem costura

É importante rolar frequentemente sobre a chave de desencriptação Kerberos da conta de computador AZUREADSSOACC (que representa a AD Azure). A conta de computador AZUREADSSOACC é criada na sua floresta de Diretório Ativo no local. Recomendamos vivamente que repasse a chave de desencriptação Kerberos pelo menos a cada 30 dias para alinhar com a forma como os membros do domínio Ative Directory apresentam alterações de palavra-passe. Não existe nenhum dispositivo associado ligado ao objeto de conta de computador AZUREADSSOACC, pelo que deve efetuar o capotamento manualmente.

Inicie o capotamento da chave de desencriptação SSO Kerberos no servidor no local que está a executar o Azure AD Connect.

Para mais informações, veja como rebordo a chave de [desencriptação Kerberos da conta de computador AZUREADSSOACC?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq)

## <a name="monitoring-and-logging"></a>Monitorização e registos

Monitorize os servidores que executam os agentes de autenticação para manter a disponibilidade da solução. Além dos contadores gerais de desempenho do servidor, os agentes de autenticação expõem objetos de desempenho que podem ajudá-lo a compreender as estatísticas e erros de autenticação.

Agentes de autenticação iniciam operações nos registos do evento Windows que estão localizados em Registos de Aplicação e Serviço\Microsoft\AzureAdConnect\AuthenticationAgent\Admin.

Também pode ligar a exploração de madeira para resolução de problemas.

Para mais informações, consulte a [autenticação pass-through do Diretório Ativo DeSshoot Azure](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-Pass-through-authentication).

## <a name="next-steps"></a>Passos seguintes

* Conheça os conceitos de [design azure AD Connect.](plan-connect-design-concepts.md)
* Escolha a [autenticação certa.](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)
* Saiba mais sobre [topoologias suportadas.](plan-connect-design-concepts.md)
