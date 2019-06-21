---
title: Gerir os custos do AWS e a utilização no Azure Cost Management
description: Este artigo ajuda-o a compreender como utilizar a análise de custos e orçamentos no Cost Management para gerir os custos do AWS e o uso.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 57e66d449b194662bfc03f7e130cf49c02a15793
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275707"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>Gerir os custos do AWS e a utilização no Azure

Depois de configurar e configurado a integração de relatório de custos do AWS e utilização para o Azure Cost Management, está pronto para começar a gerir os custos do AWS e o uso. Este artigo ajuda-o a compreender como utilizar a análise de custos e orçamentos no Cost Management para gerir os custos do AWS e o uso.

Se ainda não tiver configurado a integração, consulte [definir a cópia de segurança e configurar a integração de relatório de utilização do AWS](aws-integration-set-up-configure.md).

_Antes de começar_: Se não estiver familiarizado com a análise de custo, veja a [explorar e analisar os custos com a análise de custo](quick-acm-cost-analysis.md) início rápido. E, se não estiver familiarizado com orçamentos no Azure, consulte a [criar e gerir o Azure orçamentos](tutorial-acm-create-budgets.md) tutorial.

## <a name="view-aws-costs-in-cost-analysis"></a>Custos do AWS de ver na análise de custo

Os custos do AWS estão disponíveis na análise de custos nos seguintes âmbitos:

- Contas AWS ligado num grupo de gestão
- Custos de conta AWS ligado
- Custos de conta AWS consolidados

As secções seguintes descrevem como utilizar os âmbitos para que vê o custo e dados de utilização para cada um deles.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>Contas AWS ligado de vista num grupo de gestão

Visualizar os custos com o âmbito do grupo de gestão é a única forma de ver os custos agregados provenientes de diferentes subscrições e contas. Utilizar um grupo de gestão fornece uma vista de entre Clouds.

Na análise de custos, abra o Seletor de âmbito e selecione o grupo de gestão que contém as contas AWS ligado. Aqui está uma imagem de exemplo no portal do Azure:

![Exemplo de vista de âmbito selecione](./media/aws-integration-manage/select-scope01.png)



Eis um exemplo que mostra o custo de grupo de gestão em análise de custo, agrupado por fornecedor (Azure e AWS).

