---
title: Agrupar máquinas através de dependências de máquina com o Azure Migrate | Documentos da Microsoft
description: Descreve como criar uma avaliação com as dependências das máquinas com o serviço Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: af47678b19209936aed86c132a8a3f400c3a7e8f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60596764"
---
# <a name="group-machines-using-machine-dependency-mapping"></a>Agrupar máquinas através de mapeamento de dependências de máquina

Este artigo descreve como criar um grupo de máquinas para [do Azure Migrate](migrate-overview.md) avaliação ao visualizar as dependências de máquinas. Geralmente usa esse método quando pretender avaliar a grupos de VMs com níveis mais altos de confiança por verificar as dependências das máquinas, antes de executar uma avaliação. Visualização de dependência pode ajudar a planear efetivamente a migração para o Azure. Ele ajuda a garantir que nada seja deixado e não ocorrerem falhas de surpresa quando estiver a migrar para o Azure. Pode descobrir todos os sistemas interdependentes que precisam migrar em conjunto e identificar se um sistema em execução ainda está a servir os utilizadores ou é uma candidata para desativar em vez de migração.

> [!NOTE]
> A funcionalidade de visualização de dependência não está disponível no Azure Government.

## <a name="prepare-for-dependency-visualization"></a>Preparar para a visualização de dependências
O Azure Migrate tira partido da solução mapa de serviço nos registos do Azure Monitor para ativar a visualização de dependências de máquinas.

### <a name="associate-a-log-analytics-workspace"></a>Associar uma área de trabalho do Log Analytics
Para aproveitar a visualização de dependências, precisa associar área de trabalho do Log Analytics, nova ou existente, com um projeto do Azure Migrate. Só pode criar ou anexar uma área de trabalho na mesma subscrição em que é criado o projeto de migração.

- Para anexar uma área de trabalho do Log Analytics a um projeto, no **descrição geral**, aceda a **Essentials** secção do projeto clique **requer configuração**

    ![Associar área de trabalho do Log Analytics](./media/concepts-dependency-visualization/associate-workspace.png)

- Ao associar uma área de trabalho, obterá a opção para criar uma nova área de trabalho ou anexar um já existente:
  - Quando cria uma nova área de trabalho, tem de especificar um nome para a área de trabalho. A área de trabalho, em seguida, é criada numa região na mesma [geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) como o projeto de migração.
  - Quando anexa uma área de trabalho existente, pode escolher entre todas as áreas de trabalho disponíveis na mesma subscrição que o projeto de migração. Tenha em atenção que apenas essas áreas de trabalho estão listadas que foram criados numa região onde [mapa de serviço é suportado](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions). Para poder ligar uma área de trabalho, certifique-se de que tem acesso de "Reader" para a área de trabalho.

> [!NOTE]
> Não é possível alterar a área de trabalho associada a um projeto de migração.

### <a name="download-and-install-the-vm-agents"></a>Transferir e instalar os agentes da VM
Depois de configurar uma área de trabalho, terá de transferir e instalar agentes em cada máquina no local que pretende avaliar. Além disso, se tiver máquinas sem conectividade internet, terá de transferir e instalar [gateway do Log Analytics](../azure-monitor/platform/gateway.md) nos mesmos.

1. Na **descrição geral**, clique em **gerir** > **máquinas**e selecione a máquina necessária.
2. Na **dependências** coluna, clique em **instalar agentes**.
3. Sobre o **dependências** página, transferir e instalar o Microsoft Monitoring Agent (MMA) e o agente de dependência em cada VM que pretende avaliar.
4. Copie o ID e a chave da área de trabalho. Vai precisar de ambos quando instalar o MMA na máquina no local.

> [!NOTE]
> Para automatizar a instalação de agentes pode utilizar qualquer ferramenta de implementação, como o System Center Configuration Manager ou utilizar a nossa ferramenta de parceiros [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration), que tem uma solução de implantação do agente para o Azure Migrate.

### <a name="install-the-mma"></a>Instalar o MMA

#### <a name="install-the-agent-on-a-windows-machine"></a>Instalar o agente num computador Windows

Para instalar o agente num computador Windows:

1. Faça duplo clique no agente transferido.
2. Na página **Bem-vindo**, clique em **Seguinte**. Na página **Termos de Licenciamento**, clique em **Concordo** para aceitar a licença.
3. Na **pasta de destino**, manter ou modificar a pasta de instalação predefinida > **próxima**.
4. Na **opções de configuração do agente**, selecione **Azure Log Analytics** > **seguinte**.
5. Clique em **adicionar** para adicionar uma nova área de trabalho do Log Analytics. Cole o ID de área de trabalho e a chave que copiou do portal. Clique em **Seguinte**.

Pode instalar o agente a partir da linha de comando ou usando um método automatizado, como o System Center Configuration Manager. [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) sobre como utilizar estes métodos para instalar o agente MMA.

#### <a name="install-the-agent-on-a-linux-machine"></a>Instalar o agente num computador com Linux

Para instalar o agente num computador Linux:

1. Transfira o pacote adequado (x86 ou x64) para o seu computador Linux utilizar o scp/sftp.
2. Instalar o pacote utilizando o argumento-- instalação.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Saiba mais](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) sobre a lista de suporte de sistemas operativos Linux por MMA.

#### <a name="install-the-agent-on-a-machine-monitored-by-scom"></a>Instalar o agente num computador monitorizado pelo SCOM

