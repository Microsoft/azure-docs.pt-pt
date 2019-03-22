---
title: Descrição geral do registo de atividades do Azure
description: Saiba o que é o registo de atividades do Azure e como pode usá-lo a compreender os eventos que ocorrem na sua subscrição do Azure.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: fb3ed970b7f92e1cc06a9d1023e01f5888915e94
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58088677"
---
# <a name="monitor-subscription-activity-with-the-azure-activity-log"></a>Monitorizar a atividade de subscrição com o registo de atividades do Azure

O **registo de atividades do Azure** é um registo de subscrição que fornece informações sobre os eventos de nível de assinatura que ocorreram no Azure. Isto inclui uma variedade de dados de dados operacionais do Azure Resource Manager para as atualizações em eventos de estado de funcionamento do serviço. O registo de atividades era conhecido anteriormente como "Registos de auditoria" ou "Registos operacionais", desde os eventos de plano de controlo de relatórios de categoria administrativa para as suas subscrições. Utilizar o registo de atividades, pode determinar o "o quê, quem e quando" para quaisquer operações (PUT, POST, DELETE) efetuadas nos recursos na sua subscrição de escrita. Também é possível compreender o estado da operação e outras propriedades relevantes. O registo de Atividades não incluem operações de leitura (GET) nem operações para recursos que utilizar clássica / modelo de "RDFE".

![Vs registos de atividade outros tipos de registos](./media/activity-logs-overview/Activity_Log_vs_other_logs_v5.png)

Figura 1: Vs registos de atividade outros tipos de registos

O registo de atividade difere [registos de diagnóstico](diagnostic-logs-overview.md). Registos de atividades fornecem dados sobre as operações num recurso de fora (o "plano de controlo"). Os registos de diagnóstico são emitidos por um recurso e fornecem informações sobre o funcionamento desse recurso (o "plano de dados").

> [!WARNING]
> O registo de atividades do Azure é principalmente para atividades que ocorrem no Azure Resource Manager. Não controla a recursos com o modelo de clássica/RDFE. Alguns tipos de recursos clássicos têm um fornecedor de recursos de proxy no Azure Resource Manager (por exemplo, Microsoft. classiccompute). Se interage com um tipo de recurso clássico através do Azure Resource Manager com estes fornecedores de recursos de proxy, as operações aparecem no registo de atividades. Se interage com um tipo de recurso clássico fora de proxies do Azure Resource Manager, suas ações só são registadas no registo de operação. O registo de operação pode ser navegado numa seção à parte do portal.
>
>

Pode obter eventos de seu registo de atividades no portal do Azure, CLI, cmdlets do PowerShell e a API de REST do Azure Monitor.

> [!NOTE]
> [Os alertas mais recentes](../../azure-monitor/platform/alerts-overview.md) oferecem uma experiência aprimorada, ao criar e gerir atividade regras de alerta de registo.  [Saiba mais](../../azure-monitor/platform/alerts-activity-log.md).


