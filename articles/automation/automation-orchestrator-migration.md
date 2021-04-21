---
title: Migrar do Orchestrator para a Automatização do Azure (Beta)
description: Este artigo diz como migrar runbooks e pacotes de integração de Orchestrator para Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c929781772b510639e1e5e47eed19927f408d16a
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830507"
---
# <a name="migrate-from-orchestrator-to-azure-automation-beta"></a>Migrar do Orchestrator para a Automatização do Azure (Beta)

Runbooks in [System Center 2012 - O Orquestrador](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12)) baseia-se em atividades de pacotes de integração que são escritos especificamente para Orquestrador, enquanto os livros de execução em Azure Automation são baseados no Windows PowerShell. [Os runbooks gráficos](automation-runbook-types.md#graphical-runbooks) na Azure Automation têm uma aparência semelhante aos runbooks do Orquestrador, com as suas atividades representando cmdlets PowerShell, livros infantis e ativos. Além de converter os próprios runbooks, é necessário converter os pacotes de integração com as atividades que os runbooks utilizam para módulos de integração com cmdlets Windows PowerShell. 

[A Service Management Automation](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12)) (SMA) armazena e executa livros de recortes no seu datacenter local como o Orchestrator, e utiliza os mesmos módulos de integração que a Azure Automation. O Runbook Converter converte os runbooks do Orquestrador em livros gráficos, que não são suportados em SMA. Ainda pode instalar os Módulos de Integração do Módulo de Atividades Padrão e do Centro de Sistemas orquestradores em SMA, mas tem de reescrever manualmente [os seus livros de execução](/system-center/sma/authoring-automation-runbooks).

## <a name="download-the-orchestrator-migration-toolkit"></a>Descarregue o kit de ferramentas de migração do Orquestrador

