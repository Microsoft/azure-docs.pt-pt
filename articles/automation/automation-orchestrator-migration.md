---
title: Migrando do Orchestrator para a automação do Azure
description: Descreve como migrar runbooks e pacotes de integração do System Center Orchestrator para a automação do Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b34554798130d9741318e0f518c32a41f82a17e3
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849671"
---
# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migrando do Orchestrator para a automação do Azure (beta)
Os runbooks no [System Center Orchestrator](https://technet.microsoft.com/library/hh237242.aspx) são baseados em atividades de pacotes de integração que são escritos especificamente para o Orchestrator enquanto os Runbooks na automação do Azure são baseados no Windows PowerShell.  Os [Runbooks gráficos](automation-runbook-types.md#graphical-runbooks) na automação do Azure têm uma aparência semelhante aos Runbooks do Orchestrator com suas atividades que representam cmdlets do PowerShell, runbooks filho e ativos.

O [System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) inclui ferramentas para ajudá-lo a converter Runbooks do Orchestrator para a automação do Azure.  Além de converter os próprios runbooks, você deve converter os pacotes de integração com as atividades que os runbooks usam para módulos de integração com cmdlets do Windows PowerShell.  

Veja a seguir o processo básico para converter runbooks do Orchestrator na automação do Azure.  Cada uma dessas etapas é descrita detalhadamente nas seções a seguir.

1. Baixe o [System Center Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) que contém as ferramentas e os módulos abordados neste artigo.
2. Importe o [módulo atividades padrão](#standard-activities-module) na automação do Azure.  Isso inclui versões convertidas de atividades do orquestrador padrão que podem ser usadas por runbooks convertidos.
3. Importe [módulos de integração do System Center Orchestrator](#system-center-orchestrator-integration-modules) para a automação do Azure para os pacotes de integração usados por seus runbooks que acessam o System Center.
4. Converta pacotes de integração personalizados e de terceiros usando o [conversor do pacote de integração](#integration-pack-converter) e importe para a automação do Azure.
5. Converta runbooks do Orchestrator usando o [runbook converter](#runbook-converter) e instale na automação do Azure.
6. Crie manualmente os ativos do Orchestrator necessários na automação do Azure, pois o runbook converter não converte esses recursos.
7. Configure um [Hybrid runbook Worker](#hybrid-runbook-worker) no seu data center local para executar runbooks convertidos que acessarão os recursos locais.

## <a name="service-management-automation"></a>Service Management Automation
[Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx) (SMA) armazena e executa runbooks no data center local, como o Orchestrator, e usa os mesmos módulos de integração que a automação do Azure. O [runbook converter](#runbook-converter) converte Runbooks do Orchestrator em runbooks gráficos, embora não tenham suporte no SMA.  Você ainda pode instalar o [módulo de atividades padrão](#standard-activities-module) e os [módulos de integração do System Center Orchestrator](#system-center-orchestrator-integration-modules) no SMA, mas você deve [reescrever seus runbooks](https://technet.microsoft.com/library/dn469262.aspx)manualmente.

## <a name="hybrid-runbook-worker"></a>Função de Trabalho de Runbook Híbrida
Os Runbooks no Orchestrator são armazenados em um servidor de banco de dados e executados em servidores runbook, tanto no data center local.  Os Runbooks na automação do Azure são armazenados na nuvem do Azure e podem ser executados no data center local usando uma [Hybrid runbook Worker](automation-hybrid-runbook-worker.md).  É assim que você geralmente executará runbooks convertidos do Orchestrator, pois eles são projetados para serem executados em servidores locais.

## <a name="integration-pack-converter"></a>Conversor de pacote de integração
O conversor do pacote de integração converte pacotes de integração criados usando o [Orchestrator Integration Toolkit (OIT)](https://technet.microsoft.com/library/hh855853.aspx) em módulos de integração baseados no Windows PowerShell que podem ser importados para a automação do Azure ou Service Management Automation.  

Ao executar o conversor do pacote de integração, você verá um assistente que permitirá selecionar um arquivo do pacote de integração (. OIP).  O assistente, em seguida, lista as atividades incluídas no pacote de integração e permite que você Selecione quais serão migradas.  Quando você conclui o assistente, ele cria um módulo de integração que inclui um cmdlet correspondente para cada uma das atividades no pacote de integração original.

### <a name="parameters"></a>Parâmetros
Todas as propriedades de uma atividade no pacote de integração são convertidas em parâmetros do cmdlet correspondente no módulo de integração.  Os cmdlets do Windows PowerShell têm um conjunto de [parâmetros comuns](https://technet.microsoft.com/library/hh847884.aspx) que podem ser usados com todos os cmdlets.  Por exemplo, o parâmetro-Verbose faz com que um cmdlet gere informações detalhadas sobre sua operação.  Nenhum cmdlet pode ter um parâmetro com o mesmo nome que um parâmetro comum.  Se uma atividade tiver uma propriedade com o mesmo nome que um parâmetro comum, o assistente solicitará que você forneça outro nome para o parâmetro.

### <a name="monitor-activities"></a>Monitorar atividades
Monitore runbooks no Orchestrator comece com uma [atividade de monitor](https://technet.microsoft.com/library/hh403827.aspx) e execute continuamente aguardando para ser invocado por um evento específico.  A automação do Azure não dá suporte a runbooks de monitor, portanto, nenhuma atividade de monitor no pacote de integração será convertida.  Em vez disso, um cmdlet de espaço reservado é criado no módulo de integração para a atividade monitorar.  Esse cmdlet não tem funcionalidade, mas permite que qualquer runbook convertido que o use seja instalado.  Esse runbook não poderá ser executado na automação do Azure, mas pode ser instalado para que você possa modificá-lo.

### <a name="integration-packs-that-cannot-be-converted"></a>Pacotes de integração que não podem ser convertidos
Os pacotes de integração que não foram criados com OIT não podem ser convertidos com o conversor do pacote de integração. Há também alguns pacotes de integração fornecidos pela Microsoft que atualmente não podem ser convertidos com essa ferramenta.  Versões convertidas desses pacotes de integração foram [fornecidas para download](#system-center-orchestrator-integration-modules) para que possam ser instaladas na automação do Azure ou Service Management Automation.

## <a name="standard-activities-module"></a>Módulo de atividades padrão
O Orchestrator inclui um conjunto de [atividades padrão](https://technet.microsoft.com/library/hh403832.aspx) que não estão incluídas em um pacote de integração, mas são usadas por muitos runbooks.  O módulo atividades padrão é um módulo de integração que inclui um cmdlet equivalente para cada uma dessas atividades.  Você deve instalar este módulo de integração na automação do Azure antes de importar quaisquer runbooks convertidos que usam uma atividade padrão.

Além de dar suporte a runbooks convertidos, os cmdlets no módulo atividades padrão podem ser usados por alguém familiarizado com o Orchestrator para criar novos runbooks na automação do Azure.  Embora a funcionalidade de todas as atividades padrão possa ser executada com cmdlets, elas podem operar de forma diferente.  Os cmdlets no módulo atividades padrão convertidas funcionarão da mesma forma que suas atividades correspondentes e usarão os mesmos parâmetros.  Isso pode ajudar o autor do runbook do Orchestrator existente em sua transição para runbooks de automação do Azure.

## <a name="system-center-orchestrator-integration-modules"></a>Módulos de integração do System Center Orchestrator
A Microsoft fornece [pacotes de integração](https://technet.microsoft.com/library/hh295851.aspx) para a criação de runbooks para automatizar os componentes do System Center e outros produtos.  Atualmente, alguns desses pacotes de integração estão baseados em OIT, mas não podem ser convertidos em módulos de integração devido a problemas conhecidos.  Os [módulos de integração do System Center Orchestrator](https://www.microsoft.com/download/details.aspx?id=49555) incluem versões convertidas desses pacotes de integração que podem ser importados para a automação do Azure e Service Management Automation.  

Pela versão RTM dessa ferramenta, as versões atualizadas dos pacotes de integração com base em OIT que podem ser convertidas com o conversor de pacote de integração serão publicadas.  As diretrizes também serão fornecidas para ajudá-lo a converter runbooks usando atividades dos pacotes de integração não baseados no OIT.

## <a name="runbook-converter"></a>Conversor de runbook
O runbook Converter converte runbooks do Orchestrator em [runbooks gráficos](automation-runbook-types.md#graphical-runbooks) que podem ser importados para a automação do Azure.  

O runbook Converter é implementado como um módulo do PowerShell com um cmdlet chamado **ConvertFrom-SCORunbook** que executa a conversão.  Quando você instalar a ferramenta, ela criará um atalho para uma sessão do PowerShell que carregará o cmdlet.   

Veja a seguir o processo básico para converter um runbook do Orchestrator e importá-lo para a automação do Azure.  As seções a seguir fornecem mais detalhes sobre como usar a ferramenta e trabalhar com runbooks convertidos.

1. Exportar um ou mais runbooks do Orchestrator.
2. Obter módulos de integração para todas as atividades no runbook.
3. Converta os runbooks do Orchestrator no arquivo exportado.
4. Examine as informações nos logs para validar a conversão e determinar as tarefas manuais necessárias.
5. Importar runbooks convertidos para a automação do Azure.
6. Crie quaisquer ativos necessários na automação do Azure.
7. Edite o runbook na automação do Azure para modificar as atividades necessárias.

### <a name="using-runbook-converter"></a>Usando o runbook converter
A sintaxe para **ConvertFrom-SCORunbook** é a seguinte:

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath-caminho para o arquivo de exportação que contém os runbooks a serem convertidos.
* Módulo – lista delimitada por vírgulas de módulos de integração que contêm atividades nos runbooks.
* OutputFolder-caminho para a pasta para criar runbooks gráficos convertidos.

O comando de exemplo a seguir converte os runbooks em um arquivo de exportação chamado **MyRunbooks. ois_export**.  Esses runbooks usam os pacotes de integração Active Directory e Data Protection Manager.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="log-files"></a>Ficheiros de registo
O runbook converter criará os seguintes arquivos de log no mesmo local que o runbook convertido.  Se os arquivos já existirem, eles serão substituídos por informações da última conversão.

| Ficheiros | Conteúdos |
|:--- |:--- |
| Runbook converter-Progress. log |Etapas detalhadas da conversão, incluindo informações para cada atividade convertida com êxito e aviso para cada atividade não convertida. |
| Runbook converter-Summary. log |Resumo da última conversão, incluindo quaisquer avisos e tarefas de acompanhamento que você precisa executar, como a criação de uma variável necessária para o runbook convertido. |

### <a name="exporting-runbooks-from-orchestrator"></a>Exportando runbooks do Orchestrator
O runbook Converter funciona com um arquivo de exportação do Orchestrator que contém um ou mais runbooks.  Ele criará um runbook de automação do Azure correspondente para cada runbook do Orchestrator no arquivo de exportação.  

Para exportar um runbook do Orchestrator, clique com o botão direito do mouse no nome do runbook em Runbook Designer e selecione **Exportar**.  Para exportar todos os runbooks em uma pasta, clique com o botão direito do mouse no nome da pasta e selecione **Exportar**.

### <a name="runbook-activities"></a>Atividades do Runbook
O runbook Converter converte cada atividade no runbook do Orchestrator em uma atividade correspondente na automação do Azure.  Para as atividades que não podem ser convertidas, uma atividade de espaço reservado é criada no runbook com texto de aviso.  Depois de importar o runbook convertido para a automação do Azure, você deve substituir qualquer uma dessas atividades com atividades válidas que executam a funcionalidade necessária.

Todas as atividades do Orchestrator no [módulo atividades padrão](#standard-activities-module) serão convertidas.  No entanto, há algumas atividades padrão do orquestrador que não estão nesse módulo e não são convertidas.  Por exemplo, o **evento de plataforma de envio** não tem equivalente de automação do Azure, pois o evento é específico do Orchestrator.

[As atividades do monitor](https://technet.microsoft.com/library/hh403827.aspx) não são convertidas, pois não há nenhum equivalente a elas na automação do Azure.  A exceção são as atividades de monitor em [pacotes de integração convertidos](#integration-pack-converter) que serão convertidas na atividade de espaço reservado.

Qualquer atividade de um [pacote de integração convertido](#integration-pack-converter) será convertida se você fornecer o caminho para o módulo de integração com o parâmetro **modules** .  Para os pacotes de integração do System Center, você pode usar os [módulos de integração do System Center Orchestrator](#system-center-orchestrator-integration-modules).

### <a name="orchestrator-resources"></a>Recursos do Orchestrator
O runbook converter só converte runbooks, e não outros recursos do Orchestrator, como contadores, variáveis ou conexões.  Não há suporte para contadores na automação do Azure.  Há suporte para variáveis e conexões, mas você deve criá-las manualmente.  Os arquivos de log informarão se o runbook requer esses recursos e especifica os recursos correspondentes que você precisa criar na automação do Azure para que o runbook convertido funcione corretamente.

Por exemplo, um runbook pode usar uma variável para popular um valor específico em uma atividade.  O runbook convertido converterá a atividade e especificará um ativo de variável na automação do Azure com o mesmo nome da variável de orquestrador.  Isso será observado no arquivo **runbook converter-Summary. log** que é criado após a conversão.  Você precisará criar manualmente esse ativo de variável na automação do Azure antes de usar o runbook.

### <a name="input-parameters"></a>Parâmetros de entrada
Os Runbooks no Orchestrator aceitam parâmetros de entrada com a atividade **inicializar dados** .  Se o runbook que está sendo convertido incluir essa atividade, um [parâmetro de entrada](automation-graphical-authoring-intro.md#runbook-input-and-output) no runbook de automação do Azure será criado para cada parâmetro na atividade.  Uma atividade de [controle de script de fluxo de trabalho](automation-graphical-authoring-intro.md#activities) é criada no runbook convertido que recupera e retorna cada parâmetro.  Todas as atividades no runbook que usam um parâmetro de entrada referem-se à saída dessa atividade.

O motivo pelo qual essa estratégia é usada é o melhor espelhamento da funcionalidade no runbook do Orchestrator.  As atividades em novos runbooks gráficos devem se referir diretamente aos parâmetros de entrada usando uma fonte de dados de entrada de runbook.

### <a name="invoke-runbook-activity"></a>Invocar atividade de runbook
Os runbooks no Orchestrator iniciam outros runbooks com a atividade **invocar runbook** . Se o runbook que está sendo convertido incluir essa atividade e a opção **aguardar a conclusão** estiver definida, uma atividade de runbook será criada para ela no runbook convertido.  Se a opção **aguardar a conclusão** não estiver definida, será criada uma atividade script de fluxo de trabalho que usa **Start-AzureAutomationRunbook** para iniciar o runbook.  Depois de importar o runbook convertido para a automação do Azure, você deve modificar essa atividade com as informações especificadas na atividade.

## <a name="related-articles"></a>Artigos relacionados
* [System Center 2012 – Orchestrator](https://technet.microsoft.com/library/hh237242.aspx)
* [Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx)
* [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)
* [Atividades padrão do Orchestrator](https://technet.microsoft.com/library/hh403832.aspx)
* [Baixe o System Center Orchestrator Migration Toolkit](https://www.microsoft.com/en-us/download/details.aspx?id=47323)

