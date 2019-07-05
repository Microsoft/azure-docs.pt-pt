---
title: Ativar as auditorias de segurança para serviços de domínio do Azure AD | Documentos da Microsoft
description: Ativar as auditorias de segurança para o Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: 3105296b3c670d3d44789c93878fa1fc6076973b
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566498"
---
# <a name="enable-security-audits-for-azure-ad-domain-services-preview"></a>Ativar as auditorias de segurança para serviços de domínio do Azure AD (pré-visualização)
A auditoria de segurança de serviços de domínio do AD do Azure permite aos clientes utilizar o portal de serviço de domínio do Azure AD para transmitir eventos de auditoria de segurança aos recursos direcionados. Os recursos que podem receber esses eventos incluem o armazenamento do Azure, áreas de trabalho do Azure Log Analytics ou Hub de eventos do Azure. Pouco tempo depois de ativar eventos de auditoria de segurança, o serviço de domínio do Azure AD envia todos os eventos auditados para a categoria selecionada para o recurso de destino. Eventos de auditoria de segurança que os clientes para arquivar auditado eventos no armazenamento do Azure. Além disso, os clientes podem transmitir em fluxo eventos em segurança informações e eventos software de gestão (SIEM) (ou equivalente) com os hubs de eventos ou fazer sua própria análise e as informações ao utilizar o Azure Log Analytics no portal do Azure. 

> [!IMPORTANT]
> Auditoria de segurança do Azure AD Domain Services está disponível apenas para as instâncias baseadas no Azure Resource Manager para o Azure AD Domain Services.
>
>

## <a name="auditing-event-categories"></a>Categorias de eventos de auditoria
Auditoria de segurança dos serviços de domínio do AD do Azure se alinha com a auditoria tradicionais que vem para controladores de domínio do Active Directory Domain Services. Reutilizar existente de auditoria de padrões garante que a mesma lógica pode ser utilizada ao analisar os eventos. Auditoria de segurança do Azure AD Domain Services inclui as seguintes categorias de eventos.

