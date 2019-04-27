---
title: Migrar do Orchestrator a automatização do Azure
description: Descreve como migrar pacotes de runbooks e a integração do System Center Orchestrator para automatização do Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ae47cba8f8e9a7cdf914c0b3ea5dfb9fa6c259a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60738218"
---
# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migrar do Orchestrator a automatização do Azure (Beta)
Os Runbooks do [System Center Orchestrator](https://technet.microsoft.com/library/hh237242.aspx) baseiam-se nas atividades dos pacotes de integração que são escritas especificamente para o Orchestrator, enquanto os runbooks na automatização do Azure são baseados no Windows PowerShell.  [Os runbooks gráficos](automation-runbook-types.md#graphical-runbooks) na automatização do Azure têm uma aparência semelhante para os runbooks do Orchestrator com suas atividades que representa os cmdlets do PowerShell, os runbooks subordinados e ativos.

O [Toolkit de migração do System Center Orchestrator](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) inclui ferramentas para ajudá-lo a conversão de runbooks do Orchestrator para automatização do Azure.  Além de converter os runbooks si próprios, tem de converter os pacotes de integração com as atividades que utilizam os runbooks para módulos de integração com os cmdlets do Windows PowerShell.  

Segue-se o processo básico para a conversão de runbooks do Orchestrator para automatização do Azure.  Cada uma dessas etapas é descrita detalhadamente nas secções abaixo.

1. Transfira o [Toolkit de migração do System Center Orchestrator](https://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) que contém as ferramentas e os módulos discutidos neste artigo.
2. Importação [módulo de atividades padrão](#standard-activities-module) para a automatização do Azure.  Isto inclui versões convertidas de atividades padrão do Orchestrator que podem ser utilizadas pelos runbooks convertido.
3. Importação [módulos de integração do System Center Orchestrator](#system-center-orchestrator-integration-modules) para a automatização do Azure para os pacotes de integração utilizados pelos runbooks que acessam o System Center.
4. Converter personalizados e pacotes de integração de terceiros com o [conversor de pacote de integração](#integration-pack-converter) e importar para a automatização do Azure.
5. Converter o Orchestrator runbooks utilizando o [conversor de Runbook](#runbook-converter) e instalar na automatização do Azure.
6. Crie manualmente os recursos de Orchestrator necessários na automatização do Azure, uma vez que o conversor de Runbook não converte estes recursos.
7. Configurar uma [Runbook Worker híbrido](#hybrid-runbook-worker) no seu Datacenter local para executar runbooks convertidos que terão acesso a recursos locais.

## <a name="service-management-automation"></a>Service Management Automation
[Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx) (SMA) armazena e executa runbooks no seu centro de dados locais, como o Orchestrator, e utiliza os mesmo módulos de integração como a automatização do Azure. O [conversor de Runbook](#runbook-converter) converte os runbooks do Orchestrator para os runbooks gráficos mas que não é suportado no SMA.  É possível instalar o [módulo de atividades padrão](#standard-activities-module) e [módulos de integração do System Center Orchestrator](#system-center-orchestrator-integration-modules) para o SMA, mas tem manualmente [reescrever os runbooks](https://technet.microsoft.com/library/dn469262.aspx).

## <a name="hybrid-runbook-worker"></a>Função de Trabalho de Runbook Híbrida
Os Runbooks do Orchestrator são armazenados num servidor de base de dados e executados em servidores de runbook, ambos no seu Datacenter local.  Os Runbooks na automatização do Azure são armazenados na cloud do Azure e podem ser executados no seu centro de dados locais com uma [Runbook Worker híbrido](automation-hybrid-runbook-worker.md).  Isso é como normalmente, irá executar runbooks convertido do Orchestrator, uma vez que elas foram projetadas para serem executadas em servidores locais.

## <a name="integration-pack-converter"></a>Conversor de pacote de integração
O conversor de pacote de integração converte pacotes de integração que foram criadas utilizando o [Orchestrator Integration Toolkit (OIT)](https://technet.microsoft.com/library/hh855853.aspx) aos módulos de integração baseados no Windows PowerShell que podem ser importados para a automatização do Azure ou Service Management Automation.  

Quando executa o conversor de pacote de integração, é apresentada com um assistente que irá permitir-lhe selecionar um ficheiro de pacote (oip) de integração.  O assistente, em seguida, apresenta uma lista das atividades incluídas nesse pacote de integração e permite-lhe selecionar quais serão migrados.  Quando concluir o assistente, ele cria um módulo de integração que inclui um cmdlet correspondente para cada uma das atividades no pacote de integração do original.

### <a name="parameters"></a>Parâmetros
Todas as propriedades de uma atividade no pacote de integração são convertidas em parâmetros do cmdlet correspondente no módulo de integração.  Cmdlets do Windows PowerShell têm um conjunto de [parâmetros comuns de](https://technet.microsoft.com/library/hh847884.aspx) que podem ser utilizados com todos os cmdlets.  Por exemplo, o - parâmetro verboso faz com que um cmdlet para obter informações detalhadas sobre o respetivo funcionamento de saída.  Nenhum cmdlet pode ter um parâmetro com o mesmo nome como um parâmetro comum.  Se uma atividade de ter uma propriedade com o mesmo nome como um parâmetro comum, o assistente solicitará que indique outro nome para o parâmetro.

### <a name="monitor-activities"></a>Monitorizar atividades
Monitorizar os runbooks no início do Orchestrator com um [monitorizar a atividade](https://technet.microsoft.com/library/hh403827.aspx) e executar continuamente a aguardar para ser invocada por um determinado evento.  A automatização do Azure não suporta runbooks do monitor, para que qualquer atividade do monitor no pacote de integração não será convertida.  Em vez disso, um cmdlet de marcador de posição é criado o módulo de integração para a atividade de monitorização.  Este cmdlet não tem nenhuma funcionalidade, mas permite que qualquer runbook convertido que utiliza para ser instalado.  Este runbook não será capaz de executar na automatização do Azure, mas pode ser instalado para que pode modificá-la.

### <a name="integration-packs-that-cannot-be-converted"></a>Pacotes de integração que não não possível converter
Não não possível converter os pacotes de integração que não foram criados com OIT com o conversor de pacote de integração. Também existem alguns pacotes de integração fornecidas pela Microsoft que atualmente não é possível converter com essa ferramenta.  Tem sido convertidas versões destes pacotes de integração [fornecido para download](#system-center-orchestrator-integration-modules) para que possa ser instaladas na automatização do Azure ou o Service Management Automation.

## <a name="standard-activities-module"></a>Módulo de atividades padrão
Orchestrator inclui um conjunto de [atividades padrão](https://technet.microsoft.com/library/hh403832.aspx) que não estão incluídos no pacote de integração, mas são usados por muitos runbooks.  O módulo de atividades padrão é um módulo de integração que inclui um cmdlet equivalente para cada uma destas atividades.  Tem de instalar este módulo de integração na automatização do Azure antes de importar todos os runbooks convertidos que utilizar uma atividade padrão.

Para além de suportar os runbooks convertido, os cmdlets no módulo de atividades padrão pode ser utilizados por alguém familiarizado com o Orchestrator para criar novos runbooks na automatização do Azure.  Enquanto a funcionalidade de todas as atividades padrão pode ser efetuada com os cmdlets, eles podem operar forma diferente.  Os cmdlets no módulo de atividades padrão convertido será funcionam da mesma forma como as respetivas atividades correspondentes e usam os mesmos parâmetros.  Isso pode ajudar o autor de runbook do Orchestrator existente na sua transição para runbooks de automatização do Azure.

## <a name="system-center-orchestrator-integration-modules"></a>Módulos de integração do System Center Orchestrator
A Microsoft fornece [pacotes de integração](https://technet.microsoft.com/library/hh295851.aspx) para a criação de runbooks para automatizar os componentes do System Center e outros produtos.  Alguns destes pacotes de integração baseiam-se atualmente em OIT, mas atualmente não não possível converter para módulos de integração devido a problemas conhecidos.  [Módulos de integração do System Center Orchestrator](https://www.microsoft.com/download/details.aspx?id=49555) inclui versões convertidas destes pacotes de integração que podem ser importados para a automatização do Azure e o Service Management Automation.  

Pela versão RTM dessa ferramenta, serão publicadas versões atualizadas dos pacotes de integração com base em OIT que pode ser convertido com o conversor de pacote de integração.  Também será fornecida orientação para ajudá-lo a conversão de runbooks com atividades dos pacotes de integração não baseados em OIT.

## <a name="runbook-converter"></a>Conversor de Runbook
O conversor de Runbook converte os runbooks do Orchestrator em [runbooks gráficos](automation-runbook-types.md#graphical-runbooks) que podem ser importados para a automatização do Azure.  

Conversor de Runbook é implementado como um módulo do PowerShell com um cmdlet chamado **ConvertFrom-SCORunbook** que realiza a conversão.  Quando instalar a ferramenta, ele criará um atalho para uma sessão do PowerShell que carrega o cmdlet.   

Segue-se o processo básico para converter um runbook do Orchestrator e importe-o para a automatização do Azure.  As secções seguintes fornecem mais detalhes sobre como utilizar a ferramenta e trabalhar com runbooks convertido.

1. Exporte um ou mais runbooks do Orchestrator.
2. Obter módulos de integração para todas as atividades no runbook.
3. Converta os runbooks do Orchestrator no ficheiro exportado.
4. Rever as informações nos registos para validar a conversão e para determinar quaisquer tarefas manuais necessárias.
5. Importe runbooks convertido para automatização do Azure.
6. Crie quaisquer recursos necessários na automatização do Azure.
7. Edite o runbook na automatização do Azure para modificar quaisquer atividades necessárias.

### <a name="using-runbook-converter"></a>Usando o conversor de Runbook
A sintaxe para **ConvertFrom-SCORunbook** é o seguinte:

```powershell
ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string>
```

* RunbookPath - o caminho do ficheiro de exportação que contém os runbooks para converter.
* Módulo - delimitado por vírgula lista de módulos de integração que contêm atividades nos runbooks.
* OutputFolder - o caminho para a pasta para criar convertido runbooks gráficos.

O seguinte comando de exemplo converte os runbooks num arquivo de exportação chamado **MyRunbooks.ois_export**.  Estes runbooks, utilize os pacotes de integração do Active Directory e o Data Protection Manager.

```powershell
ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks"
```

### <a name="log-files"></a>Ficheiros de registo
O conversor de Runbook criará os seguintes ficheiros de registo na mesma localização que o runbook convertido.  Se os ficheiros já existirem, em seguida, estas serão substituídas com as informações de conversão último.

| Ficheiro | Conteúdos |
|:--- |:--- |
| Conversor de Runbook - Progress.log |Passos detalhados da conversão, incluindo informações para cada atividade convertida com êxito e de aviso para cada atividade não convertido. |
| Runbook Converter - Summary.log |Resumo da conversão última inclusive possíveis avisos e acompanhamento de tarefas que precisa para executar como a criação de uma variável necessária para o runbook convertido. |

### <a name="exporting-runbooks-from-orchestrator"></a>Exportar runbooks do Orchestrator
O conversor de Runbook funciona com um ficheiro de exportação do Orchestrator que contém um ou mais runbooks.  Ele criará um runbook de automatização do Azure correspondente para cada runbook do Orchestrator no ficheiro de exportação.  

Para exportar um runbook do Orchestrator, faça duplo clique o nome do runbook no Runbook Designer e selecione **exportar**.  Para exportar todos os runbooks numa pasta, faça duplo clique o nome de pasta e selecione **exportar**.

### <a name="runbook-activities"></a>Atividades do Runbook
O conversor de Runbook converte cada atividade num runbook do Orchestrator para uma atividade correspondente na automatização do Azure.  Para essas atividades que não não possível converter, uma atividade de marcador de posição é criada no runbook com o texto de aviso.  Depois de importar o runbook convertido para automatização do Azure, tem de substituir qualquer uma dessas atividades com atividades válidas que executam a funcionalidade necessária.

Quaisquer atividades do Orchestrator no [módulo de atividades padrão](#standard-activities-module) será convertido.  Existem algumas atividades padrão do Orchestrator que não são neste módulo entanto e não são convertidas.  Por exemplo, **enviar evento de plataforma** não tem nenhum equivalente de automatização do Azure, uma vez que o evento é específico do Orchestrator.

[Monitorizar atividades](https://technet.microsoft.com/library/hh403827.aspx) não são convertidas, uma vez que não existe nenhum equivalente a eles na automatização do Azure.  A exceção são monitor de atividades [convertido pacotes de integração](#integration-pack-converter) que será convertida para a atividade de marcador de posição.

Qualquer atividade a partir de um [convertido o pacote de integração](#integration-pack-converter) será convertido se fornecer o caminho para o módulo de integração com o **módulos** parâmetro.  Para pacotes de integração do System Center, pode utilizar o [módulos de integração do System Center Orchestrator](#system-center-orchestrator-integration-modules).

### <a name="orchestrator-resources"></a>Recursos do Orchestrator
O conversor de Runbook apenas converte runbooks, não outros recursos do Orchestrator, como contadores, variáveis ou ligações.  Contadores não são suportados na automatização do Azure.  São suportadas ligações e variáveis, mas deve criá-los manualmente.  Os ficheiros de registo irão informá-lo se o runbook necessitar desses recursos e especificar os recursos correspondentes que tem de criar na automatização do Azure para o runbook convertido funcionar corretamente.

Por exemplo, um runbook pode utilizar uma variável para preencher um determinado valor numa atividade.  O runbook convertido irá converter a atividade e especifique um recurso de variável na automatização do Azure com o mesmo nome que a variável do Orchestrator.  Isso será indicado no **conversor de Runbook - Summary.log** ficheiro criado após a conversão.  Terá de criar manualmente este recurso de variável da automatização do Azure antes de utilizar o runbook.

### <a name="input-parameters"></a>Parâmetros de entrada
Os Runbooks do Orchestrator aceitar parâmetros de entrada com o **inicializar dados** atividade.  Se o runbook que está a ser convertido incluir esta atividade, em seguida, um [parâmetro de entrada](automation-graphical-authoring-intro.md#runbook-input-and-output) a automatização do Azure runbook é criado para cada parâmetro na atividade.  R [controle de Script de fluxo de trabalho](automation-graphical-authoring-intro.md#activities) atividade é criada no runbook convertido que obtém e retorna cada parâmetro.  Qualquer atividade no runbook que utilize um parâmetro de entrada referem-se à saída desta atividade.

É o motivo que essa estratégia é usada para melhor espelhar a funcionalidade do runbook do Orchestrator.  Atividades em runbooks gráficos da nova devem consultar diretamente para os parâmetros a utilizar uma origem de dados de entrada do Runbook de entrada.

### <a name="invoke-runbook-activity"></a>Atividade invocar Runbook
Os Runbooks do Orchestrator iniciar outros runbooks com o **invocar Runbook** atividade. Se o runbook que está a ser convertido incluir esta atividade e o **aguardar pela conclusão** opção for definida, uma atividade de runbook é criada para o mesmo no runbook convertido.  Se o **aguardar pela conclusão** opção não for definida, uma atividade de Script de fluxo de trabalho é criada que utilize **início AzureAutomationRunbook** para iniciar o runbook.  Depois de importar o runbook convertido para automatização do Azure, tem de modificar esta atividade com as informações especificadas na atividade.

## <a name="related-articles"></a>Artigos relacionados
* [System Center 2012 - Orchestrator](https://technet.microsoft.com/library/hh237242.aspx)
* [Service Management Automation](https://technet.microsoft.com/library/dn469260.aspx)
* [A função de trabalho de Runbook híbrida](automation-hybrid-runbook-worker.md)
* [Atividades padrão do Orchestrator](https://technet.microsoft.com/library/hh403832.aspx)
* [Toolkit de migração do Baixe o System Center Orchestrator](https://www.microsoft.com/en-us/download/details.aspx?id=47323)

