---
title: Ativar auditorias de segurança para serviços de domínio azure AD [ Microsoft Docs
description: Saiba como permitir auditorias de segurança para centralizar o registo de eventos para análise e alertas em Serviços de Domínio Da AD Azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: ce910b553e14d09eefa35efc5f2973337dfa1309
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654668"
---
# <a name="enable-security-audits-for-azure-active-directory-domain-services"></a>Ativar auditorias de segurança para serviços de domínio de diretório ativo Azure

As auditorias de segurança da Azure Ative Directory Domain Services (Azure AD DS) permitem que o Azure transmita eventos de segurança para recursos direcionados. Estes recursos incluem O Armazenamento Azure, espaços de trabalho Azure Log Analytics ou Azure Event Hub. Depois de ativar eventos de auditoria de segurança, o Azure AD DS envia todos os eventos auditados para a categoria selecionada para o recurso direcionado.

Pode arquivar eventos em eventos de armazenamento e streaming de eventos em informações de segurança e software de gestão de eventos (Ou equivalente) utilizando hubs de eventos Azure, ou fazer a sua própria análise e utilizando espaços de trabalho Azure Log Analytics do portal Azure.

> [!IMPORTANT]
> As auditorias de segurança da Azure AD DS só estão disponíveis para instâncias baseadas em Gestores de Recursos Azure. Para obter informações sobre como migrar, consulte [Migrate Azure AD DS do modelo clássico de rede virtual para Gestor][migrate-azure-adds]de Recursos .

## <a name="security-audit-destinations"></a>Destinos de auditoria de segurança

Pode utilizar espaços de trabalho Azure Storage, Azure Event Hubs ou Azure Log Analytics como recurso-alvo para auditorias de segurança Azure AD DS. Estes destinos podem ser combinados. Por exemplo, você poderia usar o Armazenamento Azure para arquivar eventos de auditoria de segurança, mas um espaço de trabalho Azure Log Analytics para analisar e reportar sobre a informação a curto prazo.

A tabela seguinte descreve cenários para cada tipo de recurso de destino.

> [!IMPORTANT]
> Tem de criar o recurso-alvo antes de ativar as auditorias de segurança da Azure AD DS. Pode criar estes recursos utilizando o portal Azure, O Azure PowerShell ou o Azure CLI.

| Recurso-alvo | Cenário |
|:---|:---|
|Storage do Azure| Este alvo deve ser utilizado quando a sua principal necessidade é armazenar eventos de auditoria de segurança para fins de arquivo. Outros alvos podem ser usados para fins de arquivo, no entanto esses alvos fornecem capacidades para além da necessidade primária de arquivamento. <br /><br />Antes de ativar eventos de auditoria de segurança Azure AD DS, primeiro [Crie uma conta de Armazenamento Azure](../storage/common/storage-account-create.md).|
|Azure Event Hubs| Este alvo deve ser utilizado quando a sua principal necessidade é partilhar eventos de auditoria de segurança com software adicional, como software de análise de dados ou informações de segurança & software de gestão de eventos (SIEM).<br /><br />Antes de ativar eventos de auditoria de segurança Azure AD DS, [Crie um hub de eventos usando](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) o portal Azure|
|Espaço de trabalho azure Log Analytics| Este alvo deve ser utilizado quando a sua principal necessidade é analisar e rever as auditorias seguras do portal Azure diretamente.<br /><br />Antes de ativar eventos de auditoria de segurança Azure AD DS, Crie um espaço de [trabalho log Analytics no portal Azure.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)|

## <a name="enable-security-audit-events-using-the-azure-portal"></a>Ativar eventos de auditoria de segurança usando o portal Azure

Para permitir eventos de auditoria de segurança Azure AD DS utilizando o portal Azure, complete os seguintes passos.

> [!IMPORTANT]
> As auditorias de segurança da AD DS azure não são retroativas. Não se pode recuperar ou reproduzir eventos do passado. O Azure AD DS só pode enviar eventos que ocorram após a ativação das auditorias de segurança.

1. Inicie sessão no portal do Azure em https://portal.azure.com.
1. No topo do portal Azure, procure e selecione Serviços de **Domínio Azure AD.** Escolha o seu domínio gerido, como *aaddscontoso.com*.
1. Na janela Azure AD DS, selecione **as definições** de diagnóstico no lado esquerdo.
1. Nenhum diagnóstico é configurado por padrão. Para começar, selecione **Adicionar definição de diagnóstico**.

    ![Adicione uma definição de diagnóstico para serviços de domínio Azure AD](./media/security-audit-events/add-diagnostic-settings.png)

1. Introduza um nome para a configuração de diagnóstico, como *aaadds-auditing*.

    Verifique a caixa para o destino de auditoria de segurança que deseja. Pode escolher entre uma conta de Armazenamento Azure, um hub de eventos Azure ou um espaço de trabalho log Analytics. Estes recursos de destino já devem existir na sua subscrição Azure. Não se pode criar os recursos de destino neste feiticeiro.

    ![Ativar o destino e o tipo de eventos de auditoria necessários para capturar](./media/security-audit-events/diagnostic-settings-page.png)

    * **Armazenamento azure**
        * Selecione **Archive para uma conta**de armazenamento e, em seguida, escolha **Configurar**.
        * Selecione a **Subscrição** e a **conta de Armazenamento** que pretende utilizar para arquivar eventos de auditoria de segurança.
        * Quando estiver pronto, escolha **OK**.
    * **Hubs de eventos azure**
        * Selecione **Stream para um centro de eventos**e, em seguida, escolha **Configurar**.
        * Selecione a **Subscrição** e o espaço de nome do **hub do Evento**. Se necessário, escolha também um **nome de hub de evento** e, em **seguida, o nome**da política do hub do evento .
        * Quando estiver pronto, escolha **OK**.
    * **Espaços de trabalho analíticos Azure Log**
        * Selecione **Enviar para Registar Analytics**e, em seguida, escolha o Espaço de Trabalho de **Subscrição** e **Log Analytics** que pretende utilizar para armazenar eventos de auditoria de segurança.

1. Selecione as categorias de registo que deseja incluídas para o recurso-alvo específico. Se enviar os eventos de auditoria para uma conta de Armazenamento Azure, também pode configurar uma política de retenção que define o número de dias para reter dados. Uma definição padrão de *0* retém todos os dados e não gira eventos após um período de tempo.

    Pode selecionar diferentes categorias de registo para cada recurso direcionado dentro de uma única configuração. Esta habilidade permite-lhe escolher quais as categorias de logs que pretende manter para o Log Analytics e quais as categorias que pretende arquivar, por exemplo.

1. Quando terminar, selecione **Guardar** para comprometer as suas alterações. Os recursos-alvo começam a receber eventos de auditoria de segurança Azure AD DS logo após a configuração ser guardada.

## <a name="enable-security-audit-events-using-azure-powershell"></a>Ativar eventos de auditoria de segurança usando o Azure PowerShell

Para permitir eventos de auditoria de segurança Azure AD DS utilizando o Azure PowerShell, complete os seguintes passos. Se necessário, instale primeiro [o módulo Azure PowerShell e ligue-se à subscrição do Azure](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> As auditorias de segurança da AD DS azure não são retroativas. Não se pode recuperar ou reproduzir eventos do passado. O Azure AD DS só pode enviar eventos que ocorram após a ativação das auditorias de segurança.

1. Autenticar a subscrição do Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/Az.Accounts/Connect-AzAccount) Quando solicitado, insira as credenciais da sua conta.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Crie o recurso-alvo para os eventos de auditoria de segurança.

    * **Armazenamento Azure** - [Criar uma conta de armazenamento usando o Azure PowerShell](../storage/common/storage-account-create.md?tabs=azure-powershell)
    * **Hubs** - de eventos Azure[Criar um hub de eventos usando O PowerShell Azure](../event-hubs/event-hubs-quickstart-powershell.md). Também pode ser necessário utilizar o [New-AzEventHubAuthorizationRule](/powershell/module/az.eventhub/new-azeventhubauthorizationrule) cmdlet para criar uma regra de autorização que concede permissões Azure AD DS ao espaço de *nome*do centro de eventos . A regra de autorização deve incluir os direitos **de Gestão,** **Escuta**e **Envio.**

        > [!IMPORTANT]
        > Certifique-se de que define a regra de autorização no espaço de nome do centro do evento e não no próprio centro de eventos.

    * **Espaços** - de trabalho analíticos Azure Log[Create a Log Analytics workspace with Azure PowerShell](../azure-monitor/learn/quick-create-workspace-posh.md).

1. Obtenha o ID de recurso para o seu domínio gerido azure AD DS utilizando o cmdlet [Get-AzResource.](/powershell/module/Az.Resources/Get-AzResource) Criar uma variável chamada *$aadds. ResourceId* para manter o valor:

    ```azurepowershell
    $aadds = Get-AzResource -name aaddsDomainName
    ```

1. Configure as definições de diagnóstico do Azure utilizando o cmdlet [Set-AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) para utilizar o recurso-alvo para eventos de auditoria de segurança dos Serviços de Domínio Azure AD. Nos exemplos seguintes, a variável *$aadds. O ResourceId* é utilizado a partir do passo anterior.

    * **Armazenamento Azure** - Substitua *o armazenamentoAccountId* com o nome da sua conta de armazenamento:

        ```powershell
        Set-AzDiagnosticSetting `
            -ResourceId $aadds.ResourceId `
            -StorageAccountId storageAccountId `
            -Enabled $true
        ```

    * **Hubs de eventos Azure** - Substitua o *eventHubName* com o nome do seu centro de eventos e *eventoSHubRuleId* com a sua regra de autorização ID:

        ```powershell
        Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId `
            -EventHubName eventHubName `
            -EventHubAuthorizationRuleId eventHubRuleId `
            -Enabled $true
        ```

    * Espaços de **trabalho Analíticos Azure Log** - Substitua o espaço de *trabalhoId* pelo ID do espaço de trabalho Log Analytics:

        ```powershell
        Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId `
            -WorkspaceID workspaceId `
            -Enabled $true
        ```

## <a name="query-and-view-security-audit-events-using-azure-monitor"></a>Consulta e visualizar eventos de auditoria de segurança usando o Monitor Azure

Log Analytic workspaces permitem-lhe visualizar e analisar os eventos de auditoria de segurança usando o Monitor Azure e a linguagem de consulta Kusto. Esta linguagem de consulta é projetada para uso apenas de leitura que ostenta capacidades analíticas de potência com uma sintaxe de fácil leitura. Para obter mais informações para começar com as línguas de consulta de Kusto, consulte os seguintes artigos:

* [Documentação do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
* [Começar com Log Analytics no Monitor Azure](../azure-monitor/log-query/get-started-portal.md)
* [Começar com consultas de log no Monitor Azure](../azure-monitor/log-query/get-started-queries.md)
* [Criar e partilhar dashboards dos dados do Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

As seguintes consultas de amostra podem ser usadas para começar a analisar eventos de auditoria de segurança a partir de Azure AD DS.

### <a name="sample-query-1"></a>Consulta de amostra 1

Veja todos os eventos de bloqueio da conta nos últimos sete dias:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>Consulta de amostra 2

Veja todos os eventos de bloqueio de conta *(4740*) entre 3 de fevereiro de 2020 às 9h00. e 10 de fevereiro de 2020 meia-noite, ordenada ascendente pela data e hora:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-02-03 09:00) and TimeGenerated <= datetime(2020-02-10)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>Consulta de amostra 3

Consulte os eventos de inscrição na conta há sete dias (a partir de agora) para a conta nomeada utilizador:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>Consulta de amostra 4

Ver eventos de entrada de conta há sete dias para a conta nomeada utilizador que tentou assinar usando uma palavra-passe má *(0xC00000006a*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>Consulta de amostra 5

Ver eventos de inscrição de conta há sete dias a partir de agora para a conta chamada utilizador que tentou iniciar sessão enquanto a conta estava bloqueada *(0xC0000234*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>Consulta de amostra 6

Veja o número de eventos de inscrição de conta há sete dias a partir de agora para todas as tentativas de inscrição que ocorreram para todos os utilizadores bloqueados:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="audit-event-categories"></a>Categorias de eventos de auditoria

As auditorias de segurança azure AD DS alinham-se com a auditoria tradicional para controladores de domínio AD DS tradicionais. Em ambientes híbridos, pode reutilizar os padrões de auditoria existentes para que a mesma lógica possa ser usada na análise dos eventos. Dependendo do cenário que precisa para resolver ou analisar, as diferentes categorias de eventos de auditoria precisam de ser direcionadas.

Estão disponíveis as seguintes categorias de eventos de auditoria:

| Nome da categoria de auditoria | Descrição |
|:---|:---|
| Logon de conta|As auditorias tentam autenticar os dados da conta num controlador de domínio ou num Gestor de Contas de Segurança local (SAM).</p>As definições e eventos de Logon e Logoff rastreiam as tentativas de acesso a um determinado computador. As configurações e eventos desta categoria concentram-se na base de dados da conta que é utilizada. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditar validação de credenciais](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Auditar serviço de autenticação do Kerberos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Auditar operações de permissão de serviço do Kerberos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Auditar Outros Eventos de Logon/Logoff](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Gestão de Contas|Audita alterações nas contas e grupos de utilizadores e computadores. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditar gestão de grupo de aplicações](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Auditar gestão de contas de computador](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Auditar gestão de grupo de distribuição](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Auditoria Outra Gestão de Conta](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Auditar gestão de grupos de segurança](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Auditar gestão de conta de utilizadores](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| Rastreio de Detalhes|Audita atividades de aplicações individuais e utilizadores nesse computador, e para entender como um computador está sendo usado. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditar atividade de DPAPI](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[Atividade pNP de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[Criação de processo de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[Auditar termo de processo](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[Auditar eventos de RPC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Acesso a Serviços de Diretório|Auditorias tenta aceder e modificar objetos em Serviços de Domínio de Diretório Ativo (AD DS). Estes eventos de auditoria são registados apenas em controladores de domínio. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditar replicação detalhada de serviços de diretório](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Auditar acesso a serviços de diretório](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Auditar alterações de serviços de diretório](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Auditar replicação de serviços de diretório](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Logon-Logoff|As auditorias tentam iniciar sessão num computador interactivamente ou sobre uma rede. Estes eventos são úteis para rastrear a atividade do utilizador e identificar potenciais ataques aos recursos da rede. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditar bloqueio de conta](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Reclamações de utilizador/dispositivo de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[Auditar modo expandido de IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Associação de Grupos de Auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[Auditar modo principal de IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[Auditar modo rápido de IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Auditar fim de sessão](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Auditar início de sessão](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logon)</li><li>[Auditar Servidor de Políticas de Rede](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Auditar Outros Eventos de Logon/Logoff](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Auditar início de sessão especial](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Acesso a Objetos| As auditorias tentam aceder a objetos ou tipos específicos de objetos numa rede ou computador. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditar aplicação gerada](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Auditar serviços de certificação](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Auditar partilha de ficheiros detalhada](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Auditar partilha de ficheiros](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-share)</li><li>[Sistema de Ficheiros de Auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Auditar ligação de plataforma de filtragem](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Auditar largada de pacotes de plataforma de filtragem](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Manipulação de manipulação de manipulação de manipulação](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Objeto kernel de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Auditar outros eventos de acesso de objetos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Registo de Auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-registry)</li><li>[Auditoria Armazenamento Amovível](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[Auditar SAM](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sam)</li><li>[Fase da Política Central de Acesso de Auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|Mudança de Política|Audita alterações a políticas de segurança importantes num sistema ou rede local. As políticas são tipicamente estabelecidas pelos administradores para ajudar a garantir recursos de rede. Monitorizar alterações ou tentativas de alterar estas políticas pode ser um aspeto importante da gestão da segurança para uma rede. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditar alteração de política de auditoria](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Auditar alteração de política de autenticação](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Auditar alteração de política de autorização](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Auditar alteração de políticas de plataforma de filtragem](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[Auditar alteração de política de nível de regra MPSSVC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Auditoria Outra mudança de política](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Uso privilegiado| Audita a utilização de determinadas permissões num ou mais sistemas. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditar utilização de privilégio não confidenciais](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Auditar utilização de privilégio confidenciais](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Auditar outros eventos de utilização de privilégios](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|Sistema| Audita alterações ao nível do sistema a um computador não incluído noutras categorias e que têm potenciais implicações de segurança. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditar controlador IPSec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Auditar outros eventos do sistema](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Auditar de alteração de estado de segurança](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Auditar extensão de sistema de segurança](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Auditar integridade do sistema](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>IDs de eventopor categoria

 As auditorias de segurança da Azure AD DS registam as seguintes IDs do evento quando a ação específica desencadeia um evento auditável:

| Nome da categoria de evento | IDs de eventos |
|:---|:---|
|Segurança logon de conta|4767, 4774, 4775, 4776, 4777|
|Segurança na Gestão de Conta|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|Segurança de rastreio de detalhes|Nenhuma|
|Segurança de acesso dS|5136, 5137, 5138, 5139, 5141|
|Segurança Logon-Logoff|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Segurança de Acesso a Objetos|Nenhuma|
|Segurança de Mudança de Política|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|Privilégio Usar segurança|4985|
|Segurança do sistema|4612, 4621|

## <a name="next-steps"></a>Passos seguintes

Para obter informações específicas sobre Kusto, consulte os seguintes artigos:

* [Visão geral](/azure/kusto/query/) da linguagem de consulta kusto.
* [Tutorial de Kusto](/azure/kusto/query/tutorial) para familiarizá-lo com o básico da consulta.
* [Experimente consultas](/azure/kusto/query/samples) que o ajudam a aprender novas formas de ver os seus dados.
* As [boas práticas](/azure/kusto/query/best-practices) da Kusto para otimizar as suas consultas de sucesso.

<!-- LINKS - Internal -->
[migrate-azure-adds]: migrate-from-classic-vnet.md
