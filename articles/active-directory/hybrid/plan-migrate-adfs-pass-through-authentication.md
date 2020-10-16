---
title: 'Azure AD Connect: Migrar da federação para a PTA para a Azure AD'
description: Este artigo tem informações sobre a mudança do seu ambiente de identidade híbrida da federação para a autenticação pass-through.
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
ms.openlocfilehash: a0ee8661ca985e1882cff54d2fc2cdc5e9ad0a22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335974"
---
# <a name="migrate-from-federation-to-pass-through-authentication-for-azure-active-directory"></a>Migrar da federação para a autenticação pass-through para o Azure Ative Directory

Este artigo descreve como mover os domínios da sua organização dos Serviços da Federação de Diretórios Ativos (AD FS) para a autenticação pass-through.

> [!NOTE]
> Alterar o seu método de autenticação requer planeamento, teste e potencial inatividade. [O lançamento encenado](how-to-connect-staged-rollout.md) proporciona uma forma alternativa de testar e migrar gradualmente da federação para a autenticação em nuvem utilizando a autenticação pass-through.
> 
> Se planeia utilizar o lançamento encenado, lembre-se de desligar as funcionalidades de lançamento encenadas uma vez terminada a sua redução.  Para mais informações consulte [Migrar para autenticação em nuvem usando o lançamento encenado](how-to-connect-staged-rollout.md)


## <a name="prerequisites-for-migrating-to-pass-through-authentication"></a>Pré-requisitos para a migração para a autenticação transitante

São necessários os seguintes pré-requisitos para migrar da utilização de FS AD para a utilização da autenticação pass-through.

### <a name="update-azure-ad-connect"></a>Atualização Azure AD Connect

Para completar com sucesso os passos necessários para migrar para a autenticação pass-through, tem de ter [o Azure Ative Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) (Azure AD Connect) 1.1.819.0 ou uma versão posterior. No Azure AD Connect 1.1.819.0, a forma como a conversão de entrada é realizada altera significativamente. O tempo geral para migrar de AD FS para autenticação em nuvem nesta versão é reduzido de horas para minutos potencialmente.

> [!IMPORTANT]
> Pode ler em documentação, ferramentas e blogs desatualizados que a conversão do utilizador é necessária quando converte domínios da identidade federada para identidade gerida. *A reconversão de utilizadores* já não é necessária. A Microsoft está a trabalhar para atualizar documentação e ferramentas para refletir esta mudança.

Para atualizar o Azure AD Connect, complete os passos em [Azure AD Connect: Upgrade para a versão mais recente](./how-to-upgrade-previous-version.md).

### <a name="plan-authentication-agent-number-and-placement"></a>Número e colocação do agente de autenticação do plano

A autenticação pass-through requer a implementação de agentes leves no servidor AD Connect Azure e no computador no local que está a executar o Windows Server. Para reduzir a latência, instale os agentes o mais próximo possível dos controladores de domínio do Ative Directory.

Para a maioria dos clientes, dois ou três agentes de autenticação são suficientes para fornecer alta disponibilidade e a capacidade necessária. Um inquilino pode ter um máximo de 12 agentes registados. O primeiro agente é sempre instalado no próprio servidor Azure AD Connect. Para saber mais sobre as limitações do agente e as opções de implementação do agente, consulte [a autenticação pass-through AD AD do Azure: Limitações atuais](./how-to-connect-pta-current-limitations.md).

### <a name="plan-the-migration-method"></a>Planear o método de migração

Pode escolher entre dois métodos para migrar da gestão de identidade federada para a autenticação pass-through e um único sign-on sem emenda (SSO). O método que utiliza depende de como a sua instância AD FS foi originalmente configurada.

* **Azure AD Connect**. Se configurar originalmente o AD FS utilizando o Azure AD Connect, *tem* de alterar para autenticação de passagem utilizando o assistente Azure AD Connect.

   O Azure AD Connect executa automaticamente o **cmdlet de set-MsolDomainAuthentication** quando altera o método de entrada do utilizador. A azure AD Connect não mantém automaticamente todos os domínios federados verificados no seu inquilino AD Azure.

   > [!NOTE]
   > Atualmente, se usou originalmente o Azure AD Connect para configurar o AD FS, não pode evitar desaumar todos os domínios do seu inquilino quando muda o pedido de inscrição do utilizador para a autenticação de passagem.
