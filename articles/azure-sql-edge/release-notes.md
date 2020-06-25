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
ms.date: 06/05/2020
ms.openlocfilehash: d5d60db2cc0c35b908454be3f00716db6d2da945
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/25/2020
ms.locfileid: "85361647"
---
# <a name="azure-sql-edge-release-notes"></a>Notas de lançamento do Azure SQL Edge 

Este artigo descreve o que é novo e o que mudou a cada nova construção de Azure SQL Edge.

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
