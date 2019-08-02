---
title: Aplicar recomendações de desempenho – banco de dados SQL do Azure | Microsoft Docs
description: Use o portal do Azure para encontrar recomendações de desempenho que podem otimizar o desempenho do seu banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 12/19/2018
ms.openlocfilehash: b6bcb2650cd11ed97b6e81fda6aaafe7fca61202
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569601"
---
# <a name="find-and-apply-performance-recommendations"></a>Localizar e aplicar recomendações de desempenho

Você pode usar o portal do Azure para encontrar recomendações de desempenho que podem otimizar o desempenho do seu banco de dados SQL do Azure ou corrigir algum problema identificado em sua carga de trabalho. A página **recomendação de desempenho** no portal do Azure permite que você encontre as principais recomendações com base em seu impacto potencial. 

## <a name="viewing-recommendations"></a>Exibindo recomendações

Para exibir e aplicar recomendações de desempenho, você precisa das permissões corretas de [controle de acesso baseado em função](../role-based-access-control/overview.md) no Azure. **Leitor**, as permissões de **colaborador do BD SQL** são necessárias para exibir as recomendações e o **proprietário**, as permissões de **colaborador do BD SQL** são necessárias para executar qualquer ação; criar ou descartar índices e cancelar a criação do índice.

Use as etapas a seguir para encontrar recomendações de desempenho no portal do Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Vá para **todos os serviços** > bancos de dados**SQL**e selecione seu banco de dados.
3. Navegue até **recomendação de desempenho** para exibir as recomendações disponíveis para o banco de dados selecionado.

As recomendações de desempenho são mostradas na tabela semelhante à mostrada na figura a seguir:

![Recomendações](./media/sql-database-advisor-portal/recommendations.png)

As recomendações são classificadas por seu impacto potencial no desempenho nas seguintes categorias:

| Impacto | Descrição |
|:--- |:--- |
| Alta |Recomendações de alto impacto devem fornecer o impacto de desempenho mais significativo. |
| Médio |As recomendações de impacto médio devem melhorar o desempenho, mas não substancialmente. |
| Baixa |As recomendações de baixo impacto devem fornecer um desempenho melhor do que sem, mas as melhorias podem não ser significativas. |


> [!NOTE]
> O banco de dados SQL do Azure precisa monitorar atividades pelo menos por um dia para identificar algumas recomendações. O banco de dados SQL do Azure pode otimizar mais facilmente para padrões de consulta consistentes do que para picos aleatórios de atividade. Se as recomendações não estiverem disponíveis no momento, a página **recomendação de desempenho** fornecerá uma mensagem explicando o porquê.
> 

Você também pode exibir o status das operações históricas. Selecione uma recomendação ou status para ver mais informações.

Aqui está um exemplo de recomendação de "criar índice" no portal do Azure.

