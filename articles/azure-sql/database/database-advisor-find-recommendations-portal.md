---
title: Aplicar recomendações de desempenho
description: Utilize o portal Azure para encontrar recomendações de desempenho que possam otimizar o desempenho da sua base de dados.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/19/2018
ms.openlocfilehash: 27fcbcec07012dc34c846f316fccf788a0d25329
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84041319"
---
# <a name="find-and-apply-performance-recommendations"></a>Localizar e aplicar recomendações de desempenho
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Pode utilizar o portal Azure para encontrar recomendações de desempenho que possam otimizar o desempenho da sua base de dados na Base de Dados Azure SQL ou corrigir algum problema identificado na sua carga de trabalho. A página de **recomendação** de Desempenho no portal Azure permite-lhe encontrar as principais recomendações com base no seu impacto potencial.

## <a name="viewing-recommendations"></a>Recomendações de visualização

Para visualizar e aplicar recomendações de desempenho, precisa das permissões corretas [de controlo de acesso baseadas](../../role-based-access-control/overview.md) em funções em Azure. **Reader,** **SQL DB As** permissões do Contribuinte São necessárias para ver recomendações, e as permissões do **Contribuinte** **SQL DB** são necessárias para executar quaisquer ações; criar ou largar índices e cancelar a criação de índices.

Utilize os seguintes passos para encontrar recomendações de desempenho no portal Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Vá a todas as bases de dados SQL de **todos os**  >  **serviços**e selecione a sua base de dados.
3. Navegue para **a recomendação de desempenho** para visualizar as recomendações disponíveis para a base de dados selecionada.

Recomendações de desempenho são apresentadas no quadro semelhante à que se apresenta na figura seguinte:

![Recomendações](./media/database-advisor-find-recommendations-portal/recommendations.png)

As recomendações são ordenadas pelo seu impacto potencial no desempenho nas seguintes categorias:

| Impacto | Descrição |
|:--- |:--- |
| Alta |Recomendações de alto impacto devem proporcionar o impacto de desempenho mais significativo. |
| Médio |As recomendações de impacto médio devem melhorar o desempenho, mas não substancialmente. |
| Baixa |As recomendações de baixo impacto devem proporcionar um melhor desempenho do que sem, mas as melhorias podem não ser significativas. |

> [!NOTE]
> A Base de Dados Azure SQL precisa de monitorizar as atividades pelo menos durante um dia, a fim de identificar algumas recomendações. A Base de Dados Azure SQL pode facilmente otimizar para padrões de consulta consistentes do que para explosões aleatórias de atividade. Se as recomendações não estiverem atualmente disponíveis, a página de **recomendação** de Desempenho fornece uma mensagem explicando porquê.

Pode também ver o estado das operações históricas. Selecione uma recomendação ou estado para ver mais informações.

Aqui está um exemplo da recomendação "Criar índice" no portal Azure.

