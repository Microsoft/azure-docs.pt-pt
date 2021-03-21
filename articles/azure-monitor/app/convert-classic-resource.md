---
title: Migrar um recurso clássico de Aplicação Azure Monitor Insights para um recurso baseado no espaço de trabalho | Microsoft Docs
description: Conheça os passos necessários para atualizar o seu recurso clássico Azure Monitor Application Insights para o novo modelo baseado no espaço de trabalho.
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 5791abe33dee2e62aadb00ae1024338e1e44a900
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100584249"
---
# <a name="migrate-to-workspace-based-application-insights-resources"></a>Migrar para recursos de Insights de Aplicação baseados no espaço de trabalho

Este guia irá acompanhá-lo através do processo de migração de um recurso clássico application Insights para um recurso baseado no espaço de trabalho. Os recursos baseados no espaço de trabalho suportam a integração total entre o Application Insights e o Log Analytics. Os recursos baseados no espaço de trabalho enviam telemetria Application Insights para um espaço de trabalho comum do Log Analytics, que lhe permite aceder às [mais recentes funcionalidades do Azure Monitor,](#new-capabilities) mantendo os registos de aplicações, infraestruturas e plataformas num único local consolidado.

Os recursos baseados no espaço de trabalho permitem o controlo comum de acesso baseado em funções (Azure RBAC) através dos seus recursos, e elimina a necessidade de consultas cruzadas/espaço de trabalho.

**Os recursos baseados no espaço de trabalho estão atualmente disponíveis em todas as regiões comerciais e no Governo dos EUA**

## <a name="new-capabilities"></a>Novas funcionalidades

O Workspace Application Insights permite-lhe tirar partido de todas as mais recentes capacidades do Azure Monitor e do Log Analytics, incluindo:

* [As Chaves Geridas pelo Cliente (CMK)](../logs/customer-managed-keys.md) fornecem encriptação em repouso para os seus dados com chaves de encriptação às quais só tem acesso.
* [O Azure Private Link](../logs/private-link-security.md) permite-lhe ligar de forma segura os serviços Azure PaaS à sua rede virtual utilizando pontos finais privados.
* [Bring Your Own Storage (BYOS) for Profiler and Snapshot Debugger](./profiler-bring-your-own-storage.md) dá-lhe total controlo sobre a política de encriptação em repouso, a política de gestão vitalícia e o acesso à rede para todos os dados associados ao Profiler Application Insights e ao Snapshot Debugger. 
* [Os níveis de Reserva de Capacidade](../logs/manage-cost-storage.md#pricing-model) permitem-lhe economizar até 25% em comparação com o preço Pay-As-You-Go. 
* Ingestão de dados mais rápida via Log Analytics streaming ingestão.

## <a name="migration-process"></a>Processo de migração

Quando migra para um recurso baseado no espaço de trabalho, nenhum dado é transferido do armazenamento do seu recurso clássico para o novo armazenamento baseado no espaço de trabalho. A escolha de migrar irá, em vez disso, alterar a localização onde novos dados são escritos num espaço de trabalho do Log Analytics, preservando o acesso aos seus dados clássicos de recursos. 

Os seus dados clássicos de recursos persistirão e estarão sujeitos às definições de retenção no seu recurso clássico Application Insights. Todos os novos dados ingeridos após a migração estarão sujeitos às definições de [retenção](../logs/manage-cost-storage.md#change-the-data-retention-period) do espaço de trabalho associado do Log Analytics, que também suporta [diferentes definições de retenção por tipo de dados.](../logs/manage-cost-storage.md#retention-by-data-type)
O processo de migração é **permanente e não pode ser invertido.** Uma vez migrar um recurso para o Workspace Application Insights baseado no espaço de trabalho, será sempre um recurso baseado no espaço de trabalho. No entanto, uma vez migrado, é capaz de alterar o espaço de trabalho alvo sempre que necessário. 

> [!NOTE]
> A ingestão de dados e a retenção de recursos de Insights de Aplicação baseados no espaço de trabalho são [faturados através do espaço de trabalho Log Analytics](../logs/manage-cost-storage.md) onde os dados estão localizados. Se selecionou uma retenção de dados superior a 90 dias sobre os dados ingeridos no recurso Classic Application Insights antes da migração, a retenção de dados continuará a ser faturada através desse recurso Application Insights. [Saiba mais]( ./pricing.md#workspace-based-application-insights) sobre a faturação dos recursos de Aplicações Insights baseados no espaço de trabalho.

Se não precisar de migrar um recurso existente e, em vez disso, pretender criar um novo recurso de Aplicações Insights baseado no espaço de trabalho, utilize o [guia de criação de recursos baseado no espaço de trabalho.](create-workspace-resource.md)

## <a name="pre-requisites"></a>Pré-requisitos 

- Um espaço de trabalho Log Analytics com o modo de controlo de acesso definido para a **`use resource or workspace permissions`** definição. 

    - Os recursos de Insights de Aplicação baseados no espaço de trabalho não são compatíveis com espaços de trabalho definidos para a **`workspace based permissions`** definição dedicada. Para saber mais sobre o controlo do espaço de trabalho log Analytics, consulte a [orientação](../logs/manage-access.md#configure-access-control-mode) do modo de controlo de acesso de log Analytics

    - Se ainda não tiver um espaço de trabalho log analytics existente, [consulte a documentação de criação do espaço de trabalho Log Analytics](../logs/quick-create-workspace.md).
    
- As exportações contínuas não são apoiadas por recursos baseados no espaço de trabalho e devem ser desativadas.
Uma vez concluída a migração, pode utilizar [definições de diagnóstico](../essentials/diagnostic-settings.md) para configurar o arquivo de dados para uma conta de armazenamento ou streaming para o Azure Event Hub.  

- Verifique as definições de retenção atuais em Uso **Geral**  >  **e custos estimados**  >  **Retenção de Dados** para o seu espaço de trabalho Log Analytics. Esta definição terá impacto quanto tempo quaisquer novos dados ingeridos são armazenados assim que migrar o seu recurso Application Insights. Se atualmente armazenar dados de Insights de Aplicação por mais de 90 dias e pretender reter este período de retenção maior, poderá ter de ajustar as definições de retenção do espaço de trabalho.

## <a name="migrate-your-resource"></a>Migrar o seu recurso

Esta secção irá acompanhá-lo através do processo de migração de um recurso clássico application Insights para o novo tipo de recursos baseado no espaço de trabalho.

1. A partir do seu recurso Application Insights, selecione **Propriedades** sob o **título Configure** na barra de menus da esquerda.

    ![Propriedades destacadas na caixa vermelha](./media/convert-classic-resource/properties.png)

2. Selecione **`Migrate to Workspace-based`**.
    
     ![Botão de recursos migrar](./media/convert-classic-resource/migrate.png)

3. Escolha o Log Analytics Workspace onde pretende que todos os futuros insights de aplicação ingeridos sejam armazenados.

     ![UI do assistente de migração com opção de selecionar espaço de trabalho targe](./media/convert-classic-resource/migration.png)
    

Uma vez migrado o seu recurso, verá a informação do espaço de trabalho correspondente no painel **de visão** geral:

![Nome da Área de Trabalho](./media/create-workspace-resource/workspace-name.png)

Clicar no texto de ligação azul irá levá-lo ao espaço de trabalho associado do Log Analytics onde poderá tirar partido do novo ambiente de consulta de espaço de trabalho unificado.

## <a name="understanding-log-queries"></a>Compreender consultas de registo

Ainda fornecemos compatibilidade total para trás para as suas consultas clássicas de recursos, livros de trabalho e alertas baseados em registos dentro da experiência Application Insights. 

Para escrever consultas contra a [nova estrutura/esquema de mesa baseada no espaço de trabalho,](apm-tables.md)você deve primeiro navegar para o seu espaço de trabalho Log Analytics. 

Quando consultar diretamente a partir da UI Log Analytics dentro do seu espaço de trabalho, só verá os dados que são ingeridos após a migração. Para ver os dados clássicos do Application Insights + novos dados ingeridos após a migração numa experiência de consulta unificada, utilize a vista de consulta Logs (Analytics) dentro do seu recurso Application Insights migrado.

## <a name="programmatic-resource-migration"></a>Migração de recursos programáticos

### <a name="azure-cli"></a>CLI do Azure

Para aceder aos comandos de Pré-visualização Do CLI do Application Insights, primeiro tem de executar:

```azurecli
 az extension add -n application-insights
```

Se não executar o `az extension add` comando, verá uma mensagem de erro que diz: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

Agora pode executar o seguinte para criar o seu recurso Application Insights:

```azurecli
az monitor app-insights component update --app
                                         --resource-group
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--query-access {Disabled, Enabled}]
                                         [--retention-time]
                                         [--workspace]
```

#### <a name="example"></a>Exemplo

```azurecli
az monitor app-insights component update --app your-app-insights-resource-name -g your_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

Para obter a documentação completa do Azure CLI para este comando, consulte a documentação do [Azure CLI](/cli/azure/ext/application-insights/monitor/app-insights/component#ext-application-insights-az-monitor-app-insights-component-update).

### <a name="azure-powershell"></a>Azure PowerShell

O `Update-AzApplicationInsights` comando PowerShell não suporta atualmente a migração de um recurso clássico de Application Insights para base no espaço de trabalho. Para criar um recurso baseado no espaço de trabalho com o PowerShell, pode utilizar os modelos Azure Resource Manager abaixo e implementar com o PowerShell.

### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

#### <a name="template-file"></a>Arquivo de modelo

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### <a name="parameters-file"></a>Arquivo de parâmetros

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## <a name="modifying-the-associated-workspace"></a>Modificação do espaço de trabalho associado

Uma vez criado um recurso de Insights de Aplicação baseado no espaço de trabalho, pode modificar o espaço de trabalho associado do Log Analytics.

A partir do painel de recursos Do Application Insights, selecione **Propriedades**  >  **Change Workspace**  >  **Analytics Workspace**.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="access-mode"></a>Modo de acesso

**Mensagem de erro:** *O espaço de trabalho selecionado está configurado com o modo de acesso baseado no espaço de trabalho. Algumas funcionalidades de APM podem ser impactadas. Selecione outro espaço de trabalho ou permita o acesso baseado em recursos nas definições do espaço de trabalho. Pode anular este erro utilizando o CLI.* 

Para que o seu recurso Application Insights baseado no espaço de trabalho funcione corretamente, é necessário alterar o modo de controlo de acesso do seu espaço de trabalho target Log Analytics para a definição de **permissões de recursos ou espaço de trabalho.** Esta definição está localizada no espaço de trabalho do Log Analytics UI no modo de controlo de acesso **ao properties**  >  . Para obter instruções detalhadas, consulte a orientação do modo de controlo de acesso de [configuração do Log Analytics](../logs/manage-access.md#configure-access-control-mode). Se o seu modo de controlo de acesso estiver definido para a definição exclusiva **de permissões de espaço de trabalho,** a migração através da experiência de migração do portal permanecerá bloqueada.

Se não conseguir alterar o modo de controlo de acesso por razões de segurança para o seu espaço de trabalho alvo atual, recomendamos a criação de um novo espaço de trabalho log Analytics para utilizar para a migração. 

### <a name="continuous-export"></a>Exportação contínua

**Error message:** *A Exportação Contínua tem de ser desativada antes de continuar. Após a migração, utilize Definições de Diagnóstico para exportação.* 

A funcionalidade de exportação contínua do legado não é suportada por recursos baseados no espaço de trabalho. Antes de migrar, é necessário desativar a exportação contínua.

1. A partir da sua visão de recurso Application Insights, sob a **rubrica Configure** selecione **Exportação Contínua**.

    ![Item do menu de exportação contínua](./media/convert-classic-resource/continuous-export.png)

2. **Selecione Desativar**.

    ![Botão de desativação contínua de exportação](./media/convert-classic-resource/disable.png)

- Uma vez selecionados desativar, pode navegar de volta para a UI de migração. Se a página de exportação contínua de edição lhe solicitar que as suas definições não sejam guardadas, pode selecionar ok para esta solicitação, uma vez que não diz respeito à desativação/ativação da exportação contínua.

- Uma vez migrado com sucesso o seu recurso Application Insights para o espaço de trabalho, pode utilizar definições de Diagnóstico para substituir a funcionalidade que a exportação contínua usou para fornecer. Selecione **as definições** de Diagnóstico  >  **adicione a definição** de diagnóstico a partir do seu recurso Application Insights. Pode selecionar todas as tabelas ou um subconjunto de tabelas para arquivar numa conta de armazenamento ou para transmitir para um Azure Event Hub. Para obter orientações detalhadas sobre as definições de diagnóstico, consulte a orientação de diagnóstico do [Monitor Azure](../essentials/diagnostic-settings.md).

### <a name="retention-settings"></a>Definições de retenção

**Mensagem de Aviso:** *As definições de retenção personalizadas de Insights de Aplicação não se aplicarão aos dados enviados para o espaço de trabalho. Terá de reconfigurar isto separadamente.*

Não tem de fazer quaisquer alterações antes de migrar, mas esta mensagem é para o alertar de que as definições atuais de retenção de Insights de Aplicação não estão definidas para o período de retenção de 90 dias predefinido. Esta mensagem de aviso significa que pode querer modificar as definições de retenção para o seu espaço de trabalho Log Analytics antes de migrar e começar a ingerir novos dados. 

Pode verificar as definições de retenção atuais para Log Analytics em Uso **Geral**  >  **e custos estimados**  >  **Retenção** de Dados a partir do UI do Log Analytics. Esta definição terá impacto quanto tempo quaisquer novos dados ingeridos são armazenados assim que migrar o seu recurso Application Insights.

## <a name="next-steps"></a>Passos seguintes

* [Explorar métricas](../essentials/metrics-charts.md)
* [Escrever consultas da Análise](../logs/log-query-overview.md)
