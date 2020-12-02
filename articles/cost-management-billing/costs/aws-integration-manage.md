---
title: Gerir a utilização e os custos do AWS no Azure Cost Management
description: Este artigo ajuda-o a compreender como utilizar a análise de custos e os orçamentos no Cost Management para gerir os custos e a utilização do AWS.
author: bandersmsft
ms.author: banders
ms.date: 10/16/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: matrive
ms.custom: ''
ms.openlocfilehash: 5fed70ccdbebbd178412c416f37c2e9001a81f38
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188110"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>Gerir os custos e a utilização do AWS no Azure

Após definir e configurar a integração do relatório Custos e Utilização do AWS para o Azure Cost Management, estará pronto para começar a gerir os custos e a utilização do AWS. Este artigo ajuda-o a compreender como utilizar a análise de custos e os orçamentos no Cost Management para gerir os custos e a utilização do AWS.

Se ainda não configurou a integração, veja [Definir e configurar a integração do relatório Utilização do AWS](aws-integration-set-up-configure.md).

_Antes de começar_: Se não estiver familiarizado com a análise de custos, veja o início rápido [Explorar e analisar os custos com a Análise de custos](quick-acm-cost-analysis.md). Se também não estiver familiarizado com os orçamentos no Azure, veja o tutorial [Criar e gerir orçamentos do Azure](tutorial-acm-create-budgets.md).

## <a name="view-aws-costs-in-cost-analysis"></a>Ver os custos do AWS na análise de custos

Os custos do AWS estão disponíveis na Análise de Custos nos seguintes âmbitos:

- Contas associadas do AWS num grupo de gestão
- Custos de contas associadas do AWS
- Custos da conta consolidada do AWS

As secções seguintes descrevem como utilizar os âmbitos para que veja os dados dos custos e da utilização de cada um.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>Ver as contas associadas do AWS num grupo de gestão

A visualização dos custos com o âmbito do grupo de gestão é a única forma de ver os custos agregados provenientes de diferentes subscrições do Azure e de contas associadas do AWS. A utilização de um grupo de gestão fornece uma vista entre clouds para ver os custos do Azure e do AWS em conjunto.

Na análise de custos, abra o seletor de âmbito e selecione o grupo de gestão que detém as contas associadas do AWS. Veja a seguir uma imagem de exemplo no portal do Azure:

:::image type="content" source="./media/aws-integration-manage/select-scope01.png" alt-text="Exemplo da vista Selecionar âmbito com contas associadas num grupo de gestão" :::

Veja a seguir um exemplo que mostra o custo do grupo de gestão na análise de custos, agrupado por Fornecedor (Azure e AWS).

:::image type="content" source="./media/aws-integration-manage/cost-analysis-aws-azure.png" alt-text="Exemplo a mostrar os custos do Azure e do AWS para um trimestre na análise de custos" lightbox="./media/aws-integration-manage/cost-analysis-aws-azure.png" :::

> [!NOTE]
> Atualmente, os grupos de gestão não são suportados para clientes do Contrato de Cliente Microsoft (MCA). Os clientes do MCA podem criar o conector e ver os respetivos dados do AWS. No entanto, os clientes do MCA não podem ver os custos do Azure e os custos do AWS juntos num grupo de gestão.

### <a name="view-aws-linked-account-costs"></a>Ver os custos de contas associadas do AWS

Para ver os custos de contas associadas do AWS, abra o seletor de âmbito e selecione a conta associada do AWS. Tenha em atenção que as contas associadas estão associadas a um grupo de gestão, conforme definido no conector do AWS.

Veja a seguir um exemplo que mostra a seleção de um âmbito de conta associada do AWS.

:::image type="content" source="./media/aws-integration-manage/select-scope02.png" alt-text="Exemplo da vista Selecionar âmbito que mostra contas associadas do AWS" :::

### <a name="view-aws-consolidated-account-costs"></a>Ver os custos da conta consolidada do AWS

Para ver os custos da conta consolidada do AWS, abra o seletor de âmbito e selecione a conta consolidada do AWS. Veja a seguir um exemplo que mostra a seleção de um âmbito de conta consolidada do AWS.

:::image type="content" source="./media/aws-integration-manage/select-scope03.png" alt-text="Exemplo da vista Selecionar âmbito com contas consolidadas" :::

Este âmbito apresenta uma vista agregada de todas as contas associadas do AWS associadas à conta consolidada do AWS. Veja a seguir um exemplo que mostra os custos para uma conta consolidada do AWS, agrupados por nome de serviço.

