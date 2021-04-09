---
title: Utilizar monitor do Monitor Windows Monitor virtual Desktop Monitor - Azure
description: Como utilizar o Monitor Azure para o Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 03/29/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1b93e0c0d61eaa390eda66da2a583a4f429ddd79
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105709553"
---
# <a name="use-azure-monitor-for-windows-virtual-desktop-to-monitor-your-deployment"></a>Utilize o Monitor Azure para o Windows Virtual Desktop para monitorizar a sua implementação

O Azure Monitor para Windows Virtual Desktop é um dashboard construído em livros de trabalho do Monitor Azure que ajuda os profissionais de TI a compreender os seus ambientes de ambientes de ambiente virtual do Windows Desktop. Este tópico irá acompanhá-lo como configurar o Azure Monitor para o Windows Virtual Desktop para monitorizar os ambientes de ambiente de secretária virtual do Windows.

## <a name="requirements"></a>Requisitos

Antes de começar a utilizar o Monitor Azure para o Windows Virtual Desktop, terá de configurar as seguintes coisas:

- Todos os ambientes de ambientes de ambiente de trabalho virtual do Windows que monitoriza devem basear-se na versão mais recente do Windows Virtual Desktop que é compatível com o Azure Resource Manager.
- Pelo menos um espaço de trabalho de Log Analytics configurado. Utilize um espaço de trabalho designado para o Log Analytics para os anfitriões da sessão virtual do Windows desktop para garantir que os contadores de desempenho e eventos são recolhidos apenas a partir de anfitriões de sessão na sua implementação virtual do Windows Desktop.
- Ativar a recolha de dados para as seguintes coisas no seu espaço de trabalho Log Analytics:
    - Diagnósticos do ambiente de trabalho virtual do Windows
    - Contadores de desempenho recomendados dos anfitriões da sessão virtual do Windows Desktop
    - Registos recomendados de eventos do Windows a partir dos anfitriões da sessão virtual do Windows

 O processo de configuração de dados descrito neste artigo é o único que precisa para monitorizar o Windows Virtual Desktop. Pode desativar todos os outros itens que enviam dados para o seu espaço de trabalho Log Analytics para economizar custos.

Qualquer pessoa que monitorize o Monitor Azure para o Windows Virtual Desktop para o seu ambiente também necessitará das seguintes permissões de acesso à leitura:

- Read-access às subscrições do Azure que detêm os seus recursos de Desktop Virtual do Windows
- Read-access aos grupos de recursos da subscrição que detêm os anfitriões da sessão virtual do Windows
- Leia o acesso ao espaço de trabalho ou espaço de trabalho log Analytics

>[!NOTE]
> O acesso à leitura só permite que os administradores vejam os dados. Precisarão de diferentes permissões para gerir recursos no portal virtual do Windows Desktop.

## <a name="open-azure-monitor-for-windows-virtual-desktop"></a>Monitor aberto do Azure para o Windows Virtual Desktop

Pode abrir o Azure Monitor para o Windows Virtual Desktop com um dos seguintes métodos:

- Vai para [aka.ms/azmonwvdi.](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)
- Procure e selecione **o Windows Virtual Desktop** a partir do portal Azure e, em seguida, selecione **Insights**.
- Procure e selecione **Monitor Azure** a partir do portal Azure. Selecione **Insights Hub** em **Insights** e, em seguida, selecione Windows **Virtual Desktop**.
Assim que tiver a página aberta, insira a  gama de **Assinaturas, Grupo de Recursos,** **Conjunto de Anfitriões** e Tempo do ambiente que pretende monitorizar. 

>[!NOTE]
>O Windows Virtual Desktop suporta atualmente apenas monitorizar uma subscrição, grupo de recursos e piscina de anfitrião de cada vez. Se não encontrar o ambiente que pretende monitorizar, consulte a nossa documentação de [resolução de problemas](troubleshoot-azure-monitor.md) para pedidos e problemas de funcionalidades conhecidos.

## <a name="log-analytics-settings"></a>Registar definições de Analítico

