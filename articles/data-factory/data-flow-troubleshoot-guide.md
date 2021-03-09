---
title: Fluxos de dados de mapeamento de resolução de problemas
description: Saiba como resolver problemas de fluxo de dados na Azure Data Factory.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/11/2020
ms.openlocfilehash: f8a852a8c4197169061a9c7633f4f363ad057337
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102505805"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Fluxos de dados de mapeamento de resolução de problemas na Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Este artigo explora métodos comuns de resolução de problemas para mapear fluxos de dados na Azure Data Factory.

## <a name="common-error-codes-and-messages"></a>Códigos e mensagens de erro comuns 

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Código de erro: DF-Executor-SourceInvalidPayload
- **Mensagem**: Pré-visualização de dados, depuragem e execução de fluxo de dados do gasoduto falhou porque o contentor não existe
- **Causa:** Um conjunto de dados contém um recipiente que não existe no armazenamento.
- **Recomendação**: Certifique-se de que o recipiente referenciado no seu conjunto de dados existe e pode ser acedido.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Código de erro: DF-Executor-SystemImplicitCartesian

- **Mensagem**: O produto cartesiano implícito para a junção INNER não é suportado, utilize CROSS JOIN em vez disso. As colunas utilizadas em conjunto devem criar uma chave única para as linhas.
- **Causa:** Os produtos cartesianos implícitos para o INNER unem-se entre planos lógicos não são suportados. Se estiver a usar colunas na junta, crie uma chave única com pelo menos uma coluna de ambos os lados da relação.
- **Recomendação**: Para junções baseadas em não igualdade, utilize a junção CUSTOM CROSS.

### <a name="error-code-df-executor-systeminvalidjson"></a>Código de erro: DF-Executor-SystemInvalidJson

- **Mensagem**: Erro de análise JSON, codificação não suportada ou multiline
- **Causa**: Possíveis problemas com o ficheiro JSON: codificação não suportada, bytes corruptos ou utilização da fonte JSON como um único documento em muitas linhas aninhadas.
- **Recomendação**: Verifique se a codificação do ficheiro JSON é suportada. Sobre a transformação de origem que está a utilizar um conjunto de dados JSON, expanda **as definições JSON** e ligue **o Documento Único**.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Código de erro: DF-Executor-BroadcastTimeout

- **Mensagem**: Transmita erro de tempo limite, certifique-se de que o fluxo de transmissão produz dados dentro de 60 segundos em depuração e 300 segundos em execuções de trabalho
- **Causa**: A transmissão tem um tempo limite de 60 segundos em depuração e 300 segundos em execuções de trabalho. O fluxo escolhido para transmissão é demasiado grande para produzir dados dentro deste limite.
- **Recomendação**: Verifique o separador **Otimize** sobre as transformações do fluxo de dados para se juntar, existe e procurar. A opção padrão para transmissão é **Auto**. Se **o Auto** estiver definido, ou se estiver a definir manualmente o lado esquerdo ou direito para transmitir em **Fixed,** pode definir uma configuração de execução de integração Azure maior (IR) ou desligar a transmissão. Para obter o melhor desempenho nos fluxos de dados, recomendamos que permita que a Spark transmita utilizando **o Auto** e utilize um Azure IR otimizado para a memória. 
 
  Se estiver a executar o fluxo de dados numa execução de teste de depuragem a partir de um gasoduto de depuragem, poderá encontrar-se com mais frequência nesta condição. Isto porque a Azure Data Factory acelera o tempo de transmissão para 60 segundos para manter uma experiência de depuração mais rápida. Pode estender o tempo limite para o tempo limite de 300 segundos de uma corrida desencadeada. Para tal, pode utilizar a opção **Debug**  >  **Use Activity Runtime** para utilizar o Azure IR definido na sua atividade de pipeline Executar fluxo de dados.