:::image type="content" source="./media/aws-integration-manage/cost-analysis-aws-consolidated.png" alt-text="Exemplo a mostrar os custos consolidados do AWS na análise de custos" lightbox="./media/aws-integration-manage/cost-analysis-aws-consolidated.png" :::

### <a name="dimensions-available-for-filtering-and-grouping"></a>Dimensões disponíveis para filtragem e agrupamento

A tabela seguinte descreve as dimensões disponíveis para agrupar e filtrar por análise de custos.

| Dimensão | Cabeçalho CUR Amazon | Âmbitos | Comentários |
| --- | --- | --- | --- |
| Zona de disponibilidade | lineitem/AvailabilityZone | Todos |   |
| Localização | product/Region | Todos |   |
| Medidor |   | Todos |   |
| Categoria do medidor | lineItem/ProductCode | Todos |   |
| Subcategoria do medidor | lineitem/UsageType | Todos |   |
| Operação | lineItem/Operation | Todos |   |
| Recurso | lineItem/ResourceId | Todos |   |
| Tipo de recurso | product/instanceType | Todos | Se product/instanceType for nulo, será utilizado lineItem/UsageType. |
| ResourceGuid | N/D | Todos | GUID do medidor do Azure. |
| Nome do serviço | product/ProductName | Todos | Se product/ProductName for nulo, será utilizado lineItem/ProductCode. |
| Escalão de serviço |   |   |   |
| ID da subscrição | lineItem/UsageAccountId | Conta consolidada e grupo de gestão |   |
| Nome da subscrição | N/D | Conta consolidada e grupo de gestão | Os nomes de conta são recolhidos com a API da Organização do AWS. |
| Etiqueta | resourceTags | Todos | O prefixo _utilizador:_ é removido das etiquetas definidas pelo utilizador para permitir etiquetas entre clouds. O prefixo _aws:_ é deixado intacto. |
| ID da conta de faturação | bill/PayerAccountId | Grupo de gestão |   |
| Nome da conta de faturação | N/D | Grupo de gestão | Os nomes de conta são recolhidos com a API da Organização do AWS. |
| Fornecedor | N/D | Grupo de gestão | AWS ou Azure. |

## <a name="set-budgets-on-aws-scopes"></a>Definir os orçamentos nos âmbitos do AWS

Utilize orçamentos para gerir proativamente os custos e impulsionar a responsabilidade na sua organização. Os orçamentos são definidos nos âmbitos da conta consolidada do AWS e das contas associadas do AWS. Veja a seguir um exemplo de orçamentos de uma conta consolidada do AWS apresentada no Cost Management:

:::image type="content" source="./media/aws-integration-manage/budgets-aws-consolidated-account01.png" alt-text="Exemplo a mostrar os orçamentos de uma conta consolidada do AWS" :::

## <a name="aws-data-collection-process"></a>Processo de recolha de dados do AWS

Após definir o conector do AWS, a recolha de dados e os processos de deteção são iniciados. Pode demorar algumas horas para recolher todos os dados de utilização. A duração depende:

- Do tempo necessário para processar os ficheiros CUR que estão no registo AWS S3.
- Do tempo necessário para criar os âmbitos Conta consolidada do AWS e Conta associadas do AWS.
- Do tempo e da frequência de gravação dos ficheiros Relatório dos Custos e da Utilização do AWS no registo S3

## <a name="aws-integration-pricing"></a>Preços de integração do AWS

Cada conector do AWS recebe uma avaliação gratuita de 90 dias.

O preço de tabela é de 1% dos custos mensais do AWS. É-lhe faturado todos os meses com base nos custos faturados no mês anterior.

O acesso às APIs do AWS pode incorrer em custos adicionais no AWS.

## <a name="aws-integration-limitations"></a>Limitações da integração do AWS

- Os orçamentos no Cost Management não suportam grupos de gestão com várias moedas. Os grupos de gestão com várias moedas não verão uma avaliação do orçamento. É mostrada uma mensagem de erro se selecionar um grupo de gestão que tenha várias moedas quando criar um orçamento.
- Os conectores da cloud não suportam o AWS GovCloud (EUA), AWS Gov ou AWS China.
- O Cost Management mostra apenas os _custos de utilização_ do AWS. Impostos, apoios, reembolsos, RI, créditos ou quaisquer outros tipos de custos ainda não são suportados.

## <a name="troubleshooting-aws-integration"></a>Resolução de problemas da integração do AWS

