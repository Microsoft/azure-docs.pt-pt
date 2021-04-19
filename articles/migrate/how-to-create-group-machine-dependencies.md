---
title: Configurar análise de dependência baseada em agente na Azure Migrate
description: Este artigo descreve como configurar a análise de dependência baseada em agentes em Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 6ded5d4ed8c2a55939bba908a05adbd2dea2ccbf
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714781"
---
# <a name="set-up-dependency-visualization"></a>Configurar visualização de dependência

Este artigo descreve como configurar a análise de dependência baseada em agentes em Azure Migrate: Discovery and assessment. [A análise da dependência](concepts-dependency-visualization.md) ajuda-o a identificar e a compreender dependências através de servidores que pretende avaliar e migrar para Azure.

## <a name="before-you-start"></a>Antes de começar

- Reveja os requisitos de suporte e de implantação para a análise da dependência baseada no agente para:
    - [Servidores em ambiente VMware](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agent-based)
    - [Servidores físicos](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)
    - [Servidores em ambiente Hiper-V](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)
- Certifique-se de:
    - Tenha um projeto Azure Migrate. Se não o fizeres, [cria](./create-manage-projects.md) um agora.
    - Verifique se [adicionou](how-to-assess.md) a ferramenta Azure Migrate: Discovery e assessment ao projeto.
    - Crie um [aparelho Azure Migrate](migrate-appliance.md) para descobrir servidores no local. O aparelho descobre servidores no local e envia metadados e dados de desempenho para Azure Migrate: Descoberta e avaliação. Configurar um aparelho para:
        - [Servidores em ambiente VMware](how-to-set-up-appliance-vmware.md)
        - [Servidores em ambiente Hiper-V](how-to-set-up-appliance-hyper-v.md)
        - [Servidores físicos](how-to-set-up-appliance-physical.md)
- Para utilizar a visualização da dependência, associa um [espaço de trabalho log Analytics](../azure-monitor/logs/manage-access.md) a um projeto Azure Migrate:
    - Só pode anexar um espaço de trabalho depois de configurar o aparelho Azure Migrate e descobrir servidores no projeto Azure Migrate.
    - Certifique-se de que tem um espaço de trabalho na subscrição que contém o projeto Azure Migrate.
    - O espaço de trabalho deve residir nas regiões do Leste dos EUA, Sudeste Asiático ou Europa Ocidental. Espaços de trabalho noutras regiões não podem ser associados a um projeto.
    - O espaço de trabalho deve estar numa região em que [o Mapa de Serviços é suportado.](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)
    - Pode associar um novo ou existente espaço de trabalho log analytics a um projeto Azure Migrate.
    - Liga-se o espaço de trabalho da primeira vez que configura a visualização de dependência para um servidor. O espaço de trabalho para um projeto Azure Migrate não pode ser modificado depois de ser adicionado.
    - No Log Analytics, o espaço de trabalho associado ao Azure Migrate está marcado com a chave do Projeto migratório e o nome do projeto.

## <a name="associate-a-workspace"></a>Associar um espaço de trabalho

