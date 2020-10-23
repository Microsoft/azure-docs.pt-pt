---
title: Analisar custos com a Aplicação Power BI
description: Este artigo explica como instalar e utilizar a Aplicação Power BI do Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 10/14/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: benshy
ms.openlocfilehash: 7282ef9269c1168214fe57d3faa5488e531af8da
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131127"
---
# <a name="analyze-cost-with-the-azure-cost-management-power-bi-app-for-enterprise-agreements-ea"></a>Analisar o custo com a Aplicação Power BI do Azure Cost Management para Contratos Enterprise (EA)

Este artigo explica como instalar e utilizar a aplicação Power BI do Azure Cost Management. A aplicação ajuda-o a analisar e gerir os seus custos do Azure no Power BI. Pode utilizar a aplicação para monitorizar custos, tendências de utilização e identificar opções de otimização de custos para reduzir as suas despesas.

Pode utilizar a aplicação tal como está ou modificá-la para expandir os filtros, as vistas e as visualizações predefinidos para personalizar de acordo com as suas necessidades. Em seguida, utilize-a para associar dados adicionais para criar relatórios personalizados para obter perspetivas holísticas do custo global do seu negócio.

Atualmente, a Aplicação Power BI do Azure Cost Management suporta apenas clientes com um [Contrato Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).

> [!NOTE]
> As aplicações de modelo do Power BI não suportam a transferência do ficheiro PBIX.

## <a name="prerequisites"></a>Pré-requisitos

- É necessária uma [licença do Power BI Pro](/power-bi/service-self-service-signup-for-power-bi) para instalar e utilizar a aplicação.
- Para se ligar aos dados, tem de utilizar uma conta de [Administrador Empresarial](../manage/understand-ea-roles.md). A função Administrador Empresarial (só de leitura) é suportada.

## <a name="installation-steps"></a>Passos de instalação

Para instalar a aplicação:

1. Abra a [Aplicação Power BI do Azure Cost Management](https://aka.ms/costmgmt/ACMApp).
2. Na página Power BI AppSource, selecione **Obter agora**.
3. Selecione **Continuar** para concordar com os termos de utilização e a política de privacidade.
4. Na caixa **Instalar esta aplicação do Power BI**, selecione **Instalar**.
5. Se necessário, crie uma área de trabalho e selecione **Continuar**.
6. Quando a instalação estiver concluída, é apresentada uma notificação a indicar que a sua nova aplicação está pronta.
7. Selecione **Ir para a aplicação**.
8. Em **Comece já com a sua nova aplicação**, em **Ligar os dados**, selecione **Ligar**.  
  ![Comece já com a sua nova aplicação - Ligar](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/connect-data2.png)
9. Na caixa de diálogo que aparece, introduza o seu número de inscrição de EA em **BillingProfileIdOrEnrollmentNumber**. Especifique o número de meses de dados a obter. Mantenha o valor de **Âmbito** predefinido **Enrollment Number** e, em seguida, selecione **Seguinte**.  
  ![Introduzir informações de inscrição de EA](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png)  
10. A caixa de diálogo seguinte liga ao Azure e obtém os dados necessários para recomendações de instância reservada. *Mantenha os valores predefinidos tal como configurados* e selecione **Iniciar sessão**.  
  ![Captura de ecrã que mostra a caixa de diálogo Ligar à Aplicação Azure Cost Management com valores predefinidos.](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit.png)  
11. O passo final de instalação liga à sua inscrição de EA e requer uma conta de [Administrador Enterprise](../manage/understand-ea-roles.md). Selecione **Iniciar sessão** para fazer a autenticação com a sua inscrição de EA. Este passo também inicia uma ação de atualização de dados no Power BI.  
  ![Ligar à inscrição de EA](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-auth.png)  
    > [!NOTE]
    > O processo de atualização de dados pode demorar bastante tempo a ser concluído. A duração depende do número de meses especificados e da quantidade de dados necessários para sincronizar.
12. Para verificar o estado da atualização de dados, selecione o separador **Conjuntos de dados** na área de trabalho. Olhe para o lado do carimbo de data/hora de Atualizado. Se ainda estiver a ser atualizado, verá um indicador que mostra que a atualização está em curso.  
  ![Atualizar dados](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/data-refresh2.png)

Depois de concluída a atualização de dados, selecione a Aplicação Azure Cost Management para visualizar os relatórios pré-criados.

## <a name="reports-available-with-the-app"></a>Relatórios disponíveis na aplicação

Os seguintes relatórios estão disponíveis na aplicação.

**Getting Started** (Introdução) - Fornece ligações úteis para documentação e ligações para dar feedback.

**Account overview** (Descrição geral da conta) - O relatório mostra um resumo mensal de informações, incluindo:

- Custos em relação a créditos
- Novas compras
- Custos do Azure Marketplace
- Utilizações excedidas e total de custos

**Usage by Subscriptions and Resource Groups** (Utilização por Subscrições e Grupos de Recursos) - Fornece uma vista de custo ao longo do tempo e gráficos que mostram o custo por subscrição e grupo de recursos.

**Usage by Services** (Utilização por Serviços) - Fornece uma vista ao longo do tempo da utilização por MeterCategory. Pode controlar os seus dados de utilização e analisar anomalias para compreender os picos ou as quedas de utilização.

**Top 5 Usage drivers** (5 Principais fatores de Utilização) - O relatório mostra um resumo filtrado dos custos pelos 5 principais MeterCategory e MeterName correspondente.

**Windows Server AHB Usage** (Utilização do AHB do Windows Server) - O relatório mostra o número de máquinas virtuais que têm o Benefício Híbrido do Azure ativado. Também mostra uma contagem de núcleos/vCPUs utilizados pelas máquinas virtuais.

![Relatório completo dos Benefícios Híbridos do Azure](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report-full.png)

O relatório também identifica as VMs do Windows nas quais o Benefício Híbrido está **ativado**, mas há _menos do que_ 8 vCPUs. Mostra também as VMs nas quais o Benefício Híbrido não está **ativado**, que tenham 8 _ou mais_ vCPUs. Esta informação ajuda-o a utilizar totalmente o seu Benefício Híbrido. Aplique o benefício às suas máquinas virtuais mais caras para maximizar as suas poupanças potenciais.

![Benefícios Híbridos do Azure – Menos de 8 vCPUs e vCPUs não ativadas](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ahb-report.png)

**RI Chargeback** (Estorno de RI) - O relatório ajuda-o a compreender onde e que quantidade de um benefício de instância reservada (RI) é aplicado por região, subscrição, grupo de recursos ou recurso. O relatório utiliza dados de utilização amortizados para mostrar a vista.

Pode aplicar um filtro em _chargetype_ para ver os dados de subutilização de RI.

Para obter mais informações sobre os dados amortizados, veja [Obter custos e utilização de reservas do Contrato Enterprise](../reservations/understand-reserved-instance-usage-ea.md).

**RI Savings** (Poupanças de RI) - O relatório mostra as poupanças acumuladas pelas reservas para subscrição, grupo de recursos e o nível de recurso. Apresenta:

- Custo com a reserva
- Custo a pedido estimado se a reserva não se aplicasse à utilização
- Poupança de custos acumulada da reserva

 O relatório subtrai qualquer custo de perda de reserva subutilizada da poupança total. A perda não ocorreria sem uma reserva.

Pode utilizar os dados de utilização amortizados como base para os dados.

<a name="shared-recommendation"></a>
**VM RI Coverage (shared recommendation)** (Cobertura de RI de VM (recomendação partilhada)) - O relatório está dividido entre a utilização de VM a pedido e a utilização de VM de RI durante o período selecionado. Fornece recomendações para compras de RI de VM num âmbito partilhado.

Para utilizar o relatório, selecione o filtro de desagregação.

![Relatório de cobertura de RI de VM – selecionar desagregação](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-drill-down2.png)

Selecione a região que pretende analisar. Em seguida, selecione o grupo de flexibilidade de tamanho de instância, e assim sucessivamente.

Para cada nível de desagregação, são aplicados ao relatório os seguintes filtros:

- Os dados de cobertura à direita correspondem ao filtro que mostra a quantidade de utilização cobrada com a taxa a pedido vs a quantidade coberta pela reserva.
- As recomendações também são filtradas.

A tabela de recomendações fornece recomendações em relação à compra de reservas, com base nos tamanhos de VM utilizados.

Os valores de _Normalized Size_ (Tamanho Normalizado) e _Recommended Quantity Normalized_ (Quantidade Recomendada Normalizada) ajudam a normalizar a compra para o tamanho mais pequeno para um grupo de flexibilidade de tamanho de instância. A informação é útil se pretende comprar apenas uma reserva para todos os tamanhos no grupo de flexibilidade de tamanho de instância.

![Recomendações de RI](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ri-recomendations.png)

**VM RI Coverage (single recommendation)** - (Cobertura de RI de VM (recomendação individual)) - O relatório está dividido entre a utilização de VM a pedido e a utilização de VM de RI durante o período de tempo selecionado. Fornece recomendações para compras de RI de VM num âmbito de subscrição.

Para obter mais detalhes sobre como utilizar o relatório, veja a secção [Cobertura de RI de VM (recomendação partilhada)](#shared-recommendation).

**RI purchases** (Compras de RI) - O relatório mostra as compras de RI durante o período especificado.

**Price sheet** (Folha de preços) - O relatório mostra uma lista detalhada de preços específicos de uma Conta de faturação ou inscrição de EA.

## <a name="troubleshoot-problems"></a>Resolução de problemas

Se estiver a ter problemas com a aplicação Power BI, as informações de resolução de problemas seguintes podem ajudar.

### <a name="error-processing-the-data-in-the-dataset"></a>Erro ao processar os dados no conjunto de dados

Pode obter um erro a indicar:

```
There was an error when processing the data in the dataset.
Data source error: {"error":{"code":"ModelRefresh_ShortMessage_ProcessingError","pbi.error":{"code":"ModelRefresh_ShortMessage_ProcessingError","parameters":{},"details":[{"code":"Message","detail":{"type":1,"value":"We cannot convert the value \"Required Field: 'Enr...\" to type List."}}],"exceptionCulprit":1}}} Table: <TableName>.
```

Deverá aparecer um nome de tabela em vez de `<TableName>`.

#### <a name="cause"></a>Causa

O valor predefinido de **Âmbito** de `Enrollment Number` foi mudado na ligação ao Cost Management.

#### <a name="solution"></a>Solução

Volte a ligar-se ao Cost Management e defina o valor de **Âmbito** como `Enrollment Number`. Não introduza o número de inscrição da sua organização; em alternativa, escreva `Enrollment Number` exatamente como aparece na imagem seguinte.

![Introduzir as informações de inscrição de EA](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/ea-number.png)  

### <a name="budgetamount-error"></a>Erro BudgetAmount

Pode obter um erro a indicar:

```
Something went wrong
There was an error when processing the data in the dataset.
Please try again later or contact support. If you contact support, please provide these details.
Data source error: The 'budgetAmount' column does not exist in the rowset. Table: Budgets.
```

#### <a name="cause"></a>Causa

Este erro ocorre devido a um erro com os metadados subjacentes. O problema acontece porque não há orçamento disponível em **Gestão de Custos > Orçamento** no portal do Azure. A correção do erro está em processo de ser implementada no Power BI Desktop e no serviço Power BI. 

#### <a name="solution"></a>Solução

- Até o erro ser corrigido, pode resolver o problema ao adicionar um orçamento de teste no portal do Azure ao nível de inscrição da conta de faturação/EA. O orçamento de teste desbloqueia a ligação ao Power BI. Para obter mais informações sobre como criar um orçamento, veja [Tutorial: Create and manage Azure budgets](tutorial-acm-create-budgets.md) (Tutorial: criar e gerir orçamentos do Azure).


### <a name="invalid-credentials-for-azureblob-error"></a>Credenciais inválidas para o erro AzureBlob

Pode obter um erro a indicar:

```
Failed to update data source credentials: The credentials provided for the AzureBlobs source are invalid.
```

#### <a name="cause"></a>Causa

Este erro ocorre se alterar o método de autenticação para a ligação de blobs AutoFitComboMeter.

#### <a name="solution"></a>Solução

1. Ligue aos seus dados.
1. Depois de introduzir a sua inscrição EA e o número de meses, certifique-se de que mantém o valor predefinido **Anónimo** para o Método de autenticação e **Nenhum** para a Definição do nível de privacidade.  
  ![Captura de ecrã que mostra a caixa de diálogo Ligar à Aplicação Azure Cost Management com valores Anónimo e Nenhum introduzidos.](./media/analyze-cost-data-azure-cost-management-power-bi-template-app/autofit-troubleshoot.png)  
1. Na página seguinte, defina **OAuth2** para o Método de autenticação e **Nenhum** para o Nível de privacidade. Em seguida, inicie sessão para fazer a autenticação com a sua inscrição. Este passo também inicia uma atualização de dados no Power BI.


## <a name="data-reference"></a>Referência de dados

As seguintes informações resumem os dados disponíveis através da aplicação. Há também ligações para APIs que dão detalhes aprofundados para campos de dados e valores.

| **Referência de tabela** | **Descrição** |
| --- | --- |
| **AutoFitComboMeter** | Dados incluídos na aplicação para normalizar a recomendação e utilização da RI para o tamanho mais pequeno no grupo da família de instâncias. |
| [**Resumo de saldo**](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary#response) | Resumo de saldo para Contratos Enterprise. |
| [**Orçamentos**](/rest/api/consumption/budgets/get#definitions) | Detalhes do orçamento para ver os custos reais ou a utilização em relação aos alvos do orçamento existente. |
| [**Folhas de preços**](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet#see-also) | Taxas de medidor aplicáveis para o perfil de faturação fornecido ou a inscrição de EA. |
| [**Custos de RI**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges#response) | Custos associados às suas instâncias reservadas nos últimos 24 meses. |
| [**Recomendações de RI (partilhadas)** ](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response) | Recomendações de compra de instâncias reservadas com base em todas as suas tendências de utilização da subscrição nos últimos 7, 30 ou 60 dias. |
| [**Recomendações de RI (individuais)** ](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#response-1) | Recomendações de compra de instâncias reservadas com base nas tendências de utilização da sua subscrição individual nos últimos 7, 30 ou 60 dias. |
| [**Detalhes de utilização de RI**](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#response) | Detalhes de consumo das suas instâncias reservadas existentes durante o último mês. |
| [**Resumo de utilização de RI**](/rest/api/consumption/reservationssummaries/list) | Percentagem de utilização de reserva diária do Azure. |
| [**Detalhes de utilização**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) | Uma discriminação das quantidades consumidas e dos custos estimados para o perfil de faturação indicado na inscrição de EA. |
| [**Detalhes de utilização amortizados**](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#usage-details-field-definitions) | Uma discriminação das quantidades consumidas e dos custos amortizados estimados para o perfil de faturação indicado na inscrição de EA. |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a configuração de dados, atualização, partilha de relatórios e personalização adicional de relatórios, veja os seguintes artigos:

- [Configurar a atualização agendada](/power-bi/refresh-scheduled-refresh)
- [Partilhar dashboards e relatórios do Power BI com colegas e outras pessoas](/power-bi/service-share-dashboards)
- [Subscrever relatórios e dashboards no serviço Power BI para si e para outras pessoas](/power-bi/service-report-subscribe)
- [Transferir um relatório do serviço Power BI para o Power BI Desktop](/power-bi/service-export-to-pbix)
- [Guardar um relatório no serviço Power BI e no Power BI Desktop](/power-bi/service-report-save)
- [Criar um relatório no serviço Power BI através da importação de um conjunto de dados](/power-bi/service-report-create-new)