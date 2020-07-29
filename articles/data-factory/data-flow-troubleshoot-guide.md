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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83829125"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Fluxos de dados de resolução de problemas na Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo explora métodos comuns de resolução de problemas para fluxos de dados na Azure Data Factory.

## <a name="common-errors-and-messages"></a>Erros e mensagens comuns

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Código de erro: DF-Executor-SourceInvalidPayload
- **Mensagem**: Pré-visualização de dados, depuragem e execução de fluxo de dados do gasoduto falhou porque o contentor não existe
- **Causas**: Quando o conjunto de dados contém um recipiente que não existe no armazenamento
- **Recomendação**: Certifique-se de que o recipiente referenciado no seu conjunto de dados existe ou está acessível.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Código de erro: DF-Executor-SystemImplicitCartesian

- **Mensagem**: O produto cartesiano implícito para a junção INNER não é suportado, utilize CROSS JOIN em vez disso. As colunas utilizadas em conjunto devem criar uma chave única para as linhas.
- **Causas**: O produto cartesiano implícito para o INNER entre planos lógicos não é suportado. Se as colunas utilizadas na junta criarem a chave única, é necessária pelo menos uma coluna de ambos os lados da relação.
- **Recomendação**: Para a união baseada na não igualdade, tem de optar pela CUSTOM CROSS JOIN.

### <a name="error-code-df-executor-systeminvalidjson"></a>Código de erro: DF-Executor-SystemInvalidJson

- **Mensagem**: Erro de análise JSON, codificação não suportada ou multiline
- **Causas**: Possíveis problemas com o ficheiro JSON: codificação não suportada, bytes corruptos ou utilização da fonte JSON como documento único em muitas linhas aninhadas
- **Recomendação**: Verifique se a codificação do ficheiro JSON está suportada. Sobre a transformação de Origem que está a utilizar um conjunto de dados JSON, expanda as definições de JSON e ligue o 'Documento Único'.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Código de erro: DF-Executor-BroadcastTimeout

- **Mensagem**: Transmita erro de tempo limite, certifique-se de que o fluxo de transmissão produz dados dentro de 60 segundos em depuração e 300 segundos em execuções de trabalho
- **Causas**: A transmissão tem um tempo limite de 60 segundos em depuração e 300 segundos em execuções de emprego. O fluxo escolhido para transmissão parece ser grande para produzir dados dentro deste limite.
- **Recomendação**: Verifique o separador Otimize sobre as transformações do fluxo de dados para Join, Exists e Lookup. A opção padrão para transmissão é "Auto". Se isto estiver definido, ou se estiver a definir manualmente o lado esquerdo ou direito para transmitir em "Fixo", então pode definir uma configuração de runtime de integração Azure maior ou desligar a transmissão. A abordagem recomendada para o melhor desempenho nos fluxos de dados é permitir que a Spark transmita usando "Auto" e utilize um Azure IR otimizado de memória.

### <a name="error-code-df-executor-conversion"></a>Código de erro: DF-Executor-Conversão

- **Mensagem**: Converter para uma data ou hora falhou devido a um caráter inválido
- **Causas**: Os dados não estão no formato esperado
- **Recomendação**: Utilize o tipo de dados correto

### <a name="error-code-df-executor-invalidcolumn"></a>Código de erro: DF-Executor-InvalidColumn

- **Mensagem**: O nome da coluna tem de ser especificado na consulta, definir um pseudónimo se utilizar uma função SQL
- **Causas**: Nenhum nome de coluna foi especificado
- **Recomendação**: Desaver um pseudónimo se utilizar uma função SQL como min()/max(), etc.

### <a name="error-code-getcommand-outputasync-failed"></a>Código de erro: GetCommand OutputAsync falhou

- **Mensagem**: Durante o depurador de fluxo de dados e pré-visualização de dados: GetCommand OutputAsync falhou com ...
- **Causas**: Trata-se de um erro de serviço de back-end. Pode voltar a tentar a operação e também reiniciar a sua sessão de depurar.
- **Recomendação**: Se voltar a tentar e reiniciar não resolva o problema, contacte o apoio ao cliente.

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>Código de erro: Acerte a exceção inesperada e a execução falhou

- **Mensagem**: Durante a execução da atividade do Fluxo de Dados: Acerte uma exceção inesperada e a execução falhou.
- **Causas**: Trata-se de um erro de serviço de back-end. Pode voltar a tentar a operação e também reiniciar a sua sessão de depurar.
- **Recomendação**: Se voltar a tentar e reiniciar não resolva o problema, contacte o apoio ao cliente.

## <a name="general-troubleshooting-guidance"></a>Orientação geral de resolução de problemas

1. Verifique o estado das suas ligações de conjunto de dados. Em cada transformação de Fonte e Pia, visite o Serviço Linked para cada conjunto de dados que está a utilizar e teste de ligações.
1. Verifique o estado do seu ficheiro e as ligações de tabela do designer de fluxos de dados. Ligue o Debug e clique na visualização de dados nas suas transformações de Origem para garantir que consegue aceder aos seus dados.
1. Se tudo estiver bem desde a pré-visualização de dados, vá ao designer pipeline e coloque o seu fluxo de dados numa atividade de pipeline. Depurar o oleoduto para um teste de ponta a ponta.

## <a name="next-steps"></a>Próximos passos

Para obter mais ajuda para resolver problemas, experimente estes recursos:
*  [Blog da Fábrica de Dados](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Pedidos de recursos da Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&Uma página de perguntas](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Stack Overflow Forum para a Fábrica de Dados](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre a Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Dados de mapeamento ADF fluem Guia de Desempenho](concepts-data-flow-performance.md)