- **Mensagem**: Enviar erro de tempo limite de transmissão, pode escolher 'Off' da opção de transmissão na transformação de join/exists/lookup para evitar este problema. Se pretende transmitir a opção de aderir para melhorar o desempenho, então certifique-se de que o fluxo de transmissão pode produzir dados dentro de 60 segundos em depurações de depurações e 300 segundos em execuções de emprego.
- **Causa**: A transmissão tem um tempo limite de 60 segundos em depuração e 300 segundos em execuções de trabalho. Na transmissão, o fluxo escolhido para transmissão é demasiado grande para produzir dados dentro deste limite. Se uma junção de transmissão não for utilizada, a transmissão padrão por fluxo de dados pode atingir o mesmo limite.
- **Recomendação**: Desligue a opção de transmissão ou evite transmitir grandes fluxos de dados para os quais o processamento pode demorar mais de 60 segundos. Escolha um fluxo menor para transmitir. As grandes tabelas do Armazém de Dados Azure SQL e os ficheiros de origem não são normalmente boas escolhas. Na ausência de uma junção de transmissão, utilize um cluster maior se este erro ocorrer.

### <a name="error-code-df-executor-conversion"></a>Código de erro: DF-Executor-Conversão

- **Mensagem**: Converter para uma data ou hora falhou devido a um caráter inválido
- **Causa:** Os dados não estão no formato esperado.
- **Recomendação**: Utilize o tipo de dados correto.

### <a name="error-code-df-executor-invalidcolumn"></a>Código de erro: DF-Executor-InvalidColumn
- **Mensagem**: O nome da coluna tem de ser especificado na consulta, definir um pseudónimo se utilizar uma função SQL
- **Causa**: Não é especificado nenhum nome de coluna.
- **Recomendação**: Desafine um pseudónimo se estiver a utilizar uma função SQL como min() ou max().

### <a name="error-code-df-executor-drivererror"></a>Código de erro: DF-Executor-DriverError
- **Mensagem**: INT96 é o tipo de timetamp legado que não é suportado por ADF Dataflow. Por favor, considere atualizar o tipo de coluna para os tipos mais recentes.
- **Causa**: Erro do condutor.
- **Recomendação**: O INT96 é um tipo de timetamp legado que não é suportado pelo fluxo de dados da Azure Data Factory. Considere atualizar o tipo de coluna para o tipo mais recente.

### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Código de erro: DF-Executor-BlockCountExceedsLimitError
- **Mensagem**: A contagem de blocos não comprometida não pode exceder o limite máximo de 100.000 blocos. Verifique a configuração do blob.
- **Causa**: O número máximo de blocos não autorizados numa bolha é de 100.000.
- **Recomendação**: Contacte a equipa de produtos da Microsoft para obter mais detalhes sobre este problema.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Código de erro: DF-Executor-PartitionDirectoryError
- **Mensagem**: O caminho de origem especificado tem ou vários diretórios divididos (por <Source Path> exemplo/<Diretório raiz de partição 1>/a=10/b=20, <Source Path> /<Diretório raiz de partição 2>/c=10/d=30) ou diretório dividido com outro ficheiro ou diretório não dividido (por exemplo <Source Path> /<Diretório raiz de partição 1>/a=10/b=20, <Source Path> /Diretório 2/file1), remover o diretório de raiz de partição da via de origem e lê-lo através de uma transformação de fonte separada.
- **Causa**: O caminho de origem tem vários diretórios divididos ou um diretório dividido que tem outro ficheiro ou diretório não dividido.
- **Recomendação:** Retire o diretório de raiz dividido do caminho da origem e leia-o através de uma transformação de fonte separada.

### <a name="error-code-df-executor-invalidtype"></a>Código de erro: DF-Executor-InvalidType
- **Mensagem**: Certifique-se de que o tipo de parâmetro coincide com o tipo de valor transmitido. A passagem de parâmetros flutuantes dos oleodutos não está suportada atualmente.
- **Causa**: O tipo de dados para o tipo declarado não é compatível com o valor do parâmetro real.
- **Recomendação**: Verifique se os valores dos parâmetros transmitidos para o fluxo de dados correspondem ao tipo declarado.

### <a name="error-code-df-executor-columnunavailable"></a>Código de erro: DF-Executor-ColumnUn disponível
- **Mensagem**: Nome da coluna utilizado na expressão não está disponível ou inválido
- **Causa:** Um nome de coluna inválido ou indisponível usado numa expressão.
- **Recomendação**: Verifique os nomes das colunas nas expressões.

