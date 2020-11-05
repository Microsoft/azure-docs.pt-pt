---
title: Notas de lançamento para Azure SQL Edge
description: Notas de lançamento detalhando o que é novo ou o que mudou nas imagens Azure SQL Edge
keywords: notas de lançamento SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 543a71bc2a77f0a31f8ddaa0de13135b93db48ec
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395041"
---
# <a name="azure-sql-edge-release-notes"></a>Notas de lançamento do Azure SQL Edge 

Este artigo descreve o que é novo e o que mudou a cada nova construção de Azure SQL Edge.

## <a name="azure-sql-edge---100-rtm"></a>Azure SQL Edge - 1.0.0 (RTM)

### <a name="sql-engine-build-number---15020001552"></a>NÚMERO DE CONSTRUÇÃO DO MOTOR SQL - 15.0.2000.1552

### <a name="whats-new"></a>Novidades
1. Ubuntu 18.04 imagens de contentores à base. 
2. Suporte `IGNORE NULL` e `RESPECT NULL` sintaxe com `LAST_VALUE()` e `FIRST_VALUE()` funções. 
3. Melhorias de fiabilidade para PREVISÃO com ONNX.
4. Assistência à limpeza baseada na política de retenção de dados.
   - Suporte de tampão para tarefa de limpeza de retenção para resolução de problemas.
5. Suporte de novas funcionalidades 
   - Recuperação Rápida
   - Afinação automática de consultas
   - Ativar cenários de execução paralela
6. Melhorias na poupança de energia para o modo de baixa potência
7. Streaming de novos suportes de funcionalidades 
   - [Snapshot Windows](/stream-analytics-query/snapshot-window-azure-stream-analytics) : novo tipo de janela que permite agrupar-se por eventos que chegam exatamente ao mesmo tempo. 
   - Ativar [o TopOne](/stream-analytics-query/topone-azure-stream-analytics) e [o CollectTop](/stream-analytics-query/collecttop-azure-stream-analytics) como função analítica, isto permitirá devolver registos encomendados pela coluna à sua escolha, sem o necessário para fazer parte de uma janela. 
   - Melhorias na [MATCH_RECOGNIZE.](/stream-analytics-query/match-recognize-stream-analytics) 

### <a name="fixes"></a>Correções
1. Mensagens de erro adicionais e detalhes para resolução de problemas das operações de streaming da TSQL. 
2. Melhorias para preservar a vida útil da bateria no modo Idle. 
3. Correções do motor de streaming TSQL: 
   - Limpeza para trabalho de streaming parado 
   - Correções para localização e melhorias no manuseamento de unicódigo
   - Melhorar a depurabilidade para o streaming de TSQL edge, permitir que os utilizadores questionem erros de falha de trabalho a partir de get_streaming_job.
4. Limpeza baseada em política de retenção de dados
   - Correções para cenários de criação de políticas de retenção e limpeza.
5. Correções para tarefas de temporizador de fundo para melhorar a poupança de energia para o modo de baixa potência.

### <a name="known-issues"></a>Problemas Conhecidos 
1. Date_Bucket Função T-SQL não pode ser utilizada numa coluna computada.


## <a name="ctp-23"></a>CTP 2.3
### <a name="sql-engine-build-number---15020001549"></a>NÚMERO DE CONSTRUÇÃO DO MOTOR SQL - 15.0.2000.1549
### <a name="whats-new"></a>Novidades
1. Suporte para origens personalizadas na função Date_Bucket(). 
2. Suporte para ficheiros BacPac como parte da implementação do SQL.
3. Assistência à limpeza baseada na política de retenção de dados.      
   - Apoio DDL para permitir a política de retenção 
   - Procedimentos de limpeza armazenados e tarefa de limpeza de antecedentes
   - Eventos alargados para monitorizar tarefas de limpeza

### <a name="fixes"></a>Correções
1. Mensagens de erro adicionais e detalhes para resolução de problemas das operações de streaming da TSQL. 
2. Melhorias para preservar a vida útil da bateria no modo Idle. 
3. Correções do motor de streaming TSQL: 
   - Corrigir problema de marca de água preso com janela de salto substreamed 
   - Corrigir o tratamento da excepção-quadro para garantir que é recolhido como erro de funcionamento do utilizador


