---
title: Conectar computadores Linux ao Azure Monitor | Microsoft Docs
description: Este artigo descreve como conectar computadores Linux hospedados em outras nuvens ou locais para Azure Monitor com o agente de Log Analytics para Linux.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 12/24/2019
ms.openlocfilehash: c06a7551a5c0f14be94ed14072b81c189e359aa8
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75542001"
---
# <a name="connect-linux-computers-to-azure-monitor"></a>Conectar computadores Linux ao Azure Monitor

Para monitorar e gerenciar máquinas virtuais ou computadores físicos em seu datacenter local ou em outro ambiente de nuvem com Azure Monitor, você precisa implantar o agente de Log Analytics e configurá-lo para relatar a um espaço de trabalho Log Analytics. O agente também dá suporte à função de Hybrid Runbook Worker para a automação do Azure.

O agente do Log Analytics para Linux pode ser instalado usando um dos métodos a seguir. Detalhes sobre como usar cada método são fornecidos posteriormente neste artigo.

* [Baixe e instale manualmente](#install-the-agent-manually) o agente. Isso é necessário quando o computador Linux não tem acesso à Internet e se comunicará com o Azure Monitor ou a automação do Azure por meio do [Gateway de log Analytics](gateway.md). 
* [Instale o agente para Linux usando um script wrapper](#install-the-agent-using-wrapper-script) hospedado no github. Esse é o método recomendado para instalar e atualizar o agente quando o computador tem conectividade com a Internet, diretamente ou por meio de um servidor proxy.

Para compreender a configuração suportada, consulte [sistemas operativos Linux suportados](log-analytics-agent.md#supported-linux-operating-systems) e [configuração de firewall de rede](log-analytics-agent.md#network-firewall-requirements).

>[!NOTE]
>O agente do Log Analytics para Linux não pode ser configurado para reportar a mais do que uma área de trabalho do Log Analytics. Ele só pode ser configurado para relatar tanto para um grupo de gerenciamento System Center Operations Manager quanto para Log Analytics espaço de trabalho simultaneamente, ou para individualmente.

## <a name="agent-install-package"></a>Pacote de instalação do agente

O agente do Log Analytics para Linux é composto por vários pacotes. O arquivo de versão contém os pacotes a seguir, que estão disponíveis com a execução do pacote de shell com o parâmetro `--extract`:

**Agrupa** | **Versão** | **Descrição**
----------- | ----------- | --------------
omsagent | 1.12.15 | O agente de Log Analytics para Linux
programa omsconfig | 1.1.1 | Agente de configuração para o agente de Log Analytics
OMI | 1.6.3 | OMI (infraestrutura de gerenciamento aberta) – um servidor CIM leve. *Observe que o OMI requer acesso de raiz para executar um trabalho cron necessário para o funcionamento do serviço*
SCX | 1.6.3 | Provedores CIM OMI para métricas de desempenho do sistema operacional
Apache-cimprov | 1.0.1 | Provedor de monitoramento de desempenho do Apache HTTP Server para OMI. Instalado somente se o Apache HTTP Server for detectado.
MySQL-cimprov | 1.0.1 | Provedor de monitoramento de desempenho do MySQL Server para OMI. Instalado somente se o servidor MySQL/MariaDB for detectado.
Docker-cimprov | 1.0.0 | Provedor do Docker para OMI. Instalado somente se o Docker for detectado.

### <a name="agent-installation-details"></a>Detalhes de instalação do agente

Depois de instalar o agente de Log Analytics para pacotes do Linux, as seguintes alterações de configuração adicionais em todo o sistema são aplicadas. Esses artefatos são removidos quando o pacote omsagent é desinstalado.

* Um usuário sem privilégios chamado: `omsagent` é criado. O daemon é executado nessa credencial. 
* Um arquivo de *inclusão* de sudors é criado no `/etc/sudoers.d/omsagent`. Isso autoriza o `omsagent` a reiniciar os daemons syslog e omsagent. Se as diretivas *incluir* o sudo não forem suportadas na versão instalada do sudo, essas entradas serão gravadas em `/etc/sudoers`.
* A configuração do syslog é modificada para encaminhar um subconjunto de eventos para o agente. Para obter mais informações, consulte [Configure syslog Data Collection](data-sources-syslog.md).

Em um computador Linux monitorado, o agente é listado como `omsagent`. `omsconfig` é o agente de configuração do Log Analytics Agent para Linux que procura a nova configuração no lado do portal a cada 5 minutos. A configuração nova e atualizada é aplicada aos arquivos de configuração do agente localizados em `/etc/opt/microsoft/omsagent/conf/omsagent.conf`.

## <a name="obtain-workspace-id-and-key"></a>Obter o ID e a chave da área de trabalho

Antes de instalar o agente do Log Analytics para Linux, precisa do ID e da chave da área de trabalho do Log Analytics. Essas informações são necessárias durante a instalação do agente para configurá-lo corretamente e garantir que ele possa se comunicar com êxito com Azure Monitor.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. No canto superior esquerdo da portal do Azure, selecione **todos os serviços**. Na caixa de pesquisa, digite **log Analytics**. Conforme você digita, a lista filtra com base em sua entrada. Selecione **log Analytics espaços de trabalho**.

2. Na lista de espaços de trabalho do Log Analytics, selecione o espaço de trabalho que você criou anteriormente. (Você pode ter nomeado **DefaultLAWorkspace**.)

3. Selecione **Configurações avançadas**:

    ![Menu de configurações avançadas para Log Analytics no portal do Azure](../learn/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. Selecione **Origens Ligadas** e, em seguida, selecione **Servidores Linux**.

5. O valor à direita de **ID da Área de Trabalho** e **Chave Primária**. Copie e cole ambos no seu editor favorito.

## <a name="install-the-agent-manually"></a>Instalar o agente manualmente

O agente de Log Analytics para Linux é fornecido em um pacote de script de Shell de extração automática e instalável. Esse pacote contém pacotes Debian e RPM para cada um dos componentes do agente e pode ser instalado diretamente ou extraído para recuperar os pacotes individuais. Um pacote é fornecido para x64 e outro para arquiteturas x86. 

Para VMs do Azure, recomendamos que você instale o agente neles usando a [extensão de VM log Analytics do Azure](../../virtual-machines/extensions/oms-linux.md) para Linux. 

1. Transfira o pacote apropriado (x86 ou x64) para sua VM do Linux ou computador físico usando o SCP/SFTP.

2. Instale o pacote usando o argumento `--install`. Para carregar um espaço de trabalho do Log Analytics durante a instalação, forneça os parâmetros `-w <WorkspaceID>` e `-s <workspaceKey>` copiados anteriormente.

    >[!NOTE]
    >Você precisará usar o argumento `--upgrade` se algum pacote dependente, como OMI, SCX, programa omsconfig ou suas versões mais antigas forem instalados, como seria o caso se o agente do System Center Operations Manager para Linux já estiver instalado. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Para configurar o agente do Linux para instalar e conectar-se a um Log Analytics espaço de trabalho por meio de um gateway de Log Analytics, execute o comando a seguir fornecendo os parâmetros proxy, ID do espaço de trabalho e chave do espaço de trabalho. Essa configuração pode ser especificada na linha de comando, incluindo `-p [protocol://][user:password@]proxyhost[:port]`. A propriedade *proxyHost* aceita um nome de domínio totalmente qualificado ou endereço IP do servidor de gateway de log Analytics.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Se a autenticação for necessária, você precisará especificar o nome de usuário e a senha. Por exemplo: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Para configurar o computador Linux para se conectar a um Log Analytics espaço de trabalho na nuvem do Azure governamental, execute o seguinte comando fornecendo a ID do espaço de trabalho e a chave primária copiadas anteriormente.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Se você quiser instalar os pacotes de agente e configurá-lo para relatar para um espaço de trabalho específico do Log Analytics em um momento posterior, execute o seguinte comando:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Se você quiser extrair os pacotes de agente do pacote sem instalar o agente, execute o seguinte comando:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="install-the-agent-using-wrapper-script"></a>Instalar o agente usando o script de wrapper

As etapas a seguir configuram a instalação do agente para Log Analytics no Azure e na nuvem do Azure governamental usando o script de wrapper para computadores Linux que podem se comunicar diretamente ou por meio de um servidor proxy para baixar o agente hospedado no GitHub e instalar o agente.  

Se o seu computador Linux precisar se comunicar por meio de um servidor proxy para Log Analytics, essa configuração poderá ser especificada na linha de comando, incluindo `-p [protocol://][user:password@]proxyhost[:port]`. A propriedade *Protocol* aceita `http` ou `https`, e a propriedade *proxyHost* aceita um nome de domínio totalmente qualificado ou um endereço IP do servidor proxy. 

Por exemplo: `https://proxy01.contoso.com:30443`

Se a autenticação for necessária em ambos os casos, você precisará especificar o nome de usuário e a senha. Por exemplo: `https://user01:password@proxy01.contoso.com:30443`

1. Para configurar o computador Linux para se conectar a um Log Analytics espaço de trabalho, execute o seguinte comando fornecendo a ID do espaço de trabalho e a chave primária. Este comando transfere o agente, valida a respetiva soma de verificação e instala-o.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    O comando a seguir inclui o `-p` parâmetro de proxy e a sintaxe de exemplo quando a autenticação é exigida pelo servidor proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Para configurar o computador Linux para se conectar ao Log Analytics espaço de trabalho na nuvem do Azure governamental, execute o seguinte comando fornecendo a ID do espaço de trabalho e a chave primária copiadas anteriormente. Este comando transfere o agente, valida a respetiva soma de verificação e instala-o. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    O comando a seguir inclui o `-p` parâmetro de proxy e a sintaxe de exemplo quando a autenticação é exigida pelo servidor proxy:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Reinicie o agente ao executar o seguinte comando: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="upgrade-from-a-previous-release"></a>Atualizar de uma versão anterior

A atualização de uma versão anterior, começando com a versão 1.0.0-47, tem suporte em cada versão. Execute a instalação com o parâmetro `--upgrade` para atualizar todos os componentes do agente para a versão mais recente.

## <a name="next-steps"></a>Passos seguintes

- Examine [o gerenciamento e a manutenção do agente de log Analytics para Windows e Linux](agent-manage.md) para saber mais sobre como reconfigurar, atualizar ou remover o agente da máquina virtual.

- Examine [solução de problemas do agente do Linux](agent-linux-troubleshoot.md) se você encontrar problemas ao instalar ou gerenciar o agente.
