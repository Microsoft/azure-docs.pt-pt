---
title: Use o monitor virtual de visualização do Monitor de Ambiente de Trabalho do Monitor do Windows - Azure
description: Como utilizar o Monitor Azure para o Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e0be6decf28fcbb2edacd5019f567d26403b1f31
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467727"
---
# <a name="use-azure-monitor-for-windows-virtual-desktop-to-monitor-your-deployment-preview"></a>Utilize o Monitor Azure para o Windows Virtual Desktop para monitorizar a sua implementação (pré-visualização)

>[!IMPORTANT]
>O Monitor Azure para o Windows Virtual Desktop encontra-se atualmente em pré-visualização pública. Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos a sua utilização para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Azure Monitor para Windows Virtual Desktop (pré-visualização) é um dashboard construído em Livros de Trabalho do Monitor Azure que ajuda os profissionais de TI a compreender os seus ambientes de ambientes de ambiente virtual do Windows Desktop. Este tópico irá acompanhá-lo como configurar o Azure Monitor para o Windows Virtual Desktop para monitorizar os ambientes de ambiente de secretária virtual do Windows.

## <a name="requirements"></a>Requisitos

Antes de começar a utilizar o Monitor Azure para o Windows Virtual Desktop, terá de configurar as seguintes coisas:

- Todos os ambientes de ambientes de ambiente de trabalho virtual do Windows que monitoriza devem basear-se na versão mais recente do Windows Virtual Desktop que é compatível com o Azure Resource Manager.

- Pelo menos um espaço de trabalho de Log Analytics configurado.

- Ativar a recolha de dados para as seguintes coisas no seu espaço de trabalho Log Analytics:
    - Quaisquer contadores de desempenho necessários
    - Quaisquer contadores de desempenho ou eventos utilizados no Azure Monitor para o Windows Virtual Desktop
    - Dados da ferramenta De diagnóstico para todos os objetos no ambiente que estará a monitorizar.
    - Máquinas virtuais (VMs) no ambiente que irá monitorizar.

Qualquer pessoa que monitorize o Monitor Azure para o Windows Virtual Desktop para o seu ambiente também necessitará das seguintes permissões de acesso à leitura:

- Leia o acesso ao grupo de recursos onde estão localizados os recursos do ambiente.

- Leia o acesso ao grupo de recursos onde estão localizados os anfitriões da sessão do ambiente

>[!NOTE]
> O acesso à leitura só permite que os administradores vejam os dados. Precisarão de diferentes permissões para gerir recursos no portal virtual do Windows Desktop.

## <a name="open-azure-monitor-for-windows-virtual-desktop"></a>Monitor aberto do Azure para o Windows Virtual Desktop

Pode abrir o Azure Monitor para o Windows Virtual Desktop com um dos seguintes métodos:

- Vai para [aka.ms/azmonwvdi.](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)

- Procure e selecione **o Windows Virtual Desktop** a partir do portal Azure e, em seguida, selecione **Insights**.

- Procure e selecione **Monitor Azure** a partir do portal Azure. Selecione **Insights Hub** em **Insights**, e em **Outros Ambientes** **de Trabalho Virtuais do Windows** selecione para abrir o dashboard na página Azure Monitor.

Assim que tiver o Monitor Azure para o Ambiente de Trabalho Virtual do Windows aberto, selecione uma ou mais das caixas de verificação rotuladas **por Subscrição**, **Grupo de Recursos,** **Pool anfitrião** e **intervalo de tempo** com base no ambiente que pretende monitorizar.

>[!NOTE]
>O Windows Virtual Desktop suporta atualmente apenas monitorizar uma subscrição, grupo de recursos e piscina de anfitrião de cada vez. Se não encontrar o ambiente que pretende monitorizar, consulte a nossa documentação de [resolução de problemas](troubleshoot-azure-monitor.md) para pedidos e problemas conhecidos de recursos.!

## <a name="set-up-with-the-configuration-workbook"></a>Configurar com o livro de configuração

Se esta for a primeira vez que abre o Azure Monitor para o Windows Virtual Desktop, terá de configurar o Azure Monitor para os recursos de Ambiente de Trabalho Virtual do Windows. Para configurar os seus recursos:

1. Abra o seu livro no portal Azure.
2. Selecione **Abra o livro de configuração**.

O livro de configuração configura o ambiente de monitorização e permite-lhe verificar a configuração depois de terminar o processo de configuração. É importante verificar a sua configuração se os itens no painel de instrumentos não estão a ser apresentados corretamente, ou quando o grupo de produtos publica atualizações que requerem pontos de dados adicionais.

## <a name="host-pool-diagnostic-settings"></a>Definições de diagnóstico do conjunto de anfitriões

Terá de ativar as definições de diagnóstico do Azure Monitor em todos os objetos dentro do ambiente de trabalho virtual do Windows que suportam esta funcionalidade.