‎
* **Azure AD Connect with PowerShell**. Só pode utilizar este método se não configurar originalmente o AD FS utilizando o Azure AD Connect. Para esta opção, ainda tem de alterar o método de inscrição do utilizador através do assistente Azure AD Connect. A diferença central com esta opção é que o assistente não execute automaticamente o **cmdlet set-MsolDomainAuthentication.** Com esta opção, tem controlo total sobre quais domínios são convertidos e em que ordem.

Para entender que método deve utilizar, complete os passos nas seguintes secções.

#### <a name="verify-current-user-sign-in-settings"></a>Verifique as definições de inscrição do utilizador atual

1. Inscreva-se no [portal AD Azure](https://aad.portal.azure.com/) utilizando uma conta De Administrador Global.
2. Na secção **de insuição do Utilizador,** verifique as seguintes definições:
   * **A Federação** está preparada para **ativar.**
   * **O único sinal de inscrição** sem emenda está definido para **Desativado**.
   * **A autenticação pass-through** está definida para **Desativado**.

   ![Screenshot das definições na secção de inscrição do utilizador Azure AD Connect](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image1.png)

#### <a name="verify-how-federation-was-configured"></a>Verifique como a federação foi configurada

1. No seu servidor AZure AD Connect, abra o Azure AD Connect. Selecione **Configurar**.
2. Na página **de tarefas adicionais,** selecione **Ver a configuração atual**e, em seguida, selecione **Seguinte**.<br />
 
   ![Screenshot da opção de configuração atual Ver na página de tarefas adicionais](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image2.png)<br />
3. No âmbito **de tarefas adicionais > Gerir Federação,** percorra os **Serviços da Federação de Diretórios Ativos (AD FS)**.<br />

   * Se a configuração AD FS aparecer nesta secção, pode presumir com segurança que o AD FS foi originalmente configurado utilizando o Azure AD Connect. Pode converter os seus domínios da identidade federada para identidade gerida utilizando a opção **de inscrição** do utilizador Azure AD Connect Change. Para obter mais informações sobre o processo, consulte a secção **Opção A: Configurar a autenticação pass-through utilizando o Azure AD Connect**.
   * Se o FS AD não estiver listado nas definições atuais, deve converter manualmente os seus domínios da identidade federada para identidade gerida utilizando o PowerShell. Para obter mais informações sobre este processo, consulte a secção **Opção B: Mude de federação para autenticação pass-through utilizando Azure AD Connect e PowerShell**.

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
* [Set-MsolDomainAuthentication](/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Se **o SupportsMfa** estiver definido para **True,** está a utilizar uma solução de autenticação multi-factor no local para injetar um desafio de segundo fator no fluxo de autenticação do utilizador. Esta configuração já não funciona para cenários de autenticação AZure AD. 
>
> Em vez disso, utilize o serviço baseado em nuvem de autenticação multi-factor Azure para executar a mesma função. Avalie cuidadosamente os seus requisitos de autenticação de vários fatores antes de continuar. Antes de converter os seus domínios, certifique-se de que compreende como utilizar a Autenticação Multi-Factor Azure, as implicações de licenciamento e o processo de registo do utilizador.

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
| Utiliza O FS AD para bloquear versões anteriores de clientes de autenticação.| Considere substituir os controlos AD FS que bloqueiam versões anteriores de clientes de autenticação utilizando uma combinação de [controlos](../conditional-access/concept-conditional-access-conditions.md) de acesso condicional e regras de [acesso ao cliente online de troca.](https://aka.ms/EXOCAR) |
| É necessário que os utilizadores realizem a autenticação de vários fatores contra uma solução de servidor de autenticação multi-factor no local quando os utilizadores autenticarem para AD FS.| Num domínio de identidade gerido, não é possível injetar um desafio de autenticação de vários fatores através da solução de autenticação multi-factor no local para o fluxo de autenticação. No entanto, pode utilizar o serviço de autenticação multi-factor Azure para autenticação multi-factor após a conversão do domínio.<br /><br /> Se os seus utilizadores não utilizarem atualmente a autenticação multi-factor Azure, é necessário um passo único de registo do utilizador. Deve preparar-se e comunicar o registo planeado aos seus utilizadores. |
| Atualmente utiliza políticas de controlo de acesso (regras AuthZ) em FS AD para controlar o acesso ao Microsoft 365.| Considere substituir as políticas pelas políticas equivalentes de acesso condicionado Azure AD e trocar [regras](../conditional-access/overview.md) [de acesso ao cliente online.](https://aka.ms/EXOCAR)|

### <a name="common-ad-fs-customizations"></a>Personalizações comuns do AD FS

Esta secção descreve personalizações comuns de AD FS.

#### <a name="insidecorporatenetwork-claim"></a>Alegação insideCorporateNetwork

A AD FS emite a alegação **InsideCorporateNetwork** se o utilizador que está a autenticar estiver dentro da rede corporativa. Esta alegação pode então ser transmitida à Azure AD. A alegação é utilizada para contornar a autenticação de vários fatores com base na localização da rede do utilizador. Para saber se esta funcionalidade está disponível atualmente em FS AD, consulte [IPs fidedignos para utilizadores federados](../authentication/howto-mfa-adfs.md).

A alegação **InsideCorporateNetwork** não está disponível depois de os seus domínios serem convertidos para autenticação pass-through. Pode utilizar [localizações nomeadas em Azure AD](../reports-monitoring/quickstart-configure-named-locations.md) para substituir esta funcionalidade.

Depois de configurar localizações nomeadas, deve atualizar todas as políticas de Acesso Condicional que foram configuradas para incluir ou excluir a rede **Todos os locais fidedignos** ou valores **de IPs fidedignos para** refletir as novas localizações nomeadas.

Para obter mais informações sobre a condição **de localização** no Acesso Condicional, consulte [os locais de acesso condicional do Diretório Ativo.](../conditional-access/location-condition.md)

#### <a name="hybrid-azure-ad-joined-devices"></a>Dispositivos híbridos AD AD

Quando se junta a um dispositivo ao Azure AD, pode criar regras de Acesso Condicional que impõem que os dispositivos cumpram os seus padrões de acesso para segurança e conformidade. Além disso, os utilizadores podem iniciar scontabilidade num dispositivo utilizando uma conta de trabalho organizacional ou escolar em vez de uma conta pessoal. Quando utilizar dispositivos híbridos aderidos a Ad Ad, pode juntar-se aos dispositivos de domínio do Ative Directory ao Azure AD. O seu ambiente federado pode ter sido configurado para utilizar esta funcionalidade.

Para garantir que a união híbrida continua a funcionar para quaisquer dispositivos que estejam unidos ao domínio depois de os seus domínios serem convertidos para autenticação pass-through, para clientes windows 10, tem de utilizar o Azure AD Connect para sincronizar contas de computador do Ative Directory para o Azure AD.

Para as contas de computador do Windows 8 e Windows 7, a união híbrida utiliza SSO sem costura para registar o computador em Azure AD. Não é necessário sincronizar contas de computador do Windows 8 e Windows 7, como faz para dispositivos Windows 10. No entanto, deve implementar um ficheiro workplacejoin.exe atualizado (através de um ficheiro .msi) para os clientes do Windows 8 e Windows 7 para que possam registar-se utilizando SSO sem costura. [Descarregue o ficheiro .msi](https://www.microsoft.com/download/details.aspx?id=53554).

Para obter mais informações, consulte [dispositivos híbridos Azure AD da Configure.](../devices/hybrid-azuread-join-plan.md)

#### <a name="branding"></a>Imagem corporativa

Se a sua organização [personalizou as suas páginas de sposição AD FS](/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) para exibir informações mais pertinentes para a organização, considere fazer [personalizações semelhantes à página de inscrição AD AD do Azure.](../fundamentals/customize-branding.md)

Embora existam personalizações semelhantes, algumas alterações visuais nas páginas de inscrição devem ser esperadas após a conversão. É melhor fornecer informações sobre as alterações esperadas nas suas comunicações aos utilizadores.

> [!NOTE]
> A marca de organização só está disponível se comprar a licença Premium ou Basic para O Diretório Ativo Azure ou se tiver uma licença Microsoft 365.

## <a name="plan-for-smart-lockout"></a>Plano para bloqueio inteligente

O bloqueio inteligente da AD AD protege contra ataques de senha de força bruta. O bloqueio inteligente impede que uma conta ative directory no local seja bloqueada quando a autenticação pass-through está a ser utilizada e uma política de grupo de bloqueio de conta é definida no Ative Directory.

Para mais informações, consulte [o bloqueio inteligente do Azure Ative Directory](../authentication/howto-password-smart-lockout.md).

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

Depois de implementada a autenticação pass-through e sSO sem emenda, a experiência de acesso ao Microsoft 365 e outros recursos que são autenticados através de alterações AD Azure. Os utilizadores que estão fora da rede vêem apenas a página de entrada AD Azure. Estes utilizadores não são redirecionados para a página baseada em formulários que é apresentada por servidores de procuração de aplicações web virados para o exterior.

Inclua os seguintes elementos na sua estratégia de comunicação:

* Notifique os utilizadores sobre a próxima funcionalidade e lançada utilizando:
   * E-mail e outros canais de comunicação interna.
   * Visuais, como cartazes.
   * Executivo, ao vivo ou outras comunicações.
* Determine quem irá personalizar as comunicações e quem enviará as comunicações, e quando.

## <a name="implement-your-solution"></a>Implemente a sua solução

Planeou a sua solução. Agora, pode implementá-lo. A implementação envolve os seguintes componentes:

* Preparação para SSO sem costura.
* Alterar o método de inscrição para a autenticação de passagem e permitir um SSO sem costura.

### <a name="step-1-prepare-for-seamless-sso"></a>Passo 1: Prepare-se para sSO sem costura

Para que os seus dispositivos utilizem SSO sem costura, deve adicionar um URL AD AZure às definições da zona intranet dos utilizadores utilizando uma política de grupo no Ative Directory.

Por padrão, os navegadores da Web calculam automaticamente a zona correta, seja internet ou intranet, a partir de um URL. Por exemplo, **http: \/ \/ contoso/** mapas para a zona intranet e **http: \/ \/ intranet.contoso.com** mapas para a zona da Internet (porque o URL contém um período). Os navegadores enviam bilhetes Kerberos para um ponto final em nuvem, como o URL AZure AD, apenas se adicionar explicitamente o URL à zona intranet do navegador.

Preencha os passos para [lançar](./how-to-connect-sso-quick-start.md) as alterações necessárias aos seus dispositivos.

> [!IMPORTANT]
> Fazer esta alteração não modifica a forma como os seus utilizadores insinam no Azure AD. No entanto, é importante que aplique esta configuração em todos os seus dispositivos antes de prosseguir. Os utilizadores que iniciarem seduca em dispositivos que não tenham recebido esta configuração simplesmente são obrigados a introduzir um nome de utilizador e palavra-passe para iniciar súm no AZure AD.

### <a name="step-2-change-the-sign-in-method-to-pass-through-authentication-and-enable-seamless-sso"></a>Passo 2: Alterar o método de inscrição para a autenticação de passagem e permitir sSO sem costura

Tem duas opções para alterar o método de inscrição para a autenticação de passagem e permitir um SSO sem costura.

#### <a name="option-a-configure-pass-through-authentication-by-using-azure-ad-connect"></a>Opção A: Configurar a autenticação pass-through utilizando o Azure AD Connect

Utilize este método se configurar inicialmente o seu ambiente AD FS utilizando o Azure AD Connect. Não pode utilizar este método se *não* configurar originalmente o seu ambiente AD FS utilizando o Azure AD Connect.

> [!IMPORTANT]
> Depois de completar os seguintes passos, todos os seus domínios são convertidos da identidade federada para identidade gerida. Para mais informações, [reveja o plano do método de migração.](#plan-the-migration-method)

Primeiro, altere o método de inscrição:

1. No servidor Azure AD Connect, abra o assistente Azure AD Connect.
2. Selecione **Alterar o pedido de insusição**do utilizador e, em seguida, selecione **Seguinte**. 
3. Na página **'Ligar a AD' Azure,** insira o nome de utilizador e a palavra-passe de uma conta De administrador global.
4. Na página **de insusição** do Utilizador, selecione o botão **de autenticação Pass-through,** selecione **Iniciar uma única sação**e, em seguida, selecione **Seguinte**.
5. Na página **de inscrição única,** introduza as credenciais de uma conta de Administrador de Domínio e, em seguida, selecione **Seguinte**.

   > [!NOTE]
   > As credenciais de conta do Administrador de Domínio são necessárias para permitir sSO sem costura. O processo completa as seguintes ações, que requerem estas permissões elevadas. As credenciais de conta do Administrador de Domínio não são armazenadas no Azure AD Connect ou no Azure AD. As credenciais de conta do Administrador de Domínio são utilizadas apenas para ligar a funcionalidade. As credenciais são descartadas quando o processo termina com sucesso.
   >
   > 1. Uma conta informática chamada AZUREADSSOACC (que representa a Azure AD) é criada no seu caso de Ative Directory no local.
   > 2. A chave de desencriptação Kerberos da conta do computador é partilhada de forma segura com a Azure AD.
   > 3. Dois nomes principais de serviço Kerberos (SPNs) são criados para representar dois URLs que são usados durante a entrada de Azure AD.

6. Na página **Pronto para configurar,** certifique-se de que o **processo de sincronização iniciar quando a configuração completar a** caixa de verificação é selecionado. Em seguida, selecione **Configure**.<br />

   ![Screenshot da página Ready to Configure](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image8.png)<br />
7. No portal AZure AD, selecione **Azure Ative Directory**e, em seguida, selecione **Azure AD Connect**.
8. Verifique estas definições:
   * **A Federação** está preparada para **deficientes.**
   * **O sinal único sem emenda** está definido para **Ativado**.
   * **A autenticação pass-through** está definida para **Ativado**.<br />

   ![Screenshot que mostra as definições na secção de insente do utilizador](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image9.png)<br />

Seguinte. implementar métodos de autenticação adicionais:

1. No portal Azure, vá ao **Azure Ative Directory**  >  **Azure AD Connect**e, em seguida, selecione **a autenticação Pass-through**.
2. Na página **de autenticação Pass-through,** selecione o botão **Descarregar.**
3. Na página do **agente descarregue,** selecione **Aceitar termos e baixar**.

   Os agentes de autenticação adicionais começam a descarregar. Instale o agente de autenticação secundária num servidor ligado a domínios. 

   > [!NOTE]
   > O primeiro agente é sempre instalado no próprio servidor Azure AD Connect como parte das alterações de configuração efetuadas na secção **de entrada** do Utilizador da ferramenta Azure AD Connect. Instale quaisquer agentes de autenticação adicionais num servidor separado. Recomendamos que tenha dois ou três agentes de autenticação adicionais disponíveis. 

4. Executar a instalação do agente de autenticação. Durante a instalação, deve introduzir as credenciais de uma conta De Administrador Global.

   ![Screenshot que mostra o botão de instalação que utiliza para executar o Pacote agente de autenticação de ligação AD do Microsoft Azure.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image11.png)

   ![Screenshot que mostra a página de sindes de sção da Microsoft.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image12.png)

5. Quando o agente de autenticação estiver instalado, pode voltar à página de saúde do agente de autenticação pass-through para verificar o estado dos agentes adicionais.

Passar para [testar e próximos passos](#testing-and-next-steps).

> [!IMPORTANT]
> Saltar a secção **Opção B: Mudar de federação para autenticação de passagem utilizando Azure AD Connect e PowerShell**. Os passos nessa secção não se aplicam se escolher a Opção A para alterar o método de inscrição para a autenticação de passagem e permitir sSO sem costura. 

#### <a name="option-b-switch-from-federation-to-pass-through-authentication-by-using-azure-ad-connect-and-powershell"></a>Opção B: Mudar de federação para autenticação de passagem utilizando Azure AD Connect e PowerShell

Utilize esta opção se não configurar inicialmente os seus domínios federados utilizando o Azure AD Connect.

Primeiro, ativar a autenticação pass-through:

1. No Azure AD Connect Server, abra o assistente Azure AD Connect.
2. Selecione **Alterar o pedido de insusição**do utilizador e, em seguida, selecione **Seguinte**.
3. Na página **'Ligar a AD' Azure,** insira o nome de utilizador e a palavra-passe de uma conta De administrador global.
4. Na página **de insusição do Utilizador,** selecione o botão **de autenticação Pass-through.** **Selecione Ativar um único sinal de inscrição**e, em seguida, selecione **Seguinte**.
5. Na página **de inscrição única,** introduza as credenciais de uma conta de Administrador de Domínio e, em seguida, selecione **Seguinte**.

   > [!NOTE]
   > As credenciais de conta do Administrador de Domínio são necessárias para permitir sSO sem costura. O processo completa as seguintes ações, que requerem estas permissões elevadas. As credenciais de conta do Administrador de Domínio não são armazenadas no Azure AD Connect ou no Azure AD. As credenciais de conta do Administrador de Domínio são utilizadas apenas para ligar a funcionalidade. As credenciais são descartadas quando o processo termina com sucesso.
   > 
   > 1. Uma conta informática chamada AZUREADSSOACC (que representa a Azure AD) é criada no seu caso de Ative Directory no local.
   > 2. A chave de desencriptação Kerberos da conta do computador é partilhada de forma segura com a Azure AD.
   > 3. Dois nomes principais de serviço Kerberos (SPNs) são criados para representar dois URLs que são usados durante a entrada de Azure AD.

6. Na página **Pronto para configurar,** certifique-se de que o **processo de sincronização iniciar quando a configuração completar a** caixa de verificação é selecionado. Em seguida, selecione **Configure**.<br />

   ![Screenshot que mostra a página Ready to configure e o botão Configure](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image18.png)<br />
   Os seguintes passos ocorrem quando seleciona **configurar:**

   1. O primeiro agente de autenticação pass-through está instalado.
   2. A função de passagem está ativada.
   3. O SSO sem emenda está ativado.

7. Verifique estas definições:
   * **A Federação** está preparada para **ativar.**
   * **O sinal único sem emenda** está definido para **Ativado**.
   * **A autenticação pass-through** está definida para **Ativado**.
   
   ![Screenshot que mostra as definições para verificar na secção de insuição do Utilizador.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image19.png)
8. Selecione **a autenticação pass-through** e verifique se o estado está **ativo**.<br />
   
   Se o agente de autenticação não estiver ativo, complete alguns [passos de resolução de problemas](./tshoot-connect-pass-through-authentication.md) antes de continuar com o processo de conversão do domínio no passo seguinte. Corre o risco de causar uma paragem de autenticação se converter os seus domínios antes de validar que os seus agentes de autenticação pass-through estão instalados com sucesso e que o seu estado **Ativo** no portal Azure.

Em seguida, implementar agentes de autenticação adicionais:

1. No portal Azure, vá ao **Azure Ative Directory**  >  **Azure AD Connect**e, em seguida, selecione **a autenticação Pass-through**.
2. Na página **de autenticação Pass-through,** selecione o botão **Descarregar.** 
3. Na página do **agente descarregue,** selecione **Aceitar termos e baixar**.
 
   O agente de autenticação começa a descarregar. Instale o agente de autenticação secundária num servidor ligado a domínios.

   > [!NOTE]
   > O primeiro agente é sempre instalado no próprio servidor Azure AD Connect como parte das alterações de configuração efetuadas na secção **de entrada** do Utilizador da ferramenta Azure AD Connect. Instale quaisquer agentes de autenticação adicionais num servidor separado. Recomendamos que tenha dois ou três agentes de autenticação adicionais disponíveis.
 
4. Executar a instalação do agente de autenticação. Durante a instalação, deve introduzir as credenciais de uma conta De Administrador Global.<br />

   ![Screenshot que mostra o botão de instalação na página do Pacote de Agente de autenticação de ligação AD do Microsoft Azure](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image23.png)<br />
   ![Screenshot que mostra a página de inscrição](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image24.png)<br />
5. Após a instalação do agente de autenticação, pode voltar à página de saúde do agente de autenticação pass-through para verificar o estado dos agentes adicionais.

Neste momento, a autenticação federada ainda está ativa e operacional para os seus domínios. Para continuar com a implementação, deve converter cada domínio da identidade federada para identidade gerida para que a autenticação pass-through comece a servir pedidos de autenticação para o domínio.

Não tens de converter todos os domínios ao mesmo tempo. Pode optar por começar com um domínio de teste no seu inquilino de produção ou começar com o seu domínio que tem o menor número de utilizadores.

Complete a conversão utilizando o módulo Azure AD PowerShell:

1. Na PowerShell, inscreva-se no Azure AD utilizando uma conta de Administrador Global.
2. Para converter o primeiro domínio, executar o seguinte comando:
 
   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```
 
3. No portal AZure AD, selecione **Azure Ative Directory**  >  **Azure AD Connect**.
4. Depois de converter todos os seus domínios federados, verifique estas definições:
   * **A Federação** está preparada para **deficientes.**
   * **O sinal único sem emenda** está definido para **Ativado**.
   * **A autenticação pass-through** está definida para **Ativado**.<br />

   ![Screenshot que mostra as definições na secção de insuição do Utilizador no portal AD AZure.](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image26.png)<br />

## <a name="testing-and-next-steps"></a>Testes e próximos passos

Complete as seguintes tarefas para verificar a autenticação pass-through e para terminar o processo de conversão.

### <a name="test-pass-through-authentication"></a>Teste de autenticação pass-through 

Quando o seu inquilino usou identidade federada, os utilizadores foram redirecionados da página de inscrição AZure AD para o seu ambiente AD FS. Agora que o inquilino está configurado para usar a autenticação pass-through em vez de autenticação federada, os utilizadores não são redirecionados para AD FS. Em vez disso, os utilizadores insinuam-se diretamente na página de entrada Azure AD.

Para testar a autenticação transitar:

1. Abra o Internet Explorer no modo InPrivate para que o SSO sem costura não o inscreva automaticamente.
2. Aceda à página de inscrição do Office 365 [https://portal.office.com](https://portal.office.com/) ().
3. Introduza um utilizador UPN e, em seguida, selecione **Seguinte**. Certifique-se de que inserta a UPN de um utilizador híbrido que foi sincronizado a partir do seu local de instrução ative, e que anteriormente usou a autenticação federada. Aparece uma página na qual introduz o nome de utilizador e a palavra-passe:

   ![Screenshot que mostra a página de entrada em que você insira um nome de utilizador](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image27.png)

   ![Screenshot que mostra a página de entrada em que introduz uma palavra-passe](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image28.png)

4. Depois de introduzir a palavra-passe e selecionar **Iniciar s-se em sedutar,** é redirecionado para o portal Do Office 365.

   ![Screenshot que mostra o portal office 365](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image29.png)

### <a name="test-seamless-sso"></a>Teste SSO sem costura

Para testar sSO sem costura:

1. Inscreva-se numa máquina ligada ao domínio que está ligada à rede corporativa.
2. No Internet Explorer ou Chrome, vá a um dos seguintes URLs (substitua "contoso" pelo seu domínio):

   * https: \/ \/ myapps.microsoft.com/contoso.com
   * https: \/ \/ myapps.microsoft.com/contoso.onmicrosoft.com

   O utilizador é brevemente redirecionado para a página de insusição AZure AD, que mostra a mensagem "Tentar inscrevê-lo". O utilizador não é solicitado para um nome de utilizador ou senha.<br />

   ![Screenshot que mostra a página e mensagem de ad AZure](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image30.png)<br />
3. O utilizador é redirecionado e é assinado com sucesso no painel de acesso:

   > [!NOTE]
   > O Seamless SSO funciona em serviços Microsoft 365 que suportam sugestões de domínio (por exemplo, myapps.microsoft.com/contoso.com). Atualmente, o portal Microsoft 365 (portal.office.com) não suporta dicas de domínio. Os utilizadores são obrigados a introduzir uma UPN. Após a entrada de uma UPN, o SSO sem costura recupera o bilhete Kerberos em nome do utilizador. O utilizador é assinado sem introduzir uma senha.

   > [!TIP]
   > Considere implementar a [azure AD hybrid juntar-se ao Windows 10](../devices/overview.md) para uma experiência SSO melhorada.

### <a name="remove-the-relying-party-trust"></a>Remova a confiança do partido confiando

Depois de validar que todos os utilizadores e clientes estão a autenticar com sucesso através do Azure AD, é seguro remover a confiança da parte de confiança da Microsoft 365.

Se não utilizar FS AD para outros fins (isto é, para outros fidedignos), é seguro desativar a AD FS neste momento.

### <a name="rollback"></a>Recuo

Se descobrir um problema importante e não conseguir resolvê-lo rapidamente, pode optar por reverter a solução para a federação.

Consulte a documentação de conceção e implantação da federação para os seus detalhes específicos de implantação. O processo deve envolver estas tarefas:

* Converta os domínios geridos para a autenticação federada utilizando o **cmdlet Convert-MSOLDomainToFederated.**
* Se necessário, configurar regras adicionais de reclamações.

### <a name="sync-userprincipalname-updates"></a>Sync userPrincipalName atualizações

Historicamente, as atualizações para o atributo **UserPrincipalName,** que utiliza o serviço de sincronização a partir do ambiente no local, são bloqueadas a menos que ambas as condições sejam verdadeiras:

* O utilizador encontra-se num domínio de identidade gerido (não federado).
* O utilizador não foi designado uma licença.

Para saber como verificar ou ligar esta funcionalidade, consulte [as atualizações do Sync userPrincipalName](./how-to-connect-syncservice-features.md).

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Rolar sobre a chave de desencriptação SSO Kerberos sem costura

É importante rolar frequentemente sobre a chave de desencriptação Kerberos da conta de computador AZUREADSSOACC (que representa Azure AD). A conta informática AZUREADSSOACC é criada na sua floresta ative directy. Recomendamos vivamente que rebola a chave de desencriptação Kerberos pelo menos a cada 30 dias para se alinhar com a forma como os membros do domínio do Ative Directory submetem alterações de senha. Não existe nenhum dispositivo associado ligado ao objeto da conta do computador AZUREADSSOACC, pelo que deve efetuar a capotamento manualmente.

Inicie a capotagem da chave de desencriptação SSO Kerberos sem costura no servidor no local que está a executar O AZure AD Connect.

Para mais informações, veja [como é que revoco a chave de desencriptação kerberos da conta de computador AZUREADSSOACC?](./how-to-connect-sso-faq.md)

## <a name="monitoring-and-logging"></a>Monitorização e registos

Monitorize os servidores que executam os agentes de autenticação para manter a disponibilidade da solução. Além dos contadores gerais de desempenho do servidor, os agentes de autenticação expõem objetos de desempenho que podem ajudá-lo a compreender estatísticas e erros de autenticação.

Agentes de autenticação registam operações nos registos de eventos do Windows que estão localizados em Registos de Aplicação e Serviço\Microsoft\Microsoft\AzureAdConnect\AuthenticationAgent\Admin.

Também pode ligar o registo para resolução de problemas.

Para mais informações, consulte [a autenticação pass-through do Troubleshoot Azure Ative Directory](./tshoot-connect-pass-through-authentication.md).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre os conceitos de [design Azure AD Connect](plan-connect-design-concepts.md).
* Escolha a [autenticação certa.](./choose-ad-authn.md)
* Conheça as [topologias apoiadas.](plan-connect-design-concepts.md)