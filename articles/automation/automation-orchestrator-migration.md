---
title: Migrar do Orquestrador para a Automação Azure (Beta)
description: Este artigo diz como migrar livros de corridas e pacotes de integração de Orchestrator para A Automação Azure.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 7e8bb448e88d7aaa3d59ec9392a3b3ac69373c4d
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715499"
---
# <a name="migrate-from-orchestrator-to-azure-automation-beta"></a>Migrar do Orquestrador para a Automação Azure (Beta)

Runbooks in [System Center 2012 - O Orchestrator](https://technet.microsoft.com/library/hh237242.aspx) baseia-se em atividades de pacotes de integração que são escritos especificamente para Orchestrator, enquanto os livros de execução em Azure Automation são baseados no Windows PowerShell. [Os livros gráficos](automation-runbook-types.md#graphical-runbooks) da Azure Automation têm uma aparência semelhante aos livros de corridas do Orchestrator, com as suas atividades representando cmdlets PowerShell, livros infantis e ativos. Além de converter os próprios livros de execução, tem de converter os pacotes de integração com as atividades que os livros de execução utilizam para módulos de integração com cmdlets Windows PowerShell. 

[A Automatização](https://technet.microsoft.com/library/dn469260.aspx) de Gestão de Serviços (SMA) armazena e executa livros de execução no seu datacenter local como o Orchestrator, e utiliza os mesmos módulos de integração que o Azure Automation. O Runbook Converter converte livros de orquestração em livros gráficos, que não são suportados em SMA. Ainda pode instalar os Módulos de Integração de Orquestradores de Módulos de Atividades Padrão e Sistema no SMA, mas tem de reescrever manualmente os seus livros de [execução.](https://technet.microsoft.com/library/dn469262.aspx)

## <a name="download-the-orchestrator-migration-toolkit"></a>Descarregue o conjunto de ferramentas de migração orchestrator

O primeiro passo na migração é descarregar o Kit de Ferramentas de [Migração orquestrador](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all)a sistema center . Este kit de ferramentas inclui ferramentas para ajudá-lo na conversão de livros de execução do Orchestrator para a Automação Azure.  

## <a name="import-the-standard-activities-module"></a>Importar o módulo Atividades Padrão

Importar o [Módulo de Atividades Padrão](https://docs.microsoft.com/system-center/orchestrator/standard-activities?view=sc-orch-2019) para a Automação Azure. Isto inclui versões convertidas de atividades de Orquestrador padrão que os livros de execução gráficos convertidos podem usar.

## <a name="import-orchestrator-integration-modules"></a>Módulos de integração de Orquestradores de Importação

A Microsoft fornece pacotes de integração para a [construção](https://technet.microsoft.com/library/hh295851.aspx) de livros de execução para automatizar componentes do System Center e outros produtos. Alguns destes pacotes de integração baseiam-se atualmente no OIT, mas não podem atualmente ser convertidos em módulos de integração devido a questões conhecidas. Módulos de [Integração orquestradora](https://www.microsoft.com/download/details.aspx?id=49555) do Centro de Importação em Automação Azure para os pacotes de integração utilizados pelos seus livros de execução que acedem ao System Center. Este pacote inclui versões convertidas dos pacotes de integração que podem ser importados para a Automação de Azure e automação de serviços.  

## <a name="convert-integration-packs"></a>Converter pacotes de integração

Utilize o Conversor de [Pacotes integração](https://docs.microsoft.com/system-center/orchestrator/orch-integration-toolkit/integration-pack-wizard?view=sc-orch-2019) para converter quaisquer pacotes de integração criados utilizando o Kit de Ferramentas de [Integração Orquestrador (OIT)](https://technet.microsoft.com/library/hh855853.aspx) para módulos de integração baseados no PowerShell que podem ser importados para Automação Azure ou Automação de Gestão de Serviços. Ao executar o Conversor de Pacotes de Integração, é-lhe apresentado um assistente que lhe permite selecionar um ficheiro de integração (.oip). O assistente lista então as atividades incluídas nesse pacote de integração e permite-lhe selecionar quais as atividades a migrar. Quando completa o assistente, cria um módulo de integração que inclui um cmdlet correspondente para cada uma das atividades no pacote de integração original.

> [!NOTE]
> Não é possível utilizar o Conversor de Pacotes integração para converter pacotes de integração que não foram criados com o OIT. Existem também alguns pacotes de integração fornecidos pela Microsoft que não podem ser atualmente convertidos com esta ferramenta. As versões convertidas destes pacotes de integração são fornecidas para download para que possam ser instaladas na Azure Automation ou Naautomatização de Gestão de Serviços.

### <a name="parameters"></a>Parâmetros

Quaisquer propriedades de uma atividade no pacote de integração são convertidas em parâmetros do cmdlet correspondente no módulo de integração.  Os cmdlets Windows PowerShell têm um conjunto de [parâmetros comuns](https://technet.microsoft.com/library/hh847884.aspx) que podem ser usados com todos os cmdlets. Por exemplo, o parâmetro -Verbose faz com que um cmdlet produza informações detalhadas sobre o seu funcionamento.  Nenhum cmdlet pode ter um parâmetro com o mesmo nome que um parâmetro comum. Se uma atividade tiver uma propriedade com o mesmo nome que um parâmetro comum, o assistente pede-lhe que forneça outro nome para o parâmetro.

### <a name="monitor-activities"></a>Monitorizar atividades

Monitorize os livros em Orchestrator comece com uma atividade de [monitor](https://technet.microsoft.com/library/hh403827.aspx) e corra continuamente à espera de ser invocado por um determinado evento. A Azure Automation não suporta os livros de monitorização, pelo que quaisquer atividades de monitorização no pacote de integração não são convertidas. Em vez disso, é criado um cmdlet de espaço reservado no módulo de integração para a atividade do monitor.  Este cmdlet não tem funcionalidade, mas permite que qualquer livro de execução convertido que o utilize seja instalado. Este livro de execução não é capaz de funcionar na Automatização Azure, mas pode ser instalado para que possa modificá-lo.

O Orchestrator inclui um conjunto de [atividades padrão](https://technet.microsoft.com/library/hh403832.aspx) que não estão incluídas num pacote de integração, mas são usadas por muitos livros de corridas.  O módulo Atividades Standard é um módulo de integração que inclui um equivalente cmdlet para cada uma destas atividades. Deve instalar este módulo de integração na Automatização Azure antes de importar quaisquer livros de execução convertidos que utilizem uma atividade padrão.

Além de apoiar livros de corridas convertidos, os cmdlets no módulo de atividades padrão podem ser usados por alguém familiarizado com o Orchestrator para construir novos livros de execução na Azure Automation. Embora a funcionalidade de todas as atividades padrão possa ser realizada com cmdlets, podem funcionar de forma diferente. Os cmdlets no módulo de atividades padrão convertidos funcionam da mesma forma que as suas atividades correspondentes e utilizam os mesmos parâmetros. Isto pode ajudá-lo na transição para os livros de execução da Automação Azure.

## <a name="convert-orchestrator-runbooks"></a>Converter livros de orquestração

O Orchestrator Runbook Converter converte os livros de corridas de Orchestrator em [livros gráficos](automation-runbook-types.md#graphical-runbooks) que podem ser importados para a Automação Azure. O Conversor de Livro de Execução é implementado como um módulo PowerShell com o cmdlet `ConvertFrom-SCORunbook` que faz a conversão. Quando instala o conversor, cria um atalho para uma sessão powerShell que carrega o cmdlet.   

Aqui estão os passos básicos para converter um livro de corridas e importá-lo para a Automação Azure. Os detalhes da utilização do cmdlet são fornecidos mais tarde nesta secção.

1. Exportar um ou mais livros de livros do Orquestrador.
2. Obtenha módulos de integração para todas as atividades no livro de execução.
3. Converta os livros de orquetadores no ficheiro exportado.
4. Reveja as informações em diários de bordo para validar a conversão e para determinar quaisquer tarefas manuais necessárias.
5. Importar livros convertidos em Automação Azure.
6. Crie quaisquer ativos necessários na Automação Azure.
7. Editar o livro de execução na Automatização Azure para modificar as atividades necessárias.

A sintaxe `ConvertFrom-SCORunbook` é:

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath - Caminho para o ficheiro de exportação contendo os livros de execução para converter.
* Módulo - Comma lista delimitada de módulos de integração contendo atividades nos livros de execução.
* OutputFolder - Caminho para a pasta para criar livros gráficos convertidos.

O comando de exemplo seguinte converte os livros de execução num ficheiro de exportação chamado **MyRunbooks.ois_export**.  Estes livros de execução utilizam os pacotes de integração do Ative Directory and Data Protection Manager.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="use-runbook-converter-log-files"></a>Utilizar ficheiros de registo do Conversor de Livro de Execução

O Conversor de Livro de Execução cria os seguintes ficheiros de registo no mesmo local que o livro de execução convertido.  Se os ficheiros já existirem, são substituídos com informações da última conversão.

| Ficheiro | Conteúdos |
|:--- |:--- |
| Conversor de livro de execução - Progress.log |Etapas detalhadas da conversão, incluindo informações para cada atividade convertidas com sucesso e aviso para cada atividade não convertida. |
| Conversor de livro de execução - Resumo.log |Resumo da última conversão, incluindo quaisquer advertências e tarefas de acompanhamento que precisa para realizar, tais como a criação de uma variável necessária para o livro de execução convertido. |

### <a name="export-runbooks-from-orchestrator"></a>Livros de exportação do Orquestrador

O Runbook Converter trabalha com um ficheiro de exportação do Orchestrator que contém um ou mais livros de execução.  Cria um livro de execução de Automação Azure correspondente para cada livro de execução de Orchestrator no ficheiro de exportação.  

Para exportar um livro de corridas do Orchestrator, clique no nome do livro de corridas em Runbook Designer e selecione **Export**.  Para exportar todos os livros de execução numa pasta, clique no nome da pasta e selecione **Export .**

### <a name="convert-runbook-activities"></a>Converter atividades de livro de corridas

O Runbook Converter converte cada atividade no livro de execução do Orquestrador para uma atividade correspondente na Automação Azure.  Para as atividades que não podem ser convertidas, uma atividade de espaço reservado é criada no livro de execução com texto de aviso.  Depois de importar o livro de ensaios convertido para a Automação Azure, deve substituir qualquer uma destas atividades por atividades válidas que realizem a funcionalidade necessária.

Quaisquer atividades de Orquestradores no Módulo de Atividades Padrão são convertidas. Existem algumas atividades de Orquestrador padrão que não estão neste módulo e não são convertidas. Por exemplo, `Send Platform Event` não tem equivalente Azure Automation uma vez que o evento é específico para Orchestrator.

[As atividades](https://technet.microsoft.com/library/hh403827.aspx) de monitor não são convertidas, uma vez que não há equivalente a elas na Automação Azure. As exceções são as atividades de monitorização em pacotes de integração convertidos que são convertidos para a atividade do espaço reservado.

Qualquer atividade a partir de um pacote de integração convertido é convertida se você fornecer o caminho para o módulo de integração com o `modules` parâmetro. Para pacotes de integração do System Center, pode utilizar os Módulos de Integração orquestradora do System Center.

### <a name="manage-orchestrator-resources"></a>Gerir recursos orquestradores

O Runbook Converter apenas converte livros de execução, e não outros recursos orquestradores, tais como contadores, variáveis ou ligações.  Os balcões não são suportados na Automação Azure.  As variáveis e ligações são suportadas, mas deve criá-las manualmente. Os ficheiros de registo informam-no se o livro de execução requer esses recursos e especifica os recursos correspondentes que precisa de criar na Automação Azure para que o livro de execução convertido funcione corretamente.

Por exemplo, um livro de execução pode usar uma variável para povoar um valor particular numa atividade.  O livro de execução convertido converte a atividade e especifica um ativo variável na Automação Azure com o mesmo nome que a variável Orchestrator. Esta ação é notada no ficheiro **Runbook Converter - Summary.log** que é criado após a conversão. Deve criar manualmente este ativo variável na Automatização Azure antes de utilizar o livro de execução.

### <a name="work-with-orchestrator-input-parameters"></a>Trabalhar com parâmetros de entrada de Orquestrador

Os livros de execução em Orchestrator aceitam parâmetros de entrada com a `Initialize Data` atividade.  Se o livro de execução que está a ser convertido inclui esta atividade, então é criado um parâmetro de [entrada](automation-graphical-authoring-intro.md#handle-runbook-input) no livro de execução da Automatização Azure para cada parâmetro da atividade.  Uma atividade de controlo do [Script workflow](automation-graphical-authoring-intro.md#use-activities) é criada no livro de execução convertido que recupera e devolve cada parâmetro. Quaisquer atividades no livro de execução que utilizem um parâmetro de entrada referem-se à saída desta atividade.

A razão pela qual esta estratégia é usada é para melhor espelhar a funcionalidade no livro de execução do Orquestrador.  As atividades em novos livros de execução gráficadevem referir-se diretamente aos parâmetros de entrada utilizando uma fonte de dados de entrada do Livro de Execução.

### <a name="invoke-runbook-activity"></a>Invocar atividade do Livro de Corridas

Os livros em Orchestrator iniciam outros livros com a `Invoke Runbook` atividade. Se o livro de execução que está a ser convertido inclui esta atividade e a `Wait for completion` opção for definida, então é criada uma atividade de livro de execução para ele no livro de execução convertido.  Se a `Wait for completion` opção não estiver definida, então é criada uma atividade do Script workflow que utiliza o [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0) para iniciar o livro de execução. Depois de importar o livro de ensaios convertido para a Automação Azure, deve modificar esta atividade com as informações especificadas na atividade.

## <a name="create-orchestrator-assets"></a>Criar ativos de Orquestrador

O Conversor de Livros de Execução não converte ativos de Orquestradores. Deve criar manualmente quaisquer ativos orquestradores necessários na Automação Azure.

## <a name="configure-hybrid-runbook-worker"></a>Configure Hybrid Runbook Worker

O Orchestrator armazena livros de execução num servidor de base de dados e executa-os em servidores de livros de corridas, ambos no seu centro de dados local. Os livros de execução em Automatização Azure são armazenados na nuvem Azure e podem ser executados no seu centro de dados local usando um [Trabalhador de Runbook Híbrido](automation-hybrid-runbook-worker.md). Configure um trabalhador para executar os seus livros convertidos em Orchestrator, uma vez que são projetados para funcionar em servidores locais e aceder a recursos locais.

## <a name="related-articles"></a>Artigos relacionados

* [System Center 2012 - Orchestrator](https://technet.microsoft.com/library/hh237242.aspx)
* [Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx)
* [Função de Trabalho de Runbook Híbrida](automation-hybrid-runbook-worker.md)
* [Atividades Padrão orquestradoras](https://technet.microsoft.com/library/hh403832.aspx)
* [Descarregue o conjunto de ferramentas de migração do Centro de Sistemas de Descarregamento](https://www.microsoft.com/download/details.aspx?id=47323)