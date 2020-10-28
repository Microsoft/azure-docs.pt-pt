---
title: Desenvolver ações de script para personalizar clusters Azure HDInsight
description: Aprenda a usar scripts Bash para personalizar clusters HDInsight. As ações de script permitem executar scripts durante ou após a criação do cluster para alterar as definições de configuração do cluster ou instalar software adicional.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/28/2019
ms.openlocfilehash: f7959b639b75d912d44670c8b00a7327cb7857d6
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92629447"
---
# <a name="script-action-development-with-hdinsight"></a>Desenvolvimento de ação de script com HDInsight

Aprenda a personalizar o seu cluster HDInsight usando scripts Bash. As ações do script são uma forma de personalizar o HDInsight durante ou após a criação do cluster.

## <a name="what-are-script-actions"></a>O que são ações de script

As ações do Script são scripts Bash que o Azure executa nos nós do cluster para fazer alterações de configuração ou instalar software. Uma ação de script é executada como raiz, e fornece direitos de acesso completos aos nós do cluster.

As ações do script podem ser aplicadas através dos seguintes métodos:

| Use este método para aplicar um script... | Durante a criação de clusters... | Num aglomerado de corridas... |
| --- |:---:|:---:|
| Portal do Azure |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| CLI Clássica do Azure |&nbsp; |✓ |
| HDInsight .NET SDK |✓ |✓ |
| Modelo do Azure Resource Manager |✓ |&nbsp; |

