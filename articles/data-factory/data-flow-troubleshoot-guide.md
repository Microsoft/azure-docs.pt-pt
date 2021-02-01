---
title: Fluxos de dados de mapeamento de resolução de problemas
description: Saiba como resolver problemas de fluxo de dados na Azure Data Factory.
services: data-factory
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/11/2020
ms.openlocfilehash: cc87694686bd5143b03d690286bd3171cf8b0e18
ms.sourcegitcommit: 983eb1131d59664c594dcb2829eb6d49c4af1560
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2021
ms.locfileid: "99222154"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Fluxos de dados de mapeamento de resolução de problemas na Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo explora métodos comuns de resolução de problemas para mapear fluxos de dados na Azure Data Factory.

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
- **Causas**: A transmissão tem um tempo limite de 60 segundos em depuração e 300 segundos em execuções de trabalho. O fluxo escolhido para transmissão parece demasiado grande para produzir dados dentro deste limite.
- **Recomendação**: Verifique o separador Otimize sobre as transformações do fluxo de dados para Join, Exists e Lookup. A opção padrão para transmissão é "Auto". Se "Auto" estiver definido, ou se estiver a definir manualmente o lado esquerdo ou direito para transmitir em "Fixo", então pode definir uma configuração de runtime de integração Azure maior ou desligar a transmissão. A abordagem recomendada para o melhor desempenho nos fluxos de dados é permitir que a Spark transmita usando "Auto" e utilize um Azure IR otimizado de memória.

Se estiver a executar o fluxo de dados numa execução de teste de depuragem a partir de uma corrida de gasoduto de depuragem, poderá encontrar-se com mais frequência nesta condição. Isto porque a ADF acelera o tempo limite de transmissão para 60 segundos, a fim de manter uma experiência de depuração mais rápida. Se quiser estender isso ao intervalo de 300 segundos de uma execução desencadeada, pode utilizar a opção Debug > Use Activity Runtime para utilizar o Azure IR definido na sua atividade de pipeline Executar fluxo de dados.

### <a name="error-code-df-executor-conversion"></a>Código de erro: DF-Executor-Conversão

- **Mensagem**: Converter para uma data ou hora falhou devido a um caráter inválido
- **Causas**: Os dados não estão no formato esperado
- **Recomendação**: Utilize o tipo de dados correto

### <a name="error-code-df-executor-invalidcolumn"></a>Código de erro: DF-Executor-InvalidColumn

- **Mensagem**: O nome da coluna tem de ser especificado na consulta, definir um pseudónimo se utilizar uma função SQL
- **Causas**: Nenhum nome de coluna foi especificado
- **Recomendação**: Desaver um pseudónimo se utilizar uma função SQL como min()/max(), etc.

 ### <a name="error-code-df-executor-drivererror"></a>Código de erro: DF-Executor-DriverError
- **Mensagem**: INT96 é o tipo de timetamp legado que não é suportado por ADF Dataflow. Por favor, considere atualizar o tipo de coluna para os tipos mais recentes.
- **Causas**: Erro do condutor
- **Recomendação**: INT96 é o tipo de timetamp legacy, que não é suportado pelo Fluxo de Dados ADF. Considere atualizar o tipo de coluna para os tipos mais recentes.

 ### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Código de erro: DF-Executor-BlockCountExceedsLimitError
- **Mensagem**: A contagem de blocos não comprometida não pode exceder o limite máximo de 100.000 blocos. Verifique a configuração do blob.
- **Causas**: Pode haver um máximo de 100.000 blocos não comprometidos numa bolha.
- **Recomendação**: Contacte a equipa de produtos da Microsoft sobre este problema para obter mais detalhes

 ### <a name="error-code-df-executor-partitiondirectoryerror"></a>Código de erro: DF-Executor-PartitionDirectoryError
- **Mensagem**: O caminho de origem especificado tem ou vários diretórios divididos (por <Source Path> exemplo/<Diretório raiz de partição 1>/a=10/b=20, <Source Path> /<Diretório raiz de partição 2>/c=10/d=30) ou diretório dividido com outro ficheiro ou diretório não dividido (por exemplo <Source Path> /<Diretório raiz de partição 1>/a=10/b=20, <Source Path> /Diretório 2/file1), remover o diretório de raiz de partição da via de origem e lê-lo através de uma transformação de fonte separada.
- **Causas**: O caminho de origem tem vários diretórios divididos ou diretórios divididos com outro ficheiro ou diretório não dividido.
- **Recomendação**: Retire o diretório de raiz partido do caminho da origem e leia-o através de uma transformação separada da fonte.

 ### <a name="error-code-df-executor-outofmemoryerror"></a>Código de erro: DF-Executor-OutOfMemoryError