Para computadores monitorizados pelo System Center Operations Manager 2012 R2 ou posterior, não é necessário para instalar o agente MMA. Mapa de serviço tem uma integração com SCOM que aproveita o MMA do SCOM para recolher os dados de dependência necessária. Pode ativar a integração com a orientação de utilização [aqui](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites). No entanto, observe que o agente de dependência tem instalado nessas máquinas.


### <a name="install-the-dependency-agent"></a>Instalar o agente de Dependência
1. Para instalar o agente de dependência num computador Windows, clique duas vezes o ficheiro de configuração e siga o assistente.
2. Para instalar o agente de dependência numa máquina Linux, instale como raiz com o seguinte comando:

    ```sh InstallDependencyAgent-Linux64.bin```

Saiba mais sobre o suporte de agente de dependência para o [Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) e [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems) sistemas operativos.

[Saiba mais](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) sobre como pode utilizar scripts para instalar o agente de dependência.


## <a name="create-a-group"></a>Criar um grupo

1. Depois de instalar os agentes, vá para o portal e clique em **Manage** > **máquinas**.
2. Pesquisa para a máquina em que instalou os agentes.
3. O **dependências** coluna para a máquina deve agora mostrar como **ver dependências**. Clique na coluna para ver as dependências da máquina.
4. O mapa de dependência para a máquina mostra os seguintes detalhes:
    - Entrada (clientes) e as ligações de saída (servidores) TCP de/para a máquina
        - As máquinas dependentes que não têm o agente MMA e de dependência instalado são agrupadas por números de porta
        - As máquinas dependentes que têm o MMA e instalado o agente de dependência são apresentadas como caixas separadas
    - Processos em execução no interior da máquina, pode expandir cada caixa de máquina para ver os processos
    - Propriedades como nome de domínio completamente qualificado, sistema operativo, etc. do endereço MAC de cada máquina, pode clicar em cada caixa de máquina para ver estes detalhes

      ![Ver as dependências das máquinas](./media/how-to-create-group-machine-dependencies/machine-dependencies.png)

4. Pode ver as dependências para diferentes horas de duração clicando-se na duração de tempo na etiqueta de intervalo de tempo. Por predefinição, o intervalo é uma hora. Pode modificar o intervalo de tempo ou especificar o início e datas de término e duração.

   > [!NOTE]
   >    Atualmente, a visualização de dependência da interface do Usuário não suporta a seleção de um intervalo de tempo mais de uma hora. Utilização do Azure Monitor regista [consultar os dados de dependência](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) ao longo de um período mais longo.

5. Depois de identificar as máquinas dependentes que pretende agrupar, utilize Ctrl + clique para selecionar várias máquinas no mapa e clique em **agrupar máquinas**.
6. Especifique um nome de grupo. Certifique-se de que as máquinas dependentes são detetadas pelo Azure Migrate.

    > [!NOTE]
    > Se uma máquina dependente não for detetada pelo Azure Migrate, não é possível adicioná-lo ao grupo. Para adicionar essas máquinas ao grupo, terá de executar o processo de deteção novamente com o âmbito correto no vCenter Server e certifique-se de que a máquina é detetada pelo Azure Migrate.  

7. Se quiser criar uma avaliação para este grupo, selecione a caixa de verificação para criar uma nova avaliação para o grupo.
8. Clique em **OK** para guardar o grupo.

Assim que o grupo for criado, recomenda-se para instalar agentes em todas as máquinas do grupo e refinar o grupo ao visualizar a dependência de grupo inteiro.

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Consultar dados de dependência de logs de Monitor do Azure

Dados de dependência capturados pelo mapa de serviço estão disponíveis para consulta na área de trabalho do Log Analytics associada ao projeto do Azure Migrate. [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) sobre as tabelas de dados de mapa de serviço para consultar no Azure Monitor registos. 

Para executar as consultas de Kusto:

1. Depois de instalar os agentes, vá para o portal e clique em **descrição geral**.
2. No **descrição geral**, aceda à **Essentials** secção do projeto e clique no nome de área de trabalho fornecido junto a **área de trabalho OMS**.
3. Na página de área de trabalho do Log Analytics, clique em **gerais** > **registos**.
4. Escreva a consulta para recolher dados de dependência com os registos do Azure Monitor. Localize consultas de exemplo na secção seguinte.
5. Execute a consulta ao clicar em execução. 

[Saiba mais](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) sobre como escrever consultas do Kusto. 

### <a name="sample-azure-monitor-logs-queries"></a>Consultas de registos de exemplo do Azure Monitor

Seguem-se exemplos de consultas que pode utilizar para extrair dados de dependência. Pode modificar as consultas para extrair os pontos de dados preferencial. Está disponível uma lista exaustiva dos campos nos registos de dados de dependência [aqui](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records). Encontre mais de exemplos de consultas [aqui](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches).

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Resumir as conexões de entrada num conjunto de máquinas

Tenha em atenção que os registos na tabela de métricas de ligação, VMConnection, não representam conexões de rede físico individuais. Várias ligações de rede física são agrupadas numa conexão lógica. [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) sobre a ligação de rede físico como os dados são agregados num único registo lógico na VMConnection. 

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

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>Resumir o volume de dados enviados e recebidos em ligações de entrada entre um conjunto de máquinas

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

## <a name="next-steps"></a>Passos Seguintes

- [Saiba mais](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization) sobre as perguntas frequentes sobre a visualização de dependência.
- [Saiba como](how-to-create-group-dependencies.md) para refinar o grupo ao visualizar as dependências de grupo.
- [Saiba mais](concepts-assessment-calculation.md) sobre como são calculadas as avaliações.
