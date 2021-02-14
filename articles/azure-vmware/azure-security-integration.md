---
title: Proteja o seu VMS de Solução VMware Azure com integração do Centro de Segurança Azure
description: Proteja os VMS da Solução VMware Azure com as ferramentas de segurança nativas da Azure do painel do Centro de Segurança Azure.
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: b37d09d6e8f239586a18c0fa3b1dcd7bfee98102
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516367"
---
# <a name="protect-your-azure-vmware-solution-vms-with-azure-security-center-integration"></a>Proteja o seu VMS de Solução VMware Azure com integração do Centro de Segurança Azure

As ferramentas de segurança nativas Azure fornecem proteção para um ambiente híbrido de Azure, Azure VMware Solution e máquinas virtuais no local (VMs). Este artigo mostra-lhe como configurar ferramentas Azure para a segurança do ambiente híbrido. Usará estas ferramentas para identificar e enfrentar várias ameaças.

## <a name="azure-native-services"></a>Serviços nativos de Azure

Aqui está um resumo rápido dos serviços nativos de Azure:

- **Log Analytics espaço de trabalho:** Log Analytics workspace é um ambiente único para armazenar dados de registo. Cada área de trabalho tem o seu próprio repositório de dados e a sua própria configuração. As fontes de dados e as soluções estão configuradas para armazenar os seus dados num espaço de trabalho específico.
- **Centro de Segurança Azure:** O Azure Security Center é um sistema unificado de gestão de segurança de infraestruturas. Reforça a segurança dos centros de dados e fornece uma proteção avançada de ameaças através de cargas de trabalho híbridas na nuvem ou nas instalações.
- **Azure Sentinel:** Azure Sentinel é uma solução nativa de informação de segurança (SIEM). Fornece análise de segurança, deteção de alerta e resposta automática de ameaças em todo o ambiente.

## <a name="topology"></a>Topologia

![Um diagrama que mostra a arquitetura da segurança integrada do Azure.](media/azure-security-integration/azure-integrated-security-architecture.png)

O agente Log Analytics permite a recolha de dados de registo da Azure, Azure VMware Solution e VMs no local. Os dados de registo são enviados para registos do Monitor Azure e são armazenados num espaço de trabalho do Log Analytics. Pode implementar o agente Log Analytics utilizando o suporte de [extensões VM](../azure-arc/servers/manage-vm-extensions.md) dos servidores ativados pelo Arc para novos VMs existentes. 

Uma vez recolhidos os registos pelo espaço de trabalho Log Analytics, pode configurar o espaço de trabalho Log Analytics com o Azure Security Center. O Azure Security Center avaliará o estado de vulnerabilidade dos VMs de Solução VMware Azure e levantará um alerta para qualquer vulnerabilidade crítica. Por exemplo, avalia patches de sistema operativo em falta, configurações de segurança e [proteção de pontos finais](../security-center/security-center-services.md).

Pode configurar o espaço de trabalho Log Analytics com o Azure Sentinel para deteção de alerta, visibilidade de ameaças, caça e resposta a ameaças. No diagrama anterior, o Azure Security Center está ligado ao Azure Sentinel utilizando o conector do Centro de Segurança Azure. O Azure Security Center irá encaminhar a vulnerabilidade ambiental para o Azure Sentinel para criar um incidente e mapear com outras ameaças. Também pode criar a consulta de regras programadas para detetar atividade indesejada e convertê-la nos incidentes.

## <a name="benefits"></a>Benefícios

- Os serviços nativos do Azure podem ser utilizados para a segurança do ambiente híbrido em Azure, Azure VMware Solution e serviços no local.
- Utilizando um espaço de trabalho Log Analytics, pode recolher os dados ou os registos num único ponto e apresentar os mesmos dados a diferentes serviços nativos do Azure.
- O Azure Security Center oferece muitas funcionalidades, incluindo:
    - Monitorização da integridade do ficheiro
    - Deteção de ataque sem ficheiros
    - Avaliação do patch do sistema operativo 
    - Avaliação de erros de segurança
    - Avaliação da proteção do ponto final
- Azure Sentinel permite-lhe:
    - Recolha dados em escala de nuvem em todos os utilizadores, dispositivos, aplicações e infraestruturas, tanto nas instalações como em várias nuvens.
    - Detetar ameaças não detetadas anteriormente.
    - Investigue ameaças com inteligência artificial e procure atividades suspeitas em escala.
    - Responda rapidamente a incidentes com orquestração incorporada e automatização de tarefas comuns.

## <a name="create-a-log-analytics-workspace"></a>Criar uma área de trabalho do Log Analytics

Você precisará de um espaço de trabalho Log Analytics para recolher dados de várias fontes. Para obter mais informações, consulte [Criar um espaço de trabalho Log Analytics a partir do portal Azure.](../azure-monitor/learn/quick-create-workspace.md) 

