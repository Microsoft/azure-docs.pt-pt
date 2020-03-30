---
title: Recolher registo de atividade seleção azure com configurações de diagnóstico - Monitor Azure / Microsoft Docs
description: Utilize configurações de diagnóstico para retransmitir registos da Atividade Azure para registos do Monitor Azure, armazenamento Azure ou Hubs de Eventos Azure.
author: bwren
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 02/04/2020
ms.openlocfilehash: 6d4c724c7cfb4c1779f0fc6592a7e61e060755b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096906"
---
# <a name="update-to-azure-activity-log-collection-and-export"></a>Atualização à recolha e exportação de registos da Atividade Azure
O [registo da Atividade Azure](platform-logs-overview.md) é um registo de [plataforma](platform-logs-overview.md) que fornece informações sobre eventos de nível de subscrição que ocorreram no Azure. O método para enviar entradas de registo de atividade para [um centro de eventos ou uma conta](activity-log-export.md) de armazenamento ou para um espaço de trabalho log [Analytics](activity-log-collect.md) mudou para usar [configurações de diagnóstico](diagnostic-settings.md). Este artigo descreve a diferença entre os métodos e como limpar as definições do legado na preparação para mudar para configurações de diagnóstico.


## <a name="differences-between-methods"></a>Diferenças entre métodos

### <a name="advantages"></a>Vantagens
A utilização de definições de diagnóstico tem as seguintes vantagens sobre os métodos atuais:

- Método consistente para recolher todos os registos da plataforma.
- Colete registo de Atividades em várias subscrições e inquilinos.
- Filtrar a recolha para recolher apenas registos para determinadas categorias.
- Colete todas as categorias de registo de Atividade. Algumas categorias não são recolhidas usando o método legado.
- Latência mais rápida para ingestão de loging. O método anterior tem cerca de 15 minutos de latência, enquanto as definições de diagnóstico adicionam apenas cerca de 1 minuto.

### <a name="considerations"></a>Considerações
Considere os seguintes detalhes da recolha de registos da Atividade utilizando definições de diagnóstico antes de ativar esta funcionalidade.

- A definição de retenção para a recolha do registo de atividade para o armazenamento do Azure foi removida, o que significa que os dados serão armazenados indefinidamente até que o remova.
- Atualmente, só é possível criar uma definição de diagnóstico de nível de subscrição utilizando o portal Azure. Para utilizar outros métodos como powerShell ou CLI, pode criar um modelo de Gestor de Recursos.


### <a name="differences-in-data"></a>Diferenças de dados
As definições de diagnóstico recolhem os mesmos dados que os métodos anteriores utilizados para recolher o registo de Atividade com as seguintes diferenças atuais:

As seguintes colunas foram removidas. A substituição destas colunas está num formato diferente, pelo que poderá ser necessário modificar as consultas de registo que as utilizam. Ainda pode ver colunas removidas no esquema, mas não serão povoadas com dados.

| Coluna removida | Coluna de substituição |
|:---|:---|
| Estatuto de Atividade    | ActivityStatusValue    |
| Subestatuto de atividade | ActivitySubstatusValue |
| OperationName     | OperationNameValue     |
| ResourceProvider  | Valor do Fornecedor de Recursos  |

Foram adicionadas as seguintes colunas:

- Authorization_d
- Claims_d
- Properties_d

> [!IMPORTANT]
> Em alguns casos, os valores nestas colunas podem estar em todas as maiúsculas. Se tiver uma consulta que inclua estas colunas, deve utilizar o [operador =~](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) para fazer uma comparação insensível.

## <a name="work-with-legacy-settings"></a>Trabalhar com configurações antigas
As definições antigas para a recolha do registo de atividade continuarão a funcionar se não optar por substituir por uma definição de diagnóstico. Utilize o seguinte método para gerir o perfil de registo para uma subscrição.

1. A partir do menu **Azure Monitor** no portal Azure, selecione **registo de atividade**.
3. Clique em **Definições de diagnóstico**.

   ![Definições de diagnóstico](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Clique no banner roxo para a experiência do legado.

    ![Experiência legado](media/diagnostic-settings-subscription/legacy-experience.png)


Consulte os seguintes artigos para obter mais detalhes sobre a utilização dos métodos de recolha do legado.

- [Recolher e analisar registos de atividade do Azure no espaço de trabalho do Log Analytics no Monitor Azure](activity-log-collect.md)
- [Colete sessão de atividade do Azure no Azure Monitor através de inquilinos do Diretório Ativo azure](activity-log-collect-tenants.md)
- [Export Azure Activity log to storage or Azure Event Hubs](activity-log-export.md)

## <a name="disable-existing-settings"></a>Desativar as definições existentes
Deve desativar a recolha existente da Atividade antes de a permitir utilizando definições de diagnóstico. Ter ambos ativados pode resultar em dados duplicados.

### <a name="disable-collection-into-log-analytics-workspace"></a>Desativar a recolha no espaço de trabalho log Analytics

1. Abra o menu de espaços de **trabalho Log Analytics** no portal Azure e selecione o espaço de trabalho para recolher o Registo de Atividades.
2. Na secção **Fontes** de Dados do Espaço de Trabalho do menu do espaço de trabalho, selecione **o registo da Atividade Do Azure**.
3. Clique na subscrição que pretende desligar.
4. Clique em **Desligar** e, em **seguida, sim** quando lhe for pedido para confirmar a sua escolha.

### <a name="disable-log-profile"></a>Desativar o perfil de registo

1. Utilize o procedimento descrito no [Trabalho com configurações antigas](#work-with-legacy-settings) para abrir as definições do legado.
2. Desative qualquer recolha atual para centros de armazenamento ou eventos.



## <a name="activity-log-monitoring-solution"></a>Solução de monitorização do Registo de Atividades
A solução de monitorização do Azure Log Analytics inclui múltiplas consultas de registo e vistas para analisar os registos de Registo de Atividade sintetizadores no seu espaço de trabalho Log Analytics. Esta solução utiliza dados de registo recolhidos num espaço de trabalho do Log Analytics e continuará a funcionar sem alterações se recolher o registo de atividade utilizando definições de diagnóstico. Consulte [a solução](activity-log-collect.md#activity-logs-analytics-monitoring-solution) de monitorização de Registos de Atividade Analytics para obter detalhes sobre esta solução.

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre o Registo de Atividades](../../azure-resource-manager/management/view-activity-logs.md)
* [Saiba mais sobre as definições de diagnóstico](diagnostic-settings.md)