O primeiro passo na migração é descarregar o [Kit de Ferramentas de Migração do Orquestrador do Sistema.](https://www.microsoft.com/download/details.aspx?id=47323) Este conjunto de ferramentas inclui ferramentas para o ajudar a converter livros de ensaios do Orquestrador para a Azure Automation.  

## <a name="import-the-standard-activities-module"></a>Importar o módulo de Atividades Padrão

Importe o [Módulo de Atividades Padrão](/system-center/orchestrator/standard-activities) para a Azure Automation. Isto inclui versões convertidas de atividades orquestradoras padrão que os livros gráficos convertidos podem usar.

## <a name="import-orchestrator-integration-modules"></a>Módulos de integração de orquestradores de importação

A Microsoft fornece [pacotes de integração](/previous-versions/system-center/packs/hh295851(v=technet.10)) para a construção de livros para automatizar componentes do System Center e outros produtos. Alguns destes pacotes de integração são atualmente baseados no OIT, mas não podem ser convertidos para módulos de integração devido a problemas conhecidos. [Módulos de Integração de Orquestradores do Centro de Sistemas](https://www.microsoft.com/download/details.aspx?id=49555) de Importação em Azure Automation para os pacotes de integração utilizados pelos seus runbooks que acedem ao System Center. Este pacote inclui versões convertidas dos pacotes de integração que podem ser importados para a Azure Automation and Service Management Automation.  

## <a name="convert-integration-packs"></a>Converter pacotes de integração

Utilize o [Conversor de Pacotes de Integração](/system-center/orchestrator/orch-integration-toolkit/integration-pack-wizard) para converter quaisquer pacotes de integração criados utilizando o Kit de Ferramentas de [Integração de Orquestradores (OIT)](/previous-versions/system-center/developer/hh855853(v=msdn.10)) para módulos de integração baseados em PowerShell que podem ser importados para Azure Automation ou Service Management Automation. Quando executar o Conversor de Pacotes de Integração, é-lhe apresentado um assistente que lhe permite selecionar um ficheiro de pacote de integração (.oip). Em seguida, o assistente lista as atividades incluídas nesse pacote de integração e permite-lhe selecionar quais as atividades a migrar. Quando completa o assistente, cria um módulo de integração que inclui um cmdlet correspondente para cada uma das atividades no pacote de integração original.

> [!NOTE]
> Não é possível utilizar o Conversor de Pacotes integrais para converter pacotes de integração que não foram criados com o OIT. Existem também alguns packs de integração fornecidos pela Microsoft que não podem ser convertidos com esta ferramenta. As versões convertidas destes pacotes de integração são fornecidas para download para que possam ser instaladas na Azure Automation ou na Service Management Automation.

### <a name="parameters"></a>Parâmetros

Quaisquer propriedades de uma atividade no pacote de integração são convertidas em parâmetros do cmdlet correspondente no módulo de integração.  Os cmdlets Windows PowerShell têm um conjunto de [parâmetros comuns](/powershell/module/microsoft.powershell.core/about/about_commonparameters) que podem ser utilizados com todos os cmdlets. Por exemplo, o parâmetro -Verbose faz com que um cmdlet produza informações detalhadas sobre o seu funcionamento.  Nenhum cmdlet pode ter um parâmetro com o mesmo nome que um parâmetro comum. Se uma atividade tiver uma propriedade com o mesmo nome que um parâmetro comum, o assistente pede-lhe que forneça outro nome para o parâmetro.

### <a name="monitor-activities"></a>Monitorizar atividades

Monitor de runbooks em Orchestrator começa com uma [atividade de monitor](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) e corre continuamente à espera de ser invocado por um evento particular. A Azure Automation não suporta os runbooks monitor, pelo que quaisquer atividades de monitorização no pacote de integração não são convertidas. Em vez disso, é criado um cmdlet de espaço reservado no módulo de integração para a atividade do monitor.  Este cmdlet não tem funcionalidade, mas permite que qualquer livro de bordo convertido que o utilize seja instalado. Este runbook não é capaz de funcionar na Azure Automation, mas pode ser instalado para que possa modificá-lo.

O orquestrador inclui um conjunto de [atividades padrão](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12)) que não estão incluídas num pacote de integração, mas são usadas por muitos runbooks.  O módulo Atividades Standard é um módulo de integração que inclui um equivalente cmdlet para cada uma destas atividades. Deve instalar este módulo de integração na Azure Automation antes de importar quaisquer livros de execução convertidos que utilizem uma atividade padrão.

Além de suportar em runbooks convertidos, os cmdlets no módulo de atividades padrão podem ser usados por alguém familiarizado com o Orquestrador para construir novos runbooks na Azure Automation. Embora a funcionalidade de todas as atividades padrão possa ser realizada com cmdlets, podem funcionar de forma diferente. Os cmdlets do módulo de atividades padrão convertido funcionam da mesma forma que as suas atividades correspondentes e utilizam os mesmos parâmetros. Isto pode ajudá-lo na transição para os runbooks da Azure Automation.

## <a name="convert-orchestrator-runbooks"></a>Converter diários de orquestradores

O Orquestrador Runbook Converter converte os runbooks do Orquestrador em [livros gráficos](automation-runbook-types.md#graphical-runbooks) que podem ser importados para a Azure Automation. O Conversor runbook é implementado como um módulo PowerShell com o cmdlet `ConvertFrom-SCORunbook` que faz a conversão. Quando instala o conversor, cria um atalho para uma sessão PowerShell que carrega o cmdlet.   

Aqui estão os passos básicos para converter um livro de bordo e importá-lo em Azure Automation. Os detalhes da utilização do cmdlet são fornecidos mais tarde nesta secção.

1. Exporte um ou mais livros de corridas do Orquestrador.
2. Obtenha módulos de integração para todas as atividades no livro de recortes.
3. Converta os livros de execução do Orquestrador no ficheiro exportado.
4. Reveja as informações em registos para validar a conversão e para determinar quaisquer tarefas manuais necessárias.
5. Importar livros convertidos em Azure Automation.
6. Crie os ativos necessários na Azure Automation.
7. Edite o livro de recortes na Azure Automation para modificar as atividades necessárias.

A sintaxe `ConvertFrom-SCORunbook` para é:

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath - Caminho para o ficheiro de exportação que contém os livros para converter.
* Módulo - Lista delimitada de módulos de integração contendo atividades nos livros de execução.
* OutputFolder - Caminho para a pasta para criar livros gráficos convertidos.

O seguinte exemplo de comando converte os livros de bordo num ficheiro de exportação chamado **MyRunbooks.ois_export**.  Estes livros utilizam os pacotes de integração do Ative Directory and Data Protection Manager.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="use-runbook-converter-log-files"></a>Utilize ficheiros de registo do Conversor de Runbook

O Conversor runbook cria os seguintes ficheiros de registo no mesmo local que o runbook convertido.  Se os ficheiros já existirem, são substituídos com informações da última conversão.

| Ficheiro | Conteúdos |
|:--- |:--- |
| Conversor de runbook - Progresso.log |Passos pormenorizados da conversão, incluindo informações para cada atividade convertida com sucesso e aviso para cada atividade não convertida. |
| Conversor de runbook - Resumo.log |Resumo da última conversão, incluindo quaisquer advertências e tarefas de seguimento que você precisa realizar, tais como a criação de uma variável necessária para o runbook convertido. |

### <a name="export-runbooks-from-orchestrator"></a>Livros de exportação do Orquestrador

O Runbook Converter trabalha com um ficheiro de exportação do Orquestrador que contém um ou mais livros.  Cria um diário de bordo correspondente da Azure Automation para cada livro de bordo do Orquestrador no ficheiro de exportação.  

Para exportar um runbook do Orchestrator, clique com o direito no nome do runbook em Runbook Designer e selecione **Export.**  Para exportar todos os livros numa pasta, clique com o direito no nome da pasta e selecione **Export**.

### <a name="convert-runbook-activities"></a>Converter atividades de runbook

O Conversor runbook converte cada atividade no runbook do Orquestrador para uma atividade correspondente na Azure Automation.  Para as atividades que não podem ser convertidas, uma atividade de espaço reservado é criada no livro de bordo com texto de aviso.  Depois de importar o livro de bordo convertido para a Azure Automation, deverá substituir qualquer uma destas atividades por atividades válidas que realizem a funcionalidade pretendida.

Todas as atividades do Orquestrador no Módulo de Atividades Padrão são convertidas. Existem algumas atividades orquestradoras padrão que não estão neste módulo e não são convertidas. Por exemplo, `Send Platform Event` não tem equivalente da Azure Automation uma vez que o evento é específico do Orquestrador.

[As atividades de](/previous-versions/system-center/system-center-2012-R2/hh403827(v=sc.12)) monitorização não são convertidas, uma vez que não existem equivalentes a elas na Azure Automation. As exceções são as atividades de monitorização em pacotes de integração convertidos que são convertidos para a atividade do espaço reservado.

Qualquer atividade de um pacote de integração convertido é convertida se fornecer o caminho para o módulo de integração com o `modules` parâmetro. Para pacotes de integração de centros de sistema, você pode usar os módulos de integração do Sistema Center Orchestrator.

### <a name="manage-orchestrator-resources"></a>Gerir os recursos do Orquestrador

O Conversor runbook apenas converte livros de execução, não outros recursos orquestradores, tais como contadores, variáveis ou ligações.  Os contadores não são suportados na Azure Automation.  As variáveis e ligações são suportadas, mas deve criá-las manualmente. Os ficheiros de registo informam-no se o livro de recortes requer esses recursos e especifica os recursos correspondentes que necessita de criar na Azure Automation para que o livro de bordo convertido funcione corretamente.

Por exemplo, um livro pode usar uma variável para preencher um valor particular numa atividade.  O runbook convertido converte a atividade e especifica um ativo variável na Azure Automation com o mesmo nome que a variável Orquestrador. Esta ação é notada no Ficheiro Do Conversor de **Runbook - Resumo.log** que é criado após a conversão. Deve criar manualmente este ativo variável na Azure Automation antes de utilizar o livro de recortes.

### <a name="work-with-orchestrator-input-parameters"></a>Trabalhar com parâmetros de entrada do Orquestrador

Os runbooks no Orquestrador aceitam parâmetros de entrada com a `Initialize Data` atividade.  Se o livro de bordo que está a ser convertido incluir esta atividade, então é criado um parâmetro de [entrada](automation-graphical-authoring-intro.md#handle-runbook-input) no runbook Azure Automation para cada parâmetro da atividade.  Uma atividade [de controlo do Script workflow](automation-graphical-authoring-intro.md#use-activities) é criada no runbook convertido que recupera e devolve cada parâmetro. Quaisquer atividades no livro de recortes que utilizem um parâmetro de entrada referem-se à saída desta atividade.

A razão pela qual esta estratégia é usada é para melhor espelhar a funcionalidade no runbook do Orquestrador.  As atividades em novos livros gráficos devem consultar-se diretamente aos parâmetros de entrada utilizando uma fonte de dados de entrada do Runbook.

### <a name="invoke-runbook-activity"></a>Invocar atividade de Runbook

Os runbooks em Orchestrator iniciam outros livros com a `Invoke Runbook` atividade. Se o livro de execução que está a ser convertido incluir esta atividade e a `Wait for completion` opção for definida, então é criada uma atividade de runbook para ele no runbook convertido.  Se a `Wait for completion` opção não estiver definida, então é criada uma atividade de Script de Fluxo de Trabalho que utiliza [o Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) para iniciar o livro de execução. Depois de importar o livro de bordo convertido para a Azure Automation, deve modificar esta atividade com as informações especificadas na atividade.

## <a name="create-orchestrator-assets"></a>Criar ativos de Orquestrador

O Conversor runbook não converte os ativos do Orquestrador. Deve criar manualmente os ativos orquestradores necessários na Azure Automation.

## <a name="configure-hybrid-runbook-worker"></a>Configure trabalhador de runbook híbrido

O orquestrador armazena os runbooks num servidor de base de dados e executa-os em servidores de runbook, ambos no centro de dados local. Os runbooks em Azure Automation são armazenados na nuvem Azure e podem funcionar no seu datacenter local usando um [Trabalhador De Runbook Híbrido](automation-hybrid-runbook-worker.md). Configure um trabalhador para executar os seus runbooks convertidos do Orquestrador, uma vez que são projetados para funcionar em servidores locais e aceder a recursos locais.

## <a name="related-articles"></a>Artigos relacionados

* Para mais detalhes do Orquestrador, consulte [o System Center 2012 - Orquestrador.](/previous-versions/system-center/system-center-2012-R2/hh237242(v=sc.12))
* Saiba mais sobre automatização da gestão de serviços na Automação de Gestão de [Serviços.](/previous-versions/system-center/system-center-2012-R2/dn469260(v=sc.12))
* Os detalhes das atividades do Orquestrador podem ser encontrados nas [Atividades Standard Do Orquestrador.](/previous-versions/system-center/system-center-2012-R2/hh403832(v=sc.12))
* Para obter o kit de ferramentas de migração do Orquestrador, consulte [download System Orchestrator Migration Toolkit](https://www.microsoft.com/download/details.aspx?id=47323).
* Para uma visão geral do Azure Automation Hybrid Runbook Worker, consulte a [visão geral do Trabalhador do Runbook Híbrido](automation-hybrid-runbook-worker.md).
