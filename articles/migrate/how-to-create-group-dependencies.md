---
title: Refinar um grupo de avaliação com o mapeamento de dependência de grupo no Azure Migrate | Documentos da Microsoft
description: Descreve como refinar uma avaliação com o mapeamento de grupo de dependência no serviço Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: a3c0e94e213b861714bfc5f38e20e4d9a1de3a19
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58107921"
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Refinar um grupo com o mapeamento de grupo de dependência

Este artigo descreve como refinar um grupo ao visualizar as dependências de todas as máquinas no grupo. Geralmente usa esse método quando pretender refinar a associação de um grupo existente, por verificação de dependências de grupo, antes de executar uma avaliação. Refinar um grupo através de visualização de dependência pode ajudar a planear efetivamente a sua migração para o Azure. Pode descobrir todos os sistemas interdependentes que precisam para migrar em conjunto. Ele ajuda a garantir que nada seja deixado e não ocorrerem falhas de surpresa quando estiver a migrar para o Azure.


> [!NOTE]
> Grupos para o qual pretende visualizar as dependências não devem conter mais de 10 máquinas. Se tiver mais de 10 máquinas no grupo, recomendamos que dividi-lo em grupos mais pequenos para aproveitar a funcionalidade de visualização de dependência.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prepare-for-dependency-visualization"></a>Preparar para a visualização de dependências
O Azure Migrate tira partido da solução mapa de serviço nos registos do Azure Monitor para ativar a visualização de dependências de máquinas.

> [!NOTE]
> A funcionalidade de visualização de dependência não está disponível no Azure Government.

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
Para ver as dependências de um grupo, terá de transferir e instalar agentes em cada máquina no local que faz parte do grupo. Além disso, se tiver máquinas sem conectividade internet, terá de transferir e instalar [gateway do Log Analytics](../azure-monitor/platform/gateway.md) nos mesmos.

1. Na **descrição geral**, clique em **gerir** > **grupos**, vá para o grupo de necessário.
2. Na lista de máquinas, no **agente de dependência** coluna, clique em **requer instalação** para ver instruções sobre como transferir e instalar os agentes.
3. Sobre o **dependências** página, transferir e instalar o Microsoft Monitoring Agent (MMA) e o agente de dependência em cada VM que faz parte do grupo.
4. Copie o ID e a chave da área de trabalho. Vai precisar de ambos quando instalar o MMA nas máquinas no local.

### <a name="install-the-mma"></a>Instalar o MMA

#### <a name="install-the-agent-on-a-windows-machine"></a>Instalar o agente num computador Windows

Para instalar o agente num computador Windows:

1. Faça duplo clique no agente transferido.
2. Na página **Bem-vindo**, clique em **Seguinte**. Na página **Termos de Licenciamento**, clique em **Concordo** para aceitar a licença.
3. Na **pasta de destino**, manter ou modificar a pasta de instalação predefinida > **próxima**.
4. Na **opções de configuração do agente**, selecione **Azure Log Analytics** > **seguinte**.
5. Clique em **adicionar** para adicionar uma nova área de trabalho do Log Analytics. Cole o ID de área de trabalho e a chave que copiou do portal. Clique em **Seguinte**.

Pode instalar o agente a partir da linha de comando ou usando um método automatizado, como o DSC de automatização do Azure, System Center Configuration Manager, ou com um modelo Azure Resource Manager, se tiver implementado o Microsoft Azure Stack no seu datacenter. [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) sobre como utilizar estes métodos para instalar o agente MMA.

#### <a name="install-the-agent-on-a-linux-machine"></a>Instalar o agente num computador com Linux

Para instalar o agente num computador Linux:

1. Transfira o pacote adequado (x86 ou x64) para o seu computador Linux utilizar o scp/sftp.
2. Instalar o pacote utilizando o argumento-- instalação.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

#### <a name="install-the-agent-on-a-machine-monitored-by-system-center-operations-manager"></a>Instalar o agente num computador monitorizado pelo System Center Operations Manager

Para computadores monitorizados pelo Operations Manager 2012 R2 ou posterior, não é necessário para instalar o agente MMA. Mapa de serviço tem uma integração com o Operations Manager, que aproveita o MMA do Gestor de operações para recolher os dados de dependência necessária. Pode ativar a integração com a orientação de utilização [aqui](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites). No entanto, observe que o agente de dependência tem instalado nessas máquinas.