## <a name="categories-in-the-activity-log"></a>Categorias no registo de atividades
O registo de atividade contém várias categorias de dados. Para obter detalhes completos sobre esquemas uma destas categorias [veja este artigo](../../azure-monitor/platform/activity-log-schema.md). Estas incluem:
* **Administrativas** -nesta categoria contém o registo de todos os criar, operações de atualização, eliminação e ação executada através do Resource Manager. Os exemplos dos tipos de eventos que veria nesta categoria incluem "Criar máquina virtual" e "Eliminar grupo de segurança rede" todas as ações executadas por um utilizador ou aplicação com o Resource Manager é modelada como uma operação num tipo de recurso específico. Se o tipo de operação de escrita, eliminação ou ação, os registos de início e de êxito ou falha dessa operação é registada na categoria administrativa. A categoria administrativa também inclui todas as alterações ao controlo de acesso baseado em funções numa subscrição.
* **Estado de funcionamento do serviço** -nesta categoria contém o registo de qualquer incidentes de estado de funcionamento do serviço que ocorreram no Azure. Um exemplo do tipo de evento, que veria nesta categoria é "do SQL Azure na região E.U.A. Leste está a sofrer um período de indisponibilidade." Eventos de estado de funcionamento do serviço têm cinco variedades: É necessária qualquer ação, recuperação assistido, incidente, manutenção, informações ou segurança e só serão apresentadas se tiver um recurso na subscrição que seria afetada pelo evento.
* **Estado de funcionamento do recurso** -nesta categoria contém o registo de quaisquer eventos de estado de funcionamento do recurso que ocorreram aos recursos do Azure. Um exemplo do tipo de evento, que veria nesta categoria é o "Estado de funcionamento estado da Máquina Virtual foi alterado para indisponível." Eventos de estado de funcionamento de recursos podem representar um dos quatro Estados de estado de funcionamento: Disponível, indisponível, degradado e desconhecido. Além disso, os eventos de estado de funcionamento de recursos podem ser categorizados como sendo iniciado de plataforma ou utilizador.
* **Alerta** -nesta categoria contém o registo de todas as ativações de alertas do Azure. Um exemplo do tipo de evento, que veria nesta categoria é "% da CPU no myVM foi mais de 80 durante os últimos 5 minutos." Uma variedade de sistemas do Azure tem um conceito de alerta – pode definir uma regra de algum tipo e receber uma notificação quando as condições corresponderem essa regra. Sempre que um tipo de alerta do Azure suportado 'ativa,' ou as condições são cumpridas para gerar uma notificação, um registo da ativação também é emitidos via push para esta categoria de registo de atividades.
* **Dimensionamento automático** -nesta categoria contém o registo de quaisquer eventos relacionados com a operação do mecanismo de dimensionamento automático com base em quaisquer definições de dimensionamento automático que definiu na sua subscrição. Um exemplo do tipo de evento, que veria nesta categoria é "Aumento do dimensionamento automático a ação falhou." Utilizo o dimensionamento automático, pode automaticamente aumentar ou reduzir horizontalmente o número de instâncias num tipo de recurso suportados com base na hora do dia e/ou carga dados (métricas) através de uma definição de dimensionamento automático. Quando as condições são cumpridas para aumento ou redução vertical, o início e de eventos com êxito ou falhados são registrados nesta categoria.
* **Recomendação** -nesta categoria contém eventos de recomendação do Assistente do Azure.
* **Segurança** -nesta categoria contém o registo de todos os alertas gerados pelo centro de segurança do Azure. Um exemplo do tipo de evento, que veria nesta categoria é "ficheiro de extensão dupla suspeito executado".
* **Política** -nesta categoria contém registos de todas as operações de ação de efeito efetuadas pelo Azure Policy. Os tipos de eventos que veria nesta categoria exemplos de auditoria e negar. Cada ação tomada pela política é modelada como uma operação num recurso.

## <a name="event-schema-per-category"></a>Esquema de eventos por categoria
[Consulte este artigo para compreender o esquema de eventos de registo de atividades por categoria.](../../azure-monitor/platform/activity-log-schema.md)

## <a name="what-you-can-do-with-the-activity-log"></a>O que pode fazer com o registo de atividades
Aqui estão algumas das coisas que pode fazer com o registo de atividades:

![Registo de atividades do Azure](./media/activity-logs-overview/Activity_Log_Overview_v3.png)