### <a name="error-code-df-executor-parseerror"></a>Código de erro: DF-Executor-ParseError
- **Mensagem**: A expressão não pode ser analisada
- **Causa**: Uma expressão gerou erros de análise devido a formatação incorreta.
- **Recomendação**: Verifique a formatação na expressão.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Código de erro: DF-Executor-SystemImplicitCartesian
- **Mensagem**: O produto cartesiano implícito para a junção INNER não é suportado, utilize CROSS JOIN em vez disso. As colunas utilizadas em conjunto devem criar uma chave única para as linhas.
- **Causa:** Os produtos cartesianos implícitos para o INNER unem-se entre planos lógicos não são suportados. Se estiver a usar colunas na junta, crie uma chave única.
- **Recomendação**: Para aderir a não-igualdade, utilize CROSS JOIN.

### <a name="error-code-df-executor-systeminvalidjson"></a>Código de erro: DF-Executor-SystemInvalidJson
- **Mensagem**: Erro de análise JSON, codificação não suportada ou multiline
- **Causa**: Possíveis problemas com o ficheiro JSON: codificação não suportada, bytes corruptos ou utilização da fonte JSON como um único documento em muitas linhas aninhadas.
- **Recomendação**: Verifique se a codificação do ficheiro JSON é suportada. Sobre a transformação de origem que está a utilizar um conjunto de dados JSON, expanda **as definições JSON** e ligue **o Documento Único**.



### <a name="error-code-df-executor-conversion"></a>Código de erro: DF-Executor-Conversão
- **Mensagem**: Converter para uma data ou hora falhou devido a um caráter inválido
- **Causa:** Os dados não estão no formato esperado.
- **Recomendação**: Utilize o tipo de dados correto.


### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Código de erro: DF-Executor-BlockCountExceedsLimitError
- **Mensagem**: A contagem de blocos não comprometida não pode exceder o limite máximo de 100.000 blocos. Verifique a configuração do blob.
- **Causa**: O número máximo de blocos não autorizados numa bolha é de 100.000.
- **Recomendação**: Contacte a equipa de produtos da Microsoft para obter mais detalhes sobre este problema.

### <a name="error-code-df-executor-partitiondirectoryerror"></a>Código de erro: DF-Executor-PartitionDirectoryError
- **Mensagem**: O caminho de origem especificado tem ou vários diretórios divididos (por *<Source Path> exemplo/<Diretório raiz de partição 1>/a=10/b=20, <Source Path> /<Diretório raiz de partição 2>/c=10/d=30*) ou diretório dividido com outro ficheiro ou diretório não dividido (por *<Source Path> exemplo/<Diretório raiz de partição 1>/a=10/b=20, <Source Path> /Diretório 2/file1*), remover o diretório de raiz de partição do caminho da origem e lê-lo através de uma transformação separada.
- **Causa**: O caminho de origem tem vários diretórios divididos ou um diretório dividido que tem outro ficheiro ou diretório não dividido. 
- **Recomendação:** Retire o diretório de raiz dividido do caminho da origem e leia-o através de uma transformação de fonte separada.

### <a name="error-code-getcommand-outputasync-failed"></a>Código de erro: GetCommand OutputAsync falhou
- **Mensagem**: Durante o depurador de fluxo de dados e pré-visualização de dados: GetCommand OutputAsync falhou com ...
- **Causa**: Este erro é um erro de serviço de back-end. 
- **Recomendação:** Revendo a operação e reinicie a sua sessão de depuragem. Se voltar a tentar e reiniciar não resolver o problema, contacte o suporte do cliente. 

### <a name="error-code-df-executor-outofmemoryerror"></a>Código de erro: DF-Executor-OutOfMemoryError
 
- **Mensagem**: O cluster escorria para o problema de memória durante a execução, por favor, recava o tempo de integração com maior contagem de núcleos e/ou tipo de computação otimizada da memória
- **Causa:** O aglomerado está a ficar sem memória.
- **Recomendação**: Os aglomerados de depurg destinam-se ao desenvolvimento. Utilize a amostragem de dados e um tipo e tamanho de cálculo adequados para executar a carga útil. Para obter dicas de desempenho, consulte [o guia de desempenho do fluxo de dados de mapeamento](concepts-data-flow-performance.md).

### <a name="error-code-df-executor-illegalargument"></a>Código de erro: DF-Executor-ilegalArgument

- **Mensagem**: Certifique-se de que a chave de acesso no seu Serviço Linked está correta
- **Causa**: O nome da conta ou a chave de acesso estão incorretos.
- **Recomendação**: Certifique-se de que o nome da conta ou a chave de acesso especificada no seu serviço ligado está correto. 