### <a name="install-the-dependency-agent"></a>Instalar o agente de Dependência
1. Para instalar o agente de dependência num computador Windows, clique duas vezes o ficheiro de configuração e siga o assistente.
2. Para instalar o agente de dependência numa máquina Linux, instale como raiz com o seguinte comando:

    ```sh InstallDependencyAgent-Linux64.bin```

Saiba mais sobre o suporte de agente de dependência para o [Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) e [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems) sistemas operativos.

[Saiba mais](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) sobre como pode utilizar scripts para instalar o agente de dependência.

## <a name="refine-the-group-based-on-dependency-visualization"></a>Refinar o grupo com base na visualização de dependências
Depois de instalar agentes em todas as máquinas do grupo, pode visualizar as dependências do grupo e refiná-la ao seguir os passos abaixo.

1. O Azure Migrate em projeto, **Manage**, clique em **grupos**e selecione o grupo.
2. Na página de grupo, clique em **ver dependências**, para abrir o mapa de dependências de grupo.
3. O mapa de dependência para o grupo mostra os seguintes detalhes:
   - Entrada (clientes) e as ligações de saída (servidores) TCP de/para todas as máquinas que fazem parte do grupo
       - As máquinas dependentes que não têm o agente MMA e de dependência instalado são agrupadas por números de porta
       - As máquinas dependentes que têm o MMA e instalado o agente de dependência são apresentadas como caixas separadas
   - Processos em execução no interior da máquina, pode expandir cada caixa de máquina para ver os processos
   - Propriedades como nome de domínio completamente qualificado, sistema operativo, etc. do endereço MAC de cada máquina, pode clicar em cada caixa de máquina para ver estes detalhes

     ![Ver dependências de grupo](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. Para ver dependências mais granulares, clique no intervalo de tempo de modificá-lo. Por predefinição, o intervalo é uma hora. Pode modificar o intervalo de tempo ou especificar o início e datas de término e duração.

   > [!NOTE]
   >    Atualmente, a visualização de dependência da interface do Usuário não suporta a seleção de um intervalo de tempo mais de uma hora. Utilização do Azure Monitor regista [consultar os dados de dependência](https://docs.microsoft.com/azure/migrate/how-to-create-a-group) ao longo de um período mais longo.

4. Verifique se as máquinas dependentes, o processo em execução dentro de cada máquina e identificar as máquinas que devem ser adicionadas ou removidas do grupo.
5. Utilize Ctrl + clique para selecionar máquinas no mapa para adicionar ou removê-los do grupo.
    - Só é possível adicionar máquinas que tenham sido detetadas.
    - Adicionar e remover máquinas de um grupo invalida anteriores avaliações para ele.
    - Opcionalmente, pode criar uma nova avaliação ao modificar o grupo.
5. Clique em **OK** para guardar o grupo.

    ![Adicionar ou remover máquinas](./media/how-to-create-group-dependencies/add-remove.png)

Se pretender verificar as dependências de uma máquina específica que aparece no mapa de dependências de grupo, a [configurar o mapeamento de dependência de máquina](how-to-create-group-machine-dependencies.md).

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Consultar dados de dependência de logs de Monitor do Azure

Dados de dependência capturados pelo mapa de serviço estão disponíveis para consulta na área de trabalho do Log Analytics associada ao projeto do Azure Migrate. [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) sobre as tabelas de dados de mapa de serviço para consultar no Azure Monitor registos. 

Para executar as consultas de Kusto:

1. Depois de instalar os agentes, vá para o portal e clique em **descrição geral**.
2. No **descrição geral**, aceda à **Essentials** secção do projeto e clique no nome de área de trabalho fornecido junto a **área de trabalho OMS**.
3. Na página de área de trabalho do Log Analytics, clique em **gerais** > **registos**.
4. Escreva a consulta para recolher dados de dependência com os registos do Azure Monitor. Consultas de exemplo para recolher dados de dependência estão disponíveis [aqui](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches).
5. Execute a consulta ao clicar em execução. 

[Saiba mais](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) sobre como escrever consultas do Kusto. 


## <a name="next-steps"></a>Passos Seguintes
- [Saiba mais](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization) sobre as perguntas frequentes sobre a visualização de dependência.
- [Saiba mais](concepts-assessment-calculation.md) sobre como são calculadas as avaliações.
