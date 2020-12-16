---
title: Azure SQL Edge utiliza e diagnostica a configuração de dados
description: Aprenda a configurar dados de utilização e diagnóstico em Azure SQL Edge.
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 08/04/2020
ms.openlocfilehash: 81b2aabbae148faec06c9ab420bdfecde475b4bb
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605010"
---
# <a name="azure-sql-edge-usage-and-diagnostics-data-configuration"></a>Azure SQL Edge utiliza e diagnostica a configuração de dados

Por padrão, a Azure SQL Edge recolhe informações sobre como os seus clientes estão a usar a aplicação. Especificamente, a Azure SQL Edge recolhe informações sobre a experiência de implementação, utilização e desempenho. Esta informação ajuda a Microsoft a melhorar o produto para melhor atender às necessidades dos clientes. Por exemplo, a Microsoft recolhe informações sobre que tipos de códigos de erro os clientes encontram para que possamos corrigir bugs relacionados, melhorar a nossa documentação sobre como usar o Azure SQL Edge e determinar se as funcionalidades devem ser adicionadas ao produto para melhor servir os clientes.

Especificamente, a Microsoft não envia nenhum dos seguintes tipos de informação através deste mecanismo:

- Quaisquer valores de dentro das tabelas de utilizadores.
- Quaisquer credenciais de início de súmã ou outras informações de autenticação.
- Quaisquer dados pessoais ou de clientes.

O cenário da amostra que se segue inclui informações de utilização de recursos que ajudam a melhorar o produto.

Uma consulta de exemplo das consultas utilizadas para a recolha de dados de utilização e diagnóstico é fornecida abaixo. A consulta identifica a contagem e tipos de diferentes fontes de dados de streaming que estão a ser utilizadas em Azure SQL Edge. Estes dados ajudam a Microsoft a identificar quais as fontes de dados de streaming que estão a ser utilizadas comumente de modo a que a Microsoft possa melhorar o desempenho e a experiência do utilizador associado a estas fontes de dados. 

```sql
select 
count(*) as [count], sum(inputs) as inputs, sum(outputs) as outputs, sum(linked_to_job) 
as linked_to_job, data_source_type
from ( 
select isnull(value,'unknown') as data_source_type, inputs, outputs, linked_to_job
from 
    ( 
        select 
        convert(sysname, lower(substring(ds.location, 0, charindex('://', ds.location))), 1) as data_source_type, 
        isnull(inputs, 0) as inputs, isnull(outputs, 0) as outputs, isnull(js.stream_id/js.stream_id, 0) as linked_to_job 
        from sys.external_streams es
        join sys.external_data_sources ds 
             on es.data_source_id = ds.data_source_id
        left join 
            ( 
            select stream_id, max(cast(is_input as int)) inputs, max(cast(is_output as int)) outputs 
            from sys.external_job_streams group by stream_id 
            ) js
             on js.stream_id = es.object_id 
    ) ds
left join 
    (
        select value from string_split('edgehub,sqlserver,kafka', ',')) as known_ep on data_source_type = value 
    ) known_ds
group by data_source_type
```

## <a name="disable-usage-and-diagnostic-data-collection"></a>Desativar a recolha de dados de utilização e diagnóstico

A recolha de dados de utilização e diagnóstico no Azure SQL Edge pode ser desativada utilizando qualquer um dos métodos abaixo.

> [!NOTE]
> Os dados de utilização e diagnóstico não podem ser desativados para a versão Do Desenvolvedor.

### <a name="disable-usage-and-diagnostics-using-environment-variables"></a>Desativar o uso e os diagnósticos utilizando variáveis ambientais

Para desativar a recolha de dados de Utilização e Diagnóstico no Azure SQL Edge, adicione a seguinte variável ambiental e desaprova o seu valor para `*False*` . Para obter mais informações sobre a configuração da borda Azure SQL utilizando variáveis ambientais, consulte [a Configuração utilizando Variáveis ambientais](configure.md#configure-by-using-environment-variables).

`MSSQL_TELEMETRY_ENABLED = TRUE | FALSE`

- TRUE - Permite a recolha de dados de utilização e diagnóstico. Esta é a configuração predefinida.
- FALSE - Desativa a recolha de dados de utilização e diagnóstico.

### <a name="disable-usage-and-diagnostics-using-mssqlconf-file"></a>Desativar o uso e diagnósticos usando o ficheiro mssql.conf

Para desativar a recolha de dados de Utilização e Diagnóstico no Azure SQL Edge, adicione as seguintes linhas no ficheiro mssql.conf na unidade de armazenamento persistente que é mapeada para a pasta /var/opt/mssql/ no módulo SQL Edge. Para obter mais informações sobre a configuração do Azure SQL Edge utilizando o ficheiro mssql.conf, consulte [o ficheiro Configure utilizando o ficheiro mssql.conf](configure.md#configure-by-using-an-mssqlconf-file).

```ini
[telemetry]
customerfeedback = false
```

## <a name="local-audit-of-usage-and-diagnostic-data-collection"></a>Auditoria local da recolha de dados de utilização e diagnóstico

O componente de Auditoria Local da recolha de dados de utilização e diagnóstico do Azure SQL Edge pode escrever dados recolhidos pelo serviço numa pasta designada, representando os dados (registos) que serão enviados para a Microsoft. O objetivo da Auditoria Local é permitir que os clientes vejam todos os dados que a Microsoft recolhe com esta funcionalidade, por razões de conformidade, regulação ou validação de privacidade.

### <a name="enable-local-audit-of-usage-and-diagnostics-data"></a>Permitir a auditoria local dos dados de utilização e diagnóstico

Para ativar os dados de utilização e diagnóstico de auditoria local no Azure SQL Edge

1. Crie um directório-alvo para o novo armazenamento de registos de auditoria local. Este directório-alvo pode estar no hospedeiro ou dentro do contentor. No exemplo abaixo, o directório-alvo é criado no mesmo volume de montagem que é mapeado para /var/opt/mssql/ caminho em SQL Edge.

   ```bash
   sudo mkdir <host mount path>/audit
   ```

2. Configure a auditoria de dados de utilização e diagnóstico utilizando variáveis ambientais ou ficheiro mssql.conf.

   - Utilização de variáveis ambientais - Adicione a seguinte variável de ambiente à sua implementação SQL Edge e especifique o directório-alvo para os ficheiros de auditoria.
   
     `*MSSQL_TELEMETRY_DIR = <host mount path>/audit*`
   
   - Utilização do ficheiro mssql.conf - Adicione as seguintes linhas no ficheiro mssql.conf e especifique o directório-alvo para os ficheiros de auditoria.
       ```ini
       [telemetry]
       userrequestedlocalauditdirectory  = <host mount path>/audit
       ```  

## <a name="next-steps"></a>Passos seguintes

- [Ligue-se à Borda Azure SQL](connect.md)
- [Construa uma solução IoT de ponta a ponta com o SQL no Edge](tutorial-deploy-azure-resources.md)
