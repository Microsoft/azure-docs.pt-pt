---
title: Fluxos de dados de resolução de problemas
description: Saiba como resolver problemas de fluxo de dados na Azure Data Factory.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.openlocfilehash: 2edd5b661240b6156cf8a02059b2b9a668c402f3
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83829125"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Fluxos de dados de resolução de problemas na Fábrica de Dados de Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

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
- **Recomendação**: Verifique o separador Otimize sobre as transformações de fluxo de dados para Aderir, Existe e Procurar. A opção predefinida para Transmissão é "Auto". Se isto estiver definido, ou se estiver a definir manualmente o lado esquerdo ou direito para transmitir em "Fixo", então pode definir uma configuração de tempo de funcionamento de integração Azure maior ou desligar a transmissão. A abordagem recomendada para um melhor desempenho nos fluxos de dados é permitir que a Spark transmita usando "Auto" e utilize um Azure IR otimizado de memória.

### <a name="error-code-df-executor-conversion"></a>Código de erro: DF-Executor-Conversão

- **Mensagem**: Conversão para data ou hora falhou devido a um carácter inválido
- **Causas**: Os dados não estão no formato esperado
- **Recomendação**: Utilize o tipo de dados correto

### <a name="error-code-df-executor-invalidcolumn"></a>Código de erro: DF-Executor-InvalidColumn

- **Mensagem**: O nome da coluna deve ser especificado na consulta, definir um pseudónimo se utilizar uma função SQL
- **Causas**: Nenhum nome da coluna foi especificado
- **Recomendação**: Detete um pseudónimo se utilizar uma função SQL, como min()/max(), etc.

### <a name="error-code-getcommand-outputasync-failed"></a>Código de erro: GetCommand OutputAsync falhou

- **Mensagem**: Durante o depuramento do Fluxo de Dados e pré-visualização de dados: GetCommand OutputAsync falhou com ...
- **Causas**: Trata-se de um erro de serviço de back-end. Pode voltar a tentar a operação e reiniciar também a sua sessão de depuração.
- **Recomendação**: Se voltar a tentar e reiniciar não resolver o problema, contacte o apoio ao cliente.

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>Código de erro: Acerte a exceção inesperada e a execução falhou

- **Mensagem**: Durante a execução da atividade do Fluxo de Dados: Atingir uma exceção inesperada e a execução falhou.
- **Causas**: Trata-se de um erro de serviço de back-end. Pode voltar a tentar a operação e reiniciar também a sua sessão de depuração.
- **Recomendação**: Se voltar a tentar e reiniciar não resolver o problema, contacte o apoio ao cliente.

## <a name="general-troubleshooting-guidance"></a>Orientação geral de resolução de problemas

1. Verifique o estado das ligações do conjunto de dados. Em cada transformação de Origem e Sink, visite o Serviço Linked para cada conjunto de dados que está a utilizar e testa as ligações.
1. Verifique o estado do seu ficheiro e ligações de mesa do designer de fluxo de dados. Ligue o Debug e clique na Pré-visualização de dados nas transformações da fonte para garantir que consegue aceder aos seus dados.
1. Se tudo correr bem a partir da pré-visualização de dados, entre no pipeline designer e coloque o fluxo de dados numa atividade de pipeline. Desperte o gasoduto para um teste de ponta a ponta.

## <a name="next-steps"></a>Passos seguintes

Para obter mais ajuda para resolução de problemas, experimente estes recursos:
*  [Blog da Fábrica de Dados](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Pedidos de funcionalidade sinuosos da Fábrica de Dados](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&Uma página de perguntas](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Stack Overflow forum para fábrica de dados](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre data factory](https://twitter.com/hashtag/DataFactory)
*  [ADF mapeando dados fluxos Performance Guide](concepts-data-flow-performance.md)
