---
title: 'Azure AD Connect: Migrar de Federação para a sincronização de hash de palavra-passe para o Azure AD | Documentos da Microsoft'
description: Este artigo tem informações sobre como mover o seu ambiente de identidade híbrida da Federação para a sincronização de hash de palavra-passe.
services: active-directory
author: billmath
manager: daveba
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/13/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 806d060cd58322d745ea6ebdaa59eb85c6a35cbd
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56867155"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-active-directory"></a>Migrar de Federação para a sincronização de hash de palavra-passe do Azure Active Directory

Este artigo descreve como mover seus domínios de organização de serviços de Federação do Active Directory (AD FS) para sincronização de hash de palavra-passe.

Pode [transferir este artigo](https://aka.ms/ADFSTOPHSDPDownload).

## <a name="prerequisites-for-migrating-to-password-hash-synchronization"></a>Pré-requisitos para migrar para a sincronização de hash de palavra-passe

Os seguintes pré-requisitos são necessários para migrar a partir do AD FS para utilizar a sincronização de hash de palavra-passe.

### <a name="update-azure-ad-connect"></a>Atualização do Azure AD Connect

Mínimo para realizar com êxito os passos para migrar para a sincronização de hash de palavra-passe, deverá ter [do Azure AD connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0. Esta versão contém alterações significativas na forma como o início de sessão de conversão é executada e reduz o tempo geral para migrar de Federação para autenticação em nuvem de potencialmente horas e minutos.


> [!IMPORTANT]
> Pode ler na documentação desatualizada, ferramentas e blogs que a conversão do utilizador é necessário quando converter domínios de identidades federadas para identidade gerida. *Conversão dos usuários* já não é necessário. Microsoft está trabalhando para atualizar a documentação e ferramentas para refletir esta alteração.

Para atualizar o Azure AD Connect, conclua os passos na [do Azure AD Connect: Atualizar para a versão mais recente](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

### <a name="password-hash-synchronization-required-permissions"></a>Permissões de necessária a sincronização de hash de palavra-passe

Pode configurar o Azure AD Connect utilizando as definições rápidas ou uma instalação personalizada. Se tiver utilizado a opção de instalação personalizada, o [permissões obrigatórias](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-accounts-permissions) de hash de palavra-passe a sincronização poderá não ser no local.

A conta de serviço do Azure AD Connect Active Directory Domain Services (AD DS) requer as seguintes permissões para sincronizar os hashes de palavra-passe:

* Replicar as alterações de diretório
* Diretório de replicar muda tudo

Agora é um bom momento para verificar se estas permissões são implementados para todos os domínios na floresta.

### <a name="plan-the-migration-method"></a>Planear o método de migração

Pode escolher entre dois métodos para migrar do gerenciamento de identidades federadas para sincronização de hash de palavra-passe e totalmente integrada início de sessão único (SSO). O método utilizado depende da forma como a sua instância do AD FS foi originalmente configurada.

* **Do Azure AD Connect**. Se configurou originalmente do AD FS com o Azure AD Connect, *tem* alterar para sincronização de hash de palavra-passe utilizando o Assistente do Azure AD Connect.

   O Azure AD Connect executa automaticamente o **MsolDomainAuthentication conjunto** cmdlet quando altera o método de início de sessão do utilizador. O Azure AD Connect unfederates automaticamente todos os domínios federados verificados no seu inquilino do Azure AD.

   > [!NOTE]
   > Atualmente, se utilizou originalmente do Azure AD Connect para configurar o AD FS, é possível evitar unfederating todos os domínios no seu inquilino, quando altera o utilizador inicie sessão para a sincronização de hash de palavra-passe. ‎
* **Com o PowerShell do Azure AD Connect**. Pode utilizar este método apenas se originalmente não configurar o AD FS com o Azure AD Connect. Para esta opção, ainda tem de alterar o método de início de sessão do utilizador através do Assistente do Azure AD Connect. A principal diferença com esta opção é que o assistente não é executado automaticamente a **Set-MsolDomainAuthentication** cmdlet. Com esta opção, tem controlo total sobre quais os domínios que são convertidos e em que ordem.

Para compreender qual método deve usar, conclua os passos nas secções seguintes.

#### <a name="verify-current-user-sign-in-settings"></a>Certifique-se de início de sessão configurações do usuário atual

Para verificar o seu atual início de sessão definições de utilizador:

1. Inicie sessão para o [portal do Azure AD](https://aad.portal.azure.com/) com uma conta de Administrador Global.
2. Na **sessão do utilizador** secção, verifique se as seguintes definições:
   * **Federação** está definido como **ativado**.
   * **Totalmente integrado início de sessão único** está definido como **desativado**.
   * **Autenticação pass-through** está definido como **desativado**.

   ![Captura de ecrã das definições na secção de início de sessão de utilizador ligar do Azure AD](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-the-azure-ad-connect-configuration"></a>Verificar a configuração do Azure AD Connect

1. No servidor do Azure AD Connect, abra o Azure AD Connect. Selecione **configurar**.
2. Sobre o **tarefas adicionais** página, selecione **ver configuração atual**e, em seguida, selecione **seguinte**.<br />

   ![Captura de ecrã da vista atual configuração opção selecionada na página tarefas adicionais](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)<br />
3. Sobre o **rever a sua solução** página, tenha em atenção a **sincronização de hash de palavra-passe** estado.<br /> 

   * Se **sincronização de hash de palavra-passe** está definida como **desativado**, conclua os passos neste artigo para ativá-la.
   * Se **sincronização de hash de palavra-passe** está definida como **ativado**, pode ignorar a secção **passo 1: Ativar a sincronização de hash de palavra-passe** neste artigo.
4. Sobre o **rever a sua solução** página, desloque-se para **serviços de Federação do Active Directory (AD FS)**.<br />

   * Se a configuração do AD FS for apresentada nesta seção, pode presumir com segurança do AD FS foi originalmente configurado com o Azure AD Connect. Pode converter seus domínios de identidades federadas para identidade gerida através da utilização do Azure AD Connect **alterar utilizador inicie sessão** opção. O processo é detalhado na secção **opção a: Mudar da Federação para sincronização de hash de palavra-passe, através da utilização do Azure AD Connect**.
   * Se o AD FS não está listado nas configurações atuais, tem manualmente de converter seus domínios de identidades federadas para identidade gerida com o PowerShell. Para obter mais informações sobre este processo, consulte a secção **opção b: Mudar da Federação para sincronização de hash de palavra-passe, através da utilização do Azure AD Connect e o PowerShell**.

### <a name="document-current-federation-settings"></a>Definições de Federação atuais do documento

Para localizar as definições de Federação atual, execute o **Get-MsolDomainFederationSettings** cmdlet:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Exemplo:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

Verifique se as definições que poderão foram personalizadas para a documentação de design e implementação de Federação. Mais concretamente, procure as personalizações no **PreferredAuthenticationProtocol**, **SupportsMfa**, e **PromptLoginBehavior**.

Para obter mais informações, veja estes artigos:

* [Linha de comandos do AD FS = suporte de parâmetros de início de sessão](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Se **SupportsMfa** está definida como **verdadeiro**, estiver a utilizar uma solução de autenticação multifator no local para injetar um desafio de segundo fator o fluxo de autenticação de utilizador. Esta configuração já não funciona para cenários de autenticação do Azure AD. 
>
> Em alternativa, utilize o serviço baseado na nuvem do Azure multi-factor Authentication para efetuar a mesma função. Avalie cuidadosamente os seus requisitos de autenticação multifator antes de continuar. Antes de converter os domínios, certifique-se de que compreende como utilizar o Azure multi-factor Authentication, as implicações de licenciamento e o processo de registo do utilizador.

#### <a name="back-up-federation-settings"></a>Fazer uma cópia de segurança das definições de Federação

Embora não são efetuadas alterações a outras entidades confiadoras no farm do AD FS durante os processos descritos neste artigo, recomendamos que tem uma cópia de segurança válida atual do farm do AD FS que pode restaurar a partir de. Pode criar uma cópia de segurança válida atual com o gratuito da Microsoft [AD FS rápida restaurar ferramenta](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool). Pode utilizar a ferramenta para criar cópias de segurança do AD FS e para restaurar um farm existente ou criar um novo farm.

Se optar por não utilizar o AD FS rápida restaurar ferramenta, no mínimo, deve exportar a plataforma identidade do Microsoft Office 365 da entidade confiadora fidedignidade de terceiros e quaisquer regras de afirmação personalizada associada que adicionou. Pode exportar a confiança de entidade confiadora e regras de afirmação associadas ao utilizar o seguinte exemplo do PowerShell:

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>Considerações sobre a implementação e utilização do AD FS

Esta secção descreve as considerações de implantação e os detalhes sobre como utilizar o AD FS.

### <a name="current-ad-fs-use"></a>Utilizar o atual do AD FS

Antes de converter de identidades federadas para identidade gerida, examine de perto como a utilizar atualmente o AD FS para o Azure AD, o Office 365 e outros aplicativos (confianças de entidade confiadora). Especificamente, considere os cenários descritos na tabela a seguir:

| IF | em seguida |
|-|-|
| Planeia continuar a utilizar o AD FS com outros aplicativos (que não o Azure AD e o Office 365). | Depois de converter os domínios, irá utilizar o AD FS e o Azure AD. Considere a experiência do usuário. Em alguns cenários, os utilizadores poderão ser necessárias para se autenticar duas vezes: uma vez para o Azure AD (em que um utilizador obtém acesso SSO para outros aplicativos, como o Office 365) e, novamente, para todos os aplicativos que ainda estão vinculados ao AD FS, como uma fidedignidade de entidade confiadora. |
| A instância do AD FS altamente personalizada e depende de definições de personalização específicos no ficheiro onload.js (por exemplo, se tiver alterado a experiência de início de sessão para que os utilizadores utilizam apenas um **SamAccountName** formato para o respetivo nome de utilizador em vez de um utilizador (UPN) do nome do Principal ou a sua organização tem muito com a marca a experiência de início de sessão). O ficheiro de onload.js não pode ser duplicado no Azure AD. | Antes de continuar, certifique-se de que o Azure AD pode satisfazer as suas necessidades de personalização atual. Para obter mais informações e para obter orientações, veja as secções em uma marca para AD FS e personalização do AD FS.|
| Utilizar o AD FS para bloquear versões anteriores dos clientes de autenticação.| Considerar a substituição de controles do AD FS que bloquear versões anteriores dos clientes de autenticação ao utilizar uma combinação de [controlos de acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) e [regras de acesso de cliente Online do Exchange](http://aka.ms/EXOCAR). |
| Exigir que os usuários realizem a autenticação multifator em relação a uma solução de servidor de autenticação multifator no local quando os usuários são autenticados para o AD FS.| Num domínio de identidade gerida, não pode injetar um desafio de autenticação multifator através da solução de autenticação multifator no local para o fluxo de autenticação. No entanto, pode utilizar o serviço de multi-factor Authentication para autenticação multifator, depois do domínio é convertido.<br /><br /> Se os utilizadores não utilizarem atualmente o Azure multi-factor Authentication, um passo de registo de utilizador onetime é necessário. Tem de preparar para e comunicar o registo em planeadas aos seus utilizadores. |
| Utilizar atualmente a políticas de controlo de acesso (regras de AuthZ) no AD FS para controlar o acesso ao Office 365.| Considere substituir as políticas com o Azure AD equivalente [políticas de acesso condicional](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) e [regras de acesso de cliente Online do Exchange](http://aka.ms/EXOCAR).|

### <a name="common-ad-fs-customizations"></a>Personalizações do comuns do AD FS

Esta secção descreve as personalizações de comuns do AD FS.

#### <a name="insidecorporatenetwork-claim"></a>Afirmação InsideCorporateNetwork

Problemas do AD FS a **InsideCorporateNetwork** afirmação se o utilizador que está a autenticar será dentro da rede empresarial. Esta afirmação, em seguida, pode ser passada para o Azure AD. A afirmação é utilizada para ignorar a autenticação multifator com base na localização de rede do utilizador. Para saber como determinar se essa funcionalidade atualmente está ativada no AD FS, veja [IPs fidedignos para utilizadores federados](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud).

O **InsideCorporateNetwork** afirmação não está disponível depois dos domínios são convertidos em sincronização de hash de palavra-passe. Pode usar [localizações com nome no Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) substituir esta funcionalidade.

Depois de configurar localizações com nome, tem de atualizar todas as políticas de acesso condicional que foram configuradas para incluir ou excluir da rede **todas as localizações fidedignas** ou **IPs fidedignos de MFA** valores a refletir a nova localizações com nome.

Para obter mais informações sobre o **localização** condição no acesso condicional, consulte [localizações de acesso condicional do Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

#### <a name="hybrid-azure-ad-joined-devices"></a>Híbrido do Azure AD-dispositivos associados a um

Ao aderir a um dispositivo para o Azure AD, pode criar regras de acesso condicional que impõem a que os dispositivos satisfazem as normas de acesso de segurança e conformidade. Além disso, os utilizadores podem iniciar sessão a um dispositivo ao utilizar uma conta escolar ou profissional de organizacional em vez de uma conta pessoal. Quando utiliza híbrido do Azure associados ao AD dispositivos, pode associar os dispositivos associados a um domínio do Active Directory ao Azure AD. Seu ambiente federado pode foram configurado para utilizar esta funcionalidade.

Para garantir que essa associação ao híbrido continua a funcionar para todos os dispositivos que são associados ao domínio depois que os domínios são convertidos em sincronização de hash de palavra-passe, para clientes do Windows 10, tem de utilizar o Azure AD Connect para sincronizar contas de computador do Active Directory para o Azure AD. 

Para contas de computador do Windows 8 e Windows 7, associação ao híbrido utiliza o SSO totalmente integrado para registar o computador no Azure AD. Não tem a sincronização do Windows 8 e contas de computador do Windows 7 como fazer para dispositivos Windows 10. No entanto, tem de implementar um ficheiro atualizado workplacejoin.exe (por meio de um arquivo. msi) para clientes Windows 8 e Windows 7 para que eles podem se registrar com o SSO totalmente integrado. [Transfira o ficheiro. msi](https://www.microsoft.com/download/details.aspx?id=53554).

Para obter mais informações, consulte [híbrido do Azure configurar dispositivos associados ao AD](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup).

#### <a name="branding"></a>Personalização

Se sua organização [personalizadas as páginas de início de sessão do AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) para apresentar informações que são mais pertinentes para a organização, considere fazer semelhante [personalizações para a página de início de sessão do Azure AD](https://docs.microsoft.com/azure/active-directory/customize-branding).

Embora as personalizações semelhantes estejam disponíveis, devem esperar algumas alterações visual nas páginas de início de sessão após a conversão. Pode querer fornecer informações sobre as alterações esperadas nas comunicações aos utilizadores.

> [!NOTE]
> Imagem corporativa organização está disponível apenas se adquirir a licença básico ou Premium do Azure Active Directory ou se tiver uma licença do Office 365.

## <a name="plan-deployment-and-support"></a>Planear a implementação e suporte

Conclua as tarefas descritas nesta secção para ajudar a planear a implementação e suporte.

### <a name="plan-the-maintenance-window"></a>Planear a janela de manutenção

Embora o processo de conversão de domínio é relativamente rápido, do Azure AD poderá continuar a enviar alguns pedidos de autenticação para servidores do AD FS para até quatro horas depois de concluída a conversão de domínio. Durante este período de quatro horas e, dependendo de vários caches de lado do serviço, do Azure AD não pode aceitar estes autenticações. Os usuários podem receber um erro. O utilizador ainda com êxito pode autenticar com o AD FS, mas do Azure AD já não aceita o utilizador emitiu o token, porque essa confiança de Federação foi agora removida.

Apenas os utilizadores aceder aos serviços por meio de um navegador da web durante esta janela de pós-conversão antes da cache do lado do serviço é limpo são afetados. Espera-se que os clientes herdados (Exchange ActiveSync, Outlook 2010/2013) não são afetados porque o Exchange Online mantém um cache das credenciais para um determinado período de tempo. O cache é utilizada para autenticar silenciosamente o utilizador. O utilizador não tem a devolver para o AD FS. As credenciais armazenadas no dispositivo para estes clientes são utilizadas para autenticar-se silenciosamente após isso em cache está desmarcado. Os utilizadores não espera receber quaisquer pedidos de palavra-passe como resultado do processo de conversão de domínio. 

Os clientes de autenticação moderna (Office 2016 e Office 2013, iOS e as aplicações Android) utilizam um token de atualização válido para obter novos tokens de acesso de acesso contínuo aos recursos em vez de retornar para o AD FS. Estes clientes são imunes a quaisquer pedidos de palavra-passe resultantes do processo de conversão de domínio. Os clientes continuarão a funcionar sem configuração adicional.

> [!IMPORTANT]
> Não encerre o seu ambiente do AD FS ou remova a confiança de entidade confiadora do Office 365, até ter verificado que todos os utilizadores podem autenticar com êxito utilizando a autenticação na nuvem.

### <a name="plan-for-rollback"></a>Plano de reversão

Se tiver uma questão importante que não consegue resolver rapidamente, pode optar por revertê-lo a solução de Federação. É importante planejar o que fazer se a sua implementação não distribuir conforme pretendido. Se a conversão do domínio ou os utilizadores falhar durante a implementação, ou se tiver de reverter para a Federação, tem de compreender como reduzir qualquer interrupção e reduzir o efeito sobre os seus utilizadores.

#### <a name="to-roll-back"></a>Para reverter

Para planear de reversão, consulte a documentação de design e implementação de Federação para os detalhes da sua implementação específica. O processo deve incluir estas tarefas:

* Converter domínios geridos para domínios federados, utilizando o **Convert-MSOLDomainToFederated** cmdlet.
* Se necessário, adicionais a configurar regras de afirmações.

### <a name="plan-communications"></a>Planear as comunicações

Uma parte importante do planeamento, implementação e suporte é garantir que os seus utilizadores estiverem proativamente-se informado sobre alterações futuras. Os usuários devem saber com antecedência, o que poderão deparar-se e o que é necessário um deles. 

Após a sincronização de hash de palavra-passe e o SSO totalmente integrado são implementadas, o utilizador inicie sessão ocorrer para aceder ao Office 365 e outros recursos que são autenticados através de alterações do Azure AD. Os utilizadores que estão fora da rede veem apenas página do Azure AD início de sessão. Estes utilizadores não são redirecionados para a página de baseada em formulários de mensagens em fila que é apresentada por servidores de proxy de aplicações web de face externa.

Inclua os seguintes elementos na sua estratégia de comunicação:

* Notificar os utilizadores sobre a funcionalidade de futuro e lançado com o:
   * E-mail e outros canais de comunicação interna.
   * Elementos visuais, tais como cartazes.
   * Executivos, em direto ou outras comunicações.
* Determinar quem irá personalizar as comunicações e que enviará as comunicações e quando.

## <a name="implement-your-solution"></a>Implementar a sua solução

Prevista sua solução. Agora, pode agora implementá-lo. A implementação envolve os seguintes componentes:

* Ativar a sincronização de hash de palavra-passe.
* A preparar a SSO totalmente integrado.
* Alterar o método de início de sessão de sincronização de hash de palavra-passe e ativar o SSO totalmente integrado.

### <a name="step-1-enable-password-hash-synchronization"></a>Passo 1: Ativar a sincronização de hash de palavra-passe

O primeiro passo para implementar esta solução é ativar a sincronização de hash de palavra-passe utilizando o Assistente do Azure AD Connect. Sincronização de hash de palavra-passe é uma funcionalidade opcional que pode ativar em ambientes que usam o Federação. Não há nenhum efeito sobre o fluxo de autenticação. Neste caso, o Azure AD Connect será iniciada a sincronização de hashes de palavra-passe sem afetar os utilizadores que iniciem sessão com o Federação.

Por esse motivo, recomendamos que conclua este passo como uma tarefa de preparação bem antes de alterar o método de início de sessão do seu domínio. Em seguida, terá bastante tempo para verificar se a sincronização de hash de palavra-passe funciona corretamente.

Para ativar a sincronização de hash de palavra-passe:

1. No servidor do Azure AD Connect, abra o Assistente do Azure AD Connect e, em seguida, selecione **configurar**.
2. Selecione **personalizar as opções de sincronização**e, em seguida, selecione **próxima**.
3. Sobre o **ligar para o Azure AD** página, introduza o nome de utilizador e palavra-passe de uma conta de Administrador Global.
4. Sobre o **ligar os seus diretórios** página, selecione **próxima**.
5. Sobre o **domínio e UO filtragem** página, selecione **próxima**.
6. Sobre o **funcionalidades opcionais** página, selecione **sincronização de palavra-passe**e, em seguida, selecione **seguinte**.
 
   ![Captura de ecrã da opção de sincronização de palavra-passe selecionada na página de funcionalidades opcionais](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)<br />
7. Selecione **seguinte** nas restantes páginas. Na última página, selecione **configurar**.
8. O Azure AD Connect começa a sincronização de hashes de palavra-passe na próxima sincronização.

Após a sincronização de hash de palavra-passe estiver ativada, a palavra-passe codifica para todos os utilizadores no Azure AD Connect escopo de sincronização são com novo hash e escritos para o Azure AD. Dependendo do número de utilizadores, esta operação poderá demorar minutos ou várias horas.

Para fins de planejamento, deve determinar que aproximadamente 20 000 utilizadores são processados em 1 hora.

Para verificar que a sincronização de hash de palavra-passe funciona corretamente, execute o **resolução de problemas** tarefas no Assistente do Azure AD Connect:

1. Abra uma nova sessão do Windows PowerShell no seu servidor do Azure AD Connect utilizando a execução como opção de administrador.
2. Execute `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted`.
3. Inicie o Assistente do Azure AD Connect.
4. Vá para o **tarefas adicionais** página, selecione **resolução de problemas**e, em seguida, selecione **seguinte**.
5. Sobre o **resolução de problemas** página, selecione **iniciar** para iniciar o menu de resolução de problemas no PowerShell.
6. No menu principal, selecione **resolver problemas de sincronização de hash de palavra-passe**.
7. No submenu, selecione **sincronização de hash de palavra-passe não funciona em todos os**.

Para resolução de problemas, consulte [resolver problemas de sincronização de hash de palavra-passe com o Azure AD Connect sync](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization).

### <a name="step-2-prepare-for-seamless-sso"></a>Passo 2: Preparar para SSO totalmente integrado

Para os seus dispositivos utilizar o SSO totalmente integrado, tem de adicionar um URL de AD do Azure para as definições de zona de intranet dos utilizadores com uma política de grupo no Active Directory.

Por predefinição, os navegadores da web calcular automaticamente a zona correta, internet ou intranet, a partir de uma URL. Por exemplo, **http:\/\/contoso /** mapeia para a zona da intranet e **http:\/\/intranet.contoso.com** mapeia para a zona de internet porque ( o URL contém um período). Browsers enviam pedidos de suporte de Kerberos para um ponto final da cloud, como o URL de AD do Azure, apenas se adicionar explicitamente o URL para a zona de intranet do navegador.

Conclua os passos para [distribuam](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start) as alterações necessárias para os seus dispositivos.

> [!IMPORTANT]
> Efetuar esta alteração não modifica a forma como os utilizadores iniciam sessão Azure AD. No entanto, é importante que aplicar esta configuração para todos os dispositivos antes de continuar. Os utilizadores que iniciem sessão em dispositivos que não tiver recebido esta configuração simplesmente têm de introduzir um nome de utilizador e palavra-passe para iniciar sessão Azure AD.

### <a name="step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso"></a>Passo 3: Altere o método de início de sessão para sincronização de hash de palavra-passe e ativar o SSO totalmente integrado

Tem duas opções para alterar o método de início de sessão para sincronização de hash de palavra-passe e ativar o SSO totalmente integrado.

#### <a name="option-a-switch-from-federation-to-password-hash-synchronization-by-using-azure-ad-connect"></a>Opção a: Mudar da Federação para sincronização de hash de palavra-passe, através da utilização do Azure AD Connect

Utilize este método se configurou inicialmente o ambiente do AD FS com o Azure AD Connect. Não é possível utilizar este método se *não* originalmente configurar seu ambiente do AD FS com o Azure AD Connect.

Em primeiro lugar, altere o método de início de sessão:

1. No servidor do Azure AD Connect, abra o Assistente do Azure AD Connect.
2. Selecione **alterar utilizador inicie sessão**e, em seguida, selecione **próxima**. 

   ![Captura de ecrã da alteração utilizador início de sessão opção na página tarefas adicionais](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)<br />
3. Sobre o **ligar para o Azure AD** página, introduza o nome de utilizador e palavra-passe de uma conta de Administrador Global.
4. Sobre o **sessão do utilizador** página, selecione a **botão de sincronização de hash de palavra-passe**. Certifique-se de selecionar o **não converter contas de utilizador** caixa de verificação. A opção foi preterida. Selecione **ativar o início de sessão único**e, em seguida, selecione **próxima**.

   ![Captura de ecrã da ativar início de sessão em página única](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)<br />

   > [!NOTE]
   > A partir do Azure AD Connect versão 1.1.880.0, o **totalmente integrada início de sessão único** caixa de verificação está selecionada por predefinição.

   > [!IMPORTANT]
   > Pode ignorar os avisos que indicam a conversão de utilizador e sincronização de hash de palavras-passe completa são os passos necessários para a conversão da Federação para autenticação na nuvem. Tenha em atenção que estes passos não são necessários mais. Se ainda vir estes avisos, certifique-se de que está a executar a versão mais recente do Azure AD Connect e que está a utilizar a versão mais recente deste guia. Para obter mais informações, consulte a secção [atualizar o Azure AD Connect](#update-azure-ad-connect).

5. Sobre o **ativar o início de sessão único** página, introduza as credenciais de conta de administrador de domínio e, em seguida, selecione **próxima**.

   ![Captura de ecrã da ativar início de sessão em página única](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)<br />

   > [!NOTE]
   > As credenciais da conta de administrador de domínio são necessários para ativar o SSO totalmente integrado. O processo conclui as seguintes ações, que exigem essas permissões elevadas. As credenciais da conta de administrador de domínio não são armazenadas no Azure AD Connect ou no Azure AD. As credenciais da conta de administrador de domínio são utilizadas apenas para ativar a funcionalidade. As credenciais são eliminadas quando o processo estar concluído com êxito.
   >
   > 1. É criada uma conta de computador com o nome AZUREADSSOACC (que representa o Azure AD) na sua instância do Active Directory no local.
   > 2. Chave de desencriptação do Kerberos da conta de computador é compartilhado com segurança com o Azure AD.
   > 3. Dois Kerberos principal nomes de serviço (SPNs) são criados para representar dois URLs que são utilizadas durante o início de sessão no Azure AD.

6. Sobre o **pronto para configurar** página, certifique-se de que o **iniciar o processo de sincronização quando tiver concluído a configuração** caixa de verificação está selecionada. Em seguida, selecione **configurar**.

      ![Captura de ecrã do preparado para Configurar página](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)<br />

   > [!IMPORTANT]
   > Neste momento, todos os domínios federados serão alterados para a autenticação gerida. Sincronização de hash de palavra-passe é o novo método de autenticação.

7. No portal do Azure AD, selecione **do Azure Active Directory** > **do Azure AD Connect**.
8. Verifique se estas definições:
   * **Federação** está definido como **desativado**.
   * **Totalmente integrado início de sessão único** está definido como **ativado**.
   * **Sincronização de palavra-passe** está definido como **ativado**.<br /> 

   ![Captura de ecrã que mostra as definições na secção de início de sessão de utilizador](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)<br />

Avance para o [etapas de testes e seguintes](#testing-and-next-steps).

   > [!IMPORTANT]
   > Ignorar a secção **opção b: Mudar da Federação para sincronização de hash de palavra-passe, através da utilização do Azure AD Connect e o PowerShell**. Os passos nesta secção não se aplicam se tiver escolhido a opção A alterar o método de início de sessão de sincronização de hash de palavra-passe e ativar o SSO totalmente integrado.

#### <a name="option-b-switch-from-federation-to-password-hash-synchronization-using-azure-ad-connect-and-powershell"></a>Opção b: Mudar da Federação para sincronização de hash de palavra-passe com o Azure AD Connect e o PowerShell

Utilize esta opção se não configurar inicialmente os domínios federados com o Azure AD Connect. Durante este processo, habilitar o SSO totalmente integrado e um comutador seus domínios de federado para o managed.

1. No servidor do Azure AD Connect, abra o Assistente do Azure AD Connect.
2. Selecione **alterar utilizador inicie sessão**e, em seguida, selecione **próxima**.
3. Sobre o **ligar para o Azure AD** página, introduza o nome de utilizador e palavra-passe para uma conta de Administrador Global.
4. Sobre o **sessão do utilizador** página, selecione a **sincronização de hash de palavra-passe** botão. Selecione **ativar o início de sessão único**e, em seguida, selecione **próxima**.

   Antes de ativar a sincronização de hash de palavra-passe: ![Captura de ecrã que mostra a faça não configurar opção na página de início de sessão do utilizador](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)<br />

   Depois de ativar a sincronização de hash de palavra-passe: ![Captura de ecrã que mostra as novas opções na página de início de sessão do utilizador](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)<br />
   
   > [!NOTE]
   > A partir do Azure AD Connect versão 1.1.880.0, o **totalmente integrada início de sessão único** caixa de verificação está selecionada por predefinição.

5. Sobre o **ativar o início de sessão único** página, introduza as credenciais de uma conta de administrador de domínio e, em seguida, selecione **próxima**.

   > [!NOTE]
   > As credenciais da conta de administrador de domínio são necessários para ativar o SSO totalmente integrado. O processo conclui as seguintes ações, que exigem essas permissões elevadas. As credenciais da conta de administrador de domínio não são armazenadas no Azure AD Connect ou no Azure AD. As credenciais da conta de administrador de domínio são utilizadas apenas para ativar a funcionalidade. As credenciais são eliminadas quando o processo estar concluído com êxito.
   >
   > 1. É criada uma conta de computador com o nome AZUREADSSOACC (que representa o Azure AD) na sua instância do Active Directory no local.
   > 2. Chave de desencriptação do Kerberos da conta de computador é compartilhado com segurança com o Azure AD.
   > 3. Dois Kerberos principal nomes de serviço (SPNs) são criados para representar dois URLs que são utilizadas durante o início de sessão no Azure AD.

6. Sobre o **pronto para configurar** página, certifique-se de que o **iniciar o processo de sincronização quando tiver concluído a configuração** caixa de verificação está selecionada. Em seguida, selecione **configurar**.

   ![Captura de ecrã que mostra o botão Configurar no preparado para Configurar página](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)<br />
   Quando seleciona a **configurar** botão, o SSO totalmente integrado está configurada conforme indicado no passo anterior. Configuração de sincronização de hash de palavra-passe não é modificada porque foi ativada anteriormente.

   > [!IMPORTANT]
   > Não são efetuadas alterações à forma como os utilizadores iniciam sessão neste momento.

7. No portal do Azure AD, verifique se estas definições:
   * **Federação** está definido como **ativado**.
   * **Totalmente integrado início de sessão único** está definido como **ativado**.
   * **Sincronização de palavra-passe** está definido como **ativado**.

   ![Captura de ecrã que mostra as definições na secção de início de sessão de utilizador](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>Converter domínios a partir de federado para gerido

Neste momento, Federação ainda está ativado e operacional dos seus domínios. Para continuar com a implementação, cada domínio tem de ser convertidos de federado para o managed para forçar autenticação do utilizador através da sincronização de hash de palavra-passe.

> [!IMPORTANT]
> Não precisa converter todos os domínios ao mesmo tempo. Pode optar por começar com um domínio de teste no seu inquilino de produção ou começar com o domínio que tenha o menor número de utilizadores.

Conclua a conversão, utilizando o módulo Azure AD PowerShell:

1. No PowerShell, inicie sessão para o Azure AD com uma conta de Administrador Global.
2. Para converter o primeiro domínio, execute o seguinte comando:

   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```

3. No portal do Azure AD, selecione **do Azure Active Directory** > **do Azure AD Connect**.
4. Certifique-se de que o domínio foi convertido em geridos ao executar o seguinte comando:

   ``` PowerShell
   Get-MsolDomain -DomainName <domain name>
   ```

## <a name="testing-and-next-steps"></a>Passos de testes e seguintes

Conclua as seguintes tarefas para verificar a sincronização de hash de palavra-passe e para concluir o processo de conversão.

### <a name="test-authentication-by-using-password-hash-synchronization"></a>Testar a autenticação utilizando a sincronização de hash de palavra-passe 

Quando o seu inquilino utilizado identidade federada, os utilizadores foram redirecionados da página de início de sessão do Azure AD para o seu ambiente do AD FS. Agora que o inquilino está configurado para utilizar a sincronização de hash de palavra-passe em vez de autenticação federada, os utilizadores não são redirecionados para o AD FS. Em vez disso, os utilizadores iniciam sessão diretamente na página de início de sessão do Azure AD.

Para testar a sincronização de hash de palavra-passe:

1. Abra o Internet Explorer no modo InPrivate para que o SSO totalmente integrado não iniciar sessão automaticamente.
2. Aceda à página de início de sessão do Office 365 ([http://portal.office.com](http://portal.office.com/)).
3. Introduza um UPN de utilizador e, em seguida, selecione **seguinte**. Certifique-se de que introduz o UPN do utilizador híbrido que foi sincronizado a partir da sua instância do Active Directory no local e que tenha utilizado autenticação federada. É apresentada uma página em que introduza o nome de utilizador e palavra-passe:

   ![Captura de ecrã que mostra a página de início de sessão na qual introduz um nome de utilizador](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

   ![Captura de ecrã que mostra a página de início de sessão na qual introduz uma palavra-passe](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

4. Depois de introduzir a palavra-passe e selecione **iniciar sessão**, está redirecionado para o portal do Office 365.

   ![Captura de ecrã que mostra o portal do Office 365](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)


### <a name="test-seamless-sso"></a>SSO totalmente integrado de teste

1. Inicie sessão para um computador associado a um domínio que está ligado à rede empresarial.
2. No Internet Explorer ou o Chrome, aceda a um dos seguintes URLs (substitua "contoso" com o seu domínio):

   * https:\/\/myapps.microsoft.com/contoso.com
   * https:\/\/myapps.microsoft.com/contoso.onmicrosoft.com

   O utilizador é redirecionado brevemente a página do Azure AD início de sessão, que mostra a mensagem "A tentar iniciar sessão." Não é pedido ao utilizador para um nome de utilizador ou palavra-passe.<br />

   ![Captura de ecrã que mostra a página de início de sessão no Azure AD e a mensagem](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)<br />
3. O utilizador é redirecionado e tiver sessão iniciado com êxito painel de acesso:

   > [!NOTE]
   > SSO totalmente integrado funciona em serviços do Office 365 que suportam a sugestão de domínio (por exemplo, myapps.microsoft.com/contoso.com). Atualmente, o portal do Office 365 (portal.office.com) não suporta sugestões de domínio. Os utilizadores terão de introduzir um UPN. Depois que um UPN é inserido, o SSO totalmente integrado obtém a permissão de Kerberos em nome do utilizador. O utilizador tem sessão iniciado sem introduzir uma palavra-passe.

   > [!TIP]
   > Considere implementar [híbridos do Azure AD associar-se no Windows 10](https://docs.microsoft.com/azure/active-directory/device-management-introduction) para uma melhor experiência SSO.

### <a name="remove-the-relying-party-trust"></a>Remover a confiança de entidade confiadora

Depois de validar que todos os utilizadores e os clientes são autenticar com êxito através do Azure AD, é seguro remover a confiança de entidade confiadora do Office 365.

Se não utilizar o AD FS para outros fins (ou seja, para outras confianças de entidades confiadoras), é seguro desativar o AD FS neste momento.

### <a name="rollback"></a>Reversão

Se detetar um grande problema e não é possível resolvê-lo rapidamente, pode optar por revertê-lo a solução de Federação.

Consulte a documentação de design e implementação de Federação para os detalhes da sua implementação específica. O processo deve envolver estas tarefas:

* Converter domínios geridos para a autenticação federada, utilizando o **Convert-MSOLDomainToFederated** cmdlet.
* Se for necessário, configure regras de afirmações adicionais.

### <a name="sync-userprincipalname-updates"></a>Atualizações de userPrincipalName de sincronização

Historicamente, as atualizações para o **UserPrincipalName** atributo, que utiliza o serviço de sincronização a partir do ambiente no local, são bloqueados, a menos que ambas estas condições forem verdadeiras:

* O utilizador está num domínio identidade gerida (não federadas).
* O utilizador não tenha sido atribuído uma licença.

Para saber como verificar ou ativar esta funcionalidade, veja [sincronizar as atualizações de userPrincipalName](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features).

### <a name="troubleshooting"></a>Resolução de problemas

A equipa de suporte deve compreender como resolver quaisquer problemas de autenticação que podem surgir durante ou após a alteração da Federação para gerido. Utilize a seguinte documentação de resolução de problemas para ajudar a sua equipa de suporte se familiarizar com os passos de resolução de problemas comuns e as ações adequadas que podem ajudar a isolar e resolver o problema.

[Resolver problemas de sincronização de hash de palavra-passe do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)

[Resolver problemas relacionados com o Azure Active Directory totalmente integrada início de sessão único](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sso)

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Sobre a chave de desencriptação do Kerberos do SSO totalmente integrada

É importante frequentemente paire sobre a chave de desencriptação do Kerberos da conta de computador AZUREADSSOACC (que representa o Azure AD). A conta de computador AZUREADSSOACC é criada na floresta do Active Directory no local. Recomendamos vivamente que efetue o rollover da chave de desencriptação do Kerberos, pelo menos, a cada 30 dias para se alinhar com a forma que os membros do domínio do Active Directory enviar alterações de palavra-passe. Não existe nenhum dispositivo associado anexado ao objeto de conta de computador AZUREADSSOACC, pelo que tem de efetuar o rollover manualmente.

Inicie o rollover da chave de desencriptação de Kerberos do SSO totalmente integrado no servidor no local que está a executar o Azure AD Connect.

Para obter mais informações, consulte [como eu agrego sobre a chave de desencriptação do Kerberos da conta de computador AZUREADSSOACC?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq).

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [conceitos de design do Azure AD Connect](plan-connect-design-concepts.md).
* Escolha o [certa autenticação](https://docs.microsoft.com/azure/security/azure-ad-choose-authn).
* Saiba mais sobre [suportadas topologias](plan-connect-design-concepts.md).
