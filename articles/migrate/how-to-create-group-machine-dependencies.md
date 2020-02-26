---
title: Cria ção de visualização de dependência em Azure Migrate
description: Descreve a visualização da dependência em Azure Migrate Server Assessment.
ms.topic: article
ms.date: 2/24/2020
ms.openlocfilehash: f5b0682adf345681c03bb6dbf14e9a0d9ba1fd58
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589072"
---
# <a name="set-up-dependency-visualization"></a>Configurar a visualização da dependência

Este artigo descreve como configurar a visualização da dependência em Azure Migrate:Server Assessment. [A visualização da dependência](concepts-dependency-visualization.md#what-is-dependency-visualization) ajuda-o a identificar e compreender dependências entre máquinas que pretende avaliar e migrar para Azure.

## <a name="before-you-start"></a>Antes de começar

- [Reveja](concepts-dependency-visualization.md) os requisitos e custos associados à visualização da dependência.
- Certifique-se de [ter criado](how-to-add-tool-first-time.md) um projeto Azure Migrate.
- Se já criou um projeto, certifique-se de ter [adicionado](how-to-assess.md) a ferramenta de avaliação do servidor Azure Migrate:Server.
- Certifique-se de que montou um [aparelho Azure Migrate](migrate-appliance.md) para descobrir as suas máquinas no local. Aprenda a configurar um aparelho para [VMware](how-to-set-up-appliance-vmware.md) ou [Hyper-V](how-to-set-up-appliance-hyper-v.md). O aparelho descobre máquinas no local e envia dados de metadados e desempenho para a Avaliação do Servidor Azure Migrate.Server.
- Para utilizar a visualização da dependência, associa um espaço de [trabalho log Analytics](../azure-monitor/platform/manage-access.md) a um projeto Azure Migrate:
    - Certifique-se de que tem um espaço de trabalho na subscrição que contém o projeto Azure Migrate.
    - O espaço de trabalho deve residir nas regiões leste dos EUA, sudeste asiático ou na Europa Ocidental. Espaços de trabalho noutras regiões não podem ser associados a um projeto.
    - O espaço de trabalho deve estar numa região em que o Mapa de [Serviços é apoiado.](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)
    - Pode associar um novo ou existente espaço de trabalho log analytics com um projeto Azure Migrate.
    - Anexa-se o espaço de trabalho na primeira vez que cria a visualização da dependência de uma máquina. O espaço de trabalho para um projeto Azure Migrate não pode ser modificado depois de adicionado.
    - No Log Analytics, o espaço de trabalho associado ao Azure Migrate está marcado com a chave do Projeto migração e o nome do projeto.

- Só é possível anexar um espaço de trabalho depois de descobrir máquinas no projeto Azure Migrate. Pode fazê-lo configurando um aparelho Azure Migrate para [VMware](how-to-set-up-appliance-vmware.md) ou [Hyper-V](how-to-set-up-appliance-hyper-v.md). O aparelho descobre máquinas no local e envia dados de metadados e desempenho para o Azure Migrate: Server Assessment. [Saiba mais](migrate-appliance.md).

## <a name="associate-a-workspace"></a>Associar um espaço de trabalho

1. Depois de ter descoberto máquinas para avaliação, em **Servidores** > **Avaliação de Migração Azure:Servidor**, clique na **visão geral**.  
2. Em **Azure Migrate:Server Assessment**, clique em **Essencial .**
3. No **workspace OMS,** clique requer **configuração**.

     ![Configure log Analytics espaço de trabalho](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. No espaço de **trabalho Configure OMS,** especifique se pretende criar um novo espaço de trabalho ou utilizar um existente.
    - Pode selecionar um espaço de trabalho existente a partir de todos os espaços de trabalho na subscrição do projeto migratório.
    - Precisa do acesso do Leitor ao espaço de trabalho para o associar.
5. Se criar um novo espaço de trabalho, selecione um local para o mesmo.

    ![Adicione um novo espaço de trabalho](./media/how-to-create-group-machine-dependencies/workspace.png)


## <a name="download-and-install-the-vm-agents"></a>Transferir e instalar os agentes da VM

Em cada máquina que queira analisar, instale os agentes.

> [!NOTE]
    > Para máquinas monitorizadas pelo System Center Operations Manager 2012 R2 ou posteriormente, não precisa de instalar o agente MMA. O Mapa de Serviçointegra-se ao Gestor de Operações. [Siga esta](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites) orientação de integração.

1. Em **Azure Migrate: Avaliação do servidor,** clique em **servidores descobertos**.
2. Para cada máquina pretende analisar com visualização de dependência, na coluna **Dependencies,** clique requer **instalação do agente**.
3. Na página **Dependencies,** descarregue o agente MMA e Dependency para Windows ou Linux.
4. Sob o **agente Configure MMA, copie**o ID do espaço de trabalho e a chave. Precisa disto quando instalar o agente mma.

    ![Instale os agentes](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>Instalar o MMA

Instale o MMA em cada windows ou máquina Linux que queira analisar.

### <a name="install-mma-on-a-windows-machine"></a>Instale MMA numa máquina do Windows

Para instalar o agente numa máquina do Windows:

1. Faça duplo clique no agente transferido.
2. Na página **Bem-vindo**, clique em **Seguinte**. Na página **Termos de Licenciamento**, clique em **Concordo** para aceitar a licença.
3. Na **pasta destino,** guarde ou modifique a pasta de instalação predefinida > **Seguinte**.
4. Nas opções de configuração do **agente,** selecione **Azure Log Analytics** > **Seguinte**.
5. Clique em **Adicionar** para adicionar um novo espaço de trabalho log Analytics. Colhe no ID do espaço de trabalho e na chave que copiou do portal. Clique em **Seguinte**.

Pode instalar o agente a partir da linha de comando ou utilizar um método automatizado como O Gestor de Configuração ou [Intigua](https://go.microsoft.com/fwlink/?linkid=2104196).
- [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) sobre a utilização destes métodos para instalar o agente MMA.
- O agente MMA também pode ser instalado com este [script](https://go.microsoft.com/fwlink/?linkid=2104394).
- [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) sobre os sistemas operativos Windows suportados pelo MMA.

### <a name="install-mma-on-a-linux-machine"></a>Instale MMA numa máquina Linux

Para instalar o MMA numa máquina Linux:

1. Transfira o pacote apropriado (x86 ou x64) para o seu computador Linux utilizando scp/sftp.
2. Instale o pacote utilizando o argumento --instalação.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Saiba mais](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#supported-linux-operating-systems) sobre a lista de suporte de sistemas operativos Linux por MMA. 

## <a name="install-the-dependency-agent"></a>Instalar o agente de Dependência

1. Para instalar o agente Dependency numa máquina Windows, clique duas vezes no ficheiro de configuração e siga o assistente.
2. Para instalar o agente Dependency numa máquina Linux, instale como raiz utilizando o seguinte comando:

    ```sh InstallDependencyAgent-Linux64.bin```

- [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-hybrid-cloud#installation-script-examples) sobre como pode usar scripts para instalar o agente Dependency.
- [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#supported-operating-systems) sobre os sistemas operativos suportados pelo agente Dependency.


## <a name="create-a-group-using-dependency-visualization"></a>Criar um grupo usando a visualização da dependência

Agora crie um grupo para avaliação. 


> [!NOTE]
> Grupos para os quais se quer visualizar dependências não devem conter mais de 10 máquinas. Se tiver mais de 10 máquinas, divida-as em grupos mais pequenos.

1. Em **Azure Migrate: Avaliação do servidor,** clique em **servidores descobertos**.
2. Na coluna **Dependencies,** clique em **Ver dependências** para cada máquina que pretende rever.
3. No mapa da dependência, pode ver o seguinte:
    - Ligações TCP de entrada (clientes) e saída (servidores) de e para a máquina.
    - Máquinas dependentes que não têm os agentes de dependência instalados são agruparadas por números de porta.
    - As máquinas dependentes com agentes de dependência instalados são mostradas como caixas separadas.
    - Processos a correr dentro da máquina. Expanda cada caixa de máquinas para ver os processos.
    - Propriedades da máquina (incluindo FQDN, sistema operativo, endereço MAC). Clique em cada caixa de máquinas para ver os detalhes.

4. Pode olhar para dependências durante diferentes períodos de tempo clicando na duração do tempo na etiqueta de intervalo de tempo.
    - Por defeito, o intervalo é de uma hora. 
    - Pode modificar o intervalo de tempo, ou especificar datas de início e fim e duração.
    - O intervalo de tempo pode chegar a uma hora. Se necessitar de um alcance mais longo, utilize o Monitor Azure para consultar dados dependentes por um período mais longo.

5. Depois de identificar as máquinas dependentes que pretende agrupar, use Ctrl+Click para selecionar várias máquinas no mapa e clique em **máquinas de grupo**.
6. Especifique um nome de grupo.
7. Verifique se as máquinas dependentes são descobertas pela Azure Migrate.

    - Se uma máquina dependente não for descoberta pela Azure Migrate: Server Assessment, não pode adicioná-la ao grupo.
    - Para adicionar uma máquina, volte a fazer a descoberta e verifique se a máquina foi descoberta.

8. Se quiser criar uma avaliação para este grupo, selecione a caixa de verificação para criar uma nova avaliação para o grupo.
8. Clique **em OK** para salvar o grupo.

Depois de criar o grupo, recomendamos que instale agentes em todas as máquinas do grupo e, em seguida, visualize dependências para todo o grupo.

## <a name="query-dependency-data-in-azure-monitor"></a>Dados de dependência de consulta no Monitor Azure

Pode consultar dados de dependência capturados pelo Service Map no espaço de trabalho log Analytics associado ao projeto Azure Migrate. O Log Analytics é usado para escrever e executar consultas de registo do Monitor Azure.

- [Saiba como](../azure-monitor/insights/service-map.md#log-analytics-records) procurar dados do Mapa de Serviços no Log Analytics.
- [Obtenha uma visão geral](../azure-monitor/log-query/get-started-queries.md) da escrita de consultas de registo no [Log Analytics](../azure-monitor/log-query/get-started-portal.md).

Executar uma consulta para dados de dependência da seguinte forma:

1. Depois de instalar os agentes, vá ao portal e clique em **Visão Geral**.
2. Em **Azure Migrate: Avaliação do servidor,** clique na **visão geral**. Clique na seta para baixo para expandir o **Essencial**.
3. No **Workspace OMS,** clique no nome do espaço de trabalho.
3. Na página do espaço de trabalho Log Analytics > **General,** clique em **Registos**.
4. Escreva a sua consulta e clique em **Executar**.

### <a name="sample-queries"></a>Amostras de consultas

Aqui estão algumas consultas de amostra que pode usar para extrair dados de dependência.

- Pode modificar as consultas para extrair os seus pontos de dados preferidos.
- [Reveja](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) uma lista completa dos registos de dados de dependência.
- [Reveja](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches) as consultas adicionais de amostra.

#### <a name="sample-review-inbound-connections"></a>Amostra: Rever as ligações de entrada

Reveja as ligações de entrada para um conjunto de VMs.

- Os registos na tabela para métricas de ligação (VMConnection) não representam ligações físicas individuais da rede.
- Várias ligações físicas de rede são agruparadas numa ligação lógica.
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

#### <a name="sample-summarize-sent-and-received-data"></a>Amostra: Resumo enviado e recebido dados

Esta amostra resume o volume de dados enviados e recebidos nas ligações de entrada entre um conjunto de máquinas.

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


