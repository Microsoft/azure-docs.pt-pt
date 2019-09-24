---
title: Agrupar computadores usando dependências de computador com migrações para Azure | Microsoft Docs
description: Descreve como criar uma avaliação usando dependências de computador com o serviço de migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/17/2019
ms.author: hamusa
ms.openlocfilehash: 8e793891ea646ae8c91077ead36be9b84c1b08c8
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71200195"
---
# <a name="set-up-dependency-visualization-for-assessment"></a>Configurar a visualização de dependência para avaliação

Este artigo descreve como configurar o mapeamento de dependência nas migrações para Azure: Avaliação do Servidor.

O mapeamento de dependência ajuda a Visualizar dependências entre computadores que você deseja avaliar e migrar.

- Nas migrações para Azure: Avaliação do servidor, você reúne computadores para avaliação. Geralmente, os computadores que você deseja migrar juntos.
- Normalmente, você usa o mapeamento de dependência quando deseja avaliar grupos com níveis de confiança mais altos.
- O mapeamento de dependência ajuda a verificar dependências entre computadores antes de executar uma avaliação e migração.
- Mapear e Visualizar dependências ajuda a planejar efetivamente sua migração para o Azure. Ele ajuda a garantir que nada seja deixado para trás, evitando interrupções surpresa durante a migração.
- Usando o mapeamento, você pode descobrir sistemas interdependentes que precisam ser migrados juntos. Você também pode identificar se um sistema em execução ainda está atendendo aos usuários ou é um candidato para encerramento em vez de migração.

