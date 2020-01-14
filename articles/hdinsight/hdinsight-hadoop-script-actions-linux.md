---
title: Desenvolver ações de script para personalizar os clusters do Azure HDInsight
description: Saiba como usar scripts bash para personalizar os clusters HDInsight. As ações de script permitem executar scripts durante ou após a criação do cluster para alterar as definições de configuração de cluster ou instalar software adicional.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/28/2019
ms.openlocfilehash: ad9b4b69b0be34c89d03b677c1889e486aae0379
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2020
ms.locfileid: "75931700"
---
# <a name="script-action-development-with-hdinsight"></a>Desenvolvimento de ação de script com o HDInsight

Saiba como personalizar o cluster HDInsight usando scripts bash. As ações de script são uma maneira de personalizar o HDInsight durante ou após a criação do cluster.

## <a name="what-are-script-actions"></a>O que são ações de script

As ações de script são scripts bash que o Azure executa nos nós de cluster para fazer alterações de configuração ou instalar software. Uma ação de script é executada como raiz e fornece direitos de acesso completo aos nós de cluster.

As ações de script podem ser aplicadas por meio dos seguintes métodos:

| Use este método para aplicar um script... | Durante a criação do cluster... | Em um cluster em execução... |
| --- |:---:|:---:|
| Portal do Azure |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| CLI Clássica do Azure |&nbsp; |✓ |
| SDK de .NET do HDInsight |✓ |✓ |
| Modelo do Azure Resource Manager |✓ |&nbsp; |