- **Mensagem**: O cluster escorria para o problema de memória durante a execução, por favor, recava o tempo de integração com maior contagem de núcleos e/ou tipo de computação otimizada da memória
- **Causas**: O cluster está a ficar sem memória
- **Recomendação**: Os agrupamentos de depurg destinam-se a fins de desenvolvimento. Alavancar a amostragem de dados, o tipo de computação apropriado e o tamanho para executar a carga útil. Consulte o guia de desempenho do [fluxo de dados de mapeamento](concepts-data-flow-performance.md) para obter o melhor desempenho.

 ### <a name="error-code-df-executor-illegalargument"></a>Código de erro: DF-Executor-ilegalArgument
- **Mensagem**: Certifique-se de que a chave de acesso no seu Serviço Linked está correta
- **Causas**: Nome da conta ou chave de acesso incorreta
- **Recomendação**: Certifique-se de que o nome da conta ou a chave de acesso especificada no seu serviço ligado está correto. 

 ### <a name="error-code-df-executor-invalidtype"></a>Código de erro: DF-Executor-InvalidType
- **Mensagem**: Certifique-se de que o tipo de parâmetro coincide com o tipo de valor transmitido. A passagem de parâmetros flutuantes dos oleodutos não está suportada atualmente.
- **Causas**: Tipos de dados incompatíveis entre o tipo declarado e o valor do parâmetro real
- **Recomendação**: Verifique se os valores dos seus parâmetros passados num fluxo de dados correspondem ao tipo declarado.

 ### <a name="error-code-df-executor-columnunavailable"></a>Código de erro: DF-Executor-ColumnUn disponível
- **Mensagem**: Nome da coluna utilizado na expressão não está disponível ou inválido
- **Causas**: Nome de coluna inválido ou indisponível utilizado em expressões
- **Recomendação**: Verificar nomes de colunas utilizados em expressões

 ### <a name="error-code-df-executor-parseerror"></a>Código de erro: DF-Executor-ParseError
- **Mensagem**: A expressão não pode ser analisada
- **Causas**: A expressão tem erros de análise devido à formatação
- **Recomendação**: Verificar formatação em expressão

### <a name="error-code-getcommand-outputasync-failed"></a>Código de erro: GetCommand OutputAsync falhou

- **Mensagem**: Durante o depurador de fluxo de dados e pré-visualização de dados: GetCommand OutputAsync falhou com ...
- **Causas**: Trata-se de um erro de serviço de back-end. Pode voltar a tentar a operação e também reiniciar a sua sessão de depurar.
- **Recomendação**: Se voltar a tentar e reiniciar não resolva o problema, contacte o apoio ao cliente.

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>Código de erro: Acerte a exceção inesperada e a execução falhou

- **Mensagem**: Durante a execução da atividade do Fluxo de Dados: Acerte uma exceção inesperada e a execução falhou.
- **Causas**: Trata-se de um erro de serviço de back-end. Pode voltar a tentar a operação e também reiniciar a sua sessão de depurar.
- **Recomendação**: Se voltar a tentar e reiniciar não resolva o problema, contacte o apoio ao cliente.

### <a name="error-code-debug-data-preview-no-output-data-on-join"></a>Código de erro: Debug pré-visualização de dados Sem Dados de Saída em Conjunto

- **Mensagem**: Há um elevado número de valores nulos ou valores em falta que podem ser causados por terem poucas linhas amostradas. Tente atualizar o limite da linha de depuragem e refrescar os dados.
- **Causas**: A condição de junção não correspondeu a nenhuma linha ou resultou num elevado número de NULLs durante a pré-visualização dos dados.
- **Recomendação**: Vá às Definições de Debug e aumente o número de linhas no limite da linha de origem. Certifique-se de que selecionou um Azure IR com um cluster de fluxo de dados suficientemente grande para lidar com mais dados.

### <a name="error-code-validation-error-at-source-with-multiline-csv-files"></a>Código de erro: Erro de validação na Fonte com ficheiros CSV multiline 