### <a name="error-code-df-executor-invalidtype"></a>Código de erro: DF-Executor-InvalidType
- **Mensagem**: Certifique-se de que o tipo de parâmetro coincide com o tipo de valor transmitido. A passagem de parâmetros flutuantes dos oleodutos não está suportada atualmente.
- **Causa**: O tipo de dados para o tipo declarado não é compatível com o valor do parâmetro real. 
- **Recomendação**: Forneça os tipos de dados corretos.

### <a name="error-code-df-executor-columnunavailable"></a>Código de erro: DF-Executor-ColumnUn disponível
- **Mensagem**: O nome da coluna utilizado na expressão não está disponível ou inválido.
- **Causa**: Um nome de coluna inválido ou indisponível é usado numa expressão.
- **Recomendação**: Verifique os nomes das colunas utilizadas nas expressões.


### <a name="error-code-df-executor-parseerror"></a>Código de erro: DF-Executor-ParseError
- **Mensagem**: A expressão não pode ser analisada.
- **Causa**: Uma expressão gerou erros de análise devido a formatação incorreta. 
- **Recomendação**: Verifique a formatação na expressão.


 ### <a name="error-code-df-executor-outofdiskspaceerror"></a>Código de erro: DF-Executor-OutOfDiskSpaceError
- **Mensagem**: Erro interno do servidor
- **Causa:** O cluster está a ficar sem espaço em disco.
- **Recomendação:** Recandidutar o gasoduto. Se isso não resolver o problema, contacte o apoio ao cliente.


 ### <a name="error-code-df-executor-storeisnotdefined"></a>Código de erro: DF-Executor-StoreIsNotDefined
- **Mensagem**: A configuração da loja não está definida. Este erro é potencialmente causado por uma atribuição de parâmetros inválidos no pipeline.
- **Causa:** Indeterminado.
- **Recomendação**: Verifique a atribuição do valor dos parâmetros no oleoduto. Uma expressão de parâmetro pode conter caracteres inválidos.

### <a name="error-code-df-excel-invalidconfiguration"></a>Código de erro: DF-Excel-InvalidConfiguration
- **Mensagem**: É necessário o nome ou índice da folha excel.
- **Causa:** Indeterminado.
- **Recomendação**: Verifique o valor do parâmetro. Especifique o nome ou índice da folha de cálculo para a leitura dos dados do Excel.

- **Mensagem**: O nome e o índice da folha de excel não podem existir ao mesmo tempo.
- **Causa:** Indeterminado.
- **Recomendação**: Verifique o valor do parâmetro. Especifique o nome ou índice da folha de cálculo para a leitura dos dados do Excel.

