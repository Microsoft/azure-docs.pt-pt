---
title: Impedir a autorização com chave partilhada (pré-visualização)
titleSuffix: Azure Storage
description: Para exigir que os clientes utilizem a Azure AD para autorizar pedidos, pode não permitir pedidos na conta de armazenamento que são autorizadas com Chave Partilhada (pré-visualização).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/20/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: 16080440a9458753992c62309ce75ed241fb64d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91715118"
---
# <a name="prevent-shared-key-authorization-for-an-azure-storage-account-preview"></a>Impedir a autorização da Chave Partilhada para uma conta de Armazenamento Azure (pré-visualização)

Todos os pedidos seguros a uma conta de Armazenamento Azure devem ser autorizados. Por predefinição, os pedidos podem ser autorizados com credenciais do Azure Ative Directory (Azure AD) ou utilizando a chave de acesso à conta para a autorização da Chave Partilhada. Destes dois tipos de autorização, o Azure AD proporciona uma segurança superior e facilidade de utilização sobre a Chave Partilhada, e é recomendado pela Microsoft. Para exigir que os clientes utilizem a Azure AD para autorizar pedidos, pode não permitir pedidos na conta de armazenamento que são autorizadas com Chave Partilhada (pré-visualização).

Quando não autoriza a Chave Partilhada para uma conta de armazenamento, o Azure Storage rejeita todos os pedidos subsequentes a essa conta que são autorizadas com as chaves de acesso à conta. Apenas os pedidos seguros autorizados com a Azure AD serão bem sucedidos. Para obter mais informações sobre a utilização do Azure AD, consulte [o Acesso autorizado a bolhas e filas utilizando o Azure Ative Directory](storage-auth-aad.md).

