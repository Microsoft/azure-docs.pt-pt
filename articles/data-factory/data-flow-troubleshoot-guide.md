---
title: Fluxos de dados de resolução de problemas
description: Saiba como resolver problemas de fluxo de dados na Azure Data Factory.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.openlocfilehash: e2e1ddd031041f49107545cd0b3d3de4eaebcd6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472133"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Fluxos de dados de resolução de problemas na Fábrica de Dados de Azure

Este artigo explora métodos comuns de resolução de problemas para fluxos de dados na Azure Data Factory.

## <a name="common-errors-and-messages"></a>Erros e mensagens comuns

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Código de erro: DF-Executor-SourceInvalidPayload
- **Mensagem**: A pré-visualização de dados, o depuração e a execução de fluxode dados do gasoduto falharam porque o contentor não existe
- **Causas**: Quando o conjunto de dados contém um recipiente que não existe no armazenamento
- **Recomendação**: Certifique-se de que o recipiente referenciado no seu conjunto de dados existe ou está acessível.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Código de erro: DF-Executor-SystemImplicitCartesian

- **Mensagem**: O produto cartesiano implícito para a adesão ao INNER não é suportado, utilize o CROSS JOIN em vez disso. As colunas utilizadas em adesão devem criar uma chave única para as linhas.
- **Causas**: Produto cartesiano implícito para a adesão do INNER entre planos lógicos não é suportado. Se as colunas utilizadas na união criarem a chave única, são necessárias pelo menos uma coluna de ambos os lados da relação.
- **Recomendação**: Para uniões baseadas em não igualdade, tem de optar pela CUSTOM CROSS JOIN.

### <a name="error-code-df-executor-systeminvalidjson"></a>Código de erro: DF-Executor-SystemInvalidJson

- **Mensagem**: Erro de análise JSON, codificação não suportada ou multilinha
- **Causas**: Possíveis problemas com o ficheiro JSON: codificação não suportada, bytes corruptos ou utilização da fonte JSON como único documento em muitas linhas aninhadas
- **Recomendação**: Verifique se a codificação do ficheiro JSON está suportada. Sobre a transformação da Fonte que está a utilizar um conjunto de dados JSON, expanda 'Definições JSON' e ligue o 'Documento Único'.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Código de erro: DF-Executor-BroadcastTimeout

- **Mensagem**: Transmissão de erros de tempo de encontro, certifique-se de que o fluxo de transmissão produz dados dentro de 60 segundos em corridas de depuração e 300 segundos em corridas de emprego
- **Causas**: A transmissão tem um prazo padrão de 60 segundos em corridas de depuração e 300 segundos em corridas de emprego. O fluxo escolhido para transmissão parece ser grande para produzir dados dentro deste limite.
- **Recomendação**: Evite transmitir grandes fluxos de dados onde o processamento possa demorar mais de 60 segundos. Escolha um fluxo menor para transmitir. Grandes mesas SQL/DW e ficheiros de origem são tipicamente maus candidatos.

### <a name="error-code-df-executor-conversion"></a>Código de erro: DF-Executor-Conversão

- **Mensagem**: Conversão para data ou hora falhou devido a um carácter inválido
- **Causas**: Os dados não estão no formato esperado
- **Recomendação**: Utilize o tipo de dados correto

### <a name="error-code-df-executor-invalidcolumn"></a>Código de erro: DF-Executor-InvalidColumn

- **Mensagem**: O nome da coluna deve ser especificado na consulta, definir um pseudónimo se utilizar uma função SQL
- **Causas**: Nenhum nome da coluna foi especificado
- **Recomendação**: Detete um pseudónimo se utilizar uma função SQL, como min()/max(), etc.

## <a name="general-troubleshooting-guidance"></a>Orientação geral de resolução de problemas

1. Verifique o estado das ligações do conjunto de dados. Em cada transformação de Origem e Sink, visite o Serviço Linked para cada conjunto de dados que está a utilizar e testa as ligações.
1. Verifique o estado do seu ficheiro e ligações de mesa do designer de fluxo de dados. Ligue o Debug e clique na Pré-visualização de dados nas transformações da fonte para garantir que consegue aceder aos seus dados.
1. Se tudo correr bem a partir da pré-visualização de dados, entre no pipeline designer e coloque o fluxo de dados numa atividade de pipeline. Desperte o gasoduto para um teste de ponta a ponta.

## <a name="next-steps"></a>Passos seguintes

Para obter mais ajuda para resolução de problemas, experimente estes recursos:
*  [Blog da Fábrica de Dados](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Pedidos de funcionalidade sinuosos da Fábrica de Dados](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow forum para fábrica de dados](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre data factory](https://twitter.com/hashtag/DataFactory)
*  [ADF mapeando dados fluxos Performance Guide](concepts-data-flow-performance.md)
