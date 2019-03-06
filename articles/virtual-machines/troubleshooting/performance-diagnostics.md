---
title: Diagnóstico de desempenho para máquinas virtuais do Azure | Documentos da Microsoft
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
ms.devlang: na
ms.topic: troubleshooting
ms.date: 9/20/2018
ms.author: anandh
ms.openlocfilehash: c2089f9f6267f318dafe641a6a5b22e7e87427ca
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57441071"
---
# <a name="performance-diagnostics-for-azure-virtual-machines"></a>Diagnóstico de desempenho para máquinas virtuais do Azure

A ferramenta de diagnóstico de desempenho ajuda-o a resolver problemas de desempenho que podem afetar uma máquina virtual de Windows (VM). Resolução de problemas de cenários suportados incluem verificações rápidas sobre problemas conhecidos e práticas recomendadas e problemas complexos que envolvem o desempenho lento de VMS ou alto uso da CPU, espaço em disco ou memória. 

Pode executar o diagnóstico de desempenho diretamente a partir do portal do Azure, onde também pode consultar informações e um relatório sobre vários registos de configuração avançada e dados de diagnóstico. Recomendamos que execute o diagnóstico de desempenho e reveja os dados de diagnóstico e de informações antes de contactar o Support da Microsoft.

> [!NOTE]
> Diagnóstico de desempenho é atualmente suportado em VMs do Windows que têm a versão do SDK do .NET 4.5 ou posterior instalado. Para obter os passos executar o diagnóstico de desempenho em VMs clássicas, consulte [extensão de VM de diagnóstico de desempenho do Azure](performance-diagnostics-vm-extension.md).

### <a name="supported-operating-systems"></a>Sistemas Operativos Suportados
Windows 10, Windows 8, Windows 8 Enterprise, Windows 8 Pro, Windows 8.1, Windows Server 2016, Windows Server 2012, Windows Server 2012 Datacenter, Windows Server 2012 R2, Windows Server 2012 R2 Datacenter, Windows Server 2012 R2 Standard, Windows Server 2012 Standard, Windows Server 2008 R2, Windows Server 2008 R2 Datacenter, Windows Server 2008 R2 Enterprise, Windows Server 2008 R2 Foundation, Windows Server 2008 R2 SP1, Windows Server 2008 R2 Standard.

