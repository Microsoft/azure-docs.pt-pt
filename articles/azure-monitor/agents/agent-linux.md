---
title: Instalar o agente do Log Analytics em computadores Linux
description: Este artigo descreve como ligar computadores Linux alojados em outras nuvens ou no local ao Azure Monitor com o agente Log Analytics para Linux.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: 5171cefdb82b958ae8148ff63f1daef5f67916c6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044962"
---
# <a name="install-log-analytics-agent-on-linux-computers"></a>Instalar o agente do Log Analytics em computadores Linux
Este artigo fornece detalhes sobre a instalação do agente Log Analytics em computadores Linux utilizando os seguintes métodos:

* [Instale o agente para o Linux utilizando um script de invólucro](#install-the-agent-using-wrapper-script) alojado no GitHub. Este é o método recomendado para instalar e atualizar o agente quando o computador tem conectividade com a Internet, diretamente ou através de um servidor proxy.
* [Faça o download manual e instale](#install-the-agent-manually) o agente. Isto é necessário quando o computador Linux não tem acesso à Internet e estará a comunicar com o Azure Monitor ou a Azure Automation através do [gateway Log Analytics](./gateway.md). 

>[!IMPORTANT]
> Os métodos de instalação descritos neste artigo são normalmente utilizados para máquinas virtuais no local ou em outras nuvens. Consulte [as opções de instalação](./log-analytics-agent.md#installation-options) para opções mais eficientes que pode utilizar para máquinas virtuais Azure.



## <a name="supported-operating-systems"></a>Sistemas operativos suportados

Consulte [a visão geral dos agentes do Azure Monitor](agents-overview.md#supported-operating-systems) para obter uma lista de distribuições Linux suportadas pelo agente Log Analytics.

>[!NOTE]
>O OpenSSL 1.1.0 só é suportado em plataformas x86_x64 (64 bits) e o OpenSSL antes de 1.x não é suportado em nenhuma plataforma.

>[!NOTE]
>A execução do Agente Linux Log Analytics em contentores não é suportada. Se precisar de monitorizar os contentores, por favor, aproveite a [solução de monitorização](../containers/containers.md) do contentor para os anfitriões do Docker ou [informações sobre o contentor](../containers/container-insights-overview.md) para kubernetes.

Começando com versões lançadas após agosto de 2018, estamos a fazer as seguintes alterações ao nosso modelo de suporte:  

* Apenas as versões do servidor são suportadas, não clientes.  
* Concentre o suporte em qualquer um dos [distros Azure Linux Endossados.](../../virtual-machines/linux/endorsed-distros.md) Note que pode haver algum atraso entre um novo distro/versão sendo Azure Linux Endossado e suportado para o agente Log Analytics Linux.
* Todos os lançamentos menores são suportados para cada versão principal listada.
* As versões que tenham passado a data de fim de suporte do fabricante não são suportadas.
* Suporta apenas imagens VM; os contentores, mesmo os derivados de imagens oficiais de editores distro, não são suportados.
* As novas versões da AMI não são suportadas.  
* Apenas as versões que executam o OpenSSL 1.x por padrão são suportadas.

>[!NOTE]
>Se estiver a utilizar um distro ou versão que não esteja atualmente suportado e não esteja alinhado com o nosso modelo de suporte, recomendamos que você forque este repo, reconhecendo que o suporte da Microsoft não fornecerá assistência com versões de agentes forcados.

### <a name="python-requirement"></a>Requisito de Python

Partindo da versão 1.13.27 do Agente Linux, o Agente Linux irá suportar os Python 2 e 3. Recomendamos sempre a utilização do último agente. 

Se estiver a utilizar uma versão mais antiga do agente, deve pedir à Máquina Virtual que utilize o Python 2 por predefinição. Se a sua máquina virtual estiver a utilizar um distro que não inclua python 2 por defeito, então deve instalá-lo. Os seguintes comandos de amostra instalarão Python 2 em diferentes distros.

 - Chapéu Vermelho, CentOS, Oráculo: `yum install -y python2`
 - Ubuntu, Debian: `apt-get install -y python2`
 - SUSE: `zypper install -y python2`

O python2 executável deve ser aliasado a *pitão.* Segue-se um método que pode utilizar para definir este pseudónimo:

1. Executar o seguinte comando para remover quaisquer pseudónimos existentes.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Executar o seguinte comando para criar o pseudónimo.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

## <a name="supported-linux-hardening"></a>Endurecimento de Linux apoiado
O Agente OMS tem um suporte limitado de personalização para o Linux. 

Neste momento, são apoiados: 
- FIPs

Estão em consideração, mas ainda não apoiados:
- CIS
- SELINUX

Outros métodos de endurecimento e personalização não são suportados nem planeados para o agente OMS.  

## <a name="agent-prerequisites"></a>Pré-requisitos do agente

A tabela seguinte realça as embalagens necessárias para [os distros Linux suportados](#supported-operating-systems) em que o agente será instalado.

|Pacote necessário |Description |Versão mínima |
|-----------------|------------|----------------|
|Glibc |    Biblioteca GNU C | 2.5-12 
|Openssl    | Bibliotecas OpenSSL | 1.0.x ou 1.1.x |
|Curl | cURL cliente web | 7.15.5 |
|Python | | 2.7 ou 3.6+
|Pitão-ctypes | | 
|PAM | Módulos de Autenticação Incorporável | | 

>[!NOTE]
>Ou rssyslog ou syslog-ng são necessários para recolher mensagens syslog. O syslog daemon padrão na versão 5 da red hat Enterprise Linux, CentOS e oracle Linux versão (sysklog) não é suportado para a coleção de eventos syslog. Para recolher dados syslog desta versão destas distribuições, o anão de rsyslog deve ser instalado e configurado para substituir o sysklog.

## <a name="network-requirements"></a>Requisitos de rede
Consulte [a visão geral](./log-analytics-agent.md#network-requirements) do agente Log Analytics para os requisitos de rede do agente Linux.

## <a name="agent-install-package"></a>Pacote de instalação de agente

O agente Log Analytics para Linux é composto por vários pacotes. O ficheiro de libertação contém os seguintes pacotes, que estão disponíveis executando o pacote de conchas com o `--extract` parâmetro:

**Pacote** | **Versão** | **Descrição**
----------- | ----------- | --------------
omsagent | 1.13.9 | O Agente de Analítica de Registo para Linux
omsconfig | 1.1.1 | Agente de configuração para o agente Log Analytics
omi | 1.6.4 | Open Management Infrastructure (OMI) -- um servidor CIM leve. *Note que o OMI requer acesso à raiz para executar um trabalho de cron necessário para o funcionamento do serviço*
scx | 1.6.4 | Provedores da OMI CIM para métricas de desempenho do sistema operativo
apache-cimprov | 1.0.1 | Prestador de monitorização de desempenho do Servidor Apache HTTP para OMI. Só foi instalado se o Servidor HTTP Apache for detetado.
mysql-cimprov | 1.0.1 | MySQL Server fornecedor de monitorização de desempenho para OMI. Só foi instalado se for detetado o servidor MySQL/MariaDB.
estivador-cimprov | 1.0.0 | Fornecedor de estivador para o OMI. Só é instalado se o Docker for detetado.

### <a name="agent-installation-details"></a>Detalhes da instalação do agente

Após a instalação do agente Log Analytics para pacotes Linux, são aplicadas as seguintes alterações adicionais de configuração a nível do sistema. Estes artefactos são removidos quando o pacote omsagent é desinstalado.

* Um utilizador não privilegiado chamado: `omsagent` é criado. O daemon corre sob esta credencial. 
* Um ficheiro *que inclui* os sudoers é criado em `/etc/sudoers.d/omsagent` . Isto autoriza `omsagent` a reiniciar o syslog e os damsagent daemons. Se as diretivas de sudo *incluir* não forem suportadas na versão instalada do sudo, estas entradas serão escritas para `/etc/sudoers` .
* A configuração do syslog é modificada para encaminhar um subconjunto de eventos para o agente. Para obter mais informações, consulte [a recolha de dados Configure Syslog](data-sources-syslog.md).

Num computador Linux monitorizado, o agente está listado como `omsagent` . `omsconfig` é o agente Log Analytics para o agente de configuração Linux que procura uma nova configuração lateral do portal a cada 5 minutos. A configuração nova e atualizada é aplicada aos ficheiros de configuração do agente localizados em `/etc/opt/microsoft/omsagent/conf/omsagent.conf` .

## <a name="install-the-agent-using-wrapper-script"></a>Instale o agente usando o script do invólucro

Os passos seguintes configuram a configuração do agente para Log Analytics em Azure e Azure Government cloud usando o script de invólucro para computadores Linux que podem comunicar diretamente ou através de um servidor proxy para descarregar o agente hospedado no GitHub e instalar o agente.  

Se o seu computador Linux necessitar de comunicar através de um servidor proxy para registar analytics, esta configuração pode ser especificada na linha de comando, incluindo `-p [protocol://][user:password@]proxyhost[:port]` . A propriedade *protocolar* aceita `http` ou , e a propriedade `https` *proxyhost* aceita um nome de domínio totalmente qualificado ou endereço IP do servidor proxy. 

Por exemplo: `https://proxy01.contoso.com:30443`

Se for necessária a autenticação em qualquer dos casos, tem de especificar o nome de utilizador e a palavra-passe. Por exemplo: `https://user01:password@proxy01.contoso.com:30443`

1. Para configurar o computador Linux para ligar a um espaço de trabalho Log Analytics, executar o seguinte comando fornecendo o ID do espaço de trabalho e a chave primária. Este comando transfere o agente, valida a respetiva soma de verificação e instala-o.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    O seguinte comando inclui o `-p` parâmetro proxy e a sintaxe de exemplo quando a autenticação é exigida pelo seu servidor proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Para configurar o computador Linux para ligar ao espaço de trabalho do Log Analytics na nuvem do Governo Azure, executar o seguinte comando fornecendo o ID do espaço de trabalho e a chave primária copiada anteriormente. Este comando transfere o agente, valida a respetiva soma de verificação e instala-o. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    O seguinte comando inclui o `-p` parâmetro proxy e a sintaxe de exemplo quando a autenticação é exigida pelo seu servidor proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Reinicie o agente ao executar o seguinte comando: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 


## <a name="install-the-agent-manually"></a>Instalar o agente manualmente

O agente Log Analytics do Linux é fornecido num pacote de script de concha auto-extrator e instalado. Este pacote contém pacotes Debian e RPM para cada um dos componentes do agente e pode ser instalado diretamente ou extraído para recuperar as embalagens individuais. Um pacote é fornecido para x64 e um para arquiteturas x86. 

> [!NOTE]
> Para os VMs Azure, recomendamos que instale o agente neles utilizando a [extensão VM Azure Log Analytics](../../virtual-machines/extensions/oms-linux.md) para o Linux. 

1. [Faça o download](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) e transfira o pacote apropriado (x64 ou x86) para o seu Linux VM ou computador físico, utilizando scp/sftp.

2. Instale o pacote utilizando o `--install` argumento. Para embarcar num espaço de trabalho log Analytics durante a instalação, forneça os `-w <WorkspaceID>` `-s <workspaceKey>` parâmetros e parâmetros copiados anteriormente.

    >[!NOTE]
    >É necessário utilizar o `--upgrade` argumento se forem instalados pacotes dependentes como omi, scx, omsconfig ou as suas versões mais antigas, como seria o caso se o agente do System Center Operations Manager para o Linux já estiver instalado. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Para configurar o agente Linux para instalar e ligar a um espaço de trabalho Log Analytics através de um gateway Log Analytics, executar o seguinte comando fornecendo os parâmetros chave do proxy, espaço de trabalho e espaço de trabalho. Esta configuração pode ser especificada na linha de comando, incluindo `-p [protocol://][user:password@]proxyhost[:port]` . A propriedade *proxyhost* aceita um nome de domínio totalmente qualificado ou endereço IP do servidor de gateway Log Analytics.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Se for necessária a autenticação, tem de especificar o nome de utilizador e a palavra-passe. Por exemplo: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Para configurar o computador Linux para ligar a um espaço de trabalho log Analytics na nuvem do Governo Azure, executar o seguinte comando fornecendo o ID do espaço de trabalho e a chave primária copiada anteriormente.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Se pretender instalar os pacotes do agente e configuá-lo para reportar a um espaço de trabalho específico do Log Analytics mais tarde, execute o seguinte comando:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Se pretender extrair os pacotes do agente do pacote sem instalar o agente, executar o seguinte comando:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="upgrade-from-a-previous-release"></a>Upgrade de um lançamento anterior

O upgrade a partir de uma versão anterior, a começar pela versão 1.0.0-47, é suportado em cada versão. Execute a instalação com o `--upgrade` parâmetro para atualizar todos os componentes do agente para a versão mais recente.

## <a name="cache-information"></a>Cache informação
Os dados do agente Log Analytics do Linux estão em cache na máquina local *a %STATE_DIR_WS%/out_oms_common*.buffer* antes de serem enviados para o Azure Monitor. Os dados de registo personalizados são tamponados em *%STATE_DIR_WS%/out_oms_blob*.buffer*. O caminho pode ser diferente para [algumas soluções e tipos de dados.](https://github.com/microsoft/OMS-Agent-for-Linux/search?utf8=%E2%9C%93&q=+buffer_path&type=)

O agente tenta carregar a cada 20 segundos. Se falhar, esperará um período de tempo exponencialmente aumentando até que consiga: 30 segundos antes da segunda tentativa, 60 segundos antes do terceiro, 120 segundos... e assim até um máximo de 16 minutos entre as retrações até que se conecte com sucesso novamente. O agente tentará até 6 vezes para um determinado pedaço de dados antes de descartar e passar para o próximo. Isto continua até que o agente possa carregar com sucesso novamente. Isto significa que os dados podem ser tamponados até aproximadamente 30 minutos antes de serem descartados.

O tamanho da cache padrão é de 10 MB, mas pode ser modificado no [ficheiro omsagent.conf](https://github.com/microsoft/OMS-Agent-for-Linux/blob/e2239a0714ae5ab5feddcc48aa7a4c4f971417d4/installer/conf/omsagent.conf).


## <a name="next-steps"></a>Passos seguintes

- [Reveja a gestão e manutenção do agente Log Analytics para Windows e Linux](agent-manage.md) para aprender como reconfigurar, atualizar ou remover o agente da máquina virtual.

- [Reveja a resolução de problemas do agente Linux](agent-linux-troubleshoot.md) se encontrar problemas durante a instalação ou gestão do agente.