1. Depois de ter descoberto servidores para avaliação, nos **Servidores**  >  **Azure Migrate: Discovery and assessment**, clique em **Overview**.  
2. Em **Azure Migrate: Descoberta e avaliação,** clique em **Essentials**.
3. No **espaço de trabalho OMS,** clique requer **configuração**.

     ![Configurar a área de trabalho do Log Analytics](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. No **espaço de trabalho Configure OMS**, especifique se pretende criar um novo espaço de trabalho ou utilizar um existente.
    - Pode selecionar um espaço de trabalho existente a partir de todos os espaços de trabalho na subscrição do projeto.
    - Precisa do leitor ter acesso ao espaço de trabalho para o associar.
5. Se criar um novo espaço de trabalho, selecione um local para o mesmo.

    ![Adicione um novo espaço de trabalho](./media/how-to-create-group-machine-dependencies/workspace.png)

> [!Note]
> [Aprenda](https://docs.microsoft.com/azure/azure-monitor/logs/private-link-security) a configurar o espaço de trabalho OMS para a conectividade privada do ponto final.  

## <a name="download-and-install-the-vm-agents"></a>Transferir e instalar os agentes da VM

Em cada servidor que pretende analisar, instale os agentes.

> [!NOTE]
> Para servidores monitorizados pelo System Center Operations Manager 2012 R2 ou posteriormente, não é necessário instalar o agente MMA. O Mapa de Serviços integra-se com o Gestor de Operações. [Siga a](../azure-monitor/vm/service-map-scom.md#prerequisites) orientação de integração.

1. Em **Azure Migrate: Discovery and assessment**, clique em **servidores Descobertos**.
2. Para cada servidor que pretende analisar com visualização de dependência, na coluna **Dependências,** clique em **Requerer a instalação do agente**.
3. Na página **Dependências,** descarregue o MMA e o agente De dependência para Windows ou Linux.
4. Sob **o agente MMA configurado,** copie o ID do espaço de trabalho e a chave. Precisa disto quando instalar o agente MMA.

    ![Instalar os agentes](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>Instalar o MMA

Instale o MMA em cada servidor Windows ou Linux que pretende analisar.

### <a name="install-mma-on-a-windows-server"></a>Instale o MMA num servidor windows

Para instalar o agente num servidor windows:

1. Faça duplo clique no agente transferido.
2. Na página **Bem-vindo**, clique em **Seguinte**. Na página **Termos de Licenciamento**, clique em **Concordo** para aceitar a licença.
3. Na **Pasta destino**, guarde ou modifique a pasta de instalação predefinido > **Seguinte**.
4. Nas **opções de configuração do agente**, selecione **Azure Log Analytics**  >  **Next**.
5. Clique em **Adicionar** para adicionar um novo espaço de trabalho Log Analytics. Cole no iD do espaço de trabalho e na chave que copiou do portal. Clique em **Seguinte**.

Pode instalar o agente a partir da linha de comando ou utilizando um método automatizado, como o Gestor de Configurações ou [o Intigua.](https://www.intigua.com/intigua-for-azure-migration)
- [Saiba mais](../azure-monitor/agents/log-analytics-agent.md#installation-options) sobre a utilização destes métodos para instalar o agente MMA.
- O agente MMA também pode ser instalado com este [script](https://github.com/brianbar-MSFT/Install-MMA).
- [Saiba mais](../azure-monitor/agents/agents-overview.md#supported-operating-systems) sobre os sistemas operativos Windows suportados pelo MMA.

### <a name="install-mma-on-a-linux-server"></a>Instale o MMA num servidor Linux

Para instalar o MMA num servidor Linux:

1. Transfira o pacote apropriado (x86 ou x64) para o seu computador Linux utilizando scp/sftp.
2. Instale o pacote utilizando o argumento de instalação.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Saiba mais](../azure-monitor/agents/agents-overview.md#supported-operating-systems) sobre a lista de sistemas operativos Linux suportados pelo MMA. 

## <a name="install-the-dependency-agent"></a>Instalar o agente de Dependência

1. Para instalar o agente Dependency num servidor Windows, clique duas vezes no ficheiro de configuração e siga o assistente.
2. Para instalar o agente Dependency num servidor Linux, instale como raiz utilizando o seguinte comando:

    ```sh InstallDependencyAgent-Linux64.bin```

- [Saiba mais](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent) sobre como pode usar scripts para instalar o agente Dependency.
- [Saiba mais](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) sobre os sistemas operativos suportados pelo agente Dependency.


## <a name="create-a-group-using-dependency-visualization"></a>Criar um grupo que utilize a visualização da dependência

Agora crie um grupo para avaliação. 


> [!NOTE]
> Os grupos para os quais pretende visualizar dependências não devem conter mais de 10 servidores. Se tiver mais de 10 servidores, divida-os em grupos mais pequenos.

1. Em **Azure Migrate: Discovery and assessment**, clique em **servidores Descobertos**.
2. Na coluna **Dependências,** clique em **Ver As dependências** de cada servidor que pretende rever.
3. No mapa de dependência, pode ver o seguinte:
    - Ligações TCP de entrada (clientes) e saída (servidores) de e para o servidor.
    - Os servidores dependentes que não têm os agentes de dependência instalados são agrupados por números de porta.
    - Os servidores dependentes com agentes de dependência instalados são apresentados como caixas separadas.
    - Processos a correr dentro do servidor. Expandir cada caixa de servidor para ver os processos.
    - Propriedades do servidor (incluindo FQDN, sistema operativo, endereço MAC). Clique em cada caixa de servidor para ver os detalhes.

4. Pode olhar para as dependências para diferentes durações de tempo clicando na duração do tempo na etiqueta do intervalo de tempo.
    - Por defeito, o alcance é de uma hora. 
    - Pode modificar o intervalo de tempo, ou especificar datas de início e fim e duração.
    - O intervalo de tempo pode chegar a uma hora. Se precisar de um alcance mais longo, utilize o Azure Monitor para consultar dados dependentes por um período mais longo.

5. Depois de identificar os servidores dependentes que pretende agrupar, utilize o Ctrl+Click para selecionar vários servidores no mapa e clique em **máquinas de grupo**.
6. Especifique um nome de grupo.
7. Verifique se os servidores dependentes são descobertos pela Azure Migrate.

    - Se um servidor dependente não for descoberto pela Azure Migrate: Descoberta e avaliação, não é possível adicioná-lo ao grupo.
    - Para adicionar um servidor, execute a descoberta novamente e verifique se o servidor foi descoberto.

8. Se pretender criar uma avaliação para este grupo, selecione a caixa de verificação para criar uma nova avaliação para o grupo.
8. Clique **em OK** para salvar o grupo.

Depois de criar o grupo, recomendamos que instale agentes em todos os servidores do grupo e, em seguida, visualize as dependências para todo o grupo.

## <a name="query-dependency-data-in-azure-monitor"></a>Dados de dependência de consultas no Azure Monitor

Pode consultar os dados de dependência capturados pelo Mapa de Serviços no espaço de trabalho Log Analytics associado ao projeto Azure Migrate. O Log Analytics é utilizado para escrever e executar consultas de registo do Azure Monitor.

- [Saiba como](../azure-monitor/vm/service-map.md#log-analytics-records) procurar dados do Mapa de Serviços no Log Analytics.
- [Obtenha uma visão geral](../azure-monitor/logs/get-started-queries.md)  das consultas de registo de escrita no [Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md).

Executar uma consulta para os dados de dependência da seguinte forma:

1. Depois de instalar os agentes, vá ao portal e clique em **Overview**.
2. Em **Azure Migrate: Descoberta e avaliação,** clique **em Visão Geral**. Clique na seta para baixo para expandir **o Essencial.**
3. No **espaço de trabalho OMS,** clique no nome do espaço de trabalho.
3. Na página do espaço de trabalho do Log Analytics > **General**, clique em **Registars**.
4. Escreva a sua consulta e clique em **Executar**.

### <a name="sample-queries"></a>Consultas de exemplo

Aqui estão algumas consultas de amostra que pode usar para extrair dados de dependência.

- Pode modificar as consultas para extrair os seus pontos de dados preferidos.
- [Reveja](../azure-monitor/vm/service-map.md#log-analytics-records) uma lista completa de registos de dados de dependência.
- [Reveja](../azure-monitor/vm/service-map.md#sample-log-searches) as consultas adicionais de amostras.

#### <a name="sample-review-inbound-connections"></a>Amostra: Rever ligações de entrada

Reveja as ligações de entrada para um conjunto de servidores.

- Os registos na tabela para métricas de ligação (VMConnection) não representam ligações físicas individuais.
- Múltiplas ligações físicas de rede são agrupadas numa ligação lógica.
- [Saiba mais](../azure-monitor/vm/service-map.md#connections) sobre como os dados de ligação à rede física são agregados em VMConnection.

```
// the servers of interest
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

#### <a name="sample-summarize-sent-and-received-data"></a>Amostra: Resumo dos dados enviados e recebidos

Esta amostra resume o volume de dados enviados e recebidos em ligações de entrada entre um conjunto de servidores.

```
// the servers of interest
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

[Criar uma avaliação](how-to-create-assessment.md) para um grupo.