1. Abra o Monitor Azure para o Ambiente de Trabalho Virtual do Windows [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks), em seguida, selecione **O Livro de Configuração**.

2. Selecione um ambiente para monitorizar em **Subscrição,** **Grupo de Recursos** e Pool **anfitrião**.

3. Em **Configurações de Diagnóstico do Pool Host**, verifique se os diagnósticos virtuais do Windows estão ativados para o pool de anfitriões. Se não forem, aparecerá uma mensagem de erro que diz "Não foi encontrada nenhuma configuração de Diagnóstico existente para a piscina de anfitrião selecionada." 
   
   Devem ser ativadas as seguintes tabelas:

    - Check Point
    - Erro
    - Gestão
    - Ligação
    - HostRegistration

    >[!NOTE]
    > Se não vir a mensagem de erro, não precisa de fazer o passo 4.

4. Selecione **a piscina de anfitriões Configure**.

5. Selecione **Implementar**.

6. Refresque o livro.

Pode aprender mais sobre como ativar diagnósticos em todos os objetos do ambiente de trabalho virtual do Windows ou aceder ao espaço de trabalho do Log Analytics no [Send Windows Virtual Desktop para Registar Analytics.](diagnostics-log-analytics.md)

## <a name="configure-log-analytics"></a>Configurar o Log Analytics

