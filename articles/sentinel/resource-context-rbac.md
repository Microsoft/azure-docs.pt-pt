---
title: Gerir o acesso aos dados do Azure Sentinel através de recursos | Microsoft Docs
description: Este artigo explica que pode gerir o acesso aos dados do Azure Sentinel pelos recursos a que um utilizador pode aceder. Gerir o acesso por recurso permite-lhe fornecer acesso apenas a dados específicos, sem toda a experiência do Azure Sentinel. Este método também é conhecido como RBAC de contexto de recursos.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: bagol
ms.openlocfilehash: 26124f8f650e1006244b4871e26962d417d90fd4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102055048"
---
# <a name="manage-access-to-azure-sentinel-data-by-resource"></a>Gerir o acesso aos dados do Azure Sentinel por recurso

Normalmente, os utilizadores que tenham acesso a um espaço de trabalho Azure Sentinel também têm acesso a todos os dados do espaço de trabalho, incluindo conteúdo de segurança. Os administradores podem usar [funções Azure](roles.md) para configurar o acesso a funcionalidades específicas no Azure Sentinel, dependendo dos requisitos de acesso na sua equipa.

No entanto, pode ter alguns utilizadores que precisam de aceder apenas a dados específicos no seu espaço de trabalho Azure Sentinel, mas não devem ter acesso a todo o ambiente do Azure Sentinel. Por exemplo, é melhor fornecer uma equipa de operações de não segurança (não SOC) com acesso aos dados do evento windows para os servidores que possuem.

Nesses casos, recomendamos que configuure o seu controlo de acesso baseado em funções (RBAC) com base nos recursos que são permitidos aos seus utilizadores, em vez de lhes fornecer acesso ao espaço de trabalho Azure Sentinel ou funcionalidades específicas do Azure Sentinel. Este método também é conhecido como configuração **de contexto de recursos RBAC**.

Quando os utilizadores têm acesso aos dados do Azure Sentinel através dos recursos a que podem aceder em vez do espaço de trabalho Azure Sentinel, podem visualizar registos e livros de trabalho utilizando os seguintes métodos:

- **Através do próprio recurso,** como uma Máquina Virtual Azure. Utilize este método para visualizar registos e livros apenas para um recurso específico.

- **Via Azure Monitor**. Utilize este método quando pretender criar consultas que abrangem múltiplos recursos e/ou grupos de recursos. Ao navegar em registos e livros no Azure Monitor, defina o seu âmbito para um ou mais grupos ou recursos específicos de recursos.

Ativar o contexto de recursos RBAC no Azure Monitor. Para obter mais informações, consulte [Gerir o acesso aos dados de registo e espaços de trabalho no Azure Monitor.](/azure/azure-monitor/logs/manage-access)

