---
title: Notas de lançamento para Azure SQL Edge
description: Notas de lançamento que detalham as novidades ou o que mudou nas imagens Azure SQL Edge.
keywords: notas de lançamento SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 11/24/2020
ms.openlocfilehash: e078fb91b3279b6f4321cd51dfb094f82bbe5f14
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696384"
---
# <a name="azure-sql-edge-release-notes"></a>Notas de lançamento do Azure SQL Edge 

Este artigo descreve o que é novo e o que mudou com cada nova construção de Azure SQL Edge.

## <a name="azure-sql-edge-102"></a>Aresta Azure SQL 1.0.2

Motor SQL constrói 15.0.2000.1554

### <a name="fixes"></a>Correções

- Streaming T-SQL  
   - Fixar na propriedade e permissões para objetos de streaming
   - Registar melhorias com rotação de registo e pré-fixação de registos
   - Azure Stream Analytics: Registar melhorias, melhorar código de erro/mensagens de erro nos adaptadores 

- ONNX
    - Correções de bugs para cenário de consulta paralela e falhas de limpeza de modelos
    - Tempo de funcionaamento onNX atualizado para 1.5.1

## <a name="azure-sql-edge-101"></a>Aresta Azure SQL 1.0.1

Motor SQL constrói 15.0.2000.1553

### <a name="whats-new"></a>Novidades

- Permitir Date_Bucket expressões definidas em colunas computadas.

### <a name="fixes"></a>Correções

- Correção da política de retenção para deixar cair uma tabela que tem uma política de retenção ativada com um intervalo infinito
- Suporte de implementação da DacFx para funcionalidades de streaming e funcionalidades de política de retenção 
- Correção de implementação da DacFx para permitir a implantação de uma pasta aninhada num URL SAS 
- PREVISÃO corrigir para suportar nomes de colunas longas em mensagens de erro

## <a name="azure-sql-edge-100-rtm"></a>Aresta Azure SQL 1.0.0 (RTM)

Motor SQL constrói 15.0.2000.1552

### <a name="whats-new"></a>Novidades
- Imagens de contentores baseadas em Ubuntu 18.04 
- Suporte `IGNORE NULL` e `RESPECT NULL` sintaxe com `LAST_VALUE()` e `FIRST_VALUE()` funções 
- Melhorias de fiabilidade para PREVISÃO com ONNX
- Suporte à limpeza com base na política de retenção de dados:
   - Suporte de tampão de anel para uma tarefa de limpeza de retenção para resolução de problemas
- Suporte de novas funcionalidades: 
   - Recuperação rápida
   - Afinação automática de consultas
   - Cenários de execução paralela
- Melhorias na poupança de energia para o modo de baixa potência
- Suporte de novas funcionalidades: 
   - [Janelas instantâneas](/stream-analytics-query/snapshot-window-azure-stream-analytics): Um novo tipo de janela permite-lhe agrupar-se por eventos que chegam ao mesmo tempo.
   - [TopOne](/stream-analytics-query/topone-azure-stream-analytics) e [CollectTop](/stream-analytics-query/collecttop-azure-stream-analytics) podem ser ativados como funções analíticas. Pode devolver os registos encomendados pela coluna à sua escolha. Não precisam de ser parte de uma janela. 
   - Melhorias na [MATCH_RECOGNIZE.](/stream-analytics-query/match-recognize-stream-analytics) 

### <a name="fixes"></a>Correções
- Mensagens de erro adicionais e detalhes para resolução de problemas das operações de streaming T-SQL 
- Melhorias para preservar a vida útil da bateria em modo inativo 
- Correções do motor de streaming T-SQL: 
   - Limpeza para trabalhos de streaming parados 
   - Correções para localização 
   - Melhor manuseamento do Unicode 
   - Melhor depuração para o streaming SQL Edge T-SQL, permitindo aos utilizadores consultarem erros de falha de trabalho a partir de get_streaming_job
- Limpeza baseada na política de retenção de dados: 
    - Correções para cenários de criação e limpeza de políticas de retenção
- Correções para tarefas de temporizador de fundo para melhorar a poupança de energia para o modo de baixa potência

### <a name="known-issues"></a>Problemas conhecidos 
- A função T-SQL Date_Bucket não pode ser utilizada numa coluna computada.