Para começar a utilizar o Monitor Azure para o Windows Virtual Desktop, também necessitará de pelo menos um espaço de trabalho log Analytics para recolher dados do ambiente que planeia monitorizar e fornecer ao livro de trabalho. Se já tiver uma configuração, avance para [configurar os contadores de desempenho](#set-up-performance-counters). Para configurar um novo espaço de trabalho log Analytics para a subscrição Azure que contenha o seu ambiente de trabalho virtual do Windows, consulte [Criar um espaço de trabalho Log Analytics no portal Azure](../azure-monitor/learn/quick-create-workspace.md).

>[!NOTE]
>Aplicar-se-ão os custos padrão de armazenamento de dados para o Log Analytics. Para começar, recomendamos que escolha o modelo pay-as-you-go e ajuste à medida que escala a sua implementação e recolha de mais dados. Para saber mais, consulte [os preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="set-up-performance-counters"></a>Configurar contadores de desempenho

É necessário ativar contadores de desempenho específicos para recolha no intervalo de amostra correspondente no espaço de trabalho Log Analytics. Estes contadores de desempenho são os únicos contadores que precisa para monitorizar o Windows Virtual Desktop. Pode desativar todos os outros para poupar custos.

Se já tiver contadores de desempenho ativados e quiser removê-los, siga as instruções nos [contadores de desempenho configurar](../azure-monitor/platform/data-sources-performance-counters.md) para reconfigurar os contadores de desempenho. Embora o artigo descreva como adicionar contadores, também pode removê-los no mesmo local.

Se ainda não montou balcões de desempenho, eis como configurá-los para o Azure Monitor para o Windows Virtual Desktop:

1. Vá para [aka.ms/azmonwvdi,](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks)em seguida, selecione o fundo do livro de **configuração** da janela.

2. Na **Configuração de Analítica de Registo,** selecione o espaço de trabalho que criou para a sua subscrição.

3. Nos **contadores de desempenho do Workspace,** verá a lista de contadores necessários para monitorização. No lado direito dessa lista, verifique os itens na lista de **contadores desaparecidos** para ativar os contadores de que necessita para começar a monitorizar o seu espaço de trabalho.

4. Selecione **Balcões de Desempenho Configurar**.

5. **Selecione Aplicar Config**.

6. Refresque o livro de configuração e continue a configurar o seu ambiente.

Também pode adicionar novos contadores de desempenho após a configuração inicial sempre que o serviço atualiza e requer novas ferramentas de monitorização. Pode verificar se todos os contadores necessários estão ativados selecionando-os na lista de **contadores desaparecidos.**

>[!NOTE]
>Os contadores de desempenho de atraso de entrada só são compatíveis com o Windows 10 RS5 e posteriormente ou o Windows Server 2019 e mais tarde.

Para saber mais sobre como adicionar manualmente contadores de desempenho que ainda não estão habilitados para recolha, consulte [balcões de desempenho configurados](../azure-monitor/platform/data-sources-performance-counters.md).

### <a name="set-up-windows-events"></a>Configurar eventos do Windows

Em seguida, terá de ativar eventos específicos do Windows para recolha no espaço de trabalho Do Log Analytics. Os eventos descritos nesta secção são os únicos que o Azure Monitor necessita para o Windows Virtual Desktop. Pode desativar todos os outros para poupar custos.

Para configurar eventos do Windows:

1. Se já tiver o Windows Events ativo e quiser removê-los, remova os eventos que não deseja antes de utilizar o livro de configuração para ativar o conjunto necessário para a monitorização.

2. Vá ao Monitor Azure para o Ambiente de Trabalho Virtual do Windows [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks), em seguida, selecione o livro de **configuração** na parte inferior da janela.

3. Na **configuração de Eventos do Windows,** há uma lista de Eventos windows necessários para monitorização. No lado direito dessa lista está a lista de **eventos Desaparecidos,** onde encontrará os nomes de eventos e tipos de eventos necessários que não estão atualmente habilitados para o seu espaço de trabalho. Grave cada um destes nomes para mais tarde.

4. Selecione **Configuração de espaços de trabalho abertos**.

5. Selecione **Dados**.

6. Selecione **Registos de eventos do Windows**.

7. Adicione os nomes do evento em falta do passo 3 e o tipo de evento necessário para cada um.

8. Refresque o livro de configuração e continue a configurar o seu ambiente.

Pode adicionar novos eventos do Windows após a configuração inicial se as atualizações da ferramenta de monitorização exigirem ativar novos eventos. Para ter a certeza de que tem todos os eventos necessários, volte à lista **de eventos desaparecidos** e permita quaisquer eventos desaparecidos que veja lá.

## <a name="install-the-log-analytics-agent-on-all-hosts"></a>Instale o agente Log Analytics em todos os anfitriões

Por fim, terá de instalar o agente Log Analytics em todos os anfitriões do pool anfitrião para enviar dados dos anfitriões para o espaço de trabalho selecionado.

Para instalar o agente Log Analytics:

1. Vá ao Monitor Azure para o Ambiente de Trabalho Virtual do Windows [aka.ms/azmonwvdi](https://portal.azure.com/#blade/Microsoft_Azure_WVD/WvdManagerMenuBlade/workbooks), em seguida, selecione o livro de **configuração** na parte inferior da janela.

2. Se o Log Analytics não estiver configurado para todos os anfitriões na piscina anfitriã, verá um erro na parte inferior da secção de configuração do Log Analytics com a mensagem "Alguns anfitriões na piscina anfitriã não estão a enviar dados para o espaço de trabalho do Log Analytics selecionado." **Selecione Adicionar anfitriões ao espaço de trabalho** para adicionar os anfitriões selecionados. Se não vir a mensagem de erro, pare aqui.

3. Refresque o Livro de Configuração.

>[!NOTE]
>A máquina de anfitrião tem de estar em funcionamento para instalar a extensão Log Analytics. Se a colocação automática falhar num hospedeiro, pode sempre instalar a extensão num hospedeiro manualmente. Para saber como instalar a extensão manualmente, consulte a [extensão da máquina virtual Log Analytics para windows](../virtual-machines/extensions/oms-windows.md).

## <a name="optional-configure-alerts"></a>Opcional: alertas de configuração

Pode configurar o Azure Monitor para o Windows Virtual Desktop para o notificar se algum alerta severo do Azure Monitor ocorrer dentro da subscrição selecionada. Para tal, siga as instruções em [Responder a eventos com alertas de monitor Azure](../azure-monitor/learn/tutorial-response.md).

## <a name="diagnostic-and-usage-data"></a>Dados de diagnóstico e utilização

A Microsoft recolhe automaticamente dados de utilização e desempenho através da utilização do serviço Azure Monitor. A Microsoft utiliza estes dados para melhorar a qualidade, segurança e integridade do serviço.

Para fornecer capacidades precisas e eficientes de resolução de problemas, os dados recolhidos incluem o ID da sessão do portal, o ID do utilizador do Azure Ative Directory e o nome do separador portal onde ocorreu o evento. A Microsoft não recolhe nomes, endereços ou outras informações de contacto.

Para obter mais informações sobre a recolha e utilização de dados, consulte a [Declaração de Privacidade dos Serviços Online da Microsoft](https://privacy.microsoft.com/privacystatement).

>[!NOTE]
>Para saber sobre a visualização ou eliminação dos seus dados pessoais recolhidos pelo serviço, consulte os [Pedidos de Assunto de Dados do Azure para o RGPD.](/microsoft-365/compliance/gdpr-dsr-azure) Para obter mais informações sobre o RGPD, consulte [a secção RGPD do portal Service Trust](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).

## <a name="next-steps"></a>Passos seguintes

Agora que configuraste o teu portal Virtual Desktop Azure do Windows, eis alguns recursos que podem ajudá-lo:

- Consulte o nosso [glossário](azure-monitor-glossary.md) para saber mais sobre termos e conceitos relacionados com o Azure Monitor para o Windows Virtual Desktop.
- Se encontrar algum problema, consulte o nosso [guia de resolução de problemas](troubleshoot-azure-monitor.md) para pedir ajuda.