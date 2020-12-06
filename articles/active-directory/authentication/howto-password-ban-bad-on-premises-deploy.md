---
title: Implementar no local Azure AD Password Protection
description: Saiba como planear e implementar a Proteção de PasswordS AZure AD num ambiente de Serviços de Domínio de Diretório Ativo no local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: deb1f74902fe28d53a5180e4f341547f339a83ac
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741990"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>Planear e implementar no local Azure Ative Directory Password Protection

Os utilizadores muitas vezes criam senhas que usam palavras locais comuns, como uma escola, uma equipa desportiva ou uma pessoa famosa. Estas palavras-passe são fáceis de adivinhar e fracas contra ataques baseados em dicionários. Para impor senhas fortes na sua organização, a Azure Ative Directory (Azure AD) Password Protection fornece uma lista de palavras-passe proibidas global e personalizada. Um pedido de alteração de palavra-passe falha se houver uma correspondência nesta lista de senhas proibidas.

Para proteger o ambiente de Serviços de Domínio de Diretório Ativo (AD DS) no local, pode instalar e configurar a Proteção de Passwords AD Ad Azure para trabalhar com o seu DC on-prem. Este artigo mostra-lhe como instalar e registar o serviço de procuração de senha azure AD e o agente Azure AD Password Protection DC no seu ambiente no local.

Para obter mais informações sobre como a Proteção de Passwords AD AZure funciona num ambiente no local, consulte como impor a proteção de [senha azure AD para o Windows Server Ative Directory](concept-password-ban-bad-on-premises.md).

## <a name="deployment-strategy"></a>Estratégia de implantação

O seguinte diagrama mostra como os componentes básicos da Proteção de Senha AD AZure funcionam em conjunto num ambiente de Diretório Ativo no local:

![Como os componentes de proteção de senha AD Azure funcionam em conjunto](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

É uma boa ideia rever como o software funciona antes de o implementar. Para mais informações, consulte [a visão geral conceptual da Proteção de PasswordS AD Azure.](concept-password-ban-bad-on-premises.md)

Recomendamos que inicie as implementações no modo *de auditoria.* O modo de auditoria é a definição inicial predefinida, onde as palavras-passe podem continuar a ser definidas. As palavras-passe que seriam bloqueadas são registadas no registo do evento. Depois de implementar os servidores proxy e agentes DC em modo de auditoria, monitorize o impacto que a política de palavra-passe terá nos utilizadores quando a política for executada.

Durante a fase de auditoria, muitas organizações consideram que as seguintes situações se aplicam:

* Precisam de melhorar os processos operacionais existentes para utilizarem senhas mais seguras.
* Os utilizadores usam frequentemente senhas inseguras.
* Precisam de informar os utilizadores sobre a próxima alteração na aplicação da segurança, possível impacto nos mesmos e como escolher senhas mais seguras.

Também é possível que uma validação mais forte da palavra-passe afete a automatização de implementação do controlador de domínio ative existente. Recomendamos que pelo menos uma promoção de DC e uma despromoção de DC ocorram durante a avaliação do período de auditoria para ajudar a desvendar tais problemas. Para obter mais informações, veja os seguintes artigos:

* [Ntdsutil.exe é incapaz de definir uma senha de modo de reparação de serviços de diretório fraco](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [A promoção da réplica do controlador de domínio falha devido a uma senha fraca do Modo de Reparação de Serviços de Diretório](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [A despromoção do controlador de domínio falha devido a uma senha de administrador local fraca](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

Depois de a funcionalidade estar a funcionar em modo de auditoria durante um período razoável, pode mudar a configuração de *Auditoria* para *Enforce* para exigir senhas mais seguras. Um acompanhamento adicional durante este tempo é uma boa ideia.

É importante notar que o Azure AD Password Protection só pode validar palavras-passe durante a alteração de senha ou as operações definidas. As palavras-passe que foram aceites e armazenadas no Ative Directory antes da implementação da Azure AD Password Protection nunca serão validadas e continuarão a funcionar como está. Com o tempo, todos os utilizadores e contas irão eventualmente começar a utilizar senhas validadas pela Azure AD Password Protection, uma vez que as suas palavras-passe existentes expiram normalmente. As contas configuradas com "password nunca expira" estão isentas desta situação.

### <a name="multiple-forest-considerations"></a>Múltiplas considerações florestais

Não existem requisitos adicionais para implantar a Azure AD Password Protection em várias florestas.

Cada floresta está configurada de forma independente, conforme descrito na secção seguinte para [implantar a proteção de senha azure Ad on-prem.](#download-required-software) Cada proxy de proteção de senha AD Azure só pode suportar controladores de domínio da floresta a que se juntou.

O software Azure AD Password Protection em qualquer floresta desconhece o software de proteção de senhas que está implantado noutras florestas, independentemente das configurações de confiança do Ative Directory.

### <a name="read-only-domain-controller-considerations"></a>Considerações do controlador de domínio apenas para leitura

A alteração de palavra-passe ou os eventos definidos não são processados e persistidos em controladores de domínio apenas de leitura (RODCs). Em vez disso, são encaminhados para controladores de domínio. Não é preciso instalar o software de proteção de passwords Azure AD em RODCs.

Além disso, não é suportado para executar o serviço de procuração de senha Azure AD num controlador de domínio apenas de leitura.

### <a name="high-availability-considerations"></a>Considerações de elevada disponibilidade

A principal preocupação para a proteção de passwords é a disponibilidade de servidores de proteção de senha azure AD quando os DCs numa floresta tentam descarregar novas políticas ou outros dados do Azure. Cada agente Azure AD Password Protection DC usa um algoritmo simples ao decidir qual servidor proxy para chamar. O agente ignora servidores proxy que não respondem.

Para a maioria das implementações de Ative Directory totalmente conectadas que tenham replicação saudável do estado de pasta de diretório e sysvol, dois servidores de proteção de senha azure AD é suficiente para garantir a disponibilidade. Esta configuração resulta em descarregamento oportuno de novas políticas e outros dados. Pode implementar servidores adicionais de proteção de senhas Azure AD, se desejar.

O design do software de proteção de senha azure AD Password DC atenua os problemas habituais que estão associados à alta disponibilidade. O agente Azure AD Password Protection DC mantém uma cache local da mais recente política de senha descarregada. Mesmo que todos os servidores de procuração registados fiquem indisponíveis, os agentes da Azure AD Password Protection DC continuam a impor a sua política de senha em cache.

Uma frequência de atualização razoável para as políticas de palavra-passe numa grande implementação é geralmente dias, não horas ou menos. Assim, breves interrupções dos servidores proxy não afetam significativamente a Proteção de Passwords AD Azure.

## <a name="deployment-requirements"></a>Requisitos de implementação

Para obter informações sobre o licenciamento, consulte [os requisitos de licenciamento de proteção de senha azure AD](concept-password-ban-bad.md#license-requirements).

Aplicam-se os seguintes requisitos fundamentais:

* Todas as máquinas, incluindo controladores de domínio, que tenham componentes de proteção de senha AD AD Azure instalados devem ter o Tempo de Execução Universal C instalado.
    * Pode obter o tempo de funcionação certificando-se de que tem todas as atualizações a partir do Windows Update. Ou pode obtê-lo num pacote de atualização específico do OS. Para obter mais informações, consulte [Update for Universal C Runtime in Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* Precisa de uma conta que tenha privilégios de administrador de domínio do Ative Directory no domínio das raízes da floresta para registar a floresta de Diretório Ativo do Windows Server com Azure AD.
* O Serviço de Distribuição de Chaves deve ser ativado em todos os controladores de domínio do domínio que executam o Windows Server 2012. Por predefinição, este serviço é ativado através do arranque manual do gatilho.
* A conectividade da rede deve existir entre pelo menos um controlador de domínio em cada domínio e pelo menos um servidor que hospeda o serviço de procuração para a Proteção de Passwords AD Azure. Esta conectividade deve permitir ao controlador de domínio aceder à porta do mapa 135 do ponto final do RPC e à porta do servidor RPC no serviço de procuração.
    * Por predefinição, a porta do servidor RPC é uma porta RPC dinâmica, mas pode ser configurada para [utilizar uma porta estática](#static).
* Todas as máquinas onde será instalado o serviço Azure AD Password Protection Proxy devem ter acesso à rede aos seguintes pontos finais:

    |**Ponto final**|**Objetivo**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Pedidos de autenticação|
    |`https://enterpriseregistration.windows.net`|Funcionalidade de Proteção de Passwords Azure AD|

### <a name="azure-ad-password-protection-dc-agent"></a>Agente DC de Proteção de Passwords Azure AD

Os seguintes requisitos aplicam-se ao agente Azure AD Password Protection DC:

* Todas as máquinas onde o software de proteção de passwords AD AD Azure será instalado devem executar o Windows Server 2012 ou mais tarde.
    * O domínio ou floresta do Ative Directory não precisa de estar no nível funcional do domínio do Windows Server 2012 (DFL) ou no nível funcional da floresta (FFL). Como mencionado nos [Princípios de Design,](concept-password-ban-bad-on-premises.md#design-principles)não há dfl mínimo ou FFL necessário para que o agente DC ou software proxy seja executado.
* Todas as máquinas que executam o agente Azure AD Password Protection DC devem ter .NET 4.5 instaladas.
* Qualquer domínio de Diretório Ativo que executa o serviço de agente de proteção de passwords Azure AD deve utilizar a replicação do sistema de ficheiros distribuído (DFSR) para replicação sysvol.
   * Se o seu domínio já não estiver a utilizar o DFSR, tem de migrar antes de instalar a Proteção de PasswordS AD Azure. Para mais informações, consulte [o Guia de Migração da Replicação SYSVOL: FRS à replicação do DFS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

    > [!WARNING]
    > O software de agente Azure AD Password Protection DC irá atualmente instalar-se em controladores de domínio em domínios que ainda utilizam FRS (a tecnologia antecessora para DFSR) para replicação de sysvol, mas o software NÃO funcionará corretamente neste ambiente.
    >
    > Os efeitos colaterais negativos adicionais incluem ficheiros individuais que não conseguem replicar-se, e os procedimentos de restauro sysvol parecem ter sucesso, mas silenciosamente não conseguem replicar todos os ficheiros.
    >
    > Migrar o seu domínio para utilizar o DFSR o mais rapidamente possível, tanto para os benefícios inerentes da DFSR como para desbloquear a implementação da Proteção de Password AD AD Azure. Futuras versões do software serão automaticamente desativadas quando estiverem a funcionar num domínio que ainda está a utilizar o FRS.

### <a name="azure-ad-password-protection-proxy-service"></a>Serviço de procuração de proteção de senha Azure AD

Os seguintes requisitos aplicam-se ao serviço de procuração de senhas Azure AD:

* Todas as máquinas onde o serviço de proteção de senhas AD AZure será instalado devem executar o Windows Server 2012 R2 ou mais tarde.

    > [!NOTE]
    > A implementação do serviço de proteção de senhas Azure AD é um requisito obrigatório para a implementação da Proteção de Password AD Azure, mesmo que o controlador de domínio possa ter conectividade direta de internet de saída.

* Todas as máquinas onde será instalado o serviço de proteção de senhas Azure AD devem ter .NET 4.7 instalado.
    * .NET 4.7 já deve ser instalado num Servidor Windows totalmente atualizado. Se necessário, faça o download e executar o instalador encontrado no [instalador offline .NET Framework 4.7 para o Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* Todas as máquinas que acolhem o serviço de procuração de proteção de senhas Azure AD devem ser configuradas para conceder aos controladores de domínio a capacidade de iniciar sessão no serviço de procuração. Esta capacidade é controlada através da atribuição de privilégios "Aceder a este computador a partir da rede".
* Todas as máquinas que acolhem o serviço de procuração de proteção de senhas Azure AD devem ser configuradas para permitir o tráfego de saída TLS 1.2 HTTP.
* Uma conta *de administrador* global ou administrador *de segurança* para registar o serviço de procuração de senha azure AD e floresta com Azure AD.
* O acesso à rede deve ser ativado para o conjunto de portas e URLs especificados nos [procedimentos de configuração do ambiente Proxy de aplicação](../manage-apps/application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>Microsoft Azure AD Connect Agent Updater pré-requisitos

O serviço Microsoft Azure AD Connect Agent Updater está instalado lado a lado com o serviço Azure AD Password Protection Proxy. É necessária uma configuração adicional para que o serviço microsoft Azure AD Connect Agent Updater possa funcionar:

* Se o seu ambiente utilizar um servidor de procuração HTTP, siga as diretrizes especificadas no [Trabalho com servidores proxy existentes no local](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md).
* O serviço Microsoft Azure AD Connect Agent Updater também requer os passos TLS 1.2 especificados nos [requisitos TLS](../manage-apps/application-proxy-add-on-premises-application.md#tls-requirements).

> [!WARNING]
> Azure AD Password Protection proxy e Azure AD Application Proxy instalam diferentes versões do serviço Microsoft Azure AD Connect Agent Updater, razão pela qual as instruções se referem ao conteúdo de Procuração de Aplicação. Estas diferentes versões são incompatíveis quando instaladas lado a lado e isso impedirá o serviço de Atualização de Agentes de contactar o Azure para atualizações de software, pelo que nunca deverá instalar o Proxy de Proteção de Passwords AZure AD na mesma máquina.

## <a name="download-required-software"></a>Descarregue o software necessário

Existem dois instaladores necessários para uma implementação de Proteção de Senha Ad Ad no local:

* Agente Azure AD Password Protection DC *(AzureADPasswordProtectionDCAgentSetup.msi*)
* Azure AD Password Protection proxy *(AzureADPasswordProtectionProxySetup.exe*)

Descarregue ambos os instaladores do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="install-and-configure-the-proxy-service"></a>Instale e configuure o serviço de procuração

O serviço de procuração de proteção de senha azure AD está normalmente num servidor de membros no ambiente AD DS no local. Uma vez instalado, o serviço de procuração de senha Azure AD comunica com a Azure AD para manter uma cópia das listas de senhas banidas global e cliente para o seu inquilino AZURE AD.

Na secção seguinte, instale os agentes Azure AD Password Protection DC em controladores de domínio no ambiente AD DS no local. Estes agentes dc comunicam com o serviço de procuração para obter as mais recentes listas de senhas proibidas para utilização quando processam eventos de mudança de palavra-passe dentro do domínio.

Escolha um ou mais servidores para hospedar o serviço de procuração de senha azure AD. Aplicam-se as seguintes considerações para o(s) servidor(s):

* Cada um desses serviços só pode fornecer políticas de senha para uma única floresta. A máquina hospedeira deve ser unida a qualquer domínio naquela floresta.
* Ele suporta instalar o serviço proxy em domínios de raiz ou criança, ou uma combinação desses.
* Precisa de conectividade de rede entre pelo menos um DC em cada domínio da floresta e um servidor de procuração de proteção de palavras-passe.
* Pode executar o serviço de procuração de senha Ad AD Azure num controlador de domínio para testes, mas esse controlador de domínio requer então conectividade à Internet. Esta conectividade pode ser uma preocupação de segurança. Recomendamos esta configuração apenas para testes.
* Recomendamos pelo menos dois servidores de proteção de senhas Azure AD por floresta para redundância, como indicado na secção anterior sobre [considerações de elevada disponibilidade](#high-availability-considerations).
* Não é suportado para executar o serviço de procuração de senha Azure AD num controlador de domínio apenas de leitura.

Para instalar o serviço de procuração de proteção de senha azure AD, complete os seguintes passos:

1. Para instalar o serviço de procuração de proteção de senhas Azure AD, execute o `AzureADPasswordProtectionProxySetup.exe` instalador de software.

    A instalação do software não requer um reboot e pode ser automatizada usando procedimentos MSI padrão, como no exemplo seguinte:
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > O serviço Windows Firewall deve estar a funcionar antes de instalar a `AzureADPasswordProtectionProxySetup.exe` embalagem para evitar um erro de instalação.
    >
    > Se o Windows Firewall estiver configurado para não funcionar, a solução é para ativar e executar temporariamente o serviço Firewall durante a instalação. O software proxy não tem dependência específica do Windows Firewall após a instalação.
    >
    > Se estiver a utilizar uma firewall de terceiros, ainda deve estar configurada para satisfazer os requisitos de implementação. Estes incluem permitir o acesso à entrada na porta 135 e a porta do servidor proxy RPC. Para obter mais informações, consulte a secção anterior sobre os [requisitos de implementação](#deployment-requirements).

1. O software de proteção de senha azure AD inclui um novo módulo PowerShell, `AzureADPasswordProtection` . Os passos seguintes executam vários cmdlets deste módulo PowerShell.

    Para utilizar este módulo, abra uma janela PowerShell como administrador e importe o novo módulo da seguinte forma:
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```

1. Para verificar se o serviço de procuração de proteção de passwords AZure AD está em funcionamento, utilize o seguinte comando PowerShell:

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    O resultado deve mostrar um **Estado** de *Execução*.

1. O serviço de procuração está a funcionar na máquina, mas não tem credenciais para comunicar com a Azure AD. Registe o servidor de procuração de proteção de passwords Azure AD com Azure AD utilizando o `Register-AzureADPasswordProtectionProxy` cmdlet.

    Este cmdlet requer credenciais *de Administrador Global* ou Administrador de *Segurança* para o seu inquilino Azure. Este cmdlet também deve ser executado usando uma conta com privilégios de administrador local.

    Após este comando ter sucesso uma vez para um serviço de procuração de senha Azure AD, invocações adicionais do mesmo são bem sucedidas, mas são desnecessárias.

    O `Register-AzureADPasswordProtectionProxy` cmdlet suporta os seguintes três modos de autenticação. Os dois primeiros modos suportam a autenticação multi-factor Azure AD, mas o terceiro modo não.

    > [!TIP]
    > Pode haver um atraso notável antes de ser concluído a primeira vez que este cmdlet é executado para um inquilino específico do Azure. A menos que um falhanço seja reportado, não se preocupe com este atraso.

     * Modo de autenticação interativa:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Este modo não funciona nos sistemas operativos Server Core. Em vez disso, utilize um dos seguintes modos de autenticação. Além disso, este modo pode falhar se a configuração de segurança melhorada do Internet Explorer estiver ativada. A solução é desativar essa Configuração, registar o proxy e, em seguida, reativá-la.

     * Modo de autenticação do código do dispositivo:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Quando solicitado, seguindo o link para abrir um navegador web e introduzir o código de autenticação.

     * Modo de autenticação silencioso (baseado em palavra-passe):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Este modo falha se a autenticação multi-factor Azure AD for necessária para a sua conta. Nesse caso, utilize um dos dois modos de autenticação anteriores ou utilize uma conta diferente que não exija MFA.
        >
        > Também pode ver o MFA exigido se o Registo do Dispositivo Azure (que é utilizado sob as capas pela Azure AD Password Protection) tiver sido configurado para exigir globalmente MFA. Para contornar este requisito, poderá utilizar uma conta diferente que suporte o MFA com um dos dois modos de autenticação anteriores, ou poderá também relaxar temporariamente o requisito de MFA de Registo do Dispositivo Azure.
        >
        > Para escoar esta alteração, procure e selecione **O Diretório Ativo Azure** no portal Azure, selecione **dispositivos > Configurações do Dispositivo**. Definir **Requerem que o Multi-Factor Auth se junte aos dispositivos** para *o Nº*. Certifique-se de reconfigurar esta definição de volta para *Sim* uma vez que o registo esteja completo.
        >
        > Recomendamos que os requisitos de MFA sejam ignorados apenas para fins de teste.

    Não precisa de especificar o parâmetro *-ForestCredential,* que está reservado para a funcionalidade futura.

    O registo do serviço de procuração de senha azure AD é necessário apenas uma vez durante a vida útil do serviço. Depois disso, o serviço de procuração de proteção de senhas Azure AD executará automaticamente qualquer outra manutenção necessária.

1. Registe agora a floresta ative diretório com as credenciais necessárias para comunicar com o Azure utilizando o `Register-AzureADPasswordProtectionForest` cmdlet PowerShell.

    > [!NOTE]
    > Se vários servidores de proteção de passwords Azure AD forem instalados no seu ambiente, não importa que servidor proxy usa para registar a floresta.

    O cmdlet requer credenciais *de Administrador Global* ou Administrador de *Segurança* para o seu inquilino Azure. Também requer no local privilégios de Administrador de Empresas de Direção Ativa. Também deve executar este cmdlet usando uma conta com privilégios de administrador local. A conta Azure que é utilizada para registar a floresta pode ser diferente da conta ative directy no local.
    
    Este passo é executado uma vez por floresta.

    O `Register-AzureADPasswordProtectionForest` cmdlet suporta os seguintes três modos de autenticação. Os dois primeiros modos suportam a autenticação multi-factor Azure AD, mas o terceiro modo não.

    > [!TIP]
    > Pode haver um atraso notável antes de ser concluído a primeira vez que este cmdlet é executado para um inquilino específico do Azure. A menos que um falhanço seja reportado, não se preocupe com este atraso.

     * Modo de autenticação interativa:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Este modo não funcionará nos sistemas operativos Server Core. Em vez disso, utilize um dos dois modos de autenticação seguintes. Além disso, este modo pode falhar se a configuração de segurança melhorada do Internet Explorer estiver ativada. A solução é desativar essa Configuração, registar a floresta e, em seguida, reativá-la.  

     * Modo de autenticação do código do dispositivo:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Quando solicitado, seguindo o link para abrir um navegador web e introduzir o código de autenticação.

     * Modo de autenticação silencioso (baseado em palavra-passe):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Este modo falha se a autenticação multi-factor Azure AD for necessária para a sua conta. Nesse caso, utilize um dos dois modos de autenticação anteriores ou utilize uma conta diferente que não exija MFA.
        >
        > Também pode ver o MFA exigido se o Registo do Dispositivo Azure (que é utilizado sob as capas pela Azure AD Password Protection) tiver sido configurado para exigir globalmente MFA. Para contornar este requisito, poderá utilizar uma conta diferente que suporte o MFA com um dos dois modos de autenticação anteriores, ou poderá também relaxar temporariamente o requisito de MFA de Registo do Dispositivo Azure.
        >
        > Para escoar esta alteração, procure e selecione **O Diretório Ativo Azure** no portal Azure, selecione **dispositivos > Configurações do Dispositivo**. Definir **Requerem que o Multi-Factor Auth se junte aos dispositivos** para *o Nº*. Certifique-se de reconfigurar esta definição de volta para *Sim* uma vez que o registo esteja completo.
        >
        > Recomendamos que os requisitos de MFA sejam ignorados apenas para fins de teste.

       Estes exemplos só são bem sucedidos se o utilizador atualmente inscrito for também um administrador de domínio do Ative Directory para o domínio raiz. Se não for esse o caso, pode fornecer credenciais de domínio alternativas através do parâmetro *-ForestCredential.*

    O registo da floresta do Diretório Ativo só é necessário uma vez na vida útil da floresta. Depois disso, os agentes Azure AD Password Protection DC na floresta realizam automaticamente qualquer outra manutenção necessária. Depois de `Register-AzureADPasswordProtectionForest` correr com sucesso para uma floresta, invocações adicionais do cmdlet são bem sucedidas, mas são desnecessárias.
    
    Para `Register-AzureADPasswordProtectionForest` ter sucesso, pelo menos um DC com execução do Windows Server 2012 ou mais tarde deve estar disponível no domínio do servidor de proteção de senhas Azure AD. O software do agente Azure AD Password Protection DC não tem de ser instalado em nenhum controlador de domínio antes deste passo.

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>Configure o serviço de procuração para comunicar através de um representante HTTP

Se o seu ambiente exigir a utilização de um representante http específico para comunicar com o Azure, utilize os seguintes passos para configurar o serviço de Proteção de Passwords Azure AD.

Crie um ficheiro *AzureADPasswordProtectionProxy.exe.config* na `%ProgramFiles%\Azure AD Password Protection Proxy\Service` pasta. Incluir o seguinte conteúdo:

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

Se o seu representante HTTP necessitar de autenticação, adicione a etiqueta *DedefaultCredentials de utilização:*

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

Em ambos os casos, `http://yourhttpproxy.com:8080` substitua-o pelo endereço e porta do seu servidor de procuração HTTP específico.

Se o seu representante HTTP estiver configurado para utilizar uma política de autorização, deve conceder acesso à conta de computador Ative Directory da máquina que acolhe o serviço de procuração para proteção de passwords.

Recomendamos que pare e reinicie o serviço de procuração de senha azure AD depois de criar ou atualizar o ficheiro *AzureADPasswordProtectionProxy.exe.config.*

O serviço de procuração não suporta o uso de credenciais específicas para a ligação a um representante HTTP.

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>Configure o serviço de procuração para ouvir em uma porta específica

O software do agente Azure AD Password Protection DC utiliza RPC sobre TCP para comunicar com o serviço de procuração. Por predefinição, o serviço de procuração de proteção de senha azure AD ouve em qualquer ponto final dinâmico RPC disponível. Pode configurar o serviço para ouvir numa porta TCP específica, se necessário devido a requisitos de topologia ou firewall em rede no seu ambiente.

<a id="static" /></a>Para configurar o serviço para funcionar sob uma porta estática, utilize o `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet da seguinte forma:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> Tem de parar e reiniciar o serviço de procuração de proteção de senhas Azure AD para que estas alterações entrem em vigor.

Para configurar o serviço para funcionar sob uma porta dinâmica, use o mesmo procedimento, mas volte a *zero:*

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> Tem de parar e reiniciar o serviço de procuração de proteção de senhas Azure AD para que estas alterações entrem em vigor.

O serviço de procuração de proteção de senha azure AD requer um reinício manual após qualquer alteração na configuração da porta. Não é preciso reiniciar o serviço de agente de proteção de passwords Azure AD nos controladores de domínio depois de escamar estas alterações de configuração.

Para consultar a configuração atual do serviço, utilize o `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet como mostrado no exemplo seguinte

```powershell
Get-AzureADPasswordProtectionProxyConfiguration | fl
```

A saída de exemplo a seguir mostra que o serviço de procuração de proteção de senhas Azure AD está a utilizar uma porta dinâmica:

```output
ServiceName : AzureADPasswordProtectionProxy
DisplayName : Azure AD password protection Proxy
StaticPort  : 0
```

## <a name="install-the-dc-agent-service"></a>Instale o serviço de agente DC

Para instalar o serviço de agente de proteção de senhas Azure AD, execute o `AzureADPasswordProtectionDCAgentSetup.msi` pacote.

Pode automatizar a instalação do software utilizando os procedimentos MSI padrão, como mostra o seguinte exemplo:

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

A `/norestart` bandeira pode ser omitida se preferir que o instalador reinicie automaticamente a máquina.

A instalação do software, ou desinstalação, requer um reinício. Este requisito deve-se ao momento em que os DLLs do filtro de palavra-passe só são carregados ou descarregados através de um reinício.

A instalação da Proteção de Passwords AD AD on-prem Azure está completa após a instalação do software do agente DC num controlador de domínio e o computador é reiniciado. Nenhuma outra configuração é necessária ou possível. Os eventos de alteração de palavra-passe contra os DCs on-prem usam as listas de senhas proibidas configuradas do Azure AD.

Para ativar a proteção de senha azure Ad on-prem a partir do portal Azure ou configurar senhas proibidas personalizadas, consulte [Enable on-in-in Azure AD Password Protection](howto-password-ban-bad-on-premises-operations.md).

> [!TIP]
> Pode instalar o agente Azure AD Password Protection DC numa máquina que ainda não é um controlador de domínio. Neste caso, o serviço inicia e funciona mas permanece inativo até que a máquina seja promovida para ser um controlador de domínio.

## <a name="upgrading-the-proxy-service"></a>Modernização do serviço de procuração

O serviço de procuração de proteção de senha azure AD suporta a atualização automática. A atualização automática utiliza o serviço Microsoft Azure AD Connect Agent Updater, que está instalado lado a lado com o serviço proxy. A atualização automática é iniciada por predefinição e pode ser ativada ou desativada utilizando o `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet.

A definição atual pode ser consultada utilizando o `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet. Recomendamos que a definição de atualização automática esteja sempre ativada.

O `Get-AzureADPasswordProtectionProxy` cmdlet pode ser usado para consultar a versão de software de todos os servidores de proteção de senha azure AD atualmente instalados numa floresta.

### <a name="manual-upgrade-process"></a>Processo de atualização manual

Uma atualização manual é realizada executando a versão mais recente do instalador de `AzureADPasswordProtectionProxySetup.exe` software. A versão mais recente do software está disponível no [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

Não é necessário desinstalar a versão atual do serviço de procuração de senhas Azure AD - o instalador executa uma atualização no local. Não deve ser necessário reiniciar a atualização do serviço de procuração. A atualização do software pode ser automatizada utilizando procedimentos MSI padrão, tais como `AzureADPasswordProtectionProxySetup.exe /quiet` .

## <a name="upgrading-the-dc-agent"></a>Modernização do agente de DC

Quando uma versão mais recente do software de proteção de senhas AD AD Azure está disponível, a atualização é realizada executando a versão mais recente do pacote de `AzureADPasswordProtectionDCAgentSetup.msi` software. A versão mais recente do software está disponível no [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

Não é necessário desinstalar a versão atual do software do agente DC - o instalador executa uma atualização no local. É sempre necessário um reboot ao atualizar o software do agente DC - este requisito é causado pelo comportamento do Core Windows.

A atualização do software pode ser automatizada utilizando procedimentos MSI padrão, tais como `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart` .

Pode omitir a `/norestart` bandeira se preferir que o instalador reinicie automaticamente a máquina.

O `Get-AzureADPasswordProtectionDCAgent` cmdlet pode ser usado para consultar a versão de software de todos os agentes Azure AD Password Protection DC atualmente instalados numa floresta.

## <a name="next-steps"></a>Passos seguintes

Agora que instalou os serviços de que necessita para a Proteção de Passwords AD AZure nos seus servidores no local, [ative a proteção de senha azure pré-prem no portal Azure](howto-password-ban-bad-on-premises-operations.md) para completar a sua implementação.