## <a name="deploy-security-center-and-configure-azure-vmware-solution-vms"></a>Implementar Centro de Segurança e configurar VMs de Solução VMware Azure

O Azure Security Center é uma ferramenta pré-configurada que não requer implantação. No portal Azure, procure o **Centro de Segurança** e selecione-o.

### <a name="enable-azure-defender"></a>Ativar o Azure Defender

O Azure Defender estende a proteção avançada de ameaças do Azure Security Center através das suas cargas de trabalho híbridas, tanto nas instalações como na nuvem. Assim, para proteger os VMs da Solução VMware Azure, terá de ativar o Azure Defender. 

1. No Centro de Segurança, **selecione Começar a trabalhar**.

2. Selecione o **separador 'Actualização'** e, em seguida, selecione a sua subscrição ou espaço de trabalho. 

3. Selecione **Upgrade** para ativar o Azure Defender.

## <a name="add-azure-vmware-solution-vms-to-security-center"></a>Adicione VMs de Solução VMware Azure ao Centro de Segurança

1. No portal Azure, procure no **Arco Azure** e selecione-o.

2. Em Recursos, selecione **Servidores** e, em seguida, **+Adicionar**.

    :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="Uma imagem mostrando a página Azure Arc Servers para adicionar um VM de Solução VMware Azure ao Azure.":::

3. **Selecione Gerar script**.
 
    :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="Uma imagem da página do Azure Arc mostrando a opção para adicionar um servidor usando script interativo."::: 
 
4. No **separador Pré-Requisitos,** selecione **Seguinte**.

5. No separador **Detalhes de Recursos,** preencha os seguintes detalhes: 
    - Subscrição
    - Grupo de recursos
    - Region 
    - Sistema operativo
    - Detalhes do Servidor Proxy
    
    Em seguida, selecione **Seguinte: Tags**.

6. No **separador Tags,** selecione **Seguinte**.

7. No **separador Descarregar e executar script,** selecione **Baixar**.

8. Especifique o seu sistema operativo e execute o script no VM da solução VMware Azure.

## <a name="view-recommendations-and-passed-assessments"></a>Ver recomendações e avaliações aprovadas

1. No Centro de Segurança Azure, selecione **Inventário** a partir do painel esquerdo.

2. Para o tipo de recurso, selecione **Servidores - Azure Arc**.
 
     :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="Uma imagem da página de inventário do Centro de Segurança Azure mostrando servidores - Azure Arc selecionados sob o tipo de recurso.":::

3. Selecione o nome do seu recurso. Abre-se uma página que mostra os detalhes de saúde de segurança do seu recurso.

4. Na **lista de recomendações**, selecione as **recomendações**, **avaliações aprovadas** e **separadores de avaliações indisponíveis** para visualizar estes detalhes.

    :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="Uma imagem do Centro de Segurança Azure mostrando recomendações de segurança e avaliações.":::

## <a name="deploy-an-azure-sentinel-workspace"></a>Implementar um espaço de trabalho Azure Sentinel

Azure Sentinel é construído em cima de um espaço de trabalho Log Analytics. O seu primeiro passo para embarcar no Azure Sentinel é selecionar o espaço de trabalho Log Analytics que pretende utilizar para o efeito.

1. No portal Azure, procure **por Azure Sentinel** e selecione-o.

2. Na página de espaços de trabalho Azure Sentinel, selecione **+Adicionar**.

3. Selecione o espaço de trabalho Log Analytics e **selecione Adicionar**.

## <a name="enable-data-collector-for-security-events-on-azure-vmware-solution-vms"></a>Ativar o coletor de dados para eventos de segurança em VMS de Solução VMware Azure

Agora está pronto para ligar o Azure Sentinel às suas fontes de dados, neste caso, eventos de segurança.

1. Na página de espaços de trabalho Azure Sentinel, selecione o espaço de trabalho configurado.

2. Em Configuração, selecione **conectores de dados**.

3. Sob a coluna Nome do Conector, selecione **Eventos** de Segurança da lista e, em seguida, selecione **Abrir a página do conector**.

4. Na página do conector, selecione os eventos que deseja transmitir e, em seguida, **selecione 'Alterar' Alterações**.

    :::image type="content" source="media/azure-security-integration/select-events-you-want-to-stream.png" alt-text="Screenshot of Security Events page in Azure Sentinel onde pode selecionar quais eventos transmitir.":::

## <a name="connect-azure-sentinel-with-azure-security-center"></a>Ligue Azure Sentinel ao Centro de Segurança Azure  

1. Na página do espaço de trabalho Azure Sentinel, selecione o espaço de trabalho configurado.

2. Em Configuração, selecione **conectores de dados**.

