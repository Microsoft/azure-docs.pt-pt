---
title: Aplicar recomendações de desempenho - base de dados SQL do Azure | Documentos da Microsoft
description: Utilize o portal do Azure para encontrar as recomendações de desempenho que podem otimizar o desempenho da base de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: d80581aae56fc9d65d6f24d21f2c582cb74b3f2d
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58863207"
---
# <a name="find-and-apply-performance-recommendations"></a>Localizar e aplicar recomendações de desempenho

Pode utilizar o portal do Azure para obter recomendações de desempenho que podem otimizar o desempenho da base de dados SQL do Azure ou para corrigir algum problema identificado na carga de trabalho. **Recomendação de desempenho** página no portal do Azure permite-lhe encontrar as principais recomendações com base no seu impacto potencial. 

## <a name="viewing-recommendations"></a>Ver recomendações

Para visualizar e aplicar recomendações de desempenho, é necessário o correto [controlo de acesso baseado em funções](../role-based-access-control/overview.md) permissões no Azure. **Leitor**, **contribuinte da BD SQL** são necessárias permissões para ver as recomendações, e **proprietário**, **contribuinte da BD SQL** permissões são necessárias para executar qualquer ação; Criar ou remova índices e cancelar a criação de índices.

Utilize os seguintes passos para localizar recomendações de desempenho no portal do Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Aceda a **todos os serviços** > **bases de dados SQL**e selecione a sua base de dados.
3. Navegue para **recomendação de desempenho** para ver recomendações disponíveis para a base de dados selecionada.

Recomendações de desempenho são mostradas na tabela semelhante à mostrada na figura a seguir:

![Recomendações](./media/sql-database-advisor-portal/recommendations.png)

As recomendações são ordenadas pelo seu potencial impacto no desempenho nas seguintes categorias:

| Impacto | Descrição |
|:--- |:--- |
| Elevado |Recomendações de alto impacto devem fornecer o impacto mais significativo do desempenho. |
| Médio |Impacto intermédio recomendações devem melhorar o desempenho, mas não substancialmente. |
| Baixa |Recomendações de baixo impacto devem fornecer um desempenho melhor do que sem, mas melhorias poderão não ser significativas. |


> [!NOTE]
> Base de dados SQL do Azure tem de monitorizar as atividades, pelo menos, um dia para identificar algumas recomendações. A base de dados do SQL do Azure mais facilmente pode otimizar os padrões de consulta consistente de que o que pode para aleatórios, spotty absorver picos de atividade. Se as recomendações não estão atualmente disponíveis, o **recomendação de desempenho** página fornece uma mensagem que explica por que.
> 

Também pode ver o estado das operações históricos. Selecione uma recomendação ou estado para obter mais informações.

Eis um exemplo de recomendação "Criar o índice" no portal do Azure.

