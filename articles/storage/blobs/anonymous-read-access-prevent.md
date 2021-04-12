---
title: Impedir que o público anónimo leia o acesso a contentores e bolhas
titleSuffix: Azure Storage
description: Saiba como analisar pedidos anónimos contra uma conta de armazenamento e como impedir o acesso anónimo a toda a conta de armazenamento ou a um recipiente individual.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/09/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: 53f29c2b8f7a17ac2a23cc081660e8dcb4b9f387
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701863"
---
# <a name="prevent-anonymous-public-read-access-to-containers-and-blobs"></a>Impedir que o público anónimo leia o acesso a contentores e bolhas

O público anónimo lê o acesso a contentores e bolhas no Azure Storage é uma forma conveniente de partilhar dados, mas também pode apresentar um risco de segurança. É importante gerir o acesso anónimo criteriosamente e entender como avaliar o acesso anónimo aos seus dados. A complexidade operacional, o erro humano ou o ataque malicioso contra dados acessíveis ao público podem resultar em dispendiosas violações de dados. A Microsoft recomenda que permita o acesso anónimo apenas quando necessário para o seu cenário de aplicação.

Por padrão, o acesso do público aos seus dados blob é sempre proibido. No entanto, a configuração padrão de uma conta de armazenamento permite a um utilizador com permissões adequadas para configurar o acesso público a contentores e bolhas numa conta de armazenamento. Para uma maior segurança, pode proibir todo o acesso do público à conta de armazenamento, independentemente da definição de acesso público a um recipiente individual. A não adesão do público à conta de armazenamento impede um utilizador de permitir o acesso público a um contentor na conta. A Microsoft recomenda que não odeia o acesso do público a uma conta de armazenamento, a menos que o seu cenário o exija. A não adesão do público ajuda a prevenir violações de dados causadas por acesso anónimo não permitido.

Quando não permite o acesso público à conta de armazenamento, o Azure Storage rejeita todos os pedidos anónimos para essa conta. Após o acesso público ser proibido por uma conta, os contentores nessa conta não podem ser posteriormente configurados para acesso público. Quaisquer contentores que já tenham sido configurados para acesso público deixarão de aceitar pedidos anónimos. Para mais informações, consulte o público [anónimo Configure acesso para contentores e bolhas.](anonymous-read-access-configure.md)

Este artigo descreve como utilizar um quadro DRAG (Detecção-Remediação-Auditoria-Governação) para gerir continuamente o acesso público às suas contas de armazenamento.

## <a name="detect-anonymous-requests-from-client-applications"></a>Detetar pedidos anónimos de aplicações de clientes

Quando não permite o acesso do público a uma conta de armazenamento, arrisca-se a rejeitar pedidos a contentores e bolhas que estão atualmente configurados para acesso público. A não permitir o acesso do público a uma conta de armazenamento sobrepõe-se às definições de acesso público a contentores individuais nessa conta de armazenamento. Quando o acesso público for proibido para a conta de armazenamento, quaisquer futuros pedidos anónimos a essa conta falharão.

Para entender como a não concessão do acesso público pode afetar as aplicações dos clientes, a Microsoft recomenda que você ative o registo e métricas para essa conta e analise padrões de pedidos anónimos durante um intervalo de tempo. Utilize métricas para determinar o número de pedidos anónimos na conta de armazenamento e utilize registos para determinar quais os recipientes que estão a ser acedidos anonimamente.

### <a name="monitor-anonymous-requests-with-metrics-explorer"></a>Monitorize pedidos anónimos com o Metrics Explorer

Para rastrear pedidos anónimos a uma conta de armazenamento, utilize o Azure Metrics Explorer no portal Azure. Para obter mais informações sobre o Metrics Explorer, consulte [Começar com o Azure Metrics Explorer](../../azure-monitor/essentials/metrics-getting-started.md).

Siga estes passos para criar uma métrica que rastreie pedidos anónimos:

1. Navegue para a sua conta de armazenamento no portal do Azure. Na secção **de Monitorização,** selecione **Métricas**.
1. Selecione **Adicionar métrica**. No diálogo **métrico,** especifique os seguintes valores:
    1. Deixe o campo Scope definido para o nome da conta de armazenamento.
    1. Descreva o **espaço de nome métrico** para *Blob*. Esta métrica reportará apenas pedidos contra o armazenamento blob.
    1. Desa estação **métrica** para *transações.*
    1. Desagregar o campo **de agregação** para *Sum*.

    A nova métrica apresentará a soma do número de transações contra o armazenamento blob durante um determinado intervalo de tempo. A métrica resultante aparece como mostra a seguinte imagem:

    :::image type="content" source="media/anonymous-read-access-prevent/configure-metric-blob-transactions.png" alt-text="Screenshot mostrando como configurar métrica para sum blob transações":::

1. Em seguida, selecione o botão **de filtro Adicionar** para criar um filtro na métrica para pedidos anónimos.
1. No diálogo **do filtro,** especifique os seguintes valores:
    1. Desa estava o valor **da Propriedade** à *Autenticação.*
    1. Coloque o campo **do operador** no sinal de igual igual (=).
    1. Desavista o campo **Valores** para *Anónimo*.
1. No canto superior direito, selecione o intervalo de tempo sobre o qual deseja ver a métrica. Também pode indicar como deve ser granular a agregação de pedidos, especificando intervalos entre 1 minuto e 1 mês.

Depois de configurar a métrica, os pedidos anónimos começarão a aparecer no gráfico. A imagem que se segue mostra pedidos anónimos agregados ao longo dos últimos 30 minutos.

:::image type="content" source="media/anonymous-read-access-prevent/metric-anonymous-blob-requests.png" alt-text="Screenshot mostrando pedidos anónimos agregados contra o armazenamento blob":::

Também pode configurar uma regra de alerta para notificá-lo quando um certo número de pedidos anónimos são feitos contra a sua conta de armazenamento. Para obter mais informações, consulte [Criar, ver e gerir alertas métricos utilizando o Azure Monitor](../../azure-monitor/alerts/alerts-metric.md).

### <a name="analyze-logs-to-identify-containers-receiving-anonymous-requests"></a>Analisar registos para identificar contentores que recebem pedidos anónimos

Os registos de armazenamento Azure captam detalhes sobre pedidos feitos contra a conta de armazenamento, incluindo como um pedido foi autorizado. Pode analisar os registos para determinar quais os contentores que estão a receber pedidos anónimos.

Para registar pedidos na sua conta de Armazenamento Azure para avaliar pedidos anónimos, pode utilizar o registo de armazenamento Azure no Azure Monitor (pré-visualização). Para obter mais informações, consulte [monitor Azure Storage](./monitor-blob-storage.md).

O registo de armazenamento Azure no Azure Monitor suporta a utilização de consultas de registo para analisar dados de registo. Para consultar registos, pode utilizar um espaço de trabalho Azure Log Analytics. Para saber mais sobre consultas de log, consulte [Tutorial: Começar com consultas de Log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md).

> [!NOTE]
> A pré-visualização do registo de armazenamento Azure no Azure Monitor é suportada apenas na nuvem pública do Azure. As nuvens governamentais não suportam o registo de madeira para o Azure Storage com o Azure Monitor.

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Criar uma definição de diagnóstico no portal Azure

Para registar os dados de Armazenamento Azure com o Azure Monitor e analisá-los com o Azure Log Analytics, tem primeiro de criar uma definição de diagnóstico que indique quais os tipos de pedidos e quais os serviços de armazenamento que pretende registar dados. Para criar uma definição de diagnóstico no portal Azure, siga estes passos:

1. Crie um novo espaço de trabalho log Analytics na subscrição que contenha a sua conta de Armazenamento Azure. Depois de configurar o registo da sua conta de armazenamento, os registos estarão disponíveis no espaço de trabalho do Log Analytics. Para obter mais informações, consulte [Criar um espaço de trabalho log Analytics no portal Azure](../../azure-monitor/logs/quick-create-workspace.md).
1. Navegue para a sua conta de armazenamento no portal do Azure.
1. Na secção de Monitorização, selecione **definições de diagnóstico (pré-visualização)**.
1. Selecione **Blob** para registar os pedidos feitos contra o armazenamento blob.
1. Selecione **Adicionar definição de diagnóstico**.
1. Forneça um nome para a definição de diagnóstico.
1. Em **detalhes de categoria**, na secção de **registo,** escolha quais os tipos de pedidos para registar. Todos os pedidos anónimos serão lidos pedidos, por isso selecione **StorageRead** para capturar pedidos anónimos.
1. Nos **detalhes do Destino**, selecione Enviar para Registar **Analítico**. Selecione a sua subscrição e o espaço de trabalho Log Analytics que criou anteriormente, como mostrado na imagem seguinte.

    :::image type="content" source="media/anonymous-read-access-prevent/create-diagnostic-setting-logs.png" alt-text="Screenshot mostrando como criar uma definição de diagnóstico para pedidos de registo":::

Depois de criar a definição de diagnóstico, os pedidos para a conta de armazenamento são subsequentemente registados de acordo com essa definição. Para obter mais informações, consulte [Criar a definição de diagnóstico para recolher registos e métricas de recursos em Azure](../../azure-monitor/essentials/diagnostic-settings.md).