[Saiba mais](concepts-dependency-visualization.md#how-does-it-work) sobre a visualização de dependência.

## <a name="before-you-start"></a>Antes de começar

- Certifique-se de ter [criado](how-to-add-tool-first-time.md) um projeto de migrações para Azure.
- Se você já tiver criado um projeto, certifique-se de ter [adicionado](how-to-assess.md) a migração do Azure: Ferramenta de avaliação do servidor.
- Verifique se você descobriu seus computadores nas migrações para Azure; Você pode fazer isso Configurando um dispositivo de migrações para Azure para [VMware](how-to-set-up-appliance-vmware.md) ou [Hyper-V](how-to-set-up-appliance-hyper-v.md). O dispositivo descobre computadores locais e envia metadados e dados de desempenho para migrações para o Azure: Avaliação do Servidor. [Saiba mais](migrate-appliance.md).


**Funcionalidades** | **Nota**
--- | ---
Disponibilidade | A visualização de dependência não está disponível no Azure governamental.
Service Map | A visualização de dependência usa Mapa do Serviço solução no Azure Monitor. [Mapa do serviço](../azure-monitor/insights/service-map.md) descobre e mostra automaticamente as conexões entre os servidores.
Agentes | Para usar a visualização de dependência, instale os seguintes agentes nos computadores que você deseja mapear:<br/> - Agente de [agente log Analytics](../azure-monitor/platform/log-analytics-agent.md) (anteriormente conhecido como Microsoft Monitoring Agent (MMA).<br/> - [Mapa do serviço agente de dependência](../azure-monitor/insights/vminsights-enable-overview.md#the-microsoft-dependency-agent).<br/><br/> Para automatizar a instalação do agente, você pode usar uma ferramenta de implantação, como System Center Configuration Manager ou uma ferramenta de parceiro, como [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration), que tem uma solução de implantação de agente para migrações para Azure.
Dependency Agent | Examine o [suporte do agente de dependência](../azure-monitor/insights/vminsights-enable-overview.md#the-microsoft-dependency-agent) para Windows e Linux.<br/><br/> [Saiba mais](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples) sobre como usar scripts para instalar o Dependency Agent.
Agente de Log Analytics (MMA) | [Saiba mais](../azure-monitor/platform/log-analytics-agent.md#install-and-configure-agent) sobre os métodos de instalação do MMA.<br/><br/> Para computadores monitorados pelo System Center Operations Manager 2012 R2 ou posterior, você não precisa instalar o agente do MMA. Mapa do Serviço integra-se com Operations Manager. Você pode habilitar a integração usando as diretrizes [aqui](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites). No entanto, observe que o agente de dependência precisará ser instalado nesses computadores.<br/><br/> [Examine](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) os sistemas operacionais Linux com suporte no agente de log Analytics.
Grupos de avaliação | Os grupos para os quais você deseja visualizar dependências não devem conter mais de 10 máquinas. Se você tiver mais de 10 máquinas, divida-as em grupos menores para visualizar as dependências.

## <a name="associate-a-log-analytics-workspace"></a>Associar um espaço de trabalho do Log Analytics

Para usar a visualização de dependência, você precisa associar um [espaço de trabalho log Analytics](../azure-monitor/platform/manage-access.md) a um projeto de migrações para Azure.

- Você pode anexar um espaço de trabalho somente na assinatura do projeto de migrações para Azure.
- Você pode anexar um espaço de trabalho existente ou criar um novo.
- Você anexa o espaço de trabalho na primeira vez em que configura a visualização de dependência para um computador.
- Você pode anexar um espaço de trabalho somente após descobrir computadores no projeto de migrações para Azure. Você pode fazer isso Configurando um dispositivo de migrações para Azure para [VMware](how-to-set-up-appliance-vmware.md) ou [Hyper-V](how-to-set-up-appliance-hyper-v.md). O dispositivo descobre computadores locais e envia metadados e dados de desempenho para migrações para o Azure: Avaliação do Servidor. [Saiba mais](migrate-appliance.md).

Anexe um espaço de trabalho da seguinte maneira:

1. Nas **migrações para Azure: Avaliação**do servidor, clique em **visão geral**. Se você ainda não tiver adicionado a ferramenta de avaliação do servidor, [faça isso primeiro](how-to-assess.md).
2. Em **visão geral**, clique na seta para baixo para expandir o **Essentials**.
3. No **espaço de trabalho do OMS**, clique em **requer configuração**.
4. Em **Configurar espaço de trabalho**, especifique se deseja criar um novo espaço de trabalho ou usar um existente:

    ![Adicionar área de trabalho](./media/how-to-create-group-machine-dependencies/workspace.png)

    - Depois de especificar um nome para um novo espaço de trabalho, você pode escolher a [região](https://azure.microsoft.com/global-infrastructure/regions/) na qual o espaço de trabalho será criado.
    - Ao anexar um espaço de trabalho existente, você pode escolher entre todos os espaços de trabalho disponíveis na mesma assinatura que o projeto de migração.
    - Você precisa de acesso de leitor ao espaço de trabalho para poder anexá-lo.
    - Você não pode modificar o espaço de trabalho associado a um projeto depois que ele é anexado.

## <a name="download-and-install-the-vm-agents"></a>Transferir e instalar os agentes da VM

Baixe e instale os agentes em cada computador local que você deseja visualizar com o mapeamento de dependência.

1. Nas **migrações para Azure: Avaliação**do servidor, clique em **servidores**descobertos.
2. Para cada computador para o qual você deseja usar a visualização de dependência, clique em **requer instalação do agente**.
3. Na página **dependências** de um computador > **Baixe e instale o MMA**, baixe o agente apropriado e instale-o conforme descrito abaixo.
4. Em **baixar e instalar o agente de dependência**, baixe o agente apropriado e instale-o conforme descrito abaixo.
5. Em **Configurar agente do MMA**, copie a ID e a chave do espaço de trabalho. Você precisará delas quando instalar o agente do MMA.

### <a name="install-the-mma"></a>Instalar o MMA

#### <a name="install-the-agent-on-a-windows-machine"></a>Instalar o agente em um computador Windows

Para instalar o agente em um computador Windows:

1. Faça duplo clique no agente transferido.
2. Na página **Bem-vindo**, clique em **Seguinte**. Na página **Termos de Licenciamento**, clique em **Concordo** para aceitar a licença.
3. Em **pasta de destino**, mantenha ou modifique a pasta de instalação padrão > **Avançar**.
4. Em **Opções de instalação do agente**, selecione **Azure log Analytics** > **Avançar**.
5. Clique em **Adicionar** para adicionar um novo espaço de trabalho log Analytics. Cole a ID do espaço de trabalho e a chave que você copiou do Portal. Clique em **Seguinte**.

#### <a name="install-the-agent-on-a-linux-machine"></a>Instalar o agente em um computador Linux

Para instalar o agente em um computador Linux:

1. Transfira o pacote apropriado (x86 ou x64) para seu computador Linux usando o SCP/SFTP.
2. Instale o pacote usando o argumento--install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

### <a name="install-the-dependency-agent"></a>Instalar o agente de Dependência
1. Para instalar o Dependency Agent em um computador Windows, clique duas vezes no arquivo de instalação e siga o assistente.
2. Para instalar o Dependency Agent em um computador Linux, instale como raiz usando o seguinte comando:

    ```sh InstallDependencyAgent-Linux64.bin```

## <a name="create-a-group-using-dependency-visualization"></a>Criar um grupo usando a visualização de dependência

1. Nas **migrações para Azure: Avaliação**do servidor, clique em **servidores**descobertos.
2. Na coluna **dependências** , clique em **Exibir dependências** para cada computador que você deseja examinar.
3. No mapa de dependências, você pode ver o seguinte:
    - Conexões TCP de entrada (clientes) e de saída (servidores), de e para o computador.
    - Os computadores dependentes que não têm os agentes de dependência instalados são agrupados por números de porta.
    - Os computadores dependentes com agentes de dependência instalados são mostrados como caixas separadas.
    - Processos em execução dentro da máquina. Expanda cada caixa da máquina para exibir os processos.
    - Propriedades da máquina (incluindo FQDN, sistema operacional, endereço MAC). Clique em cada caixa da máquina para exibir os detalhes.

4. Você pode examinar as dependências de diferentes durações de tempo clicando no tempo duração no rótulo de intervalo de tempo. Por padrão, o intervalo é de uma hora. Você pode modificar o intervalo de tempo ou especificar datas de início e de término e duração.

    > [!NOTE]
    > O intervalo de tempo pode ser de até uma hora. Se você precisar de um intervalo mais longo, use Azure Monitor para consultar dados dependentes por um período mais longo.

5. Depois de identificar os computadores dependentes que você deseja agrupar, use Ctrl + clique para selecionar vários computadores no mapa e clique em **Agrupar computadores**.
6. Especifique um nome de grupo.
7. Verifique se os computadores dependentes são descobertos pelas migrações para Azure.

    - Se um computador dependente não for descoberto por migrações para Azure: Avaliação do servidor, você não pode adicioná-lo ao grupo.
    - Para adicionar um computador, execute a descoberta novamente e verifique se o computador foi descoberto.

8. Se você quiser criar uma avaliação para esse grupo, marque a caixa de seleção para criar uma nova avaliação para o grupo.
8. Clique em **OK** para salvar o grupo.

Depois de criar o grupo, recomendamos que você instale agentes em todos os computadores do grupo e, em seguida, visualize as dependências de todo o grupo.

## <a name="query-dependency-data-in-azure-monitor"></a>Dados de dependência de consulta em Azure Monitor

Você pode consultar dados de dependência capturados por Mapa do Serviço no espaço de trabalho Log Analytics associado ao projeto de migrações para Azure. Log Analytics é usado para gravar e executar Azure Monitor consultas de log.

- [Saiba como](../azure-monitor/insights/service-map.md#log-analytics-records) procurar mapa do serviço dados no log Analytics.
- [Obtenha uma visão geral](../azure-monitor/log-query/get-started-queries.md) da gravação de consultas de log no [log Analytics](../azure-monitor/log-query/get-started-portal.md).

Execute uma consulta para dados de dependência da seguinte maneira:

1. Depois de instalar os agentes, acesse o portal e clique em **visão geral**.
2. Nas **migrações para Azure: Avaliação**do servidor, clique em **visão geral**. Clique na seta para baixo para expandir o **Essentials**.
3. No **espaço de trabalho do OMS**, clique no nome do espaço de trabalho.
3. Na página Log Analytics espaço de trabalho > **geral**, clique em **logs**.
4. Escreva sua consulta e clique em **executar**.

### <a name="sample-queries"></a>Amostras de consultas

Fornecemos um número de consultas de exemplo que você pode usar para extrair dados de dependência.

- Você pode modificar as consultas para extrair seus pontos de dados preferenciais.
- [Examine](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) uma lista completa de registros de dados de dependência.
- [Examine](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches) as consultas de exemplo adicionais.

#### <a name="sample-review-inbound-connections"></a>Exemplo: Revisar conexões de entrada

Examine as conexões de entrada para um conjunto de VMs.

- Os registros na tabela para métricas de conexão (VMConnection) não representam conexões de rede física individuais.
- Várias conexões de rede física são agrupadas em uma conexão lógica.
- [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections) sobre como os dados de conexão de rede física são agregados no VMConnection.

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

#### <a name="sample-summarize-sent-and-received-data"></a>Exemplo: Resumir dados enviados e recebidos

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
