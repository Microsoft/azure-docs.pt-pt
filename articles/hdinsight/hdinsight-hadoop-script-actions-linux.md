---
title: Desenvolver ações de script para personalizar clusters Azure HDInsight
description: Aprenda a usar scripts Bash para personalizar clusters HDInsight. As ações do script permitem-lhe executar scripts durante ou após a criação do cluster para alterar as definições de configuração do cluster ou instalar software adicional.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/28/2019
ms.openlocfilehash: ad9b4b69b0be34c89d03b677c1889e486aae0379
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931700"
---
# <a name="script-action-development-with-hdinsight"></a>Desenvolvimento de ação de script com HDInsight

Aprenda a personalizar o seu cluster HDInsight usando scripts Bash. As ações do script são uma forma de personalizar o HDInsight durante ou após a criação do cluster.

## <a name="what-are-script-actions"></a>O que são ações de script

As ações do script são scripts Bash que o Azure executa nos nós do cluster para fazer alterações de configuração ou instalar software. Uma ação de script é executada como raiz, e fornece direitos de acesso completos aos nós do cluster.

As ações do script podem ser aplicadas através dos seguintes métodos:

| Use este método para aplicar um script... | Durante a criação de cluster... | Num aglomerado de corridas... |
| --- |:---:|:---:|
| Portal do Azure |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| CLI Clássica do Azure |&nbsp; |✓ |
| HDInsight .NET SDK |✓ |✓ |
| Modelo do Azure Resource Manager |✓ |&nbsp; |

Para obter mais informações sobre a utilização destes métodos para aplicar ações de script, consulte [customize os clusters HDInsight usando ações](hdinsight-hadoop-customize-cluster-linux.md)de script .

## <a name="best-practices-for-script-development"></a><a name="bestPracticeScripting"></a>Boas práticas para o desenvolvimento de scripts

Quando desenvolve um script personalizado para um cluster HDInsight, existem várias boas práticas a ter em mente:

* [Alvo da versão Apache Hadoop](#bPS1)
* [Alvo da versão OS](#bps10)
* [Fornecer ligações estáveis aos recursos de script](#bPS2)
* [Utilizar recursos pré-compilados](#bPS4)
* [Certifique-se de que o script de personalização do cluster é idempotente](#bPS3)
* [Garantir a elevada disponibilidade da arquitetura do cluster](#bPS5)
* [Configure os componentes personalizados para utilizar o armazenamento Azure Blob](#bPS6)
* [Escreva informações para STDOUT e STDERR](#bPS7)
* [Guardar ficheiros como ASCII com terminações de linha LF](#bps8)
* [Use a lógica de retry para recuperar de erros transitórios](#bps9)

> [!IMPORTANT]  
> As ações do script devem ser concluídas dentro de 60 minutos ou o processo falha. Durante o fornecimento do nó, o script funciona simultaneamente com outros processos de configuração e configuração. A concorrência por recursos como o tempo de CPU ou a largura de banda da rede pode fazer com que o script demore mais tempo a terminar do que no seu ambiente de desenvolvimento.

### <a name="target-the-apache-hadoop-version"></a><a name="bPS1"></a>Alvo da versão Apache Hadoop

Diferentes versões do HDInsight têm diferentes versões de serviços hadoop e componentes instalados. Se o seu script espera uma versão específica de um serviço ou componente, só deve utilizar o script com a versão do HDInsight que inclui os componentes necessários. Pode encontrar informações sobre versões de componentes incluídas com o HDInsight utilizando o documento de versão do [componente HDInsight.](hdinsight-component-versioning.md)

### <a name="checking-the-operating-system-version"></a>Verificação da versão do sistema operativo

Diferentes versões do HDInsight dependem de versões específicas do Ubuntu. Pode haver diferenças entre as versões OS que deve verificar no seu script. Por exemplo, pode ser necessário instalar um binário que esteja ligado à versão de Ubuntu.

Para verificar a versão `lsb_release`OS, utilize . Por exemplo, o seguinte script demonstra como referenciar um ficheiro de alcatrão específico dependendo da versão S:

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

### <a name="target-the-operating-system-version"></a><a name="bps10"></a>Direcione a versão do sistema operativo

O HDInsight baseia-se na distribuição ubuntu Linux. Diferentes versões do HDInsight dependem de diferentes versões do Ubuntu, o que pode alterar o seu funcionamento do seu script. Por exemplo, hDInsight 3.4 e anteriorsão baseados em versões Ubuntu que usam Upstart. As versões 3.5 e maiores baseiam-se no Ubuntu 16.04, que utiliza o Systemd. Sistemae Upstart dependem de comandos diferentes, por isso o seu script deve ser escrito para trabalhar com ambos.

Outra diferença importante entre hDInsight 3.4 `JAVA_HOME` e 3.5 é que agora aponta para Java 8. O seguinte código demonstra como determinar se o script está em execução em Ubuntu 14 ou 16:

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

Você pode encontrar o script completo que https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.shcontém estes cortes em .

Para a versão do Ubuntu que é utilizada pelo HDInsight, consulte o documento da [versão componente HDInsight.](hdinsight-component-versioning.md)

Para compreender as diferenças entre Systemd e Upstart, consulte [Systemd para utilizadores upstart](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="provide-stable-links-to-script-resources"></a><a name="bPS2"></a>Fornecer ligações estáveis aos recursos de script

O guião e os recursos associados devem permanecer disponíveis durante toda a vida útil do cluster. Estes recursos são necessários se forem adicionados novos nós ao cluster durante as operações de escala.

A melhor prática é descarregar e arquivar tudo numa conta de Armazenamento Azure na sua subscrição.

> [!IMPORTANT]  
> A conta de armazenamento utilizada deve ser a conta de armazenamento por defeito do cluster ou de um recipiente público, apenas de leitura em qualquer outra conta de armazenamento.

Por exemplo, as amostras fornecidas pela [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) Microsoft são armazenadas na conta de armazenamento. Esta localização é um recipiente público e só de leitura mantido pela equipa HDInsight.

### <a name="use-pre-compiled-resources"></a><a name="bPS4"></a>Utilizar recursos pré-compilados

Para reduzir o tempo que demora a executar o script, evite operações que compilem recursos a partir do código fonte. Por exemplo, pré-compile os recursos e guarde-os numa conta de Armazenamento Azure blob no mesmo centro de dados que o HDInsight.

### <a name="ensure-that-the-cluster-customization-script-is-idempotent"></a><a name="bPS3"></a>Certifique-se de que o script de personalização do cluster é idempotente

Os scripts devem ser idempotentes. Se o guião for executado várias vezes, deve devolver o cluster ao mesmo estado todas as vezes.

Por exemplo, um script que modifica ficheiros de configuração não deve adicionar entradas duplicadas se for em várias vezes.

### <a name="ensure-high-availability-of-the-cluster-architecture"></a><a name="bPS5"></a>Garantir a elevada disponibilidade da arquitetura do cluster

Os clusters HDInsight baseados em Linux fornecem dois nós de cabeça que estão ativos dentro do cluster, e as ações de script funcionam em ambos os nós. Se os componentes que instala esperarem apenas um nó de cabeça, não instale os componentes em ambos os nós da cabeça.

> [!IMPORTANT]  
> Os serviços prestados como parte do HDInsight foram concebidos para falhar entre os dois nós da cabeça, conforme necessário. Esta funcionalidade não é estendida a componentes personalizados instalados através de ações de script. Se necessitar de uma elevada disponibilidade para componentes personalizados, tem de implementar o seu próprio mecanismo de failover.

### <a name="configure-the-custom-components-to-use-azure-blob-storage"></a><a name="bPS6"></a>Configure os componentes personalizados para utilizar o armazenamento Azure Blob

Os componentes que instala no cluster podem ter uma configuração predefinida que utiliza o armazenamento do Sistema de Ficheiros Distribuídos Apache Hadoop (HDFS). O HDInsight utiliza o Armazenamento Azure ou o Armazenamento do Lago de Dados como armazenamento predefinido. Ambos fornecem um sistema de ficheirocompatível com HDFS que persiste dados mesmo que o cluster seja eliminado. Pode ser necessário configurar os componentes que instala para utilizar WASB ou ADL em vez de HDFS.

Para a maioria das operações, não precisa especificar o sistema de ficheiros. Por exemplo, as seguintes cópias do ficheiro hadoop-common.jar do sistema de ficheiros local para armazenamento de cluster:

```bash
hdfs dfs -put /usr/hdp/current/hadoop-client/hadoop-common.jar /example/jars/
```

Neste exemplo, `hdfs` o comando utiliza de forma transparente o armazenamento de cluster predefinido. Para algumas operações, poderá ter de especificar o URI. Por exemplo, `adl:///example/jars` para o Azure `abfs:///example/jars` Data Lake Storage Gen1, para Data Lake Storage Gen2 ou `wasb:///example/jars` para armazenamento azure.

### <a name="write-information-to-stdout-and-stderr"></a><a name="bPS7"></a>Escreva informações para STDOUT e STDERR

HDInsight regista saída de script que é escrita para STDOUT e STDERR. Pode ver esta informação usando a UI web ambari.

> [!NOTE]  
> Apache Ambari só está disponível se o cluster for criado com sucesso. Se utilizar uma ação de script durante a criação de clusters, e a criação falhar, consulte a secção de resolução de [problemas Customize HDInsight clusters usando a ação](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting) do script para outras formas de aceder a informações registadas.

A maioria dos utilitários e pacotes de instalação já escrevem informações para STDOUT e STDERR, no entanto, pode querer adicionar registo saqueado adicional. Para enviar sms para `echo`STDOUT, utilize . Por exemplo:

```bash
echo "Getting ready to install Foo"
```

Por padrão, `echo` envia a corda para STDOUT. Para direcioná-lo para `>&2` `echo`STDERR, adicione antes . Por exemplo:

```bash
>&2 echo "An error occurred installing Foo"
```

Isto redireciona as informações escritas para STDOUT para STDERR (2) em vez disso. Para obter mais informações sobre [https://www.tldp.org/LDP/abs/html/io-redirection.html](https://www.tldp.org/LDP/abs/html/io-redirection.html)a reorientação da IO, consulte .

Para obter mais informações sobre a visualização de informações registadas por ações de script, consulte [Customize HDInsight clusters usando ação de script](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

### <a name="save-files-as-ascii-with-lf-line-endings"></a><a name="bps8"></a>Guardar ficheiros como ASCII com terminações de linha LF

Os scripts de bash devem ser armazenados como formato ASCII, com linhas terminadas por LF. Os ficheiros que são armazenados como UTF-8, ou utilizam o CRLF, uma vez que o fim da linha pode falhar com o seguinte erro:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="use-retry-logic-to-recover-from-transient-errors"></a><a name="bps9"></a>Use a lógica de retry para recuperar de erros transitórios

Ao descarregar ficheiros, instalar pacotes utilizando apt-get, ou outras ações que transmitam dados através da internet, a ação pode falhar devido a erros transitórios de rede. Por exemplo, o recurso remoto com que está a comunicar pode estar em processo de falhar num nó de reserva.

Para tornar o seu guião resiliente a erros transitórios, pode implementar a lógica de retry. A função seguinte demonstra como implementar a lógica de retry. Tenta novamente a operação três vezes antes de falhar.

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

Os seguintes exemplos demonstram como utilizar esta função.

```bash
retry ls -ltr foo

retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
```

## <a name="helper-methods-for-custom-scripts"></a><a name="helpermethods"></a>Métodos de ajudante para scripts personalizados

Os métodos de ajuda de ação do script são utilitários que pode usar enquanto escreve scripts personalizados. Estes métodos estão [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) contidos no guião. Use o seguinte para descarregá-los e usá-los como parte do seu script:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

Os seguintes ajudantes disponíveis para utilização no seu script:

| Utilização do ajudante | Descrição |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Descarrega um ficheiro da fonte URI para o caminho de ficheiro especificado. Por padrão, não substitui um ficheiro existente. |
| `untar_file TARFILE DESTDIR` |Extrai um ficheiro `-xf`de alcatrão (utilizando) para o diretório de destino. |
| `test_is_headnode` |Se correr em um nó de cabeça cluster, retorno 1; caso contrário, 0. |
| `test_is_datanode` |Se o nó atual for um nó de dados (trabalhador), devolva um 1; caso contrário, 0. |
| `test_is_first_datanode` |Se o nó atual for o nó de primeiro si (trabalhador) (nomeado workernode0) devolver um 1; caso contrário, 0. |
| `get_headnodes` |Devolva o nome de domínio totalmente qualificado dos cabeçanos do cluster. Os nomes são vírem delimitados. Uma corda vazia é devolvida por engano. |
| `get_primary_headnode` |Obtém o nome de domínio totalmente qualificado do cabeçalho principal. Uma corda vazia é devolvida por engano. |
| `get_secondary_headnode` |Obtém o nome de domínio totalmente qualificado do cabeçalho secundário. Uma corda vazia é devolvida por engano. |
| `get_primary_headnode_number` |Obtém o sufixo numérico do cabeçada primário. Uma corda vazia é devolvida por engano. |
| `get_secondary_headnode_number` |Obtém o sufixo numérico do nódoa secundário. Uma corda vazia é devolvida por engano. |

## <a name="common-usage-patterns"></a><a name="commonusage"></a>Padrões de utilização comuns

Esta secção fornece orientações sobre a implementação de alguns dos padrões de utilização comuns que você pode encontrar enquanto escreve o seu próprio script personalizado.

### <a name="passing-parameters-to-a-script"></a>Passar parâmetros para um script

Em alguns casos, o seu guião pode requerer parâmetros. Por exemplo, pode necessitar da senha de administração do cluster ao utilizar a API Ambari REST.

Os parâmetros passados para o script são conhecidos `$1` como *parâmetros posicionais,* e são atribuídos para o primeiro parâmetro, `$2` para o segundo, e assim por diante. `$0`contém o nome do próprio guião.

Os valores passados para o guião como parâmetros devem ser incluídos por cotações únicas ('). Ao fazê-lo, assegura-se de que o valor passado seja tratado como um literal.

### <a name="setting-environment-variables"></a>Definição de variáveis ambientais

A definição de uma variável ambiental é executada pela seguinte declaração:

    VARIABLENAME=value

Onde O NOME VARIÁVEL é o nome da variável. Para aceder à `$VARIABLENAME`variável, use. Por exemplo, para atribuir um valor fornecido por um parâmetro posicional como uma variável ambiental chamada PASSWORD, utilizaria a seguinte declaração:

    PASSWORD=$1

O acesso subsequente à `$PASSWORD`informação poderia então ser utilizado .

As variáveis ambientais definidas dentro do script só existem no âmbito do script. Em alguns casos, poderá ser necessário adicionar variáveis ambientais em todo o sistema que persistirão após o script ter terminado. Para adicionar variáveis ambientais em todo `/etc/environment`o sistema, adicione a variável a . Por exemplo, a `HADOOP_CONF_DIR`seguinte declaração acrescenta:

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Acesso a locais onde os scripts personalizados são armazenados

Os scripts utilizados para personalizar um cluster precisam de ser armazenados num dos seguintes locais:

* Uma __conta de Armazenamento Azure__ que está associada ao cluster.

* Uma __conta de armazenamento adicional__ associada ao cluster.

* Um __URI publicamente legível.__ Por exemplo, um URL para dados armazenados no OneDrive, Dropbox ou outro serviço de hospedagem de ficheiros.

* Uma conta de armazenamento de lago de __dados Azure__ que está associada ao cluster HDInsight. Para obter mais informações sobre a utilização do Armazenamento do Lago De Dados Azure com o HDInsight, consulte [Quickstart: Instale clusters no HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

    > [!NOTE]  
    > O diretor de serviço HDInsight usa para aceder ao Data Lake Storage deve ter lido o acesso ao script.

Os recursos utilizados pelo guião também devem estar disponíveis ao público.

Armazenar os ficheiros numa conta de Armazenamento Azure ou no Armazenamento de Lagos De Dados Azure proporciona acesso rápido, uma vez que tanto dentro da rede Azure.

> [!NOTE]  
> O formato URI utilizado para fazer referência ao script difere consoante o serviço utilizado. Para contas de armazenamento associadas ao cluster `wasb://` `wasbs://`HDInsight, utilize ou . Para URIs publicamente `http://` `https://`legível, utilize ou . Para armazenamento de `adl://`data lake, use .

## <a name="checklist-for-deploying-a-script-action"></a><a name="deployScript"></a>Lista de verificação para implementar uma ação de script

Aqui estão os passos dados quando se prepara para implementar um script:

* Coloque os ficheiros que contêm os scripts personalizados num local acessível pelos nós do cluster durante a implementação. Por exemplo, o armazenamento predefinido para o cluster. Os ficheiros também podem ser armazenados em serviços de hospedagem publicamente legível.
* Verifique se o guião é idempotente. Ao fazê-lo permite que o guião seja executado várias vezes no mesmo nó.
* Utilize um diretório de ficheiros temporário /tMP para manter os ficheiros descarregados utilizados pelos scripts e, em seguida, limpá-los depois de os scripts terem sido executados.
* Se as definições de nível OS ou os ficheiros de configuração do serviço Hadoop forem alterados, poderá querer reiniciar os serviços HDInsight.

## <a name="how-to-run-a-script-action"></a><a name="runScriptAction"></a>Como executar uma ação de script

Pode utilizar ações de script para personalizar clusters HDInsight utilizando os seguintes métodos:

* Portal do Azure
* Azure PowerShell
* Modelos do Azure Resource Manager
* O HDInsight .NET SDK.

Para obter mais informações sobre a utilização de cada método, consulte [Como utilizar a ação do script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="custom-script-samples"></a><a name="sampleScripts"></a>Amostras personalizadas de script

A Microsoft fornece scripts de amostra para instalar componentes num cluster HDInsight. Consulte [instalar e utilizar hue em clusters HDInsight](hdinsight-hadoop-hue-linux.md) como uma ação de script exemplo.

## <a name="troubleshooting"></a>Resolução de problemas

Seguem-se erros que poderá supor ao utilizar scripts que desenvolveu:

**Erro:** `$'\r': command not found`. Às vezes seguido por. `syntax error: unexpected end of file`

*Causa*: Este erro é causado quando as linhas de um script terminam com CRLF. Os sistemas Unix esperam apenas LF como o fim da linha.

Este problema ocorre mais frequentemente quando o script é da autoria de um ambiente Windows, uma vez que CRLF é uma linha comum terminando para muitos editores de texto no Windows.

*Resolução*: Se for uma opção no seu editor de texto, selecione o formato Unix ou LF para o final da linha. Também pode utilizar os seguintes comandos num sistema Unix para alterar o CRLF para um LF:

> [!NOTE]  
> Os seguintes comandos são aproximadamente equivalentes na forma como devem alterar os terminações da linha CRLF para LF. Selecione um com base nos utilitários disponíveis no seu sistema.

| Comando | Notas |
| --- | --- |
| `unix2dos -b INFILE` |O ficheiro original está apoiado com um . Extensão BAK |
| `tr -d '\r' < INFILE > OUTFILE` |OUTFILE contém uma versão com apenas finais LF |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Modifica o ficheiro diretamente |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |OutFILE contém uma versão com apenas terminações LF. |

**Erro:** `line 1: #!/usr/bin/env: No such file or directory`.

*Causa*: Este erro ocorre quando o script foi guardado como UTF-8 com uma Marca de Ordem byte (BOM).

*Resolução*: Guarde o ficheiro quer como ASCII, quer como UTF-8 sem BOM. Também pode utilizar o seguinte comando num sistema Linux ou Unix para criar um ficheiro sem o BOM:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Substitua-a `INFILE` com o ficheiro que contém o BOM. `OUTFILE`deve ser um novo nome de arquivo, que contém o script sem o BOM.

## <a name="next-steps"></a><a name="seeAlso"></a>Passos seguintes

* Saiba como [personalizar os clusters HDInsight usando a ação](hdinsight-hadoop-customize-cluster-linux.md) do script
* Utilize a [referência HDInsight .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight) para saber mais sobre a criação de aplicações .NET que gerem o HDInsight
* Utilize a [API HDInsight REST](https://msdn.microsoft.com/library/azure/mt622197.aspx) para aprender a utilizar o REST para realizar ações de gestão em clusters HDInsight.