Para obter mais informações sobre como usar esses métodos para aplicar as ações de script, consulte [Personalizar clusters HDInsight usando ações de script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="bestPracticeScripting"></a>Práticas recomendadas para o desenvolvimento de scripts

Quando você desenvolve um script personalizado para um cluster HDInsight, há várias práticas recomendadas para ter em mente:

* [Direcionar a versão de Apache Hadoop](#bPS1)
* [Direcionar a versão do so](#bps10)
* [Fornecer links estáveis para recursos de script](#bPS2)
* [Usar recursos pré-compilados](#bPS4)
* [Verifique se o script de personalização do cluster é idempotente](#bPS3)
* [Garantir a alta disponibilidade da arquitetura do cluster](#bPS5)
* [Configurar os componentes personalizados para usar o armazenamento de BLOBs do Azure](#bPS6)
* [Gravar informações em STDOUT e STDERR](#bPS7)
* [Salvar arquivos como ASCII com terminações de linha de LF](#bps8)
* [Use a lógica de repetição para se recuperar de erros transitórios](#bps9)

> [!IMPORTANT]  
> As ações de script devem ser concluídas em até 60 minutos ou o processo falhará. Durante o provisionamento de nó, o script é executado simultaneamente com outros processos de instalação e configuração. A competição por recursos como tempo de CPU ou largura de banda de rede pode fazer com que o script demore mais para ser concluído do que em seu ambiente de desenvolvimento.

### <a name="bPS1"></a>Direcionar a versão de Apache Hadoop

Versões diferentes do HDInsight têm versões diferentes dos serviços e componentes do Hadoop instalados. Se o seu script espera uma versão específica de um serviço ou componente, você só deve usar o script com a versão do HDInsight que inclui os componentes necessários. Você pode encontrar informações sobre versões de componentes incluídas com o HDInsight usando o documento de [controle de versão de componente do hdinsight](hdinsight-component-versioning.md) .

### <a name="checking-the-operating-system-version"></a>Verificando a versão do sistema operacional

Versões diferentes do HDInsight dependem de versões específicas do Ubuntu. Pode haver diferenças entre as versões do sistema operacional que você deve verificar em seu script. Por exemplo, talvez seja necessário instalar um binário que esteja vinculado à versão do Ubuntu.

Para verificar a versão do sistema operacional, use `lsb_release`. Por exemplo, o script a seguir demonstra como fazer referência a um arquivo tar específico dependendo da versão do sistema operacional:

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

### <a name="bps10"></a>Direcionar a versão do sistema operacional

O HDInsight é baseado na distribuição de Ubuntu Linux. Versões diferentes do HDInsight dependem de versões diferentes do Ubuntu, que podem alterar a forma como o seu script se comporta. Por exemplo, o HDInsight 3,4 e versões anteriores baseiam-se na versão do Ubuntu que usam o Upstart. As versões 3,5 e posteriores se baseiam no Ubuntu 16, 4, que usa o sistema. O sistema e o Upstart dependem de comandos diferentes, de modo que o script deve ser escrito para funcionar com ambos.

Outra diferença importante entre o HDInsight 3,4 e o 3,5 é que `JAVA_HOME` agora aponta para o Java 8. O código a seguir demonstra como determinar se o script está em execução no Ubuntu 14 ou 16:

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

Você pode encontrar o script completo que contém esses trechos de código em https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh.

Para a versão do Ubuntu que é usada pelo HDInsight, consulte o documento [versão do componente HDInsight](hdinsight-component-versioning.md) .

Para entender as diferenças entre o sistema e o Upstart, consulte [sistemad para usuários do Upstart](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="bPS2"></a>Fornecer links estáveis para recursos de script

O script e os recursos associados devem permanecer disponíveis durante o tempo de vida do cluster. Esses recursos serão necessários se novos nós forem adicionados ao cluster durante operações de dimensionamento.

A prática recomendada é baixar e arquivar tudo em uma conta de armazenamento do Azure em sua assinatura.

> [!IMPORTANT]  
> A conta de armazenamento usada deve ser a conta de armazenamento padrão para o cluster ou um contêiner público somente leitura em qualquer outra conta de armazenamento.

Por exemplo, os exemplos fornecidos pela Microsoft são armazenados na conta de armazenamento [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) . Esse local é um contêiner público e somente leitura mantido pela equipe do HDInsight.

### <a name="bPS4"></a>Usar recursos pré-compilados

Para reduzir o tempo necessário para executar o script, evite operações que compilem recursos do código-fonte. Por exemplo, pré-compile recursos e armazene-os em um blob da conta de armazenamento do Azure na mesma data center que o HDInsight.

### <a name="bPS3"></a>Verifique se o script de personalização do cluster é idempotente

Os scripts devem ser idempotentes. Se o script for executado várias vezes, ele deverá retornar o cluster para o mesmo estado a cada vez.

Por exemplo, um script que modifica os arquivos de configuração não deve adicionar entradas duplicadas, se executado várias vezes.

### <a name="bPS5"></a>Garantir a alta disponibilidade da arquitetura do cluster

Os clusters HDInsight baseados em Linux fornecem dois nós de cabeçalho que estão ativos no cluster e as ações de script são executadas em ambos os nós. Se os componentes que você instalar esperam apenas um nó de cabeçalho, não instale os componentes em ambos os nós de cabeçalho.

> [!IMPORTANT]  
> Os serviços fornecidos como parte do HDInsight são projetados para fazer failover entre os dois nós de cabeçalho, conforme necessário. Essa funcionalidade não é estendida para componentes personalizados instalados por meio de ações de script. Se precisar de alta disponibilidade para componentes personalizados, você deverá implementar seu próprio mecanismo de failover.

### <a name="bPS6"></a>Configurar os componentes personalizados para usar o armazenamento de BLOBs do Azure

Os componentes que você instala no cluster podem ter uma configuração padrão que usa o armazenamento Apache Hadoop Sistema de Arquivos Distribuído (HDFS). O HDInsight usa o armazenamento do Azure ou Data Lake Storage como o armazenamento padrão. Ambos fornecem um sistema de arquivos compatível com HDFS que persiste dados mesmo se o cluster for excluído. Talvez seja necessário configurar os componentes que você instala para usar WASB ou ADL em vez de HDFS.

Para a maioria das operações, você não precisa especificar o sistema de arquivos. Por exemplo, o seguinte copia o arquivo Hadoop-Common. jar do sistema de arquivos local para o armazenamento de cluster:

```bash
hdfs dfs -put /usr/hdp/current/hadoop-client/hadoop-common.jar /example/jars/
```

Neste exemplo, o comando `hdfs` usa de forma transparente o armazenamento de cluster padrão. Para algumas operações, talvez seja necessário especificar o URI. Por exemplo, `adl:///example/jars` para Azure Data Lake Storage Gen1, `abfs:///example/jars` para Data Lake Storage Gen2 ou `wasb:///example/jars` para o armazenamento do Azure.

### <a name="bPS7"></a>Gravar informações em STDOUT e STDERR

O HDInsight registra a saída de script que é gravada em STDOUT e STDERR. Você pode exibir essas informações usando a interface do usuário da Web do amAmbari.

> [!NOTE]  
> O Apache Ambari só estará disponível se o cluster for criado com êxito. Se você usar uma ação de script durante a criação do cluster e a criação falhar, consulte a seção solução de problemas [Personalizar clusters HDInsight usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting) para obter outras maneiras de acessar informações registradas.

A maioria dos pacotes de instalação e utilitários já grava informações para STDOUT e STDERR, no entanto, talvez você queira adicionar logs adicionais. Para enviar texto para STDOUT, use `echo`. Por exemplo:

```bash
echo "Getting ready to install Foo"
```

Por padrão, `echo` envia a cadeia de caracteres para STDOUT. Para direcioná-lo para STDERR, adicione `>&2` antes de `echo`. Por exemplo:

```bash
>&2 echo "An error occurred installing Foo"
```

Isso redireciona as informações gravadas em STDOUT para STDERR (2) em vez disso. Para obter mais informações sobre o redirecionamento de e/s, consulte [https://www.tldp.org/LDP/abs/html/io-redirection.html](https://www.tldp.org/LDP/abs/html/io-redirection.html).

Para obter mais informações sobre como exibir informações registradas em log por ações de script, consulte [Personalizar clusters HDInsight usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

### <a name="bps8"></a>Salvar arquivos como ASCII com terminações de linha de LF

Os scripts bash devem ser armazenados como formato ASCII, com linhas terminadas por LF. Os arquivos que são armazenados como UTF-8 ou usam CRLF como a terminação de linha podem falhar com o seguinte erro:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="bps9"></a>Use a lógica de repetição para se recuperar de erros transitórios

Ao baixar arquivos, instalar pacotes usando apt-get ou outras ações que transmitem dados pela Internet, a ação pode falhar devido a erros transitórios de rede. Por exemplo, o recurso remoto com o qual você está se comunicando pode estar no processo de failover para um nó de backup.

Para tornar seu script resiliente a erros transitórios, você pode implementar a lógica de repetição. A função a seguir demonstra como implementar a lógica de repetição. Ele tenta novamente a operação três vezes antes de falhar.

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

Os exemplos a seguir demonstram como usar essa função.

```bash
retry ls -ltr foo

retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
```

## <a name="helpermethods"></a>Métodos auxiliares para scripts personalizados

Os métodos auxiliares de ação de script são utilitários que você pode usar ao escrever scripts personalizados. Esses métodos estão contidos no script [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) . Use o seguinte para baixar e usá-los como parte do seu script:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

Os seguintes auxiliares estão disponíveis para uso no seu script:

| Uso do auxiliar | Descrição |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Baixa um arquivo do URI de origem para o caminho de arquivo especificado. Por padrão, ele não substitui um arquivo existente. |
| `untar_file TARFILE DESTDIR` |Extrai um arquivo tar (usando `-xf`) para o diretório de destino. |
| `test_is_headnode` |Se executado em um nó de cabeçalho do cluster, retorne 1; caso contrário, 0. |
| `test_is_datanode` |Se o nó atual for um nó de dados (trabalho), retorne um 1; caso contrário, 0. |
| `test_is_first_datanode` |Se o nó atual for o primeiro nó de dados (trabalhador) (chamado workernode0), retornará 1; caso contrário, 0. |
| `get_headnodes` |Retorne o nome de domínio totalmente qualificado do cabeçalho no cluster. Os nomes são delimitados por vírgula. Uma cadeia de caracteres vazia é retornada com erro. |
| `get_primary_headnode` |Obtém o nome de domínio totalmente qualificado do cabeçalho primário. Uma cadeia de caracteres vazia é retornada com erro. |
| `get_secondary_headnode` |Obtém o nome de domínio totalmente qualificado do cabeçalho secundário. Uma cadeia de caracteres vazia é retornada com erro. |
| `get_primary_headnode_number` |Obtém o sufixo numérico do cabeçalho primário. Uma cadeia de caracteres vazia é retornada com erro. |
| `get_secondary_headnode_number` |Obtém o sufixo numérico do cabeçalho secundário. Uma cadeia de caracteres vazia é retornada com erro. |

## <a name="commonusage"></a>Padrões de uso comuns

Esta seção fornece orientação sobre como implementar alguns dos padrões de uso comuns que você pode encontrar ao escrever seu próprio script personalizado.

### <a name="passing-parameters-to-a-script"></a>Passando parâmetros para um script

Em alguns casos, seu script pode exigir parâmetros. Por exemplo, você pode precisar da senha de administrador para o cluster ao usar a API REST do Ambari.

Os parâmetros passados para o script são conhecidos como *parâmetros posicionais*e são atribuídos a `$1` para o primeiro parâmetro, `$2` para o segundo e assim por diante. `$0` contém o nome do script em si.

Os valores passados para o script como parâmetros devem ser colocados entre aspas simples ('). Isso garante que o valor transmitido seja tratado como um literal.

### <a name="setting-environment-variables"></a>Definindo variáveis de ambiente

A definição de uma variável de ambiente é executada pela seguinte instrução:

    VARIABLENAME=value

Em que VARIABLEname é o nome da variável. Para acessar a variável, use `$VARIABLENAME`. Por exemplo, para atribuir um valor fornecido por um parâmetro posicional como uma variável de ambiente chamada PASSWORD, você usaria a seguinte instrução:

    PASSWORD=$1

O acesso subsequente às informações pode, então, usar `$PASSWORD`.

As variáveis de ambiente definidas no script só existem dentro do escopo do script. Em alguns casos, talvez seja necessário adicionar variáveis de ambiente de todo o sistema que persistirão após a conclusão do script. Para adicionar variáveis de ambiente de todo o sistema, adicione a variável a `/etc/environment`. Por exemplo, a instrução a seguir adiciona `HADOOP_CONF_DIR`:

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Acesso a locais onde os scripts personalizados são armazenados

Os scripts usados para personalizar um cluster precisam ser armazenados em um dos seguintes locais:

* Uma __conta de armazenamento do Azure__ associada ao cluster.

* Uma __conta de armazenamento adicional__ associada ao cluster.

* Um __URI legível publicamente__. Por exemplo, uma URL para dados armazenados no OneDrive, Dropbox ou outro serviço de Hospedagem de arquivo.

* Uma __conta de Azure data Lake Storage__ associada ao cluster HDInsight. Para obter mais informações sobre como usar Azure Data Lake Storage com o HDInsight, consulte [início rápido: configurar clusters no hdinsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

    > [!NOTE]  
    > A entidade de serviço que o HDInsight usa para acessar Data Lake Storage deve ter acesso de leitura ao script.

Os recursos usados pelo script também devem estar publicamente disponíveis.

Armazenar os arquivos em uma conta de armazenamento do Azure ou Azure Data Lake Storage fornece acesso rápido, como na rede do Azure.

> [!NOTE]  
> O formato de URI usado para referenciar o script difere dependendo do serviço que está sendo usado. Para contas de armazenamento associadas ao cluster HDInsight, use `wasb://` ou `wasbs://`. Para URIs publicamente legíveis, use `http://` ou `https://`. Para Data Lake Storage, use `adl://`.

## <a name="deployScript"></a>Lista de verificação para implantar uma ação de script

Estas são as etapas necessárias para se preparar para implantar um script:

* Coloque os arquivos que contêm os scripts personalizados em um local que possa ser acessado pelos nós de cluster durante a implantação. Por exemplo, o armazenamento padrão para o cluster. Os arquivos também podem ser armazenados em serviços de hospedagem legíveis publicamente.
* Verifique se o script é idempotente. Isso permite que o script seja executado várias vezes no mesmo nó.
* Use um diretório de arquivo temporário/tmp para manter os arquivos baixados usados pelos scripts e, em seguida, limpá-los depois que os scripts tiverem sido executados.
* Se as configurações no nível do sistema operacional ou os arquivos de configuração do serviço Hadoop forem alterados, talvez você queira reiniciar os serviços do HDInsight.

## <a name="runScriptAction"></a>Como executar uma ação de script

Você pode usar ações de script para personalizar os clusters HDInsight usando os seguintes métodos:

* Portal do Azure
* Azure PowerShell
* Modelos do Azure Resource Manager
* O SDK do .NET do HDInsight.

Para obter mais informações sobre como usar cada método, consulte [como usar a ação de script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="sampleScripts"></a>Exemplos de script personalizado

A Microsoft fornece scripts de exemplo para instalar componentes em um cluster HDInsight. Consulte [instalar e usar o matiz em clusters HDInsight](hdinsight-hadoop-hue-linux.md) como uma ação de script de exemplo.

## <a name="troubleshooting"></a>Resolução de problemas

Estes são os erros que podem surgir ao usar os scripts que você desenvolveu:

**Erro**: `$'\r': command not found`. Às vezes, seguido por `syntax error: unexpected end of file`.

*Causa*: esse erro é causado quando as linhas em um script terminam com CRLF. Os sistemas UNIX esperam apenas LF como terminação de linha.

Esse problema ocorre com mais frequência quando o script é criado em um ambiente do Windows, pois CRLF é uma terminação de linha comum para muitos editores de texto no Windows.

*Resolução*: se for uma opção em seu editor de texto, selecione formato UNIX ou LF para a terminação de linha. Você também pode usar os seguintes comandos em um sistema UNIX para alterar o CRLF para um LF:

> [!NOTE]  
> Os comandos a seguir são aproximadamente equivalentes, pois eles devem alterar as terminações de linha CRLF para LF. Selecione uma com base nos utilitários disponíveis no seu sistema.

| Comando | Notas |
| --- | --- |
| `unix2dos -b INFILE` |O backup do arquivo original é feito com um. Extensão BAK |
| `tr -d '\r' < INFILE > OUTFILE` |Outfile contém uma versão somente com terminações LF |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Modifica o arquivo diretamente |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |Outfile contém uma versão somente com terminações LF. |

**Erro**: `line 1: #!/usr/bin/env: No such file or directory`.

*Causa*: esse erro ocorre quando o script foi salvo como UTF-8 com uma marca de ordem de byte (bom).

*Resolução*: Salve o arquivo como ASCII ou UTF-8 sem uma bom. Você também pode usar o seguinte comando em um sistema Linux ou UNIX para criar um arquivo sem a BOM:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Substitua `INFILE` pelo arquivo que contém a BOM. `OUTFILE` deve ser um novo nome de arquivo, que contém o script sem a BOM.

## <a name="seeAlso"></a>Passos seguintes

* Saiba como [Personalizar os clusters HDInsight usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md)
* Use a [referência do SDK do .net do HDInsight](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight) para saber mais sobre como criar aplicativos .NET que gerenciam o HDInsight
* Use a [API REST do HDInsight](https://msdn.microsoft.com/library/azure/mt622197.aspx) para aprender a usar o REST para executar ações de gerenciamento em clusters HDInsight.