| Nome da categoria de auditoria | Descrição |
|:---|:---|
| Início de sessão de conta|Audita tentativas de autenticação de dados de conta num controlador de domínio ou num Gestor de contas do local segurança (SAM).</p>Início de sessão e as definições de política de Logoff e eventos de controlam tentativas de acesso a um determinado computador. As definições e eventos nesta categoria enfocam a base de dados de conta é utilizada. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditar validação de credenciais](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Serviço de autenticação do Kerberos de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Auditar operações de permissão de serviço do Kerberos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Auditar outros eventos de Logon/Logoff](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Gestão de Contas|Auditorias de alterações às contas de utilizador e computador e grupos. Esta categoria inclui as seguintes subcategorias:<ul><li>[Gestão de grupos de aplicativo de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Auditar gestão de contas de computador](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Gestão de grupos de distribuição de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Auditar gestão de contas de outros](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Gestão de grupo de segurança de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Auditar gestão de contas de utilizador](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| Detalhes de controlo|Atividades de auditorias de aplicativos individuais e os usuários nesse computador e para compreender como um computador está a ser utilizado. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditar atividade de DPAPI](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[Atividade de PNP de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[Criação de processo de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[Auditar termo de processo](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[Auditar eventos de RPC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Acesso de serviços de diretório|As auditorias tenta acessar e modificar objetos no Active Directory Domain Services (AD DS). Estes eventos são registrados apenas em controladores de domínio de auditoria. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditar replicação de serviços de diretório detalhadas](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Auditar acesso ao serviço de diretório](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Auditar alterações no serviço de diretório](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Auditar replicação de serviços de diretório](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Fim de sessão de início de sessão|Audita tentativas para iniciar sessão num computador interativamente ou através de uma rede. Esses eventos são úteis para controlar a atividade do utilizador e identificar potenciais ataques nos recursos de rede. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditar bloqueio de conta](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Afirmações de utilizador/dispositivo de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[Modo expandido de IPsec de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Associação ao grupo de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[Modo de principal de IPsec de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[Modo rápido de IPsec de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Auditar fim de sessão](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Auditar início de sessão](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logon)</li><li>[Servidor de políticas de rede de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Auditar outros eventos de Logon/Logoff](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Auditar início de sessão especial](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Acesso a objetos| Audita tentativas de acesso a objetos específicos ou tipos de objetos num computador ou rede. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditar aplicação gerada](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Serviços de certificação de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Auditar partilha de ficheiros detalhada](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Auditar partilha de ficheiros](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-share)</li><li>[Auditar sistema de ficheiros](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Ligação de plataforma de filtragem de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Largada de pacotes da plataforma de filtragem de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Auditar manipulação de identificador](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Auditar objeto de Kernel](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Auditar outros eventos de acesso de objeto](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Registo de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-registry)</li><li>[Auditar o armazenamento amovível](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[Auditar SAM](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sam)</li><li>[Auditar transição de política de acesso Central](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|Alteração de política|Auditorias de alterações às políticas de segurança importante num sistema local ou a rede. As políticas, normalmente, são estabelecidas pelos administradores para ajudar os recursos de rede segura. Monitorização de alterações ou tentativas para alterar estas políticas pode ser um aspecto importante de gerenciamento de segurança para uma rede. Esta categoria inclui as seguintes subcategorias:<ul><li>[Alteração de política de auditoria de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Auditar alteração de política de autenticação](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Auditar alteração de política de autorização](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Alteração de política de plataforma de filtragem de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[Auditar alteração de política de nível de regra MPSSVC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Outra alteração de política de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Uso de privilégios| Audita a utilização de determinadas permissões num ou mais sistemas. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditar utilização de privilégio não confidenciais](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Auditar utilização de privilégio confidenciais](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Auditar outros eventos de utilização de privilégio](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|Sistema| As alterações ao nível do sistema de auditorias para um computador não incluídos nas outras categorias e que tem implicações de segurança potencial. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditar controlador IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Auditar outros eventos de sistema](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Alteração de estado de segurança de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Extensão de sistema de segurança de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Auditar integridade do sistema](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>IDs de eventos por categoria
 Auditoria de segurança dos serviços de domínio do AD do Azure regista os seguintes IDs de evento, quando a ação específica aciona um evento auditável.

| Nome da categoria de evento | IDs de evento |
|:---|:---|
|Segurança de início de sessão de conta|4767, 4774, 4775, 4776, 4777|
|Segurança da gestão de conta|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|Segurança de controlo de detalhe|Nenhuma|
|Segurança de acesso ao DS|5136, 5137, 5138, 5139, 5141|
|Segurança de fim de sessão de início de sessão|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Segurança de acesso de objeto|Nenhuma|
|Segurança de alteração de política|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|Segurança de utilização de privilégio|4985|
|Segurança do sistema|4612, 4621|

## <a name="enable-security-audit-events"></a>Ativar eventos de auditoria de segurança
As seguintes orientações ajudam-o a com êxito subscrever a eventos de auditoria de segurança de serviços de domínio do Azure AD.

> [!IMPORTANT]
> As auditorias de segurança do Azure AD Domain Services não estão retroativa. Não é possível obter eventos do passado ou para reproduzir eventos do passado. O serviço só pode enviar eventos que ocorrem depois de ser ativada.
>

### <a name="choose-the-target-resource"></a>Escolha o recurso de destino
Pode utilizar qualquer combinação de armazenamento do Azure, os Hubs de eventos do Azure ou áreas de trabalho do Azure Log Analytics como um recurso de destino para auditorias de segurança. Considere a seguinte tabela para o melhor recurso para o seu caso de utilização.

> [!IMPORTANT]
> Tem de criar o recurso de destino antes de ativar as auditorias de segurança do Azure AD Domain Services.
>

| Recurso de destino | Cenário |
|:---|:---|
|Storage do Azure|Considere utilizar neste destino, quando sua necessidade primária é armazenar eventos de auditoria de segurança para fins de arquivamento. Outros destinos podem ser utilizados para fins de registo; No entanto, esses destinos fornecem capacidades para além de precisar primário de arquivamento. Para criar uma conta de armazenamento do Azure, siga [criar uma conta de armazenamento.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal#create-a-storage-account-1)|
|Azure Event Hubs|Considere utilizar neste destino, quando sua necessidade primária é compartilhar eventos de auditoria de segurança com o software adicional, como o software de análise de dados ou software de gestão (SIEM) de informações e eventos de segurança. Para criar um hub de eventos, siga [início rápido: Crie um hub de eventos com o portal do Azure.](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)|
|Área de trabalho do Azure Log Analytics|Considere utilizar neste destino, quando sua necessidade primária é analisar e rever o seguras de auditorias a partir do portal do Azure diretamente.  Para criar uma área de trabalho do Log Analytics, siga [criar uma área de trabalho do Log Analytics no portal do Azure.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)|

## <a name="using-the-azure-portal-to-enable-security-audit-events"></a>Utilizar o portal do Azure para ativar eventos de auditoria de segurança 
1. Inicie sessão no portal do Azure em https://portal.azure.com.  No portal do Azure, clique em todos os serviços. Na lista de recursos, escreva **domínio**. À medida que começa a escrever, a lista filtra com base na sua entrada. Clique em **dos serviços de domínio do Azure AD**.
2. Clique na instância de serviços de domínio do Azure AD da lista.
3. Clique em **das definições de diagnóstico (pré-visualização)** na lista de ações no lado esquerdo.</p>
![ação de definição de diagnóstico](./media/security-audit-events/diagnostic-settings-action.png)
4. Escreva o nome da configuração de diagnóstico (**aadds-auditing** como exemplo).</p>
![página de definições de diagnóstico](./media/security-audit-events/diagnostic-settings-page.png)
5. Selecione a caixa de verificação junto aos recursos direcionados, que usará com eventos de auditoria de segurança apropriada.
    > [!NOTE]
    > Não é possível criar recursos de destino a partir desta página.
    >
    
    **armazenamento do Azure:**</p>
    Selecione **arquivo para uma conta de armazenamento**. Clique em **Configurar**. Selecione o **subscrição** e o **conta de armazenamento** que pretende utilizar para arquivar eventos de auditoria de segurança. Clique em **OK**.</p>
    
    ![definições de armazenamento do diagnóstico](./media/security-audit-events/diag-settings-storage.png)
    
    **Hubs de eventos do Azure:**</p>
    Selecione **Stream para um hub de eventos**. Clique em **Configurar**. Na **página do hub de eventos Select**, selecione a **subscrição** utilizado para criar o hub de eventos. Em seguida, selecione o **espaço de nomes de hub de eventos**, **nome do hub de eventos**, e **nome de política do hub de eventos**. Clique em **OK**.</p>
    ![definições do hub de eventos de diagnóstico](./media/security-audit-events/diag-settings-eventhub.png)
    
    **Áreas de trabalho do Log Analytics do Azure:**</p>
    Selecione **enviar para o Log Analytics**. Selecione o **subscrição** e **área de trabalho do Log Analytics** utilizada para armazenar eventos de auditoria de segurança.</p>
    ![definições de diagnóstico de área de trabalho](./media/security-audit-events/diag-settings-log-analytics.png)

6. Selecione as categorias de registo que pretender incluído para o recurso de destino específico. Se utilizar contas de armazenamento, pode configurar políticas de retenção.

    > [!NOTE]
    > Pode selecionar categorias de registo diferente para cada recurso de destino dentro de uma configuração única. Isto permite-lhe escolher registos que pretende manter para o Log Analytics e que regista categorias quer arquivar de categorias.
    >

7. Clique em **guardar** para consolidar as alterações. Os recursos de destino irão receber eventos de auditoria de segurança do Azure AD Domain Services pouco tempo depois de guardar a configuração.

## <a name="using-azure-powershell-to-enable-security-audit-events"></a>Com o Azure PowerShell para ativar a eventos de auditoria de segurança
 
### <a name="prerequisites"></a>Pré-requisitos

Siga as instruções no artigo para [instalar o módulo Azure PowerShell e ligue à sua subscrição do Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="enable-security-audits"></a>Ativar as auditorias de segurança

1. Autenticar para o Azure Resource Manager para o inquilino adequado e a subscrição a utilizar o **Connect-AzAccount** cmdlet do PowerShell do Azure.
2. Crie o recurso de destino para garantir a segurança eventos de auditoria.</p>
    **armazenamento do Azure:**</p>
    Siga [criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-powershell) para criar a conta de armazenamento.</p>
    **Hubs de eventos do Azure:**</p>
    Siga [início rápido: Criar um hub de eventos com o Azure PowerShell](https://docs.microsoft.com/azure/event-hubs/event-hubs-quickstart-powershell) para criar o seu hub de eventos. Também poderá ter de utilizar o [New-AzEventHubAuthorizationRule](https://docs.microsoft.com/powershell/module/az.eventhub/new-azeventhubauthorizationrule?view=azps-2.3.2) cmdlet do PowerShell do Azure para criar uma regra de autorização para permitir permissões de serviços de domínio do AD do Active Directory para o hub de eventos **espaço de nomes**. A regra de autorização tem de incluir o **Manage**, **escutar**, e **enviar** direitos.
    > [!IMPORTANT]
    > Certifique-se de que define a regra de autorização do espaço de nomes do hub de eventos e não o hub de eventos.
       
    </p>
    
    **Áreas de trabalho do Log Analytics do Azure:**</p>
    Siga [criar uma área de trabalho do Log Analytics com o Azure PowerShell](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace-posh) para criar a sua área de trabalho.
3. Obtenha o ID de recurso para a sua instância do Azure AD Domain Services. Na consola do Windows PowerShell aberta, autenticada, escreva o seguinte comando. Utilize o **$aadds. ResourceId** variável como um parâmetro para o ID de recurso de serviços de domínio do Azure AD para cmdlets futuras.
    ```powershell
    $aadds = Get-AzResource -name aaddsDomainName
    ``` 
4. Utilizar o **Set-AzDiagnosticSetting** cmdlet para configurar as definições de diagnóstico do Azure para utilizar o recurso de destino para eventos de auditoria de segurança do Azure AD Domain Services. Nos exemplos abaixo, a variável $aadds. ResourceId representa o ID de recurso da sua instância do Azure AD Domain Services (consulte o passo 3).</p>
    **armazenamento do Azure:**
    ```powershell
    Set-AzDiagnosticSetting `
    -ResourceId $aadds.ResourceId` 
    -StorageAccountId storageAccountId `
    -Enabled $true
    ```
    Substitua *storageAccountId* com seu armazenamento de conta ID.</p>
    
    **Hubs de eventos do Azure:**
    ```powershell
    Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId ` 
    -EventHubName eventHubName `
    -EventHubAuthorizationRuleId eventHubRuleId `
    -Enabled $true
    ```
    Substitua *eventHubName* com o nome do seu hub de eventos. Substitua *eventHubRuleId* com o seu ID de regra de autorização que criou anteriormente.</p>
    
    **Áreas de trabalho do Log Analytics do Azure:**
    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId ` 
    -WorkspaceID workspaceId `
    -Enabled $true
    ```
    Substitua *workspaceId* com o ID da área de trabalho do Log Analytics que criou anteriormente. 

## <a name="view-security-audit-events-using-azure-monitor"></a>Ver eventos de auditoria de segurança através do Azure Monitor
Áreas de trabalho de análise de registos permitem-lhe ver e analisar os eventos de auditoria de segurança com o Azure Monitor e a linguagem de consulta de Kusto. A linguagem de consulta foi concebida para utilização só de leitura, que pode se orgulhar dos capacidades de análise de energia, com uma sintaxe fácil de ler.
Aqui estão alguns recursos para ajudar a começar com linguagens de consulta de Kusto.
* [Documentação do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
* [Introdução ao Log Analytics no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)
* [Introdução às consultas de registo no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)
* [Criar e partilhar dashboards de dados do Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-logs-dashboards)

## <a name="sample-queries"></a>Amostras de consultas

### <a name="sample-query-1"></a>Consulta de exemplo 1
Todos os conta bloqueio eventos nos últimos sete dias.
```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>Consulta de exemplo 2
Todos os conta bloqueio eventos (4740) entre 26 de Junho de 2019 às 9 horas e 1 de Julho de 2019 meia-noite, ordenados ascendente pela data e hora.
```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01) 
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>Consulta de exemplo 3
Conta registo de eventos de sete dias atrás (a partir de agora) para a conta de utilizador nomeada.
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>Consulta de exemplo 4
Eventos de início de sessão de conta sete dias atrás do agora para a conta de utilizador que tentou iniciar sessão com uma palavra-passe incorreta (0xC0000006a) de nomeado.
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>Consulta de exemplo 5
Eventos de início de sessão de conta sete dias atrás do agora para a conta de nome de utilizador que tentou iniciar sessão, enquanto a conta foi bloqueada (0xC0000234).
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>Consulta de exemplo 6
O número de eventos de início de sessão de conta sete dias atrás do agora para todos os início de sessão tentativas que ocorreram para todos os utilizadores bloqueados.
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="related-content"></a>Conteúdo relacionado
* [Descrição geral](https://docs.microsoft.com/azure/kusto/query/) do Kusto linguagem de consulta.
* [Tutorial de Kusto](https://docs.microsoft.com/azure/kusto/query/tutorial) se familiarizar com os fundamentos básicos de consulta.
* [Consultas de exemplo](https://docs.microsoft.com/azure/kusto/query/samples) que o ajudam a aprender novas formas para ver os seus dados.
* Kusto [melhores práticas](https://docs.microsoft.com/azure/kusto/query/best-practices) – otimizar as suas consultas para o sucesso.














 