- **Mensagem**: Pode ver uma das seguintes mensagens de erro:
   - A última coluna é nula ou desaparecida.
   - A validação do esquema na fonte falha.
   - A importação de schema não aparece corretamente no UX e a última coluna tem um novo carácter de linha no nome.
- **Causas**: No fluxo de dados de mapeamento, atualmente, a fonte multiline CSV não funciona com o \r\n como delimiter de linha. Às vezes, linhas extras na carruagem retornam valores de origem. 
- **Recomendação**: Ou gera o ficheiro na fonte com \n como delimiter de linha em vez de \r\n. Ou, utilize a Copy Activity para converter o ficheiro CSV com \r\n para \n como umlimiter de linha.

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Código de erro: DF-Executor-SourceInvalidPayload
- **Mensagem**: Pré-visualização de dados, depuragem e execução de fluxo de dados do gasoduto falhou porque o contentor não existe
- **Causas**: Quando o conjunto de dados contém um recipiente que não existe no armazenamento
- **Recomendação**: Certifique-se de que o recipiente referenciado no seu conjunto de dados existe ou está acessível.


 ### <a name="error-code-df-executor-systemimplicitcartesian"></a>Código de erro: DF-Executor-SystemImplicitCartesian
- **Mensagem**: O produto cartesiano implícito para a junção INNER não é suportado, utilize CROSS JOIN em vez disso. As colunas utilizadas em conjunto devem criar uma chave única para as linhas.
- **Causas**: O produto cartesiano implícito para o INNER entre planos lógicos não é suportado. Se as colunas utilizadas na junta criarem a chave única
- **Recomendação**: Para a união baseada na não igualdade, tem de optar pela CROSS JOIN.


 ### <a name="error-code-df-executor-systeminvalidjson"></a>Código de erro: DF-Executor-SystemInvalidJson
- **Mensagem**: Erro de análise JSON, codificação não suportada ou multiline
- **Causas**: Possíveis problemas com o ficheiro JSON: codificação não suportada, bytes corruptos ou utilização da fonte JSON como documento único em muitas linhas aninhadas
- **Recomendação**: Verifique se a codificação do ficheiro JSON está suportada. Sobre a transformação de Origem que está a utilizar um conjunto de dados JSON, expanda as definições de JSON e ligue o 'Documento Único'.


 ### <a name="error-code-df-executor-broadcasttimeout"></a>Código de erro: DF-Executor-BroadcastTimeout
- **Mensagem**: Enviar erro de tempo limite de transmissão, pode escolher 'Off' da opção de transmissão na transformação de join/exists/lookup para evitar este problema. Se pretende transmitir a opção de aderir para melhorar o desempenho, então certifique-se de que o fluxo de transmissão pode produzir dados dentro de 60 segundos em depurações de depurações e 300 segundos em execuções de emprego.
- **Causas**: A transmissão tem um tempo limite de 60 segundos em depuração e 300 segundos em execuções de emprego. Na transmissão, o fluxo escolhido para transmissão parece demasiado grande para produzir dados dentro deste limite. Se uma junção de transmissão não for utilizada, a transmissão padrão feita por fluxo de dados pode atingir o mesmo limite
- **Recomendação**: Desligue a opção de transmissão ou evite transmitir grandes fluxos de dados onde o processamento pode demorar mais de 60 segundos. Escolha um fluxo menor para transmitir. Grandes tabelas SQL/DW e ficheiros de origem são tipicamente maus candidatos. Na ausência de uma junção de transmissão, utilize um cluster maior se ocorrer o erro.


 ### <a name="error-code-df-executor-conversion"></a>Código de erro: DF-Executor-Conversão
- **Mensagem**: Converter para uma data ou hora falhou devido a um caráter inválido
- **Causas**: Os dados não estão no formato esperado
- **Recomendação**: Utilize o tipo de dados correto


 ### <a name="error-code-df-executor-invalidcolumn"></a>Código de erro: DF-Executor-InvalidColumn
- **Mensagem**: O nome da coluna tem de ser especificado na consulta, definir um pseudónimo se utilizar uma função SQL
- **Causas**: Não foi especificado nenhum nome de coluna.


 ### <a name="error-code-df-executor-drivererror"></a>Código de erro: DF-Executor-DriverError
