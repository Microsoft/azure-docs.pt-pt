---
title: Diagnóstico de desempenho para máquinas virtuais do Azure | Microsoft Docs
description: Apresenta o diagnóstico de desempenho do Azure para Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: przlplx
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 9/20/2018
ms.author: anandh
ms.openlocfilehash: 16be3d1695608165405a3490b686a01ba6a2a62c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70080606"
---
# <a name="performance-diagnostics-for-azure-virtual-machines"></a>Diagnóstico de desempenho para máquinas virtuais do Azure

A ferramenta de diagnóstico de desempenho ajuda a solucionar problemas de desempenho que podem afetar uma VM (máquina virtual) Windows ou Linux. Os cenários de solução de problemas com suporte incluem verificações rápidas de problemas conhecidos e práticas recomendadas e problemas complexos que envolvem desempenho lento da VM ou alto uso de CPU, espaço em disco ou memória.

Você pode executar o diagnóstico de desempenho diretamente do portal do Azure, em que você também pode examinar informações e um relatório sobre vários logs, configuração avançada e dados de diagnóstico. Recomendamos que você execute o diagnóstico de desempenho e examine as informações e os dados de diagnóstico antes de entrar em contato com a Suporte da Microsoft.

> [!NOTE]
> Para o Windows, o diagnóstico de desempenho tem suporte atualmente em VMs que têm o SDK do .NET versão 4,5 ou uma versão posterior instalada. Para ver as etapas para executar o diagnóstico de desempenho em VMs clássicas, consulte [extensão de VM de diagnóstico de desempenho do Azure](performance-diagnostics-vm-extension.md).

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

### <a name="windows"></a>Windows

Windows 10, Windows 8, Windows 8 Enterprise, Windows 8 Pro, Windows 8.1, Windows Server 2016, Windows Server 2012, Windows Server 2012 datacenter, Windows Server 2012 R2, Windows Server 2012 R2 Data Center, Windows Server 2012 R2 Standard, Windows Server 2012 Standard, o Windows Server 2008 R2, o Windows Server 2008 R2 Datacenter, o Windows Server 2008 R2 Enterprise, o Windows Server 2008 R2 Foundation, o Windows Server 2008 R2 SP1, o Windows Server 2008 R2 Standard.

### <a name="linux"></a>Linux

Oracle Linux Server 6,10 [`*`], 7,3, 7,6, 7,5 (Oracle-Database-EE 13,8 imagem do Marketplace), CentOS 6,5`*`[], 7,6, RHEL 7,2, 7,5, 8,0`*`[], Ubuntu 14, 4, 16, 4, 18, 4, Debian 8, 9, 10`*`[], SLES 12 SP4 [`*`]

