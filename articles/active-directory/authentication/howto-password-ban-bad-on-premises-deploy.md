---
title: Implementar proteção de senha seletiva Azure - Diretório Ativo Azure
description: Implementar proteção de senha seletiva azure para proibir senhas erradas no local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a3eb121b68311084fd516c6abb7e00ad70eba8b
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226834"
---
# <a name="deploy-azure-ad-password-protection"></a>Implementar proteção de palavras-passe do Azure AD

Agora que compreende como impor a proteção de [palavras-passe Da AD Azure para o Diretório Ativo](concept-password-ban-bad-on-premises.md)do Windows Server, o próximo passo é planear e executar a sua implementação.

## <a name="deployment-strategy"></a>Estratégia de implantação

Recomendamos que inicie as implementações no modo de auditoria. O modo de auditoria é a definição inicial predefinida, onde as palavras-passe podem continuar a ser definidas. As palavras-passe que seriam bloqueadas são registadas no registo do evento. Depois de implementar os servidores proxy e agentes de DC no modo de auditoria, deve monitorizar o impacto que a política de passwords terá nos utilizadores e no ambiente quando a política for aplicada.

Durante a fase de auditoria, muitas organizações descobrem que:

* Precisam de melhorar os processos operacionais existentes para utilizarem senhas mais seguras.
* Os utilizadores usam frequentemente senhas não seguras.
* Precisam de informar os utilizadores sobre a próxima alteração na aplicação da segurança, possível impacto sobre eles e como escolher palavras-passe mais seguras.

Também é possível que uma validação mais forte da palavra-passe afete a automatização de implementação do controlador de domínio Ative Directory existente. Recomendamos que pelo menos uma promoção em DC e uma despromoção em DC ocorram durante a avaliação do período de auditoria, a fim de ajudar a desvendar antecipadamente tais questões.  Para obter mais informações, consulte:

* [Ntdsutil.exe é incapaz de definir uma senha fraca do Modo de Reparação de Serviços de Diretório](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [A promoção da réplica do controlador de domínio falha devido a uma fraca senha do Modo de Reparação de Serviços de Diretório](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [A despromoção do controlador de domínio falha devido a uma fraca senha do Administrador Local](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

Depois de a funcionalidade ter estado em execução no modo de auditoria durante um período razoável, pode mudar a configuração da *Auditoria* para *a Aplicação* para exigir senhas mais seguras. A monitorização focalizada durante este tempo é uma boa ideia.

## <a name="deployment-requirements"></a>Requisitos de implementação

* Os requisitos de licenciamento para a proteção de senhas Azure AD podem ser encontrados no artigo [Eliminar palavras-passe más na sua organização](concept-password-ban-bad.md#license-requirements).
* Todas as máquinas onde o software de proteção de senhas Azure AD será instalado devem executar o Windows Server 2012 ou mais tarde. Este requisito não implica que o domínio ou a floresta do Diretório Ativo também deva estar no domínio do Windows Server 2012 ou nível funcional florestal. Como mencionado nos princípios de [design,](concept-password-ban-bad-on-premises.md#design-principles)não existe um DFL ou FFL mínimo necessários para que o agente DC ou o software proxy sejam executados.
* Todas as máquinas que tenham o serviço de agente DC instalado devem ter .NET 4.5 instaladas.
* Todas as máquinas onde o serviço de proxy de proteção de senhas Azure AD será instalado devem executar o Windows Server 2012 R2 ou mais tarde.
   > [!NOTE]
   > A implementação do serviço Proxy é um requisito obrigatório para a implementação da proteção de senhas Azure AD, mesmo que o controlador de domínio possa ter conectividade direta de internet. 
   >
* Todas as máquinas onde o serviço de procuração de passwords Azure AD será instalado devem ter .NET 4.7 instaladas.
  .NET 4.7 já deve ser instalado num Servidor Windows totalmente atualizado. Se necessário, faça o download e execute o instalador encontrado no [instalador offline .NET Framework 4.7 para windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* Todas as máquinas, incluindo controladores de domínio, que tenham componentes de proteção de senhas Azure AD instalados devem ter o Tempo de Execução Universal C instalado. Pode obter o tempo de execução, certificando-se de que tem todas as atualizações a partir do Windows Update. Ou pode obtê-lo num pacote de atualização específico para o OS. Para mais informações, consulte [Atualização para o Tempo de Execução Universal C no Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* A conectividade da rede deve existir entre pelo menos um controlador de domínio em cada domínio e pelo menos um servidor que acolhe o serviço proxy para proteção de palavras-passe. Esta conectividade deve permitir ao controlador de domínio aceder à porta de mapa de ponto final RPC 135 e à porta do servidor RPC no serviço proxy. Por predefinição, a porta do servidor RPC é uma porta RPC dinâmica, mas pode ser configurada para [utilizar uma porta estática](#static).
* Todas as máquinas onde o serviço de procuração de passwords Azure AD será instalado devem ter acesso à rede aos seguintes pontos finais:

    |**Ponto final**|**Objetivo**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Pedidos de autenticação|
    |`https://enterpriseregistration.windows.net`|Funcionalidade de proteção de senha seletiva Azure AD|
 
* Pré-requisitos do AD Connect Agent da Microsoft Azure

  O serviço de atualização do Agente de Ligação AD Microsoft Azure é instalado lado a lado com o serviço de proxy de proteção de passwords Azure AD. É necessária uma configuração adicional para que o serviço de atualização do Agente de Ligação AD da Microsoft Azure seja capaz de funcionar:

  Se o seu ambiente utilizar um servidor http proxy, deve seguir as diretrizes especificadas no [Trabalho com servidores proxy existentes no local](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers).

  O serviço de atualização do Agente de Ligação AD Microsoft Azure também requer as etapas TLS 1.2 especificadas nos [requisitos do TLS](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#tls-requirements).

  O acesso à rede deve ser ativado para o conjunto de portas e urls especificados nos procedimentos de configuração do ambiente proxy de [aplicação](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment).

  > [!WARNING]
  > O Proxy de Proteção de Passwords Azure AD e o Proxy de Aplicação instalam diferentes versões do serviço de atualização do agente de ligação addato do Microsoft Azure, razão pela qual as instruções se referem ao conteúdo proxy da aplicação. Estas diferentes versões são incompatíveis quando instaladas lado a lado, pelo que não é aconselhável instalar o Proxy de Proteção de Passwords Azure AD e proxy de aplicação lado a lado na mesma máquina.

* Todas as máquinas que acolhem o serviço proxy para proteção por palavra-passe devem ser configuradas para conceder aos controladores de domínio a capacidade de iniciar sessão no serviço proxy. Esta capacidade é controlada através da atribuição de privilégios "Access this computer from the network".
* Todas as máquinas que acolhem o serviço proxy para proteção por palavra-passe devem ser configuradas para permitir o tráfego de saída TLS 1.2 HTTP.
* Uma conta de Administrador Global para registar o serviço proxy para proteção de passwords e floresta com Azure AD.
* Uma conta que tem privilégios de administrador de domínio ative diretório no domínio da raiz da floresta para registar a floresta de Diretório Ativo do Windows Server com a AD Azure.
* Qualquer domínio de Diretório Ativo que execute o software de serviço do Agente DC deve utilizar a replicação do sistema de ficheiros distribuídos (DFSR) para a replicação sysvol.

  Se o seu domínio ainda não estiver a utilizar o DFSR, deve migrar para utilizar o DFSR antes de instalar a Proteção de Passwords Azure AD. Para mais informações, consulte o seguinte link:

  [Guia de migração de replicação SYSVOL: FRS para replicação DFS](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

  > [!WARNING]
  > O software Azure AD Password Protection DC Agent irá atualmente instalar em controladores de domínio em domínios que ainda estão a usar FRS (a tecnologia antecessora para DFSR) para replicação sysvol, mas o software NÃO funcionará corretamente neste ambiente. Efeitos colaterais negativos adicionais incluem ficheiros individuais que não se replicam, e os procedimentos de restauro sysvol parecem ter sucesso, mas falhando silenciosamente em replicar todos os ficheiros. Deve migrar o seu domínio para utilizar o DFSR o mais rapidamente possível, tanto para os benefícios inerentes da DFSR como para desbloquear a implementação da Proteção de Passwords Azure AD. As futuras versões do software serão automaticamente desativadas quando estiverem a funcionar num domínio que ainda está a utilizar frs.

* O Serviço de Distribuição de Chaves deve ser ativado em todos os controladores de domínio do domínio que executam o Windows Server 2012. Por predefinição, este serviço é ativado através do arranque manual do gatilho.

## <a name="single-forest-deployment"></a>Implantação de uma única floresta

O diagrama seguinte mostra como os componentes básicos da proteção da palavra-passe Azure AD funcionam em conjunto num ambiente de Diretório Ativo no local.

![Como os componentes de proteção de senha seletiva Azure funcionam em conjunto](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

É uma boa ideia rever como o software funciona antes de o implementares. Consulte [a visão geral conceptual da proteção de senhas Azure AD](concept-password-ban-bad-on-premises.md).

### <a name="download-the-software"></a>Descarregue o software

Existem dois instaladores necessários para a proteção de senhas Azure AD. Estão disponíveis no [Microsoft Download Center.](https://www.microsoft.com/download/details.aspx?id=57071)

### <a name="install-and-configure-the-proxy-service-for-password-protection"></a>Instale e configure o serviço proxy para proteção de passwords

1. Escolha um ou mais servidores para hospedar o serviço proxy para proteção de palavras-passe.
   * Cada serviço deste tipo só pode fornecer políticas de senha para uma única floresta. A máquina hospedeira deve ser unida a um domínio naquela floresta. Os domínios raiz e infantil são ambos apoiados. Precisa de conectividade de rede entre pelo menos um DC em cada domínio da floresta e a máquina de proteção de senhas.
   * Pode executar o serviço proxy num controlador de domínio para ser testado. Mas esse controlador de domínio requer então conectividade de internet, o que pode ser uma preocupação de segurança. Recomendamos esta configuração apenas para testes.
   * Recomendamos pelo menos dois servidores proxy para despedimento. Ver [Alta disponibilidade](howto-password-ban-bad-on-premises-deploy.md#high-availability).
   * Não é suportado para executar o serviço proxy em um controlador de domínio apenas de leitura.

1. Instale o serviço de proxy de proteção de passwords Azure AD utilizando o instalador de software `AzureADPasswordProtectionProxySetup.exe`.
   * A instalação do software não requer um reboot. A instalação do software pode ser automatizada utilizando procedimentos mSI padrão, por exemplo:

      `AzureADPasswordProtectionProxySetup.exe /quiet`

      > [!NOTE]
      > O serviço Windows Firewall deve estar a funcionar antes de instalar o pacote AzureADPasswordProtectionProxySetup.msi para evitar um erro de instalação. Se o Windows Firewall estiver configurado para não funcionar, a suposição é ativar e executar temporariamente o serviço Firewall durante a instalação. O software proxy não tem nenhuma dependência específica do Windows Firewall após a instalação. Se estiver a utilizar uma firewall de terceiros, deve ainda ser configurada para satisfazer os requisitos de implementação. Estes incluem permitir o acesso de entrada à porta 135 e a porta de servidor proxy RPC. Consulte [os requisitos de implantação](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements).

1. Abra uma janela PowerShell como administrador.
   * O software proxy de proteção de passwords inclui um novo módulo PowerShell, *AzureADPasswordProtection*. Os passos seguintes são executados vários cmdlets deste módulo PowerShell. Importar o novo módulo da seguinte forma:

      ```powershell
      Import-Module AzureADPasswordProtection
      ```

   * Para verificar se o serviço está em funcionamento, utilize o seguinte comando PowerShell:

      `Get-Service AzureADPasswordProtectionProxy | fl`.

     O resultado deve mostrar um **estado** de "Corrida".

1. Registe o representante.
   * Após a conclusão do passo 3, o serviço proxy está em funcionamento na máquina, mas ainda não tem as credenciais necessárias para comunicar com a Azure AD. É necessária a inscrição com a AD Azure:

     `Register-AzureADPasswordProtectionProxy`

     Este cmdlet requer credenciais de administrador global para o seu inquilino Azure. Você também precisa de privilégios de administrador de domínio de diretório ativo no domínio da raiz da floresta. Este cmdlet também deve ser executado utilizando uma conta com privilégios de administrador local.

     Depois deste comando ter sucesso uma vez para um serviço de procuração, as invocações adicionais dele terão sucesso, mas são desnecessárias.

      O `Register-AzureADPasswordProtectionProxy` cmdlet suporta os seguintes três modos de autenticação. Os dois primeiros modos suportam a autenticação Azure Multi-Factor, mas o terceiro modo não. Consulte os comentários abaixo para mais detalhes.

     * Modo de autenticação interativa:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Este modo não funciona nos sistemas operativos Server Core. Em vez disso, utilize um dos seguintes modos de autenticação. Além disso, este modo pode falhar se a configuração de segurança melhorada do Internet Explorer estiver ativada. A suposição é desativar essa Configuração, registar o proxy e, em seguida, reativa-lo.

     * Modo de autenticação do código do dispositivo:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Em seguida, completa a autenticação seguindo as instruções visualizadas num dispositivo diferente.

     * Modo de autenticação silencioso (baseado em palavra-passe):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Este modo falha se a autenticação de multifactor do Azure for necessária para a sua conta. Nesse caso, utilize um dos dois modos de autenticação anteriores ou utilize uma conta diferente que não exija MFA.
        >
        > Também pode ver o MFA necessário se o Registo de Dispositivos Azure (que é utilizado sob as capas pela Azure AD Password Protection) tiver sido configurado para exigir globalmente mFA. Para contornar esta situação poderá utilizar uma conta diferente que suporta o MFA com um dos dois modos de autenticação anteriores, ou poderá também relaxar temporariamente o requisito de MFA de Registo de Dispositivos Azure. Para isso, vá ao portal de gestão Azure, depois vá ao Diretório Ativo Azure, depois dispositivos, depois Definições de Dispositivos, em seguida, definir "Requerer multi-factor Auth para unir dispositivos" para No. Certifique-se de reconfigurar esta definição para Sim uma vez que o registo esteja completo.
        >
        > Recomendamos que os requisitos de MFA sejam ignorados apenas para efeitos de teste.

       Atualmente não é preciso especificar o parâmetro *-ForestCredential,* que está reservado para futuras funcionalidades.

   O registo do serviço proxy para proteção de senhas só é necessário uma vez na vida útil do serviço. Depois disso, o serviço proxy realizará automaticamente qualquer outra manutenção necessária.

   > [!TIP]
   > Pode haver um atraso notável antes de terminar a primeira vez que este cmdlet é executado para um inquilino Azure específico. A menos que uma falha seja reportada, não se preocupe com este atraso.

1. Registe a floresta.
   * Inicialize a floresta de Diretório Ativo no local com as credenciais necessárias para comunicar com o Azure utilizando o `Register-AzureADPasswordProtectionForest` cmdlet PowerShell.

      O cmdlet requer credenciais de administrador global para o seu inquilino Azure.  Também deve executar este cmdlet usando uma conta com privilégios de administrador local. Também requer privilégios de administrador de empresa de diretório ativo no local. Este passo é executado uma vez por floresta.

      O `Register-AzureADPasswordProtectionForest` cmdlet suporta os seguintes três modos de autenticação. Os dois primeiros modos suportam a autenticação Azure Multi-Factor, mas o terceiro modo não. Consulte os comentários abaixo para mais detalhes.

     * Modo de autenticação interativa:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Este modo não funcionará nos sistemas operativos Server Core. Em vez disso, utilize um dos dois modos de autenticação seguintes. Além disso, este modo pode falhar se a configuração de segurança melhorada do Internet Explorer estiver ativada. A suposição é desativar essa Configuração, registar a floresta e, em seguida, reativa-la.  

     * Modo de autenticação do código do dispositivo:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Em seguida, completa a autenticação seguindo as instruções visualizadas num dispositivo diferente.

     * Modo de autenticação silencioso (baseado em palavra-passe):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Este modo falha se a autenticação de multifactor do Azure for necessária para a sua conta. Nesse caso, utilize um dos dois modos de autenticação anteriores ou utilize uma conta diferente que não exija MFA.
        >
        > Também pode ver o MFA necessário se o Registo de Dispositivos Azure (que é utilizado sob as capas pela Azure AD Password Protection) tiver sido configurado para exigir globalmente mFA. Para contornar esta situação poderá utilizar uma conta diferente que suporta o MFA com um dos dois modos de autenticação anteriores, ou poderá também relaxar temporariamente o requisito de MFA de Registo de Dispositivos Azure. Para isso, vá ao portal de gestão Azure, depois vá ao Diretório Ativo Azure, depois dispositivos, depois Definições de Dispositivos, em seguida, definir "Requerer multi-factor Auth para unir dispositivos" para No. Certifique-se de reconfigurar esta definição para Sim uma vez que o registo esteja completo.
        >
        > Recomendamos que os requisitos de MFA sejam ignorados apenas para efeitos de teste.

       Estes exemplos só têm sucesso se o utilizador atualmente assinado for também um administrador de domínio de Diretório Ativo para o domínio raiz. Se não for esse o caso, pode fornecer credenciais de domínio alternativas através do parâmetro *-ForestCredential.*

   > [!NOTE]
   > Se vários servidores proxy estiverem instalados no seu ambiente, não importa qual servidor proxy que usa para registar a floresta.
   >
   > [!TIP]
   > Pode haver um atraso notável antes de terminar a primeira vez que este cmdlet é executado para um inquilino Azure específico. A menos que uma falha seja reportada, não se preocupe com este atraso.

   O registo da floresta de Diretório Ativo só é necessário uma vez na vida da floresta. Depois disso, os agentes controladores de domínio na floresta realizarão automaticamente qualquer outra manutenção necessária. Depois de `Register-AzureADPasswordProtectionForest` corre com sucesso para uma floresta, as invocações adicionais do cmdlet têm sucesso, mas são desnecessárias.

   Para `Register-AzureADPasswordProtectionForest` para ter sucesso, pelo menos um controlador de domínio que executa o Windows Server 2012 ou mais tarde deve estar disponível no domínio do servidor proxy. O software dc agent não tem de ser instalado em quaisquer controladores de domínio antes deste passo.

1. Configure o serviço proxy para a proteção de palavras-passe para comunicar através de um proxy HTTP.

   Se o seu ambiente necessitar da utilização de um proxy HTTP específico para comunicar com o Azure, utilize este método: Crie um ficheiro *AzureADPasswordProtectionProxy.exe.config* na pasta %ProgramFiles%\Azure AD Password Protection Proxy\Service. Incluir os seguintes conteúdos:

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

   Em ambos os casos, substitua `http://yourhttpproxy.com:8080` pelo endereço e porta do seu servidor proxy HTTP específico.

   Se o seu representante HTTP estiver configurado para utilizar uma política de autorização, deve conceder acesso à conta de computador do Diretório Ativo da máquina que acolhe o serviço proxy para proteção de palavras-passe.

   Recomendamos que pare e reinicie o serviço proxy depois de criar ou atualizar o ficheiro *AzureADPasswordProtectionProxy.exe.cofig.*

   O serviço proxy não suporta o uso de credenciais específicas para a ligação a um proxy HTTP.

1. Opcional: Configure o serviço proxy para a proteção da palavra-passe para ouvir numa porta específica.
   * O software dc agent para proteção de palavras-passe nos controladores de domínio utiliza RPC sobre TCP para comunicar com o serviço proxy. Por predefinição, o serviço proxy ouve qualquer ponto final dinâmico rPC disponível. Pode configurar o serviço para ouvir uma porta TCP específica, se necessário devido aos requisitos de topoologia de rede ou firewall no seu ambiente.
      * <a id="static" /></a>Para configurar o serviço para funcionar sob uma porta estática, utilize o `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet.

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Tem de parar e reiniciar o serviço para que estas alterações entrem em vigor.

      * Para configurar o serviço para funcionar sob uma porta dinâmica, utilize o mesmo procedimento, mas volte a colocar *a StaticPort* a zero:

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Tem de parar e reiniciar o serviço para que estas alterações entrem em vigor.

   > [!NOTE]
   > O serviço proxy para proteção de palavras-passe requer um reinício manual após qualquer alteração na configuração da porta. Mas não é preciso reiniciar o software de serviço do Agente DC nos controladores de domínio depois de fazer estas alterações de configuração.

   * Para consultar a configuração atual do serviço, utilize o `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet:

      ```powershell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-dc-agent-service"></a>Instale o serviço de Agente DC

   Instale o serviço DC Agent para proteção de senhas utilizando o pacote `AzureADPasswordProtectionDCAgentSetup.msi`.

   A instalação do software, ou desinstalação, requer um reinício. Este requisito deve-se ao dever-se ao dLLs do filtro de senha somente carregado soro ou descarregado por um reinício.

   Pode instalar o serviço de agente DC numa máquina que ainda não é controlador de domínio. Neste caso, o serviço começará e funcionará, mas permanecerá inativo até que a máquina seja promovida para ser um controlador de domínio.

   Pode automatizar a instalação do software utilizando procedimentos MSI padrão. Por exemplo:

   `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`

   Pode omitir a bandeira `/norestart` se preferir que o instalador reinicie automaticamente a máquina.

A instalação fica completa após a instalação do software DC Agent num controlador de domínio e este computador é reiniciado. Não é necessária qualquer outra configuração ou possível.

## <a name="upgrading-the-proxy-agent"></a>Modernização do agente Proxy

Quando uma versão mais recente do software Proxy de Proteção de Passwords Azure AD está disponível, a atualização é realizada executando a versão mais recente do instalador de software `AzureADPasswordProtectionProxySetup.exe`. A versão mais recente do software está disponível no [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

Não é necessário desinstalar a versão atual do software Proxy - o instalador irá realizar uma atualização no local. Não deve ser necessário reiniciar durante a atualização do software Proxy. A atualização do software pode ser automatizada utilizando procedimentos mSI padrão, por exemplo: `AzureADPasswordProtectionProxySetup.exe /quiet`.

O agente Proxy suporta a atualização automática. A atualização automática utiliza o serviço de atualização do Agente de Ligação AD Microsoft Azure, que é instalado lado a lado com o serviço Proxy. A atualização automática está por defeito e pode ser ativada ou desativada utilizando o `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet. A regulação atual pode ser consultada utilizando o `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet. A Microsoft recomenda que a definição de atualização automática esteja sempre ativada.

O `Get-AzureADPasswordProtectionProxy` cmdlet pode ser usado para consultar a versão de software de todos os agentes Proxy atualmente instalados numa floresta.

## <a name="upgrading-the-dc-agent"></a>Modernização do agente dc

Quando uma versão mais recente do software Azure AD Password Protection DC Agent está disponível, a atualização é realizada executando a versão mais recente do pacote de software `AzureADPasswordProtectionDCAgentSetup.msi`. A versão mais recente do software está disponível no [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

Não é necessário desinstalar a versão atual do software do agente DC - o instalador irá realizar uma atualização no local. É sempre necessário reiniciar na atualização do software do agente DC - este requisito é causado pelo comportamento do Windows. 

A atualização do software pode ser automatizada utilizando procedimentos mSI padrão, por exemplo: `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`.

Pode omitir a bandeira `/norestart` se preferir que o instalador reinicie automaticamente a máquina.

O `Get-AzureADPasswordProtectionDCAgent` cmdlet pode ser usado para consultar a versão de software de todos os agentes DC atualmente instalados numa floresta.

## <a name="multiple-forest-deployments"></a>Múltiplas implantações florestais

Não existem requisitos adicionais para implementar a proteção de senhas Azure AD em várias florestas. Cada floresta é configurada independentemente como descrito na secção "Implantação de uma única floresta". Cada representante de proteção de senhas só pode suportar controladores de domínio da floresta a que está unida. O software de proteção de passwords em qualquer floresta desconhece o software de proteção de passwords que é implantado noutras florestas, independentemente das configurações de confiança do Ative Directory.

## <a name="read-only-domain-controllers"></a>Controladores de domínio apenas de leitura

As alterações/conjuntos de palavra-passe não são processadas e persistem nos controladores de domínio apenas de leitura (RODCs). São encaminhados para controladores de domínio supreendáveis. Então, não tens de instalar o software do Agente DC nos RODCs.

Não é suportado para executar o serviço proxy em um controlador de domínio apenas de leitura.

## <a name="high-availability"></a>Elevada disponibilidade

A principal preocupação de disponibilidade para a proteção de passwords é a disponibilidade de servidores proxy quando os controladores de domínio numa floresta tentam descarregar novas políticas ou outros dados do Azure. Cada agente dc usa um algoritmo simples de estilo robin redondo ao decidir qual servidor proxy ligar. O Agente ignora servidores de procuração que não estão a responder. Para as implementações de Diretório Ativo mais conectadas que têm replicação saudável tanto do estado de pastagem de diretório como de sysvol, dois servidores proxy são suficientes para garantir a disponibilidade. Isto resulta no download atempada de novas políticas e outros dados. Mas pode implementar servidores adicionais de procuração.

O design do software dc agent atenua os problemas habituais que estão associados com alta disponibilidade. O Agente DC mantém uma cache local da política de senhamais recentemente descarregada. Mesmo que todos os servidores proxy registados fiquem indisponíveis, os Agentes DC continuam a aplicar a sua política de senha seleto. Uma frequência de atualização razoável para as políticas de senha numa grande implementação é geralmente dias, não horas ou menos. Assim, breves interrupções dos servidores proxy não impactam significativamente a proteção de senhas da AD Azure.

## <a name="next-steps"></a>Passos seguintes

Agora que instalou os serviços de que necessita para a proteção de passwords Azure AD nos seus servidores no local, execute a [configuração pós-instalação e recolha informações](howto-password-ban-bad-on-premises-operations.md) de reporte para completar a sua implementação.

[Visão geral conceptual da proteção de senhas azure AD](concept-password-ban-bad-on-premises.md)