![Criar índice](./media/sql-database-advisor-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Aplicando recomendações
O banco de dados SQL do Azure oferece controle total sobre como as recomendações são habilitadas usando qualquer uma das três opções a seguir: 

* Aplique recomendações individuais uma de cada vez.
* Habilite o ajuste automático para aplicar recomendações automaticamente.
* Para implementar uma recomendação manualmente, execute o script T-SQL recomendado em seu banco de dados.

Selecione qualquer recomendação para exibir seus detalhes e clique em **Exibir script** para examinar os detalhes exatos de como a recomendação é criada.

O banco de dados permanece online enquanto a recomendação é aplicada – usar recomendação de desempenho ou ajuste automático nunca coloca um banco de dados offline.

### <a name="apply-an-individual-recommendation"></a>Aplicar uma recomendação individual
Você pode examinar e aceitar recomendações uma de cada vez.

1. Na página **recomendações** , selecione uma recomendação.
2. Na página **detalhes** , clique no botão **aplicar** .
   
    ![Aplicar recomendação](./media/sql-database-advisor-portal/apply.png)

A recomendação selecionada é aplicada no banco de dados.

### <a name="removing-recommendations-from-the-list"></a>Removendo as recomendações da lista

Se sua lista de recomendações contiver itens que você deseja remover da lista, você poderá descartar a recomendação:

1. Selecione uma recomendação na lista de **recomendações** para abrir os detalhes.
2. Clique em descartar na página de **detalhes** .

Se desejar, você pode adicionar itens descartados de volta à lista de **recomendações** :

1. Na página **recomendações** , clique em **Exibir Descartado**.
2. Selecione um item descartado na lista para exibir seus detalhes.
3. Opcionalmente, clique em **desfazer** descartar para adicionar o índice de volta à lista principal de **recomendações**.

> [!NOTE]
> Observe que, se o [ajuste automático](sql-database-automatic-tuning.md) do banco de dados SQL estiver habilitado e você tiver Descartado manualmente uma recomendação da lista, essa recomendação nunca será aplicada automaticamente. Descartar uma recomendação é uma maneira prática para os usuários terem o ajuste automático habilitado nos casos em que exigirem que uma recomendação específica não deva ser aplicada.
> Você pode reverter esse comportamento adicionando recomendações descartadas de volta à lista de recomendações selecionando a opção desfazer descartar.
> 

### <a name="enable-automatic-tuning"></a>Ativar o ajuste automático
Você pode definir o banco de dados SQL do Azure para implementar recomendações automaticamente. Conforme as recomendações ficam disponíveis, elas são aplicadas automaticamente. Assim como com todas as recomendações gerenciadas pelo serviço, se o impacto no desempenho for negativo, a recomendação será revertida.

1. Na página **recomendações** , clique em **automatizar**:
   
    ![Configurações do Advisor](./media/sql-database-advisor-portal/settings.png)
2. Selecione ações para automatizar:
   
    ![Índices recomendados](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Observe que a opção **DROP_INDEX** não é compatível atualmente com aplicativos que usam a alternância de partição e dicas de índice. 
>

Depois de selecionar a configuração desejada, clique em aplicar.

### <a name="manually-apply-recommendations-through-t-sql"></a>Aplicar recomendações manualmente por meio do T-SQL

Selecione qualquer recomendação e clique em **Exibir script**. Execute este script em seu banco de dados para aplicar manualmente a recomendação.

*Os índices executados manualmente não são monitorados e validados quanto ao impacto no desempenho pelo serviço* , portanto, é recomendável que você monitore esses índices após a criação para verificar se eles fornecem ganhos de desempenho e os ajustam ou excluem, se necessário. Para obter detalhes sobre como criar índices, consulte [CREATE INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx). Além disso, as recomendações aplicadas manualmente permanecerão ativas e exibidas na lista de recomendações de 24-48 horas. antes que o sistema as retire automaticamente. Se você quiser remover uma recomendação mais cedo, poderá descartá-la manualmente.

### <a name="canceling-recommendations"></a>Cancelando recomendações

As recomendações que estão em um status **pendente**, de **validação**ou de **êxito** podem ser canceladas. As recomendações com um status de **execução** não podem ser canceladas.

1. Selecione uma recomendação na área **histórico de ajuste** para abrir a página **detalhes de recomendações** .
2. Clique em **Cancelar** para anular o processo de aplicação da recomendação.

## <a name="monitoring-operations"></a>Operações de monitorização

A aplicação de uma recomendação pode não acontecer instantaneamente. O portal fornece detalhes sobre o status de recomendação. Estes são os Estados possíveis nos quais um índice pode estar:

| Estado | Descrição |
|:--- |:--- |
| Pendente |O comando aplicar recomendação foi recebido e está agendado para execução. |
| A executar |A recomendação está sendo aplicada. |
| A Validar |A recomendação foi aplicada com êxito e o serviço está medindo os benefícios. |
| Êxito |A recomendação foi aplicada com êxito e os benefícios foram medidos. |
| Erro |Ocorreu um erro durante o processo de aplicação da recomendação. Isso pode ser um problema transitório ou, possivelmente, uma alteração de esquema na tabela e o script não é mais válido. |
| A reverter |A recomendação foi aplicada, mas foi considerada sem desempenho e está sendo revertida automaticamente. |
| Revertido |A recomendação foi revertida. |

Clique em uma recomendação em processo da lista para ver mais informações:

![Índices recomendados](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Revertendo uma recomendação
Se você usou as recomendações de desempenho para aplicar a recomendação (ou seja, você não executou o script T-SQL manualmente), ela reverterá automaticamente a alteração se encontrar o impacto de desempenho negativo. Se, por algum motivo, você simplesmente quiser reverter uma recomendação, poderá fazer o seguinte:

1. Selecione uma recomendação aplicada com êxito na área **histórico de ajuste** .
2. Clique em **reverter** na página **detalhes da recomendação** .

![Índices recomendados](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Monitoramento do impacto do desempenho de recomendações de índice
Depois que as recomendações são implementadas com êxito (atualmente, somente as recomendações de operações de indexação e parametrização de consultas), você pode clicar em **informações de consulta** na página detalhes de recomendação para abrir [informações de desempenho de consulta](sql-database-query-performance.md) e ver o impacto no desempenho de suas principais consultas.

![Monitorar o impacto no desempenho](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>Resumo
O banco de dados SQL do Azure fornece recomendações para melhorar o desempenho do banco de dados SQL. Ao fornecer scripts T-SQL, você obtém assistência para otimizar seu banco de dados e, por fim, melhorar o desempenho da consulta.

## <a name="next-steps"></a>Passos Seguintes
Monitore suas recomendações e continue a aplicá-las para refinar o desempenho. As cargas de trabalho de banco de dados são dinâmicas e mudam continuamente. O banco de dados SQL do Azure continua a monitorar e fornecer recomendações que podem melhorar o desempenho do seu banco de dados. 

* Consulte [ajuste automático](sql-database-automatic-tuning.md) para saber mais sobre o ajuste automático no banco de dados SQL do Azure.
* Consulte [recomendações de desempenho](sql-database-advisor.md) para obter uma visão geral das recomendações de desempenho do banco de dados SQL do Azure.
* Consulte análise de [desempenho de consultas](sql-database-query-performance.md) para saber mais sobre como exibir o impacto no desempenho de suas principais consultas.

## <a name="additional-resources"></a>Recursos adicionais
* [Query Store](https://msdn.microsoft.com/library/dn817826.aspx) (Arquivo de Consultas)
* [CRIAR ÍNDICE](https://msdn.microsoft.com/library/ms188783.aspx)
* [Controlo de acesso baseado em funções](../role-based-access-control/overview.md)

