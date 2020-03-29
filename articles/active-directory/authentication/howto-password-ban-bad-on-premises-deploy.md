---
title: Implementar no local a Proteção de Passwords AD Azure
description: Saiba como planear e implementar a Proteção de Passwords Azure AD num ambiente de Serviços de Domínio de Diretório Ativo no local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 03/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: a977eac19128886dd3c379e200f7cb78066a06af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671765"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>Planear e implementar no local a Proteção de Palavras-passe de Diretório Ativo azure

Os utilizadores geralmente criam senhas que usam palavras locais comuns, como uma escola, equipe de esportes ou pessoa famosa. Estas palavras-passe são fáceis de adivinhar, e fracas contra ataques baseados no dicionário. Para impor senhas fortes na sua organização, a Azure Ative Directory (Azure AD) Password Protection fornece uma lista de senhas proibidas global e personalizada. Um pedido de alteração de senha falha se houver uma correspondência nesta lista de senhas proibidas.

Para proteger o ambiente de Serviços de Domínio ativo do Diretório Ativo (AD DS) no local, pode instalar e configurar a Proteção de Passwords Azure AD para trabalhar com o seu em prem DC. Este artigo mostra-lhe como instalar e registar o serviço de proxy de proteção de passwords Azure AD e o agente DC de proteção de senhas Azure AD no seu ambiente no local.

Para obter mais informações sobre como a Proteção de Passwords Azure AD funciona num ambiente no local, consulte como impor a Proteção de [Passwords AD Azure para](concept-password-ban-bad-on-premises.md)o Diretório Ativo do Servidor do Windows .

## <a name="deployment-strategy"></a>Estratégia de implantação

O diagrama seguinte mostra como os componentes básicos da Proteção de Passwords Azure AD funcionam em conjunto num ambiente de Diretório Ativo no local:

![Como os componentes de proteção de senha seletiva saem em conjunto](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

É uma boa ideia rever como o software funciona antes de o implementares. Para mais informações, consulte a [visão geral conceptual da Proteção de Passwords Azure AD](concept-password-ban-bad-on-premises.md).

Recomendamos que inicie as implementações no modo de *auditoria.* O modo de auditoria é a definição inicial predefinida, onde as palavras-passe podem continuar a ser definidas. As palavras-passe que seriam bloqueadas são registadas no registo do evento. Depois de implementar os servidores proxy e os agentes DED no modo de auditoria, monitorize o impacto que a política de passwords terá nos utilizadores quando a política for aplicada.

Durante a fase de auditoria, muitas organizações constatam que as seguintes situações se aplicam:

* Precisam de melhorar os processos operacionais existentes para utilizarem senhas mais seguras.
* Os utilizadores usam frequentemente senhas não seguras.
* Precisam de informar os utilizadores sobre a próxima alteração na aplicação da segurança, possível impacto sobre eles e como escolher palavras-passe mais seguras.

Também é possível que uma validação de palavra-passe mais forte afete a automatização de implementação do controlador de domínio Ative Directory existente. Recomendamos que pelo menos uma promoção em DC e uma despromoção em DC ocorram durante a avaliação do período de auditoria para ajudar a desvendar tais questões. Para obter mais informações, veja os artigos seguintes:

* [Ntdsutil.exe é incapaz de definir uma senha fraca do Modo de Reparação de Serviços de Diretório](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [A promoção da réplica do controlador de domínio falha devido a uma fraca senha do Modo de Reparação de Serviços de Diretório](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [A despromoção do controlador de domínio falha devido a uma fraca senha do Administrador Local](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

Depois de a funcionalidade ter estado em execução no modo de auditoria durante um período razoável, pode mudar a configuração da *Auditoria* para *a Aplicação* para exigir senhas mais seguras. Uma monitorização adicional durante este tempo é uma boa ideia.

### <a name="multiple-forest-considerations"></a>Múltiplas considerações florestais

Não existem requisitos adicionais para implementar a Proteção de Passwords Azure AD em várias florestas.

Cada floresta é configurada independentemente, como descrito na secção seguinte para implantar a proteção de [senhas ad-prem Azure AD](#download-required-software). Cada representante de proteção de senhas Azure AD só pode suportar controladores de domínio da floresta a que está unida.

O software de proteção de passwords Azure AD em qualquer floresta desconhece o software de proteção de passwords que é implantado noutras florestas, independentemente das configurações de confiança do Ative Directory.

### <a name="read-only-domain-controller-considerations"></a>Considerações de controlador de domínio apenas de leitura

Os eventos de alteração de palavra-passe ou de conjunto não são processados e persistem em controladores de domínio apenas de leitura (RODCs). Em vez disso, são encaminhados para controladores de domínio supreendáveis. Não é necessário instalar o software de agente DC de proteção de senhas Azure AD em RODCs.

Além disso, não é suportado para executar o serviço de proxy de proteção de senhas Azure AD num controlador de domínio apenas para leitura.

### <a name="high-availability-considerations"></a>Considerações de elevada disponibilidade

A principal preocupação para a proteção de passwords é a disponibilidade de servidores de proxy de proteção de senhas Azure AD quando os DCs numa floresta tentam descarregar novas políticas ou outros dados do Azure. Cada agente azure AD Password Protection DC usa um algoritmo simples de estilo robin redondo ao decidir qual servidor proxy ligar. O agente ignora servidores de procuração que não estão a responder.

Para as implementações de Diretório Ativo mais conectadas que possuem replicação saudável tanto do estado de pastagem de diretório como de sysvol, dois servidores de proxy de proteção de passwords Azure AD são suficientes para garantir a disponibilidade. Esta configuração resulta no download atempada de novas políticas e outros dados. Pode implementar servidores adicionais de proxy de proteção de passwords Azure AD, se desejar.

O design do software de agente DC de proteção de senhas Azure AD atenua os problemas habituais que estão associados com elevada disponibilidade. O agente DC de proteção de senhas Azure AD mantém uma cache local da política de senha mais recentemente descarregada. Mesmo que todos os servidores proxy registados fiquem indisponíveis, os agentes DC de Proteção de Passwords AD do Azure continuam a aplicar a sua política de senha seletiva.

Uma frequência de atualização razoável para as políticas de senha numa grande implementação é geralmente dias, não horas ou menos. Assim, breves interrupções dos servidores proxy não impactam significativamente a Proteção de Passwords Azure AD.

## <a name="deployment-requirements"></a>Requisitos de implementação

Para obter informações sobre o licenciamento, consulte os requisitos de licenciamento de proteção de [senhas da Azure AD](concept-password-ban-bad.md#license-requirements).

Aplicam-se os seguintes requisitos fundamentais:

* Todas as máquinas, incluindo controladores de domínio, que tenham componentes de proteção de senhas Azure AD instalados devem ter o Tempo de Execução Universal C instalado.
    * Pode obter o tempo de execução, certificando-se de que tem todas as atualizações a partir do Windows Update. Ou pode obtê-lo num pacote de atualização específico para o OS. Para mais informações, consulte [Atualização para o Tempo de Execução Universal C no Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* Precisa de uma conta que tenha privilégios de administrador de domínio ative diretório no domínio da raiz da floresta para registar a floresta de Diretório Ativo do Windows Server com a AD Azure.
* O Serviço de Distribuição de Chaves deve ser ativado em todos os controladores de domínio do domínio que executam o Windows Server 2012. Por predefinição, este serviço é ativado através do arranque manual do gatilho.
* A conectividade da rede deve existir entre pelo menos um controlador de domínio em cada domínio e pelo menos um servidor que acolhe o serviço proxy para a Proteção de Passwords AD Azure. Esta conectividade deve permitir ao controlador de domínio aceder à porta de mapa de ponto final RPC 135 e à porta do servidor RPC no serviço proxy.
    * Por predefinição, a porta do servidor RPC é uma porta RPC dinâmica, mas pode ser configurada para [utilizar uma porta estática](#static).
* Todas as máquinas onde o serviço de procuração de passwords Azure AD será instalado devem ter acesso à rede aos seguintes pontos finais:

    |**Ponto Final**|**Objetivo**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Pedidos de autenticação|
    |`https://enterpriseregistration.windows.net`|Funcionalidade de Proteção de Passwords Azure AD|

### <a name="azure-ad-password-protection-dc-agent"></a>Agente DC de proteção de senha da AD Azure

Os seguintes requisitos aplicam-se ao agente DC de proteção de senhas da AD Azure:

* Todas as máquinas onde o software de agente DC de proteção de senhas Azure AD será instalado devem executar o Windows Server 2012 ou mais tarde.
    * O domínio ou floresta de Diretório Ativo não precisa de estar ao nível funcional do domínio Do Windows Server 2012 (DFL) ou ao nível funcional da floresta (FFL). Como mencionado nos princípios de [design,](concept-password-ban-bad-on-premises.md#design-principles)não há dFL ou FFL mínimo necessários para que o agente DC ou o software proxy sejam executados.
* Todas as máquinas que executam o agente DC de proteção de senhas Azure AD devem ter .NET 4.5 instaladas.
* Qualquer domínio de Diretório Ativo que execute o serviço de agente DC de proteção de passwords Azure AD deve utilizar a replicação do sistema de ficheiros distribuídos (DFSR) para a replicação de sysvol.
   * Se o seu domínio ainda não estiver a utilizar o DFSR, tem de migrar antes de instalar a Proteção de Passwords Azure AD. Para mais informações, consulte Guia de [Migração de Replicação SYSVOL: FRS para replicação dfs](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

    > [!WARNING]
    > O software do agente DC de proteção de passwords Azure AD irá atualmente instalar em controladores de domínio em domínios que ainda estão a utilizar FRS (a tecnologia antecessora para DFSR) para a replicação de sysvol, mas o software NÃO funcionará corretamente neste ambiente.
    >
    > Efeitos colaterais negativos adicionais incluem ficheiros individuais que não se replicam, e os procedimentos de restauro sysvol parecem ter sucesso, mas falhando silenciosamente em replicar todos os ficheiros.
    >
    > Migrar o seu domínio para utilizar o DFSR o mais rapidamente possível, tanto para os benefícios inerentes da DFSR como para desbloquear a implementação da Proteção de Passwords Azure AD. As futuras versões do software serão automaticamente desativadas quando estiverem a funcionar num domínio que ainda está a utilizar FRS.

### <a name="azure-ad-password-protection-proxy-service"></a>Serviço de proxy de proteção de senhas Azure AD

Os seguintes requisitos aplicam-se ao serviço de proxy de proteção de passwords Azure AD:

* Todas as máquinas onde o serviço de proxy de proteção de senhas Azure AD será instalado devem executar o Windows Server 2012 R2 ou mais tarde.

    > [!NOTE]
    > A implementação do serviço de proxy de proteção por palavra-passe Azure AD é um requisito obrigatório para a implementação da Proteção de Passwords Azure AD, mesmo que o controlador de domínio possa ter uma conectividade direta de internet.

* Todas as máquinas onde o serviço de proteção por palavra-passe Azure AD será instalado devem ter .NET 4.7 instaladas.
    * .NET 4.7 já deve ser instalado num Servidor Windows totalmente atualizado. Se necessário, faça o download e execute o instalador encontrado no [instalador offline .NET Framework 4.7 para windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* Todas as máquinas que acolhem o serviço de proxy de proteção de passwords Azure AD devem ser configuradas para conceder aos controladores de domínio a capacidade de iniciar sessão no serviço proxy. Esta capacidade é controlada através da atribuição de privilégios "Access this computer from the network".
* Todas as máquinas que acolhem o serviço de proxy de proteção de senhas Azure AD devem ser configuradas para permitir o tráfego de saída TLS 1.2 HTTP.
* Uma conta *de Administrador Global* para registar o serviço de proxy de proteção de senhas Azure AD e floresta com AD Azure.
* O acesso à rede deve ser ativado para o conjunto de portas e URLs especificados nos procedimentos de configuração do ambiente proxy de [aplicação](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment).

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>Pré-requisitos do AD Connect Agent da Microsoft Azure

O serviço de atualização do Agente de Ligação AD Microsoft Azure é instalado lado a lado com o serviço de proxy de proteção de passwords Azure AD. É necessária uma configuração adicional para que o serviço de atualização do Agente de Ligação AD da Microsoft Azure seja capaz de funcionar:

* Se o seu ambiente utilizar um servidor proxy HTTP, siga as diretrizes especificadas no [Trabalho com servidores proxy existentes no local](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers).
* O serviço de atualização do Agente de Ligação AD Microsoft Azure também requer as etapas TLS 1.2 especificadas nos [requisitos do TLS](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#tls-requirements).

> [!WARNING]
> O representante de proteção de passwords Azure AD e o Proxy de Aplicação AD Azure instalam diferentes versões do serviço de atualização do agente de ligação ad da Microsoft Azure, razão pela qual as instruções se referem ao conteúdo proxy da aplicação. Estas diferentes versões são incompatíveis quando instaladas lado a lado, pelo que não é recomendado instalar o Proxy de Proteção de Passwords Azure AD e proxy de aplicação na mesma máquina.

## <a name="download-required-software"></a>Descarregar software necessário

Existem dois instaladores necessários para uma implementação de proteção de senhas Azure AD no local:

* Agente DC de proteção de senha seletiva*AzureADPasswordProtectionDCAgentSetup.msi*)
* Procuração de proteção de senha seletiva Azure AD *(AzureADPasswordProtectionProxySetup.exe*)

Descarregue ambos os instaladores do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="install-and-configure-the-proxy-service"></a>Instale e configure o serviço proxy

O serviço de proxy de proteção por palavra-passe Azure AD está normalmente num servidor membro no seu ambiente AD DS no local. Uma vez instalado, o serviço de proxy de proteção de senhas Azure AD comunica com a Azure AD para manter uma cópia das listas de senhas banidas global e de cliente para o seu inquilino Azure AD.

Na secção seguinte, instala os agentes DC de proteção de senhas Azure AD em controladores de domínio no seu ambiente AD DS no local. Estes agentes de DC comunicam com o serviço proxy para obter as mais recentes listas de senhas proibidas para uso ao processar eventos de mudança de palavra-passe dentro do domínio.

Escolha um ou mais servidores para acolher o serviço de proxy de proteção de senhas Azure AD. As seguintes considerações aplicam-se para os servidores( s):

* Cada serviço deste tipo só pode fornecer políticas de senha para uma única floresta. A máquina hospedeira deve ser unida a um domínio naquela floresta. Os domínios raiz e infantil são ambos apoiados. Precisa de conectividade de rede entre pelo menos um DC em cada domínio da floresta e a máquina de proteção de senhas.
* Pode executar o serviço de proxy de proteção de palavras-passe Azure AD num controlador de domínio para testes, mas esse controlador de domínio necessita de conectividade de internet. Esta conectividade pode ser uma preocupação de segurança. Recomendamos esta configuração apenas para testes.
* Recomendamos pelo menos dois servidores de proxy de proteção de senhas Azure AD para despedimento, como notado na secção anterior sobre [considerações](#high-availability-considerations)de alta disponibilidade .
* Não é suportado para executar o serviço de proxy de proteção de senhas Azure AD num controlador de domínio apenas para leitura.

Para instalar o serviço de proxy de proteção por palavra-passe Azure AD, preencha os seguintes passos:

1. Para instalar o serviço de proxy de `AzureADPasswordProtectionProxySetup.exe` proteção de senhas AD Azure, execute o instalador de software.

    A instalação do software não requer um reboot e pode ser automatizada utilizando procedimentos msi padrão, como no exemplo seguinte:
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > O serviço Windows Firewall deve estar `AzureADPasswordProtectionProxySetup.exe` a funcionar antes de instalar a embalagem para evitar um erro de instalação.
    >
    > Se o Windows Firewall estiver configurado para não funcionar, a suposição é ativar e executar temporariamente o serviço Firewall durante a instalação. O software proxy não tem nenhuma dependência específica do Windows Firewall após a instalação.
    >
    > Se estiver a utilizar uma firewall de terceiros, deve ainda ser configurada para satisfazer os requisitos de implementação. Estes incluem permitir o acesso de entrada à porta 135 e a porta de servidor proxy RPC. Para mais informações, consulte a secção anterior sobre [os requisitos](#deployment-requirements)de implementação .

1. O software de proxy de proteção de passwords Azure AD inclui um novo módulo PowerShell, `AzureADPasswordProtection`. Os passos seguintes são executados vários cmdlets deste módulo PowerShell.

    Para utilizar este módulo, abra uma janela PowerShell como administrador e importe o novo módulo da seguinte forma:
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```

1. Para verificar se o serviço de proxy de proteção por palavra-passe AD Azure está em funcionamento, utilize o seguinte comando PowerShell:

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    O resultado deve mostrar um **Estado** de *Execução*.

1. O serviço de procuração está a funcionar na máquina, mas não tem credenciais para comunicar com a Azure AD. Registe o servidor de proxy de proteção de `Register-AzureADPasswordProtectionProxy` senhas Azure AD com a AD Azure utilizando o cmdlet.

    Este cmdlet requer credenciais de administrador global para o seu inquilino Azure. Você também precisa de privilégios de administrador de domínio de diretório ativo no domínio da raiz da floresta. Este cmdlet também deve ser executado utilizando uma conta com privilégios de administrador local:

    Depois deste comando ter sucesso uma vez para um serviço de proxy de proteção de senhas Azure AD, as invocações adicionais do mesmo têm sucesso, mas são desnecessárias.

    O `Register-AzureADPasswordProtectionProxy` cmdlet suporta os seguintes três modos de autenticação. Os dois primeiros modos suportam a autenticação Azure Multi-Factor, mas o terceiro modo não.

    > [!TIP]
    > Pode haver um atraso notável antes de terminar a primeira vez que este cmdlet é executado para um inquilino Azure específico. A menos que uma falha seja reportada, não se preocupe com este atraso.

     * Modo de autenticação interativa:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Este modo não funciona nos sistemas operativos Server Core. Em vez disso, utilize um dos seguintes modos de autenticação. Além disso, este modo pode falhar se a configuração de segurança melhorada do Internet Explorer estiver ativada. A suposição é desativar essa Configuração, registar o proxy e, em seguida, reativa-lo.

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
        > Este modo falha se a autenticação de multifactor do Azure for necessária para a sua conta. Nesse caso, utilize um dos dois modos de autenticação anteriores ou utilize uma conta diferente que não exija MFA.
        >
        > Também pode ver o MFA necessário se o Registo de Dispositivos Azure (que é utilizado sob as capas pela Azure AD Password Protection) tiver sido configurado para exigir globalmente mFA. Para contornar este requisito poderá utilizar uma conta diferente que suporta o MFA com um dos dois modos de autenticação anteriores, ou poderá também relaxar temporariamente o requisito de MFA de Registo de Dispositivos Azure.
        >
        > Para efazer esta alteração, procure e selecione **O Diretório Ativo Azure** no portal Azure e, em seguida, selecione **Dispositivos > Definições**de Dispositivo . O conjunto **requer a Auth multi-factor para unir dispositivos** a *No*. Certifique-se de reconfigurar esta definição para *Sim* uma vez que o registo esteja completo.
        >
        > Recomendamos que os requisitos de MFA sejam ignorados apenas para efeitos de teste.

    Atualmente não é preciso especificar o parâmetro *-ForestCredential,* que está reservado para futuras funcionalidades.

    O registo do serviço de procuração de senhas Azure AD só é necessário uma vez na vida útil do serviço. Depois disso, o serviço de proxy de proteção por palavra-passe Azure AD realizará automaticamente qualquer outra manutenção necessária.

1. Registe agora a floresta de Diretório Ativo no local com as `Register-AzureADPasswordProtectionForest` credenciais necessárias para comunicar com o Azure utilizando o cmdlet PowerShell.

    > [!NOTE]
    > Se vários servidores de proxy de proteção de senhas Azure AD estiverem instalados no seu ambiente, não importa qual servidor proxy que utilize para registar a floresta.

    O cmdlet requer credenciais de administrador global para o seu inquilino Azure. Também deve executar este cmdlet usando uma conta com privilégios de administrador local. Também requer privilégios de administrador de empresa de diretório ativo no local. Este passo é executado uma vez por floresta.

    O `Register-AzureADPasswordProtectionForest` cmdlet suporta os seguintes três modos de autenticação. Os dois primeiros modos suportam a autenticação Azure Multi-Factor, mas o terceiro modo não.

    > [!TIP]
    > Pode haver um atraso notável antes de terminar a primeira vez que este cmdlet é executado para um inquilino Azure específico. A menos que uma falha seja reportada, não se preocupe com este atraso.

     * Modo de autenticação interativa:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Este modo não funcionará nos sistemas operativos Server Core. Em vez disso, utilize um dos dois modos de autenticação seguintes. Além disso, este modo pode falhar se a configuração de segurança melhorada do Internet Explorer estiver ativada. A suposição é desativar essa Configuração, registar a floresta e, em seguida, reativa-la.  

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
        > Este modo falha se a autenticação de multifactor do Azure for necessária para a sua conta. Nesse caso, utilize um dos dois modos de autenticação anteriores ou utilize uma conta diferente que não exija MFA.
        >
        > Também pode ver o MFA necessário se o Registo de Dispositivos Azure (que é utilizado sob as capas pela Azure AD Password Protection) tiver sido configurado para exigir globalmente mFA. Para contornar este requisito poderá utilizar uma conta diferente que suporta o MFA com um dos dois modos de autenticação anteriores, ou poderá também relaxar temporariamente o requisito de MFA de Registo de Dispositivos Azure.
        >
        > Para efazer esta alteração, procure e selecione **O Diretório Ativo Azure** no portal Azure e, em seguida, selecione **Dispositivos > Definições**de Dispositivo . O conjunto **requer a Auth multi-factor para unir dispositivos** a *No*. Certifique-se de reconfigurar esta definição para *Sim* uma vez que o registo esteja completo.
        >
        > Recomendamos que os requisitos de MFA sejam ignorados apenas para efeitos de teste.

       Estes exemplos só têm sucesso se o utilizador atualmente assinado for também um administrador de domínio de Diretório Ativo para o domínio raiz. Se não for esse o caso, pode fornecer credenciais de domínio alternativas através do parâmetro *-ForestCredential.*

    O registo da floresta de Diretório Ativo só é necessário uma vez na vida da floresta. Depois disso, os agentes DC de proteção de senhas AD Azure na floresta realizam automaticamente qualquer outra manutenção necessária. Depois `Register-AzureADPasswordProtectionForest` de correr com sucesso para uma floresta, as invocações adicionais do cmdlet têm sucesso, mas são desnecessárias.
    
    Para `Register-AzureADPasswordProtectionForest` ter sucesso, pelo menos um DC que executa o Windows Server 2012 ou mais tarde deve estar disponível no domínio do servidor proxy de proteção de passwords Azure AD. O software do agente DC de proteção de senhas Azure AD não tem de ser instalado em quaisquer controladores de domínio antes deste passo.

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>Configure o serviço proxy para comunicar através de um proxy HTTP

Se o seu ambiente necessitar da utilização de um representante http específico para comunicar com o Azure, utilize os seguintes passos para configurar o serviço de proteção de passwords Azure AD.

Crie um ficheiro *AzureADPasswordProtectionProxy.exe.config* na `%ProgramFiles%\Azure AD Password Protection Proxy\Service` pasta. Incluir os seguintes conteúdos:

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

Se o seu proxy HTTP necessitar de autenticação, adicione a etiqueta *useDefaultCredentials:*

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

Em ambos os `http://yourhttpproxy.com:8080` casos, substitua-o pelo endereço e porta do seu servidor proxy HTTP específico.

Se o seu representante HTTP estiver configurado para utilizar uma política de autorização, deve conceder acesso à conta de computador do Diretório Ativo da máquina que acolhe o serviço proxy para proteção de palavras-passe.

Recomendamos que pare e reinicie o serviço de proxy de proteção de senhas Azure AD depois de criar ou atualizar o ficheiro *AzureADPasswordProtectionProxy.exe.config.*

O serviço proxy não suporta o uso de credenciais específicas para a ligação a um proxy HTTP.

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>Configure o serviço de procuração para ouvir numa porta específica

O software de agente DC de proteção de senhas Azure AD utiliza RPC sobre TCP para comunicar com o serviço proxy. Por predefinição, o serviço de proxy de proteção por palavra-passe Azure AD ouve qualquer ponto final dinâmico rPC disponível. Pode configurar o serviço para ouvir uma porta TCP específica, se necessário devido aos requisitos de topoologia de rede ou firewall no seu ambiente.

<a id="static" /></a>Para configurar o serviço a funcionar sob `Set-AzureADPasswordProtectionProxyConfiguration` uma porta estática, utilize o cmdlet da seguinte forma:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> Tem de parar e reiniciar o serviço de proxy de proteção de senhas Azure AD para que estas alterações entrem em vigor.

Para configurar o serviço para funcionar sob uma porta dinâmica, utilize o mesmo procedimento, mas volte a colocar *a StaticPort* a zero:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> Tem de parar e reiniciar o serviço de proxy de proteção de senhas Azure AD para que estas alterações entrem em vigor.

O serviço de proxy de proteção por palavra-passe Azure AD requer um reinício manual após qualquer alteração na configuração da porta. Não é necessário reiniciar o serviço de agente DC de proteção de senhas Azure AD nos controladores de domínio depois de então fazer estas alterações de configuração.

Para consultar a configuração atual do `Get-AzureADPasswordProtectionProxyConfiguration` serviço, utilize o cmdlet como mostrado no seguinte exemplo

```powershell
Get-AzureADPasswordProtectionProxyConfiguration | fl
```

A saída de exemplo seguinte mostra que o serviço de proxy de proteção de senhas Azure AD está a utilizar uma porta dinâmica:

```output
ServiceName : AzureADPasswordProtectionProxy
DisplayName : Azure AD password protection Proxy
StaticPort  : 0
```

## <a name="install-the-dc-agent-service"></a>Instale o serviço de agente DC

Para instalar o serviço de agente DC de `AzureADPasswordProtectionDCAgentSetup.msi` proteção de senhas Azure AD, execute o pacote.

Pode automatizar a instalação do software utilizando procedimentos MSI padrão, como mostra o seguinte exemplo:

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

A `/norestart` bandeira pode ser omitida se preferir que o instalador reinicie automaticamente a máquina.

A instalação do software, ou desinstalação, requer um reinício. Este requisito deve-se ao dever-se ao dLLs do filtro de senha somente carregado soro ou descarregado por um reinício.

A instalação de proteção de palavra-passe ad azure em prem é completa da dona do software do agente DC num controlador de domínio, e esse computador é reiniciado. Não é necessária qualquer outra configuração ou possível. Os eventos de alteração de palavra-passe contra os DCs on-prem utilizam as listas de senhas proibidas configuradas a partir de Azure AD.

Para ativar a proteção de palavras-passe ad azure on-prem do portal Azure ou configurar senhas proibidas personalizadas, consulte [Enable on-premise Azure AD Password Protection](howto-password-ban-bad-on-premises-operations.md).

> [!TIP]
> Pode instalar o agente DC de proteção de senhas Azure AD numa máquina que ainda não seja um controlador de domínio. Neste caso, o serviço começa e funciona, mas permanece inativo até que a máquina seja promovida para ser um controlador de domínio.

## <a name="upgrading-the-proxy-service"></a>Modernização do serviço de procuração

O serviço de proxy de proteção por palavra-passe Azure AD suporta a atualização automática. A atualização automática utiliza o serviço de atualização do Agente de Ligação AD Microsoft Azure, que é instalado lado a lado com o serviço proxy. A atualização automática está por defeito e pode `Set-AzureADPasswordProtectionProxyConfiguration` ser ativada ou desativada utilizando o cmdlet.

A regulação atual pode ser `Get-AzureADPasswordProtectionProxyConfiguration` consultada utilizando o cmdlet. Recomendamos que a definição de atualização automática esteja sempre ativada.

O `Get-AzureADPasswordProtectionProxy` cmdlet pode ser utilizado para consultar a versão de software de todos os servidores de proxy de proteção de senhas AD atualmente instalados numa floresta.

### <a name="manual-upgrade-process"></a>Processo de atualização manual

Uma atualização manual é realizada executando a versão mais recente do instalador de `AzureADPasswordProtectionProxySetup.exe` software. A versão mais recente do software está disponível no [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

Não é necessário desinstalar a versão atual do serviço de proxy de proteção de passwords Azure AD - o instalador realiza uma atualização no local. Não deve ser necessário reiniciar durante a atualização do serviço de procuração. A atualização do software pode ser automatizada `AzureADPasswordProtectionProxySetup.exe /quiet`utilizando procedimentos mSI padrão, tais como .

## <a name="upgrading-the-dc-agent"></a>Modernização do agente dc

Quando uma versão mais recente do software de agente DC de proteção de senhas Azure AD está disponível, a atualização é realizada executando a versão mais recente do `AzureADPasswordProtectionDCAgentSetup.msi` pacote de software. A versão mais recente do software está disponível no [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

Não é necessário desinstalar a versão atual do software do agente DC - o instalador realiza uma atualização no local. É sempre necessário reiniciar na atualização do software do agente DC - este requisito é causado pelo comportamento do Windows.

A atualização do software pode ser automatizada `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`utilizando procedimentos mSI padrão, tais como .

Pode omitir `/norestart` a bandeira se preferir que o instalador reinicie automaticamente a máquina.

O `Get-AzureADPasswordProtectionDCAgent` cmdlet pode ser utilizado para consultar a versão de software de todos os agentes dc de proteção de senha seletivas do Azure AD atualmente instalados numa floresta.

## <a name="next-steps"></a>Passos seguintes

Agora que instalou os serviços de que necessita para a Proteção de Passwords Azure AD nos seus servidores no local, ative a proteção de [palavras-passe ad-ad-in-prem Azure no portal Azure](howto-password-ban-bad-on-premises-operations.md) para completar a sua implementação.
