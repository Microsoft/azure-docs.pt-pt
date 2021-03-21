---
title: 'Azure AD Connect: Migrar da federação para PHS para a Azure AD | Microsoft Docs'
description: Este artigo tem informações sobre a mudança do seu ambiente de identidade híbrida da federação para a sincronização de haxixe de palavra-passe.
services: active-directory
author: billmath
manager: daveba
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/29/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: dca888bf9e3dc75e80764949a11d95efe3514635
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861821"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-active-directory"></a>Migrar da federação para a sincronização de haxixe de palavra-passe para o Azure Ative Directory

Este artigo descreve como mover os domínios da sua organização dos Serviços da Federação de Diretórios Ativos (AD FS) para a sincronização de hash de palavra-passe.

> [!NOTE]
> Alterar o seu método de autenticação requer planeamento, teste e potencial inatividade. [O lançamento encenado](how-to-connect-staged-rollout.md) fornece uma forma alternativa de testar e migrar gradualmente da federação para a autenticação em nuvem usando a sincronização de hash de palavra-passe.
>
> Se planeia utilizar o lançamento encenado, lembre-se de desligar as funcionalidades de lançamento encenadas uma vez terminada a sua redução.  Para mais informações consulte [Migrar para autenticação em nuvem usando o lançamento encenado](how-to-connect-staged-rollout.md)


## <a name="prerequisites-for-migrating-to-password-hash-synchronization"></a>Pré-requisitos para migração para sincronização de haxixe de palavra-passe

São necessários os seguintes pré-requisitos para migrar da utilização de FS AD para a utilização da sincronização de hash de palavra-passe.


### <a name="update-azure-ad-connect"></a>Atualização Azure AD Connect

