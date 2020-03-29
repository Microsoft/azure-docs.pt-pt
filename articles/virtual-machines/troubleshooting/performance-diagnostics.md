---
title: Diagnósticos de desempenho para máquinas virtuais Azure Microsoft Docs
description: Introduz diagnósticos de desempenho azure para Windows.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70080606"
---
# <a name="performance-diagnostics-for-azure-virtual-machines"></a>Diagnóstico de desempenho para máquinas virtuais do Azure

A ferramenta de diagnóstico de desempenho ajuda-o a resolver problemas de desempenho que podem afetar uma máquina virtual Windows ou Linux (VM). Os cenários de resolução de problemas suportados incluem verificações rápidas sobre questões conhecidas e boas práticas, e problemas complexos que envolvem desempenho lento de VM ou alta utilização de CPU, espaço de disco ou memória.

Pode executar diagnósticos de desempenho diretamente a partir do portal Azure, onde também pode rever insights e um relatório sobre vários registos, configuração rica e dados de diagnóstico. Recomendamos que execute diagnósticos de desempenho e reveja os dados de insights e diagnósticos antes de contactar o Microsoft Support.

> [!NOTE]
> Para o Windows, os diagnósticos de Desempenho são atualmente suportados em VMs que têm a versão 4.5 do SDK .NET ou uma versão posterior instalada. Para que os passos para executar diagnósticos de desempenho em VMs clássicos, consulte a [extensão VM](performance-diagnostics-vm-extension.md)de Diagnóstico de Desempenho do Azure .

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

### <a name="windows"></a>Windows

Windows 10, Windows 8, Windows 8 Enterprise, Windows 8 Pro, Windows 8.1, Windows Server 2016, Windows Server 2012, Windows Server 2012 Datacenter, Windows Server 2012 R2, Windows Server 2012 R2 Datacenter, Windows Server 2012 R2 Standard, Windows Server 2012 Standard, Windows Server 2008 R2, Windows Server 2008 R2 Datacenter, Windows Server 2008 R2 Enterprise, Windows Server 2008 R2 Foundation, Windows Server 2008 R2 SP1, Windows Server 2008 R2 Standard.

### <a name="linux"></a>Linux

Oracle`*`Linux Server 6.10 [ ], 7.3, 7.6, 7.5 (Oracle-Database-Ee 13.8 imagem de mercado), CentOS 6.5 [`*`],`*`7.6, RHEL 7.2, 7.5, 8.0 [ ], Ubuntu 14.04, 16.04, 18.04, Debian 8, 9, 10 [`*`], SLES ] 12 ]`*`