## <a name="ctp-23"></a>CTP 2.3
Motor SQL constrói 15.0.2000.1549
### <a name="whats-new"></a>Novidades
- Suporte para origens personalizadas na função Date_Bucket() 
- Suporte para ficheiros BACPAC como parte da implantação do SQL
- Suporte à limpeza com base na política de retenção de dados:      
   - Suporte DDL para permitir a política de retenção 
   - Limpeza para procedimentos armazenados e tarefa de limpeza de fundo
   - Eventos alargados para monitorizar tarefas de limpeza

### <a name="fixes"></a>Correções
- Mensagens de erro adicionais e detalhes para resolução de problemas das operações de streaming T-SQL 
- Melhorias para preservar a vida útil da bateria em modo inativo 
- Motor de streaming T-SQL: 
   - Fixação para marca de água presa na janela de salto substreamed 
   - Correção para o manuseamento de exceções de enquadramento para garantir que é recolhido como um erro de localização do utilizador


## <a name="ctp-22"></a>CTP 2.2
Motor SQL constrói 15.0.2000.1546
### <a name="whats-new"></a>Novidades
- Suporte para recipientes não-raiz 
- Suporte para a recolha de dados de utilização e diagnóstico 
- Atualizações de streaming T-SQL:
   - Suporte para caracteres Unicode para nomes de objetos de fluxo

### <a name="fixes"></a>Correções
- Atualizações de streaming T-SQL:
   - Melhorias na limpeza do processo
   - Melhorias no registo e no diagnóstico
- Melhoria do desempenho para a ingestão de dados

## <a name="ctp-21"></a>CTP 2.1 
Motor SQL constrói 15.0.2000.1545
### <a name="fixes"></a>Correções
- Permitiu que os modelos PREDICT-with-ONNX lidassem com um problema cpUID na ARM 
- Melhor manuseamento do caminho de falha quando o streaming T-SQL começa
- Valor corrigido do atraso da marca de água nas métricas de trabalho quando não há dados.
- Correção para um problema com o adaptador de saída quando o adaptador tem um esquema variável entre lotes  

## <a name="ctp-20"></a>CTP 2.0 
Motor SQL constrói 15.0.2000.1401
### <a name="whats-new"></a>Novidades
-   Nome do produto atualizado para *Azure SQL Edge*
-  Date_Bucket função:
    - Suporte para tipos de data, hora e data
- PREVER com ONNX:
    - Requisito ONNX para o parâmetro RUNTIME  
- Suporte de streaming T-SQL (pré-visualização limitada) 
 
### <a name="known-issues"></a>Problemas conhecidos

- Problema: Potenciais falhas na aplicação do DACPAC no arranque devido a um problema de tempo.
- Solução alternativa: Reiniciar o SQL Server. Caso contrário, o recipiente voltará a tentar aplicar o DACPAC.

### <a name="request-support"></a>Solicitar apoio
Pode solicitar apoio na [página de suporte.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Selecione os seguintes campos: 
- **Tipo de problema**: *técnico* 
- **Serviço**: *IoT Edge*
- **Tipo de problema**: *O meu problema diz respeito a um módulo IoT Edge*
- **Subtipo de problema**: *Azure SQL Edge*

:::image type="content" source="media/get-support/support-ticket.png" alt-text="Screenshot mostrando um bilhete de suporte de amostra.":::

## <a name="ctp-15"></a>CTP 1.5
Motor SQL constrói 15.0.2000.1331
### <a name="whats-new"></a>Novidades
- Date_Bucket função:
    - Suporte para o tipo DateTimeOffset
- PREVER com os modelos ONNX:
  - Suporte NVARCHAR
 
## <a name="ctp-14"></a>CTP 1.4
Motor SQL constrói 15.0.2000.1247
### <a name="whats-new"></a>Novidades
-   PREVER com os modelos ONNX:
    - Suporte VARCHAR
    - Migração para onNX versão runtime 1.0 

- As seguintes funcionalidades estão ativadas:
  - Apoio cdc
  - Tabela de história com compressão
  - Um fator de maior escala para a leitura do registo
  - Pushdown do filtro ES do modo de lote
  - Otimizações de leitura antecipada
 
## <a name="ctp-13"></a>CTP 1.3
Motor SQL constrói 15.0.2000.1147
### <a name="whats-new"></a>Novidades
- Implantação do portal Azure IoT: 
    - Suporte para implantação de imagens AMD64 e ARM
    - Apoio à criação de emprego em streaming
    - Implantação da DACPAC
- PREVER com os modelos ONNX:
    - Suporte tipo numérico
- As seguintes funcionalidades estão ativadas:
    - Agregação pushdown para a varredura da loja de colunas
    - Digitalizações de carrosséis
- Trabalho de redução da pegada e do consumo de memória