>[!Note]
>[`*`] Consulte os [problemas conhecidos](how-to-use-perfinsights-linux.md#known-issues)

## <a name="install-and-run-performance-diagnostics-on-your-vm"></a>Instalar e executar o diagnóstico de desempenho em sua VM

O diagnóstico de desempenho instala uma extensão de VM que executa uma ferramenta de diagnóstico chamada PerfInsights. O PerfInsights está disponível para [Windows](https://aka.ms/perfinsights) e [Linux](https://aka.ms/perfinsightslinux). Para instalar e executar o diagnóstico de desempenho, siga estas etapas:

1. Na coluna à esquerda dos comandos, selecione **máquinas virtuais**.
1. Na lista de nomes de VM, selecione a VM na qual você deseja executar o diagnóstico.
1. Na coluna à direita dos comandos, selecione **diagnóstico de desempenho**.

    ![Captura de tela de portal do Azure, com o botão instalar diagnóstico de desempenho realçado](media/performance-diagnostics/performance-diagnostics-install.png)

    > [!NOTE]
    > Nesta captura de tela, a folha de nomes de VM é oculta.
1. Selecionar uma conta de armazenamento (opcional)

    Se você quiser usar uma única conta de armazenamento para armazenar os resultados de diagnóstico de desempenho para várias VMs, poderá selecionar uma conta de armazenamento clicando no botão **configurações** na barra de ferramentas. Clique no botão **OK** depois de selecionar a conta de armazenamento.

    Se você não especificar uma conta de armazenamento, uma nova conta de armazenamento será criada por padrão.

    ![Captura de tela da folha diagnóstico de desempenho, com o botão de barra de ferramentas configurações destacado](media/performance-diagnostics/settings-button.png)

    ![Captura de tela da seleção de conta de armazenamento da folha configurações de diagnóstico de desempenho](media/performance-diagnostics/select-storage-account.png)

1. Selecione o botão **instalar diagnóstico de desempenho** .
1. Marque a caixa de seleção **Executar diagnóstico** se desejar executar um diagnóstico após a conclusão da instalação. Se você fizer essa seleção, poderá escolher o cenário de análise de desempenho e as opções relacionadas.

    ![Captura de tela do botão de instalação do diagnóstico de desempenho](media/performance-diagnostics/install-diagnostics-button.png)

## <a name="select-an-analysis-scenario-to-run"></a>Selecione um cenário de análise para executar

Os cenários de análise a seguir estão disponíveis no portal do Azure. Selecione uma análise, dependendo do problema de desempenho que você está tendo. Selecione as opções de duração e rastreamento conforme necessário para a análise.

* **Análise rápida de desempenho**  
    Verifica se há problemas conhecidos, analisa as práticas recomendadas e coleta dados de diagnóstico. Essa análise leva vários minutos para ser executada. Saiba mais [Windows](https://aka.ms/perfinsights/quick) ou [Linux](https://aka.ms/perfinsightslinux/quick)

* **Análise de desempenho**  
    Inclui todas as verificações na análise rápida de desempenho e monitora o alto consumo de recursos. Use esta versão para solucionar problemas gerais de desempenho, como alta utilização de CPU, memória e disco. Essa análise leva 30 segundos a 15 minutos, dependendo da duração selecionada. Saiba mais [Windows](https://aka.ms/perfinsights/vmslow) ou [Linux](https://aka.ms/perfinsightslinux/vmslow)

* **Análise de desempenho avançada**`*`  
    Inclui todas as verificações na análise de desempenho e coleta um ou mais dos rastreamentos, conforme listado nas seções a seguir. Use este cenário para solucionar problemas complexos que exigem rastreamentos adicionais. A execução desse cenário por períodos mais longos aumentará o tamanho geral da saída de diagnóstico, dependendo do tamanho da VM e das opções de rastreamento selecionadas. Essa análise leva 30 segundos a 15 minutos para ser executada, dependendo da duração selecionada. [Saiba mais](https://aka.ms/perfinsights/advanced)

* **Análise de arquivos do Azure**`*`  
    Inclui todas as verificações na análise de desempenho e captura um rastreamento de rede e contadores SMB. Use este cenário para solucionar problemas de desempenho de arquivos do Azure. Essa análise leva 30 segundos a 15 minutos para ser executada, dependendo da duração selecionada. [Saiba mais](https://aka.ms/perfinsights/azurefiles)

>[!Note]
>[`*`] Esses cenários de análise só têm suporte no Windows.

![Captura de tela do painel Executar diagnóstico dentro da folha diagnóstico de desempenho](media/performance-diagnostics/run-diagnostics-pane.png)

### <a name="provide-symptoms-optional"></a>Fornecer sintomas (opcional)

Selecione quaisquer sintomas preselecionados na lista ou adicione novos sintomas. Isso nos ajuda a melhorar a análise no futuro.

### <a name="provide-support-request-number-if-available-optional"></a>Forneça o número da solicitação de suporte, se disponível (opcional)

Se você estiver trabalhando com um engenheiro de suporte da Microsoft em um tíquete de suporte existente, forneça o número do tíquete de suporte.

### <a name="review-the-privacy-policy-and-legal-terms-and-select-the-check-box-to-acknowledge-required"></a>Examine a política de privacidade e os termos legais e marque a caixa de seleção para confirmar (obrigatório)

Para executar o diagnóstico, você deve concordar com os termos legais e aceitar a política de privacidade.

### <a name="select-ok-to-run-the-diagnostics"></a>Selecione OK para executar o diagnóstico

Uma notificação é exibida à medida que o diagnóstico de desempenho começa a ser instalado. Depois que a instalação for concluída, você verá uma notificação que indica que a instalação foi bem-sucedida. Em seguida, a análise selecionada é executada durante a duração especificada. Esse seria um bom momento para reproduzir o problema de desempenho para que os dados de diagnóstico possam ser capturados no horário correto.

Após a conclusão da análise, os seguintes itens são carregados nas tabelas do Azure e um contêiner BLOB (objeto binário grande) na conta de armazenamento especificada:

* Todos os insights e informações relacionadas sobre a execução
* Um arquivo compactado (. zip) de saída (chamado **PerformanceDiagnostics_yyyy-mm-dd_hh-mm-SS-FFF. zip** ) no Windows e um arquivo de bola tar (chamado **PerformanceDiagnostics_yyyy-mm-dd_hh-mm-SS-FFF. tar. gz** ) no Linux que contém arquivos de log
* Um relatório HTML

Após o carregamento, um novo relatório de diagnóstico é listado no portal do Azure.

![Captura de tela de uma lista de relatórios de diagnóstico na folha diagnóstico de desempenho](media/performance-diagnostics/diagnostics-report-list.png)

## <a name="how-to-change-performance-diagnostics-settings"></a>Como alterar as configurações de diagnóstico de desempenho

Use o botão da barra de ferramentas **configurações** para alterar a conta de armazenamento onde os insights e a saída de diagnóstico podem ser armazenados. Você pode usar a mesma conta de armazenamento para várias VMs que usam o diagnóstico de desempenho. Quando você altera a conta de armazenamento, os relatórios e as informações antigas não são excluídos. No entanto, eles não serão mais exibidos na lista de relatórios de diagnóstico.

## <a name="review-insights-and-performance-diagnostics-report"></a>Revisar relatório de diagnóstico de desempenho e informações

Cada execução de diagnóstico contém uma lista de ideias e recomendações, recursos afetados, arquivos de log e outras informações de diagnósticos avançadas que são coletadas, além de um relatório para exibição offline. Para obter uma lista completa de todos os dados de diagnóstico coletados, consulte **que tipo de informação é coletada pelo PerfInsights?** no [Windows](how-to-use-perfinsights.md#what-kind-of-information-is-collected-by-perfinsights) ou no [Linux](how-to-use-perfinsights-linux.md#what-kind-of-information-is-collected-by-perfinsights).

### <a name="select-a-performance-diagnostics-report"></a>Selecionar um relatório de diagnóstico de desempenho

Você pode usar a lista de relatórios de diagnóstico para localizar todos os relatórios de diagnóstico que foram executados. A lista inclui detalhes sobre a análise que foi usada, informações que foram encontradas e seus níveis de impacto. Selecione uma linha para exibir mais detalhes.

![Captura de tela de seleção de um relatório de diagnóstico na folha diagnóstico de desempenho](media/performance-diagnostics/select-report.png)

### <a name="review-a-performance-diagnostics-report"></a>Examinar um relatório de diagnóstico de desempenho

Cada relatório de diagnóstico de desempenho pode conter várias informações e indicar um nível de impacto de alto, médio ou baixo. Cada Insight também contém recomendações para ajudar a diminuir a preocupação. As informações são agrupadas para facilitar a filtragem.

Os níveis de impacto representam o potencial de problemas de desempenho, com base em fatores como configuração incorreta, problemas conhecidos ou problemas que são relatados por outros usuários. Talvez você ainda não esteja enfrentando um ou mais dos problemas listados. Por exemplo, você pode ter arquivos de log do SQL e arquivos de banco de dados no mesmo disco. Essa condição tem um alto potencial para afunilamentos e outros problemas de desempenho se o uso do banco de dados for alto, enquanto você não perceberá um problema se o uso for baixo.

![Captura de tela da folha visão geral do relatório de diagnóstico de desempenho](media/performance-diagnostics/performance-diagnostics-report-overview.png)

### <a name="reviewing-performance-diagnostics-insights-and-recommendations"></a>Revisando informações e recomendações de diagnóstico de desempenho

Você pode selecionar um insight para exibir mais detalhes sobre os recursos afetados, as atenuações sugeridas e os links de referência.

![Captura de tela de detalhes de um diagnóstico de desempenho](media/performance-diagnostics/insight-detail.png)

### <a name="download-and-review-the-full-performance-diagnostics-report"></a>Baixar e examinar o relatório de diagnóstico de desempenho completo

Você pode usar o botão **baixar relatório** para baixar um relatório HTML que contém informações adicionais de diagnóstico avançado, como configuração de armazenamento e rede, contadores de desempenho, rastreamentos, lista de processos e logs. O conteúdo depende da análise selecionada. Para solução de problemas avançada, o relatório pode conter informações adicionais e gráficos interativos relacionados ao alto uso da CPU, alto uso do disco e processos que consomem memória excessiva. Para obter mais informações sobre o relatório de diagnóstico de desempenho, consulte [Windows](how-to-use-perfinsights.md#review-the-diagnostics-report) ou [Linux](how-to-use-perfinsights-linux.md#review-the-diagnostics-report).

## <a name="manage-performance-diagnostics-reports"></a>Gerenciar relatórios de diagnóstico de desempenho

Você pode excluir um ou mais relatórios de diagnóstico de desempenho usando o botão **excluir relatório** .

## <a name="how-to-uninstall-performance-diagnostics"></a>Como desinstalar o diagnóstico de desempenho

Você pode desinstalar o diagnóstico de desempenho de uma VM. Essa ação remove a extensão de VM, mas não afeta os dados de diagnóstico que estão na conta de armazenamento.

![Captura de tela da folha de ferramentas de diagnóstico de desempenho com o botão desinstalar realçado](media/performance-diagnostics/uninstal-button.png)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="where-is-the-diagnostics-data-from-my-vm-stored"></a>Onde estão os dados de diagnóstico da minha VM armazenada

Todas as informações e relatórios de diagnóstico de desempenho são armazenados em sua própria conta de armazenamento. As informações são armazenadas nas tabelas do Azure. O arquivo de relatórios compactados é armazenado em um contêiner BLOB (objeto binário grande) chamado azdiagextnresults.

Você pode exibir as informações da conta de armazenamento usando o botão configurações na barra de ferramentas.

### <a name="how-do-i-share-this-data-with-microsoft-customer-support"></a>Como fazer compartilhar esses dados com o atendimento ao cliente da Microsoft

Há várias maneiras de compartilhar o relatório de diagnóstico com a Microsoft.

**Opção 1:** Compartilhar automaticamente o relatório mais recente  
Quando você abre um tíquete de suporte com a Microsoft, é importante compartilhar o relatório de diagnóstico de desempenho. Se você optou por compartilhar essas informações com a Microsoft enquanto executa o diagnóstico (selecionando a caixa de seleção "**eu concordo em compartilhar informações de diagnóstico com a Microsoft**"), a Microsoft poderá acessar o relatório de sua conta de armazenamento usando um link SAS para o arquivo zip de saída por até 30 dias a partir da data de execução. Somente o relatório mais recente está disponível para o engenheiro de suporte.

**Opção 2:** Gerar uma assinatura de acesso compartilhado para o arquivo compactado do relatório de diagnóstico  
Você pode compartilhar um link para o arquivo Reports compactado usando assinaturas de acesso compartilhado. Para tal, siga estes passos:

1. Na portal do Azure, navegue até a conta de armazenamento na qual os dados de diagnóstico são armazenados.
1. Selecione **BLOBs** na seção **serviço blob** .
1. Selecione o contêiner **azdiagextnresults** .
1. Selecione o arquivo compactado de saída de diagnóstico de desempenho que você deseja compartilhar.
1. Na guia **gerar SAS** , selecione os critérios para compartilhamento.
1. Clique em **gerar token SAS de BLOB e URL**.
1. Copie a **URL SAS do blob**e compartilhe-a com o engenheiro de suporte.

**Opção 3:** Baixar o relatório da conta de armazenamento

Você também pode localizar o arquivo compactado do relatório de diagnóstico de desempenho usando as etapas 1 a 4 na opção 2. Selecione para baixar o arquivo e, em seguida, compartilhe-o por email ou peça ao engenheiro de suporte para obter instruções para carregar o arquivo.  

### <a name="how-do-i-capture-the-diagnostics-data-at-the-correct-time"></a>Como fazer capturar os dados de diagnóstico no horário correto

Cada execução de diagnóstico de desempenho tem dois estágios:

1. Instale ou atualize a extensão de VM de diagnóstico de desempenho.
1. Execute o diagnóstico para a duração especificada.

Atualmente, não há uma maneira fácil de saber exatamente quando a instalação da extensão da VM é concluída. Geralmente, leva cerca de 45 segundos a 1 minuto para instalar a extensão de VM. Depois que a extensão de VM for instalada, você poderá executar as etapas de reprodução para que o diagnóstico de desempenho Capture o conjunto correto de dados para solução de problemas.

## <a name="next-steps"></a>Passos seguintes

Depois de examinar o relatório e as informações de diagnóstico de desempenho, se você ainda não puder determinar a causa do problema e precisar de mais ajuda, poderá abrir um tíquete de suporte com o atendimento ao cliente da Microsoft.

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione **obter suporte**. Para obter informações sobre como usar o suporte do Azure, leia as [perguntas frequentes sobre suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).