>[!Note]
>[`*`] Por favor, consulte [questões conhecidas](how-to-use-perfinsights-linux.md#known-issues)

## <a name="install-and-run-performance-diagnostics-on-your-vm"></a>Instale e execute diagnósticos de desempenho no seu VM

O diagnóstico de desempenho instala uma extensão VM que executa uma ferramenta de diagnóstico que se chama PerfInsights. PerfInsights está disponível tanto para [Windows](https://aka.ms/perfinsights) como [Linux](https://aka.ms/perfinsightslinux). Para instalar e executar diagnósticos de desempenho, siga estes passos:

1. Na coluna de comandos à esquerda, selecione **máquinas Virtuais**.
1. A partir da lista de nomes VM, selecione o VM em que pretende executar diagnósticos.
1. Na coluna de comandos correta, selecione **diagnósticos**de desempenho .

    ![Screenshot do portal Azure, com botão de diagnóstico de desempenho de instalação em destaque](media/performance-diagnostics/performance-diagnostics-install.png)

    > [!NOTE]
    > Nesta imagem, a lâmina dos nomes vm está escondida.
1. Selecione uma conta de armazenamento (opcional)

    Se pretender utilizar uma única conta de armazenamento para armazenar os resultados dos diagnósticos de desempenho para vários VMs, pode selecionar uma conta de armazenamento clicando no botão **Definições** na barra de ferramentas. Clique no botão **OK** assim que selecionar a conta de armazenamento.

    Se não especificar uma conta de armazenamento, uma nova conta de armazenamento será criada por padrão.

    ![Screenshot da lâmina de diagnóstico de desempenho, com botão de barra de ferramentas Definições realçado](media/performance-diagnostics/settings-button.png)

    ![Screenshot da seleção da conta de armazenamento da lâmina de definições de diagnóstico de desempenho](media/performance-diagnostics/select-storage-account.png)

1. Selecione o botão de diagnóstico de **desempenho Instalar.**
1. Selecione a caixa de verificação de **diagnósticos executar** se pretender eexecutar um diagnóstico após a instalação estar concluída. Se fizer esta seleção, poderá escolher o cenário de análise de desempenho e opções relacionadas.

    ![Screenshot dos diagnósticos de desempenho instalar botão](media/performance-diagnostics/install-diagnostics-button.png)

## <a name="select-an-analysis-scenario-to-run"></a>Selecione um cenário de análise para executar

Os seguintes cenários de análise estão disponíveis no portal Azure. Selecione uma análise, dependendo do problema de desempenho que está a ter. Selecione as opções de duração e rastreio necessárias para a análise.

* **Análise rápida do desempenho**  
    Verifica questões conhecidas, analisa as melhores práticas e recolhe dados de diagnóstico. Esta análise leva vários minutos para correr. Saiba mais [Windows](https://aka.ms/perfinsights/quick) ou [Linux](https://aka.ms/perfinsightslinux/quick)

* **Análise de desempenho**  
    Inclui todos os controlos na análise rápida do desempenho e monitoriza o alto consumo de recursos. Utilize esta versão para resolver problemas de desempenho geral, tais como alta CPU, memória e utilização do disco. Esta análise demora 30 segundos a 15 minutos, dependendo da duração selecionada. Saiba mais [Windows](https://aka.ms/perfinsights/vmslow) ou [Linux](https://aka.ms/perfinsightslinux/vmslow)

* **Análise avançada de desempenho**`*`  
    Inclui todos os controlos na análise de desempenho e recolhe um ou mais dos vestígios, conforme listado nas seguintes secções. Use este cenário para resolver problemas complexos que requerem vestígios adicionais. Executar este cenário por períodos mais longos aumentará o tamanho global da produção de diagnósticos, dependendo do tamanho do VM e das opções de rastreio que são selecionadas. Esta análise leva 30 segundos a 15 minutos para ser executada, dependendo da duração selecionada. [Mais informações](https://aka.ms/perfinsights/advanced)

* **Análise de Ficheiros Azure**`*`  
    Inclui todos os controlos na análise de desempenho e captura um traço de rede e contadores SMB. Use este cenário para resolver o desempenho dos ficheiros Azure. Esta análise leva 30 segundos a 15 minutos para ser executada, dependendo da duração selecionada. [Mais informações](https://aka.ms/perfinsights/azurefiles)

>[!Note]
>[`*`] Estes cenários de análise só são suportados no Windows.

![Screenshot de executar diagnósticos painel dentro da lâmina de diagnóstico de desempenho](media/performance-diagnostics/run-diagnostics-pane.png)

### <a name="provide-symptoms-optional"></a>Fornecer sintomas (opcional)

Selecione quaisquer sintomas pré-selecionados da lista ou adicione novos sintomas. Isto ajuda-nos a melhorar a análise no futuro.

### <a name="provide-support-request-number-if-available-optional"></a>Fornecer número de pedido de apoio, se disponível (opcional)

Se estiver a trabalhar com um engenheiro de suporte da Microsoft num bilhete de suporte existente, forneça o número do bilhete de apoio.

### <a name="review-the-privacy-policy-and-legal-terms-and-select-the-check-box-to-acknowledge-required"></a>Reveja a política de privacidade e os termos legais e selecione a caixa de verificação para reconhecer (obrigatório)

Para executar os diagnósticos, deve concordar com os termos legais e aceitar a política de privacidade.

### <a name="select-ok-to-run-the-diagnostics"></a>Selecione OK para executar os diagnósticos

É apresentada uma notificação à medida que os diagnósticos de desempenho começam a ser instalados. Após a instalação estar concluída, consulte uma notificação que indica que a instalação foi bem sucedida. A análise selecionada é então executada durante a duração especificada. Esta seria uma boa altura para reproduzir o problema de desempenho para que os dados de diagnóstico possam ser capturados no momento correto.

Após a análise estar concluída, os seguintes itens são enviados para as tabelas Azure e um recipiente binário de objetos grandes (BLOB) na conta de armazenamento especificada:

* Todos os insights e informações relacionadas sobre a corrida
* Um ficheiro comprimido (.zip) de saída (denominado **PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.zip** ) no Windows e um ficheiro de bola de alcatrão (chamado **PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.tar.gz** ) no Linux que contém ficheiros de log
* Um relatório HTML

Após o upload, um novo relatório de diagnóstico está listado no portal Azure.

![Screenshot de uma lista de relatórios de diagnóstico na lâmina de diagnóstico de desempenho](media/performance-diagnostics/diagnostics-report-list.png)

## <a name="how-to-change-performance-diagnostics-settings"></a>Como alterar as definições de diagnóstico de desempenho

Utilize o botão de barra de ferramentas **Definições** para alterar a conta de armazenamento onde os conhecimentos de diagnóstico e a saída podem ser armazenados. Pode utilizar a mesma conta de armazenamento para vários VMs que utilizam diagnósticos de desempenho. Quando muda a conta de armazenamento, os relatórios e insights antigos não são apagados. No entanto, deixarão de ser apresentados na lista de relatórios de diagnóstico.

## <a name="review-insights-and-performance-diagnostics-report"></a>Rever insights e relatório de diagnóstico de desempenho

Cada execução de diagnóstico contém uma lista de insights e recomendações, recursos afetados, ficheiros de registo e outras informações ricas de diagnóstico que são recolhidas, além de um relatório para visualização offline. Para obter uma lista completa de todos os dados de diagnóstico recolhidos, consulte **que tipo de informação é recolhida pela PerfInsights no** [Windows](how-to-use-perfinsights.md#what-kind-of-information-is-collected-by-perfinsights) ou [no Linux](how-to-use-perfinsights-linux.md#what-kind-of-information-is-collected-by-perfinsights).

### <a name="select-a-performance-diagnostics-report"></a>Selecione um relatório de diagnóstico de desempenho

Pode usar a lista de relatórios de diagnóstico para encontrar todos os relatórios de diagnóstico que foram executados. A lista inclui detalhes sobre a análise que foi usada, insights que foram encontrados, e seus níveis de impacto. Selecione uma linha para ver mais detalhes.

![Screenshot de selecionar um relatório de diagnóstico da lâmina de diagnóstico performance](media/performance-diagnostics/select-report.png)

### <a name="review-a-performance-diagnostics-report"></a>Reveja um relatório de diagnóstico de desempenho

Cada relatório de diagnóstico de desempenho pode conter várias informações e indicar um nível de impacto de Alto, Médio ou Baixo. Cada insight também contém recomendações para ajudar a diminuir a preocupação. Insights são agrupados para uma filtragem fácil.

Os níveis de impacto representam o potencial para problemas de desempenho, com base em fatores como a configuração errada, problemas conhecidos ou problemas que são relatados por outros utilizadores. Pode ainda não estar a passar por uma ou mais das questões listadas. Por exemplo, pode ter ficheiros de registo SQL e ficheiros de base de dados no mesmo disco de dados. Esta condição tem um elevado potencial para estrangulamentos e outros problemas de desempenho se o uso da base de dados for elevado, enquanto você pode não notar um problema se o uso for baixo.

![Screenshot de performance diagnósticos relatório visão geral lâmina](media/performance-diagnostics/performance-diagnostics-report-overview.png)

### <a name="reviewing-performance-diagnostics-insights-and-recommendations"></a>Revisão de insights e recomendações de diagnóstico de desempenho

Pode selecionar uma visão para ver mais detalhes sobre os recursos afetados, atenuações sugeridas e links de referência.

![Screenshot de um detalhe de insight de diagnóstico de desempenho](media/performance-diagnostics/insight-detail.png)

### <a name="download-and-review-the-full-performance-diagnostics-report"></a>Descarregue e reveja o relatório completo de diagnósticos de desempenho

Pode utilizar o botão **de descarregamento** para descarregar um relatório HTML que contém informações adicionais de diagnóstico ricas, tais como armazenamento e configuração de rede, contadores de desempenho, vestígios, lista de processos e registos. O conteúdo depende da análise selecionada. Para resolução avançada de problemas, o relatório pode conter informações adicionais e gráficos interativos relacionados com o elevado uso de CPU, uso elevado do disco e processos que consomem memória excessiva. Para obter mais informações sobre o relatório de diagnóstico de desempenho, consulte [Windows](how-to-use-perfinsights.md#review-the-diagnostics-report) ou [Linux](how-to-use-perfinsights-linux.md#review-the-diagnostics-report).

## <a name="manage-performance-diagnostics-reports"></a>Gerir relatórios de diagnóstico de desempenho

Pode eliminar um ou mais relatórios de diagnóstico de desempenho utilizando o botão **de relatório Delete.**

## <a name="how-to-uninstall-performance-diagnostics"></a>Como desinstalar diagnósticos de desempenho

Pode desinstalar diagnósticos de desempenho a partir de um VM. Esta ação remove a extensão VM, mas não afeta quaisquer dados de diagnóstico que se encontre na conta de armazenamento.

![Screenshot da barra de ferramentas de diagnóstico de desempenho com botão Desinstalar em destaque](media/performance-diagnostics/uninstal-button.png)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="where-is-the-diagnostics-data-from-my-vm-stored"></a>Onde estão os dados de diagnóstico do meu VM armazenados

Todos os conhecimentos e relatórios de diagnóstico de desempenho são armazenados na sua própria conta de armazenamento. Os insights são armazenados dentro das mesas do Azure. O ficheiro comprimido de relatórios é armazenado num recipiente binário de objetos grandes (BLOB) que é chamado de azdiagextnresults.

Pode visualizar as informações da conta de armazenamento utilizando o botão Definições na barra de ferramentas.

### <a name="how-do-i-share-this-data-with-microsoft-customer-support"></a>Como partilho estes dados com o Suporte ao Cliente da Microsoft

Existem várias formas de partilhar o relatório de diagnóstico com a Microsoft.

**Opção 1:** Partilhe automaticamente o último relatório  
Ao abrir um bilhete de suporte com a Microsoft, é importante partilhar o relatório de diagnóstico de desempenho. Se optou por partilhar estas informações com a Microsoft enquanto executa os diagnósticos (selecionando a caixa de verificação "**Concordo em partilhar informações**de diagnóstico com a Microsoft "), a Microsoft poderá aceder ao relatório a partir da sua conta de armazenamento utilizando uma ligação SAS ao ficheiro zip de saída até 30 dias a contar da data de execução. Apenas o último relatório está disponível para o engenheiro de apoio.

**Opção 2:** Gerar uma Assinatura de Acesso Partilhado para o relatório de diagnóstico sonhá-lo  
Pode partilhar um link para o ficheiro comprimido dos relatórios utilizando assinaturas de acesso partilhado. Para tal, siga estes passos:

1. No portal Azure, navegue na conta de armazenamento na qual os dados de diagnóstico são armazenados.
1. Selecione **Blobs** sob a secção **de serviço Blob.**
1. Selecione o recipiente de **resultados azdiagextn.**
1. Selecione o ficheiro comprimido de saída de diagnóstico de desempenho que pretende partilhar.
1. No separador **Generate SAS,** selecione os critérios de partilha.
1. Clique **Em gerar token e URL blob SAS**.
1. Copie o **URL Blob SAS**e partilhe-o com o engenheiro de suporte.

**Opção 3:** Faça o download do relatório da conta de armazenamento

Também pode localizar o ficheiro de diagnóstico de desempenho comprimido utilizando os passos 1-4 na Opção 2. Selecione para descarregar o ficheiro e, em seguida, partilhá-lo através de e-mail ou pedir instruções ao engenheiro de suporte para fazer o upload do ficheiro.  

### <a name="how-do-i-capture-the-diagnostics-data-at-the-correct-time"></a>Como capturar os dados de diagnóstico no momento certo

Cada execução de diagnóstico de desempenho tem duas fases:

1. Instale ou atualize a extensão VM de diagnóstico de desempenho.
1. Executar os diagnósticos durante a duração especificada.

Atualmente, não existe uma maneira fácil de saber exatamente quando a instalação de extensão VM está completa. Geralmente demora cerca de 45 segundos a 1 minuto para instalar a extensão VM. Após a instalação da extensão VM, pode executar os seus passos de repro para que os diagnósticos de desempenho capturem o conjunto correto de dados para resolução de problemas.

## <a name="next-steps"></a>Passos seguintes

Depois de rever os insights e relatórios de diagnóstico de desempenho, se ainda não conseguir determinar a causa do problema e precisar de mais ajuda, pode abrir um bilhete de suporte com o Suporte ao Cliente da Microsoft.

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de apoio ao Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/), e selecione **Obter suporte**. Para obter informações sobre a utilização do suporte do Azure, leia o suporte do [Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).
