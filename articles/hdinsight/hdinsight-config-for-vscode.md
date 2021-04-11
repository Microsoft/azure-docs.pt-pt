---
title: Referência de configuração Azure HDInsight
description: Introduza a configuração da extensão Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 04/07/2021
ms.custom: devx-track-python
ms.openlocfilehash: a9a444c2557ea17114123cbd5084cbbd9fe6dac6
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259998"
---
# <a name="azure-hdinsight-configuration-settings-reference"></a>Referência de configuração Azure HDInsight

A extensão de ferramentas de hive spark & para código de estúdio visual é altamente configurável. Esta página descreve as definições de chave com as que pode trabalhar.  

Para obter informações gerais sobre o trabalho com as definições no Código VS, consulte [as definições do Utilizador e do espaço de trabalho,](https://code.visualstudio.com/docs/getstarted/settings)e a [referência variáveis](https://code.visualstudio.com/docs/editor/variables-reference) para obter informações sobre suporte variável predefinido.

## <a name="open-the-azure-hdinsight-configuration"></a>Abra a configuração Azure HDInsight

1. Abra primeiro uma pasta para criar definições de espaço de trabalho.
2. Prima **Ctrl + Shift + P,** ou navegue para **Ver**  ->  **Paleta de Comando...** para mostrar todos os comandos.
3. **Configuração do conjunto de pesquisa**.
4. Expanda **as extensões** no diretório esquerdo e selecione **a configuração HDInsight**.

 ![hdi imagem config](./media/HDInsight-config-for-vscode/HDInsight-config-for-vscode.png)

## <a name="general-settings"></a>Definições gerais   

|  Propriedade   | Predefinição | Description   |
| ----- | ----- |----- |
| HDInsight: Ambiente Azure | Azure | Ambiente azul |
| HDInsight: Desativar a ligação de pesquisa aberta | Marcado | Ativar/Desativar o inquérito HDInsight de abertura |
| HDInsight: Ativar a instalação skip Pyspark | Desselecionado | Ativar/Desativar a instalação do pyspark |
| HDInsight: Dicas de login ativas | Desselecionado | Quando esta opção for verificada, haverá um aviso ao iniciar sessão em Azure |
| HDInsight: Versão de extensão anterior | Mostrar o número de versão da extensão atual | Mostrar a versão de extensão anterior|
| HDInsight: Resultados Fonte Família | -sistema de maçã,BlinkMacSystemFont,Segoe WPC,Segoe UI,HelveticaNeue-Light,Ubuntu,Droid Sans,sans-serif | Definir a família de fontes para a grelha de resultados; definido para em branco para usar a fonte editor |
| HDInsight: Tamanho do tipo de fonte de resultados | 13 |Definir o tamanho do tipo de letra para a cingição de resultados; definido para em branco para usar o tamanho do editor |
| HdInsight Cluster: Cluster Ligado | -- | Urls de clusters ligados. Também pode editar o ficheiro JSON para definir |
| Colmeia HDInsight: Aplicar localização | Desselecionado | [Opcional] Opções de configuração para localizar no local configurado do VSCode (deve reiniciar o VSCode para que as definições produzam efeitos)|
| Colmeia HDInsight: Copy Include Headers | Desselecionado | [Opcional] Opção de configuração para copiar resultados a partir da visualização de resultados |
| Colmeia HDInsight: Copiar remover nova linha | Marcado | [Opcional] Opções de configuração para copiar resultados de várias linhas a partir da Visualização de Resultados |
| HDInsight Hive › Formato: Alinhar definições de colunas em colunas | Desselecionado | Se a definição da coluna for alinhada |
| HDInsight Hive › Formato: Datatype Casing | nenhum | Se os tipos de dados forem formatados como MAI, minúsculas ou nenhuma (não formatadas) |
| HDInsight Hive › Formato: Chave palavras casing | nenhum | Se as palavras-chave forem formatadas como MAI, minúsculas ou nenhuma (não formatadas) |
| HDInsight Hive › Formato: Coloque as vírgulas antes da próxima declaração | Desselecionado | As vírgulas devem ser colocadas no início de cada declaração numa lista, por exemplo, "mycolumn2" em vez de no final 'mycolumn1',|
| HDInsight Hive › Formato: Coloque referências de declaração selecionadas em nova linha | Desselecionado | As referências a objetos numa declaração selecionada devem ser divididas em linhas separadas? Por exemplo, para 'SELECT C1, C2 FROM T1' tanto C1 como C2 estarão em linhas separadas
| Colmeia HDInsight: Informações de Debug de Log | Desselecionado | [Opcional] Log depurar a saída para a consola vs Code (Ferramentas de desenvolvimento de > ajuda) 
| Colmeia HDInsight: Mensagens padrão abertas | Marcado | Verdade para que o painel de mensagens seja aberto por defeito; falso para fechado|
| Colmeia HDInsight: Resultados Fonte Família | -sistema de maçã,BlinkMacSystemFont,Segoe WPC,Segoe UI,HelveticaNeue-Light,Ubuntu,Droid Sans,sans-serif | Definir a família de fontes para a grelha de resultados; definido para em branco para usar a fonte editor |
| Colmeia HDInsight: Tamanho do tipo de fonte de resultados | 13 | Definir o tamanho do tipo de letra para a grelha de resultados; definido para em branco para usar o tamanho do editor |
| HDInsight Hive › Save As Csv: Inclua cabeçalhos | Marcado | [Opcional] Quando verdadeiros, os cabeçalhos das colunas são incluídos ao guardar resultados como CSV |
| Colmeia HDInsight: Atalhos | -- | Atalhos relacionados com a janela de resultados |
| Colmeia HDInsight: Mostrar tempo do lote| Desselecionado | [Opcional] Se o tempo de execução for mostrado para lotes individuais |
| Colmeia HDInsight: Seleção de Pane Split | seguinte | [Opcional] Opções de configuração para as quais as novas colunas de resultados devem abrir em |
| Submissão de emprego hdInsight: Cluster Conf | -- | Configuração do Cluster |
| Submissão de emprego hdinsight: Livy Conf | -- | Configuração Livy. POST/Lotes |
| HDInsight Jupyter: Resultados do apêndice| Marcado | Se anexar os resultados à janela de resultados, senão claro e exibido. |
| HDInsight Jupyter: Línguas | -- | Definições predefinidos por idioma. |
| HDInsight Jupyter › Log: Verbose | Desselecionado | Se permitir a exploração de verbose |
| HDInsight Jupyter › Caderno: Startup Args | Pode adicionar artigo | Argumentos de linha de comando 'jupyter notebook'. Cada argumento é um item separado na matriz. Para uma lista completa, escreva 'jupyter notebook --help' numa janela terminal. |
| HDInsight Jupyter › Caderno: Pasta de arranque | ${workspaceRoot} |-- |
| HDInsight Jupyter: Extensão python ativada | Marcado | Utilize a Python-Interactive-Window da extensão ms-python ao apresentar trabalhos pySpark Interactive. Caso contrário, use a nossa própria janela jupyter |
| HDInsight Spark.NET: 7z | C:\Ficheiros do programa\7-Zip | <Caminho para a 7z.exe> |
| HDInsight Spark.NET: HADOOP_HOME | D:\winutils | <Caminho para bin\winutils.exe> janelas apenas OS |
| HDInsight Spark.NET: JAVA_HOME | C:\Ficheiros do programa\Java\jdk1.8.0_201\ | Caminho para a casa de Java|
| HDInsight Spark.NET: SCALA_HOME | C:\Ficheiros do programa (x86)\scala\ | Caminho para Scala Home |
| HDInsight Spark.NET: SPARK_HOME | D:\faísca-2.3.3-bin-hadoop2.7\ | Caminho para a Casa da Faísca |
| Colmeia: Persistência Dos separadores de resultados da consulta | Desselecionado | Hive PersistQueryResultTabs |
| Colmeia: Seleção de Pane Split | seguinte | [Opcional] Opções de configuração para as quais as novas colunas de resultados devem abrir em |
| Hive Interactive: Copy Executable Folder | Desselecionado | Se copiar a pasta de tempo de funcionação do serviço interativo da colmeia para a pasta tmp do utilizador |
| Hql Interactive Server: Porta de invólucro | 13424 | Porta de serviço interativo hive |
| Hql Language Server: Porta de invólucro de idioma | 12342 | Os servidores de porta de serviço de idiomas da colmeia ouvem. |
| Hql Language Server: Número máximo de problemas | 100 | Controla o número máximo de problemas produzidos pelo servidor. |
| Synapse Spark Compute: Synapse Spark Compute Azure Environment | em branco | sinapse Spark Compute Azure ambiente |
| Sinapse Spark pool Job Submission: Livy Conf | -- | Configuração Livy. POST/Lotes
| Submissão de emprego na piscina de faíscas: Synapse Spark Pool Cluster Conf | -- | Configuração da piscina de faíscas de sinapse |


## <a name="next-steps"></a>Passos seguintes

- Para obter informações sobre Azure HDInsight para VSCode, consulte [Spark & Hive for Visual Studio Code Tools](https://docs.microsoft.com/sql/big-data-cluster/spark-hive-tools-vscode).
- Para um vídeo que demonstre a utilização de Spark & Hive para Visual Studio Code, consulte [Spark & Hive for Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).