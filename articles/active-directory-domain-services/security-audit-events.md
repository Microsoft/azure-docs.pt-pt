---
title: Permitir auditorias de segurança para serviços de domínio Azure AD Microsoft Docs
description: Saiba como permitir que as auditorias de segurança centralizem o registo de eventos para análise e alertas nos Serviços de Domínio AD Azure
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 98e64f1ea1196b6fd773514d4dad5b6e178a6898
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88722539"
---
# <a name="enable-security-audits-for-azure-active-directory-domain-services"></a>Permitir auditorias de segurança para serviços de domínio de diretório ativo Azure

As auditorias de segurança Azure Ative Directory Domain Services (Azure AD DS) permitem que a Azure transmita eventos de segurança para recursos direcionados. Estes recursos incluem Azure Storage, Azure Log Analytics espaços de trabalho ou Azure Event Hub. Depois de ativar eventos de auditoria de segurança, a Azure AD DS envia todos os eventos auditados para a categoria selecionada para o recurso direcionado.

Você pode arquivar eventos em armazenamento Azure e transmitir eventos em software de informação de segurança e gestão de eventos (OU equivalente) usando Azure Event Hubs, ou fazer a sua própria análise e usando espaços de trabalho Azure Log Analytics a partir do portal Azure.

> [!IMPORTANT]
> As auditorias de segurança da Azure AD DS só estão disponíveis para domínios geridos baseados no Azure Resource Manager. Para obter informações sobre como migrar, consulte [o AD AD Migração Azure do modelo de rede virtual Clássico para o Gestor de Recursos.][migrate-azure-adds]

## <a name="security-audit-destinations"></a>Destinos de auditoria de segurança

Você pode usar Azure Storage, Azure Event Hubs ou Azure Log Analytics espaços de trabalho como um recurso alvo para auditorias de segurança Azure AD DS. Estes destinos podem ser combinados. Por exemplo, você poderia usar o Azure Storage para arquivar eventos de auditoria de segurança, mas um espaço de trabalho Azure Log Analytics para analisar e reportar sobre a informação a curto prazo.

A tabela seguinte descreve cenários para cada tipo de recurso de destino.

> [!IMPORTANT]
> Tem de criar o recurso-alvo antes de ativar as auditorias de segurança da Azure AD DS. Pode criar estes recursos utilizando o portal Azure PowerShell ou o Azure CLI.

| Recurso-alvo | Cenário |
|:---|:---|
|Storage do Azure| Este alvo deve ser utilizado quando a sua necessidade principal é armazenar eventos de auditoria de segurança para fins de arquivo. Outros alvos podem ser usados para fins de arquivo, no entanto esses alvos fornecem capacidades para além da necessidade primária de arquivamento. <br /><br />Antes de ativar eventos de auditoria de segurança Azure AD DS, primeiro [Crie uma conta de Armazenamento Azure](../storage/common/storage-account-create.md).|
|Azure Event Hubs| Este alvo deve ser utilizado quando a sua necessidade principal é partilhar eventos de auditoria de segurança com software adicional, como software de análise de dados ou informações de segurança & software de gestão de eventos (SIEM).<br /><br />Antes de ativar eventos de auditoria de segurança Azure AD DS, [Crie um centro de eventos usando o portal Azure](../event-hubs/event-hubs-create.md)|
|Espaço de trabalho Azure Log Analytics| Este alvo deve ser utilizado quando a sua necessidade principal é analisar e rever diretamente auditorias seguras do portal Azure.<br /><br />Antes de ativar eventos de auditoria de segurança Azure AD DS, [Crie um espaço de trabalho Log Analytics no portal Azure.](../azure-monitor/learn/quick-create-workspace.md)|

## <a name="enable-security-audit-events-using-the-azure-portal"></a>Permitir eventos de auditoria de segurança utilizando o portal Azure

Para ativar eventos de auditoria de segurança Azure AD DS utilizando o portal Azure, complete os seguintes passos.

> [!IMPORTANT]
> As auditorias de segurança da AD DS do Azure não são retroativas. Não se pode recuperar ou reproduzir eventos do passado. O Azure AD DS só pode enviar eventos que ocorram após a ativação das auditorias de segurança.

1. Inicie sessão no portal do Azure em https://portal.azure.com.
1. No topo do portal Azure, procure e selecione **serviços de domínio Azure AD**. Escolha o seu domínio gerido, como *aaddscontoso.com*.
1. Na janela Azure AD DS, selecione **as definições** de diagnóstico no lado esquerdo.
1. Nenhum diagnóstico é configurado por defeito. Para começar, **selecione Adicionar a definição de diagnóstico**.

    ![Adicione uma definição de diagnóstico para serviços de domínio AD AZure](./media/security-audit-events/add-diagnostic-settings.png)

