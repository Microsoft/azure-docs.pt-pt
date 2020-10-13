---
title: Diagnósticos de desempenho para máquinas virtuais Azure Microsoft Docs
description: Introduz diagnósticos de desempenho Azure para windows.
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
ms.openlocfilehash: 04b70e593e8b1bee8beb72ac88bc8441bc38bb9a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91963249"
---
# <a name="performance-diagnostics-for-azure-virtual-machines"></a>Diagnóstico de desempenho para máquinas virtuais do Azure

A ferramenta de diagnóstico de desempenho ajuda-o a resolver problemas de desempenho que podem afetar uma máquina virtual Windows ou Linux (VM). Os cenários de resolução de problemas suportados incluem verificações rápidas de questões conhecidas e boas práticas, e problemas complexos que envolvem desempenho lento de VM ou alta utilização de CPU, espaço em disco ou memória.

Pode executar diagnósticos de desempenho diretamente a partir do portal Azure, onde também pode rever insights e um relatório sobre vários registos, configuração rica e dados de diagnóstico. Recomendamos que execute diagnósticos de desempenho e reveja os dados de insights e diagnósticos antes de contactar o Microsoft Support.

> [!NOTE]
> Para o Windows, os diagnósticos de desempenho são atualmente suportados em VMs que tenham a versão 4.5 da NET SDK ou uma versão posterior instalada. Para que os passos para executar diagnósticos de desempenho em VMs clássicos, consulte [a extensão VM de Diagnóstico de Desempenho Azure](performance-diagnostics-vm-extension.md).

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

### <a name="windows"></a>Windows

* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows 10
* Windows 8.1
* Windows 8

### <a name="linux"></a>Linux

- As seguintes distribuições são atualmente suportadas:

    | Distribuição               | Versão                                         |
    |----------------------------|-------------------------------------------------|
    | Servidor Oracle Linux        | 6.10 [ `*` ], 7.3, 7.6, 7.5 |
    | CentOS                     | 6.5 `*` [ ], 7.6                                    |
    | RHEL                       | 7.2, 7.5, 8.0 `*` .                               |
    | Ubuntu                     | 14.04, 16.04, 18.04, 20.04                               |
    | Debian                     | 8, 9, 10 `*` .                                    |
    | SLES                       | 12 SP4 `*` [ ]                                      |
    |                            |                                                   |

