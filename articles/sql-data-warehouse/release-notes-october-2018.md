---
title: Notas de versão de armazém de dados SQL do Azure, Outubro de 2018 | Documentos da Microsoft
description: Notas de versão do Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 12/04/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 6a27f23b5a0eba04ea3fa73a2f59364f9101c069
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57852171"
---
# <a name="whats-new-in-azure-sql-data-warehouse-october-2018"></a>O que há de novo no Azure SQL Data Warehouse? Outubro de 2018
O Azure SQL Data Warehouse recebe melhorias continuamente. Este artigo descreve os novos recursos e alterações que foram introduzidas em Outubro de 2018.

## <a name="devops-for-data-warehousing"></a>DevOps para armazenamento de dados
O recurso altamente solicitado para o SQL Data Warehouse (SQL DW) já está em pré-visualização com o suporte para o SQL Server Data ferramenta (SSDT) no Visual Studio! Equipes de desenvolvedores podem agora colaborar ao longo de uma base de código único, controlados por versão e implementar rapidamente as alterações para qualquer instância do mundo. Interessado em associação? Esta funcionalidade está disponível para pré-visualização hoje mesmo! Pode se registrar visitando o [SQL dados armazém Visual Studio SQL Server Data Tools (SSDT) - o formulário de inscrição de pré-visualização](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4-brmKy3TZOjoktwuHd7S1UODkwQ1lVMEw1NDBGRjNLRDNWOFlQRUpIRi4u). Devido uma procura elevada, estamos gerenciando aceitação no pré-visualização para garantir a melhor experiência para os nossos clientes. Assim que estiver inscrito, o nosso objetivo é confirmar o estatuto de dentro de sete dias de negócios.

## <a name="row-level-security-generally-available"></a>Segurança ao nível da linha em disponibilidade geral
O Azure SQL Data Warehouse (SQL DW) suporta agora a segurança de nível de linha (RLS) adicionando uma funcionalidade poderosa para proteger os dados confidenciais. Com a introdução da RLS, é possível implementar políticas de segurança para controlar o acesso às linhas das tabelas, como em quem podem acessar quais linhas. RLS permite este controlo de acesso detalhado sem ter de redesenhar o seu armazém de dados. RLS simplifica o modelo de segurança geral, como a lógica de restrição de acesso está localizada na camada de base de dados em si, em vez de longe os dados em outro aplicativo. RLS também elimina a necessidade de apresentar vistas para filtrar as linhas para gestão de controlo de acesso. Não existe nenhum custo adicional para esta funcionalidade de segurança de nível empresarial para todos os nossos clientes.

## <a name="advanced-advisors"></a>Consultores avançadas
Avançadas otimização para o Azure SQL Data Warehouse (SQL DW) apenas ficou mais simples com recomendações de armazém de dados adicionais e métricas. Existem recomendações de desempenho avançadas adicionais através do Assistente do Azure à sua disposição, incluindo:
1.  Cache adaptável – ser avisado quando a escala para otimizar a utilização da cache.
2.  Distribuição da tabela – determinar quando replicar tabelas para reduzir o movimento de dados e aumentar o desempenho da carga de trabalho. 
3.  Tempdb – compreender quando dimensionar e configurar recursos classes para reduzir a contenção do tempdb.

Há uma integração mais profunda das métricas de armazém de dados com [do Azure Monitor](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/) incluindo um gráfico monitorização personalizável aprimorado para métricas em tempo real quase no painel de descrição geral. Já não precisa de deixar que o painel de descrição geral do armazém de dados aceda às métricas do Azure Monitor quando monitorizar a utilização ou validar e aplicar as recomendações do armazém de dados. Além disso, existem novas métricas disponíveis, como tempdb e a utilização de cache adaptável para complementar as suas recomendações de desempenho.

## <a name="advanced-tuning-with-integrated-advisors"></a>Advanced ajuste com assistentes integrados
Avançadas otimização para o Azure SQL Data Warehouse (SQL DW) apenas ficou mais simples com recomendações de armazém de dados adicionais e métricas e um novo projeto do painel de descrição geral do portal que fornece uma experiência integrada com o Assistente do Azure e o Azure Monitor.

## <a name="accelerated-database-recovery-adr"></a>Recuperação de base de dados acelerada (ADR)
Do Azure SQL Data Warehouse acelerada da base de dados recuperação (ADR) está agora em pré-visualização pública. Regras de implementação automática são um novo motor SQL Server que melhora significativamente a disponibilidade de base de dados, especialmente na presença de transações de longa execução, por redesenhar completamente o processo de recuperação atual desde o backup. Principais vantagens da ADR são recuperação rápida e consistente da base de dados e reversão de transação de instantâneo.