> [!NOTE]
> Se os seus dados não forem um recurso Azure, como dados Syslog, CEF ou AAD, ou dados recolhidos por um colecionador personalizado, terá de configurar manualmente o ID de recursos utilizado para identificar os dados e ativar o acesso.
>
> Para obter mais informações, consulte [explicitamente configurar o contexto de recursos RBAC](#explicitly-configure-resource-context-rbac).
>
## <a name="scenarios-for-resource-context-rbac"></a>Cenários para o contexto de recursos RBAC

A tabela seguinte destaca os cenários em que o RBAC de contexto de recursos é mais útil. Note as diferenças nos requisitos de acesso entre equipas SOC e equipas não SOC.

| Tipo de requisito |Equipa SOC  |Equipa não SOC  |
|---------|---------|---------|
|**Permissões**     | Todo o espaço de trabalho        |   Recursos específicos apenas      |
|**Acesso a dados**     |  Todos os dados no espaço de trabalho       | Apenas dados para recursos a que a equipa está autorizada a aceder        |
|**Experiência**     |  A experiência completa do Azure Sentinel, possivelmente limitada pelas [permissões funcionais atribuídas](roles.md) ao utilizador       |  Consultas de registo e livros de trabalho apenas       |
|     |         |         |

Se a sua equipa tiver requisitos de acesso semelhantes à equipa não SOC descrita na tabela acima, o RBAC de contexto de recursos pode ser uma boa solução para a sua organização.

## <a name="alternative-methods-for-implementing-resource-context-rbac"></a>Métodos alternativos para implementar o rbac de contexto de recursos

Dependendo das permissões necessárias na sua organização, a utilização do RBAC de contexto de recursos pode não fornecer uma solução completa.

A lista que se segue descreve cenários em que outras soluções para acesso a dados podem se adequar melhor aos seus requisitos:

|Scenario  |Solução  |
|---------|---------|
|**Uma subsidiária tem uma equipa SOC que requer uma experiência completa do Azure Sentinel.**     |  Neste caso, utilize uma arquitetura multi-workspace para separar as suas permissões de dados. <br><br>Para obter mais informações, consulte: <br>- [Estender Azure Sentinel através de espaços de trabalho e inquilinos](extend-sentinel-across-workspaces-tenants.md)<br>    - [Trabalhar com incidentes em muitos espaços de trabalho ao mesmo tempo](multiple-workspace-view.md)          |
|**Pretende fornecer acesso a um tipo específico de evento.**     |  Por exemplo, fornecer a um administrador do Windows acesso a eventos de Segurança do Windows em todos os sistemas. <br><br>Nesses casos, utilize [o RBAC ao nível da mesa](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) para definir permissões para cada tabela.       |
| **Limitar o acesso a um nível mais granular, não baseado no recurso, ou apenas a um subconjunto dos campos num evento**   |   Por exemplo, é melhor limitar o acesso aos registos do Office 365 com base na subsidiária de um utilizador. <br><br>Neste caso, fornecer acesso a dados utilizando a integração incorporada com [painéis e relatórios Power BI](/azure/azure-monitor/platform/powerbi).      |
| | |

## <a name="explicitly-configure-resource-context-rbac"></a>Configurar explicitamente o contexto de recursos RBAC

Utilize os seguintes passos se pretender configurar o RBAC de contexto de recursos, mas os seus dados não são um recurso Azure.

Por exemplo, os dados no seu espaço de trabalho Azure Sentinel que não são recursos Azure incluem Syslog, CEF ou dados AAD, ou dados recolhidos por um colecionador personalizado.

**Para configurar explicitamente o contexto de recursos RBAC:**

1. Certifique-se de que [ativou o contexto de recursos RBAC](/azure/azure-monitor/platform/manage-access) no Azure Monitor. 

1. [Crie um grupo](/azure/azure-resource-manager/management/manage-resource-groups-portal) de recursos para cada equipa de utilizadores que necessite de aceder aos seus recursos sem todo o ambiente Azure Sentinel.

    Atribua [permissões de leitor](/azure/azure-monitor/platform/manage-access#resource-permissions) de registo para cada um dos membros da equipa.

1. Atribua recursos aos grupos de equipas de recursos que criou e marque eventos com os IDs de recursos relevantes.

    Quando os recursos Azure enviam dados para a Azure Sentinel, os registos de registo são automaticamente marcados com o ID de recursos da fonte de dados.

    > [!TIP]
    > Recomendamos que agrupar os recursos para os quais está a conceder acesso ao abrigo de um grupo de recursos específico criado para o efeito.
    >
    > Se não conseguir, certifique-se de que a sua equipa tem permissões de leitor de registo diretamente nos recursos a que pretende aceder.
    >

    Para obter mais informações sobre os IDs de recursos, consulte:

    - [IDs de recursos com encaminhamento de log](#resource-ids-with-log-forwarding)
    - [IDs de recursos com coleção Logstash](#resource-ids-with-logstash-collection)
    - [IDs de recursos com a coleção API do Log Analytics](#resource-ids-with-the-log-analytics-api-collection)

### <a name="resource-ids-with-log-forwarding"></a>IDs de recursos com encaminhamento de log

Quando os eventos são recolhidos usando [o Common Event Format (CEF)](connect-common-event-format.md) ou [Syslog,](connect-syslog.md)o reencaminhamento de log é utilizado para recolher eventos de vários sistemas de origem.

Por exemplo, quando um CEF ou Syslog reencaminha vM ouve as fontes que enviam eventos Syslog, e os reencaminho para Azure Sentinel, o ID de recursos VM de reencaminhamento de log é atribuído a todos os eventos que eles encaminham.

Se tiver várias equipas, certifique-se de que tem VMs de reencaminhamento separados processando os eventos para cada equipa separada.

Por exemplo, separar os seus VMs garante que os eventos Syslog que pertencem à Equipa A são recolhidos usando o colecionador VM A.

> [!TIP]
> - Quando utilizar um VM no local ou outro VM em nuvem, como a AWS, como o seu reencaminhador de registo, certifique-se de que tem um ID de recursos implementando [o Arco Azure](/azure/azure-arc/servers/overview).
> - Para escalar o ambiente VM de reencaminhamento de registos, considere criar uma [escala VM definida](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854) para recolher os seus registos CEF e Sylog.


### <a name="resource-ids-with-logstash-collection"></a>IDs de recursos com coleção Logstash

Se estiver a recolher os seus dados utilizando o plugin de saída Azure Sentinel [Logstash,](connect-logstash.md)utilize o campo **azure_resource_id** para configurar o seu colecionador personalizado para incluir o ID de recursos na sua saída.

Se estiver a utilizar o RBAC de contexto de recursos e quiser que os eventos recolhidos pela API estejam disponíveis para utilizadores específicos, utilize o ID de recursos do grupo de recursos que [criou para os seus utilizadores.](#explicitly-configure-resource-context-rbac)

Por exemplo, o seguinte código mostra um ficheiro de configuração de logstash de amostra:

``` ruby
 input {
     beats {
         port => "5044"
     }
 }
 filter {
 }
 output {
     microsoft-logstash-output-azure-loganalytics {
       workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
       workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
       custom_log_table_name => "tableName"
       azure_resource_id => "/subscriptions/wvvu95a2-99u4-uanb-hlbg-2vatvgqtyk7b/resourceGroups/contosotest" # <your resource ID>   
     }
 }
```

> [!TIP]
> Pode querer adicionar `output` várias secções para diferenciar as tags aplicadas a diferentes eventos.
>
### <a name="resource-ids-with-the-log-analytics-api-collection"></a>IDs de recursos com a coleção API do Log Analytics

Ao recolher a API do [coletor de dados do Log Analytics,](/azure/azure-monitor/platform/data-collector-api)pode atribuir eventos com um ID de recurso utilizando o cabeçalho de pedido HTTP [*x-ms-AzureResourceId.*](/azure/azure-monitor/platform/data-collector-api#request-headers)

Se estiver a utilizar o RBAC de contexto de recursos e quiser que os eventos recolhidos pela API estejam disponíveis para utilizadores específicos, utilize o ID de recursos do grupo de recursos que [criou para os seus utilizadores.](#explicitly-configure-resource-context-rbac)


## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte [permissões em Azure Sentinel.](roles.md)