## <a name="ctp-22"></a>CTP 2.2
### <a name="sql-engine-build-number---15020001546"></a>NÚMERO DE CONSTRUÇÃO DO MOTOR SQL - 15.0.2000.1546
### <a name="whats-new"></a>Novidades
1. Apoio a contentores não-enraizados 
2. Suporte à recolha de dados de utilização e diagnóstico 
3. Atualizações de streaming T-SQL
   - Suporte para caracteres Unicode para nomes de objetos de fluxo

### <a name="fixes"></a>Correções
1. Atualizações de streaming T-SQL
   - Melhorias na limpeza do processo
   - Melhorias no registo e no diagnóstico
2. Melhoria do desempenho para a ingestão de dados

## <a name="ctp-21"></a>CTP 2.1 
### <a name="sql-engine-build-number---15020001545"></a>NÚMERO DE CONSTRUÇÃO DO MOTOR SQL - 15.0.2000.1545
### <a name="fixes"></a>Correções
1. Corrija o PREVISÃO com os modelos ONNX para lidar com o problema do CPUID na ARM 
2. Correção para melhorar o manuseamento do caminho de falha no arranque do streaming TSQL 
3. Corrija o valor incorreto do atraso da marca de água nas métricas de trabalho quando não há dados. 
4. Corrija o problema com o adaptador de saída quando o adaptador tem esquemas variáveis entre lotes.  

## <a name="ctp-20"></a>CTP 2.0 
### <a name="sql-engine-build-number---15020001401"></a>NÚMERO DE CONSTRUÇÃO DO MOTOR SQL - 15.0.2000.1401
### <a name="whats-new"></a>Novidades
1.  Nome do produto atualizado para 'Azure SQL Edge'
1.  função Date_bucket

    i.  Suporte para data, hora, tipo data
3.  PREVER com ONNX
    
    i.  Parâmetro RUNTIME necessário para ONNX 
    
4.  Suporte de streaming TSQL (pré-visualização limitada) 
 
### <a name="known-issues"></a>Problemas Conhecidos

1. <b>Emissão:</b> Potenciais falhas na aplicação do dacpac no arranque devido a problemas de tempo.

    <b>Solução alternativa:</b> Reiniciar o SQL Server ou o contentor vai voltar a tentar aplicar o dacpac e deve corrigir o problema
### <a name="request-support"></a>Apoio ao Pedido
1. Pode solicitar apoio na [página de suporte.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

4. Certifique-se de que os seguintes campos são selecionados: 
    * Tipo de emissão - Técnico 
    * Serviço - IoT Edge
    * Tipo de Problema - O meu problema diz respeito a um módulo IoT Edge
    * Subtipo de problema - Azure SQL Edge

   ![Bilhete de apoio à amostra](media/get-support/support-ticket.png)

## <a name="ctp-15"></a>CTP 1.5
### <a name="sql-engine-build-number---15020001331"></a>NÚMERO DE CONSTRUÇÃO DO MOTOR SQL - 15.0.2000.1331
### <a name="whats-new"></a>Novidades
1. função Date_bucket
    
    i. Suporte para o tipo DateTimeOffset
2. PREVER com os modelos ONNX

    i. suporte nvarchar
 
## <a name="ctp-14"></a>CTP 1.4
### <a name="sql-engine-build-number---15020001247"></a>NÚMERO DE CONSTRUÇÃO DO MOTOR SQL - 15.0.2000.1247
### <a name="whats-new"></a>Novidades
1.  PREVER com os modelos ONNX
 
    i.  Apoio varchar
    
    ii. Migração para onNX versão runtime 1.0 
2.  Suporte de funcionalidades - As seguintes funcionalidades estão ativadas:

    i.   Apoio cdc

    ii.  Tabela de história com compressão

    iii. Fator de escala mais elevado para leitura de log

    iv.  Pushdown do filtro ES do modo de lote

    v.   Ler otimizações antecipadas
 
## <a name="ctp-13"></a>CTP 1.3
### <a name="sql-engine-build-number---15020001147"></a>NÚMERO DE CONSTRUÇÃO DO MOTOR SQL - 15.0.2000.1147
### <a name="whats-new"></a>Novidades
1. Implantação do portal Azure IOT 

    i.   Suporte para implantação de imagens AMD64 e ARM

    ii.  Apoio à criação de emprego em streaming

    iii. Implantação da Dacpac
2. PREVER com os modelos ONNX

    i. Suporte tipo numérico
3. Suporte de funcionalidades - As seguintes funcionalidades estão ativadas:

    i.  Agregação pushdown para a varredura da loja de colunas

    ii. Digitalizações de carrosséis
4. Trabalho de redução do consumo de pegadas e memória