1. Introduza um nome para a configuração de diagnóstico, como *aadds-auditing*.

    Verifique a caixa para o destino de auditoria de segurança que deseja. Você pode escolher entre uma conta de Armazenamento Azure, um centro de eventos Azure ou um espaço de trabalho Log Analytics. Estes recursos de destino já devem existir na sua assinatura Azure. Não se pode criar os recursos de destino neste feiticeiro.

    ![Permitir o destino e tipo de eventos de auditoria necessários para capturar](./media/security-audit-events/diagnostic-settings-page.png)

    * **Armazenamento azul**
        * Selecione **Archive para uma conta de armazenamento**e, em seguida, escolha **Configurar**.
        * Selecione a **Subscrição** e a **conta de Armazenamento** que pretende utilizar para arquivar eventos de auditoria de segurança.
        * Quando estiver pronto, escolha **OK**.
    * **Hubs de eventos Azure**
        * Selecione **Stream para um centro de eventos**e, em seguida, escolha **Configurar**.
        * Selecione a **Subscrição** e o **espaço de nomes do centro do evento.** Se necessário, escolha também um **nome de hub de Evento** e, em seguida, nome de política do centro de **eventos**.
        * Quando estiver pronto, escolha **OK**.
    * **Espaços de trabalho Azure Log Analytic**
        * Selecione **Enviar para Registar Analytics,** em seguida, escolha o **espaço de trabalho** **subscrição** e log analytics que pretende utilizar para armazenar eventos de auditoria de segurança.

1. Selecione as categorias de registo que deseja incluídas para o recurso-alvo específico. Se enviar os eventos de auditoria para uma conta de Armazenamento Azure, também pode configurar uma política de retenção que define o número de dias para reter dados. Uma definição predefinitiva de *0* retém todos os dados e não gira eventos após um período de tempo.

    Pode selecionar diferentes categorias de registo para cada recurso direcionado numa única configuração. Esta capacidade permite-lhe escolher quais as categorias de registos que pretende guardar para o Log Analytics e quais as categorias que pretende arquivar, por exemplo.

1. Quando terminar, **selecione Guardar** para cometer as suas alterações. Os recursos-alvo começam a receber eventos de auditoria de segurança Azure AD DS logo após a configuração ser guardada.

## <a name="enable-security-audit-events-using-azure-powershell"></a>Permitir eventos de auditoria de segurança usando a Azure PowerShell

