---
title: Instalação do agente do Azure AD Connect Health | Microsoft Docs
description: Esta é a página do Azure AD Connect Health que descreve a instalação do agente para AD FS e sincronização.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 1cc8ae90-607d-4925-9c30-6770a4bd1b4e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d6e453819ad749972de89658fa695d803d8e222
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57898827"
---
# <a name="azure-ad-connect-health-agent-installation"></a>Instalação do Agente do Azure AD Connect Health
Este documento descreve a instalação e a configuração dos Agentes do Azure AD Connect Health. Pode transferir os agentes de [aqui](how-to-connect-install-roadmap.md#download-and-install-azure-ad-connect-health-agent).

## <a name="requirements"></a>Requisitos
A tabela seguinte é uma lista de requisitos para utilizar o Azure AD Connect Health.

| Requisito | Descrição |
| --- | --- |
| Azure AD Premium |O Azure AD Connect Health é uma funcionalidade do Azure AD Premium e requer o Azure AD Premium. <br /><br />Para obter mais informações, veja [ Introdução ao Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) <br />Para iniciar uma versão de avaliação gratuita de 30 dias, veja [Iniciar uma versão de avaliação.](https://azure.microsoft.com/trial/get-started-active-directory/) |
| Tem de ser um administrador global do seu Azure AD para começar a utilizar o Azure AD Connect Health |Por predefinição, apenas os administradores globais podem instalar e configurar os agentes de estado de funcionamento para iniciar, aceder ao portal e executar operações no Azure AD Connect Health. Para obter mais informações, veja [Administrar o diretório do Azure AD](../fundamentals/active-directory-administer.md). <br /><br /> Utilizando um Controlo de Acesso Baseado em Funções, pode permitir o acesso ao Azure AD Connect Health a outros utilizadores na sua organização. Para obter mais informações, veja [Role Based Access Control for Azure AD Connect Health (Controlo de Acesso Baseado em Funções para o Azure AD Connect Health).](how-to-connect-health-operations.md#manage-access-with-role-based-access-control) <br /><br />**Importante:** A conta utilizada ao instalar os agentes tem de ser uma conta escolar ou profissional. Não pode ser uma conta Microsoft. Para obter mais informações, veja [Sign up for Azure as an organization (Inscrever-se no Azure como uma organização)](../fundamentals/sign-up-organization.md) |
| O Agente do Azure AD Connect Health é instalado em cada servidor de destino | O Azure AD Connect Health exige que os Agentes de Estado de Funcionamento sejam instalados e configurados em servidores de destino para receber os dados e oferecer as funcionalidades Monitorização e Análise. <br /><br />Por exemplo, para obter dados da sua infraestrutura do AD FS, o agente tem de estar instalado nos servidores do AD FS e nos servidores Proxy de Aplicações Web. Do mesmo modo, para obter os dados na sua infraestrutura local do AD DS, o agente tem de ser instalado nos controladores de domínio. <br /><br /> |
| Conectividade de saída para os pontos finais de serviço do Azure | Durante a instalação e o tempo de execução, o agente requer conectividade aos pontos finais do serviço do Azure AD Connect Health. Se a conectividade de saída for bloqueada com Firewalls, certifique-se de que os pontos finais seguintes são adicionados à lista de permissões. Veja [Pontos finais de conectividade de saída](how-to-connect-health-agent-install.md#outbound-connectivity-to-the-azure-service-endpoints) |
|Conectividade de saída com base em Endereços IP | Para filtragem baseada em endereço IP nas firewalls, consulte [Azure IP Ranges (Intervalos IP do Azure IP)](https://www.microsoft.com/download/details.aspx?id=41653).|
| A inspeção do SSL para o tráfego de saída é filtrada ou está desativada | As operações de carregamento de dados ou o passo de registo do agente poderá falhar se existir inspeção ou terminação SSL para tráfego de saída na camada de rede. Saiba mais sobre [como configurar a inspeção SSL](https://technet.microsoft.com/library/ee796230.aspx) |
| Portas da firewall no servidor onde o agente é executado |O agente requer que as seguintes portas de firewall estejam abertas para que possa comunicar com os pontos finais de serviço do Azure AD Health.<br /><br /><li>Porta TCP 443</li><li>Porta TCP 5671</li> <br />Observe que a porta 5671 já não é necessária para a versão mais recente do agente. Atualizar para a versão mais recente, pelo que apenas a porta 443 é necessária. Leia mais sobre como [ativar as portas da firewall](https://technet.microsoft.com/library/ms345310(v=sql.100).aspx) |
| Permitir os seguintes Web sites, caso esteja ativada a Segurança Avançada do IE |Se a Segurança Avançada do IE estiver ativada, os Web sites seguintes têm de ser permitidos no servidor no qual o agente vai ser instalado.<br /><br /><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com</li><li>https:\//login.windows.net</li><li>O servidor de federação para a sua organização considerado fidedigno pelo o Azure Active Directory. Por exemplo: https:\//sts.contoso.com</li> Leia mais sobre [como configurar o IE](https://support.microsoft.com/help/815141/internet-explorer-enhanced-security-configuration-changes-the-browsing) |
| Certifique-se de que o PowerShell v4.0 ou mais recente está instalado | <li>O Windows Server 2008 R2 inclui o PowerShell v2.0, que é insuficiente para o agente. Atualize o PowerShell, conforme explicado abaixo em [Agent installation on Windows Server 2008 R2 Servers (Instalação do agente em Servidores do Windows Server 2008 R2)](#agent-installation-on-windows-server-2008-r2-servers).</li><li>O Windows Server 2012 inclui o PowerShell v3.0, que é insuficiente para o agente.  [Atualize](https://www.microsoft.com/download/details.aspx?id=40855) o Windows Management Framework.</li><li>O Windows Server 2012 R2 e versão posterior incluem uma versão suficientemente recente do PowerShell.</li>|
|Desativar FIPS|O FIPS não é suportado por agentes do Azure AD Connect Health.|

### <a name="outbound-connectivity-to-the-azure-service-endpoints"></a>Conectividade de saída para os pontos finais de serviço do Azure
 Durante a instalação e o tempo de execução, o agente requer conectividade aos pontos finais do serviço do Azure AD Connect Health. Se a conectividade de saída for bloqueada com Firewalls, certifique-se de que os seguintes URLs não estão bloqueados por predefinição. Não desative a monitorização de segurança ou de inspeção de URLs, mas permitir que os mesmos como faria com outro tráfego de internet. Eles permitem a comunicação com pontos finais de serviço do Azure AD Connect Health. Leia mais sobre como [verificar a conectividade de saída](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)

| Ambiente de Domínio | Pontos finais do serviço do Azure necessários |
| --- | --- |
| Público Geral | <li>&#42;.blob.core.windows.net </li><li>&#42;.aadconnecthealth.azure.com </li><li>&#42;.servicebus.windows.net - Port: 5671 </li><li>&#42;.adhybridhealth.azure.com/</li><li>https:\//management.azure.com </li><li>https:\//policykeyservice.dc.ad.msft.net/</li><li>https:\//login.windows.net</li><li>https:\//login.microsoftonline.com</li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https:\//www.office.com *este ponto final serve apenas para efeitos de deteção durante o registo.</li> |
| Azure Alemanha | <li>&#42;.blob.core.cloudapi.de </li><li>&#42;.servicebus.cloudapi.de </li> <li>&#42;.aadconnecthealth.microsoftazure.de </li><li>https:\//management.microsoftazure.de </li><li>https:\//policykeyservice.aadcdi.microsoftazure.de </li><li>https:\//login.microsoftonline.de </li><li>https:\//secure.aadcdn.microsoftonline-p.de </li><li>https:\//www.office.de *este ponto final serve apenas para efeitos de deteção durante o registo.</li> |
| Azure Government | <li>&#42;.blob.core.usgovcloudapi.net </li> <li>&#42;.servicebus.usgovcloudapi.net </li> <li>&#42;.aadconnecthealth.microsoftazure.us </li> <li>https:\//management.usgovcloudapi.net </li><li>https:\//policykeyservice.aadcdi.azure.us </li><li>https:\//login.microsoftonline.us </li><li>https:\//secure.aadcdn.microsoftonline-p.com </li><li>https:\//www.office.com *este ponto final serve apenas para efeitos de deteção durante o registo.</li> |


## <a name="download-and-install-the-azure-ad-connect-health-agent"></a>Transferir e instalar o Agente do Azure AD Connect Health
* Certifique-se de que [cumpre os requisitos](how-to-connect-health-agent-install.md#requirements) para o Azure AD Connect Health.
* Introdução ao Azure AD Connect Health para AD FS
    * [Transferir o Agente do Azure AD Connect Health para o AD FS.](https://go.microsoft.com/fwlink/?LinkID=518973)
    * [Veja a instruções de instalação](#installing-the-azure-ad-connect-health-agent-for-ad-fs).
* Introdução ao Azure AD Connect Health para sincronização
    * [Transferir e instalar a versão mais recente do Azure AD Connect](https://go.microsoft.com/fwlink/?linkid=615771). O Agente de Estado de Funcionamento para sincronização será instalado como parte da instalação do Azure AD Connect (versão 1.0.9125.0 ou superior).
* Introdução ao Azure AD Connect Health para AD DS
    * [Transfira o Agente do Azure AD Connect Health para AD DS](https://go.microsoft.com/fwlink/?LinkID=820540).
    * [Veja a instruções de instalação](#installing-the-azure-ad-connect-health-agent-for-ad-ds).

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-fs"></a>Instalar o agente do Azure AD Connect Health para o AD FS
> [!NOTE]
> O servidor do AD FS deve ser diferente do seu servidor de Sincronização. Não instale o agente do AD FS para o seu Servidor de sincronização.
>

Antes da instalação, certifique-se de que o nome de anfitrião do servidor do AD FS é exclusivo e não está presente no serviço do AD FS.
Para iniciar a instalação do agente, faça duplo clique no ficheiro .exe que transferiu. No primeiro ecrã, clique em Instalar.

![Verificar o Azure AD Connect Health](./media/how-to-connect-health-agent-install/install1.png)

Uma vez concluída a instalação, clique em Configurar Agora.

![Verificar o Azure AD Connect Health](./media/how-to-connect-health-agent-install/install2.png)

Esta ação inicia uma janela do PowerShell para iniciar o processo de registo do agente. Quando lhe for pedido, inicie sessão com uma conta do Azure AD que tenha acesso para efetuar o registo do agente. Por predefinição, a conta de Administrador Global possui acesso.

![Verificar o Azure AD Connect Health](./media/how-to-connect-health-agent-install/install3.png)

Depois de iniciar sessão, o PowerShell prosseguirá. Uma vez concluído, pode fechá-lo e a configuração estará concluída.

Neste momento, os serviços de agente devem ser iniciados automaticamente, permitindo que o agente carregue os dados necessários para o serviço cloud de forma segura.

Se não tiver cumprido todos os pré-requisitos que foram descritos nas secções anteriores, são apresentados avisos na janela do PowerShell. Antes de instalar o agente, certifique-se de que concluiu os [requisitos](how-to-connect-health-agent-install.md#requirements). A captura de ecrã seguinte mostra um exemplo destes erros.

![Verificar o Azure AD Connect Health](./media/how-to-connect-health-agent-install/install4.png)

Para verificar se o agente foi instalado, procure os serviços seguintes no servidor. Se concluiu a configuração, já devem estar a ser executados. Caso contrário, são parados até que a configuração esteja concluída.

* Serviço de Diagnóstico do Azure AD Connect Health AD FS
* Serviço de Informações do Azure AD Connect Health AD FS
* Serviço de Monitorização do Azure AD Connect Health AD FS

![Verificar o Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

### <a name="agent-installation-on-windows-server-2008-r2-servers"></a>Instalação do agente em servidores do Windows Server 2008 R2
Passos para servidores do Windows Server 2008 R2:

1. Certifique-se de que o servidor está a ser executado com o Service Pack 1 ou superior.
2. Desative o IE ESC para instalação de agentes:
3. Antes de instalar o agente do AD Health, instale o Windows PowerShell 4.0 em cada um dos servidores. Para instalar o Windows PowerShell 4.0:
   * Instale o [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779) utilizando a seguinte ligação para transferir o instalador offline.
   * Instale o ISE do PowerShell (a partir de Funcionalidades do Windows)
   * Instale o [Windows Management Framework 4.0.](https://www.microsoft.com/download/details.aspx?id=40855)
   * Instale no servidor o Internet Explorer versão 10 ou posterior. (É um requisito do Serviço de Integridade para o autenticar com as suas credenciais de administrador do Azure.)
4. Para obter mais informações sobre como instalar o Windows PowerShell 4.0 no Windows Server 2008 R2, veja o artigo wiki [aqui](https://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

### <a name="enable-auditing-for-ad-fs"></a>Ativar a auditoria para o AD FS
> [!NOTE]
> Esta secção aplica-se apenas aos servidores do AD FS. Não tem de seguir estes passos nos Servidores de Proxy de Aplicações Web.
>

Para que a funcionalidade de Análise de Utilização possa recolher e analisar dados, o agente do Azure AD Connect Health precisa das informações nos Registos de Auditoria do AD FS. Estes registos não estão ativados por predefinição. Utilize os procedimentos seguintes para ativar a auditoria do AD FS e para localizar os registos de auditoria do AD FS nos servidores AD FS.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2008-r2"></a>Para ativar a auditoria para o AD FS no Windows Server 2008 R2
1. Clique em **Iniciar**, aponte para **Programas**, aponte para **Ferramentas Administrativas** e, em seguida, clique em **Política de Segurança Local**.
2. Navegue até à pasta **Security Settings\Local Policies\User Rights Assignment** e, em seguida, faça duplo clique em **Gerar auditorias de segurança**.
3. No separador **Definição de Segurança Local**, certifique-se de que a conta de serviço do AD FS 2.0 está listada. Se não estiver presente, clique em **Adicionar Utilizador ou Grupo**, adicione-a à lista e, em seguida, clique em **OK**.
4. Para ativar a auditoria, abra uma Linha de Comandos com privilégios elevados e execute o comando seguinte: <code>auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable</code>
5. Feche a **Política de Segurança Local**.
<br />   -- **Os seguintes passos apenas são necessários para servidores do AD FS primários.** -- <br />
6. Abra a snap-in da **Gestão do AD FS**. Para abrir o snap-in Gestão do AD FS, clique em **Iniciar**, aponte para **Programas**, aponte para **Ferramentas Administrativas** e, em seguida, clique em **Gestão do AD FS 2.0**.
7. No painel **Ações**, clique em **Editar Propriedades do Serviço de Federação**.
8. Na caixa de diálogo **Propriedades do Serviço de Federação**, clique no separador **Eventos**.
9. Selecione as caixas de verificação **Auditorias de êxito** e **Auditorias de falha**.
10. Clique em **OK**.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2012-r2"></a>Para ativar a auditoria para o AD FS no Windows Server 2012 R2
1. Abra a **Política de Segurança Local** abrindo o **Gestor de Servidores** no ecrã Iniciar ou o Gestor de Servidores na barra de tarefas no ambiente de trabalho e clique em **Ferramentas/Política de Segurança Local**.
2. Navegue até à pasta **Security Settings\Local Policies\User Rights Assignment** e, em seguida, faça duplo clique em **Gerar auditorias de segurança**.
3. No separador **Definição de Segurança Local**, certifique-se de que a conta de serviço do AD FS está listada. Se não estiver presente, clique em **Adicionar Utilizador ou Grupo**, adicione-a à lista e, em seguida, clique em **OK**.
4. Para ativar a auditoria, abra uma linha de comandos com privilégios elevados e execute o comando seguinte: ```auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable```.
5. Feche a **Política de Segurança Local**.
<br />   -- **Os seguintes passos apenas são necessários para servidores do AD FS primários.** -- <br />
6. Abra a o snap-in **Gestão do AD FS** (no Gestor de Servidor, clique em Ferramentas e selecione Gestão do AD FS).
7. No painel **Ações**, clique em **Editar Propriedades do Serviço de Federação**.
8. Na caixa de diálogo **Propriedades do Serviço de Federação**, clique no separador **Eventos**.
9. Selecione as caixas de verificação **Auditorias de êxito e Auditorias de falha** e clique em **OK**.

#### <a name="to-enable-auditing-for-ad-fs-on-windows-server-2016"></a>Para ativar a auditoria para o AD FS no Windows Server 2016
1. Abra a **Política de Segurança Local** abrindo o **Gestor de Servidores** no ecrã Iniciar ou o Gestor de Servidores na barra de tarefas no ambiente de trabalho e clique em **Ferramentas/Política de Segurança Local**.
2. Navegue até à pasta **Security Settings\Local Policies\User Rights Assignment** e, em seguida, faça duplo clique em **Gerar auditorias de segurança**.
3. No separador **Definição de Segurança Local**, certifique-se de que a conta de serviço do AD FS está listada. Se não estiver presente, clique em **Adicionar Utilizador ou Grupo**, adicione a conta de serviço do AD FS à lista e, em seguida, clique em **OK**.
4. Para ativar a auditoria, abra uma linha de comandos com privilégios elevados e execute o comando seguinte: <code>auditpol.exe /set /subcategory:{0CCE9222-69AE-11D9-BED3-505054503030} /failure:enable /success:enable.</code>
5. Feche a **Política de Segurança Local**.
<br />   -- **Os seguintes passos apenas são necessários para servidores do AD FS primários.** -- <br />
6. Abra a o snap-in **Gestão do AD FS** (no Gestor de Servidor, clique em Ferramentas e selecione Gestão do AD FS).
7. No painel **Ações**, clique em **Editar Propriedades do Serviço de Federação**.
8. Na caixa de diálogo **Propriedades do Serviço de Federação**, clique no separador **Eventos**.
9. Selecione as caixas de verificação **Auditorias de êxito e Auditorias de falha** e clique em **OK**. Isto deve ser ativado por predefinição.
10. Abra uma janela do PowerShell e execute o seguinte comando: ```Set-AdfsProperties -AuditLevel Verbose```.

Tenha em atenção que o nível de auditoria "básico" está ativado por predefinição. Leia mais sobre a [Melhoria da Auditoria do AD FS no Windows Server 2016](https://docs.microsoft.com/windows-server/identity/ad-fs/technical-reference/auditing-enhancements-to-ad-fs-in-windows-server)


#### <a name="to-locate-the-ad-fs-audit-logs"></a>Para localizar os registos de auditoria do AD FS
1. Abra o **Visualizador de Eventos**.
2. Aceda aos Registos do Windows e selecione **Segurança**.
3. À direita, clique em **Filtrar os Registos Atuais**.
4. Em Origem do evento, selecione **Auditoria do AD FS**.

    [Notas de FAQ](reference-connect-health-faq.md#operations-questions) relativas aos Registos de auditoria.

![Registos de auditoria do AD FS](./media/how-to-connect-health-agent-install/adfsaudit.png)

> [!WARNING]
> Uma política de grupo pode desativar a auditoria do AD FS. Se a auditoria do AD FS estiver desativada, a análise de utilização sobre as atividades de início de sessão não estará disponível. Certifique-se de que não tem uma política de grupo que desativa a auditoria do AD FS. >
>


## <a name="installing-the-azure-ad-connect-health-agent-for-sync"></a>Instalar o agente Azure AD Connect Health para sincronização
O agente do Azure AD Connect Health para sincronização é instalado automaticamente na compilação mais recente do Azure AD Connect. Para utilizar o Azure AD Connect para sincronização, tem de transferir a versão mais recente do Azure AD Connect e instalá-la. Pode transferir a versão mais recente [aqui](https://www.microsoft.com/download/details.aspx?id=47594).

Para verificar se o agente foi instalado, procure os serviços seguintes no servidor. Se concluiu a configuração, já devem estar a ser executados. Caso contrário, são parados até que a configuração esteja concluída.

* Serviço de Informações do Azure AD Connect Health Sincronização
* Serviço de Monitorização do Azure AD Connect Health Sincronização

![Verificar o Azure AD Connect Health para sincronização](./media/how-to-connect-health-agent-install/services.png)

> [!NOTE]
> Lembre-se de que a utilização do Azure AD Connect Health requer o Azure AD Premium. Se não tiver o Azure AD Premium, não conseguirá concluir a configuração no portal do Azure. Para obter mais informações, veja a [página de requisitos](how-to-connect-health-agent-install.md#requirements).
>
>

## <a name="manual-azure-ad-connect-health-for-sync-registration"></a>Registo manual do Azure AD Connect Health para sincronização
Se o registo do agente Azure AD Connect Health para sincronização falhar após a instalação com êxito o Azure AD Connect, pode utilizar o seguinte comando do PowerShell para registar manualmente o agente.

> [!IMPORTANT]
> Só é necessário utilizar este comando PowerShell se falhar o registo do agente depois de instalar o Azure AD Connect.
>
>

O comando do PowerShell seguinte SÓ é necessário se o registo do agente de estado de funcionamento falhar, mesmo após a instalação e configuração com êxito do Azure AD Connect. Quando o agente for registado com êxito, os serviços do Azure AD Connect Health são iniciados.

Pode registar manualmente o agente do Azure AD Connect Health para sincronização com o seguinte comando do PowerShell:

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

O comando recebe os seguintes parâmetros:

* AttributeFiltering: $true (predefinição) - se o Azure AD Connect não estiver a sincronizar o conjunto predefinido de atributos e tiver sido personalizado para utilizar um conjunto filtrado de atributos. $false, caso contrário.
* StagingMode: $false (predefinição) - se o servidor do Azure AD Connect NÃO estiver no modo de teste, $true se o servidor estiver configurado para estar no modo de teste.

Quando lhe for pedida a autenticação, deve utilizar a mesma conta de administrador global (por exemplo, admin@domain.onmicrosoft.com) que foi utilizada para configurar o Azure AD Connect.

## <a name="installing-the-azure-ad-connect-health-agent-for-ad-ds"></a>Instalar o Agente Azure AD Connect Health para o AD DS
Para iniciar a instalação do agente, faça duplo clique no ficheiro .exe que transferiu. No primeiro ecrã, clique em Instalar.

![Verificar o Azure AD Connect Health](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install1.png)

Uma vez concluída a instalação, clique em Configurar Agora.

![Verificar o Azure AD Connect Health](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install2.png)

É iniciada uma linha de comandos, seguida de alguns comandos do PowerShell, que executam Register-AzureADConnectHealthADDSAgent. Quando lhe for pedido para iniciar sessão no Azure, continue e inicie-a.

![Verificar o Azure AD Connect Health](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install3.png)

Depois de iniciar sessão, o PowerShell prosseguirá. Uma vez concluído, pode fechá-lo e a configuração estará concluída.

Nesta altura, os serviços deverão ser iniciados automaticamente, permitindo ao agente monitorizar e recolher dados. Se não tiver cumprido todos os pré-requisitos que foram descritos nas secções anteriores, são apresentados avisos na janela do PowerShell. Antes de instalar o agente, certifique-se de que concluiu os [requisitos](how-to-connect-health-agent-install.md#requirements). A captura de ecrã seguinte mostra um exemplo destes erros.

![Verificar o Azure AD Connect Health para o AD DS](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install4.png)

Para verificar se o agente foi instalado, procure os serviços seguintes no controlador de domínio.

* Serviço de Informações do Azure AD Connect Health AD DS
* Serviço de Monitorização do Azure AD Connect Health AD DS

Se concluiu a configuração, estes serviços já devem estar a ser executados. Caso contrário, são parados até que a configuração esteja concluída.

![Verificar o Azure AD Connect Health](./media/how-to-connect-health-agent-install/aadconnect-health-adds-agent-install5.png)

### <a name="quick-agent-installation-in-multiple-servers"></a>Instalação do agente rápida em vários servidores
1. Crie uma conta de utilizador no Azure AD com uma palavra-passe.
2. Atribuir a **proprietário** função para esta conta AAD local no Azure AD Connect Health através do portal. Siga os passos [aqui](how-to-connect-health-operations.md#manage-access-with-role-based-access-control). Atribua a função para todas as instâncias de serviço. 
3. Transfira o ficheiro MSI do .exe, no controlador de domínio local para a instalação.
4. Execute o seguinte script para registo. Substitua os parâmetros com a nova conta de utilizador que criou e respetiva palavra-passe. 

```
AdHealthAddsAgentSetup.exe /quiet
sleep 30
$userName = "NEWUSER@DOMAIN"
$secpasswd = ConvertTo-SecureString "PASSWORD" -AsPlainText -Force
$myCreds = New-Object System.Management.Automation.PSCredential ($userName, $secpasswd)
import-module "C:\Program Files\Azure Ad Connect Health Adds Agent\PowerShell\AdHealthAdds"
 
Register-AzureADConnectHealthADDSAgent -UserPrincipalName $USERNAME -Credential $password

```
1. Quando tiver terminado, pode remover o acesso para a conta local, fazendo uma ou mais dos seguintes procedimentos: 
    * Remover a atribuição de função para a conta local para o AAD Connect Health
    * Roda a palavra-passe para a conta local. 
    * Desativar a conta local do AAD
    * Eliminar a conta local do AAD  

## <a name="agent-registration-using-powershell"></a>Registo do Agente com o PowerShell
Depois de instalar o agente adequado setup.exe, pode efetuar o passo de registo do agente com os comandos do PowerShell seguintes, consoante a função. Abra uma Janela do PowerShell e execute o comando adequado:

```
    Register-AzureADConnectHealthADFSAgent
    Register-AzureADConnectHealthADDSAgent
    Register-AzureADConnectHealthSyncAgent

```

Estes comandos aceitam "Credencial" como um parâmetro para concluir o registo de forma não interativa ou num computador de Núcleo de Servidor.
* A Credencial pode ser capturada numa variável do PowerShell que é transmitida como um parâmetro.
* Pode fornecer qualquer Azure AD Identity que tenha acesso de registo dos agentes e NÃO tenha o MFA ativado.
* Por predefinição, os Administradores Globais têm acesso para efetuar o registo do agente. Também pode permitir que as outras identidades menos privilegiadas efetuem este passo. Leia mais sobre [Role Based Access Control (Controlo de Acesso Baseado na Função)](how-to-connect-health-operations.md#manage-access-with-role-based-access-control).

```
    $cred = Get-Credential
    Register-AzureADConnectHealthADFSAgent -Credential $cred

```

## <a name="configure-azure-ad-connect-health-agents-to-use-http-proxy"></a>Configurar os Agentes do Azure AD Connect Health para utilizarem o Proxy de HTTP
Pode configurar os Agentes do Azure AD Connect Health para trabalharem com um Proxy de HTTP.

> [!NOTE]
> * A utilização de “Netsh WinHttp define ProxyServerAddress” não é suportada, uma vez que o agente utiliza System.Net para fazer pedidos Web em vez dos Serviços HTTP do Microsoft Windows.
> * O endereço Http Proxy configurado é utilizado para fazer o pass-through de mensagens Https encriptadas.
> * Proxies autenticados (utilizando HTTPBasic) não são suportados.
>
>

### <a name="change-health-agent-proxy-configuration"></a>Alterar a configuração de Proxy do Agente de Estado de Funcionamento
Tem as seguintes opções para configurar o Agente do Azure AD Connect Health para utilizar um Proxy de HTTP.

> [!NOTE]
> Para que as definições de proxy sejam atualizadas, têm de ser reiniciados todos os serviços do Agente do Azure AD Connect Health. Execute o seguinte comando:<br />
> Restart-Service AdHealth*
>
>

#### <a name="import-existing-proxy-settings"></a>Importar Definições de proxy existente
##### <a name="import-from-internet-explorer"></a>Importar do Internet Explorer
As definições de proxy de HTTP do Internet Explorer podem ser importadas para serem utilizadas pelos Agentes do Azure AD Connect Health. Em cada um dos servidores que executem o Agente de estado de funcionamento, execute o seguinte comando do PowerShell:

    Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### <a name="import-from-winhttp"></a>Importar do WinHTTP
As definições de proxy de WinHTTP podem ser importadas para serem utilizadas pelos Agentes do Azure AD Connect Health. Em cada um dos servidores que executem o Agente de estado de funcionamento, execute o seguinte comando do PowerShell:

    Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### <a name="specify-proxy-addresses-manually"></a>Especificar manualmente endereços Proxy
Pode especificar manualmente um servidor proxy em cada servidor que executa o Agente de Estado de Funcionamento, através do seguinte comando do PowerShell:

    Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

Exemplo: *Set-AzureAdConnectHealthProxySettings - HttpsProxyAddress myproxyserver: 443*

* “address” pode ser um nome de servidor DNS resolvível ou um endereço IPv4
* pode ser omitida a “porta”. Se for omitida, então 443 é escolhida como porta predefinida.

#### <a name="clear-existing-proxy-configuration"></a>Limpar a configuração de proxy existente
Pode limpar a configuração de proxy existente ao executar o seguinte comando:

    Set-AzureAdConnectHealthProxySettings -NoProxy


### <a name="read-current-proxy-settings"></a>Ler as definições de proxy atuais
Pode ler as definições de proxy atualmente configuradas ao executar o seguinte comando:

    Get-AzureAdConnectHealthProxySettings


## <a name="test-connectivity-to-azure-ad-connect-health-service"></a>Testar a conectividade ao Serviço Azure AD Connect Health
É possível que possam surgir problemas que levem o agente do Azure AD Connect Health a perder a conectividade ao serviço do Azure AD Connect Health. Incluem problemas de rede, problemas de permissão ou vários outros motivos.

Se o agente não conseguir enviar dados para o serviço do Azure AD Connect Health durante mais de duas horas, é apresentado o alerta no portal do: "Dados de estado de funcionamento do serviço não estão atualizados." Pode confirmar se o agente do Azure AD Connect Health consegue carregar dados para este serviço ao executar o seguinte comando do PowerShell:

    Test-AzureADConnectHealthConnectivity -Role ADFS

O parâmetro de função toma atualmente os seguintes valores:

* ADFS
* Sync
* AD DS

> [!NOTE]
> Para utilizar a ferramenta de conectividade, primeiro tem de concluir o registo do agente. Se não conseguir concluir o registo do agente, certifique-se de que cumpriu todos os [requisitos](how-to-connect-health-agent-install.md#requirements) do Azure AD Connect Health. Este teste de conectividade é efetuado por predefinição durante o registo do agente.
>
>

## <a name="related-links"></a>Ligações relacionadas
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Azure AD Connect Health Operations (Operações do Azure AD Connect Health)](how-to-connect-health-operations.md)
* [Utilizar o Azure AD Connect Health com o AD FS](how-to-connect-health-adfs.md)
* [Utilizar o Azure AD Connect Health para sincronização](how-to-connect-health-sync.md)
* [Utilizar o Azure AD Connect Health com o AD DS](how-to-connect-health-adds.md)
* [FAQ do Azure AD Connect Health](reference-connect-health-faq.md)
* [Histórico das Versões do Azure AD Connect Health](reference-connect-health-version-history.md)
