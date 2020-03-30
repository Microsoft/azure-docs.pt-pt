---
title: Migração do Orquestrador para a Automação Azure
description: Descreve como migrar livros de corridas e pacotes de integração do System Center Orchestrator para a Automação Azure.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 528b961ca07ec86ad502ee1b589772e354564a3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75421687"
---
# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migração do Orquestrador para a Automação Azure (Beta)
Os livros de execução no [System Center Orchestrator](https://technet.microsoft.com/library/hh237242.aspx) baseiam-se em atividades de pacotes de integração que são escritos especificamente para Orchestrator, enquanto os livros de execução em Azure Automation são baseados no Windows PowerShell.  [Os livros gráficos](automation-runbook-types.md#graphical-runbooks) da Azure Automation têm uma aparência semelhante aos livros de corridas do Orchestrator com as suas atividades representando cmdlets PowerShell, livros infantis e ativos.

O Kit de Ferramentas de [Migração orquestradora](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) do System Center inclui ferramentas para o ajudar na conversão de livros de corridas do Orchestrator para a Automação Azure.  Além de converter os próprios livros de execução, tem de converter os packs de integração com as atividades que os livros de execução utilizam para módulos de integração com cmdlets Windows PowerShell.  

Segue-se o processo básico de conversão de livros de orquestração para a Automação Azure.  Cada um destes passos é descrito em detalhe nas secções abaixo.

1. Descarregue o Kit de Ferramentas de [Migração orquestradora](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) do System Center que contém as ferramentas e módulos discutidos neste artigo.
2. Import Emódulo [de Atividades Padrão](#standard-activities-module) em Automação Azure.  Isto inclui versões convertidas de atividades de Orquestradores padrão que podem ser usadas por livros de corridas convertidos.
3. Módulos de [Integração orquestradora](#system-center-orchestrator-integration-modules) do Centro de Importação em Automação Azure para os pacotes de integração utilizados pelos seus livros de execução que acedem ao System Center.
4. Converta pacotes de integração personalizados e de terceiros utilizando o Conversor de [Pacotes integração](#integration-pack-converter) e importe para a Automação Azure.
5. Converta os livros de orqueiro utilizando o [Conversor de Livro](#runbook-converter) de Execução e instale na Automação Azure.
6. Crie manualmente os ativos orquestradores necessários na Automação Azure, uma vez que o Conversor de Livro de Execução não converte estes recursos.
7. Configure um [Trabalhador de Runbook Híbrido](#hybrid-runbook-worker) no seu centro de dados local para executar livros de corridas convertidos que acederão aos recursos locais.

## <a name="service-management-automation"></a>Service Management Automation
[A Automatização](https://technet.microsoft.com/library/dn469260.aspx) de Gestão de Serviços (SMA) armazena e executa livros de execução no seu centro de dados local como o Orchestrator, e utiliza os mesmos módulos de integração que o Azure Automation. O [Runbook Converter](#runbook-converter) converte livros de orquestração em livros gráficos, embora não sejam suportados em SMA.  Ainda pode instalar os Módulos de Integração de [Orquestradores](#system-center-orchestrator-integration-modules) de Módulos de [Atividades Padrão](#standard-activities-module) e Sistema no SMA, mas tem de reescrever manualmente os seus livros de [execução.](https://technet.microsoft.com/library/dn469262.aspx)

## <a name="hybrid-runbook-worker"></a>Função de Trabalho de Runbook Híbrida
Os livros de execução em Orchestrator são armazenados num servidor de base de dados e funcionam em servidores de livros de execução, ambos no seu centro de dados local.  Os livros de execução em Automatização Azure são armazenados na nuvem Azure e podem ser executados no seu centro de dados local usando um [Trabalhador de Runbook Híbrido](automation-hybrid-runbook-worker.md).  É assim que normalmente executa livros convertidos de Orchestrator, uma vez que são projetados para funcionar em servidores locais.

## <a name="integration-pack-converter"></a>Conversor de pacote de integração
O Conversor de Pacote integração converte pacotes de integração que foram criados usando o Kit de Ferramentas de [Integração Orquestradora (OIT)](https://technet.microsoft.com/library/hh855853.aspx) para módulos de integração baseados no Windows PowerShell que podem ser importados para Automação Azure ou Automação de Gestão de Serviços.  

Quando executa o Conversor de Pacotes de Integração, é-lhe apresentado um assistente que lhe permitirá selecionar um ficheiro de integração (.oip).  O assistente lista então as atividades incluídas nesse pacote de integração e permite-lhe selecionar quais serão migradas.  Quando completa o assistente, cria um módulo de integração que inclui um cmdlet correspondente para cada uma das atividades no pacote de integração original.

### <a name="parameters"></a>Parâmetros
Quaisquer propriedades de uma atividade no pacote de integração são convertidas em parâmetros do cmdlet correspondente no módulo de integração.  Os cmdlets Windows PowerShell têm um conjunto de [parâmetros comuns](https://technet.microsoft.com/library/hh847884.aspx) que podem ser usados com todos os cmdlets.  Por exemplo, o parâmetro -Verbose faz com que um cmdlet produza informações detalhadas sobre o seu funcionamento.  Nenhum cmdlet pode ter um parâmetro com o mesmo nome que um parâmetro comum.  Se uma atividade tiver uma propriedade com o mesmo nome que um parâmetro comum, o assistente irá pedir-lhe que forneça outro nome para o parâmetro.

### <a name="monitor-activities"></a>Monitorizar atividades
Monitorize os livros em Orchestrator comece com uma atividade de [monitor](https://technet.microsoft.com/library/hh403827.aspx) e corra continuamente à espera de ser invocado por um determinado evento.  A Azure Automation não suporta os livros de monitorização, pelo que quaisquer atividades de monitorização no pacote de integração não serão convertidas.  Em vez disso, é criado um cmdlet de espaço reservado no módulo de integração para a atividade do monitor.  Este cmdlet não tem funcionalidade, mas permite que qualquer livro de execução convertido que o utilize seja instalado.  Este livro de execução não será capaz de funcionar na Automatização Azure, mas pode ser instalado para que possa modificá-lo.

### <a name="integration-packs-that-cannot-be-converted"></a>Pacotes de integração que não podem ser convertidos
Os pacotes de integração que não foram criados com o OIT não podem ser convertidos com o Conversor de Pacotes integração. Existem também alguns pacotes de integração fornecidos pela Microsoft que não podem atualmente ser convertidos com esta ferramenta.  Foram fornecidas versões convertidas destes pacotes de integração [para download](#system-center-orchestrator-integration-modules) para que possam ser instaladas na Azure Automation ou Naautomatização de Gestão de Serviços.

## <a name="standard-activities-module"></a>Módulo de Atividades Padrão
O Orchestrator inclui um conjunto de [atividades padrão](https://technet.microsoft.com/library/hh403832.aspx) que não estão incluídas num pacote de integração, mas são usadas por muitos livros de corridas.  O módulo Atividades Standard é um módulo de integração que inclui um equivalente cmdlet para cada uma destas atividades.  Deve instalar este módulo de integração na Automatização Azure antes de importar quaisquer livros de execução convertidos que utilizem uma atividade padrão.

Além de apoiar livros de corridas convertidos, os cmdlets no módulo de atividades padrão podem ser usados por alguém familiarizado com o Orchestrator para construir novos livros de execução na Azure Automation.  Embora a funcionalidade de todas as atividades padrão possa ser realizada com cmdlets, podem funcionar de forma diferente.  Os cmdlets no módulo de atividades padrão convertidos funcionarão da mesma forma que as suas atividades correspondentes e utilizarão os mesmos parâmetros.  Isto pode ajudar o autor do livro de corridas de Orquestradores existente na sua transição para os livros de execução da Automação Azure.

## <a name="system-center-orchestrator-integration-modules"></a>Módulos de Integração orquestradora do Centro de Sistema
A Microsoft fornece pacotes de integração para a [construção](https://technet.microsoft.com/library/hh295851.aspx) de livros de execução para automatizar componentes do System Center e outros produtos.  Alguns destes pacotes de integração baseiam-se atualmente no OIT, mas não podem atualmente ser convertidos em módulos de integração devido a questões conhecidas.  [Os Módulos](https://www.microsoft.com/download/details.aspx?id=49555) de Integração de Orquestradores do System Center incluem versões convertidas destes pacotes de integração que podem ser importados para a Automação azure e automação de gestão de serviços.  

Pela versão RTM desta ferramenta, serão publicadas versões atualizadas dos pacotes de integração baseados no OIT que podem ser convertidos com o Conversor de Pacotes integração.  Serão também fornecidas orientações para o ajudar na conversão de livros de execução utilizando atividades dos pacotes de integração não baseados no OIT.

## <a name="runbook-converter"></a>Conversor de livro de corridas
O Runbook Converter converte livros de corridas de Orquestradores em [livros gráficos](automation-runbook-types.md#graphical-runbooks) que podem ser importados para a Automação Azure.  

O Runbook Converter é implementado como um módulo PowerShell com um cmdlet chamado **ConvertFrom-SCORunbook** que realiza a conversão.  Quando instalar a ferramenta, criará um atalho para uma sessão powerShell que carrega o cmdlet.   

Segue-se o processo básico para converter um livro de orquetas e importá-lo para a Automação Azure.  As seguintes secções fornecem mais detalhes sobre a utilização da ferramenta e trabalhar com livros de execução convertidos.

1. Exportar um ou mais livros de livros do Orquestrador.
2. Obtenha módulos de integração para todas as atividades no livro de execução.
3. Converta os livros de orquetadores no ficheiro exportado.
4. Reveja as informações em diários de bordo para validar a conversão e para determinar quaisquer tarefas manuais necessárias.
5. Importar livros convertidos em Automação Azure.
6. Crie quaisquer ativos necessários na Automação Azure.
7. Editar o livro de execução na Automatização Azure para modificar as atividades necessárias.

### <a name="using-runbook-converter"></a>Usando conversor de livro de corridas
A sintaxe para **ConvertFrom-SCORunbook** é a seguinte:

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

### <a name="log-files"></a>Ficheiros de registo
O Conversor de Livro de Execução criará os seguintes ficheiros de registo no mesmo local que o livro de execução convertido.  Se os ficheiros já existirem, serão substituídos com informações da última conversão.

| Ficheiro | Conteúdos |
|:--- |:--- |
| Conversor de livro de execução - Progress.log |Etapas detalhadas da conversão, incluindo informações para cada atividade convertidas com sucesso e aviso para cada atividade não convertida. |
| Conversor de livro de execução - Resumo.log |Resumo da última conversão, incluindo quaisquer advertências e tarefas de acompanhamento que precisa para realizar, tais como a criação de uma variável necessária para o livro de execução convertido. |

### <a name="exporting-runbooks-from-orchestrator"></a>Livros de exportação do Orchestrator
O Runbook Converter trabalha com um ficheiro de exportação do Orchestrator que contém um ou mais livros de execução.  Criará um livro de execução de Automação Azure correspondente para cada livro de execução do Orchestrator no ficheiro de exportação.  

Para exportar um livro de corridas do Orchestrator, clique no nome do livro de corridas em Runbook Designer e selecione **Export**.  Para exportar todos os livros de execução numa pasta, clique no nome da pasta e selecione **Export .**

### <a name="runbook-activities"></a>Atividades do runbook
O Runbook Converter converte cada atividade no livro de execução do Orquestrador para uma atividade correspondente na Automação Azure.  Para as atividades que não podem ser convertidas, uma atividade de espaço reservado é criada no livro de execução com texto de aviso.  Depois de importar o livro de ensaios convertido para a Automação Azure, deve substituir qualquer uma destas atividades por atividades válidas que realizem a funcionalidade necessária.

Quaisquer atividades de Orquestradores no [Módulo de Atividades Padrão](#standard-activities-module) serão convertidas.  Existem algumas atividades de Orquestrador padrão que não estão neste módulo e não são convertidas.  Por exemplo, **o Send Platform Event** não tem equivalente Azure Automation uma vez que o evento é específico do Orchestrator.

[As atividades](https://technet.microsoft.com/library/hh403827.aspx) de monitor não são convertidas, uma vez que não há equivalente a elas na Automação Azure.  A exceção são as atividades de monitorização em pacotes de [integração convertidos](#integration-pack-converter) que serão convertidos para a atividade do espaço reservado.

Qualquer atividade a partir de um pacote de [integração convertido](#integration-pack-converter) será convertida se fornecer o caminho para o módulo de integração com o parâmetro de **módulos.**  Para pacotes de integração do System Center, pode utilizar os Módulos de [Integração orquestradora](#system-center-orchestrator-integration-modules)do System Center.

### <a name="orchestrator-resources"></a>Recursos orquestradores
O Runbook Converter apenas converte livros de execução, e não outros recursos orquestradores, tais como contadores, variáveis ou ligações.  Os balcões não são suportados na Automação Azure.  As variáveis e ligações são suportadas, mas deve criá-las manualmente.  Os ficheiros de registo informar-lhe-ão se o livro de recortes requer esses recursos e especifica os recursos correspondentes que precisa de criar na Automação Azure para que o livro de execução convertido funcione corretamente.

Por exemplo, um livro de execução pode usar uma variável para povoar um valor particular numa atividade.  O livro de execução convertido converterá a atividade e especificará um ativo variável na Automação Azure com o mesmo nome que a variável Orchestrator.  Isto será notado no ficheiro **Runbook Converter - Resumo.log** criado após a conversão.  Terá de criar manualmente este ativo variável na Automatização Azure antes de utilizar o livro de recortes.

### <a name="input-parameters"></a>Parâmetros de entrada
Os livros de execução em Orchestrator aceitam parâmetros de entrada com a atividade **de Dados inicializar.**  Se o livro de execução que está a ser convertido inclui esta atividade, então é criado um parâmetro de [entrada](automation-graphical-authoring-intro.md#runbook-input-and-output) no livro de execução da Automatização Azure para cada parâmetro da atividade.  Uma atividade de controlo do [Script workflow](automation-graphical-authoring-intro.md#activities) é criada no livro de execução convertido que recupera e devolve cada parâmetro.  Quaisquer atividades no livro de execução que utilizem um parâmetro de entrada referem-se à saída desta atividade.

A razão pela qual esta estratégia é usada é para melhor espelhar a funcionalidade no livro de execução do Orquestrador.  As atividades em novos livros de execução gráficadevem referir-se diretamente aos parâmetros de entrada utilizando uma fonte de dados de entrada do Livro de Execução.

### <a name="invoke-runbook-activity"></a>Invocar atividade do Livro de Corridas
Os livros de execução em Orchestrator iniciam outros livros com a atividade **invoke Book.** Se o livro de execução que está a ser convertido inclui esta atividade e a opção **de conclusão de espera para a conclusão** é definida, então é criada uma atividade de livro de execução para ele no livro de execução convertido.  Se a opção **de conclusão não** estiver definida, então é criada uma atividade do Script workflow que utiliza o **Start-AzureAutomationRunbook** para iniciar o livro de execução.  Depois de importar o livro de ensaios convertido para a Automação Azure, deve modificar esta atividade com as informações especificadas na atividade.

## <a name="related-articles"></a>Artigos relacionados
* [System Center 2012 - Orchestrator](https://technet.microsoft.com/library/hh237242.aspx)
* [Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx)
* [Função de Trabalho de Runbook Híbrida](automation-hybrid-runbook-worker.md)
* [Atividades Padrão orquestradoras](https://technet.microsoft.com/library/hh403832.aspx)
* [Descarregue o conjunto de ferramentas de migração do Centro de Sistemas de Descarregamento](https://www.microsoft.com/en-us/download/details.aspx?id=47323)