>[!Note]
>[ `*` ] Por favor, consulte [questões conhecidas](how-to-use-perfinsights-linux.md#known-issues)

## <a name="install-and-run-performance-diagnostics-on-your-vm"></a>Instale e execute diagnósticos de desempenho no seu VM

O diagnóstico de desempenho instala uma extensão VM que executa uma ferramenta de diagnóstico chamada PerfInsights. PerfInsights está disponível tanto para [Windows](./how-to-use-perfinsights.md) como [Linux.](./how-to-use-perfinsights-linux.md) Para instalar e executar diagnósticos de desempenho, siga estes passos:

1. Na coluna esquerda de comandos, selecione **Máquinas Virtuais**.
1. A partir da lista de nomes VM, selecione o VM em que pretende executar diagnósticos.
1. Na coluna de comandos direita, selecione **Diagnósticos**de Desempenho .

    ![Screenshot do portal Azure, com botão de diagnóstico de desempenho de instalação em destaque](media/performance-diagnostics/performance-diagnostics-install.png)

    > [!NOTE]
    > Nesta imagem, a lâmina dos nomes VM está escondida.
1. Selecione uma conta de armazenamento (opcional)

    Se pretender utilizar uma única conta de armazenamento para armazenar os resultados dos diagnósticos de desempenho de vários VMs, pode selecionar uma conta de armazenamento clicando no botão Definições na barra de **ferramentas.** Clique no botão **OK** assim que selecionar a conta de armazenamento.

    Se não especificar uma conta de armazenamento, será criada uma nova conta de armazenamento por padrão.

    ![Screenshot da lâmina de diagnóstico de desempenho, com o botão de barra de ferramentas definições realçado](media/performance-diagnostics/settings-button.png)

    ![Screenshot da seleção da conta de armazenamento a partir da lâmina de definições de diagnóstico de desempenho](media/performance-diagnostics/select-storage-account.png)

1. Selecione o botão **de diagnóstico de desempenho de instalação.**
1. Selecione a caixa de verificação **de diagnósticos run** se pretender executar um diagnóstico após a instalação estar concluída. Se fizer esta seleção, poderá escolher o cenário de análise de desempenho e opções relacionadas.

    ![Screenshot do botão de instalação de diagnósticos de desempenho](media/performance-diagnostics/install-diagnostics-button.png)

## <a name="select-an-analysis-scenario-to-run"></a>Selecione um cenário de análise para executar

Os seguintes cenários de análise estão disponíveis no portal Azure. Selecione uma análise, dependendo do problema de desempenho que está a ter. Selecione as opções de duração e rastreio, conforme necessário para a análise.

* **Análise rápida do desempenho**  
    Verifica questões conhecidas, analisa as melhores práticas e recolhe dados de diagnóstico. Esta análise leva vários minutos para ser executada. Saiba mais [Windows](./how-to-use-perfinsights.md) ou [Linux](./how-to-use-perfinsights-linux.md)

* **Análise de desempenho**  
    Inclui todas as verificações na análise rápida do desempenho e monitoriza o alto consumo de recursos. Utilize esta versão para resolver problemas de desempenho geral, tais como cpu alto, memória e utilização do disco. Esta análise demora 30 segundos a 15 minutos, dependendo da duração selecionada. Saiba mais [Windows](./how-to-use-perfinsights.md) ou [Linux](./how-to-use-perfinsights-linux.md)

* **Análise avançada de desempenho**`*`  
    Inclui todas as verificações na análise de desempenho e recolhe um ou mais dos vestígios, conforme listado nas seguintes secções. Use este cenário para resolver problemas complexos que requerem vestígios adicionais. Executar este cenário por períodos mais longos aumentará o tamanho global da produção de diagnósticos, dependendo do tamanho do VM e das opções de rastreio que são selecionadas. Esta análise leva 30 segundos a 15 minutos para ser executada, dependendo da duração selecionada. [Saiba mais](./how-to-use-perfinsights.md)

* **Análise de Ficheiros Azure**`*`  
    Inclui todas as verificações na análise de desempenho e captura um rastreio de rede e contadores SMB. Utilize este cenário para resolver problemas no desempenho dos ficheiros Azure. Esta análise leva 30 segundos a 15 minutos para ser executada, dependendo da duração selecionada. [Saiba mais](./how-to-use-perfinsights.md)

>[!Note]
>[ `*` ] Estes cenários de análise só são suportados no Windows.

![Screenshot do painel de diagnósticos Run dentro da lâmina de diagnóstico de desempenho](media/performance-diagnostics/run-diagnostics-pane.png)

### <a name="provide-symptoms-optional"></a>Fornecer sintomas (opcional)

Selecione quaisquer sintomas pré-selecionados da lista ou adicione novos sintomas. Isto ajuda-nos a melhorar a análise no futuro.

### <a name="provide-support-request-number-if-available-optional"></a>Fornecer número de pedido de apoio, se disponível (opcional)

Se estiver a trabalhar com um engenheiro de suporte da Microsoft num bilhete de apoio existente, forneça o número do bilhete de suporte.

### <a name="review-the-privacy-policy-and-legal-terms-and-select-the-check-box-to-acknowledge-required"></a>Reveja a política de privacidade e os termos legais, e selecione a caixa de verificação para reconhecer (obrigatório)

Para executar os diagnósticos, você deve concordar com os termos legais e aceitar a política de privacidade.

### <a name="select-ok-to-run-the-diagnostics"></a>Selecione OK para executar os diagnósticos

Uma notificação é apresentada à medida que os diagnósticos de desempenho começam a ser instalados. Após a conclusão da instalação, vê uma notificação que indica que a instalação foi bem sucedida. A análise selecionada é então executada para a duração especificada. Esta seria uma boa altura para reproduzir a questão do desempenho para que os dados de diagnóstico possam ser capturados no momento correto.

Após a análise estar concluída, os seguintes itens são enviados para as tabelas Azure e um contentor binário de objeto grande (BLOB) na conta de armazenamento especificada:

* Todas as informações e informações relacionadas sobre a corrida
* Um ficheiro comprimido de saída (.zip) (nomeado **PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.zip) ** no Windows e um ficheiro de bola de alcatrão (denominado **PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.tar.gz)** no Linux que contém ficheiros de registo
* Um relatório HTML

Após o upload, um novo relatório de diagnóstico é listado no portal Azure.

![Screenshot de uma lista de relatórios de diagnósticos na lâmina de diagnóstico de desempenho](media/performance-diagnostics/diagnostics-report-list.png)

## <a name="how-to-change-performance-diagnostics-settings"></a>Como alterar as definições de diagnóstico de desempenho

Utilize o botão de barra de **ferramentas Definições** para alterar a conta de armazenamento onde os insights e saídas de diagnóstico podem ser armazenados. Pode utilizar a mesma conta de armazenamento para vários VMs que utilizam diagnósticos de desempenho. Quando se altera a conta de armazenamento, os relatórios e insights antigos não são apagados. No entanto, deixarão de estar na lista de relatórios de diagnóstico.

## <a name="review-insights-and-performance-diagnostics-report"></a>Relatório de avaliação de insights e diagnósticos de desempenho

Cada execução de diagnóstico contém uma lista de insights e recomendações, recursos afetados, ficheiros de registo e outras informações ricas de diagnóstico que são recolhidas, além de um relatório para visualização offline. Para obter uma lista completa de todos os dados de diagnóstico recolhidos, [Windows](how-to-use-perfinsights.md#what-kind-of-information-is-collected-by-perfinsights) consulte [Linux](how-to-use-perfinsights-linux.md#what-kind-of-information-is-collected-by-perfinsights) **que tipo de informação é recolhida pela PerfInsights?**

### <a name="select-a-performance-diagnostics-report"></a>Selecione um relatório de diagnóstico de desempenho

Pode usar a lista de relatórios de diagnóstico para encontrar todos os relatórios de diagnóstico que foram executados. A lista inclui detalhes sobre a análise que foi usada, insights que foram encontrados, e seus níveis de impacto. Selecione uma linha para ver mais detalhes.

![Screenshot de selecionar um relatório de diagnóstico da lâmina de diagnóstico de desempenho](media/performance-diagnostics/select-report.png)

### <a name="review-a-performance-diagnostics-report"></a>Reveja um relatório de diagnóstico de desempenho

Cada relatório de diagnóstico de desempenho pode conter vários insights e indicar um nível de impacto de Alto, Médio ou Baixo. Cada insight também contém recomendações para ajudar a diminuir a preocupação. Os insights são agrupados para uma filtragem fácil.

Os níveis de impacto representam o potencial para problemas de desempenho, com base em fatores como a configuração errada, problemas conhecidos ou problemas que são reportados por outros utilizadores. Pode ainda não estar a passar por uma ou mais das questões listadas. Por exemplo, pode ter ficheiros de registo SQL e ficheiros de base de dados no mesmo disco de dados. Esta condição tem um elevado potencial para estrangulamentos e outros problemas de desempenho se o uso da base de dados for elevado, enquanto que você pode não notar um problema se o uso for baixo.

![Screenshot do relatório de diagnósticos de desempenho relatório de visão geral](media/performance-diagnostics/performance-diagnostics-report-overview.png)

### <a name="reviewing-performance-diagnostics-insights-and-recommendations"></a>Rever insights e recomendações de diagnóstico de desempenho

Pode selecionar uma visão para ver mais detalhes sobre os recursos afetados, mitigações sugeridas e links de referência.

![Screenshot de um detalhe de insight de diagnóstico de desempenho](media/performance-diagnostics/insight-detail.png)

### <a name="download-and-review-the-full-performance-diagnostics-report"></a>Faça o download e reveja o relatório completo de diagnósticos de desempenho

Pode utilizar o botão **'Relatório de Descarregamento'** para descarregar um relatório HTML que contenha informações adicionais de diagnósticos ricos, tais como armazenamento e configuração de rede, contadores de desempenho, traços, lista de processos e registos. O conteúdo depende da análise selecionada. Para uma resolução avançada de problemas, o relatório pode conter informações adicionais e gráficos interativos relacionados com o uso elevado do CPU, o uso elevado do disco e os processos que consomem memória excessiva. Para obter mais informações sobre o relatório de diagnóstico de desempenho, consulte [Windows](how-to-use-perfinsights.md#review-the-diagnostics-report) ou [Linux](how-to-use-perfinsights-linux.md#review-the-diagnostics-report).

## <a name="manage-performance-diagnostics-reports"></a>Gerir relatórios de diagnóstico de desempenho

Pode eliminar um ou mais relatórios de diagnóstico de desempenho utilizando o botão **'Eliminar relatório'.**

## <a name="how-to-uninstall-performance-diagnostics"></a>Como desinstalar diagnósticos de desempenho

Pode desinstalar diagnósticos de desempenho a partir de um VM. Esta ação remove a extensão VM, mas não afeta quaisquer dados de diagnóstico que se encontrem na conta de armazenamento.

![Screenshot da barra de ferramentas de lâmina de diagnóstico de desempenho com botão Desinstalar realçado](media/performance-diagnostics/uninstal-button.png)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="where-is-the-diagnostics-data-from-my-vm-stored"></a>Onde estão os dados de diagnóstico do meu VM armazenados

Todos os conhecimentos e relatórios de diagnóstico de desempenho são armazenados na sua própria conta de armazenamento. As introspeções são armazenadas dentro das tabelas Azure. O ficheiro comprimido de relatórios é armazenado num recipiente binário de objetos grandes (BLOB) que é denominado azdiagextnresults.

Pode ver as informações da conta de armazenamento utilizando o botão Definições na barra de ferramentas.

### <a name="how-do-i-share-this-data-with-microsoft-customer-support"></a>Como partilho estes dados com o Suporte ao Cliente da Microsoft

Existem várias formas de partilhar o relatório de diagnóstico com a Microsoft.

**Opção 1:** Partilhe automaticamente o último relatório  
Quando abre um bilhete de apoio com a Microsoft, é importante partilhar o relatório de diagnóstico de desempenho. Se optar por partilhar esta informação com a Microsoft enquanto executou os diagnósticos (selecionando a "**concordo em partilhar informações de diagnóstico com a Microsoft**" caixa de verificação), a Microsoft poderá aceder ao relatório a partir da sua conta de armazenamento utilizando um link SAS para o ficheiro zip de saída por até 30 dias a partir da data de execução. Apenas o último relatório está disponível para o engenheiro de apoio.

**Opção 2:** Gerar uma assinatura de acesso partilhado para o relatório de diagnósticos comprimido  
Pode partilhar um link para os relatórios comprimidos através da utilização de Assinaturas de Acesso Partilhado. Para tal, siga estes passos:

1. No portal Azure, consulte a conta de armazenamento na qual os dados de diagnóstico são armazenados.
1. Selecione **Blobs** na secção **de serviço Blob.**
1. Selecione o recipiente **azdiagextnresults.**
1. Selecione o ficheiro comprimido de saída de diagnóstico de desempenho que pretende partilhar.
1. No **separador Gerar SAS,** selecione os critérios de partilha.
1. Clique **em Gerar símbolo SAS blob e URL**.
1. Copie o **URL Blob SAS**e partilhe-o com o engenheiro de suporte.

**Opção 3:** Faça o download do relatório da conta de armazenamento

Também pode localizar o relatório de diagnóstico de desempenho comprimido utilizando os passos 1-4 na Opção 2. Selecione para descarregar o ficheiro e, em seguida, partilhe-o por e-mail ou peça instruções ao engenheiro de suporte para fazer o upload do ficheiro.  

### <a name="how-do-i-capture-the-diagnostics-data-at-the-correct-time"></a>Como posso capturar os dados de diagnóstico no momento correto

Cada execução de diagnóstico de desempenho tem duas fases:

1. Instale ou atualize a extensão VM de diagnóstico de desempenho.
1. Executar os diagnósticos para a duração especificada.

Atualmente não existe uma maneira fácil de saber exatamente quando a instalação de extensão VM está completa. Geralmente demora cerca de 45 segundos a 1 minuto para instalar a extensão VM. Após a instalação da extensão VM, pode executar os seus passos de repro para que os diagnósticos de desempenho capturem o conjunto correto de dados para a resolução de problemas.

## <a name="next-steps"></a>Passos seguintes

Depois de rever os conhecimentos de diagnóstico de desempenho e reportar, se ainda não conseguir determinar a causa do problema e precisar de mais ajuda, pode abrir um bilhete de suporte com o Microsoft Customer Support.

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode apresentar um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione Obter **suporte**. Para obter informações sobre a utilização do suporte Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).