Para obter mais informações sobre a utilização destes métodos para aplicar ações de script, consulte [os clusters HDInsight personalizados utilizando ações de script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="best-practices-for-script-development"></a><a name="bestPracticeScripting"></a>Melhores práticas para o desenvolvimento de scripts

Quando desenvolve um script personalizado para um cluster HDInsight, existem várias boas práticas a ter em mente:

* [Alvo da versão Apache Hadoop](#bPS1)
* [Destino da versão OS](#bps10)
* [Fornecer ligações estáveis aos recursos do script](#bPS2)
* [Utilizar recursos pré-compilados](#bPS4)
* [Certifique-se de que o script de personalização do cluster é idempotente](#bPS3)
* [Garantir alta disponibilidade da arquitetura de cluster](#bPS5)
* [Configure os componentes personalizados para usar o armazenamento Azure Blob](#bPS6)
* [Escrever informações para STDOUT e STDERR](#bPS7)
* [Guardar ficheiros como ASCII com terminações de linha LF](#bps8)
* [Use a lógica de relemgar para recuperar de erros transitórios](#bps9)

> [!IMPORTANT]  
> As ações do script devem ser concluídas dentro de 60 minutos ou o processo falhar. Durante o fornecimento de nó, o script é executado simultaneamente com outros processos de configuração e configuração. A competição por recursos como o tempo de CPU ou largura de banda de rede pode fazer com que o script despuça mais tempo do que no seu ambiente de desenvolvimento.

### <a name="target-the-apache-hadoop-version"></a><a name="bPS1"></a>Alvo da versão Apache Hadoop

Diferentes versões do HDInsight têm diferentes versões de serviços e componentes Hadoop instalados. Se o seu script espera uma versão específica de um serviço ou componente, só deve utilizar o script com a versão do HDInsight que inclui os componentes necessários. Pode encontrar informações sobre versões componentes incluídas com HDInsight utilizando o documento [de versão do componente HDInsight.](hdinsight-component-versioning.md)

### <a name="checking-the-operating-system-version"></a>Verificação da versão do sistema operativo

Diferentes versões do HDInsight dependem de versões específicas de Ubuntu. Pode haver diferenças entre as versões de SO que deve verificar no seu script. Por exemplo, pode ser necessário instalar um binário que esteja ligado à versão de Ubuntu.

Para verificar a versão OS, utilize `lsb_release` . Por exemplo, o seguinte script demonstra como fazer referência a um ficheiro de alcatrão específico dependendo da versão OS:

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

### <a name="target-the-operating-system-version"></a><a name="bps10"></a> Direcionar a versão do sistema operativo

O HDInsight baseia-se na distribuição Ubuntu Linux. Diferentes versões do HDInsight dependem de diferentes versões de Ubuntu, o que pode mudar o comportamento do seu script. Por exemplo, HDInsight 3.4 e anteriormente são baseados em versões Ubuntu que usam Upstart. As versões 3.5 ou maiores são baseadas em Ubuntu 16.04, que utiliza Systemd. Systemd e Upstart dependem de diferentes comandos, por isso o seu script deve ser escrito para trabalhar com ambos.

Outra diferença importante entre HDInsight 3.4 e 3.5 é que `JAVA_HOME` agora aponta para Java 8. O seguinte código demonstra como determinar se o script está a ser em execução no Ubuntu 14 ou 16:

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

Pode encontrar o roteiro completo que contém estes cortes em https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh .

Para a versão do Ubuntu que é usada pela HDInsight, consulte o documento de [versão do componente HDInsight.](hdinsight-component-versioning.md)

Para compreender as diferenças entre Systemd e Upstart, consulte [Systemd para utilizadores upstart](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="provide-stable-links-to-script-resources"></a><a name="bPS2"></a>Fornecer ligações estáveis aos recursos do script

O script e os recursos associados devem permanecer disponíveis durante toda a vida do cluster. Estes recursos são necessários se forem adicionados novos nós ao cluster durante as operações de escala.

A melhor prática é descarregar e arquivar tudo numa conta de Armazenamento Azure na sua subscrição.

> [!IMPORTANT]  
> A conta de armazenamento utilizada deve ser a conta de armazenamento predefinida para o cluster ou um recipiente público, apenas de leitura em qualquer outra conta de armazenamento.

Por exemplo, as amostras fornecidas pela Microsoft são armazenadas na [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) conta de armazenamento. Este local é um recipiente público, apenas de leitura mantido pela equipa HDInsight.

### <a name="use-pre-compiled-resources"></a><a name="bPS4"></a>Utilizar recursos pré-compilados

Para reduzir o tempo que leva para executar o script, evite operações que compilam recursos a partir do código fonte. Por exemplo, pré-compilar recursos e armazená-los numa bolha de conta Azure Storage no mesmo centro de dados que o HDInsight.

### <a name="ensure-that-the-cluster-customization-script-is-idempotent"></a><a name="bPS3"></a>Certifique-se de que o script de personalização do cluster é idempotente

Os scripts devem ser idempotentes. Se o script for executado várias vezes, deve devolver o cluster ao mesmo estado sempre.

Por exemplo, um script que modifica ficheiros de configuração não deve adicionar entradas duplicadas se for várias vezes.

### <a name="ensure-high-availability-of-the-cluster-architecture"></a><a name="bPS5"></a>Garantir alta disponibilidade da arquitetura de cluster

Os clusters HDInsight baseados em Linux fornecem dois nós de cabeça que estão ativos dentro do cluster, e as ações de script são executadas em ambos os nós. Se os componentes instalados esperarem apenas um nó de cabeça, não instale os componentes em ambos os nós da cabeça.

> [!IMPORTANT]  
> Os serviços prestados como parte do HDInsight são projetados para falhar entre os dois nós de cabeça, conforme necessário. Esta funcionalidade não é estendida a componentes personalizados instalados através de ações de script. Se necessitar de alta disponibilidade para componentes personalizados, tem de implementar o seu próprio mecanismo de falha.

### <a name="configure-the-custom-components-to-use-azure-blob-storage"></a><a name="bPS6"></a>Configure os componentes personalizados para usar o armazenamento Azure Blob

Os componentes que instala no cluster podem ter uma configuração predefinida que utiliza o armazenamento do Sistema de Ficheiros Distribuídos Apache Hadoop (HDFS). O HDInsight utiliza o Azure Storage ou o Data Lake Storage como o armazenamento predefinido. Ambos fornecem um sistema de ficheiros compatível com HDFS que persiste em dados mesmo que o cluster seja eliminado. Pode ser necessário configurar os componentes que instalou para utilizar WASB ou ADL em vez de HDFS.

Para a maioria das operações, não precisa de especificar o sistema de ficheiros. Por exemplo, as seguintes cópias do ficheiro hadoop-common.jar do sistema de ficheiros local para o armazenamento do agrupamento:

```bash
hdfs dfs -put /usr/hdp/current/hadoop-client/hadoop-common.jar /example/jars/
```

Neste exemplo, o `hdfs` comando utiliza de forma transparente o armazenamento de cluster padrão. Para algumas operações, poderá ter de especificar o URI. Por exemplo, `adl:///example/jars` para Azure Data Lake Storage Gen1, `abfs:///example/jars` para data lake storage gen2 ou para `wasb:///example/jars` armazenamento Azure.

### <a name="write-information-to-stdout-and-stderr"></a><a name="bPS7"></a>Escrever informações para STDOUT e STDERR

HDInsight regista a saída do script que está escrita para STDOUT e STDERR. Pode ver esta informação utilizando a UI web Ambari.

> [!NOTE]  
> Apache Ambari só está disponível se o cluster for criado com sucesso. Se utilizar uma ação de script durante a criação do cluster, e a criação falhar, consulte [as ações de script de resolução](./troubleshoot-script-action.md) de problemas para outras formas de aceder a informações registadas.

A maioria dos utilitários e pacotes de instalação já escrevem informações para STDOUT e STDERR, no entanto, você pode querer adicionar registos adicionais. Para enviar texto para STDOUT, utilize `echo` . Por exemplo:

```bash
echo "Getting ready to install Foo"
```

Por predefinição, `echo` envia o string para STDOUT. Para direcioná-lo para STDERR, adicione `>&2` antes `echo` . Por exemplo:

```bash
>&2 echo "An error occurred installing Foo"
```

Isto redireciona as informações escritas para STDOUT para STDERR (2) em vez disso. Para obter mais informações sobre a reorientação do IO, consulte [https://www.tldp.org/LDP/abs/html/io-redirection.html](https://www.tldp.org/LDP/abs/html/io-redirection.html) .

Para obter mais informações sobre a visualização de informações registadas pelas ações do script, consulte [as ações do script troubleshoot](./troubleshoot-script-action.md).

### <a name="save-files-as-ascii-with-lf-line-endings"></a><a name="bps8"></a> Guardar ficheiros como ASCII com terminações de linha LF

Os scripts bash devem ser armazenados como formato ASCII, com linhas terminadas pelo LF. Ficheiros que são armazenados como UTF-8, ou usam CRLF como o final da linha pode falhar com o seguinte erro:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="use-retry-logic-to-recover-from-transient-errors"></a><a name="bps9"></a> Use a lógica de relemgar para recuperar de erros transitórios

Ao descarregar ficheiros, instalar pacotes utilizando apt-get, ou outras ações que transmitam dados através da internet, a ação pode falhar devido a erros transitórios de rede. Por exemplo, o recurso remoto com que está a comunicar pode estar em vias de falhar num nó de reserva.

Para tornar o seu script resiliente a erros transitórios, pode implementar a lógica de repetição. A seguinte função demonstra como implementar a lógica de retrips. Retrição da operação três vezes antes de falhar.

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

Os exemplos a seguir demonstram como utilizar esta função.

```bash
retry ls -ltr foo

retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
```

## <a name="helper-methods-for-custom-scripts"></a><a name="helpermethods"></a>Métodos de ajuda para scripts personalizados

Os métodos de ajuda de ação do script são utilitários que pode usar enquanto escreve scripts personalizados. Estes métodos estão contidos no [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) guião. Utilize o seguinte para descarregar e usá-los como parte do seu script:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

Os seguintes ajudantes disponíveis para utilização no seu script:

| Utilização do ajudante | Descrição |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Descarrega um ficheiro da uri de origem para o caminho de ficheiro especificado. Por padrão, não substitui um ficheiro existente. |
| `untar_file TARFILE DESTDIR` |Extrai um ficheiro de alcatrão (usando) `-xf` para o diretório de destino. |
| `test_is_headnode` |Se o guião correr num nó de cabeça de cluster, devolva 1; caso contrário, 0. |
| `test_is_datanode` |Se o nó atual for um nó de dados (trabalhador), devolva um 1; caso contrário, 0. |
| `test_is_first_datanode` |Se o nó atual for o primeiro nó de dados (trabalhador) (nomeado trabalhador node0) devolva um 1; caso contrário, 0. |
| `get_headnodes` |Devolva o nome de domínio totalmente qualificado dos cabeçanças no cluster. Os nomes são delimitados. Uma corda vazia é devolvida por engano. |
| `get_primary_headnode` |Obtém o nome de domínio totalmente qualificado do cabeçano primário. Uma corda vazia é devolvida por engano. |
| `get_secondary_headnode` |Obtém o nome de domínio totalmente qualificado do headnode secundário. Uma corda vazia é devolvida por engano. |
| `get_primary_headnode_number` |Obtém o sufixo numérico do cabeçano primário. Uma corda vazia é devolvida por engano. |
| `get_secondary_headnode_number` |Obtém o sufixo numérico do cabeçano secundário. Uma corda vazia é devolvida por engano. |

## <a name="common-usage-patterns"></a><a name="commonusage"></a>Padrões de utilização comuns

Esta secção fornece orientações sobre a implementação de alguns dos padrões de utilização comuns que você pode encontrar enquanto escreve o seu próprio script personalizado.

### <a name="passing-parameters-to-a-script"></a>Passando parâmetros para um script

Em alguns casos, o seu script pode requerer parâmetros. Por exemplo, pode precisar da palavra-passe de administração para o cluster ao utilizar a API Ambari REST.

Os parâmetros passados para o script são conhecidos como *parâmetros posicionais* , e são atribuídos `$1` para o primeiro parâmetro, para o `$2` segundo, e assim-on. `$0` contém o nome do próprio guião.

Os valores passados para o guião como parâmetros devem ser incluídos por cotações únicas ('). Ao fazê-lo, garante que o valor passado é tratado como literal.

### <a name="setting-environment-variables"></a>Definição de variáveis ambientais

A definição de uma variável ambiental é realizada pela seguinte declaração:

```bash
VARIABLENAME=value
```

No exemplo anterior, `VARIABLENAME` é o nome da variável. Para aceder à variável, `$VARIABLENAME` utilize. Por exemplo, para atribuir um valor fornecido por um parâmetro posicional como uma variável ambiental chamada PASSWORD, utilizaria a seguinte declaração:

```bash
PASSWORD=$1
```

O acesso subsequente à informação poderia então ser `$PASSWORD` utilizado.

As variáveis ambientais definidas dentro do script só existem no âmbito do script. Em alguns casos, poderá ter de adicionar variáveis ambientais em todo o sistema que persistirão após o script ter terminado. Para adicionar variáveis ambientais em todo o sistema, adicione a variável a `/etc/environment` . Por exemplo, a seguinte declaração `HADOOP_CONF_DIR` acrescenta:

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Acesso a locais onde os scripts personalizados são armazenados

Os scripts utilizados para personalizar um cluster precisam de ser armazenados num dos seguintes locais:

* Uma __conta de Armazenamento Azure__ que está associada ao cluster.

* Uma __conta de armazenamento adicional__ associada ao cluster.

* Um __URI legível publicamente.__ Por exemplo, um URL para dados armazenados no OneDrive, Dropbox ou outro serviço de hospedagem de ficheiros.

* Uma __conta de armazenamento do Lago de Dados Azure__ que está associada ao cluster HDInsight. Para obter mais informações sobre a utilização do Azure Data Lake Storage com HDInsight, consulte [Quickstart: Configurar clusters em HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

    > [!NOTE]  
    > O principal serviço hdInsight usa para aceder ao Armazenamento do Lago de Dados deve ter lido o acesso ao script.

Os recursos utilizados pelo guião também devem estar disponíveis ao público.

Armazenar os ficheiros numa conta de Armazenamento Azure ou no Azure Data Lake Storage proporciona acesso rápido, como tanto dentro da rede Azure.

> [!NOTE]  
> O formato URI utilizado para fazer referência ao script difere consoante o serviço utilizado. Para contas de armazenamento associadas ao cluster HDInsight, utilize `wasb://` ou `wasbs://` . Para URIs, utilização `http://` ou . . `https://` Para armazenamento de data lake, use `adl://` .

## <a name="checklist-for-deploying-a-script-action"></a><a name="deployScript"></a>Lista de verificação para implantação de uma ação de script

Aqui estão os passos dados ao preparar-se para implementar um script:

* Coloque os ficheiros que contêm os scripts personalizados num local acessível pelos nós do cluster durante a implementação. Por exemplo, o armazenamento predefinido para o cluster. Os ficheiros também podem ser armazenados em serviços de hospedagem legível publicamente.
* Verifique se o script é idempotente. Ao fazê-lo, o script é executado várias vezes no mesmo nó.
* Utilize um diretório de ficheiros temporários /tmp para manter os ficheiros descarregados utilizados pelos scripts e, em seguida, limpe-os depois de os scripts terem sido executados.
* Se as definições de nível OS ou os ficheiros de configuração de serviço Hadoop forem alterados, poderá querer reiniciar os serviços HDInsight.

## <a name="how-to-run-a-script-action"></a><a name="runScriptAction"></a>Como executar uma ação de script

Pode utilizar ações de script para personalizar clusters HDInsight utilizando os seguintes métodos:

* Portal do Azure
* Azure PowerShell
* Modelos do Azure Resource Manager
* O HDInsight .NET SDK.

Para obter mais informações sobre a utilização de cada método, consulte [Como utilizar a ação do script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="custom-script-samples"></a><a name="sampleScripts"></a>Amostras de script personalizadas

A Microsoft fornece scripts de amostra para instalar componentes num cluster HDInsight. Consulte [instalar e utilizar o Hue nos clusters HDInsight](hdinsight-hadoop-hue-linux.md) como um exemplo de ação de script.

## <a name="troubleshooting"></a>Resolução de problemas

Os seguintes são erros que pode encontrar ao utilizar scripts que desenvolveu:

**Erro:** `$'\r': command not found` . Às vezes seguido `syntax error: unexpected end of file` por.

*Causa:* Este erro é causado quando as linhas de um script terminam com CRLF. Os sistemas Unix esperam apenas LF como o fim da linha.

Este problema ocorre mais frequentemente quando o script é da autoria num ambiente Windows, uma vez que CRLF é uma linha comum que termina para muitos editores de texto no Windows.

*Resolução* : Se for uma opção no seu editor de texto, selecione o formato Unix ou LF para o final da linha. Pode também utilizar os seguintes comandos num sistema Unix para alterar o CRLF para um LF:

> [!NOTE]  
> Os seguintes comandos são aproximadamente equivalentes na forma como devem alterar as terminações da linha CRLF para LF. Selecione um com base nos utilitários disponíveis no seu sistema.

| Comando | Notas |
| --- | --- |
| `unix2dos -b INFILE` |O ficheiro original é apoiado com um . Extensão BAK |
| `tr -d '\r' < INFILE > OUTFILE` |OUTFILE contém uma versão com apenas terminações LF |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Modifica o ficheiro diretamente |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |OUTFILE contém uma versão com apenas terminações LF. |

**Erro:** `line 1: #!/usr/bin/env: No such file or directory` .

*Causa* : Este erro ocorre quando o script foi guardado como UTF-8 com uma Marca de Ordem Byte (BOM).

*Resolução* : Guarde o ficheiro como ASCII, ou como UTF-8 sem um BOM. Pode também utilizar o seguinte comando num sistema Linux ou Unix para criar um ficheiro sem o BOM:

```bash
awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE
```

`INFILE`Substitua-a pelo ficheiro que contém o BOM. `OUTFILE` deve ser um novo nome de ficheiro, que contém o script sem o BOM.

## <a name="next-steps"></a><a name="seeAlso"></a>Passos seguintes

* Saiba como [personalizar clusters HDInsight usando ação de script](hdinsight-hadoop-customize-cluster-linux.md)
* Utilize a [referência HDInsight .NET SDK](/dotnet/api/overview/azure/hdinsight) para saber mais sobre a criação de aplicações .NET que gerem o HDInsight
* Utilize a [API HDInsight REST](/rest/api/hdinsight/) para aprender a usar o REST para executar ações de gestão em clusters HDInsight.