* Consultar e vê-la na **portal do Azure**.
* [Crie um alerta num evento do registo de atividade.](../../azure-monitor/platform/activity-log-alerts.md)
* [Stream-lo para um **Hub de eventos** ](../../azure-monitor/platform/activity-logs-stream-event-hubs.md) para ingestão por um serviço de terceiros ou de uma solução de análise personalizada, como o Power BI.
* Analise-o no Power BI com o [ **pacote de conteúdos do Power BI**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
* [Guardá-lo para um **conta de armazenamento** para inspeção de arquivamento ou manual](../../azure-monitor/platform/archive-activity-log.md). Pode especificar o tempo (em dias) de retenção utilizando o **perfil de registo**.
* Consultá-los através do Cmdlet do PowerShell, CLI ou REST API.

## <a name="query-the-activity-log-in-the-azure-portal"></a>Consultar o registo de atividades no portal do Azure
No portal do Azure, pode ver o registo de atividades em vários locais:
* O **registo de atividades** que pode aceder ao procurar o registo de atividade em **todos os serviços** no painel de navegação esquerdo.
* **Monitor** aparece por predefinição no painel de navegação esquerdo. O registo de atividade é uma seção do Azure Monitor.
* A maioria dos **recursos**, por exemplo, o painel de configuração para uma Máquina Virtual. O registo de atividades é uma seção na maioria dos painéis de recursos e clicar no mesmo automaticamente filtra os eventos para aqueles relacionados a esse recurso específico.

No portal do Azure, pode filtrar o registo de atividades por estes campos:
* Período de tempo - a hora de início e fim para eventos.
* Categoria - a categoria de evento, conforme descrito acima.
* Subscrição - um ou mais nomes de subscrição do Azure.
* Grupo de recursos - recursos de um ou mais grupos no âmbito dessas subscrições.
* Recurso (nome) - o nome de um recurso específico.
* Tipo de recurso - o tipo de recurso, por exemplo, Microsoft.Compute/virtualmachines.
* Nome da operação - o nome de uma operação do Azure Resource Manager, por exemplo, Microsoft.SQL/servers/Write.
* Gravidade - o nível de gravidade do evento (informativo, aviso, erro, crítico).
* Evento iniciado por ' - ' o "chamador", ou o utilizador que executou a operação.
* Abra pesquisa - esta é uma caixa de pesquisa de texto em aberto que procura essa cadeia de caracteres em todos os campos em todos os eventos.

Depois de definir um conjunto de filtros, pode afixar uma consulta ao dashboard do Azure para sempre manter um olho nos eventos específicos.

Para ainda mais potência, pode clicar a **Logs** ícone, que exibe os dados de registo de atividades no [recolher e analisar a solução de registos de atividades](../../azure-monitor/platform/collect-activity-logs.md). O painel de registo de atividades oferece uma experiência de pesquisa/filtro básico em registos, mas o Azure Monitor registos funcionalidade permite dinâmico, consultar e visualize os seus dados de formas mais eficientes.

## <a name="export-the-activity-log-with-a-log-profile"></a>Exportar o registo de atividades com um perfil de registo
R **perfil de registo** controla a forma como o registo de atividades é exportado. Utilizar um perfil de registo, pode configurar:

* Em que o registo de atividades devem ser enviado (conta de armazenamento ou Hubs de eventos)
* As categorias de eventos (ação de escrita, eliminação,) devem ser enviadas. *O significado de "category" nos perfis de registo e eventos de registo de atividades é diferente. No perfil de registo, o "Category" representa o tipo de operação (ação de escrita, eliminação,). Num evento de registo de atividades, a propriedade de "category" representa a origem ou o tipo de evento (por exemplo, administração, ServiceHealth, alerta e muito mais).*
* Que regiões (localizações) devem ser exportados. Certifique-se incluir "global", como o número de eventos no registo de atividades é eventos globais.
* O tempo que o registo de atividades deve ser mantido numa conta de armazenamento.
    - A retenção de zero dias significa que os registos são mantidos para sempre. Caso contrário, o valor pode ser qualquer número de dias entre 1 e 2147483647.
    - Se as políticas de retenção são definidas, mas armazenamento de registos numa conta de armazenamento está desativado (por exemplo, se apenas as opções de Hubs de eventos ou o Log Analytics estão selecionadas), as políticas de retenção não têm efeito.
    - Políticas de retenção são aplicado por dia, portanto, no final do dia (UTC), registos a partir do dia em que está, agora, além de retenção de política são eliminadas. Por exemplo, se tivesse uma política de retenção de um dia, no início do dia hoje os registos de ontem de before dia serão eliminados. O processo de eliminação começa a meia-noite UTC, mas tenha em atenção que pode demorar até 24 horas para os registos para ser eliminado da sua conta de armazenamento.

Pode usar um armazenamento conta ou event hub espaço de nomes que não esteja na mesma subscrição que aquele emite os registos. O utilizador que configura a definição tem de ter o acesso RBAC adequado para ambas as subscrições.

> [!NOTE]
>  Atualmente não é possível arquivar dados para uma conta de armazenamento que está atrás de uma rede virtual protegida.

> [!WARNING]
> O formato dos dados de registo na conta de armazenamento foi alterado para linhas de JSON no dia 1 de Novembro de 2018. [Leia este artigo para obter uma descrição do impacto e saber como atualizar a sua ferramenta para trabalhar com o novo formato.](./../../azure-monitor/platform/diagnostic-logs-append-blobs.md) 
>
> 

Estas definições podem ser configuradas através da opção de "Exportação" no painel do registo de atividades no portal do. Eles também podem ser configurados por meio de programação [utilizando a API de REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), cmdlets do PowerShell, ou a CLI. Uma subscrição só pode ter um perfil de registo.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Configurar perfis de registo no portal do Azure
Pode transmitir o registo de atividade para um Hub de eventos ou armazená-los numa conta de armazenamento ao utilizar a opção de "Exportar para Hub de eventos" no portal do Azure.

1. Navegue para **registo de atividades** utilizando o menu no lado esquerdo do portal.

    ![Navegue até ao registo de atividades no portal](./media/activity-logs-overview/activity-logs-portal-navigate-v2.png)
2. Clique nas **exportar para o Hub de eventos** botão na parte superior do painel.

    ![Botão de exportação no portal](./media/activity-logs-overview/activity-logs-portal-export-v2.png)
3. No painel que aparece, pode selecionar:  
   * regiões para o qual pretende exportar eventos
   * a conta de armazenamento para o qual pretende guardar os eventos
   * o número de dias que pretenda manter esses eventos em armazenamento. Uma definição de 0 dias retém os registos para sempre.
   * o Namespace de barramento de serviço no qual gostaria de ter um Hub de eventos a ser criado para esses eventos de transmissão em fluxo.

     ![Painel de registo de atividades de exportação](./media/activity-logs-overview/activity-logs-portal-export-blade.png)
4. Clique em **guardar** para salvar essas configurações. As definições são imediatamente aplicadas à sua subscrição.

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Configurar perfis de registo utilizando os Cmdlets do PowerShell do Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

#### <a name="get-existing-log-profile"></a>Obter perfil de registo existente

```
Get-AzLogProfile
```

#### <a name="add-a-log-profile"></a>Adicionar um perfil de registo

```
Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
```

| Propriedade | Necessário | Descrição |
| --- | --- | --- |
| Name |Sim |Nome do seu perfil de registo. |
| StorageAccountId |Não |ID de recurso da conta do Storage para o qual deverá ser guardado o registo de atividades. |
| serviceBusRuleId |Não |ID de regra de barramento de serviço para o espaço de nomes do Service Bus que deseja que os hubs de eventos criados no. É uma cadeia de caracteres com este formato: `{service bus resource ID}/authorizationrules/{key name}`. |
| Localização |Sim |Lista separada por vírgulas de regiões para o qual pretende recolher eventos de registo de atividades. |
| RetentionInDays |Sim |Número de dias para que eventos devem ser mantidos, entre 1 e 2147483647. Um valor de zero armazena os logs de indefinidamente (Eterno). |
| Categoria |Não |Lista separada por vírgulas das categorias de evento que deve ser recolhidos. Valores possíveis são escrita, eliminação e ação. |

#### <a name="remove-a-log-profile"></a>Remover um perfil de registo
```
Remove-AzLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cli"></a>Configurar perfis de registo com a CLI do Azure

#### <a name="get-existing-log-profile"></a>Obter perfil de registo existente

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

O `name` propriedade deve ser o nome do seu perfil de registo.

#### <a name="add-a-log-profile"></a>Adicionar um perfil de registo

```azurecli
az monitor log-profiles create --name <profile name> \
    --locations <location1 location2 ...> \
    --location <location> \
    --categories <category1 category2 ...>
```

Para obter a documentação completa para criar um perfil de monitor com a CLI, veja o [referência de comando da CLI](/cli/azure/monitor/log-profiles#az-monitor-log-profiles-create)

#### <a name="remove-a-log-profile"></a>Remover um perfil de registo

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="next-steps"></a>Próximos Passos
* [Saiba mais sobre o registo de Atividades (anteriormente conhecido como registos de auditoria)](../../azure-resource-manager/resource-group-audit.md)
* [O registo de atividades do Azure para os Hubs de eventos do Stream](../../azure-monitor/platform/activity-logs-stream-event-hubs.md)

