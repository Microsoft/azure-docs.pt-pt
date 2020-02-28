---
title: Ligue os computadores Linux ao Monitor Azure [ Monitor] Microsoft Docs
description: Este artigo descreve como ligar computadores Linux hospedados em outras nuvens ou no local ao Azure Monitor com o agente Log Analytics para o Linux.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/21/2020
ms.openlocfilehash: 40c279a4beee9fbebe2de7f272fe51d9039f071c
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77668711"
---
# <a name="connect-linux-computers-to-azure-monitor"></a>Ligue os computadores Linux ao Monitor Azure

Para monitorizar e gerir máquinas virtuais ou computadores físicos no seu datacenter local ou outro ambiente em nuvem com o Monitor Azure, é necessário implementar o agente Log Analytics e configurá-lo para reportar num espaço de trabalho de Log Analytics. O agente também apoia o papel de Trabalhador de Runbook Híbrido para a Automação Azure.

O agente Log Analytics para o Linux pode ser instalado utilizando um dos seguintes métodos. Os detalhes sobre a utilização de cada método são fornecidos mais tarde no artigo.

* [Faça o download manual e instale](#install-the-agent-manually) o agente. Isto é necessário quando o computador Linux não tem acesso à Internet e estará a comunicar com o Azure Monitor ou a Azure Automation através do [portal Log Analytics](gateway.md). 
* [Instale o agente para o Linux utilizando um script de invólucro](#install-the-agent-using-wrapper-script) apresentado no GitHub. Este é o método recomendado para instalar e atualizar o agente quando o computador tem conectividade com a Internet, diretamente ou através de um servidor proxy.

Para compreender a configuração suportada, consulte [sistemas operativos Linux suportados](log-analytics-agent.md#supported-linux-operating-systems) e [configuração de firewall de rede](log-analytics-agent.md#network-firewall-requirements).

>[!NOTE]
>O agente do Log Analytics para Linux não pode ser configurado para reportar a mais do que uma área de trabalho do Log Analytics. Só pode ser configurado para reportar tanto a um grupo de gestão de gestão de Gestão de Operações do System Center como ao espaço de trabalho log Analytics simultaneamente, ou a qualquer um deles individualmente.

## <a name="agent-install-package"></a>Pacote de instalação de agente

O agente Log Analytics para o Linux é composto por vários pacotes. O ficheiro de libertação contém os seguintes pacotes, que estão disponíveis executando o pacote de conchas com o parâmetro `--extract`:

**Pacote** | **Versão** | **Descrição**
----------- | ----------- | --------------
omsagent | 1.12.15 | O Agente de Log Analytics para Linux
omsconfig | 1.1.1 | Agente de configuração para o agente Log Analytics
omi | 1.6.3 | Open Management Infrastructure (OMI) -- um servidor CIM leve. *Note que o OMI requer acesso raiz para executar um trabalho cron o necessário para o funcionamento do serviço*
scx | 1.6.3 | Fornecedores de CIM oMI para métricas de desempenho do sistema operativo
apache-cimprov | 1.0.1 | Fornecedor de monitorização de desempenho do Apache HTTP Server para OMI. Instalado apenas se for detetado o Apache HTTP Server.
mysql-cimprov | 1.0.1 | Fornecedor de monitorização de desempenho do MySQL Server para OMI. Instalado apenas se for detetado o servidor MySQL/MariaDB.
estivador-cimprov | 1.0.0 | Fornecedor de Docker para OMI. Só instalado se o Docker for detetado.

### <a name="agent-installation-details"></a>Detalhes da instalação do agente

Após a instalação do agente Log Analytics para pacotes Linux, aplicam-se as seguintes alterações adicionais de configuração em todo o sistema. Estes artefactos são removidos quando a embalagem omsagent é desinstalada.

* Um utilizador não privilegiado chamado: `omsagent` é criado. O daemon passa por baixo desta credencial. 
* Um ficheiro *sudoers inclui* o arquivo é criado em `/etc/sudoers.d/omsagent`. Isto autoriza `omsagent` reiniciar os daemons syslog e omsagent. Se o sudo *incluir* diretivas não forem suportadas na versão instalada do sudo, estas entradas serão escritas para `/etc/sudoers`.
* A configuração do sislog é modificada para encaminhar um subconjunto de eventos para o agente. Para mais informações, consulte a recolha de dados do [Configure Syslog](data-sources-syslog.md).

Num computador Linux monitorizado, o agente está listado como `omsagent`. `omsconfig` é o agente Log Analytics para o agente de configuração Linux que procura uma nova configuração lateral do portal a cada 5 minutos. A configuração nova e atualizada é aplicada aos ficheiros de configuração do agente localizados em `/etc/opt/microsoft/omsagent/conf/omsagent.conf`.

## <a name="obtain-workspace-id-and-key"></a>Obter o ID e a chave da área de trabalho

Antes de instalar o agente do Log Analytics para Linux, precisa do ID e da chave da área de trabalho do Log Analytics. Estas informações são necessárias durante a configuração do agente para configurá-la corretamente e garantir que pode comunicar com sucesso com o Monitor Azure.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. No canto superior esquerdo do portal Azure, selecione **Todos os serviços.** Na caixa de pesquisa, introduza **o Log Analytics**. À medida que escreve, a lista filtra com base na sua entrada. Selecione espaços de **trabalho Log Analytics**.

2. Na sua lista de espaços de trabalho Log Analytics, selecione o espaço de trabalho que criou anteriormente. (Pode ter-lhe chamado **DefaultLAWorkspace**.)

3. Selecione **definições avançadas:**

    ![Menu de Definições Avançadas para Log Analytics no portal Azure](../learn/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. Selecione **Origens Ligadas** e, em seguida, selecione **Servidores Linux**.

5. O valor à direita de **ID da Área de Trabalho** e **Chave Primária**. Copie e cole ambos no seu editor favorito.

## <a name="install-the-agent-manually"></a>Instale o agente manualmente

O agente Log Analytics para o Linux é fornecido num pacote de scripts de concha auto-extraído e instalado. Este pacote contém pacotes Debian e RPM para cada um dos componentes do agente e pode ser instalado diretamente ou extraído para recuperar as embalagens individuais. Um pacote é fornecido para x64 e um para arquiteturas x86. 

> [!NOTE]
> Para Os VMs Azure, recomendamos que instale o agente neles utilizando a [extensão VM Azure Log Analytics](../../virtual-machines/extensions/oms-linux.md) para linux. 

1. [Descarregue](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) e transfira o pacote apropriado (x64 ou x86) para o seu Linux VM ou computador físico, utilizando scp/sftp.

2. Instale o pacote utilizando o argumento `--install`. Para embarcar num espaço de trabalho de Log Analytics durante a instalação, forneça os parâmetros `-w <WorkspaceID>` e `-s <workspaceKey>` copiados anteriormente.

    >[!NOTE]
    >Você precisa usar o argumento `--upgrade` se quaisquer pacotes dependentes como omi, scx, omsconfig ou suas versões mais antigas forem instalados, como seria o caso se o agente do Gestor de Operações do Centro de Sistema para o Linux já estiver instalado. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Para configurar o agente Linux para instalar e ligar a um espaço de trabalho log Analytics através de um gateway Log Analytics, execute o seguinte comando fornecendo os parâmetros de proxy, workspace id e space space. Esta configuração pode ser especificada na linha de comando, incluindo `-p [protocol://][user:password@]proxyhost[:port]`. A propriedade *proxyhost* aceita um nome de domínio totalmente qualificado ou endereço IP do servidor de gateway Log Analytics.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Se for necessária a autenticação, tem de especificar o nome de utilizador e a palavra-passe. Por exemplo: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Para configurar o computador Linux para ligar a um espaço de trabalho de Log Analytics na nuvem do Governo de Azure, executar o seguinte comando fornecendo o ID do espaço de trabalho e a chave primária copiado anteriormente.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Se pretender instalar os pacotes do agente e configurá-lo para reportar a um espaço de trabalho específico do Log Analytics mais tarde, execute o seguinte comando:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Se pretender extrair as embalagens do agente do pacote sem instalar o agente, execute o seguinte comando:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="install-the-agent-using-wrapper-script"></a>Instale o agente usando o script de invólucro

Os seguintes passos configuram a configuração do agente para log analytics em nuvem Azure e Azure Government usando o script de invólucro para computadores Linux que pode comunicar diretamente ou através de um servidor proxy para descarregar o agente hospedado no GitHub e instalar o agente.  

Se o seu computador Linux precisar de comunicar através de um servidor proxy para Registar Analytics, esta configuração pode ser especificada na linha de comando, incluindo `-p [protocol://][user:password@]proxyhost[:port]`. A propriedade *protocolar* aceita `http` ou `https`, e a propriedade *proxyhost* aceita um nome de domínio ou endereço IP totalmente qualificado do servidor proxy. 

Por exemplo: `https://proxy01.contoso.com:30443`

Se for necessária autenticação em qualquer dos casos, tem de especificar o nome de utilizador e a palavra-passe. Por exemplo: `https://user01:password@proxy01.contoso.com:30443`

1. Para configurar o computador Linux para se ligar a um espaço de trabalho do Log Analytics, execute o seguinte comando fornecendo o ID do espaço de trabalho e a chave primária. Este comando transfere o agente, valida a respetiva soma de verificação e instala-o.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    O seguinte comando inclui o parâmetro de procuração `-p` e a sintaxe de exemplo quando a autenticação é exigida pelo seu servidor proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Para configurar o computador Linux para ligar ao espaço de trabalho log Analytics na nuvem do Governo de Azure, executar o seguinte comando fornecendo o ID do espaço de trabalho e chave primária copiado anteriormente. Este comando transfere o agente, valida a respetiva soma de verificação e instala-o. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    O seguinte comando inclui o parâmetro de procuração `-p` e a sintaxe de exemplo quando a autenticação é exigida pelo seu servidor proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Reinicie o agente ao executar o seguinte comando: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="upgrade-from-a-previous-release"></a>Upgrade de um lançamento anterior

A atualização de uma versão anterior, a começar pela versão 1.0.0-47, é suportada em cada versão. Execute a instalação com o parâmetro `--upgrade` para atualizar todos os componentes do agente para a versão mais recente.

## <a name="next-steps"></a>Passos seguintes

- Reveja [a gestão e manutenção do agente Log Analytics para windows e Linux](agent-manage.md) para aprender como reconfigurar, atualizar ou remover o agente da máquina virtual.

- Reveja [o problema de resolução do agente Linux](agent-linux-troubleshoot.md) se encontrar problemas durante a instalação ou gestão do agente.