- **Mensagem**: É fornecido um intervalo inválido.
- **Causa:** Indeterminado.
- **Recomendação**: Verifique o valor do parâmetro. Especifique um intervalo válido por referência. Para mais informações, consulte [as propriedades do Excel.](./format-excel.md#dataset-properties)

- **Mensagem**: O ficheiro excel inválido é fornecido enquanto apenas .xlsx e .xls são suportados
- **Causa:** Indeterminado.
- **Recomendação**: Certifique-se de que a extensão do ficheiro Excel é .xlsx ou .xls.


 ### <a name="error-code-df-excel-invaliddata"></a>Código de erro: DF-Excel-InvalidData
- **Mensagem**: A folha de cálculo do Excel não existe.
- **Causa:** Indeterminado.
- **Recomendação**: Verifique o valor do parâmetro. Especifique um nome ou índice de folha de cálculo válido para ler os dados do Excel.

- **Mensagem**: Ler ficheiros excel com esquemas diferentes não é suportado agora.
- **Causa:** Indeterminado.
- **Recomendação**: Utilize um ficheiro Excel suportado.

- **Mensagem**: O tipo de dados não é suportado.
- **Causa:** Indeterminado.
- **Recomendação**: Utilize tipos de dados de ficheiros Excel suportados.

### <a name="error-code-4502"></a>Código de erro: 4502
- **Mensagem**: Existem execuções simultâneas de MappingDataflow substanciais que estão a causar falhas devido a estrangulamentos no tempo de execução da integração.
- **Causa**: Um grande número de execuções de fluxo de dados estão ocorrendo simultaneamente no tempo de integração. Para mais informações, consulte [os limites da Azure Data Factory.](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits)
- **Recomendação**: Se pretender executar mais atividades de Fluxo de Dados em paralelo, distribua-as através de vários tempos de integração.


### <a name="error-code-invalidtemplate"></a>Código de erro: InvalidTemplate
- **Mensagem**: A expressão do gasoduto não pode ser avaliada.
- **Causa**: A expressão do gasoduto passada na atividade do Fluxo de Dados não está a ser processada corretamente devido a um erro de sintaxe.
- **Recomendação**: Verifique a sua atividade na monitorização da atividade para verificar a expressão.

### <a name="error-code-2011"></a>Código de erro: 2011
- **Mensagem**: A atividade estava a decorrer no Tempo de Execução da Integração Azure e não conseguiu desencriptar a credencial de data store ou computação ligada através de um Tempo de Execução de Integração Auto-hospedado. Verifique a configuração dos serviços ligados associados a esta atividade e certifique-se de utilizar o tipo de tempo de execução de integração adequado.
- **Causa:** O fluxo de dados não suporta serviços ligados em tempos de integração auto-hospedados.
- **Recomendação**: Configurar o fluxo de dados para executar num tempo de integração da rede virtual gerido.

## <a name="miscellaneous-troubleshooting-tips"></a>Dicas de resolução de problemas diversos
- **Emissão:** Ocorreu uma exceção inesperada e a execução falhou.
    - **Mensagem**: Durante a execução da atividade do Fluxo de Dados: Acerte uma exceção inesperada e a execução falhou.
    - **Causa**: Este erro é um erro de serviço de back-end. Recompôs a operação e reinicie a sua sessão de depuragem.
    - **Recomendação**: Se voltar a tentar e reiniciar não resolver o problema, contacte o suporte do cliente.

-  **Emissão**: Não há dados de saída sobre a união durante a pré-visualização dos dados de depuração.
    - **Mensagem**: Há um elevado número de valores nulos ou valores em falta que podem ser causados por terem poucas linhas amostradas. Tente atualizar o limite da linha de depuragem e refrescar os dados.
    - **Causa**: A condição de junção ou não corresponde a nenhuma linha ou resultou num grande número de valores nulos durante a pré-visualização dos dados.
    - **Recomendação**: Em **Definições de Debug,** aumente o número de linhas no limite da linha de origem. Certifique-se de selecionar um Azure IR que tenha um cluster de fluxo de dados que é grande o suficiente para lidar com mais dados.
    
- **Emissão**: Erro de validação na fonte com ficheiros CSV multiline. 
    - **Mensagem**: Pode ver uma destas mensagens de erro:
        - A última coluna é nula ou desaparecida.
        - A validação do esquema na fonte falha.
        - A importação de schema não aparece corretamente no UX e a última coluna tem um novo carácter de linha no nome.
    - **Causa**: No fluxo de dados de mapeamento, os ficheiros de origem CSV multiline não funcionam atualmente quando \r\n é usado como o delimiter de linha. Às vezes, linhas extras nas devoluções de transporte podem causar erros. 
    - **Recomendação**: Gere o ficheiro na fonte utilizando \n como delimiter de linha em vez de \r\n. Ou utilize a atividade Copy para converter o ficheiro CSV para utilizar \n como umlimiter de linha.

## <a name="general-troubleshooting-guidance"></a>Orientação geral de resolução de problemas
1. Verifique o estado das suas ligações de conjunto de dados. Em cada origem e transformação de pia, vá ao serviço ligado para cada conjunto de dados que está a usar e teste as ligações.
2. Verifique o estado do seu ficheiro e ligações de tabela no designer de fluxo de dados. No modo de depurar, selecione **Data Preview** nas suas transformações de origem para garantir que pode aceder aos seus dados.
3. Se tudo parecer correto na pré-visualização de dados, vá ao designer pipeline e coloque o seu fluxo de dados numa atividade pipeline. Depurar o oleoduto para um teste de ponta a ponta.

## <a name="next-steps"></a>Passos seguintes

Para obter mais ajuda na resolução de problemas, consulte estes recursos:

*  [Blog da Fábrica de Dados](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Pedidos de recursos da Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stack Overflow Forum para a Fábrica de Dados](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre a Data Factory](https://twitter.com/hashtag/DataFactory)