Para obter uma referência dos campos disponíveis nos registos de armazenamento Azure no Azure Monitor, consulte [registos de recursos (pré-visualização)](./monitor-blob-storage-reference.md#resource-logs-preview).

#### <a name="query-logs-for-anonymous-requests"></a>Registos de consulta para pedidos anónimos

Os registos de armazenamento Azure no Azure Monitor incluem o tipo de autorização que foi usada para fazer um pedido a uma conta de armazenamento. Na sua consulta de registo, filtre na propriedade **AuthenticationType** para visualizar pedidos anónimos.

Para recuperar registos nos últimos 7 dias para pedidos anónimos contra o armazenamento blob, abra o seu espaço de trabalho Log Analytics. Em seguida, cole a seguinte consulta em uma nova consulta de log e executá-la:

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AuthenticationType == "Anonymous"
| project TimeGenerated, AccountName, AuthenticationType, Uri
```

Também pode configurar uma regra de alerta com base nesta consulta para notificá-lo sobre pedidos anónimos. Para obter mais informações, consulte [Criar, ver e gerir alertas de registo utilizando o Azure Monitor](../../azure-monitor/alerts/alerts-log.md).

## <a name="remediate-anonymous-public-access"></a>Remediar o acesso público anónimo

Depois de ter avaliado pedidos anónimos a contentores e bolhas na sua conta de armazenamento, pode tomar medidas para limitar ou impedir o acesso do público. Se alguns contentores na sua conta de armazenamento precisarem de estar disponíveis para acesso público, então pode configurar a definição de acesso público para cada recipiente na sua conta de armazenamento. Esta opção proporciona o maior controlo granular sobre o acesso público. Para obter mais informações, consulte [definir o nível de acesso público para um recipiente.](anonymous-read-access-configure.md#set-the-public-access-level-for-a-container)

Para uma maior segurança, pode proibir o acesso público a toda a conta de armazenamento. A definição de acesso público a uma conta de armazenamento sobrepõe-se às configurações individuais dos contentores nessa conta. Quando não permite o acesso público a uma conta de armazenamento, quaisquer contentores configurados para permitir o acesso público deixaram de ser acessíveis de forma anónima. Para mais informações, consulte [Permitir ou proibir o acesso do público a uma conta de armazenamento.](anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account)

Se o seu cenário exigir que certos contentores tenham de estar disponíveis para acesso público, pode ser aconselhável transferir esses contentores e as suas bolhas para contas de armazenamento reservadas para acesso público. Em seguida, pode proibir o acesso do público a quaisquer outras contas de armazenamento.

### <a name="verify-that-public-access-to-a-blob-is-not-permitted"></a>Verifique se o acesso público a uma bolha não é permitido

Para verificar se o acesso do público a uma bolha específica é proibido, pode tentar descarregar a bolha através do seu URL. Se o download for bem sucedido, então a bolha ainda está disponível ao público. Se a bolha não for acessível ao público porque o acesso público foi proibido para a conta de armazenamento, então verá uma mensagem de erro indicando que o acesso público não é permitido nesta conta de armazenamento.

O exemplo a seguir mostra como usar o PowerShell para tentar descarregar uma bolha através do seu URL. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```powershell
$url = "<absolute-url-to-blob>"
$downloadTo = "<file-path-for-download>"
Invoke-WebRequest -Uri $url -OutFile $downloadTo -ErrorAction Stop
```

### <a name="verify-that-modifying-the-containers-public-access-setting-is-not-permitted"></a>Verifique se não é permitida a modificação da configuração de acesso público do contentor

Para verificar se a definição de acesso público de um contentor não pode ser modificada após o acesso público à conta de armazenamento, pode tentar modificar a configuração. A alteração da configuração de acesso público do contentor falhará se o acesso público for proibido para a conta de armazenamento.

O exemplo a seguir mostra como utilizar o PowerShell para tentar alterar a configuração de acesso público de um contentor. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Set-AzStorageContainerAcl -Context $ctx -Container $containerName -Permission Blob
```

### <a name="verify-that-creating-a-container-with-public-access-enabled-is-not-permitted"></a>Verifique se não é permitida a criação de um contentor com acesso público

Se o acesso público for proibido para a conta de armazenamento, então não será possível criar um novo recipiente com acesso público habilitado. Para verificar, pode tentar criar um recipiente com acesso público ativado.

O exemplo a seguir mostra como utilizar o PowerShell para tentar criar um recipiente com acesso público ativado. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Permission Blob -Context $ctx
```

### <a name="check-the-public-access-setting-for-multiple-accounts"></a>Verifique a definição de acesso público para várias contas

Para verificar a definição de acesso público através de um conjunto de contas de armazenamento com o melhor desempenho, pode utilizar o Azure Resource Graph Explorer no portal Azure. Para saber mais sobre a utilização do Explorador de Gráficos de Recurso, consulte [Quickstart: Execute a sua primeira consulta de Gráfico de Recursos utilizando o Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md).

A propriedade **AllowBlobPublicAccess** não está definida para uma conta de armazenamento por padrão e não devolve um valor até que o tenha definido explicitamente. A conta de armazenamento permite o acesso público quando o valor da propriedade é **nulo** ou **verdadeiro.**

Executando a seguinte consulta no Resource Graph Explorer devolve uma lista de contas de armazenamento e exibe a definição de acesso público para cada conta:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
```

A imagem a seguir mostra os resultados de uma consulta através de uma subscrição. Note que para contas de armazenamento onde a propriedade **AllowBlobPublicAccess** foi explicitamente definida, aparece nos resultados como **verdadeiro** ou **falso**. Se a propriedade **AllowBlobPublicAccess** não tiver sido definida para uma conta de armazenamento, aparece como em branco (ou nulo) nos resultados da consulta.

:::image type="content" source="media/anonymous-read-access-prevent/check-public-access-setting-accounts.png" alt-text="Screenshot mostrando resultados de consulta para a definição de acesso público através de contas de armazenamento":::

## <a name="use-azure-policy-to-audit-for-compliance"></a>Utilizar a Política Azure para auditar o cumprimento

Se tiver um grande número de contas de armazenamento, talvez queira realizar uma auditoria para se certificar de que essas contas estão configuradas para impedir o acesso do público. Para auditar um conjunto de contas de armazenamento para o seu cumprimento, utilize a Azure Policy. A Azure Policy é um serviço que pode usar para criar, atribuir e gerir políticas que aplicam regras aos recursos da Azure. A Azure Policy ajuda-o a manter esses recursos em conformidade com os seus padrões corporativos e acordos de nível de serviço. Para mais informações, consulte [a Visão Geral da Política Azure.](../../governance/policy/overview.md)

### <a name="create-a-policy-with-an-audit-effect"></a>Criar uma política com efeito de auditoria

A Azure Policy suporta efeitos que determinam o que acontece quando uma regra de política é avaliada contra um recurso. O efeito Auditoria cria um aviso quando um recurso não está em conformidade, mas não impede o pedido. Para obter mais informações sobre os efeitos, consulte [os efeitos da Política de Azure.](../../governance/policy/concepts/effects.md)

Para criar uma política com efeito de auditoria para a definição de acesso público a uma conta de armazenamento com o portal Azure, siga estes passos:

1. No portal Azure, navegue para o serviço Azure Policy.
1. Na secção **de Autoria,** selecione **Definições**.
1. **Selecione Adicionar definição de política** para criar uma nova definição de política.
1. Para o campo **de localização Definição,** selecione o botão **Mais** para especificar onde está localizado o recurso de política de auditoria.
1. Especifique um nome para a apólice. Pode especificar opcionalmente uma descrição e categoria.
1. De acordo com **a regra política**, adicione a seguinte definição de política à secção **policyRule.**

    ```json
    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
              "equals": "false"
            }
          }
        ]
      },
      "then": {
        "effect": "audit"
      }
    }
    ```

1. Guarde a política.

### <a name="assign-the-policy"></a>Atribuir a política

Em seguida, atribua a apólice a um recurso. O âmbito da política corresponde a esse recurso e a quaisquer recursos abaixo dele. Para obter mais informações sobre a atribuição de políticas, consulte [a estrutura de atribuição da Política Azure](../../governance/policy/concepts/assignment-structure.md).

Para atribuir a política ao portal Azure, siga estes passos:

1. No portal Azure, navegue para o serviço Azure Policy.
1. Na secção **de Autoria,** selecione **Atribuições.**
1. Selecione **A política de atribuir** para criar uma nova atribuição de políticas.
1. Para o campo **Scope,** selecione o âmbito da atribuição de políticas.
1. Para o campo **de definição de Política,** selecione o botão **Mais** e, em seguida, selecione a política definida na secção anterior da lista.
1. Forneça um nome para a atribuição de apólices. A descrição é opcional.
1. Deixe **a aplicação da política** definida para *Ativação*. Esta definição não tem qualquer efeito sobre a política de auditoria.
1. Selecione **Review + criar** para criar a atribuição.

### <a name="view-compliance-report"></a>Ver relatório de conformidade

Depois de ter atribuído a apólice, pode ver o relatório de conformidade. O relatório de conformidade de uma política de auditoria fornece informações sobre as quais as contas de armazenamento não estão em conformidade com a política. Para obter mais informações, consulte [obter dados de conformidade com a política](../../governance/policy/how-to/get-compliance-data.md).

Pode levar vários minutos para que o relatório de conformidade fique disponível após a criação da atribuição da política.

Para ver o relatório de conformidade no portal Azure, siga estes passos:

1. No portal Azure, navegue para o serviço Azure Policy.
1. Selecione **Compliance**.
1. Filtrar os resultados para o nome da atribuição de política que criou no passo anterior. O relatório mostra quantos recursos não estão em conformidade com a política.
1. Pode aprofundar o relatório para obter mais detalhes, incluindo uma lista de contas de armazenamento que não estão em conformidade.

    :::image type="content" source="media/anonymous-read-access-prevent/compliance-report-policy-portal.png" alt-text="Screenshot mostrando relatório de conformidade para a política de auditoria para o acesso público blob":::

## <a name="use-azure-policy-to-enforce-authorized-access"></a>Use a Política Azure para impor o acesso autorizado

A Azure Policy apoia a governação em nuvem, garantindo que os recursos da Azure cumpram os requisitos e padrões. Para garantir que as contas de armazenamento na sua organização permitem apenas pedidos autorizados, pode criar uma política que impeça a criação de uma nova conta de armazenamento com uma definição de acesso público que permita pedidos anónimos. Esta política também evitará que todas as alterações de configuração de uma conta existente se a definição de acesso público a essa conta não estiver em conformidade com a política.

A política de execução utiliza o efeito Deny para impedir um pedido que crie ou modifique uma conta de armazenamento para permitir o acesso do público. Para obter mais informações sobre os efeitos, consulte [os efeitos da Política de Azure.](../../governance/policy/concepts/effects.md)

Para criar uma política com efeito Deny para uma definição de acesso público que permita pedidos anónimos, siga os mesmos passos descritos na [Política de Utilização Azure para auditar para o cumprimento,](#use-azure-policy-to-audit-for-compliance)mas forneça o seguinte JSON na secção **de regulação política** da definição de política:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
          "equals": "false"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Depois de criar a política com o efeito Deny e atribuí-la a um âmbito, um utilizador não pode criar uma conta de armazenamento que permita o acesso do público. Também não pode um utilizador fazer alterações de configuração numa conta de armazenamento existente que atualmente permite o acesso do público. Tentar fazê-lo resulta num erro. A definição de acesso público à conta de armazenamento deve ser definida **como falsa** para proceder à criação ou configuração de conta.

A imagem a seguir mostra o erro que ocorre se tentar criar uma conta de armazenamento que permita o acesso do público (o padrão para uma nova conta) quando uma política com efeito Deny requer que o acesso público seja proibido.

:::image type="content" source="media/anonymous-read-access-prevent/deny-policy-error.png" alt-text="Screenshot mostrando o erro que ocorre ao criar uma conta de armazenamento em violação da política":::

## <a name="permissions-for-allowing-or-disallowing-public-access"></a>Permissões para permitir ou desativar o acesso do público

Para definir a propriedade **AllowBlobPublicAccess** para a conta de armazenamento, um utilizador deve ter permissões para criar e gerir contas de armazenamento. As funções de controlo de acesso baseado em funções (Azure RBAC) que fornecem estas permissões incluem o **Microsoft.Storage/storageAccounts/write** ou **\* Microsoft.Storage/storageAcounts/action.** As funções incorporadas com esta ação incluem:

- A função [de proprietário](../../role-based-access-control/built-in-roles.md#owner) de recursos Azure
- O papel de [Colaborador](../../role-based-access-control/built-in-roles.md#contributor) do Gestor de Recursos Azure
- A [função de contribuinte da conta de armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Estas funções não fornecem acesso a dados numa conta de armazenamento via Azure Ative Directory (Azure AD). No entanto, incluem o **Microsoft.Storage/storageAccounts/listkeys/action**, que dá acesso às chaves de acesso à conta. Com esta permissão, um utilizador pode utilizar as chaves de acesso à conta para aceder a todos os dados numa conta de armazenamento.

As atribuições de funções devem ser a nível da conta de armazenamento ou superior para permitir que um utilizador permita ou não permita o acesso público à conta de armazenamento. Para obter mais informações sobre o âmbito de funções, consulte [o âmbito de aplicação do Azure RBAC](../../role-based-access-control/scope-overview.md).

Tenha cuidado para restringir a atribuição destas funções apenas a quem necessita da capacidade de criar uma conta de armazenamento ou atualizar as suas propriedades. Utilize o princípio do menor privilégio para garantir que os utilizadores têm menos permissões necessárias para realizar as suas tarefas. Para obter mais informações sobre como gerir o acesso com o Azure RBAC, consulte [as Melhores Práticas para O RBAC Azure](../../role-based-access-control/best-practices.md).

> [!NOTE]
> As funções clássicas de administrador de subscrição Administrador de serviço e Co-Administrator incluem o equivalente à função de [Gestor](../../role-based-access-control/built-in-roles.md#owner) de Recursos Azure. A função **Proprietário** inclui todas as ações, para que um utilizador com uma destas funções administrativas também possa criar e gerir contas de armazenamento. Para obter mais informações, consulte [as funções de administrador de subscrição clássica, funções de Azure e funções de administrador da AD Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

## <a name="next-steps"></a>Passos seguintes

- [Configurar o público anónimo ler acesso a contentores e bolhas](anonymous-read-access-configure.md)
- [Aceda a contentores públicos e bolhas de forma anónima com .NET](anonymous-read-access-client.md)