Utilize as seguintes informações de resolução de problemas para resolver problemas comuns.

### <a name="no-permission-to-aws-linked-accounts"></a>Sem permissão para Contas associadas do AWS

**Código de erro:** _Não autorizado_

Existem duas formas para obter permissões para aceder aos custos das contas associadas do AWS:

- Obter acesso ao grupo de gestão que detém as Contas associadas do AWS.
- Ser-lhe concedida permissão para aceder à conta associada do AWS.

Por predefinição, o criador do conector do AWS é o proprietário de todos os objetos que o conector criou. Incluindo, a conta consolidada do AWS e a conta associada do AWS.

Para verificar as definições do conector, precisará de, pelo menos, uma função de contribuidor. A função de leitor não consegue verificar as definições do conector

### <a name="collection-failed-with-assumerole"></a>Falha na recolha com AssumeRole

**Código de erro:** _FailedToAssumeRole_

Este erro significa que o Cost Management não conseguiu ligar à API AssumeRole do AWS. Este problema pode ocorrer devido a um problema na definição de função. Verifique se as seguintes condições são verdadeiras:

- O ID externo é idêntico ao que se encontra na definição de função e na definição de conector.
- O tipo de função está definido como **Outra conta do AWS que lhe pertence a si ou a terceiros**.
- A opção **Requerer MFA** está desmarcada.
- A conta do AWS fidedigna na Função do AWS é _432263259397_.

### <a name="collection-failed-with-access-denied---cur-report-definitions"></a>Falha na recolha com Acesso Negado – Definições do relatório CUR

**Código de erro:** _AccessDeniedReportDefinitions_

Este erro significa que o Cost Management não consegue ver as definições do relatório de Custos e Utilização. Esta permissão é utilizada para confirmar que o CUR está definido conforme o esperado pelo Azure Cost Management. Veja [Criar um relatório de Custos e Utilização no AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws).

### <a name="collection-failed-with-access-denied---list-reports"></a>Falha na recolha com Acesso Negado – Listar relatórios

**Código de erro:** _AccessDeniedListReports_

Este erro significa que o Cost Management não consegue listar o objeto no registo S3 onde o CUR está localizado. A política IAM do AWS requer uma permissão no registo e nos objetos armazenados no registo. Ver [Criar uma função e uma política no AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="collection-failed-with-access-denied---download-report"></a>Falha na recolha com Acesso Negado – Transferir relatório

**Código de erro:** _AccessDeniedDownloadReport_

Este erro significa que o Cost Management não consegue aceder e transferir os ficheiros CUR armazenados no registo Amazon S3. Confirme que a política JSON do AWS anexada à função se assemelha ao exemplo apresentado na parte inferior da secção [Criar uma função e uma política no AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="collection-failed-since-we-did-not-find-the-cost-and-usage-report"></a>Falha na recolha, uma vez que não encontramos o Relatório de Custos e Utilização

**Código de erro:** _FailedToFindReport_

Este erro significa que o Cost Management não consegue localizar o relatório de Custos e Utilização que foi definido no conector. Verifique se não foi eliminado e se a política JSON do AWS anexada à função se assemelha ao exemplo apresentado na parte inferior da secção [Criar uma função e uma política no AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws).

### <a name="unable-to-create-or-verify-connector-due-to-cost-and-usage-report-definitions-mismatch"></a>Não foi possível criar ou verificar o conector devido a um erro de correspondência das definições do Relatório de Custos e Utilização

**Código de erro:** _ReportIsNotValid_

Este erro está relacionado com a definição do Relatório de Custos e Utilização, uma vez que são necessárias definições específicas para este relatório. Veja requisitos em [Criar um relatório de Custos e Utilização no AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws).

### <a name="internal-error-when-creating-connector"></a>Erro interno ao criar o conector

**Código de erro:** _Criar conector - Falha ao criar o conector &lt;ConnectorName&gt;. Motivo: Erro interno. Confirme que foram fornecidas as propriedades corretas do AWS._

Este erro pode ocorrer quando o conector e a subscrição do AWS estão em grupos de gestão diferentes. O conector e a subscrição do AWS têm de estar no mesmo grupo de gestão.

## <a name="next-steps"></a>Próximos passos

- Se ainda não tiver configurado o seu ambiente do Azure com grupos de gestão, veja [Configuração inicial dos grupos de gestão](../../governance/management-groups/overview.md#initial-setup-of-management-groups).
