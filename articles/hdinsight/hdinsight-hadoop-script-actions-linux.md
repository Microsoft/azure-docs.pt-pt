---
title: Desenvolvimento de ação de script com o HDInsight baseado em Linux - Azure
description: Saiba como usar scripts de Bash para personalizar os clusters do HDInsight baseado em Linux. A funcionalidade de ação de script do HDInsight permite-lhe executar scripts durante ou após a criação do cluster. Scripts podem ser utilizados para alterar as definições de configuração de cluster ou instalar software adicional.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: 4bef785a08d7482468630bc690d6c19b05bdf770
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202967"
---
# <a name="script-action-development-with-hdinsight"></a>Desenvolvimento de ação de script com o HDInsight

Saiba como personalizar o seu cluster do HDInsight com scripts de Bash. Ações de script são uma forma de personalizar o HDInsight durante ou após a criação do cluster.

> [!IMPORTANT]  
> Os passos neste documento exigem um cluster do HDInsight que utilize o Linux. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a name="what-are-script-actions"></a>Quais são as ações de script

Ações de script são scripts de Bash Azure é executado em nós do cluster para fazer alterações de configuração ou de instalar o software. Uma ação de script é executada como raiz e fornece direitos de acesso total para os nós do cluster.

Ações de script podem ser aplicadas através dos seguintes métodos:

| Utilize este método para aplicar um script... | Durante a criação de cluster... | Num cluster em execução... |
| --- |:---:|:---:|
| Portal do Azure |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| CLI clássica do Azure |&nbsp; |✓ |
| SDK de .NET do HDInsight |✓ |✓ |
| Modelo do Azure Resource Manager |✓ |&nbsp; |