> [!WARNING]
> O Azure Storage suporta a autorização Azure AD para pedidos apenas para o armazenamento de Blob e Fila. Se não permitir a autorização com a Chave Partilhada para uma conta de armazenamento, os pedidos para ficheiros Azure ou armazenamento de mesa que utilizem a autorização da Chave Partilhada falharão.
>
> Durante a pré-visualização, os pedidos para ficheiros Azure ou armazenamento de mesa que utilizem fichas de acesso partilhado (SAS) que foram geradas usando as teclas de acesso à conta serão bem sucedidos quando a autorização da Chave Partilhada for proibida. Para mais informações, consulte [Sobre a pré-visualização.](#about-the-preview)
>
> A desativação do acesso da Chave Partilhada a uma conta de armazenamento não afeta as ligações SMB aos Ficheiros Azure.
>
> A Microsoft recomenda que emigre quaisquer dados de armazenamento de Azure Files ou Table para uma conta de armazenamento separada antes de não permitir o acesso à conta através da Chave Partilhada, ou que não aplique esta definição em contas de armazenamento que suportem ficheiros Azure ou cargas de trabalho de armazenamento de mesa.

Este artigo descreve como detetar pedidos enviados com autorização da Chave Partilhada e como remediar a autorização da Chave Partilhada para a sua conta de armazenamento. Para saber como se registar para a pré-visualização, consulte [Sobre a pré-visualização](#about-the-preview).

## <a name="detect-the-type-of-authorization-used-by-client-applications"></a>Detetar o tipo de autorização utilizada pelas aplicações do cliente

Quando não autoriza a Chave Partilhada para uma conta de armazenamento, os pedidos de clientes que estão a usar as chaves de acesso à conta para autorização da Chave Partilhada falharão. Para entender como a desativação da autorização da Chave Partilhada pode afetar as aplicações do cliente antes de efec se efetuar esta alteração, ative o registo e as métricas para a conta de armazenamento. Em seguida, pode analisar padrões de pedidos na sua conta durante um período de tempo para determinar como os pedidos estão a ser autorizados.

Utilize métricas para determinar quantos pedidos a conta de armazenamento está a receber que são autorizados com a Chave Partilhada ou uma assinatura de acesso partilhado (SAS). Utilize registos para determinar que clientes estão a enviar esses pedidos.

Para obter mais informações sobre a interpretação de pedidos feitos com uma assinatura de acesso partilhado durante a pré-visualização, consulte [Sobre a pré-visualização](#about-the-preview).

### <a name="monitor-how-many-requests-are-authorized-with-shared-key"></a>Monitorize quantos pedidos são autorizados com Chave Partilhada

Para acompanhar como estão a ser autorizados os pedidos a uma conta de armazenamento, utilize o Azure Metrics Explorer no portal Azure. Para obter mais informações sobre o Metrics Explorer, consulte [Começar com o Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md).

Siga estes passos para criar uma métrica que rastreie os pedidos feitos com Chave Partilhada ou SAS:

1. Navegue para a sua conta de armazenamento no portal do Azure. Na secção **de Monitorização,** selecione **Métricas**.
1. Selecione **Adicionar métrica**. No diálogo **métrico,** especifique os seguintes valores:
    1. Deixe o campo **Scope** definido para o nome da conta de armazenamento.
    1. Desa esta *Account*medida o **espaço de nomes.** Esta métrica reportará todos os pedidos contra a conta de armazenamento.
    1. Desa estação **métrica** para *transações.*
    1. Desagregar o campo **de agregação** para *Sum*.

    A nova métrica apresentará a soma do número de transações contra a conta de armazenamento durante um determinado intervalo de tempo. A métrica resultante aparece como mostra a seguinte imagem:

    :::image type="content" source="media/shared-key-authorization-prevent/configure-metric-account-transactions.png" alt-text="Screenshot mostrando como configurar a métrica para sum transações feitas com Chave Partilhada ou SAS":::

1. Em seguida, selecione o botão **de filtro Adicionar** para criar um filtro na métrica para o tipo de autorização.
1. No diálogo **do filtro,** especifique os seguintes valores:
    1. Desa estava o valor **da Propriedade** à *Autenticação.*
    1. Coloque o campo **do operador** no sinal de igual igual (=).
    1. No campo **Valores,** selecione *'Chave de Conta'* e *SAS*.
1. No canto superior direito, selecione o intervalo de tempo para o qual deseja ver a métrica. Também pode indicar como deve ser granular a agregação de pedidos, especificando intervalos entre 1 minuto e 1 mês. Por exemplo, defina o intervalo de **tempo** para 30 dias e a **granularidade** do Tempo para 1 dia para ver os pedidos agregados por dia ao longo dos últimos 30 dias.

Depois de configurar a métrica, os pedidos para a sua conta de armazenamento começarão a aparecer no gráfico. A imagem que se segue mostra pedidos que foram autorizados com Chave Partilhada ou feitos com um token SAS. Os pedidos são agregados por dia nos últimos 30 dias.

:::image type="content" source="media/shared-key-authorization-prevent/metric-shared-key-requests.png" alt-text="Screenshot mostrando como configurar a métrica para sum transações feitas com Chave Partilhada ou SAS":::

Também pode configurar uma regra de alerta para notificá-lo quando um certo número de pedidos autorizados com Chave Partilhada são feitos contra a sua conta de armazenamento. Para obter mais informações, consulte [Criar, ver e gerir alertas métricos utilizando o Azure Monitor](../../azure-monitor/platform/alerts-metric.md).

### <a name="analyze-logs-to-identify-clients-that-are-authorizing-requests-with-shared-key-or-sas"></a>Analise registos para identificar clientes que estão a autorizar pedidos com Chave Partilhada ou SAS

Os registos de armazenamento Azure captam detalhes sobre pedidos feitos contra a conta de armazenamento, incluindo como um pedido foi autorizado. Pode analisar os registos para determinar quais os clientes que estão a autorizar pedidos com Chave Partilhada ou um token SAS.

Para registar pedidos na sua conta de Armazenamento Azure para avaliar como são autorizados, pode utilizar o registo de armazenamento Azure no Azure Monitor (pré-visualização). Para obter mais informações, consulte [monitor Azure Storage](../common/monitor-storage.md).

O registo de armazenamento Azure no Azure Monitor suporta a utilização de consultas de registo para analisar dados de registo. Para consultar registos, pode utilizar um espaço de trabalho Azure Log Analytics. Para saber mais sobre consultas de log, consulte [Tutorial: Começar com consultas de Log Analytics](../../azure-monitor/log-query/get-started-portal.md).

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Criar uma definição de diagnóstico no portal Azure

Para registar os dados de Armazenamento Azure com o Azure Monitor e analisá-los com o Azure Log Analytics, tem primeiro de criar uma definição de diagnóstico que indique quais os tipos de pedidos e quais os serviços de armazenamento que pretende registar dados. Para criar uma definição de diagnóstico no portal Azure, siga estes passos:

1. Inscreva-se no registo de [armazenamento Azure na pré-visualização do Azure Monitor](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).
1. Crie um novo espaço de trabalho log Analytics na subscrição que contenha a sua conta de Armazenamento Azure ou utilize um espaço de trabalho log analytics existente. Depois de configurar o registo da sua conta de armazenamento, os registos estarão disponíveis no espaço de trabalho do Log Analytics. Para obter mais informações, consulte [Criar um espaço de trabalho log Analytics no portal Azure](../../azure-monitor/learn/quick-create-workspace.md).
1. Navegue para a sua conta de armazenamento no portal do Azure.
1. Na secção de Monitorização, selecione **definições de diagnóstico (pré-visualização)**.
1. Selecione o serviço de Armazenamento Azure para o qual pretende registar pedidos. Por exemplo, escolha **Blob** para registar pedidos para armazenamento Blob.
1. **Selecione Adicionar a definição de diagnóstico**.
1. Forneça um nome para a definição de diagnóstico.
1. Em **detalhes de categoria**, na secção de **registo,** escolha **StorageRead,** **StorageWrite**e **StorageDelete** para registar todos os pedidos de dados para o serviço selecionado.
1. Nos **detalhes do Destino**, selecione Enviar para Registar **Analítico**. Selecione a sua subscrição e o espaço de trabalho Log Analytics que criou anteriormente, como mostrado na imagem seguinte.

    :::image type="content" source="media/shared-key-authorization-prevent/create-diagnostic-setting-logs.png" alt-text="Screenshot mostrando como configurar a métrica para sum transações feitas com Chave Partilhada ou SAS":::

Pode criar uma definição de diagnóstico para cada tipo de recurso de Armazenamento Azure na sua conta de armazenamento.

Depois de criar a definição de diagnóstico, os pedidos para a conta de armazenamento são subsequentemente registados de acordo com essa definição. Para obter mais informações, consulte [Criar a definição de diagnóstico para recolher registos e métricas de recursos em Azure](../../azure-monitor/platform/diagnostic-settings.md).

Para obter uma referência dos campos disponíveis nos registos de armazenamento Azure no Azure Monitor, consulte [registos de recursos (pré-visualização)](../common/monitor-storage-reference.md#resource-logs-preview).

#### <a name="query-logs-for-requests-made-with-shared-key-or-sas"></a>Registos de consulta para pedidos feitos com Chave Partilhada ou SAS

Os registos de armazenamento Azure no Azure Monitor incluem o tipo de autorização que foi usada para fazer um pedido a uma conta de armazenamento. Para obter registos de pedidos feitos nos últimos sete dias que foram autorizados com A Chave Partilhada ou SAS, abra o seu espaço de trabalho Log Analytics. Em seguida, cole a seguinte consulta em uma nova consulta de log e executá-la. Esta consulta apresenta os dez endereços IP que mais frequentemente enviaram pedidos que foram autorizados com Chave Partilhada ou SAS:

```kusto
StorageBlobLogs
| where AuthenticationType in ("AccountKey", "SAS") and TimeGenerated > ago(7d)
| summarize count() by CallerIpAddress, UserAgentHeader, AccountName
| top 10 by count_ desc
```

Também pode configurar uma regra de alerta com base nesta consulta para notificá-lo sobre pedidos autorizados com Chave Partilhada ou SAS. Para obter mais informações, consulte [Criar, ver e gerir alertas de registo utilizando o Azure Monitor](../../azure-monitor/platform/alerts-log.md).

## <a name="remediate-authorization-via-shared-key"></a>Remediar a autorização via Chave Partilhada

Depois de ter analisado a forma como os pedidos à sua conta de armazenamento estão a ser autorizados, pode tomar medidas para impedir o acesso através da Chave Partilhada. Mas primeiro, precisa atualizar quaisquer aplicações que estejam a usar a autorização da Chave Partilhada para utilizar o Azure AD. Pode monitorizar registos e métricas conforme descrito no [Deteto o tipo de autorização utilizada pelas aplicações do cliente](#detect-the-type-of-authorization-used-by-client-applications) para acompanhar a transição. Para obter mais informações sobre a utilização do Azure AD com dados de bolhas e filas, consulte [o Acesso autorizado a bolhas e filas utilizando o Azure Ative Directory](storage-auth-aad.md).

Quando estiver confiante de que pode rejeitar com segurança pedidos autorizados com Chave Partilhada, pode definir a propriedade **AllowSharedKeyAccess** para a conta de armazenamento **falsa.**

A propriedade **AllowSharedKeyAccess** não é definida por padrão e não devolve um valor até que o tenha definido explicitamente. A conta de armazenamento permite pedidos que são autorizados com Chave Partilhada quando o valor da propriedade é **nulo** ou quando é **verdade.**

> [!WARNING]
> Se algum cliente estiver atualmente a aceder aos dados na sua conta de armazenamento com a Shared Key, então a Microsoft recomenda que migrar esses clientes para a Azure AD antes de desativar o acesso da Chave Partilhada à conta de armazenamento.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para não permitir a autorização da Chave Partilhada para uma conta de armazenamento no portal Azure, siga estes passos:

1. Navegue para a sua conta de armazenamento no portal do Azure.
1. Localizar a **definição de configuração** em **Definições**.
1. Definir **Permitir o acesso partilhado da chave** a **Desativados**.

    :::image type="content" source="media/shared-key-authorization-prevent/shared-key-access-portal.png" alt-text="Screenshot mostrando como configurar a métrica para sum transações feitas com Chave Partilhada ou SAS":::

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para não permitir a autorização da Chave Partilhada para uma conta de armazenamento com o Azure CLI, instale a versão 2.9.1 do Azure CLI ou posteriormente. Para mais informações, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli). Em seguida, configurar a propriedade **allowSharedKeyAccess** para uma conta de armazenamento nova ou existente.

O exemplo a seguir mostra como definir a propriedade **allowSharedKeyAccess** com Azure CLI. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```azurecli-interactive
$storage_account_id=$(az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query id \
    --output tsv)

az resource update \
    --ids $storage_account_id \
    --set properties.allowSharedKeyAccess=false

az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowSharedKeyAccess \
    --output tsv
```

---

Depois de não autorizar a Chave Partilhada, fazer um pedido à conta de armazenamento com autorização da Chave Partilhada falhará com o código de erro 403 (Proibido). O erro de retorna do Azure Storage indica que a autorização baseada em chaves não é permitida na conta de armazenamento.

### <a name="verify-that-shared-key-access-is-not-allowed"></a>Verifique se o acesso à Chave Partilhada não é permitido

Para verificar se a autorização da Chave Partilhada já não é permitida, pode tentar ligar para uma operação de dados com a chave de acesso à conta. O exemplo a seguir tenta criar um recipiente utilizando a chave de acesso. Esta chamada falhará quando a autorização da Chave Partilhada for proibida para a conta de armazenamento. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

> [!NOTE]
> Os pedidos anónimos não são autorizados e prosseguirão se tiver configurado a conta de armazenamento e o contentor para acesso anónimo ao público. Para mais informações, consulte o público [anónimo Configure acesso para contentores e bolhas.](../blobs/anonymous-read-access-configure.md)

### <a name="check-the-shared-key-access-setting-for-multiple-accounts"></a>Consulte a definição de acesso à Chave Partilhada para várias contas

Para verificar a definição de acesso da Chave Partilhada através de um conjunto de contas de armazenamento com o melhor desempenho, pode utilizar o Azure Resource Graph Explorer no portal Azure. Para saber mais sobre a utilização do Explorador de Gráficos de Recurso, consulte [Quickstart: Execute a sua primeira consulta de Gráfico de Recursos utilizando o Azure Resource Graph Explorer](/azure/governance/resource-graph/first-query-portal).

Executando a seguinte consulta no Explorador de Gráficos de Recurso devolve uma lista de contas de armazenamento e exibe a definição de acesso de Chave Partilhada para cada conta:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowSharedKeyAccess = parse_json(properties).allowSharedKeyAccess
| project subscriptionId, resourceGroup, name, allowSharedKeyAccess
```

## <a name="understand-how-disallowing-shared-key-affects-sas-tokens"></a>Entenda como a desafetar a Chave Partilhada afeta os tokens SAS

Quando a Chave Partilhada é proibida para a conta de armazenamento, o Azure Storage trata os tokens SAS com base no tipo de SAS e no serviço que é alvo do pedido. A tabela que se segue mostra como cada tipo de SAS é autorizado e como o Azure Storage irá lidar com esse SAS quando a propriedade **AllowSharedKeyAccess** para a conta de armazenamento é **falsa.**

| Tipo de SAS | Tipo de autorização | Comportamento quando AllowSharedKeyAccess é falso |
|-|-|-|
| Delegação de utilizadores SAS (apenas armazenamento blob) | Azure AD | O pedido é permitido. A Microsoft recomenda a utilização de uma delegação de utilizador SAS quando possível para uma segurança superior. |
| Serviço SAS | Chave Partilhada | O pedido é negado para o armazenamento blob. O pedido é permitido para o armazenamento de fila e mesa e para ficheiros Azure. Para obter mais informações, consulte [Pedidos com fichas SAS são permitidos para filas, tabelas e ficheiros quando AllowSharedKeyAccess é falso](#requests-with-sas-tokens-are-permitted-for-queues-tables-and-files-when-allowsharedkeyaccess-is-false) na secção **Sobre a pré-visualização.** |
| Conta SAS | Chave Partilhada | O pedido é negado para o armazenamento blob. O pedido é permitido para o armazenamento de fila e mesa e para ficheiros Azure. Para obter mais informações, consulte [Pedidos com fichas SAS são permitidos para filas, tabelas e ficheiros quando AllowSharedKeyAccess é falso](#requests-with-sas-tokens-are-permitted-for-queues-tables-and-files-when-allowsharedkeyaccess-is-false) na secção **Sobre a pré-visualização.** |

Para obter mais informações sobre assinaturas de acesso partilhado, consulte [Grant acesso limitado aos recursos de Armazenamento Azure utilizando assinaturas de acesso partilhado (SAS)](storage-sas-overview.md).

## <a name="consider-compatibility-with-other-azure-tools-and-services"></a>Considere a compatibilidade com outras ferramentas e serviços da Azure

Vários serviços da Azure utilizam a autorização da Chave Partilhada para comunicar com o Azure Storage. Se não autorizar a Chave Partilhada para uma conta de armazenamento, estes serviços não poderão aceder aos dados nessa conta, podendo as suas aplicações ser negativamente afetadas.

Algumas ferramentas Azure oferecem a opção de usar a autorização Azure AD para aceder ao Azure Storage. A tabela que se segue lista algumas ferramentas populares do Azure e nota se podem usar a Azure AD para autorizar pedidos para o Azure Storage.

| Ferramenta Azure | Autorização da AD AD para o Armazenamento Azure |
|-|-|
| Portal do Azure | Suportado. Para obter informações sobre a autorização da sua conta Azure AD a partir do portal Azure, consulte [Escolha como autorizar o acesso aos dados blob no portal Azure.](../blobs/authorize-blob-access-portal.md) |
| AzCopy | Suportado para armazenamento Blob. Para obter informações sobre a autorização de operações da AzCopy, consulte [Escolha como irá fornecer credenciais](storage-use-azcopy-v10.md#choose-how-youll-provide-authorization-credentials) de autorização na documentação da AzCopy. |
| Explorador do Storage do Azure | Suportado apenas para armazenamento blob e Azure Data Lake Storage Gen2. O acesso a AD AD ao armazenamento da fila não é suportado. Certifique-se de selecionar o inquilino AD Azure correto. Para mais informações, consulte [Começar com o Storage Explorer](/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows#sign-in-to-azure) |
| Azure PowerShell | Suportado. Para obter informações sobre como autorizar comandos PowerShell para operações de blob ou fila com Azure AD, consulte [comandos Run PowerShell com credenciais AD AD para aceder a dados blob](../blobs/authorize-active-directory-powershell.md) ou [executar comandos PowerShell com credenciais AD AD Aure para aceder a dados de fila](../queues/authorize-active-directory-powershell.md). |
| CLI do Azure | Suportado. Para obter informações sobre como autorizar comandos Azure CLI com Azure AD para acesso a dados de blob e fila, consulte [comandos Run Azure CLI com credenciais AD AD para aceder a dados de blob ou fila](authorize-data-operations-cli.md). |
| Hub IoT do Azure | Suportado. Para obter mais informações, consulte [o suporte do IoT Hub para redes virtuais.](../../iot-hub/virtual-network-support.md) |
| Azure Cloud Shell | Azure Cloud Shell é uma concha integrada no portal Azure. A Azure Cloud Shell acolhe ficheiros para persistência numa partilha de ficheiros Azure numa conta de armazenamento. Estes ficheiros tornar-se-ão inacessíveis se a autorização da Chave Partilhada for proibida para essa conta de armazenamento. Para obter mais informações, consulte [o armazenamento do Microsoft Azure Files](/azure/cloud-shell/overview#connect-your-microsoft-azure-files-storage). <br /><br /> Para executar comandos em Azure Cloud Shell para gerir contas de armazenamento para as quais o acesso de Chave Partilhada é proibido, certifique-se primeiro de que lhe foram concedidas as permissões necessárias a estas contas através do controlo de acesso baseado em funções Azure (Azure RBAC). Para obter mais informações, veja [o que é o controlo de acesso baseado em funções Azure (Azure RBAC)?](../../role-based-access-control/overview.md) |

## <a name="about-the-preview"></a>Sobre a pré-visualização

A pré-visualização para desativar a autorização da Chave Partilhada está disponível na nuvem pública do Azure. É suportado para contas de armazenamento que usam apenas o modelo de implementação do Gestor de Recursos Azure. Para obter informações sobre quais as contas de armazenamento que utilizam o modelo de implementação do Gestor de Recursos Azure, consulte [tipos de contas de armazenamento](storage-account-overview.md#types-of-storage-accounts).

Para se registar para a pré-visualização, consulte [Azure Storage Allow Shared Key Access Limited Public Preview](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUN1o4TUtUUzZBV0JYVlhKQ1FITDlVUUU0Ui4u).

> [!IMPORTANT]
> Esta pré-visualização destina-se apenas à utilização não-produção.

A pré-visualização inclui as limitações descritas nas seguintes secções.

### <a name="metrics-and-logging-report-all-requests-made-with-a-sas-regardless-of-how-they-are-authorized"></a>Métricas e registo de registo todos os pedidos feitos com um SAS independentemente de como são autorizados

As métricas azure e o registo no Azure Monitor não distinguem entre diferentes tipos de assinaturas de acesso partilhado na pré-visualização. O filtro **SAS** no Azure Metrics Explorer e o campo **SAS** no Azure Storage logging no Azure Monitor ambos reportam pedidos que são autorizados com qualquer tipo de SAS. No entanto, diferentes tipos de assinaturas de acesso partilhado são autorizadas de forma diferente, e comportam-se de forma diferente quando o acesso à Chave Partilhada é proibido:

- Um token SAS de serviço ou um token SAS de conta é autorizado com a Chave Partilhada e não será permitido num pedido de armazenamento Blob quando a propriedade **AllowSharedKeyAccess** estiver definida como **falsa.**
- Uma delegação de utilizador SAS é autorizada com Azure AD e será permitida a pedido de armazenamento Blob quando a propriedade **AllowSharedKeyAccess** estiver definida como **falsa**.

Quando estiver a avaliar o tráfego na sua conta de armazenamento, tenha em mente que as métricas e registos descritos no [Deteto o tipo de autorização utilizada pelas aplicações do cliente](#detect-the-type-of-authorization-used-by-client-applications) podem incluir pedidos feitos com uma delegação de utilizador SAS. Para obter mais informações sobre como o Azure Storage responde a um SAS quando a propriedade **AllowSharedKeyAccess** está definida como **falsa,** consulte [como a chave partilhada desafeta afeta os tokens SAS.](#understand-how-disallowing-shared-key-affects-sas-tokens)

### <a name="requests-with-sas-tokens-are-permitted-for-queues-tables-and-files-when-allowsharedkeyaccess-is-false"></a>Pedidos com fichas SAS são permitidos para filas, tabelas e ficheiros quando AllowSharedKeyAccess é falso

Quando o acesso da Chave Partilhada é proibido para a conta de armazenamento durante a pré-visualização, as assinaturas de acesso partilhada que os recursos de fila, tabela ou ficheiros Azure continuam a ser permitidas. Esta limitação aplica-se tanto aos tokens SAS de serviço como aos tokens SAS da conta. Ambos os tipos de SAS são autorizados com Chave Partilhada.

## <a name="next-steps"></a>Passos seguintes

- [Autorizar o acesso aos dados no Azure Storage](storage-auth.md)
- [Autorizar o acesso a bolhas e filas utilizando o Azure Ative Directory](storage-auth-aad.md)
- [Autorizar com a Chave Partilhada](/rest/api/storageservices/authorize-with-shared-key)