Para ativar eventos de auditoria de segurança Azure AD DS utilizando a Azure PowerShell, complete os seguintes passos. Se necessário, instale primeiro [o módulo Azure PowerShell e ligue-se à sua subscrição Azure](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> As auditorias de segurança da AD DS do Azure não são retroativas. Não se pode recuperar ou reproduzir eventos do passado. O Azure AD DS só pode enviar eventos que ocorram após a ativação das auditorias de segurança.

1. Autenticar a sua assinatura Azure utilizando o [cmdlet Connect-AzAccount.](/powershell/module/Az.Accounts/Connect-AzAccount) Quando solicitado, insira as credenciais da sua conta.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Crie o recurso-alvo para os eventos de auditoria de segurança.

    * **Armazenamento azul**  -  [Criar uma conta de armazenamento usando a Azure PowerShell](../storage/common/storage-account-create.md?tabs=azure-powershell)
    * **Hubs de eventos**  -  Azure [Crie um centro de eventos utilizando a Azure PowerShell](../event-hubs/event-hubs-quickstart-powershell.md). Poderá também ser necessário utilizar o cmdlet [New-AzEventHubAuthorizationRule](/powershell/module/az.eventhub/new-azeventhubauthorizationrule) para criar uma regra de autorização que conceda permissões de DS AD AD do evento para o *espaço de nome do*centro de eventos. A regra de autorização deve incluir os **direitos de Gestão,** **Escuta**e **Envio.**

        > [!IMPORTANT]
        > Certifique-se de que define a regra de autorização no espaço de nome do centro de eventos e não no próprio centro de eventos.

    * **Espaços de**  -  trabalho Azure Log Analytic [Criar um espaço de trabalho Log Analytics com Azure PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md).

1. Obtenha o ID de recurso para o seu domínio gerido Azure AD DS utilizando o cmdlet [Get-AzResource.](/powershell/module/Az.Resources/Get-AzResource) Criar uma variável chamada *$aadds. ResourceId* para manter o valor:

    ```azurepowershell
    $aadds = Get-AzResource -name aaddsDomainName
    ```

1. Configure as definições de Diagnóstico Azure utilizando o [cmdlet Set-AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) para utilizar o recurso-alvo para eventos de auditoria de segurança dos Serviços de Domínio Azure AD. Nos exemplos seguintes, a variável *$aadds. O ResourceId* é utilizado a partir do passo anterior.

    * **Armazenamento Azure** - Substitua *o armazenamentoAccountId* pelo nome da sua conta de armazenamento:

        ```powershell
        Set-AzDiagnosticSetting `
            -ResourceId $aadds.ResourceId `
            -StorageAccountId storageAccountId `
            -Enabled $true
        ```

    * **Hubs de eventos Azure** - Substitua o *eventHubName* pelo nome do seu centro de eventos e *eventoHubRuleId* com a sua regra de autorização ID:

        ```powershell
        Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId `
            -EventHubName eventHubName `
            -EventHubAuthorizationRuleId eventHubRuleId `
            -Enabled $true
        ```

    * **Azure Log Espaços de trabalho analíticos** - Substitua o *espaço de trabalhoId* pelo ID do espaço de trabalho Log Analytics:

        ```powershell
        Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId `
            -WorkspaceID workspaceId `
            -Enabled $true
        ```

## <a name="query-and-view-security-audit-events-using-azure-monitor"></a>Consultar e ver eventos de auditoria de segurança usando o Azure Monitor

Log Espaços de trabalho analíticos permite-lhe ver e analisar os eventos de auditoria de segurança usando o Azure Monitor e a linguagem de consulta Kusto. Esta linguagem de consulta é projetada para uso apenas de leitura que possui capacidades analíticas de poder com uma sintaxe fácil de ler. Para obter mais informações para começar com as línguas de consulta de Kusto, consulte os seguintes artigos:

* [Documentação do Azure Monitor](../azure-monitor/index.yml)
* [Começa com o Log Analytics no Azure Monitor](../azure-monitor/log-query/get-started-portal.md)
* [Introdução às consultas de registos no Azure Monitor](../azure-monitor/log-query/get-started-queries.md)
* [Criar e partilhar dashboards dos dados do Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

As seguintes consultas de amostra podem ser usadas para começar a analisar eventos de auditoria de segurança a partir de Azure AD DS.

### <a name="sample-query-1"></a>Consulta de amostra 1

Veja todos os eventos de bloqueio de conta nos últimos sete dias:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>Consulta de amostra 2

Veja todos os eventos de bloqueio de conta *(4740*) entre 3 de junho de 2020 às 9 da manhã. e 10 de junho de 2020 meia-noite, classificado subindo pela data e hora:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-06-03 09:00) and TimeGenerated <= datetime(2020-06-10)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>Consulta de amostra 3

Ver eventos de inscrição de conta há sete dias (a partir de agora) para a conta nomeada utilizador:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>Consulta de amostra 4

Ver eventos de entrada de conta há sete dias para a conta nomeada utilizador que tentou assinar usando uma senha má *(0xC000006a):*

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>Consulta de amostra 5

Ver eventos de inscrição de conta há sete dias para a conta nomeada utilizador que tentou entrar enquanto a conta estava bloqueada *(0xC0000234):*

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>Consulta de amostra 6

Veja o número de eventos de inscrição de conta há sete dias para todas as tentativas de inscrição que ocorreram para todos os utilizadores bloqueados:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="audit-event-categories"></a>Categorias de eventos de auditoria

As auditorias de segurança Azure AD DS alinham-se com a auditoria tradicional para controladores de domínio AD DS tradicionais. Em ambientes híbridos, pode reutilizar os padrões de auditoria existentes para que a mesma lógica possa ser usada na análise dos eventos. Dependendo do cenário que precisa para resolver problemas ou analisar, as diferentes categorias de eventos de auditoria precisam de ser direcionadas.

Estão disponíveis as seguintes categorias de eventos de auditoria:

| Nome da categoria de auditoria | Descrição |
|:---|:---|
| Início de conta|As auditorias tentam autenticar dados de conta num controlador de domínio ou num Gestor de Contas de Segurança local (SAM).</p>As definições de políticas de logon e logoff rastreiam as tentativas de acesso a um determinado computador. As configurações e eventos desta categoria concentram-se na base de dados de conta que é utilizada. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditar validação de credenciais](/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Auditar serviço de autenticação do Kerberos](/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Auditar operações de permissão de serviço do Kerberos](/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Auditoria Outros Eventos logon/Logoff](/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Gestão de Contas|Auditorias alterações nas contas e grupos de utilizadores e computadores. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditar gestão de grupo de aplicações](/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Auditar gestão de contas de computador](/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Auditar gestão de grupo de distribuição](/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Auditoria Outra Gestão de Contas](/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Auditar gestão de grupos de segurança](/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Auditar gestão de conta de utilizadores](/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| Rastreio de detalhes|Audita atividades de aplicações individuais e utilizadores nesse computador, e para entender como um computador está sendo usado. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditar atividade de DPAPI](/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[Atividade do PNP de auditoria](/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[Criação de processo de auditoria](/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[Auditar termo de processo](/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[Auditar eventos de RPC](/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Acesso de Serviços diretórios|As auditorias tentam aceder e modificar objetos em Serviços de Domínio de Diretório Ativo (DS AD). Estes eventos de auditoria são registados apenas em controladores de domínio. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditar replicação detalhada de serviços de diretório](/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Auditar acesso a serviços de diretório](/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Auditar alterações de serviços de diretório](/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Auditar replicação de serviços de diretório](/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Logon-Logoff|As auditorias tentam iniciar sessão num computador de forma interativa ou sobre uma rede. Estes eventos são úteis para rastrear a atividade do utilizador e identificar potenciais ataques a recursos de rede. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditar bloqueio de conta](/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Reclamações de utilizador/dispositivo de auditoria](/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[Auditar modo expandido de IPsec](/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Membro do Grupo de Auditoria](/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[Auditar modo principal de IPsec](/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[Auditar modo rápido de IPsec](/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Auditar fim de sessão](/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Auditar início de sessão](/windows/security/threat-protection/auditing/audit-logon)</li><li>[Auditar Servidor de Políticas de Rede](/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Auditoria Outros Eventos logon/Logoff](/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Auditar início de sessão especial](/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Acesso a Objetos| As auditorias tentam aceder a objetos ou tipos de objetos específicos numa rede ou computador. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditar aplicação gerada](/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Auditar serviços de certificação](/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Auditar partilha de ficheiros detalhada](/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Auditar partilha de ficheiros](/windows/security/threat-protection/auditing/audit-file-share)</li><li>[Sistema de Ficheiros de Auditoria](/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Auditar ligação de plataforma de filtragem](/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Auditar largada de pacotes de plataforma de filtragem](/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Manipulação do Cabo de Auditoria](/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Objeto Kernel de auditoria](/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Auditar outros eventos de acesso de objetos](/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Registo de Auditoria](/windows/security/threat-protection/auditing/audit-registry)</li><li>[Auditoria Armazenamento Amovível](/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[Auditar SAM](/windows/security/threat-protection/auditing/audit-sam)</li><li>[Fase da Política Central de Acesso de Auditoria](/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|Mudança de Política|Auditorias alterações a políticas de segurança importantes num sistema ou rede local. As políticas são tipicamente estabelecidas por administradores para ajudar a garantir os recursos da rede. Monitorizar alterações ou tentativas de alteração destas políticas pode ser um aspeto importante da gestão da segurança de uma rede. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditar alteração de política de auditoria](/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Auditar alteração de política de autenticação](/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Auditar alteração de política de autorização](/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Auditar alteração de políticas de plataforma de filtragem](/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[Auditar alteração de política de nível de regra MPSSVC](/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Auditoria Outras Alterações de Política](/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Uso de Privilégios| Audita a utilização de determinadas permissões num ou mais sistemas. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditar utilização de privilégio não confidenciais](/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Auditar utilização de privilégio confidenciais](/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Auditar outros eventos de utilização de privilégios](/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|Sistema| Auditoria alterações ao nível do sistema a um computador não incluído noutras categorias e que têm potenciais implicações de segurança. Esta categoria inclui as seguintes subcategorias:<ul><li>[Auditar controlador IPSec](/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Auditar outros eventos do sistema](/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Auditar de alteração de estado de segurança](/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Auditar extensão de sistema de segurança](/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Auditar integridade do sistema](/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>IDs de evento por categoria

 As auditorias de segurança da Azure AD DS registam os seguintes IDs de evento quando a ação específica desencadeia um evento auditável:

| Nome da categoria de evento | IDs do evento |
|:---|:---|
|Segurança de logon conta|4767, 4774, 4775, 4776, 4777|
|Segurança de gestão de conta|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|Segurança de rastreio de detalhes|Nenhum|
|Segurança de acesso DS|5136, 5137, 5138, 5139, 5141|
|segurança Logon-Logoff|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Segurança de acesso a objetos|Nenhum|
|Segurança de mudança de política|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|Segurança de uso de privilégios|4985|
|Segurança do sistema|4612, 4621|

## <a name="next-steps"></a>Passos seguintes

Para obter informações específicas sobre Kusto, consulte os seguintes artigos:

* [Visão geral](/azure/kusto/query/) da língua de consulta de Kusto.
* [Tutorial de Kusto](/azure/kusto/query/tutorial) para familiarizá-lo com o básico da consulta.
* [Consultas de amostras](/azure/kusto/query/samples) que o ajudam a aprender novas formas de ver os seus dados.
* Kusto [é uma das melhores práticas](/azure/kusto/query/best-practices) para otimizar as suas consultas para o sucesso.

<!-- LINKS - Internal -->
[migrate-azure-adds]: migrate-from-classic-vnet.md