- **Mensagem**: INT96 é o tipo de timetamp legado que não é suportado por ADF Dataflow. Por favor, considere atualizar o tipo de coluna para os tipos mais recentes.
- **Causas**: É um erro do controlador.
- **Recomendação**: INT96 é o tipo de timetamp legado que não é suportado pelo Fluxo de Dados ADF. Por favor, considere atualizar o tipo de coluna para os tipos mais recentes.


 ### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Código de erro: DF-Executor-BlockCountExceedsLimitError
- **Mensagem**: A contagem de blocos não comprometida não pode exceder o limite máximo de 100.000 blocos. Verifique a configuração do blob.
- **Causas**: Pode haver um máximo de 100.000 blocos não comprometidos numa bolha.
- **Recomendação**: Contacte a equipa de produtos da Microsoft sobre este problema para obter mais detalhes

 ### <a name="error-code-df-executor-partitiondirectoryerror"></a>Código de erro: DF-Executor-PartitionDirectoryError
- **Mensagem**: O caminho de origem especificado tem ou vários diretórios divididos (por <Source Path> exemplo/<Diretório raiz de partição 1>/a=10/b=20, <Source Path> /<Diretório raiz de partição 2>/c=10/d=30) ou diretório dividido com outro ficheiro ou diretório não dividido (por <Source Path> exemplo/<Diretório raiz de partição 1>/a=10/b=20, <Source Path> /Diretório 2/1), remover o diretório de raiz de partição da origem da via e lê-lo através de uma transformação separada.
- **Causas**: O caminho de origem tem vários diretórios divididos ou diretórios divididos com outro ficheiro ou diretório não dividido.
- **Recomendação**: Retire o diretório de raiz partido do caminho da origem e leia-o através de uma transformação separada da fonte.


 ### <a name="error-code-df-executor-outofmemoryerror"></a>Código de erro: DF-Executor-OutOfMemoryError
- **Mensagem**: O cluster escorria para o problema de memória durante a execução, por favor, recava o tempo de integração com maior contagem de núcleos e/ou tipo de computação otimizada da memória
- **Causas**: O agrupamento está a ficar sem memória.
- **Recomendação**: Os agrupamentos de depurg destinam-se a fins de desenvolvimento. Alavancar a amostragem de dados adequada tipo e tamanho para executar a carga útil. Consulte o [Guia de Desempenho do Fluxo de Dados](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-performance) para sintonizar os fluxos de dados para obter um melhor desempenho.


 ### <a name="error-code-df-executor-illegalargument"></a>Código de erro: DF-Executor-ilegalArgument
- **Mensagem**: Certifique-se de que a chave de acesso no seu Serviço Linked está correta.
- **Causas**: O nome da conta ou a chave de acesso estão incorretos.
- **Recomendação**: Forneça o nome da conta certa ou a chave de acesso.


 ### <a name="error-code-df-executor-invalidtype"></a>Código de erro: DF-Executor-InvalidType
- **Mensagem**: Certifique-se de que o tipo de parâmetro coincide com o tipo de valor transmitido. A passagem de parâmetros flutuantes dos oleodutos não está suportada atualmente.
- **Causas**: Tipos de dados incompatíveis entre o tipo declarado e o valor do parâmetro real
- **Recomendação**: Forneça os tipos certos de dados.


 ### <a name="error-code-df-executor-columnunavailable"></a>Código de erro: DF-Executor-ColumnUn disponível
- **Mensagem**: O nome da coluna utilizado na expressão não está disponível ou inválido.
- **Causas**: O nome da coluna inválida ou indisponível é utilizado em expressões.
- **Recomendação**: Verifique os nomes da coluna utilizados em expressões.


 ### <a name="error-code-df-executor-parseerror"></a>Código de erro: DF-Executor-ParseError
- **Mensagem**: A expressão não pode ser analisada.
- **Causas**: A expressão tem erros de análise devido à formatação.
- **Recomendação**: Verifique a formatação em expressão.


 ### <a name="error-code-df-executor-outofdiskspaceerror"></a>Código de erro: DF-Executor-OutOfDiskSpaceError
- **Mensagem**: Erro interno do servidor
- **Causas**: O cluster está a ficar sem espaço em disco.
- **Recomendação:** Por favor, recandiduça o gasoduto. Se o problema persistir, contacte o suporte do cliente.


 ### <a name="error-code-df-executor-storeisnotdefined"></a>Código de erro: DF-Executor-StoreIsNotDefined