## <a name="install-and-run-performance-diagnostics-on-your-vm"></a>Instalar e executar o diagnóstico de desempenho na sua VM
Diagnóstico de desempenho instala uma extensão de VM que executa uma ferramenta de diagnóstico com o nome [PerfInsights](https://aka.ms/perfinsights). Para instalar e executar o diagnóstico de desempenho, siga estes passos:
1.  Na coluna esquerda de comandos, selecione **máquinas virtuais**.
1.  Na lista de nomes VM, selecione a VM que pretende executar diagnósticos.
1.  Na coluna da direita de comandos, selecione **diagnóstico de desempenho**.

    ![Captura de ecrã do portal do Azure, com o botão de diagnóstico de desempenho de instalação realçado](media/performance-diagnostics/performance-diagnostics-install.png)

    > [!NOTE]
    > Nesta captura de ecrã, está oculta o painel de nomes VM.
1. Selecione uma conta de armazenamento (opcional)

    Se pretender utilizar uma única conta de armazenamento para armazenar os resultados de diagnóstico de desempenho de várias VMs, pode selecionar uma conta de armazenamento ao clicar o **definições** botão na barra de ferramentas. Clique nas **OK** botão depois de selecionar a conta de armazenamento.

    Se não especificar uma conta de armazenamento, será criada uma nova conta de armazenamento por predefinição.

    ![Painel de diagnósticos de captura de ecrã de desempenho, com o botão da barra de ferramentas de definições realçado](media/performance-diagnostics/settings-button.png)

    ![Captura de ecrã da seleção de conta de armazenamento do painel de definições de diagnóstico de desempenho](media/performance-diagnostics/select-storage-account.png)

1. Selecione o **instalar o diagnóstico de desempenho** botão.
1. Selecione o **executar diagnósticos** caixa de verificação se pretender executar um diagnóstico depois de concluída a instalação. Se tornar esta seleção, poderá escolher o cenário de análise de desempenho e opções relacionadas.

    ![Botão instalar o diagnóstico de captura de ecrã de desempenho](media/performance-diagnostics/install-diagnostics-button.png)

## <a name="select-an-analysis-scenario-to-run"></a>Selecionar um cenário de análise para executar

Os seguintes cenários de análise estão disponíveis a partir do portal do Azure. Selecione uma análise, dependendo do problema de desempenho que estão a receber. Selecione as opções de duração e o rastreio para a análise, conforme necessário.

* **Análise de desempenho rápido**  
    Verifica a existência de problemas conhecidos, analisa as práticas recomendadas e recolhe dados de diagnóstico. Esta análise demora vários minutos para ser executado. [Saiba mais](https://aka.ms/perfinsights/quick)

* **Análise de desempenho**  
    Inclui todas as verificações na análise de desempenho rápido e monitoriza o consumo de recursos elevados. Utilize esta versão para resolver problemas de desempenho gerais, como elevada da CPU, memória e utilização do disco. Esta análise demora 30 segundos para 15 minutos, consoante o período selecionado. [Saiba mais](https://aka.ms/perfinsights/vmslow) 
    
* **Análise de desempenho avançadas**  
    Inclui todas as verificações na análise de desempenho e recolhe um ou mais dos rastreios, conforme listado nas secções seguintes. Utilize neste cenário para resolver problemas complexos que exigem rastreios adicionais. Em execução neste cenário, por períodos mais longos aumentará o tamanho geral da saída de diagnóstico, dependendo do tamanho da VM e as opções de rastreio que estão selecionadas. Esta análise demora 30 segundos para 15 minutos para ser executado, consoante o período selecionado. [Saiba mais](https://aka.ms/perfinsights/advanced) 
    
* **Análise de ficheiros do Azure**  
    Inclui todas as verificações na análise de desempenho e capturar um rastreio de rede e de contadores SMB. Use esse cenário para resolver problemas de desempenho dos ficheiros do Azure. Esta análise demora 30 segundos para 15 minutos para ser executado, consoante o período selecionado. [Saiba mais](https://aka.ms/perfinsights/azurefiles)


![Captura de ecrã de executar o painel de diagnóstico dentro do painel de diagnóstico de desempenho](media/performance-diagnostics/run-diagnostics-pane.png)

### <a name="provide-symptoms-optional"></a>Fornecer sintomas (opcionais)
Selecione qualquer sintoma pré-selecionados na lista ou adicionar novos sintomas. Isto ajuda-na melhorar a análise no futuro. 

### <a name="provide-support-request-number-if-available-optional"></a>Fornecer o número de pedido de suporte, se estiver disponível (opcional)
Se estiver a trabalhar com um engenheiro de suporte da Microsoft num pedido de suporte existente, forneça o número de pedido de suporte. 

### <a name="review-the-privacy-policy-and-legal-terms-and-select-the-check-box-to-acknowledge-required"></a>Reveja a política de privacidade e termos legais e selecione a caixa de verificação confirmar (obrigatório)
Para executar o diagnóstico, tem de aceitar os termos legais e aceite a política de privacidade.

### <a name="select-ok-to-run-the-diagnostics"></a>Selecione OK para executar o diagnóstico 
É apresentada uma notificação que diagnóstico de desempenho é inicializado instalar. Após a instalação estiver concluída, verá uma notificação que indica que a instalação foi bem-sucedida. A análise selecionada, em seguida, é executada durante o período especificado. Isso seria um bom momento para reproduzir o problema de desempenho para que os dados de diagnóstico que podem ser capturados na hora certa. 

Depois da análise estiver concluída, os seguintes itens são carregados para tabelas do Azure e um contentor de objeto binário grande (BLOB) na conta de armazenamento especificada:

*   Todas as informações e informações relacionadas sobre a execução
*   Um ficheiro comprimido (. zip) de saída (com o nome **PerformanceDiagnostics_yyyy-MM-dd_hh-mm-ss-fff.zip**) que contém ficheiros de registo
*   Um relatório HTML

Após o carregamento, um novo relatório de diagnóstico está listado no portal do Azure.

![Captura de ecrã de uma lista de relatório de diagnóstico no painel de diagnósticos de desempenho](media/performance-diagnostics/diagnostics-report-list.png)

## <a name="how-to-change-performance-diagnostics-settings"></a>Como alterar as definições de diagnóstico de desempenho
Utilize o **definições** botão da barra de ferramentas para alterar a conta de armazenamento onde podem ser armazenadas as informações de diagnóstico e a saída. Pode utilizar a mesma conta de armazenamento de várias VMs que utilizam o diagnóstico de desempenho. Quando alterar a conta de armazenamento, os relatórios antigos e as informações não são eliminadas. No entanto, eles já não serão apresentados na lista de relatórios de diagnóstico. 

## <a name="review-insights-and-performance-diagnostics-report"></a>Reveja as informações e relatório de diagnóstico de desempenho
Cada execução de diagnóstico contém uma lista de informações e recomendações, recursos afetados, ficheiros de registo e outras informações de diagnóstico avançado que são recolhidas, além de um relatório para exibição offline. Para obter uma lista completa de todos os dados de diagnóstico recolhidos, consulte [que tipo de informações é recolhido pelo PerfInsights?](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-use-perfinsights#what-kind-of-information-is-collected-by-perfinsights) 

### <a name="select-a-performance-diagnostics-report"></a>Selecione um relatório de diagnóstico de desempenho
Pode utilizar a lista de relatórios de diagnóstico para localizar todos os relatórios de diagnóstico que foram executados. A lista inclui detalhes sobre a análise que foi utilizado, informações que foram encontradas e seus níveis de impacto. Selecione uma linha para ver mais detalhes.

![Captura de ecrã da seleção de um relatório de diagnóstico do painel de diagnóstico de desempenho](media/performance-diagnostics/select-report.png)

### <a name="review-a-performance-diagnostics-report"></a>Reveja um relatório de diagnóstico de desempenho
Cada relatório de diagnóstico de desempenho pode conter várias percepções e indicar um nível de impacto de alta, média ou baixa. Cada insight também contém recomendações para ajudar a diminuir a preocupação. Insights é agrupado para filtragem fácil. 

Níveis de impacto representam o potencial para problemas de desempenho, com base em fatores como configuração incorreta, problemas, conhecidos ou problemas reportados por outros utilizadores. Pode não ainda estar a ter um ou mais dos problemas listados. Por exemplo, pode ter ficheiros de registo SQL e ficheiros de base de dados no mesmo disco de dados. Esta condição tem um alto potencial de afunilamentos e outros problemas de desempenho se a utilização de base de dados for alta, ao passo que talvez não notasse um problema se a utilização for baixa.

![Painel de descrição geral de relatório de diagnóstico de captura de ecrã de desempenho](media/performance-diagnostics/performance-diagnostics-report-overview.png)

### <a name="reviewing-performance-diagnostics-insights-and-recommendations"></a>Rever informações de diagnóstico de desempenho e recomendações
Pode selecionar uma informação para ver mais detalhes sobre os recursos afetados, atenuações sugeridas e ligações de referência. 

![Captura de ecrã de um detalhe de informações de diagnóstico de desempenho](media/performance-diagnostics/insight-detail.png)

### <a name="download-and-review-the-full-performance-diagnostics-report"></a>Transfira e reveja o relatório de diagnóstico de desempenho total
Pode utilizar o **transferir relatório** botão para transferir um relatório em HTML que contém informações de diagnóstico avançadas adicionais, tais como o armazenamento e configuração de rede, contadores de desempenho, rastreios, lista de processos e os registos. O conteúdo depende da análise selecionada. Para resolução de problemas avançada, o relatório pode conter informações adicionais e gráficos interativos que estão relacionados com a utilização, utilização elevada do disco e processos que consomem memória excessiva da CPU elevada. Para obter mais informações sobre o relatório de diagnóstico de desempenho, consulte [reveja o relatório de diagnóstico](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-use-perfinsights#review-the-diagnostics-report).

## <a name="manage-performance-diagnostics-reports"></a>Gerir relatórios de diagnóstico de desempenho
Pode eliminar um ou mais relatórios de diagnóstico de desempenho com o **eliminar relatório** botão.

## <a name="how-to-uninstall-performance-diagnostics"></a>Como desinstalar o diagnóstico de desempenho
É possível desinstalar o diagnóstico de desempenho de uma VM. Esta ação remove a extensão de VM, mas não afeta quaisquer dados de diagnóstico que está na conta de armazenamento. 

![Captura de ecrã do desempenho diagnóstico painel barra de ferramentas com o botão de desinstalação realçado](media/performance-diagnostics/uninstal-button.png)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="where-is-the-diagnostics-data-from-my-vm-stored"></a>Onde estão os dados de diagnóstico da minha VM armazenado? 
Todas as informações de diagnóstico de desempenho e relatórios são armazenados na sua própria conta de armazenamento. Informações são armazenadas dentro das tabelas do Azure. O ficheiro comprimido de relatórios é armazenado num contentor de objeto binário grande (BLOB) com o nome azdiagextnresults.

Pode ver as informações de conta de armazenamento através do botão definições na barra de ferramentas. 

### <a name="how-do-i-share-this-data-with-microsoft-customer-support"></a>Como posso partilhar estes dados com o suporte técnico da Microsoft? 
Existem várias formas de partilhar o relatório de diagnóstico com a Microsoft.

**Opção 1:** Partilhar o relatório mais recente automaticamente  
Quando abrir um pedido de suporte com a Microsoft, é importante partilhar o relatório de diagnóstico de desempenho. Se tiver optado por partilhar estas informações com a Microsoft enquanto executa o diagnóstico (ao selecionar a "**concordo partilhar informações de diagnóstico com a Microsoft**" caixa de verificação), Microsoft será capaz de aceder ao relatório do seu armazenamento conta com uma ligação SAS para o ficheiro zip de saída durante 30 dias a contar da data de execução. Apenas o relatório mais recente está disponível para o engenheiro de suporte. 

**Opção 2:** Gerar uma assinatura de acesso partilhado para o ficheiro comprimido do relatório de diagnóstico  
Pode partilhar uma ligação para o ficheiro comprimido relatórios através da utilização de assinaturas de acesso partilhado. Para tal, siga estes passos: 
1.  No portal do Azure, navegue para a conta de armazenamento na qual os dados de diagnóstico são armazenados.
1.  Selecione **Blobs** sob a **serviço Blob** secção. 
1.  Selecione o **azdiagextnresults** contentor.
1.  Selecione o desempenho diagnóstico saída ficheiro comprimido que pretende partilhar.
1.  Sobre o **gerar SAS** separador, selecione os critérios para a partilha. 
1.  Clique em **gerar o token SAS do blob e o URL**.
1.  Copiar o **URL de SAS do Blob**e partilhá-lo com o engenheiro de suporte. 

**Opção 3:** Transferir o relatório da conta de armazenamento

Também pode localizar o ficheiro comprimido de relatório de diagnóstico do desempenho, utilizando os passos 1 a 4 na opção 2. Selecione esta opção para transferir o ficheiro e, em seguida, partilhá-lo por e-mail ou peça o engenheiro de suporte para obter instruções carregar o ficheiro.  

### <a name="how-do-i-capture-the-diagnostics-data-at-the-correct-time"></a>Como posso capturar os dados de diagnóstico no momento correto?
Cada execução de diagnóstico de desempenho tem duas fases: 
1.  Instalar ou atualizar o extensão de VM de diagnóstico de desempenho.
1.  Execute o diagnóstico durante o período especificado.

Atualmente, não existe nenhuma forma fácil de saber exatamente quando a instalação da extensão VM estiver concluída. Em geral, que demora cerca de 45 segundos a 1 minuto a instalar a extensão VM. Depois da extensão de VM está instalada, pode executar as etapas de reprodução para ter o diagnóstico de desempenho capturar o conjunto correto de dados para resolução de problemas. 

## <a name="next-steps"></a>Passos Seguintes
Depois de rever as informações de diagnóstico de desempenho e o relatório, se ainda não é possível determinar a causa do problema e precisar de mais ajuda, pode abrir um pedido de suporte com o suporte de cliente da Microsoft. 

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione **obter suporte**. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).