No mínimo para executar com sucesso os passos para migrar para a sincronização de hash de palavra-passe, deverá ter [a Azure AD a ligar](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0. Esta versão contém alterações significativas na forma como a conversão de entrada é realizada e reduz o tempo total para migrar da Federação para a Autenticação em Nuvem de horas para minutos.


> [!IMPORTANT]
> Pode ler em documentação, ferramentas e blogs desatualizados que a conversão do utilizador é necessária quando converte domínios da identidade federada para identidade gerida. *A reconversão de utilizadores* já não é necessária. A Microsoft está a trabalhar para atualizar documentação e ferramentas para refletir esta mudança.

Para atualizar o Azure AD Connect, complete os passos em [Azure AD Connect: Upgrade para a versão mais recente](./how-to-upgrade-previous-version.md).

### <a name="password-hash-synchronization-required-permissions"></a>Sincronização de hash de palavra-passe necessárias

Pode configurar o Azure AD Connect utilizando definições expressas ou uma instalação personalizada. Se utilizar a opção de instalação personalizada, as [permissões necessárias](./reference-connect-accounts-permissions.md) para sincronização de hash de palavra-passe podem não estar em vigor.

A conta de serviço Azure AD Connect Ative Directory Domain Services (AD DS) requer as seguintes permissões para sincronizar hashes de palavra-passe:

* Alterar o diretório de replicação
* Replicar mudanças de diretório tudo

Agora é uma boa hora para verificar se estas permissões estão em vigor para todos os domínios da floresta.

### <a name="plan-the-migration-method"></a>Planear o método de migração

Pode escolher entre dois métodos para migrar da gestão de identidade federada para a sincronização de hash de palavra-passe e um único sign-on sem emenda (SSO). O método que utiliza depende de como a sua instância AD FS foi originalmente configurada.

* **Azure AD Connect**. Se originalmente configurar o AD FS utilizando o Azure AD Connect, *tem de* alterar para sincronização de hash de palavra-passe utilizando o assistente Azure AD Connect.

   ‎Azure AD Connect automatically runs the **Set-MsolDomainAuthentication** cmdlet when you change the user sign-in method. A azure AD Connect não mantém automaticamente todos os domínios federados verificados no seu inquilino AD Azure.

   > [!NOTE]
   > Atualmente, se originalmente usou o Azure AD Connect para configurar o AD FS, não pode evitar desagravar todos os domínios do seu inquilino quando alterar o pedido de sôr-in do utilizador para sincronização de hash de palavra-passe. ‎
* **Azure AD Connect with PowerShell**. Só pode utilizar este método se não configurar originalmente o AD FS utilizando o Azure AD Connect. Para esta opção, ainda tem de alterar o método de inscrição do utilizador através do assistente Azure AD Connect. A diferença central com esta opção é que o assistente não execute automaticamente o **cmdlet set-MsolDomainAuthentication.** Com esta opção, tem controlo total sobre quais domínios são convertidos e em que ordem.

Para entender que método deve utilizar, complete os passos nas seguintes secções.

#### <a name="verify-current-user-sign-in-settings"></a>Verifique as definições de inscrição do utilizador atual

Para verificar as definições de inscrição do utilizador atual:

1. Inscreva-se no [portal AD Azure](https://aad.portal.azure.com/) utilizando uma conta De Administrador Global.
2. Na secção **de insuição do Utilizador,** verifique as seguintes definições:
   * **A Federação** está preparada para **ativar.**
   * **O único sinal de inscrição** sem emenda está definido para **Desativado**.
   * **A autenticação pass-through** está definida para **Desativado**.

   ![Screenshot das definições na secção de inscrição do utilizador Azure AD Connect](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-the-azure-ad-connect-configuration"></a>Verifique a configuração da Ligação AD AD Azure

1. No seu servidor AZure AD Connect, abra o Azure AD Connect. Selecione **Configurar**.
2. Na página **de tarefas adicionais,** selecione **Ver a configuração atual** e, em seguida, selecione **Seguinte**.<br />

   ![Screenshot da opção de configuração atual do Ver selecionada na página de tarefas adicionais](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)<br />
3. Na página **'Rever a Solução',** note o estado de **sincronização do hash password.**<br /> 

   * Se **a sincronização de hash password** estiver definida para **Desativado,** preencha os passos deste artigo para o ativar.
   * Se **a sincronização do hash da palavra-passe** estiver definida como **Ativada,** pode saltar a secção **Passo 1: Ativar a sincronização do hash da palavra-passe** neste artigo.
4. Na página **'Rever' a sua solução,** percorra os **Serviços da Federação de Diretórios Ativos (AD FS)**.<br />

   * ‎If the AD FS configuration appears in this section, you can safely assume that AD FS was originally configured by using Azure AD Connect. Pode converter os seus domínios da identidade federada para identidade gerida utilizando a opção **de inscrição** do utilizador Azure AD Connect Change. O processo é detalhado na secção **Opção A: Mudar de federação para sincronização de hash de palavra-passe utilizando o Azure AD Connect**.
   * Se o FS AD não estiver listado nas definições atuais, deve converter manualmente os seus domínios da identidade federada para identidade gerida utilizando o PowerShell. Para obter mais informações sobre este processo, consulte a secção **Opção B: Altere da federação para a sincronização de hash de palavra-passe utilizando Azure AD Connect e PowerShell**.

### <a name="document-current-federation-settings"></a>Documente as definições atuais da federação

Para encontrar as definições atuais da federação, execute o **cmdlet Get-MsdomainFederationSettings:**

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Exemplo:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

Verifique quaisquer configurações que possam ter sido personalizadas para a sua documentação de design e implantação da federação. Especificamente, procure personalizações em **PreferredAuthenticationProtocol,** **SupportsMfa,** e **PromptLoginBehavior**.

Para obter mais informações, veja estes artigos:

* [Suporte de parâmetro de ad FS=suporte a parâmetros de login](/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](/powershell/module/msonline/set-msoldomainauthentication)

> [!NOTE]
> Se **o SupportsMfa** estiver definido para **True,** está a utilizar uma solução de autenticação multi-factor no local para injetar um desafio de segundo fator no fluxo de autenticação do utilizador. Esta configuração deixou de funcionar para cenários de autenticação AD Azure depois de converter este domínio de federado para autenticação gerida. Depois de desativar a federação, corta a relação com a sua federação no local e isso inclui adaptadores de MFA no local. 
>
> Em vez disso, utilize o serviço baseado na nuvem de autenticação multi-factor Azure AD para executar a mesma função. Avalie cuidadosamente os seus requisitos de autenticação de vários fatores antes de continuar. Antes de converter os seus domínios, certifique-se de que compreende como utilizar a Autenticação Multi-Factor AD Azure, as implicações de licenciamento e o processo de registo do utilizador.

#### <a name="back-up-federation-settings"></a>Configurações de federação de apoio

Embora não sejam feitas alterações a outras partes que dependem da sua fazenda AD FS durante os processos descritos neste artigo, recomendamos que tenha uma cópia de segurança válida atual da sua quinta AD FS que pode restaurar. Pode criar uma cópia de segurança válida atual utilizando a ferramenta de restauro rápido do Microsoft [AD FS Rapid Restore.](/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool) Você pode usar a ferramenta para apoiar o AD FS, e restaurar uma fazenda existente ou criar uma nova fazenda.

Se optar por não utilizar a Ferramenta de Restauro Rápido AD FS, no mínimo, deverá exportar a Plataforma de Identidade Microsoft 365, confiando na confiança da parte e quaisquer regras de reclamação personalizadas associadas que adicionou. Pode exportar a confiança da parte em confiança e as regras de reclamação associadas utilizando o seguinte exemplo PowerShell:

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>Considerações de implantação e utilização de FS AD

Esta secção descreve considerações de implementação e detalhes sobre a utilização de FS AD.

### <a name="current-ad-fs-use"></a>Utilização atual de FS AD

Antes de converter de identidade federada para identidade gerida, olhe de perto como você usa AD FS para AZure AD, Microsoft 365, e outras aplicações (confiando em partes). Em concreto, considere os cenários descritos na tabela seguinte:

| Se | Então |
|-|-|
| Planeia continuar a utilizar O FS AD com outras aplicações (além do Azure AD e do Microsoft 365). | Depois de converter os seus domínios, utilizará tanto o AD FS como o Azure AD. Considere a experiência do utilizador. Em alguns cenários, os utilizadores podem ser obrigados a autenticar duas vezes: uma para Azure AD (onde um utilizador obtém acesso SSO a outras aplicações, como o Microsoft 365), e novamente para quaisquer aplicações que ainda estejam vinculadas a AD FS como uma confiança do partido. |
| A sua instância AD FS é fortemente personalizada e baseia-se em configurações específicas de personalização no ficheiro onload.js (por exemplo, se alterou a experiência de entrada para que os utilizadores utilizem apenas um formato **SamAccountName** para o seu nome de utilizador em vez de um Nome Principal de Utilizador (UPN), ou a sua organização marcou fortemente a experiência de entrada). O ficheiro onload.js não pode ser duplicado em Azure AD. | Antes de continuar, deve verificar se o Azure AD pode cumprir os seus requisitos de personalização atuais. Para obter mais informações e orientação, consulte as secções sobre a marca AD FS e a personalização da AD FS.|
| Utiliza O FS AD para bloquear versões anteriores de clientes de autenticação.| Considere substituir os controlos AD FS que bloqueiam versões anteriores de clientes de autenticação utilizando uma combinação de [controlos](../conditional-access/concept-conditional-access-conditions.md) de acesso condicional e regras de [acesso ao cliente online de troca.](/exchange/clients-and-mobile-in-exchange-online/client-access-rules/client-access-rules) |
| É necessário que os utilizadores realizem a autenticação de vários fatores contra uma solução de servidor de autenticação multi-factor no local quando os utilizadores autenticarem para AD FS.| Num domínio de identidade gerido, não é possível injetar um desafio de autenticação de vários fatores através da solução de autenticação multi-factor no local para o fluxo de autenticação. No entanto, pode utilizar o serviço de autenticação multi-factor Azure AD para autenticação multi-factor após a conversão do domínio.<br /><br /> Se os seus utilizadores não utilizarem atualmente a autenticação multi-factor Azure AD, é necessário um passo único de registo do utilizador. Deve preparar-se e comunicar o registo planeado aos seus utilizadores. |
| Atualmente utiliza políticas de controlo de acesso (regras AuthZ) em FS AD para controlar o acesso ao Microsoft 365.| Considere substituir as políticas pelas políticas equivalentes de acesso condicionado Azure AD e trocar [regras](../conditional-access/overview.md) [de acesso ao cliente online.](/exchange/clients-and-mobile-in-exchange-online/client-access-rules/client-access-rules)|

### <a name="common-ad-fs-customizations"></a>Personalizações comuns do AD FS

Esta secção descreve personalizações comuns de AD FS.

#### <a name="insidecorporatenetwork-claim"></a>Alegação insideCorporateNetwork

A AD FS emite a alegação **InsideCorporateNetwork** se o utilizador que está a autenticar estiver dentro da rede corporativa. Esta alegação pode então ser transmitida à Azure AD. A alegação é utilizada para contornar a autenticação de vários fatores com base na localização da rede do utilizador. Para saber se esta funcionalidade está ativada atualmente em FS AD, consulte [IPs fidedignos para utilizadores federados](../authentication/howto-mfa-adfs.md).

A alegação **InsideCorporateNetwork** não está disponível depois de os seus domínios serem convertidos para sincronização de hash de palavra-passe. Pode utilizar [localizações nomeadas em Azure AD](../reports-monitoring/quickstart-configure-named-locations.md) para substituir esta funcionalidade.

Depois de configurar localizações nomeadas, deve atualizar todas as políticas de Acesso Condicional que foram configuradas para incluir ou excluir a rede **Todos os locais fidedignos** ou valores **de IPs fidedignos para** refletir as novas localizações nomeadas.

Para obter mais informações sobre a condição **de localização** no Acesso Condicional, consulte [os locais de acesso condicional do Diretório Ativo.](../conditional-access/location-condition.md)

#### <a name="hybrid-azure-ad-joined-devices"></a>Dispositivos híbridos AD AD

Quando se junta a um dispositivo ao Azure AD, pode criar regras de Acesso Condicional que impõem que os dispositivos cumpram os seus padrões de acesso para segurança e conformidade. Além disso, os utilizadores podem iniciar scontabilidade num dispositivo utilizando uma conta de trabalho organizacional ou escolar em vez de uma conta pessoal. Quando utilizar dispositivos híbridos aderidos a Ad Ad, pode juntar-se aos dispositivos de domínio do Ative Directory ao Azure AD. O seu ambiente federado pode ter sido configurado para utilizar esta funcionalidade.

Para garantir que a união híbrida continua a funcionar para quaisquer dispositivos que estejam ligados ao domínio depois de os seus domínios serem convertidos para sincronização de hash de palavra-passe, para clientes do Windows 10, tem de utilizar as opções do dispositivo Azure AD Connect para sincronizar contas de computador ative Directy para Azure AD. 

Para as contas de computador do Windows 8 e Windows 7, a união híbrida utiliza SSO sem costura para registar o computador em Azure AD. Não é necessário sincronizar contas de computador do Windows 8 e Windows 7, como faz para dispositivos Windows 10. No entanto, deve implementar um ficheiro workplacejoin.exe atualizado (através de um ficheiro .msi) para os clientes do Windows 8 e Windows 7 para que possam registar-se utilizando SSO sem costura. [Descarregue o ficheiro .msi.](https://www.microsoft.com/download/details.aspx?id=53554)

Para obter mais informações, consulte [dispositivos híbridos Azure AD da Configure.](../devices/hybrid-azuread-join-plan.md)

#### <a name="branding"></a>Imagem corporativa

Se a sua organização [personalizou as suas páginas de sposição AD FS](/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) para exibir informações mais pertinentes para a organização, considere fazer [personalizações semelhantes à página de inscrição AD AD do Azure.](../fundamentals/customize-branding.md)

Embora existam personalizações semelhantes, algumas alterações visuais nas páginas de inscrição devem ser esperadas após a conversão. É melhor fornecer informações sobre as alterações esperadas nas suas comunicações aos utilizadores.

> [!NOTE]
> A marca de organização só está disponível se comprar a licença Premium ou Basic para O Diretório Ativo Azure ou se tiver uma licença Microsoft 365.

## <a name="plan-deployment-and-support"></a>Implantação e suporte do plano

Preencha as tarefas descritas nesta secção para ajudá-lo a planear a implantação e suporte.

### <a name="plan-the-maintenance-window"></a>Planeie a janela de manutenção

Embora o processo de conversão de domínio seja relativamente rápido, o Azure AD pode continuar a enviar alguns pedidos de autenticação para os seus servidores AD FS durante até quatro horas após a conversão do domínio estar concluída. Durante esta janela de quatro horas, e dependendo de várias caches laterais de serviço, a Azure AD pode não aceitar estas autenticações. Os utilizadores podem receber um erro. O utilizador ainda pode autenticar com sucesso contra a AD FS, mas a Azure AD já não aceita o token emitido pelo utilizador porque essa confiança da federação é agora removida.

Apenas os utilizadores que acedam aos serviços através de um navegador web durante esta janela pós-conversão antes da cache do lado do serviço ser desimpedida são afetados. Os clientes legados (Exchange ActiveSync, Outlook 2010/2013) não são esperados para serem afetados porque o Exchange Online mantém uma cache das suas credenciais por um determinado período de tempo. A cache é utilizada para reautoricamente o utilizador. O utilizador não tem de voltar ao AD FS. As credenciais armazenadas no dispositivo para estes clientes são usadas para se reautorarem silenciosamente depois deste cached ser limpo. Não se espera que os utilizadores recebam quaisquer pedidos de senha como resultado do processo de conversão de domínio. 

Os clientes de autenticação moderna (Office 2016 e Office 2013, iOS e aplicativos Android) usam um token de atualização válido para obter novos tokens de acesso para acesso continuado aos recursos em vez de voltar a FS AD. Estes clientes são imunes a quaisquer pedidos de senha resultantes do processo de conversão do domínio. Os clientes continuarão a funcionar sem configuração adicional.

> [!IMPORTANT]
> Não desligue o ambiente AD FS nem remova a confiança da parte do Microsoft 365 até verificar se todos os utilizadores podem autenticar com sucesso utilizando a autenticação na nuvem.

### <a name="plan-for-rollback"></a>Plano de reversão

Se encontrar um problema importante que não possa resolver rapidamente, pode decidir reverter a solução para a federação. É importante planear o que fazer se a sua implantação não for como pretendido. Se a conversão do domínio ou dos utilizadores falhar durante a implementação, ou se precisar de voltar para a federação, deve entender como atenuar qualquer falha e reduzir o efeito nos seus utilizadores.

#### <a name="to-roll-back"></a>Para voltar

Para planear o revés, consulte a documentação de conceção e implantação da federação para os seus detalhes específicos de implantação. O processo deve incluir estas tarefas:

* Converter domínios geridos em domínios federados utilizando o **cmdlet Converte-MSOLDomainToFederated.**
* Se necessário, configurar regras adicionais de sinistros.

### <a name="plan-communications"></a>Planear as comunicações

Uma parte importante do planeamento da implementação e do suporte é garantir que os seus utilizadores são informados proativamente sobre as próximas alterações. Os utilizadores devem saber com antecedência o que podem experimentar e o que lhes é exigido. 

Depois de implementada sincronização de hash de palavra-passe e SSO sem emenda, a experiência de entrada do utilizador para aceder ao Microsoft 365 e outros recursos que são autenticados através de alterações AD Azure. Os utilizadores que estão fora da rede vêem apenas a página de entrada AD Azure. Estes utilizadores não são redirecionados para a página baseada em formulários que é apresentada por servidores de procuração de aplicações web virados para o exterior.

Inclua os seguintes elementos na sua estratégia de comunicação:

* Notifique os utilizadores sobre a próxima funcionalidade e lançada utilizando:
   * E-mail e outros canais de comunicação interna.
   * Visuais, como cartazes.
   * Executivo, ao vivo ou outras comunicações.
* Determine quem irá personalizar as comunicações e quem enviará as comunicações, e quando.

## <a name="implement-your-solution"></a>Implemente a sua solução

Planeou a sua solução. Agora, pode implementá-lo. A implementação envolve os seguintes componentes:

* Ativando a sincronização do hash da palavra-passe.
* Preparação para SSO sem costura.
* Alterar o método de entrada para sincronização de hash de palavra-passe e permitir sSO sem costura.

### <a name="step-1-enable-password-hash-synchronization"></a>Passo 1: Ativar a sincronização do hash da palavra-passe

O primeiro passo para implementar esta solução é permitir a sincronização de hash de palavra-passe utilizando o assistente Azure AD Connect. A sincronização de hash de palavra-passe é uma funcionalidade opcional que pode permitir em ambientes que utilizam a federação. Não há nenhum efeito no fluxo de autenticação. Neste caso, o Azure AD Connect começará a sincronizar hashes de palavra-passe sem afetar os utilizadores que se inscrevam através da federação.

Por esta razão, recomendamos que complete este passo como uma tarefa de preparação muito antes de alterar o método de inscrição do seu domínio. Em seguida, terá tempo suficiente para verificar se a sincronização de hash de palavra-passe funciona corretamente.

Para ativar a sincronização do hash de palavra-passe:

1. No servidor Azure AD Connect, abra o assistente Azure AD Connect e, em seguida, selecione **Configure**.
2. **Selecione Personalizar opções de sincronização** e, em seguida, selecione **Seguinte**.
3. Na página **'Ligar a AD' Azure,** insira o nome de utilizador e a palavra-passe de uma conta De administrador global.
4. Na página **'Ligar os directórios',** selecione **Seguinte**.
5. Na página **de filtragem Do Domínio e DA,** selecione **Seguinte**.
6. Na página **de funcionalidades opcionais,** selecione **a sincronização de palavras-passe** e, em seguida, selecione **Seguinte**.
 
   ![Screenshot da opção de sincronização de palavras-passe selecionada na página de funcionalidades opcionais](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)<br />
7. Selecione **Seguinte** nas páginas restantes. Na última página, selecione **Configure**.
8. O Azure AD Connect começa a sincronizar hashes de palavra-passe na próxima sincronização.

Após a sincronização do hash da palavra-passe, a palavra-passe hashes para todos os utilizadores no âmbito de sincronização Azure AD Connect são re-ashed e escritas para Azure AD. Dependendo do número de utilizadores, esta operação pode demorar minutos ou várias horas.

Para fins de planeamento, deve estimar que aproximadamente 20.000 utilizadores são processados em 1 hora.

Para verificar se a sincronização de hash de palavra-passe funciona corretamente, complete a tarefa **de resolução de problemas** no assistente AZure AD Connect:

1. Abra uma nova sessão Windows PowerShell no seu servidor Azure AD Connect utilizando a opção Executar como Administrador.
2. Corra `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted` . .
3. Inicie o assistente Azure AD Connect.
4. Vá à página **de tarefas adicionais,** selecione **Resolução de Problemas** e, em seguida, selecione **Seguinte**.
5. Na página **Troubleshooting,** selecione **Lançamento** para iniciar o menu de resolução de problemas no PowerShell.
6. No menu principal, selecione **Sincronização de hash de palavra-passe de resolução de problemas**.
7. No submenu, selecione **A sincronização de hash password não funciona de todo**.

Para problemas de resolução de problemas, consulte [a sincronização de hash de resolução de problemas com a sincronização Azure AD Connect](./tshoot-connect-password-hash-synchronization.md).

### <a name="step-2-prepare-for-seamless-sso"></a>Passo 2: Prepare-se para sSO sem costura

Para que os seus dispositivos utilizem SSO sem costura, deve adicionar um URL AD AZure às definições da zona intranet dos utilizadores utilizando uma política de grupo no Ative Directory.

Por padrão, os navegadores da Web calculam automaticamente a zona correta, seja internet ou intranet, a partir de um URL. Por exemplo, **http: \/ \/ contoso/** mapas para a zona intranet e **http: \/ \/ intranet.contoso.com** mapas para a zona da Internet (porque o URL contém um período). Os navegadores enviam bilhetes Kerberos para um ponto final em nuvem, como o URL AZure AD, apenas se adicionar explicitamente o URL à zona intranet do navegador.

Preencha os passos para [lançar](./how-to-connect-sso-quick-start.md) as alterações necessárias aos seus dispositivos.

> [!IMPORTANT]
> Fazer esta alteração não modifica a forma como os seus utilizadores insinam no Azure AD. No entanto, é importante que aplique esta configuração em todos os seus dispositivos antes de prosseguir. Os utilizadores que iniciarem seduca em dispositivos que não tenham recebido esta configuração simplesmente são obrigados a introduzir um nome de utilizador e palavra-passe para iniciar súm no AZure AD.

### <a name="step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso"></a>Passo 3: Alterar o método de entrada para sincronização de haxixe de palavra-passe e permitir sSO sem costura

Tem duas opções para alterar o método de entrada para sincronização de hash de palavra-passe e permitir um SSO sem costura.

#### <a name="option-a-switch-from-federation-to-password-hash-synchronization-by-using-azure-ad-connect"></a>Opção A: Mudar de federação para sincronização de haxixe de palavra-passe utilizando o Azure AD Connect

Utilize este método se configurar inicialmente o seu ambiente AD FS utilizando o Azure AD Connect. Não pode utilizar este método se *não* configurar originalmente o seu ambiente AD FS utilizando o Azure AD Connect.

Primeiro, altere o método de inscrição:

1. No servidor Azure AD Connect, abra o assistente Azure AD Connect.
2. Selecione **Alterar o pedido de insusição** do utilizador e, em seguida, selecione **Seguinte**. 

   ![Screenshot da opção de inscrição do utilizador Change na página de tarefas adicionais](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)<br />
3. Na página **'Ligar a AD' Azure,** insira o nome de utilizador e a palavra-passe de uma conta De administrador global.
4. Na página **de entrada do Utilizador,** selecione o botão de **sincronização de hash password**. Certifique-se de que seleciona a caixa de verificação **de contas de utilizador não converta.** A opção é depreciada. **Selecione Ativar um único sinal de inscrição** e, em seguida, selecione **Seguinte**.

   ![Screenshot da página de inscrição única do Enable](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)<br />

   > [!NOTE]
   > Começando pela versão 1.1.880.0 do Azure AD Connect, a caixa de verificação **de registo única sem emenda** é selecionada por predefinição.

   > [!IMPORTANT]
   > Pode ignorar com segurança os avisos que indicam que a conversão do utilizador e a sincronização total do hash da palavra-passe são necessárias para converter da federação para a autenticação em nuvem. Note que estes passos já não são necessários. Se ainda vir estes avisos, certifique-se de que está a executar a versão mais recente do Azure AD Connect e que está a utilizar a versão mais recente deste guia. Para mais informações, consulte a secção [Update Azure AD Connect](#update-azure-ad-connect).

5. Na página **de inscrição única,** introduza as credenciais da conta Do Administrador de Domínio e, em seguida, selecione **Seguinte**.

   ![Screenshot da página de inscrição única ativa onde pode introduzir as credenciais de conta de Administrador de Domínio.](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)<br />

   > [!NOTE]
   > As credenciais de conta do Administrador de Domínio são necessárias para permitir sSO sem costura. O processo completa as seguintes ações, que requerem estas permissões elevadas. As credenciais de conta do Administrador de Domínio não são armazenadas no Azure AD Connect ou no Azure AD. As credenciais de conta do Administrador de Domínio são utilizadas apenas para ligar a funcionalidade. As credenciais são descartadas quando o processo termina com sucesso.
   >
   > 1. Uma conta informática chamada AZUREADSSOACC (que representa a Azure AD) é criada no seu caso de Ative Directory no local.
   > 2. A chave de desencriptação Kerberos da conta do computador é partilhada de forma segura com a Azure AD.
   > 3. Dois nomes principais de serviço Kerberos (SPNs) são criados para representar dois URLs que são usados durante a entrada de Azure AD.

6. Na página **Pronto para configurar,** certifique-se de que o **processo de sincronização iniciar quando a configuração completar a** caixa de verificação é selecionado. Em seguida, selecione **Configure**.

      ![Screenshot da página Ready to Configure](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)<br />

   > [!IMPORTANT]
   > Neste momento, todos os seus domínios federados mudarão para autenticação gerida. A sincronização de hash de palavra-passe é o novo método de autenticação.

7. No portal AZure AD, selecione **Azure Ative Directory**  >  **Azure AD Connect**.
8. Verifique estas definições:
   * **A Federação** está preparada para **deficientes.**
   * **O sinal único sem emenda** está definido para **Ativado**.
   * **O Sync da palavra-passe** está definido para **Ativado**.<br /> 

   ![Screenshot que mostra as definições na secção de insuição do Utilizador do portal AD AZure.](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)<br />

Passar para [testar e próximos passos](#testing-and-next-steps).

   > [!IMPORTANT]
   > Saltar a secção **Opção B: Mudar de federação para sincronização de hash de palavra-passe utilizando Azure AD Connect e PowerShell**. Os passos nessa secção não se aplicam se escolher a Opção A para alterar o método de entrada para sincronização de hash de palavra-passe e permitir sSO sem costura.

#### <a name="option-b-switch-from-federation-to-password-hash-synchronization-using-azure-ad-connect-and-powershell"></a>Opção B: Mudar de federação para sincronização de hash de palavra-passe utilizando Azure AD Connect e PowerShell

Utilize esta opção se não configurar inicialmente os seus domínios federados utilizando o Azure AD Connect. Durante este processo, você ativa sSO sem costura e muda os seus domínios de federado para gerido.

1. No servidor Azure AD Connect, abra o assistente Azure AD Connect.
2. Selecione **Alterar o pedido de insusição** do utilizador e, em seguida, selecione **Seguinte**.
3. Na página **'Ligar a AD' Azure,** insira o nome de utilizador e a palavra-passe para uma conta De Administrador Global.
4. Na página **de entrada do Utilizador,** selecione o botão de **sincronização de hash password.** **Selecione Ativar um único sinal de inscrição** e, em seguida, selecione **Seguinte**.

   Antes de ativar a sincronização de hash de palavra-passe: ![ Screenshot que mostra a opção Não configurar na página de entrada do Utilizador](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)<br />

   Depois de ativar a sincronização de hash de palavra-passe: ![ Screenshot que mostra novas opções na página de entrada do Utilizador](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)<br />
   
   > [!NOTE]
   > Começando pela versão 1.1.880.0 do Azure AD Connect, a caixa de verificação **de registo única sem emenda** é selecionada por predefinição.

5. Na página **de inscrição única,** introduza as credenciais para uma conta de Administrador de Domínio e, em seguida, selecione **Seguinte**.

   > [!NOTE]
   > As credenciais de conta do Administrador de Domínio são necessárias para permitir sSO sem costura. O processo completa as seguintes ações, que requerem estas permissões elevadas. As credenciais de conta do Administrador de Domínio não são armazenadas no Azure AD Connect ou no Azure AD. As credenciais de conta do Administrador de Domínio são utilizadas apenas para ligar a funcionalidade. As credenciais são descartadas quando o processo termina com sucesso.
   >
   > 1. Uma conta informática chamada AZUREADSSOACC (que representa a Azure AD) é criada no seu caso de Ative Directory no local.
   > 2. A chave de desencriptação Kerberos da conta do computador é partilhada de forma segura com a Azure AD.
   > 3. Dois nomes principais de serviço Kerberos (SPNs) são criados para representar dois URLs que são usados durante a entrada de Azure AD.

6. Na página **Pronto para configurar,** certifique-se de que o **processo de sincronização iniciar quando a configuração completar a** caixa de verificação é selecionado. Em seguida, selecione **Configure**.

   ![Screenshot que mostra o botão Configure na página Ready to configure](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)<br />
   Ao selecionar o botão **Configure,** o SSO sem costura é configurado como indicado no passo anterior. A configuração de sincronização de hash de palavra-passe não é modificada porque foi ativada anteriormente.

   > [!IMPORTANT]
   > Não são feitas alterações na forma como os utilizadores se inscrevem neste momento.

7. No portal AD AZure, verifique estas definições:
   * **A Federação** está preparada para **ativar.**
   * **O sinal único sem emenda** está definido para **Ativado**.
   * **O Sync da palavra-passe** está definido para **Ativado**.

   ![Screenshot que mostra as definições na secção de insente do utilizador](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>Converter domínios de federados para geridos

Neste momento, a federação ainda está ativada e operacional para os seus domínios. Para continuar com a implementação, cada domínio precisa de ser convertido de federado para conseguir forçar a autenticação do utilizador através da sincronização de hash de palavra-passe.

> [!IMPORTANT]
> Não tens de converter todos os domínios ao mesmo tempo. Pode optar por começar com um domínio de teste no seu inquilino de produção ou começar com o seu domínio que tem o menor número de utilizadores.

Complete a conversão utilizando o módulo Azure AD PowerShell:

1. Na PowerShell, inscreva-se no Azure AD utilizando uma conta de Administrador Global.
2. Para converter o primeiro domínio, executar o seguinte comando:

   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```

3. No portal AZure AD, selecione **Azure Ative Directory**  >  **Azure AD Connect**.
4. Verifique se o domínio foi convertido para gerido através da execução do seguinte comando:

   ``` PowerShell
   Get-MsolDomain -DomainName <domain name>
   ```

## <a name="testing-and-next-steps"></a>Testes e próximos passos

Complete as seguintes tarefas para verificar a sincronização do hash da palavra-passe e para terminar o processo de conversão.

### <a name="test-authentication-by-using-password-hash-synchronization"></a>A autenticação do teste utilizando a sincronização de haxixe de palavra-passe 

Quando o seu inquilino usou identidade federada, os utilizadores foram redirecionados da página de inscrição AZure AD para o seu ambiente AD FS. Agora que o inquilino está configurado para usar a sincronização de haxixe de palavra-passe em vez de autenticação federada, os utilizadores não são redirecionados para AD FS. Em vez disso, os utilizadores insinuam-se diretamente na página de entrada Azure AD.

Para testar a sincronização do hash da palavra-passe:

1. Abra o Internet Explorer no modo InPrivate para que o SSO sem costura não o inscreva automaticamente.
2. Aceda à página de inscrição do Office 365 [https://portal.office.com](https://portal.office.com/) ().
3. Introduza um utilizador UPN e, em seguida, selecione **Seguinte**. Certifique-se de que inserta a UPN de um utilizador híbrido que foi sincronizado a partir do seu local de instrução ative, e que anteriormente usou a autenticação federada. Aparece uma página na qual introduz o nome de utilizador e a palavra-passe:

   ![Screenshot que mostra a página de entrada em que você insira um nome de utilizador](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

   ![Screenshot que mostra a página de entrada em que introduz uma palavra-passe](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

4. Depois de introduzir a palavra-passe e selecionar **Iniciar s-se em sedutar,** é redirecionado para o portal Do Office 365.

   ![Screenshot que mostra o portal office 365](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)


### <a name="test-seamless-sso"></a>Teste SSO sem costura

1. Inscreva-se numa máquina ligada ao domínio que está ligada à rede corporativa.
2. No Internet Explorer ou Chrome, vá a um dos seguintes URLs (substitua "contoso" pelo seu domínio):

   * https: \/ \/ myapps.microsoft.com/contoso.com
   * https: \/ \/ myapps.microsoft.com/contoso.onmicrosoft.com

   O utilizador é brevemente redirecionado para a página de insusição AZure AD, que mostra a mensagem "Tentar inscrevê-lo". O utilizador não é solicitado para um nome de utilizador ou senha.<br />

   ![Screenshot que mostra a página e mensagem de ad AZure](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)<br />
3. O utilizador é redirecionado e é assinado com sucesso no painel de acesso:

   > [!NOTE]
   > O Seamless SSO funciona em serviços Microsoft 365 que suportam sugestões de domínio (por exemplo, myapps.microsoft.com/contoso.com). Atualmente, o portal Microsoft 365 (portal.office.com) não suporta dicas de domínio. Os utilizadores são obrigados a introduzir uma UPN. Após a entrada de uma UPN, o SSO sem costura recupera o bilhete Kerberos em nome do utilizador. O utilizador é assinado sem introduzir uma senha.

   > [!TIP]
   > Considere implementar a [azure AD hybrid juntar-se ao Windows 10](../devices/overview.md) para uma experiência SSO melhorada.

### <a name="remove-the-relying-party-trust"></a>Remova a confiança do partido confiando

Depois de validar que todos os utilizadores e clientes estão a autenticar com sucesso através do Azure AD, é seguro remover a confiança da parte de confiança da Microsoft 365.

Se não utilizar FS AD para outros fins (isto é, para outros fidedignos), é seguro desativar a AD FS neste momento.

### <a name="rollback"></a>Reversão

Se descobrir um problema importante e não conseguir resolvê-lo rapidamente, pode optar por reverter a solução para a federação.

Consulte a documentação de conceção e implantação da federação para os seus detalhes específicos de implantação. O processo deve envolver estas tarefas:

* Converta os domínios geridos para a autenticação federada utilizando o **cmdlet Convert-MSOLDomainToFederated.**
* Se necessário, configurar regras adicionais de reclamações.

### <a name="sync-userprincipalname-updates"></a>Sync userPrincipalName atualizações

Historicamente, as atualizações para o atributo **UserPrincipalName,** que utiliza o serviço de sincronização a partir do ambiente no local, são bloqueadas a menos que ambas as condições sejam verdadeiras:

* O utilizador encontra-se num domínio de identidade gerido (não federado).
* O utilizador não foi designado uma licença.

Para saber como verificar ou ligar esta funcionalidade, consulte [as atualizações do Sync userPrincipalName](./how-to-connect-syncservice-features.md).

### <a name="troubleshooting"></a>Resolução de problemas

A sua equipa de apoio deve entender como resolver quaisquer problemas de autenticação que surjam durante, ou após a mudança da federação para a gestão. Use a seguinte documentação de resolução de problemas para ajudar a sua equipa de apoio a familiarizar-se com as etapas comuns de resolução de problemas e ações apropriadas que podem ajudar a isolar e resolver o problema.

[Resolução de problemas Azure Ative Directory sincronização de hash](./tshoot-connect-password-hash-synchronization.md)

[Resolução de problemas Azure Ative Directory Sem Emenda Único Sinal-On](./tshoot-connect-sso.md)

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Rolar sobre a chave de desencriptação SSO Kerberos sem costura

É importante rolar frequentemente sobre a chave de desencriptação Kerberos da conta de computador AZUREADSSOACC (que representa Azure AD). A conta informática AZUREADSSOACC é criada na sua floresta ative directy. Recomendamos vivamente que rebola a chave de desencriptação Kerberos pelo menos a cada 30 dias para se alinhar com a forma como os membros do domínio do Ative Directory submetem alterações de senha. Não existe nenhum dispositivo associado ligado ao objeto da conta do computador AZUREADSSOACC, pelo que deve efetuar a capotamento manualmente.

Inicie a capotagem da chave de desencriptação SSO Kerberos sem costura no servidor no local que está a executar O AZure AD Connect.

Para mais informações, veja [como é que revoco a chave de desencriptação kerberos da conta de computador AZUREADSSOACC?](./how-to-connect-sso-faq.md)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre os conceitos de [design Azure AD Connect](plan-connect-design-concepts.md).
* Escolha a [autenticação certa.](./choose-ad-authn.md)
* Conheça as [topologias apoiadas.](plan-connect-design-concepts.md)