![Criar índice](./media/database-advisor-find-recommendations-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Aplicação de recomendações

A Base de Dados Azure SQL dá-lhe controlo total sobre como as recomendações são ativadas utilizando qualquer uma das seguintes três opções:

* Aplicar recomendações individuais uma de cada vez.
* Ativar a sintonização automática para aplicar automaticamente recomendações.
* Para implementar uma recomendação manualmente, execute o script T-SQL recomendado na sua base de dados.

Selecione qualquer recomendação para visualizar os seus detalhes e, em seguida, clique em **Ver script** para rever os detalhes exatos de como a recomendação é criada.

A base de dados permanece on-line enquanto a recomendação é aplicada - usando recomendação de desempenho ou afinação automática nunca desliga uma base de dados offline.

### <a name="apply-an-individual-recommendation"></a>Aplicar uma recomendação individual

Pode rever e aceitar recomendações uma de cada vez.

1. Na página **recomendações,** selecione uma recomendação.
2. Na página **Detalhes,** clique no botão **Aplicar.**

   ![Aplicar recomendação](./media/database-advisor-find-recommendations-portal/apply.png)

Recomendações selecionadas são aplicadas na base de dados.

### <a name="removing-recommendations-from-the-list"></a>Remoção de recomendações da lista

Se a sua lista de recomendações contiver itens que pretende remover da lista, pode descartar a recomendação:

1. Selecione uma recomendação na lista de **Recomendações** para abrir os detalhes.
2. Clique em **Descartar** na página **Detalhes.**

Se desejar, pode adicionar itens descartados de volta à lista de **Recomendações:**

1. Na página **recomendações,** clique em **Ver descartado**.
2. Selecione um item descartado da lista para ver os seus detalhes.
3. Opcionalmente, clique em **Desfazer Descartar** para adicionar o índice de volta à lista principal de **Recomendações**.

> [!NOTE]
> Por favor, note que se a [afinação automática](automatic-tuning-overview.md) da Base de Dados SQL estiver ativada e se tiver descartado manualmente uma recomendação da lista, essa recomendação nunca será aplicada automaticamente. Descartar uma recomendação é uma forma útil de os utilizadores terem a finação automática ativada nos casos em que exigem que não seja aplicada uma recomendação específica.
> Pode reverter este comportamento adicionando recomendações descartadas de volta à lista de Recomendações, selecionando a opção Desfazer Devoluções.

### <a name="enable-automatic-tuning"></a>Ativar o ajuste automático

Pode definir a sua base de dados para implementar recomendações automaticamente. À medida que as recomendações ficam disponíveis, são automaticamente aplicadas. Tal como acontece com todas as recomendações geridas pelo serviço, se o impacto de desempenho for negativo, a recomendação é revertida.

1. Na página **recomendações,** clique em **Automatizar:**

   ![Definições de aconselhamento](./media/database-advisor-find-recommendations-portal/settings.png)
2. Selecione ações para automatizar:

   ![Índices Recomendados](./media/database-advisor-find-recommendations-portal/server.png)

> [!NOTE]
> Por favor, note que **DROP_INDEX** opção não é compatível com aplicações que usam comutação de divisórias e dicas de índice.

Depois de ter selecionado a configuração desejada, clique em Aplicar.

### <a name="manually-apply-recommendations-through-t-sql"></a>Aplicar manualmente recomendações através do T-SQL

Selecione qualquer recomendação e, em seguida, clique em **Ver script**. Envie este guião contra a sua base de dados para aplicar manualmente a recomendação.

*Os índices que são executados manualmente não são monitorizados e validados para o impacto* de desempenho pelo serviço, pelo que é sugerido que monitorize estes índices após a criação para verificar se fornecem ganhos de desempenho e ajustá-los ou apagá-los se necessário. Para mais detalhes sobre a criação de índices, consulte [CREATE INDEX (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql). Além disso, as recomendações aplicadas manualmente permanecerão ativas e apresentadas na lista de recomendações para 24-48 horas. antes que o sistema os retire automaticamente. Se quiser remover uma recomendação mais cedo, pode descartá-la manualmente.

### <a name="canceling-recommendations"></a>Cancelamento de recomendações

Recomendações que se encontram em **estado pendente,** **validação**ou estado de **sucesso** podem ser canceladas. Recomendações com estatuto de **Execução** não podem ser canceladas.

1. Selecione uma recomendação na área de **Tuning History** para abrir a página de detalhes de **recomendações.**
2. Clique **em Cancelar** para abortar o processo de aplicação da recomendação.

## <a name="monitoring-operations"></a>Operações de monitorização

Aplicar uma recomendação pode não acontecer instantaneamente. O portal fornece detalhes sobre o estado da recomendação. Seguem-se os estados possíveis em que um índice pode estar em:

| Estado | Descrição |
|:--- |:--- |
| Pendente |Aplicar o comando de recomendação foi recebido e está agendado para a execução. |
| Execução |A recomendação está a ser aplicada. |
| Validação |A recomendação foi aplicada com sucesso e o serviço está a medir os benefícios. |
| Êxito |A recomendação foi aplicada com sucesso e os benefícios foram medidos. |
| Erro |Ocorreu um erro durante o processo de aplicação da recomendação. Esta pode ser uma questão transitória, ou possivelmente uma mudança de esquema para a mesa e o script já não é válido. |
| Reverter |A recomendação foi aplicada, mas foi considerada não executante e está a ser automaticamente revertida. |
| Revertido |A recomendação foi revertida. |

Clique numa recomendação em processo da lista para ver mais informações:

![Índices Recomendados](./media/database-advisor-find-recommendations-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Reverter uma recomendação

Se usou as recomendações de desempenho para aplicar a recomendação (o que significa que não executou manualmente o script T-SQL), reverte automaticamente a alteração se considerar o impacto de desempenho negativo. Se, por alguma razão, simplesmente quiser reverter uma recomendação, pode fazer o seguinte:

1. Selecione uma recomendação aplicada com sucesso na área de história de **Afinação.**
2. Clique em **Reverter** na página de detalhes da **recomendação.**

![Índices Recomendados](./media/database-advisor-find-recommendations-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Monitorização do impacto do desempenho das recomendações do índice

Depois de implementadas com sucesso as recomendações (atualmente, operações de índice e recomendações de parametrização), pode clicar em **Consultas** De Consulta insights na página de detalhes da recomendação para abrir [o Query Performance Insights](query-performance-insight-use.md) e ver o impacto de desempenho das suas principais consultas.

![Monitorizar o impacto do desempenho](./media/database-advisor-find-recommendations-portal/query-insights.png)

## <a name="summary"></a>Resumo

A Base de Dados Azure SQL fornece recomendações para melhorar o desempenho da base de dados. Ao fornecer scripts T-SQL, obtém assistência na otimização da sua base de dados e, em última análise, melhorando o desempenho da consulta.

## <a name="next-steps"></a>Próximos passos

Monitorize as suas recomendações e continue a aplicá-las para aperfeiçoar o desempenho. As cargas de trabalho na base de dados são dinâmicas e mudam continuamente. A Base de Dados Azure SQL continua a monitorizar e a fornecer recomendações que podem potencialmente melhorar o desempenho da sua base de dados.

* Consulte a [sintonização automática](automatic-tuning-overview.md) para saber mais sobre a sintonização automática na Base de Dados Azure SQL.
* Consulte [as recomendações](database-advisor-implement-performance-recommendations.md) de Desempenho para uma visão geral das recomendações de desempenho da Base de Dados Azure SQL.
* Consulte [a Query Performance Insights](query-performance-insight-use.md) para aprender sobre o impacto de desempenho das suas principais consultas.

## <a name="additional-resources"></a>Recursos adicionais

* [Arquivo de Consultas](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [Controlo de acesso baseado em funções](../../role-based-access-control/overview.md)
