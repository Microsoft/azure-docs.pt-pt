---
title: Agrupar computadores usando dependências de computador com migrações para Azure | Microsoft Docs
description: Descreve como criar uma avaliação usando dependências de computador com o serviço de migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/01/2019
ms.author: hamusa
ms.openlocfilehash: bf6798d557cb1d27030565e4706864e945de6f04
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472099"
---
# <a name="set-up-dependency-visualization-for-assessment"></a>Configurar a visualização de dependência para avaliação

Este artigo descreve como configurar o mapeamento de dependência em migrações para Azure: avaliação do servidor.

O mapeamento de dependência ajuda a Visualizar dependências entre computadores que você deseja avaliar e migrar.

- Em migrações para Azure: avaliação de servidor, você reúne computadores para avaliação. Geralmente, os computadores que você deseja migrar juntos.
- Normalmente, você usa o mapeamento de dependência quando deseja avaliar grupos com níveis de confiança mais altos.
- O mapeamento de dependência ajuda a verificar dependências entre computadores antes de executar uma avaliação e migração.
- Mapear e Visualizar dependências ajuda a planejar efetivamente sua migração para o Azure. Ele ajuda a garantir que nada seja deixado para trás, evitando interrupções surpresa durante a migração.
- Usando o mapeamento, você pode descobrir sistemas interdependentes que precisam migrar juntos. Também pode identificar se um sistema de funcionamento ainda serve os utilizadores, ou se é candidato ao desmantelamento em vez de migração.