![Exemplo que mostra os custos do Azure e AWS para análise de custo de um trimestre em](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>Custos de conta do modo de exibição AWS ligado

Para ver os custos de conta de ligação do AWS, abra o Seletor de âmbito e selecione o AWS associado à conta. Tenha em atenção que contas ligadas estão associadas a um grupo de gestão, conforme definido no conector do AWS.

Eis um exemplo que mostra a seleção de AWS ligada âmbito da conta.

![Exemplo de vista de âmbito selecione](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>Vista de AWS consolidados os custos da conta

Para ver o AWS consolidada conta os custos, abra o Seletor de âmbito e selecione o AWS consolidados conta. Eis um exemplo que mostra a seleção de AWS consolidados âmbito da conta.

![Exemplo de vista de âmbito selecione](./media/aws-integration-manage/select-scope03.png)



Este âmbito fornece que uma exibição agregada de todos os AWS ligado contas associadas à conta AWS consolidados. Eis um exemplo que mostra os custos de AWS consolidados conta, agrupada por nome do serviço.

![Os custos de análise de custo de consolidados de exemplo que mostra o AWS](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>Dimensões disponíveis para filtrar e agrupar

A tabela seguinte descreve as dimensões disponíveis para o grupo e filtrar por na análise de custo.

| Dimensão | Cabeçalho do Amazon ATUAIS | Âmbitos | Comentários |
| --- | --- | --- | --- |
| Zona de disponibilidade | lineitem/AvailabilityZone | Todos |   |
| Location | produto/região | Todos |   |
| Medidor |   | Todos |   |
| Categoria do medidor | lineItem/ProductCode | Todos |   |
| Subcategoria do medidor | lineitem/UsageType | Todos |   |
| Operação | lineItem/operação | Todos |   |
| Resource | lineItem/ResourceId | Todos |   |
| Tipo de recurso | product/instanceType | Todos | Se o produto/instanceType for nulo, lineItem/UsageType é utilizado. |
| ResourceGuid | N/A | Todos | GUID de medidores do Azure. |
| Nome do Serviço | produto/ProductName | Todos | Se o produto/ProductName for nulo, lineItem/ProductCode é utilizado. |
| Camada de serviços |   |   |   |
| ID da subscrição | lineItem/UsageAccountId | Grupo de gestão de conta e consolidado |   |
| Nome da subscrição | N/A | Grupo de gestão de conta e consolidado | Os nomes das contas são recolhidos através da API de organização do AWS. |
| Tag | resourceTags/\* | Todos | O _utilizador:_ prefixo é removido das marcas definidas pelo utilizador para permitir que as etiquetas de entre Clouds. O _aws:_ prefixo intacto. |
| ID da conta de faturação | bill/PayerAccountId | Grupo de gestão |   |
| Nome da conta de faturação | N/A | Grupo de gestão | Os nomes das contas são recolhidos através da API de organização do AWS. |
| Fornecedor | N/A | Grupo de gestão | O AWS ou Azure. |

## <a name="set-budgets-on-aws-scopes"></a>Orçamentos de conjunto em âmbitos AWS

Utilize orçamentos para gerenciar de forma proativa os custos e impulsionar a responsabilidade da sua organização. Orçamentos são definidos na conta AWS consolidados e AWS âmbitos da conta associada. Eis um exemplo de orçamentos de uma conta AWS consolidados mostrado na gestão de custos:

![Conta de consolidados de exemplo que mostra os orçamentos de AWS](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>Processo de recolha de dados do AWS

Depois de configurar o conector do AWS, a recolha de dados e processos de deteção iniciar. Poderá demorar algumas horas para recolher todos os dados de utilização. A duração depende o tempo limite:

- O tempo necessário para processar os ficheiros ATUAIS que estão no bucket de AWS S3.
- O tempo necessário para criar a conta AWS consolidados e âmbitos de conta AWS ligados.
- O tempo e a frequência de AWS estiver a escrever os ficheiros de custo e o relatório de utilização no registo de S3

## <a name="aws-integration-pricing"></a>Preços de integração do AWS

Cada conector AWS obtém 90 dias de avaliação gratuitos. Durante a pré-visualização pública, não existe nenhum custo associado.

O preço de lista é 1% dos seus custos mensais de AWS. Todos os meses que lhe é cobrada com base nos seus custos faturados do mês anterior.

Aceder a APIs do AWS, pode implicar custos adicionais.

## <a name="aws-integration-limitations"></a>Limitações de integração do AWS

- Gestão de custos não suporta relatórios de custos que contenham vários tipos de moeda. Uma mensagem de erro é apresentada se selecionar um âmbito com várias moedas.
- Conectores de nuvem não suportam AWS GovCloud (EUA), AWS Gov ou AWS China.
- Gestão de custos mostra AWS _os custos de utilização_ apenas. Imposto, suporte, reembolsos, RI, créditos ou outros tipos de custos não são suportados ainda.

## <a name="troubleshooting-aws-integration"></a>Resolução de problemas de integração do AWS

Utilize as seguintes informações de resolução de problemas para resolver problemas comuns.

### <a name="no-permission-to-aws-linked-accounts"></a>Não tem permissão para contas AWS ligadas

**Código de erro:** _Não autorizado_

Existem duas formas de obter permissões para aceder aos custos do contas AWS ligado:

- Obtenha acesso ao grupo de gestão que tem as contas AWS ligado.
- Ter alguém lhe dar permissão à conta AWS ligado.

Por predefinição, o criador de conector do AWS é o proprietário de todos os objetos que o conector criado. O AWS, incluindo conta consolidada e o AWS ligado conta.

Para que seja possível verificar as definições do conector, terá de, pelo menos, uma função de contribuinte, leitor não pode verificar as definições do conector

### <a name="collection-failed-with-assumerole"></a>Coleção falhou com o AssumeRole

**Código de erro:** _FailedToAssumeRole_

Este erro significa que a gestão de custos não é possível chamar a API de AssumeRole AWS. Esse problema pode acontecer devido a um problema com a definição de função. Certifique-se de que as condições seguintes forem verdadeiras:

- O ID externo é igual ao mostrado na definição de função e a definição do conector.
- O tipo de função é definido como **AWS outra conta pertencentes a si ou parte 3.**
- O **exigir MFA** opção estiver desmarcada.
- É a conta AWS fidedigna na função de AWS _432263259397_.

### <a name="collection-failed-with-access-denied"></a>Coleção falhou com o acesso negado

- **Código de erro:** _AccessDeniedReportDefinitions_ 
- **Código de erro:** _AccessDeniedListReports_ 
- **Código de erro:** _AccessDeniedDownloadReport_ 

Este erro significa que a gestão de custos não consegue aceder aos ficheiros armazenados no bucket de Amazon S3 ATUAIS de mensagens. Certifique-se de que a política de JSON de AWS associada à função é semelhante ao exemplo mostrado na parte inferior a [criar uma função e a política no AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) secção.

### <a name="collection-failed-since-we-did-not-find-the-cost-and-usage-report"></a>Falha na recolha de uma vez que não foi encontrado o custo e o relatório de utilização

**Código de erro:** _FailedToFindReport_

Este erro significa que a gestão de custos não é possível encontrar o relatório de custo e a utilização que foi definido no conector. Certifique-se este não é eliminado e que a política de JSON de AWS associada à função é semelhante ao exemplo mostrado na parte inferior a [criar uma função e a política no AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) secção.

### <a name="unable-to-create-or-verify-connector-due-to-cost-and-usage-report-definitions-mismatch"></a>Não é possível criar ou verifique se o conector devido a erro de correspondência de definições do custo e o relatório de utilização

**Código de erro:** _ReportIsNotValid_

Este erro está relacionado com a definição de custos do AWS e o relatório de utilização, exigem definições específicas para este relatório, consulte os requisitos na [criar um relatório de custo e a utilização no AWS](aws-integration-set-up-configure.md#create-a-cost-and-usage-report-in-aws)

## <a name="next-steps"></a>Passos Seguintes

- Se já não tiver configurado o seu ambiente do Azure com grupos de gestão, consulte [inicial do programa de configuração de grupos de gestão](../governance/management-groups/index.md#initial-setup-of-management-groups).