## <a name="azure-monitor-diagnostics-logs"></a>Registos de diagnóstico do Azure Monitor
O SQL Data Warehouse (SQL DW) agora permite que informações melhoradas para cargas de trabalho analíticas integrando diretamente com os registos de diagnóstico do Azure Monitor. Esta nova capacidade permite aos programadores analisar o comportamento de carga de trabalho durante um período de tempo prolongado e tomar decisões informadas sobre o gerenciamento de otimização ou capacidade de consulta. Agora introduzimos um processo de Registro em log externos por meio [registos de diagnóstico do Azure Monitor](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json#logs) que fornecem informações adicionais sobre a sua carga de trabalho do armazém de dados. Com um único clique de um botão, agora é possível configurar os registos de diagnóstico para através de capacidades de resolução de problemas de desempenho de consulta histórica [do Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-queries). Registos de diagnóstico de Monitor do Azure suportam períodos de retenção personalizável ao guardar os registos de uma conta de armazenamento para fins, de auditoria a capacidade de transmitir os registos para os hubs de eventos perto de informações de telemetria em tempo real e a capacidade de analisar registos com o Log Analytics com as consultas de registo. Os registos de diagnósticos consistem em visualizações de telemetria do seu armazém de dados equivalentes às DMVs de resolução de problemas de desempenho utilizadas com mais frequência para o SQL Data Warehouse. Nesta versão inicial, ativámos vistas para as seguintes vistas de gestão dinâmica do sistema:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql)

## <a name="columnstore-memory-management"></a>Gerenciamento de memória de Columnstore
À medida que aumenta o número de grupos de linhas do arquivo de coluna comprimido, aumenta a memória necessária para gerir os metadados de segmento de coluna interna para esses grupos de linhas.  Como resultado, podem degradar o desempenho das consultas e as consultas executadas em relação a algumas as vistas de gestão dinâmica Columnstore (DMVs).  Aprimoramentos feitos nesta versão para otimizar o tamanho dos metadados interno para estes casos, que leva a experiência melhorada e desempenho para estas consultas. 

## <a name="azure-data-lake-storage-gen2-integration-ga"></a>Integração de geração 2 de armazenamento do Data Lake (GA) do Azure
O Azure SQL Data Warehouse (SQL DW) agora tem uma integração nativa com geração 2 de armazenamento do Azure Data Lake. Os clientes agora podem carregar dados com tabelas externas de ABFS para o SQL DW. Esta funcionalidade permite aos clientes integrar com seus lakes de dados na geração 2 de armazenamento do Data Lake. 

## <a name="bug-fixes"></a>Correções de erros

| Título | Descrição |
|:---|:---|
| **CETAS a falhas de Parquet em classes de recursos pequeno em armazéns de dados de DW2000 e muito mais** | Esta correção identifica corretamente uma referência nula na criar externo tabela como caminho de código Parquet. |
|**Valor da coluna de identidade poderá perder algumas operação CTAS** | Não pode ser mantido o valor de uma coluna de identidade quando CTASed para outra tabela. Comunicados num blog: [ https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/ ](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/). |
| **Falha interna em alguns casos, quando uma sessão é terminada enquanto uma consulta ainda está em execução** | Esta correção aciona uma InvalidOperationException se uma sessão é terminada quando a consulta ainda está em execução. |
| **(Implementado em Novembro de 2018) Os clientes estavam tendo um desempenho inferior ao ideal ao tentar carregar vários arquivos pequenos do ADLS (geração 1) com o Polybase.** | Desempenho do sistema foi um afunilamento durante a validação de token de segurança do AAD. Problemas de desempenho foram atenuados ao ativar a colocação em cache de tokens de segurança. |


## <a name="next-steps"></a>Passos Seguintes
Agora que já sabe um pouco sobre o SQL Data Warehouse, saiba como rapidamente [criar um SQL Data Warehouse][create a SQL Data Warehouse]. Se não estiver familiarizado com o Azure, poderá achar útil o [Glossário do Azure][Azure glossary] quando se deparar com terminologia nova. Em alternativa, veja alguns destes outros Recursos do SQL Data Warehouse.  

* [Histórias de sucesso de clientes]
* [Blogues]
* [Pedidos de funcionalidades]
* [Vídeos]
* [Blogues da Equipa Customer Advisory]
* [Fórum do Stack Overflow]
* [Twitter]


[Blogues]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogues da Equipa Customer Advisory]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Histórias de sucesso de clientes]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Pedidos de funcionalidades]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum do Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