[Saiba mais](concepts-dependency-visualization.md#how-does-it-work) sobre a visualização da dependência.

## <a name="before-you-start"></a>Antes de começar

- Certifique-se de [ter criado](how-to-add-tool-first-time.md) um projeto Azure Migrate.
- Se já criou um projeto, certifique-se de ter [adicionado](how-to-assess.md) a ferramenta de avaliação do servidor Azure Migrate.
- Certifique-se de ter descoberto as suas máquinas em Azure Migrate; pode fazê-lo instalando um aparelho Azure Migrate para [VMware](how-to-set-up-appliance-vmware.md) ou [Hyper-V](how-to-set-up-appliance-hyper-v.md). O aparelho descobre máquinas no local e envia dados de metadados e desempenho para o Azure Migrate: Server Assessment. [Saiba mais](migrate-appliance.md).


**Funcionalidades** | **Nota**
--- | ---
Disponibilidade | A visualização da dependência não está disponível no Governo de Azure.
Mapa de Serviços | A visualização de dependência usa Mapa do Serviço solução no Azure Monitor. [O Mapa](../azure-monitor/insights/service-map.md) de Serviço descobre e mostra automaticamente ligações entre servidores.
Agentes | Para usar a visualização de dependência, instale os seguintes agentes nos computadores que você deseja mapear:<br/> - [agente de Log Analytics](../azure-monitor/platform/log-analytics-agent.md) (anteriormente referido como O Agente de Monitorização da Microsoft (MMA).<br/> - agente de dependência do mapa de [serviço.](../azure-monitor/insights/vminsights-enable-overview.md#the-microsoft-dependency-agent)<br/><br/> Para automatizar a instalação do agente, você pode usar uma ferramenta de implantação como o Configuration Manager, que tem uma solução de implantação de agente para migrações para Azure.
Agente de Dependência | Reveja o suporte do [agente dependency](../azure-monitor/insights/vminsights-enable-overview.md#the-microsoft-dependency-agent) para Windows e Linux.<br/><br/> [Saiba mais](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples) sobre a utilização de scripts para instalar o agente Dependency.
Agente de Log Analytics (MMA) | [Saiba mais](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration) sobre os métodos de instalação de MMA.<br/><br/> Para computadores monitorados pelo System Center Operations Manager 2012 R2 ou posterior, você não precisa instalar o agente do MMA. Mapa do Serviço integra-se com Operations Manager. Pode permitir a integração utilizando a orientação [aqui.](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites) No entanto, observe que o agente de dependência precisará ser instalado nesses computadores.<br/><br/> [Reveja](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) os sistemas operativos Linux suportados pelo agente Log Analytics.
Grupos de avaliação | Os grupos para os quais você deseja visualizar dependências não devem conter mais de 10 máquinas. Se você tiver mais de 10 máquinas, divida-as em grupos menores para visualizar as dependências.

## <a name="associate-a-log-analytics-workspace"></a>Associar um espaço de trabalho do Log Analytics

Para utilizar a visualização da dependência, é necessário associar um espaço de trabalho log [Analytics](../azure-monitor/platform/manage-access.md) a um projeto Azure Migrate.

- Você pode anexar um espaço de trabalho somente na assinatura do projeto de migrações para Azure.
- Você pode anexar um espaço de trabalho existente ou criar um novo.
- Você anexa o espaço de trabalho na primeira vez em que configura a visualização de dependência para um computador.
- Você pode anexar um espaço de trabalho somente após descobrir computadores no projeto de migrações para Azure. Pode fazê-lo configurando um aparelho Azure Migrate para [VMware](how-to-set-up-appliance-vmware.md) ou [Hyper-V](how-to-set-up-appliance-hyper-v.md). O aparelho descobre máquinas no local e envia dados de metadados e desempenho para o Azure Migrate: Server Assessment. [Saiba mais](migrate-appliance.md).

Anexe um espaço de trabalho da seguinte maneira:

1. Em **Azure Migrate: Avaliação do servidor,** clique na **visão geral**. Se ainda não adicionou a ferramenta de avaliação do servidor, [faça-o primeiro](how-to-assess.md).
2. Em **visão geral,** clique na seta para baixo para expandir **o Essencial**.
3. No **workspace OMS,** clique requer **configuração**.
4. No espaço de **trabalho configure,** especifique se pretende criar um novo espaço de trabalho ou utilizar um existente:

    ![Adicionar espaço de trabalho](./media/how-to-create-group-machine-dependencies/workspace.png)

    - Depois de especificar um nome para um novo espaço de trabalho, pode escolher a [região](https://azure.microsoft.com/global-infrastructure/regions/) em que o espaço de trabalho será criado.
    - Ao anexar um espaço de trabalho existente, você pode escolher entre todos os espaços de trabalho disponíveis na mesma assinatura que o projeto de migração.
    - Você precisa de acesso de leitor ao espaço de trabalho para poder anexá-lo.
    - Você não pode modificar o espaço de trabalho associado a um projeto depois que ele é anexado.

## <a name="download-and-install-the-vm-agents"></a>Transferir e instalar os agentes da VM

Baixe e instale os agentes em cada computador local que você deseja visualizar com o mapeamento de dependência.

1. Em **Azure Migrate: Avaliação do servidor,** clique em **servidores descobertos**.
2. Para cada máquina para a qual pretende utilizar a visualização da dependência, clique em **Necessitar de instalação do agente**.
3. Na página **Dependencies** para uma máquina > **Descarregue e instale MMA,** baixe o agente apropriado e instale-o como descrito abaixo.
4. No **Download e instale o agente Dependency,** descarregue o agente apropriado e instale-o como descrito abaixo.
5. Sob o **agente Configure MMA, copie**o ID do espaço de trabalho e a chave. Você precisará delas quando instalar o agente do MMA.

### <a name="install-the-mma"></a>Instalar o MMA

#### <a name="install-the-agent-on-a-windows-machine"></a>Instalar o agente em um computador Windows

Para instalar o agente em um computador Windows:

1. Faça duplo clique no agente transferido.
2. Na página **Bem-vindo**, clique em **Seguinte**. Na página **Termos de Licenciamento**, clique em **Concordo** para aceitar a licença.
3. Na **pasta destino,** guarde ou modifique a pasta de instalação predefinida > **Seguinte**.
4. Nas opções de configuração do **agente,** selecione **Azure Log Analytics** > **Seguinte**.
5. Clique em **Adicionar** para adicionar um novo espaço de trabalho log Analytics. Cole a ID do espaço de trabalho e a chave que você copiou do Portal. Clique em **Seguinte**.

Pode instalar o agente a partir da linha de comando ou utilizar um método automatizado como O Gestor de Configuração ou [Intigua](https://go.microsoft.com/fwlink/?linkid=2104196). [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) sobre a utilização destes métodos para instalar o agente MMA. O agente MMA também pode ser instalado com este [script](https://go.microsoft.com/fwlink/?linkid=2104394).

[Saiba mais](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) sobre os sistemas operativos Windows suportados pelo MMA.

#### <a name="install-the-agent-on-a-linux-machine"></a>Instalar o agente em um computador Linux

Para instalar o agente em um computador Linux:

1. Transfira o pacote apropriado (x86 ou x64) para seu computador Linux usando o SCP/SFTP.
2. Instale o pacote usando o argumento--install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Saiba mais](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-linux-operating-systems) sobre a lista de suporte de sistemas operativos Linux por MMA. 

### <a name="install-the-dependency-agent"></a>Instalar o agente de Dependência
1. Para instalar o Dependency Agent em um computador Windows, clique duas vezes no arquivo de instalação e siga o assistente.
2. Para instalar o Dependency Agent em um computador Linux, instale como raiz usando o seguinte comando:

    ```sh InstallDependencyAgent-Linux64.bin```

[Saiba mais](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#installation-script-examples) sobre como pode usar scripts para instalar o agente Dependency.

[Saiba mais](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#supported-operating-systems) sobre os sistemas operativos suportados pelo agente Dependency.


## <a name="create-a-group-using-dependency-visualization"></a>Criar um grupo usando a visualização de dependência

1. Em **Azure Migrate: Avaliação do servidor,** clique em **servidores descobertos**.
2. Na coluna **Dependencies,** clique em **Ver dependências** para cada máquina que pretende rever.
3. No mapa de dependências, você pode ver o seguinte:
    - Conexões TCP de entrada (clientes) e de saída (servidores), de e para o computador.
    - Os computadores dependentes que não têm os agentes de dependência instalados são agrupados por números de porta.
    - Os computadores dependentes com agentes de dependência instalados são mostrados como caixas separadas.
    - Processos em execução dentro da máquina. Expanda cada caixa da máquina para exibir os processos.
    - Propriedades da máquina (incluindo FQDN, sistema operacional, endereço MAC). Clique em cada caixa da máquina para exibir os detalhes.

4. Você pode examinar as dependências de diferentes durações de tempo clicando no tempo duração no rótulo de intervalo de tempo. Por padrão, o intervalo é de uma hora. Você pode modificar o intervalo de tempo ou especificar datas de início e de término e duração.

    > [!NOTE]
    > O intervalo de tempo pode ser de até uma hora. Se você precisar de um intervalo mais longo, use Azure Monitor para consultar dados dependentes por um período mais longo.

5. Depois de identificar as máquinas dependentes que pretende agrupar, use Ctrl+Click para selecionar várias máquinas no mapa e clique em **máquinas de grupo**.
6. Especifique um nome de grupo.
7. Verifique se os computadores dependentes são descobertos pelas migrações para Azure.

    - Se um computador dependente não for descoberto por migrações para Azure: avaliação de servidor, você não poderá adicioná-lo ao grupo.
    - Para adicionar um computador, execute a descoberta novamente e verifique se o computador foi descoberto.

8. Se você quiser criar uma avaliação para esse grupo, marque a caixa de seleção para criar uma nova avaliação para o grupo.
8. Clique **em OK** para salvar o grupo.

Depois de criar o grupo, recomendamos que você instale agentes em todos os computadores do grupo e, em seguida, visualize as dependências de todo o grupo.

## <a name="query-dependency-data-in-azure-monitor"></a>Dados de dependência de consulta em Azure Monitor

Você pode consultar dados de dependência capturados por Mapa do Serviço no espaço de trabalho Log Analytics associado ao projeto de migrações para Azure. Log Analytics é usado para gravar e executar Azure Monitor consultas de log.

- [Saiba como](../azure-monitor/insights/service-map.md#log-analytics-records) procurar dados do Mapa de Serviços no Log Analytics.
- [Obtenha uma visão geral](../azure-monitor/log-query/get-started-queries.md) da escrita de consultas de registo no [Log Analytics](../azure-monitor/log-query/get-started-portal.md).

Execute uma consulta para dados de dependência da seguinte maneira:

1. Depois de instalar os agentes, vá ao portal e clique em **Visão Geral**.
2. Em **Azure Migrate: Avaliação do servidor,** clique na **visão geral**. Clique na seta para baixo para expandir o **Essencial**.
3. No **Workspace OMS,** clique no nome do espaço de trabalho.
3. Na página do espaço de trabalho Log Analytics > **General,** clique em **Registos**.
4. Escreva a sua consulta e clique em **Executar**.

### <a name="sample-queries"></a>Amostras de consultas

Fornecemos um número de consultas de exemplo que você pode usar para extrair dados de dependência.

- Você pode modificar as consultas para extrair seus pontos de dados preferenciais.
- [Reveja](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) uma lista completa dos registos de dados de dependência.
- [Reveja](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches) as consultas adicionais de amostra.

#### <a name="sample-review-inbound-connections"></a>Exemplo: revisar conexões de entrada

Examine as conexões de entrada para um conjunto de VMs.

- Os registros na tabela para métricas de conexão (VMConnection) não representam conexões de rede física individuais.
- Várias conexões de rede física são agrupadas em uma conexão lógica.
- [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) sobre como os dados de ligação física da rede são agregados em VMConnection.

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="sample-summarize-sent-and-received-data"></a>Exemplo: resumir os dados enviados e recebidos

Este exemplo resume o volume de dados enviados e recebidos em conexões de entrada entre um conjunto de computadores.

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>Passos seguintes

[Crie uma avaliação](how-to-create-assessment.md) para um grupo.
