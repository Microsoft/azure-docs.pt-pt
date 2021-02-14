---
title: Instale os agentes connect Health no Diretório Ativo Azure
description: Este artigo do Azure AD Connect Health descreve a instalação do agente para serviços da Federação de Diretório Ativo (AD FS) e para o Sync.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/20/2020
ms.topic: how-to
ms.author: billmath
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0e644b7937f6ccb23b4833405b8f4ed3119879a5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362289"
---
# <a name="azure-ad-connect-health-agent-installation"></a>Instalação do agente Azure AD Connect Health

Neste artigo, você vai aprender a instalar e configurar os agentes de ligação de Saúde Azure Ative (Azure AD). Para descarregar os agentes, consulte [estas instruções.](how-to-connect-install-roadmap.md#download-and-install-azure-ad-connect-health-agent)

## <a name="requirements"></a>Requisitos

As seguintes listas de tabelas exigem a utilização do Azure AD Connect Health.

| Requisito | Description |
| --- | --- |
| Existe uma subscipção Azure AD Premium (P1 ou P2).  |Azure AD Connect Health é uma característica do Azure AD Premium (P1 ou P2). Para mais informações, consulte [Inscreva-se no Azure AD Premium](../fundamentals/active-directory-get-started-premium.md). <br /><br />Para iniciar um julgamento gratuito de 30 dias, consulte [iniciar um julgamento.](https://azure.microsoft.com/trial/get-started-active-directory/) |
| És administrador global da Azure AD. |Por padrão, apenas os administradores globais podem instalar e configurar os agentes de saúde, aceder ao portal e fazer quaisquer operações dentro da Azure AD Connect Health. Para obter mais informações, veja [Administrar o diretório do Azure AD](../fundamentals/active-directory-whatis.md). <br /><br /> Ao utilizar o controlo de acesso baseado em funções Azure (Azure RBAC), pode permitir que outros utilizadores da sua organização acedam ao Azure AD Connect Health. Para obter mais informações, consulte [Azure RBAC para Azure AD Connect Health](how-to-connect-health-operations.md#manage-access-with-azure-rbac). <br /><br />**Importante**: Utilize uma conta de trabalho ou escola para instalar os agentes. Não podes usar uma conta da Microsoft. Para mais informações, consulte [Inscrever-se no Azure como uma organização.](../fundamentals/sign-up-organization.md) |
| O agente Azure AD Connect Health está instalado em cada servidor alvo. | Os agentes de saúde devem ser instalados e configurados em servidores direcionados para que possam receber dados e fornecer capacidades de monitorização e análise. <br /><br />Por exemplo, para obter dados da sua infraestrutura ative Directory Federation Services (AD FS), tem de instalar o agente no servidor AD FS e no servidor Proxy da Aplicação Web. Da mesma forma, para obter dados da infraestrutura Azure AD Domain Services (Azure AD DS), deve instalar o agente nos controladores de domínio.  |
| Os pontos finais de serviço Azure têm conectividade de saída. | Durante a instalação e o tempo de execução, o agente requer conectividade aos pontos finais do serviço do Azure AD Connect Health. Se as firewalls bloquearem a conectividade de saída, adicione os [pontos finais de conectividade de saída](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) à lista de autorizações. |
|A conectividade de saída baseia-se em endereços IP. | Para obter informações sobre a filtragem de firewall com base em endereços IP, consulte [as gamas IP do Azure](https://www.microsoft.com/download/details.aspx?id=41653).|
| A inspeção TLS para o tráfego de saída é filtrada ou desativada. | A etapa de registo do agente ou as operações de upload de dados podem falhar se houver inspeção ou rescisão de TLS para tráfego de saída na camada de rede. Para obter mais informações, consulte [configurar a inspeção TLS.](/previous-versions/tn-archive/ee796230(v=technet.10)) |
| As portas de firewall no servidor estão a executar o agente. |O agente exige que as seguintes portas de firewall estejam abertas para que possa comunicar com os pontos finais do serviço Azure AD Connect Health: <br /><li>Porta TCP 443</li><li>Porta TCP 5671</li> <br />A versão mais recente do agente não requer a porta 5671. Upgrade para a versão mais recente para que apenas a porta 443 seja necessária. Para mais informações, consulte [a identidade híbrida exigida por portas e protocolos.](./reference-connect-ports.md) |
| Se a segurança reforçada do Internet Explorer estiver ativada, permita websites especificados.  |Se a segurança reforçada do Internet Explorer estiver ativada, então permita os seguintes websites no servidor onde instala o agente:<br /><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com</li><li>https:\//login.windows.net</li><li>https: \/ /aadcdn.msftauth.net</li><li>O servidor da federação para a sua organização que é confiado pela Azure AD (por exemplo, https: \/ /sts.contoso.com)</li> <br />Para mais informações, consulte [Como configurar o Internet Explorer.](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing) Se tiver um representante na sua rede, consulte a nota que aparece no final desta tabela.|
| A versão 4.0 ou mais recente da PowerShell é instalada. | O Windows Server 2012 inclui a versão 3.0 do PowerShell. Esta versão *não* é suficiente para o agente.</br></br> O Windows Server 2012 R2 e mais tarde inclui uma versão suficientemente recente do PowerShell.|
|O FIPS (Padrão Federal de Processamento de Informação) está desativado.|Os agentes da Azure AD Connect Health não suportam FIPS.|

> [!IMPORTANT]
> O Windows Server Core não suporta a instalação do agente Azure AD Connect Health.


> [!NOTE]
> Se tiver um ambiente altamente bloqueado e restrito, precisa de adicionar mais URLs do que os que as listas de tabelas para o Internet Explorer reforçaram a segurança. Adicione também URLs listados na tabela na secção seguinte.  


### <a name="outbound-connectivity-to-the-azure-service-endpoints"></a>Conectividade de saída para os pontos finais de serviço do Azure

Durante a instalação e o tempo de funcionamento, o agente necessita de conectividade com os pontos finais do serviço Azure AD Connect Health. Se as firewalls bloquearem a conectividade de saída, certifique-se de que os URLs da tabela seguinte não estão bloqueados por defeito. 

Não desative a monitorização de segurança ou a inspeção destes URLs. Em vez disso, permita-os como permitiria outro tráfego de internet. 

Estes URLs permitem a comunicação com os pontos finais do serviço Azure AD Connect Health. Mais tarde neste artigo, você vai aprender a verificar a [conectividade de saída](#test-connectivity-to-azure-ad-connect-health-service) usando `Test-AzureADConnectHealthConnectivity` .

| Ambiente de domínio | Pontos finais do serviço do Azure necessários |
| --- | --- |
| Público em geral | <li>&#42;.blob.core.windows.net </li><li>&#42;.aadconnecthealth.azure.com </li><li>&#42;.servicebus.windows.net - Porto: 5671 (Este ponto final não é necessário na versão mais recente do agente.)</li><li>&#42;.adhybridhealth.azure.com/</li><li>https:\//management.azure.com </li><li>https:\//policykeyservice.dc.ad.msft.net/</li><li>https:\//login.windows.net</li><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https: \/ /www.office.com (Este ponto final é utilizado apenas para fins de descoberta durante o registo.)</li> <li>https://aadcdn.msftauth.net</li><li>https://aadcdn.msauth.net</li> |
| Azure Alemanha | <li>&#42;.blob.core.cloudapi.de </li><li>&#42;.servicebus.cloudapi.de </li> <li>&#42;.aadconnecthealth.microsoftazure.de </li><li>https:\//management.microsoftazure.de </li><li>https:\//policykeyservice.aadcdi.microsoftazure.de </li><li>https:\//login.microsoftonline.de </li><li>https:\//secure.aadcdn.microsoftonline-p.de </li><li>https: \/ /www.office.de (Este ponto final é utilizado apenas para fins de descoberta durante o registo.)</li> <li>https://aadcdn.msftauth.net</li><li>https://aadcdn.msauth.net</li> |
| Azure Government | <li>&#42;.blob.core.usgovcloudapi.net </li> <li>&#42;.servicebus.usgovcloudapi.net </li> <li>&#42;.aadconnecthealth.microsoftazure.us </li> <li>https:\//management.usgovcloudapi.net </li><li>https:\//policykeyservice.aadcdi.azure.us </li><li>https:\//login.microsoftonline.us </li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https: \/ /www.office.com (Este ponto final é utilizado apenas para fins de descoberta durante o registo.)</li> <li>https://aadcdn.msftauth.net</li><li>https://aadcdn.msauth.net</li> |


## <a name="install-the-agent"></a>Instalar o agente

Para descarregar e instalar o agente Azure AD Connect Health: 

* Certifique-se de que satisfaz os [requisitos](how-to-connect-health-agent-install.md#requirements) para a Azure AD Connect Health.
* Começa a usar a Azure AD Connect Health para AD FS:
    * [Descarregue o agente Azure AD Connect Health para AD FS](https://go.microsoft.com/fwlink/?LinkID=518973).
    * Consulte as [instruções de instalação](#install-the-agent-for-ad-fs).
* Começa a usar a Azure AD Connect Health para sincronização:
    * [Transferir e instalar a versão mais recente do Azure AD Connect](https://go.microsoft.com/fwlink/?linkid=615771). O agente de saúde para Sync é instalado como parte da instalação Azure AD Connect (versão 1.0.9125.0 ou posterior).
* Começa a usar a Azure AD Connect Health para Azure AD DS:
    * [Descarregue o agente Azure AD Connect Health para Azure AD DS](https://go.microsoft.com/fwlink/?LinkID=820540).
    * Consulte as [instruções de instalação](#install-the-agent-for-azure-ad-ds).

## <a name="install-the-agent-for-ad-fs"></a>Instale o agente para AD FS

> [!NOTE]
> O servidor AD FS deve ser diferente do seu servidor Sync. Não instale o agente AD FS no seu servidor Sync.
>

Antes de instalar o agente, certifique-se de que o nome do anfitrião do servidor AD FS é único e não está presente no serviço AD FS.
Para iniciar a instalação do agente, clique duas vezes no ficheiro *.exe* que descarregou. Na primeira janela, selecione **Instalar**.

![Screenshot mostrando a janela de instalação do agente Azure AD Connect Health AD FS.](./media/how-to-connect-health-agent-install/install1.png)

Depois de terminar a instalação, selecione **Configure Now**.

![Screenshot mostrando a mensagem de confirmação para a instalação do agente FS AD Health AD.](./media/how-to-connect-health-agent-install/install2.png)

Uma janela PowerShell abre-se para iniciar o processo de registo do agente. Quando for solicitado, inscreva-se utilizando uma conta AZure AD que tenha permissões para registar o agente. Por padrão, a conta de administração global tem permissões.

![Screenshot mostrando a janela de inscrição para Azure AD Connect Health AD FS.](./media/how-to-connect-health-agent-install/install3.png)

Depois de assinar, o PowerShell continua. Quando terminar, pode fechar o PowerShell. A configuração está completa.

Neste momento, os serviços do agente devem começar automaticamente para permitir que o agente carreque os dados necessários para o serviço na nuvem.

Se ainda não conheceste todos os pré-requisitos, aparecem avisos na janela PowerShell. Certifique-se de que preenche os [requisitos](how-to-connect-health-agent-install.md#requirements) antes de instalar o agente. A imagem que se segue mostra um exemplo destes avisos.

![Screenshot mostrando o Azure AD Connect Health AD FS configurar script.](./media/how-to-connect-health-agent-install/install4.png)

Para verificar se o agente foi instalado, procure os seguintes serviços no servidor. Se concluiu a configuração, já devem estar a ser executados. Caso contrário, são parados até que a configuração esteja completa.

* Serviço de Diagnóstico do Azure AD Connect Health AD FS
* Serviço de Informações do Azure AD Connect Health AD FS
* Serviço de Monitorização do Azure AD Connect Health AD FS

![Screenshot mostrando os serviços Azure AD Connect AD FS.](./media/how-to-connect-health-agent-install/install5.png)


### <a name="enable-auditing-for-ad-fs"></a>Permitir a auditoria do AD FS

> [!NOTE]
> Esta secção aplica-se apenas aos servidores AD FS. Não é necessário seguir estes passos nos servidores Proxy da Aplicação Web.
>

A funcionalidade Usage Analytics precisa de recolher e analisar dados. Assim, o agente Azure AD Connect Health precisa da informação nos registos de auditoria da AD FS. Estes registos não são ativados por defeito. Utilize os seguintes procedimentos para permitir a auditoria da AD FS e localizar os registos de auditoria do AD FS nos seus servidores AD FS.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Para ativar a auditoria para o AD FS no Windows Server 2012 R2

1. No ecrã Iniciar, abra o **Gestor do Servidor** e, em seguida, abra a Política de Segurança **Local**. Ou na barra de tarefas, abra o **Gestor do Servidor** e, em seguida, selecione **Ferramentas/Política de Segurança Local**.
2. Aceda às *Definições de Segurança\Políticas Locais\Pasta de Atribuição de Direitos de Utilizador.* Em seguida, clique duplo **Gerar auditorias de segurança.**
3. No separador **Definição de Segurança Local**, certifique-se de que a conta de serviço do AD FS está listada. Se não estiver listado, selecione **Adicionar Utilizador ou Grupo** e adicione-o à lista. Em seguida, selecione **OK**.
4. Para ativar a auditoria, abra uma janela de aviso de comando com privilégios elevados. Em seguida, execute o seguinte comando: 
    
    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. Feche a **Política de Segurança Local**.
    >[!Important]
    >Os seguintes passos são necessários apenas para servidores AD FS primários. 
1. Abra a snap-in da **Gestão do AD FS**. (No **Gestor do Servidor,** selecione **Ferramentas**  >  **Gestão de AD FS.)**
1. No painel **de ações,** selecione **Edit Federation Service Properties**.
1. Na caixa de diálogo **'Propriedades de Serviço da Federação',** selecione o separador **Eventos.**
1. Selecione as auditorias de **sucesso e as auditorias de falha** verificam as caixas e, em seguida, selecione **OK**.
1. Para permitir a tomada de registo verbose através do PowerShell, utilize o seguinte comando: 

    `Set-AdfsProperties -LOGLevel Verbose`

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>Para ativar a auditoria para o AD FS no Windows Server 2016

1. No ecrã Iniciar, abra o **Gestor do Servidor** e, em seguida, abra a Política de Segurança **Local**. Ou na barra de tarefas, abra o **Gestor do Servidor** e, em seguida, selecione **Ferramentas/Política de Segurança Local**.
2. Aceda às *Definições de Segurança\Políticas Locais\Pasta de Atribuição de Direitos de Utilizador* e, em seguida, clique duas vezes Em **Gerar auditorias** de segurança .
3. No separador **Definição de Segurança Local**, certifique-se de que a conta de serviço do AD FS está listada. Se não estiver listado, selecione **Add User ou Group** e adicione a conta de serviço AD FS à lista. Em seguida, selecione **OK**.
4. Para ativar a auditoria, abra uma janela de aviso de comando com privilégios elevados. Em seguida, execute o seguinte comando: 

    `auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable`
1. Feche a **Política de Segurança Local**.
    >[!Important]
    >Os seguintes passos são necessários apenas para servidores AD FS primários.
1. Abra a snap-in da **Gestão do AD FS**. (No **Gestor do Servidor,** selecione **Ferramentas**  >  **Gestão de AD FS.)**
1. No painel **de ações,** selecione **Edit Federation Service Properties**.
1. Na caixa de diálogo **'Propriedades de Serviço da Federação',** selecione o separador **Eventos.**
1. Selecione as auditorias de **sucesso e as auditorias de falha** verificam as caixas e, em seguida, selecione **OK**. As auditorias de sucesso e as auditorias de falhas devem ser ativadas por defeito.
1. Abra uma janela PowerShell e executar o seguinte comando: 

    `Set-AdfsProperties -AuditLevel Verbose`

O nível de auditoria "básico" é ativado por defeito. Para obter mais informações, consulte [a melhoria da auditoria da AD FS no Windows Server 2016](/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server).


#### <a name="to-locate-the-ad-fs-audit-logs"></a>Para localizar os registos de auditoria do AD FS

1. Abra o **Visualizador de Eventos**.
2. Vá a **Registos do Windows** e, em seguida, selecione **Security**.
3. À direita, selecione **Registos de Corrente de Filtro**.
4. Para **fontes de eventos**, selecione **AD FS Auditing**.

    Para obter mais informações sobre os registos de auditoria, consulte [as questões de Operações.](reference-connect-health-faq.md#operations-questions)

    ![Screenshot mostrando a janela de registo de corrente do filtro. No campo "Fontes de evento", é selecionada a "auditoria AD FS".](./media/how-to-connect-health-agent-install/adfsaudit.png)

> [!WARNING]
> Uma política de grupo pode desativar a auditoria do AD FS. Se a auditoria da AD FS for desativada, não há disponíveis análises de utilização sobre atividades de login. Certifique-se de que não tem nenhuma política de grupo que desative a auditoria da AD FS.
>


## <a name="install-the-agent-for-sync"></a>Instale o agente para sincronização

O agente Azure AD Connect Health para Sincronização é instalado automaticamente na versão mais recente do Azure AD Connect. Para utilizar o Azure AD Connect para Sincronização, [descarregue a versão mais recente do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) e instale-a.

Para verificar se o agente foi instalado, procure os serviços seguintes no servidor. Se tiver concluído a configuração, os serviços já deverão estar em execução. Caso contrário, os serviços são interrompidos até que a configuração esteja completa.

* Serviço de Informações do Azure AD Connect Health Sincronização
* Serviço de Monitorização do Azure AD Connect Health Sincronização

![Screenshot mostrando o funcionamento Azure AD Connect Health para serviços de Sincronização no servidor.](./media/how-to-connect-health-agent-install/services.png)

> [!NOTE]
> Lembre-se que deve ter Azure AD Premium (P1 ou P2) para utilizar a Azure AD Connect Health. Se não tiver Azure AD Premium, não pode completar a configuração no portal Azure. Para mais informações, consulte os [requisitos.](how-to-connect-health-agent-install.md#requirements)
>
>

## <a name="manually-register-azure-ad-connect-health-for-sync"></a>Registe manualmente Azure AD Connect Health para Sincronização

Se o registo de agente AZure AD Connect Health for Sync falhar depois de ter instalado com sucesso o Azure AD Connect, então pode utilizar um comando PowerShell para registar manualmente o agente.

> [!IMPORTANT]
> Utilize este comando PowerShell apenas se o registo do agente falhar depois de instalar o Azure AD Connect.
>
>

Registe manualmente o agente Azure AD Connect Health para sincronização utilizando o seguinte comando PowerShell. Quando o agente for registado com êxito, os serviços do Azure AD Connect Health são iniciados.

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

O comando recebe os seguintes parâmetros:

* **AtributoFiltering**: `$true` (predefinição) se o Azure AD Connect não sincronizar o conjunto de atributos predefinidos e tiver sido personalizado para utilizar um conjunto de atributos filtrados. Caso contrário, `$false` utilize.
* **StagingMode**: `$false` (predefinido) se o servidor Azure AD Connect *não* estiver no modo de preparação. Se o servidor estiver configurado para estar no modo de preparação, utilize `$true` -o .

Quando for solicitado para a autenticação, utilize a mesma conta de administração global admin@domain.onmicrosoft.com (como) que usou para configurar o Azure AD Connect.

## <a name="install-the-agent-for-azure-ad-ds"></a>Instale o agente para Azure AD DS

Para iniciar a instalação do agente, clique duas vezes no ficheiro *.exe* que descarregou. Na primeira janela, selecione **Instalar**.

![Screenshot mostrando o agente Azure AD Connect Health para a janela de instalação AD DS.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install1.png)

Quando a instalação terminar, selecione **Configure Now**.

![Screenshot mostrando a janela que termina a instalação do agente Azure AD Connect Health para Azure AD DS.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install2.png)

Abre-se uma janela de pedido de comando. PowerShell funciona `Register-AzureADConnectHealthADDSAgent` . Quando for solicitado, inscreva-se em Azure.

![Screenshot mostrando a janela de inscrição para o agente Azure AD Connect Health para Azure AD DS.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install3.png)

Depois de assinar, o PowerShell continua. Quando terminar, pode fechar o PowerShell. A configuração está completa.

Neste momento, os serviços devem ser iniciados automaticamente, permitindo ao agente monitorizar e recolher dados. Se ainda não encontrou todos os pré-requisitos descritos nas secções anteriores, então aparecem avisos na janela PowerShell. Certifique-se de que preenche os [requisitos](how-to-connect-health-agent-install.md#requirements) antes de instalar o agente. A imagem que se segue mostra um exemplo destes avisos.

![Screenshot mostrando um aviso para o agente Azure AD Connect Health para a configuração AD DS Azure.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install4.png)

Para verificar se o agente está instalado, procure os seguintes serviços no controlador de domínio:

* Serviço de Informações do Azure AD Connect Health AD DS
* Serviço de Monitorização do Azure AD Connect Health AD DS

Se concluiu a configuração, estes serviços já devem estar a ser executados. Caso contrário, são parados até que a configuração termine.

![Screenshot mostrando os serviços de execução no controlador de domínio.](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install5.png)

### <a name="quickly-install-the-agent-on-multiple-servers"></a>Instale rapidamente o agente em vários servidores

1. Crie uma conta de utilizador em Azure AD. Fixe-o utilizando uma senha.
2. Atribua a função **de Proprietário** para esta conta Azure AD local em Azure AD Connect Health utilizando o portal. Siga [estes passos.](how-to-connect-health-operations.md#manage-access-with-azure-rbac) Atribua o papel a todas as instâncias de serviço. 
3. Descarregue o ficheiro MSI *.exe* no controlador de domínio local para a instalação.
4. Execute o seguinte script. Substitua os parâmetros pela sua nova conta de utilizador e a sua palavra-passe. 

    ```powershell
    AdHealthAddsAgentSetup.exe /quiet
    Start-Sleep 30
    $userName = "NEWUSER@DOMAIN"
    $secpasswd = ConvertTo-SecureString "PASSWORD" -AsPlainText -Force
    $myCreds = New-Object System.Management.Automation.PSCredential ($userName, $secpasswd)
    import-module "C:\Program Files\Azure Ad Connect Health Adds Agent\PowerShell\AdHealthAdds"
     
    Register-AzureADConnectHealthADDSAgent -Credential $myCreds
    
    ```

Quando terminar, pode remover o acesso à conta local fazendo uma ou mais das seguintes tarefas: 
* Remova a atribuição de funções para a conta local para Azure AD Connect Health.
* Rode a palavra-passe para a conta local. 
* Desative a conta local da Azure AD.
* Apague a conta local Azure AD.  

## <a name="register-the-agent-by-using-powershell"></a>Registar o agente utilizando o PowerShell

Depois de instalar o agente apropriado *setup.exe* ficheiro, pode registar o agente utilizando os seguintes comandos PowerShell, dependendo da função. Abra uma janela PowerShell e executar o comando apropriado:

```powershell
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

> [!NOTE]
> Para se registar contra nuvens soberanas, utilize as seguintes linhas de comando:
>
> ```powershell
> Register-AzureADConnectHealthADFSAgent -UserPrincipalName upn-of-the-user
> Register-AzureADConnectHealthADDSAgent -UserPrincipalName upn-of-the-user
> Register-AzureADConnectHealthSyncAgent -UserPrincipalName upn-of-the-user
> ```
>


Estes comandos aceitam `Credential` como parâmetro para completar o registo não interativamente ou para completar o registo numa máquina que executa o Server Core. Tenha em mente que:
* Podes capturar `Credential` numa variável PowerShell que passou como parâmetro.
* Pode fornecer qualquer identidade AD Azure que tenha permissões para registar os agentes e que *não* tenha autenticação multifactor ativada.
* Por padrão, os administradores globais têm permissões para registar os agentes. Também pode permitir que identidades menos privilegiadas façam este passo. Para mais informações, consulte [a Azure RBAC.](how-to-connect-health-operations.md#manage-access-with-azure-rbac)

```powershell
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Configure Azure AD Connect Health agentes para usar http proxy

Pode configurar agentes da Azure AD Connect Health para trabalhar com um representante http.

> [!NOTE]
> * `Netsh WinHttp set ProxyServerAddress` não é apoiado. O agente utiliza System.Net em vez de Serviços HTTP do Windows para fazer pedidos web.
> * O endereço de procuração HTTP configurado é utilizado para transmitir mensagens HTTPS encriptadas.
> * Proxies autenticados (utilizando HTTPBasic) não são suportados.
>
>

### <a name="change-the-agent-proxy-configuration"></a>Alterar a configuração de procuração de agente

Para configurar o agente Azure AD Connect Health para utilizar um representante HTTP, pode:
* Importar as definições de procuração existentes.
* Especifique os endereços de procuração manualmente.
* Limpe a configuração de procuração existente.

> [!NOTE]
> Para atualizar as definições de procuração, tem de reiniciar todos os serviços do agente Azure AD Connect Health. Execute o seguinte comando:
>
> `Restart-Service AzureADConnectHealth*`

#### <a name="import-existing-proxy-settings"></a>Importar as definições de procuração existentes

Pode importar configurações de procuração do Internet Explorer HTTP para que os agentes Azure AD Connect Health possam utilizar as definições. Em cada um dos servidores que executam o agente de saúde, executar o seguinte comando PowerShell:

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings
```

Pode importar configurações de procuração WinHTTP para que os agentes Azure AD Connect Health possam usá-las. Em cada um dos servidores que executam o agente de saúde, executar o seguinte comando PowerShell:

```powershell
Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp
```

#### <a name="specify-proxy-addresses-manually"></a>Especifique endereços de procuração manualmente

Pode especificar manualmente um servidor proxy. Em cada um dos servidores que executam o agente de saúde, executar o seguinte comando PowerShell:

```powershell
Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port
```

Eis um exemplo: 

`Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver: 443`

Neste exemplo:
* A `address` definição pode ser um nome de servidor resoluçível de DNS ou um endereço IPv4.
* Pode `port` omitir. Se o fizer, então 443 é a porta predefinido.

#### <a name="clear-the-existing-proxy-configuration"></a>Limpe a configuração de procuração existente

Pode limpar a configuração de proxy existente ao executar o seguinte comando:

```powershell
Set-AzureAdConnectHealthProxySettings -NoProxy
```

### <a name="read-current-proxy-settings"></a>Ler as definições de proxy atuais

Pode ler as definições atuais de procuração executando o seguinte comando:

```powershell
Get-AzureAdConnectHealthProxySettings
```

## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Teste conectividade ao serviço Azure AD Connect Health

Ocasionalmente, o agente Azure AD Connect Health pode perder a conectividade com o serviço Azure AD Connect Health. As causas desta perda de conectividade podem incluir problemas de rede, problemas de permissão e vários outros problemas.

Se o agente não puder enviar dados para o serviço Azure AD Connect Health por mais de duas horas, o seguinte alerta aparece no portal: "Os dados do Serviço de Saúde não estão atualizados." 

Pode descobrir se o agente Azure AD Connect Health afetado pode enviar dados para o serviço Azure AD Connect Health executando o seguinte comando PowerShell:

```powershell
Test-AzureADConnectHealthConnectivity -Role ADFS
```

O parâmetro de função toma atualmente os seguintes valores:

* ADFS
* Sync
* AD DS

> [!NOTE]
> Para utilizar a ferramenta de conectividade, tem primeiro de registar o agente. Se não conseguir completar o registo do agente, certifique-se de que preencheu todos os [requisitos](how-to-connect-health-agent-install.md#requirements) para a Azure AD Connect Health. A conectividade é testada por padrão durante o registo do agente.
>
>

## <a name="next-steps"></a>Passos seguintes

Confira os seguintes artigos relacionados:

* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Operações de Saúde](how-to-connect-health-operations.md)
* [Utilizar o Azure AD Connect Health com o AD FS](how-to-connect-health-adfs.md)
* [Utilizar o Azure AD Connect Health para Sincronização](how-to-connect-health-sync.md)
* [Usando Azure AD Connect Health com Azure AD DS](how-to-connect-health-adds.md)
* [FAQ do Azure AD Connect Health](reference-connect-health-faq.md)
* [Histórico das versões do Azure AD Connect Health](reference-connect-health-version-history.md)