Para começar a utilizar o Monitor Azure para o Windows Virtual Desktop, necessitará de pelo menos um espaço de trabalho log Analytics. Utilize um espaço de trabalho designado para o Log Analytics para os anfitriões da sessão virtual do Windows Desktop para garantir que os contadores de desempenho e eventos são apenas anfitriões de sessão de formulários recolhidos na sua implementação virtual do Windows Desktop. Se já tiver um espaço de trabalho configurado, avance para [configurar usando o livro de configuração](#set-up-using-the-configuration-workbook). Para configurar um, consulte [Criar um espaço de trabalho log analytics no portal Azure](../azure-monitor/logs/quick-create-workspace.md).

>[!NOTE]
>Aplicar-se-ão os custos padrão de armazenamento de dados para o Log Analytics. Para começar, recomendamos que escolha o modelo pay-as-you-go e ajuste à medida que escala a sua implementação e recolha de mais dados. Para saber mais, consulte [os preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="set-up-using-the-configuration-workbook"></a>Configurar usando o livro de configuração

Se for a primeira vez que abre o Azure Monitor para o Ambiente de Trabalho Virtual do Windows, terá de configurar o Azure Monitor para o ambiente de ambiente de trabalho virtual do Windows. Para configurar os seus recursos:

1. Abra o Monitor Azure para o Windows Virtual Desktop no portal Azure [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks), em seguida, selecione **o livro de configuração**.
2. Selecione um ambiente para configurar em **Subscrição,** **Grupo de Recursos** e Pool **anfitrião**.

O livro de configuração configura o ambiente de monitorização e permite-lhe verificar a configuração depois de terminar o processo de configuração. É importante verificar a sua configuração se os itens no painel de instrumentos não estão a ser apresentados corretamente ou quando o grupo de produtos publica atualizações que requerem novas definições.

### <a name="resource-diagnostic-settings"></a>Definições de diagnóstico de recursos

Para recolher informações sobre a sua infraestrutura virtual de ambiente de trabalho do Windows, terá de ativar várias definições de diagnóstico nas piscinas e espaços de trabalho do Windows Virtual Desktop (este é o seu espaço de trabalho virtual do Windows Desktop, e não o seu espaço de trabalho Log Analytics). Para saber mais sobre piscinas hospedeiras, espaços de trabalho e outros objetos de recursos do Windows Virtual Desktop, consulte o nosso [guia ambiental.](environment-setup.md)

Pode saber mais sobre os diagnósticos de ambiente de trabalho virtual do Windows e as tabelas de diagnóstico suportadas no [Send Windows Virtual Desktop diagnostics para Log Analytics](diagnostics-log-analytics.md).

Para definir as definições de diagnóstico de recursos no livro de configuração: 

1. Selecione o separador **definições de diagnóstico de recursos** no livro de configuração. 
2. Selecione **Log Analytics workspace** para enviar diagnósticos de desktop virtual do Windows. 

#### <a name="host-pool-diagnostic-settings"></a>Definições de diagnóstico do conjunto de anfitriões

Para configurar os diagnósticos do pool do anfitrião utilizando a secção de definições de diagnóstico de recursos no livro de configuração:

1. No **conjunto Host**, verifique se os diagnósticos de ambiente de trabalho virtual do Windows estão ativados. Se não forem, aparecerá uma mensagem de erro que diz "Não foi encontrada nenhuma configuração de diagnóstico existente para a piscina de anfitrião selecionada." Terá de ativar as seguintes tabelas de diagnóstico suportadas:

    - Check Point
    - Erro
    - Gestão
    - Ligação
    - HostRegistration
    - AgenteHealthStatus
    
    >[!NOTE]
    > Se não vir a mensagem de erro, não precisa de fazer os passos 2 a 4.

2. Selecione **a piscina de anfitriões Configure**.
3. Selecione **Implementar**.
4. Refresque o livro de configuração.

#### <a name="workspace-diagnostic-settings"></a>Definições de diagnóstico do espaço de trabalho 

Para configurar diagnósticos de espaço de trabalho utilizando a secção de definições de diagnóstico de recursos no livro de configuração:

 1. No **Workspace**, verifique se os diagnósticos de ambiente de trabalho virtuais do Windows estão ativados para o espaço de trabalho virtual do Windows Desktop. Se não forem, aparecerá uma mensagem de erro que diz "Não foi encontrada nenhuma configuração de diagnóstico existente para o espaço de trabalho selecionado." Terá de ativar as seguintes tabelas de diagnóstico suportadas:
 
    - Check Point
    - Erro
    - Gestão
    - Feed
    
    >[!NOTE]
    > Se não vir a mensagem de erro, não precisa de fazer os passos 2-4.

2. Selecione **Configurar espaço de trabalho**.
3. Selecione **Implementar**.
4. Refresque o livro de configuração.

### <a name="session-host-data-settings"></a>Definições de dados do anfitrião da sessão

Para recolher informações sobre os anfitriões da sessão virtual do Windows Desktop, terá de instalar o agente Log Analytics em todos os anfitriões da sessão na piscina anfitriã, certificar-se de que os anfitriões da sessão estão a enviar para um espaço de trabalho do Log Analytics e configurar as definições do seu agente Log Analytics para recolher dados de desempenho e Registos de Eventos do Windows.

O espaço de trabalho Log Analytics para o qual envia dados de anfitriões de sessão não tem de ser o mesmo para o qual envia dados de diagnóstico. Se tiver anfitriões de sessão Azure fora do ambiente de trabalho virtual do Windows, recomendamos ter um espaço de trabalho designado log analytics para os anfitriões da sessão virtual do Windows Desktop. 

Para definir o espaço de trabalho Log Analytics onde pretende recolher dados do anfitrião da sessão: 

1. Selecione o **separador definições de dados do anfitrião da Sessão** no livro de definições de configuração. 
2. Selecione o **espaço de trabalho Log Analytics** para o quais pretende enviar dados de anfitrião da sessão. 

#### <a name="session-hosts"></a>Anfitriões de sessão

Você precisará instalar o agente Log Analytics em todos os anfitriões da sessão no pool anfitrião e enviar dados desses anfitriões para o seu espaço de trabalho de Log Analytics selecionado. Se o Log Analytics não estiver configurado para todos os anfitriões da sessão na piscina anfitriã, verá uma secção de **anfitriões de Sessão** no topo das definições de dados do anfitrião da **Sessão** com a mensagem "Alguns anfitriões no pool anfitrião não estão a enviar dados para o espaço de trabalho do Log Analytics selecionado."

>[!NOTE]
> Se não vir a secção **de anfitriões** da Sessão ou mensagem de erro, todos os anfitriões da sessão estão corretamente configurado. Avance para configurar instruções para [os contadores de desempenho do Workspace](#workspace-performance-counters).

Para configurar os anfitriões restantes da sessão utilizando o livro de configuração:

1. **Selecione Adicionar anfitriões ao espaço de trabalho.** 
2. Refresque o livro de configuração.

>[!NOTE]
>A máquina de anfitrião tem de estar em funcionamento para instalar a extensão Log Analytics. Se a colocação automática não funcionar, pode instalar a extensão num hospedeiro manualmente. Para saber como instalar a extensão manualmente, consulte a [extensão da máquina virtual Log Analytics para windows](../virtual-machines/extensions/oms-windows.md).

#### <a name="workspace-performance-counters"></a>Contadores de desempenho do espaço de trabalho

Você precisará de ativar contadores de desempenho específicos para recolher informações de desempenho dos anfitriões da sessão e enviá-lo para o espaço de trabalho Log Analytics.

Se já tiver contadores de desempenho ativados e quiser removê-los, siga as instruções nos [contadores de desempenho configurantes](../azure-monitor/agents/data-sources-performance-counters.md). Pode adicionar e remover contadores de desempenho no mesmo local.

Para configurar contadores de desempenho utilizando o livro de configuração: 

1. Nos **contadores de desempenho** do Workspace no livro de configuração, verifique os **contadores configurados** para ver os contadores que já habilitado a enviar para o espaço de trabalho do Log Analytics. Verifique **os contadores em falta** para se certificar de que ativou todos os contadores necessários.
2. Se tiver balcões em falta, selecione **balcões de desempenho Configure**.
3. **Selecione Aplicar Config**.
4. Refresque o livro de configuração.
5. Certifique-se de que todos os contadores necessários estão ativados verificando a lista **de contadores desaparecidos.** 

#### <a name="configure-windows-event-logs"></a>Configurar registos de eventos do Windows

Também terá de permitir registos específicos de eventos do Windows para recolher erros, avisos e informações dos anfitriões da sessão e enviá-los para o espaço de trabalho do Log Analytics.

Se já ativou os Registos de Eventos do Windows e pretende removê-los, siga as instruções em [Configurar registos de eventos do Windows](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs).  Pode adicionar e remover Registos de Eventos do Windows no mesmo local.

Para configurar os Registos de Eventos do Windows utilizando o livro de configuração:

1. Na **configuração de Registos de Eventos do Windows,** consulte **os Registos de Eventos Configurados** para ver os Registos de Eventos que já habilitado a enviar para o espaço de trabalho do Log Analytics. Verifique **registos de eventos em falta** para se certificar de que ativou todos os Registos de Eventos do Windows.
2. Se tiver perdido registos de eventos do Windows, selecione **Configure Events**.
3. Selecione **Implementar**.
4. Refresque o livro de configuração.
5. Certifique-se de que todos os Registos de Eventos do Windows necessários estão ativados verificando a lista **de Registos de Eventos Desaparecidos.** 

>[!NOTE]
>Se a implementação automática do evento falhar, selecione **a configuração do agente Aberto** no livro de configuração para adicionar manualmente quaisquer Registos de Eventos do Windows em falta. 

## <a name="optional-configure-alerts"></a>Opcional: alertas de configuração

O Azure Monitor para o Windows Virtual Desktop permite-lhe monitorizar os alertas do Azure Monitor que ocorram dentro da subscrição selecionada no contexto dos seus dados de Ambiente de Trabalho Virtual do Windows. Os alertas do Azure Monitor são uma funcionalidade opcional nas suas subscrições Azure, e precisa de as configurar separadamente do Azure Monitor para o Windows Virtual Desktop. Pode utilizar a estrutura de alertas do Azure Monitor para definir alertas personalizados em eventos, diagnósticos e recursos virtuais do Windows Virtual Desktop. Para saber mais sobre os alertas do Azure Monitor, consulte [Responder a eventos com alertas do Monitor Azure.](../azure-monitor/alerts/tutorial-response.md)

## <a name="diagnostic-and-usage-data"></a>Dados de diagnóstico e utilização

A Microsoft recolhe automaticamente dados de utilização e desempenho através da utilização do serviço Azure Monitor. A Microsoft utiliza estes dados para melhorar a qualidade, segurança e integridade do serviço.

Para fornecer capacidades precisas e eficientes de resolução de problemas, os dados recolhidos incluem o ID da sessão do portal, o ID do utilizador do Azure Ative Directory e o nome do separador portal onde ocorreu o evento. A Microsoft não recolhe nomes, endereços ou outras informações de contacto.

Para obter mais informações sobre a recolha e utilização de dados, consulte a [Declaração de Privacidade dos Serviços Online da Microsoft](https://privacy.microsoft.com/privacystatement).

>[!NOTE]
>Para saber sobre a visualização ou eliminação dos seus dados pessoais recolhidos pelo serviço, consulte os [Pedidos de Assunto de Dados do Azure para o RGPD.](/microsoft-365/compliance/gdpr-dsr-azure) Para obter mais informações sobre o RGPD, consulte [a secção RGPD do portal Service Trust](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).

## <a name="next-steps"></a>Passos seguintes

Agora que configuraste o Azure Monitor para o ambiente de secretária virtual do Windows, eis alguns recursos que podem ajudá-lo a começar a monitorizar o seu ambiente:

- Consulte o nosso [glossário](azure-monitor-glossary.md) para saber mais sobre termos e conceitos relacionados com o Azure Monitor para o Windows Virtual Desktop.
- Se encontrar algum problema, consulte o nosso [guia de resolução de problemas](troubleshoot-azure-monitor.md) para obter ajuda e problemas conhecidos.