- **Mensagem**: A configuração da loja não está definida. Este erro é potencialmente causado por uma atribuição de parâmetros inválidos no pipeline.
- **Causas**: Indeterminado
- **Recomendação:** Verifique a atribuição do valor dos parâmetros no oleoduto. A expressão do parâmetro pode conter caracteres inválidos.


 ### <a name="error-code-df-excel-invalidconfiguration"></a>Código de erro: DF-Excel-InvalidConfiguration
- **Mensagem**: É necessário o nome ou índice da folha excel.
- **Causas**: Indeterminado
- **Recomendação**: Verifique o valor do parâmetro e especifique o nome ou o índice da folha para ler os dados do Excel.


 ### <a name="error-code-df-excel-invalidconfiguration"></a>Código de erro: DF-Excel-InvalidConfiguration
- **Mensagem**: O nome e o índice da folha de excel não podem existir ao mesmo tempo.
- **Causas**: Indeterminado
- **Recomendação**: Verifique o valor do parâmetro e especifique o nome ou o índice da folha para ler os dados do Excel.


 ### <a name="error-code-df-excel-invalidconfiguration"></a>Código de erro: DF-Excel-InvalidConfiguration
- **Mensagem**: É fornecido um intervalo inválido.
- **Causas**: Indeterminado
- **Recomendação**: Verifique o valor do parâmetro e especifique o intervalo válido por referência: [Propriedades excel](https://docs.microsoft.com/azure/data-factory/format-excel#dataset-properties).


 ### <a name="error-code-df-excel-invaliddata"></a>Código de erro: DF-Excel-InvalidData
- **Mensagem**: A folha de cálculo do Excel não existe.
- **Causas**: Indeterminado
- **Recomendação**: Verifique o valor do parâmetro e especifique o nome ou índice de folha válido para ler os dados do Excel.

 ### <a name="error-code-df-excel-invaliddata"></a>Código de erro: DF-Excel-InvalidData
- **Mensagem**: Ler ficheiros excel com esquemas diferentes não é suportado agora.
- **Causas**: Indeterminado
- **Recomendação**: Utilize o ficheiro Excel correto.


 ### <a name="error-code-df-excel-invaliddata"></a>Código de erro: DF-Excel-InvalidData
- **Mensagem**: O tipo de dados não é suportado.
- **Causas**: Indeterminado
- **Recomendação**: Utilize tipos de dados corretos do ficheiro Excel.

 ### <a name="error-code-df-excel-invalidconfiguration"></a>Código de erro: DF-Excel-InvalidConfiguration
- **Mensagem**: O ficheiro excel inválido é fornecido enquanto apenas .xlsx e .xls são suportados
- **Causas**: Indeterminado
- **Recomendação**: Certifique-se de que a extensão do ficheiro Excel é .xlsx ou .xls.

## <a name="general-troubleshooting-guidance"></a>Orientação geral de resolução de problemas
1. Verifique o estado das suas ligações de conjunto de dados. Em cada transformação de Fonte e Pia, visite o Serviço Linked para cada conjunto de dados que está a utilizar e teste de ligações.
2. Verifique o estado do seu ficheiro e as ligações de tabela do designer de fluxos de dados. Ligue o Debug e clique na visualização de dados nas suas transformações de Origem para garantir que consegue aceder aos seus dados.
3. Se tudo estiver bem desde a pré-visualização de dados, vá ao designer pipeline e coloque o seu fluxo de dados numa atividade de pipeline. Depurar o oleoduto para um teste de ponta a ponta.


## <a name="next-steps"></a>Passos seguintes

Para obter mais ajuda para resolver problemas, experimente estes recursos:
*  [Blog da Fábrica de Dados](https://techcommunity.microsoft.com/t5/azure-data-factory/bg-p/AzureDataFactoryBlog)
*  [Pedidos de recursos da Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://www.youtube.com/channel/UC2S0k7NeLcEm5_IhHUwpN0g/videos)
*  [Microsoft Q&Uma página de perguntas](/answers/topics/azure-data-factory.html)
*  [Stack Overflow Forum para a Fábrica de Dados](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre a Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Dados de mapeamento ADF fluem Guia de Desempenho](concepts-data-flow-performance.md)