Para obter mais informações sobre como usar esses métodos para aplicar ações de script, consulte [HDInsight personalizar clusters com ações de script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="bestPracticeScripting"></a>Melhores práticas para o desenvolvimento de script

Ao desenvolver um script personalizado para um cluster do HDInsight, existem várias melhores práticas a ter em mente:

* [A versão do Apache Hadoop de destino](#bPS1)
* [A versão do SO de destino](#bps10)
* [Fornecer estável links para recursos de script](#bPS2)
* [Utilizar recursos previamente compilados](#bPS4)
* [Certifique-se de que o script de personalização do cluster é idempotente](#bPS3)
* [Certifique-se de elevada disponibilidade da arquitetura do cluster](#bPS5)
* [Configurar os componentes personalizados para utilizar o armazenamento de Blobs do Azure](#bPS6)
* [Escrever informações STDOUT e STDERR](#bPS7)
* [Guardar ficheiros como ASCII com fins de linha de LF](#bps8)
* [Utilizar a lógica de repetição para recuperar de erros transitórios](#bps9)

> [!IMPORTANT]  
> Ações de script devem ser concluído em 60 minutos, ou o processo de falha. Durante o aprovisionamento de nó, o script é executado simultaneamente com outros processos de instalação e configuração. Concorrência de recursos, tais como a largura de banda de CPU, rede ou de tempo pode fazer com que o script a demorar mais tempo a concluir do que o no seu ambiente de desenvolvimento.

### <a name="bPS1"></a>A versão do Apache Hadoop de destino

Versões diferentes do HDInsight têm versões diferentes de serviços do Hadoop e os componentes instalados. Se o script espera que uma versão específica de um serviço ou componente, só deve utilizar o script com a versão do HDInsight que inclui os componentes necessários. Pode encontrar informações sobre as versões de componente incluído com o HDInsight utilizando a [controlo de versões de componente de HDInsight](hdinsight-component-versioning.md) documento.

### <a name="bps10"></a> A versão do SO de destino

HDInsight baseado em Linux baseia-se a distribuição do Ubuntu Linux. Versões diferentes do HDInsight baseiam-se em diferentes versões do Ubuntu, que pode alterar o comportamento do seu script. Por exemplo, o HDInsight 3.4 e anterior baseiam-se em versões do Ubuntu que utilizam Upstart. As versões 3.5 e superiores são baseadas em Ubuntu 16.04, que usa Systemd. Systemd e Upstart dependem comandos diferentes, para que o seu script deve ser escrito para trabalhar com ambos.

Outra diferença importante entre o HDInsight 3.4 e 3.5 é que `JAVA_HOME` agora aponta para Java 8.

Pode verificar a versão do SO com `lsb_release`. O código a seguir demonstra como determinar se o script está em execução no Ubuntu 14 ou 16:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
...
if [[ $OS_VERSION == 16* ]]; then
    echo "Using systemd configuration"
    systemctl daemon-reload
    systemctl stop webwasb.service    
    systemctl start webwasb.service
else
    echo "Using upstart configuration"
    initctl reload-configuration
    stop webwasb
    start webwasb
fi
...
if [[ $OS_VERSION == 14* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
elif [[ $OS_VERSION == 16* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
fi
```

Pode encontrar o script completo que contém esses trechos de código em https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh.

Para a versão do Ubuntu, que é utilizado pelo HDInsight, consulte a [versão do componente de HDInsight](hdinsight-component-versioning.md) documento.

Para compreender as diferenças entre Systemd e Upstart, veja [Systemd para utilizadores Upstart](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="bPS2"></a>Fornecer estável links para recursos de script

O script e os recursos associados devem permanecer disponíveis durante o ciclo de vida do cluster. Estes recursos são necessários se são adicionados novos nós ao cluster durante operações de dimensionamento.

A melhor prática é baixar e arquivar tudo numa conta de armazenamento do Azure na sua subscrição.

> [!IMPORTANT]  
> A conta de armazenamento utilizada tem de ser a conta de armazenamento predefinida para o cluster ou um contentor público, só de leitura em qualquer outra conta de armazenamento.

Por exemplo, os exemplos fornecidos pela Microsoft são armazenados no [ https://hdiconfigactions.blob.core.windows.net/ ](https://hdiconfigactions.blob.core.windows.net/) conta de armazenamento. Esta localização é um contentor de público, só de leitura mantido pela equipa do HDInsight.

### <a name="bPS4"></a>Utilizar recursos previamente compilados

Para reduzir o tempo que demora a executar o script, evite operações que compilam recursos a partir de código-fonte. Por exemplo, a pré-compile recursos e armazená-los num blob da conta de armazenamento do Azure no mesmo centro de dados do HDInsight.

### <a name="bPS3"></a>Certifique-se de que o script de personalização do cluster é idempotente

Scripts devem ser idempotentes. Se o script é executado várias vezes, deverá devolver o cluster para o mesmo Estado cada vez.

Por exemplo, um script que modifica os ficheiros de configuração não deve adicionar entradas duplicadas se foi executada várias vezes.

### <a name="bPS5"></a>Certifique-se de elevada disponibilidade da arquitetura do cluster

Os clusters do HDInsight baseado em Linux fornecem dois nós principais que estão ativos dentro do cluster e as ações de script execute em ambos os nós. Se os componentes que instalar esperam apenas um nó principal, não instale os componentes em ambos os nós principais.

> [!IMPORTANT]  
> Serviços fornecidos como parte do HDInsight são concebidos para a ativação pós-falha entre os dois nós principais, conforme necessário. Esta funcionalidade não é expandida para componentes personalizados instalados por meio de ações de script. Se precisar de elevada disponibilidade para componentes personalizados, deve implementar seu próprio mecanismo de ativação pós-falha.

### <a name="bPS6"></a>Configurar os componentes personalizados para utilizar o armazenamento de Blobs do Azure

Componentes que instalar no cluster podem ter uma configuração predefinida, que utiliza o armazenamento do Apache Hadoop Distributed File System (HDFS). HDInsight utiliza o armazenamento do Azure ou o armazenamento do Data Lake como armazenamento predefinido. Ambos fornecem um sistema de ficheiros compatível do HDFS que mantém os dados, mesmo que o cluster é eliminado. Terá de configurar os componentes que instala para utilizar WASB ou do ADL em vez do HDFS.

Na maioria das operações, não é necessário especificar o sistema de ficheiros. Por exemplo, o seguinte copia o ficheiro de giraph-examples do sistema de arquivos local para o armazenamento de cluster:

```bash
hdfs dfs -put /usr/hdp/current/giraph/giraph-examples.jar /example/jars/
```

Neste exemplo, o `hdfs` comando usa de modo transparente o armazenamento de cluster predefinido. Para algumas operações, terá de especificar o URI. Por exemplo, `adl:///example/jars` para Gen1 de armazenamento do Azure Data Lake, `abfs:///example/jars` para a geração 2 de armazenamento do Data Lake ou `wasb:///example/jars` do armazenamento do Azure.

### <a name="bPS7"></a>Escrever informações STDOUT e STDERR

HDInsight regista a saída de script que é escrita para STDOUT e STDERR. Pode ver estas informações com a IU da web de Ambari.

> [!NOTE]  
> Apache Ambari só está disponível se o cluster é criado com êxito. Se utilizar uma ação de script durante a criação do cluster e a criação falhar, consulte a secção de resolução de problemas [HDInsight personalizar clusters com ação de script](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting) para outras formas de aceder a informações com sessão iniciada.

A maioria dos utilitários e pacotes de instalação já gravar informações para STDOUT e STDERR, no entanto, pode querer adicionar registo adicional. Para enviar texto para STDOUT, utilize `echo`. Por exemplo:

```bash
echo "Getting ready to install Foo"
```

Por predefinição, `echo` envia a cadeia de caracteres para STDOUT. Para direcioná-lo para STDERR, adicione `>&2` antes de `echo`. Por exemplo:

```bash
>&2 echo "An error occurred installing Foo"
```

Isso redireciona informações gravadas em vez disso, para STDOUT para STDERR (2). Para obter mais informações sobre redirecionamento de e/s, consulte [ https://www.tldp.org/LDP/abs/html/io-redirection.html ](https://www.tldp.org/LDP/abs/html/io-redirection.html).

Para obter mais informações sobre a visualização de informações registadas pelo ações de script, consulte [HDInsight personalizar clusters com ação de script](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

### <a name="bps8"></a> Guardar ficheiros como ASCII com fins de linha de LF

Scripts de bash devem ser armazenados em formato ASCII, com linhas terminadas por LF. Ficheiros são armazenados como UTF-8, ou a utilizam CRLF como o fim da linha poderão falhar com o seguinte erro:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="bps9"></a> Utilizar a lógica de repetição para recuperar de erros transitórios

Quando o download de arquivos, instalar pacotes com apt-get, ou outras ações que transmitem dados através da internet, a ação poderá falhar devido a erros de sistema de rede transitórios. Por exemplo, o recurso remoto que estão a comunicar com pode ser no processo de ativação pós-falha para um nó de cópia de segurança.

Para tornar o seu script resiliente a erros transitórios, pode implementar a lógica de repetição. A função a seguir demonstra como implementar a lógica de repetição. Tentar novamente a operação de três vezes antes de falhar.

```bash
#retry
MAXATTEMPTS=3

retry() {
    local -r CMD="$@"
    local -i ATTMEPTNUM=1
    local -i RETRYINTERVAL=2

    until $CMD
    do
        if (( ATTMEPTNUM == MAXATTEMPTS ))
        then
                echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                return 1
        else
                echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                sleep $(( RETRYINTERVAL ))
                ATTMEPTNUM=$ATTMEPTNUM+1
        fi
    done
}
```

Os exemplos seguintes demonstram como utilizar esta função.

```bash
retry ls -ltr foo

retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
```

## <a name="helpermethods"></a>Métodos auxiliares para scripts personalizados

Métodos de programa auxiliar de ação de script são utilitários que podem ser utilizados ao escrever scripts personalizados. Esses métodos estão contidos na [ https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh ](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) script. Utilize o seguinte para transferir e utilizá-los como parte do seu script:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

Os seguintes programas auxiliares disponíveis para utilização no seu script:

| Utilização do programa auxiliar | Descrição |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Transfere um ficheiro a partir da origem de URI para o caminho de ficheiro especificado. Por padrão, ele não substitui um ficheiro existente. |
| `untar_file TARFILE DESTDIR` |Extrai um ficheiro de destino (usando `-xf`) para o diretório de destino. |
| `test_is_headnode` |Se foi executado num nó principal do cluster, return é 1; caso contrário, 0. |
| `test_is_datanode` |Se o nó atual for um nó de dados (trabalho), devolver um 1; caso contrário, 0. |
| `test_is_first_datanode` |Se o nó atual é os primeiro (trabalho) de dados (workernode0 nomeado) do nó devolver um 1; caso contrário, 0. |
| `get_headnodes` |Devolva o nome de domínio completamente qualificado de nós principais do cluster. Os nomes são delimitados por vírgulas. Uma cadeia vazia, é devolvida com o erro. |
| `get_primary_headnode` |Obtém o nome de domínio completamente qualificado do nó principal primário. Uma cadeia vazia, é devolvida com o erro. |
| `get_secondary_headnode` |Obtém o nome de domínio completamente qualificado do nó principal secundário. Uma cadeia vazia, é devolvida com o erro. |
| `get_primary_headnode_number` |Obtém o sufixo numérico de nó principal primário. Uma cadeia vazia, é devolvida com o erro. |
| `get_secondary_headnode_number` |Obtém o sufixo numérico de nó principal secundário. Uma cadeia vazia, é devolvida com o erro. |

## <a name="commonusage"></a>Padrões de utilização comuns

Esta seção fornece diretrizes sobre implementação de alguns dos padrões de utilização comuns que poderá encontrar ao escrever seu próprio script personalizado.

### <a name="passing-parameters-to-a-script"></a>Passando parâmetros para um script

Em alguns casos, o script pode exigir parâmetros. Por exemplo, poderá ter a palavra-passe de administrador para o cluster ao utilizar a API de REST do Ambari.

Parâmetros transmitidos para o script são conhecidos como *parâmetros posicionais*e são atribuídos às `$1` para o primeiro parâmetro, `$2` para o segundo e, portanto,-na. `$0` contém o nome do próprio script.

Valores transmitidos para o script como parâmetros devem ser colocadas entre aspas ('). Isso faz com que o valor transmitido é tratado como um literal.

### <a name="setting-environment-variables"></a>Definir variáveis de ambiente

Definir uma variável de ambiente é executada pela instrução seguinte:

    VARIABLENAME=value

Onde VARIABLENAME é o nome da variável. Para aceder à variável, utilize `$VARIABLENAME`. Por exemplo, para atribuir um valor fornecido por um parâmetro posicional, como uma palavra-passe com o nome de variável de ambiente, usaria a seguinte instrução:

    PASSWORD=$1

Posterior acesso às informações, em seguida, poderia usar `$PASSWORD`.

Variáveis de ambiente definidas dentro do script só existem no âmbito do script. Em alguns casos, terá de adicionar variáveis de ambiente de todo o sistema que serão mantidas depois de concluído o script. Para adicionar variáveis de ambiente de todo o sistema, adicionar a variável `/etc/environment`. Por exemplo, a seguinte instrução adiciona `HADOOP_CONF_DIR`:

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Acesso a locais onde são armazenados os scripts personalizados

Scripts utilizados para personalizar um cluster tem de ser armazenados em uma das seguintes localizações:

* Uma __conta de armazenamento do Azure__ que está associado ao cluster.

* Uma __conta de armazenamento adicional__ associada ao cluster.

* R __URI lido publicamente__. Por exemplo, um URL para dados armazenados no OneDrive, Dropbox ou outros ficheiros que aloja o serviço.

* Uma __conta de armazenamento do Azure Data Lake__ que está associado ao HDInsight cluster. Para obter mais informações sobre como utilizar o armazenamento do Azure Data Lake com o HDInsight, consulte [início rápido: Configurar clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

    > [!NOTE]  
    > O principal de serviço que HDInsight utiliza para aceder ao armazenamento do Data Lake tem de ter acesso de leitura para o script.

Recursos utilizados pelo script também tem de estar disponíveis publicamente.

Armazenar os ficheiros de uma conta de armazenamento do Azure ou o armazenamento do Azure Data Lake fornece um acesso rápido, como ambas dentro da rede do Azure.

> [!NOTE]  
> O formato de URI utilizado para referenciar o script difere consoante o serviço que está a ser utilizado. Para contas de armazenamento associadas ao cluster do HDInsight, utilize `wasb://` ou `wasbs://`. Para URIs lido publicamente, utilize `http://` ou `https://`. Para o armazenamento do Data Lake, utilize `adl://`.

### <a name="checking-the-operating-system-version"></a>A verificar a versão do sistema operativo

Versões diferentes do HDInsight baseiam-se em versões específicas do Ubuntu. Pode haver diferenças entre as versões de SO que deve verificar se há no seu script. Por exemplo, terá de instalar um binário que está associado à versão do Ubuntu.

Para verificar a versão do SO, utilize `lsb_release`. Por exemplo, o script a seguir demonstra como fazer referência a um ficheiro de destino específico dependendo da versão de SO:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
```

## <a name="deployScript"></a>Lista de verificação para implementar uma ação de script

Seguem-se a realizar passos ao se preparar para implementar um script:

* Coloque os ficheiros que contêm os scripts personalizados num local que seja acessível a nós do cluster durante a implementação. Por exemplo, o armazenamento predefinido para o cluster. Arquivos também podem ser armazenados nos serviços de alojamento disponível publicamente.
* Certifique-se de que o script é idempotent. Isso faz com que o script a ser executado várias vezes no mesmo nó.
* Utilize um /tmp de diretório do ficheiro temporário para manter os ficheiros transferidos usados pelos scripts e, em seguida, limpá-las após tem executado scripts.
* Se as definições de nível de sistema operacional ou ficheiros de configuração de serviço do Hadoop são alterados, pode querer reinicie os serviços do HDInsight.

## <a name="runScriptAction"></a>Como executar uma ação de script

Pode utilizar ações de script para personalizar os clusters do HDInsight através dos seguintes métodos:

* Portal do Azure
* Azure PowerShell
* Modelos do Azure Resource Manager
* O SDK de .NET do HDInsight.

Para obter mais informações sobre como utilizar cada método, consulte [como utilizar a ação de script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="sampleScripts"></a>Exemplos de script personalizado

A Microsoft fornece os scripts de exemplo para instalar os componentes num cluster do HDInsight. Veja as ligações seguintes para obter mais ações de script de exemplo.

* [Instalar e utilizar Hue em clusters do HDInsight](hdinsight-hadoop-hue-linux.md)
* [Instalar e usar o Apache Giraph nos clusters do HDInsight](hdinsight-hadoop-giraph-install-linux.md)
* [Instalar ou atualizar o Mono em clusters do HDInsight](hdinsight-hadoop-install-mono.md)

## <a name="troubleshooting"></a>Resolução de problemas

Seguem-se erros que poderá encontrar ao utilizar scripts desenvolvidos:

**Erro**: `$'\r': command not found`. Por vezes, seguido pelo `syntax error: unexpected end of file`.

*Motivo*: Este erro é causado quando as linhas num script terminam com CRLF. Sistemas UNIX esperam LF apenas como o fim da linha.

Este problema ocorre normalmente quando o script foi criado num ambiente Windows, como CRLF é uma linha comum final para os vários editores de texto no Windows.

*Resolução*: Se for uma opção no seu editor de texto, selecione o formato de Unix ou LF para o fim da linha. Também pode utilizar os seguintes comandos num sistema Unix para alterar o CRLF para um LF:

> [!NOTE]  
> Os comandos seguintes são quase equivalentes em que eles devem ser alterado as fins de linha CRLF para LF. Selecione um com base em utilitários disponíveis no seu sistema.

| Comando | Notas |
| --- | --- |
| `unix2dos -b INFILE` |O ficheiro original é uma cópia de segurança com um. Extensão BAK |
| `tr -d '\r' < INFILE > OUTFILE` |OUTFILE contém uma versão com fins de LF apenas |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Modifica o ficheiro diretamente |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |OUTFILE contém uma versão com apenas os fins de LF. |

**Erro**: `line 1: #!/usr/bin/env: No such file or directory`.

*Motivo*: Este erro ocorre quando o script foi guardado como UTF-8 com uma marca de ordem do Byte (BOM).

*Resolução*: Guarde o ficheiro como ASCII ou UTF-8, sem uma BOM. Também pode usar o seguinte comando num sistema Linux ou Unix para criar um ficheiro sem a BOM:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Substitua `INFILE` com o ficheiro que contém a BOM. `OUTFILE` deve ser um novo nome de ficheiro, que contém o script sem a BOM.

## <a name="seeAlso"></a>Passos seguintes

* Saiba como [HDInsight personalizar clusters com ação de script](hdinsight-hadoop-customize-cluster-linux.md)
* Utilize o [referência do SDK de .NET do HDInsight](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight) para saber mais sobre a criação de aplicações de .NET que gerem o HDInsight
* Utilizar o [API de REST do HDInsight](https://msdn.microsoft.com/library/azure/mt622197.aspx) para aprender a utilizar o REST para executar ações de gestão em clusters do HDInsight.