![Criar índice](./media/sql-database-advisor-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Aplicar recomendações
Base de dados SQL do Azure dá-lhe controlo total sobre como as recomendações são ativada através de qualquer uma das três opções seguintes: 

* Aplica recomendações individuais um de cada vez.
* Ative a otimização para aplicar automaticamente as recomendações de automática.
* Para implementar uma recomendação manualmente, execute o script de T-SQL recomendado na sua base de dados.

Selecione qualquer recomendação para ver os detalhes e, em seguida, clique em **Ver script** para rever os detalhes exatos de como a recomendação é criada.

A base de dados permanece online enquanto a recomendação está aplicada – com a recomendação de desempenho ou nunca otimização automática a coloca uma base de dados offline.

### <a name="apply-an-individual-recommendation"></a>Aplicar uma recomendação individual
Pode rever e aceitar recomendações um por vez.

1. Sobre o **recomendações** , selecione uma recomendação.
2. Sobre o **detalhes** página, clique em **aplicar** botão.
   
    ![Aplicar a recomendação](./media/sql-database-advisor-portal/apply.png)

A recomendação selecionada são aplicadas no banco de dados.

### <a name="removing-recommendations-from-the-list"></a>Remover as recomendações da lista

Se a sua lista de recomendações contém itens que pretende remover da lista, pode descartar a recomendação:

1. Selecione uma recomendação na lista de **recomendações** para abrir os detalhes.
2. Clique em **descartar** sobre o **detalhes** página.

Se assim o desejar, pode adicionar itens rejeitados de volta para o **recomendações** lista:

1. Sobre o **recomendações** página, clique em **ver eliminadas**.
2. Selecione um item de rejeitados na lista para ver os detalhes.
3. Opcionalmente, clique em **anular descartar** para adicionar o índice de volta para a lista de principal de **recomendações**.

> [!NOTE]
> Tenha em atenção que, se base de dados SQL [otimização automática](sql-database-automatic-tuning.md) estiver ativada, e se o ter manualmente rejeitados uma recomendação na lista, essa recomendação nunca será aplicada automaticamente. A rejeitar uma recomendação é uma forma prática para que os utilizadores tenham de otimização automática ativada em casos quando a necessidade de que não deve ser aplicada uma recomendação específica.
> Pode reverter esse comportamento ao adicionar recomendações eliminadas novamente para a lista de recomendações, selecionando a opção de descartar anular.
> 

### <a name="enable-automatic-tuning"></a>Ativar o ajuste automático
Pode definir a base de dados do SQL do Azure para implementar recomendações de automaticamente. À medida recomendações ficam disponíveis, estas são aplicadas automaticamente. Tal como acontece com todas as recomendações gerenciada pelo serviço, se o impacto de desempenho for negativo, a recomendação está revertida.

1. Sobre o **recomendações** página, clique em **automatizar**:
   
    ![Definições do Advisor](./media/sql-database-advisor-portal/settings.png)
2. Selecione ações para automatizar:
   
    ![Recomendado índices](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Tenha em atenção que **DROP_INDEX** opção não é atualmente compatível com aplicativos utilizar sugestões de índice e de alternância de partição. 
>

Assim que tiver selecionado a configuração pretendida, clique em aplicar.

### <a name="manually-apply-recommendations-through-t-sql"></a>Aplicar manualmente recomendações através de T-SQL

Selecione qualquer recomendação e, em seguida, clique em **Ver script**. Execute esse script em sua base de dados para aplicar manualmente a recomendação.

*Índices que são executados manualmente não são monitorizados e validados para impacto no desempenho pelo serviço* portanto, é recomendável que monitorar esses índices após a criação para verificar se eles fornecem ganhos de desempenho e ajustem ou eliminá-los, se necessário. Para obter detalhes sobre a criação de índices, consulte [criar índice (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx). Além disso, recomendações manualmente aplicadas irão permanecer Active Directory e apresentadas na lista de recomendações para 24-48 horas. antes do sistema retira automaticamente-los. Se quiser remover uma recomendação mais cedo, pode descartá-lo manualmente.

### <a name="canceling-recommendations"></a>A cancelar recomendações

Recomendações que estão num **pendente**, **validação**, ou **êxito** Estado pode ser cancelado. Recomendações com o estado **Executing** não é possível cancelar.

1. Selecione uma recomendação no **histórico de ajuste** área para abrir o **detalhes das recomendações** página.
2. Clique em **Cancelar** para abortar o processo de aplicar a recomendação.

## <a name="monitoring-operations"></a>Operações de monitorização

Aplicar uma recomendação pode não acontecer instantaneamente. O portal fornece detalhes sobre o estado da recomendação. Seguem-se Estados possíveis que pode ser um índice:

| Estado | Descrição |
|:--- |:--- |
| Pendente |Aplica a recomendação comando foi recebido e está agendado para execução. |
| A executar |A recomendação está a ser aplicada. |
| A Validar |A recomendação foi aplicada com êxito e o serviço é medir os benefícios. |
| Êxito |A recomendação foi aplicada com êxito e foram avaliados de benefícios. |
| Erro |Ocorreu um erro durante o processo de aplicação da recomendação. Isso pode ser um problema transitório, ou, possivelmente, um esquema altere para a tabela e o script já não é válido. |
| A reverter |A recomendação foi aplicada, mas tiver sido considerada não de elevado desempenho e está a ser revertida automaticamente. |
| Revertido |A recomendação foi revertida. |

Clique numa recomendação no processo da lista para ver mais informações:

![Recomendado índices](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Reverter uma recomendação
Se utilizou as recomendações de desempenho para aplicar a recomendação (ou seja, de que manualmente não tiver executado o script T-SQL), ele é automaticamente revertida a alteração se ele encontrar o impacto de desempenho para ser negativo. Se por qualquer motivo que simplesmente pretende reverter uma recomendação, pode fazer o seguinte:

1. Selecione uma recomendação aplicada com êxito no **histórico da otimização** área.
2. Clique em **Revert** sobre o **detalhes da recomendação** página.

![Recomendado índices](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Impacto no desempenho de recomendações de índice de monitorização
Depois de recomendações são implementadas com êxito (atualmente, operações de índice e parametrizar apenas recomendações de consultas), pode clicar em **informações de consulta** sob recomendação detalhes da página para abrir [consulta Informações de desempenho](sql-database-query-performance.md) e ver o impacto de desempenho de suas consultas principais.

![Impacto no desempenho do monitor](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>Resumo
Base de dados SQL do Azure fornece recomendações para melhorar o desempenho de base de dados SQL. Ao fornecer scripts T-SQL, obtenha assistência na otimizando a sua base de dados e, por fim, melhorando o desempenho de consulta.

## <a name="next-steps"></a>Passos Seguintes
Monitorizar as suas recomendações e continuar a aplicá-las para refinar o desempenho. Cargas de trabalho de base de dados são dinâmicas e altera-se continuamente. Base de dados SQL do Azure continua a monitorizar e facultar recomendações que podem melhorar potencialmente o desempenho da sua base de dados. 

* Ver [otimização automática](sql-database-automatic-tuning.md) para saber mais sobre a otimização automática na base de dados do Azure SQL.
* Ver [recomendações de desempenho](sql-database-advisor.md) para uma descrição geral das recomendações de desempenho da base de dados do Azure SQL.
* Ver [informações de desempenho de consulta](sql-database-query-performance.md) para saber mais sobre como ver o impacto de desempenho de suas consultas principais.

## <a name="additional-resources"></a>Recursos adicionais
* [Arquivo de Consultas](https://msdn.microsoft.com/library/dn817826.aspx)
* [CRIAR ÍNDICE](https://msdn.microsoft.com/library/ms188783.aspx)
* [Controlo de acesso baseado em funções](../role-based-access-control/overview.md)