3. Selecione O Centro de **Segurança Azure** da lista e, em seguida, selecione **Abrir a página do conector**.

    :::image type="content" source="media/azure-security-integration/connect-security-center-with-azure-sentinel.png" alt-text="Screenshot da página de conectores de dados em Azure Sentinel mostrando a seleção para ligar o Centro de Segurança Azure com O Azure Sentinel.":::

4. Selecione **Connect** para ligar o Centro de Segurança Azure ao Azure Sentinel.

5. Ativar **o incidente da Criação** para gerar um incidente para o Centro de Segurança Azure.

## <a name="create-rules-to-identify-security-threats"></a>Criar regras para identificar ameaças à segurança

Depois de ligar fontes de dados ao Azure Sentinel, pode criar regras para gerar alertas para ameaças detetadas. No exemplo seguinte, criaremos uma regra para tentativas de iniciar sessão no servidor do Windows com a palavra-passe errada.

1. Na página geral do Azure Sentinel, em Configurações, selecione **Analytics**.

2. Em Configurações, selecione **Analytics**.

3. Selecione **+Criar** e na regra de consulta agendada, **selecione**

4. No **separador Geral,** insira as informações necessárias.

    - Nome
    - Descrição
    - Táticas
    - Gravidade
    - Estado

    Selecione **Seguinte: Definir lógica de regra >**.

5. No separador **lógica de regra definida,** introduza as informações necessárias.

    - Consulta de regras (aqui mostrando a nossa consulta de exemplo)
    
        ```
        SecurityEvent
        |where Activity startswith '4625'
        |summarize count () by IpAddress,Computer
        |where count_ > 3
        ```
        
    - Entidades do mapa
    - Agendamento de consultas
    - Limiar de alerta
    - Agrupamento de eventos
    - Supressão

    Selecione **Seguinte**.

6. No separador **Definições de Incidente,** ative **criar incidentes a partir de alertas desencadeados por esta regra de análise** e selecionar **Seguinte: Resposta automatizada >**.
 
    :::image type="content" source="media/azure-security-integration/create-new-analytic-rule-wizard.png" alt-text="Screenshot do assistente de regras analíticas para criar uma nova regra em Azure Sentinel. Mostra Criar incidentes a partir de alertas desencadeados por esta regra conforme ativado.":::

7. Selecione **Seguinte: Rever >**.

8. No **separador 'Rever' e criar,** rever as informações e selecionar **Criar.**

Após a terceira tentativa falhada de iniciar sposição no servidor do Windows, a regra criada despoleta um incidente para cada tentativa falhada.

## <a name="view-alerts"></a>Ver alertas

Pode ver incidentes gerados com Azure Sentinel. Também pode atribuir incidentes e fechá-los assim que forem resolvidos, todos de dentro de Azure Sentinel.

1. Vá à página geral do Azure Sentinel.

2. Sob gestão de ameaças, selecione **Incidents**.

3. Selecione um incidente. Em seguida, pode atribuir o incidente a uma equipa para resolução.

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="Screenshot da página incidentes de Azure Sentinel com incidente selecionado e opção para atribuir o incidente para resolução.":::

    Depois de resolver o problema, pode fechá-lo.

## <a name="hunt-security-threats-with-queries"></a>Caçar ameaças de segurança com consultas

Pode criar consultas ou utilizar a consulta pré-definida disponível no Azure Sentinel para identificar ameaças no seu ambiente. Os passos seguintes executam uma consulta pré-definida.

1. Vá à página geral do Azure Sentinel.

2. Sob a gestão de ameaças, selecione **Hunting**. É apresentada uma lista de consultas pré-definidas.

3. Selecione uma consulta e, em seguida, **selecione '**

4. Selecione **Ver Resultados** para verificar os resultados.

### <a name="create-a-new-query"></a>Criar uma nova consulta

1.  Sob a gestão de ameaças, selecione **Hunting** e, em **seguida, +Nova Consulta.**

    :::image type="content" source="media/azure-security-integration/create-new-query.png" alt-text="Screenshot da página de caça Azure Sentinel com + Nova Consulta em destaque.":::

2. Preencha as seguintes informações para criar uma consulta personalizada.

    - Nome
    - Descrição
    - Consulta personalizada
    - Insira mapeamento
    - Táticas
    
3. Selecione **Criar**. Em seguida, pode selecionar a consulta criada, **executar consulta** e **ver resultados.**

## <a name="next-steps"></a>Passos seguintes

Agora que cobriu como proteger os seus VMs de Solução VMware Azure, talvez queira saber:

- Utilização do [painel Azure Defender](../security-center/azure-defender-dashboard.md).
- [Deteção avançada de ataques em vários estágios em Azure Sentinel](../azure-monitor/learn/quick-create-workspace.md).
- [Gestão do ciclo de vida da Azure VMware Solution VMs](lifecycle-management-of-azure-vmware-solution-vms.md).
