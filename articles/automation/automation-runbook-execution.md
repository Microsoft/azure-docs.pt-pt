---
title: Execução de runbooks na Automatização do Azure
description: Este artigo diz que fornece uma visão geral do processamento de livros de execução na Automação Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: c9e602f9ed6a9132c78dbdf0ea3246cbeae80671
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832355"
---
# <a name="runbook-execution-in-azure-automation"></a>Execução de runbooks na Automatização do Azure

A automatização de processos na Automatização Azure permite-lhe criar e gerir powerShell, PowerShell Workflow e livros gráficos. Para mais detalhes, consulte os livros de [execução da Automação Azure.](automation-runbook-types.md) 

A automatização executa os seus livros com base na lógica definida dentro deles. Se um livro de execução for interrompido, reinicia no início. Este comportamento requer que escreva livros que suportam ser reiniciados se ocorrerem problemas transitórios.

Iniciar um livro de execução na Azure Automation cria um trabalho, que é uma única instância de execução do livro de execução. Cada trabalho acede aos recursos do Azure fazendo uma ligação à sua subscrição Azure. O trabalho só pode aceder a recursos no seu datacenter se esses recursos estiverem acessíveis a partir da nuvem pública.

A Azure Automation atribui a um trabalhador que dirija cada trabalho durante a execução do livro de corridas. Enquanto os trabalhadores são partilhados por muitas contas Azure, os empregos de diferentes contas da Automação estão isolados uns dos outros. Não pode controlar que trabalhadores presta o seu trabalho.

Quando se vê a lista de livros de execução no portal Azure, mostra o estado de cada trabalho iniciado para cada livro de execução. A Azure Automation armazena registos de trabalho por um período máximo de 30 dias.

O diagrama seguinte mostra o ciclo de vida de um trabalho de livro de corridas para os livros de [execução powerShell,](automation-runbook-types.md#powershell-runbooks)livros de fluxo de [trabalho PowerShell](automation-runbook-types.md#powershell-workflow-runbooks)e [livros gráficos](automation-runbook-types.md#graphical-runbooks).

![Estatuto de Emprego - PowerShell Workflow](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="runbook-execution-environment"></a>Ambiente de execução de livro de corridas

Os livros de execução em Azure Automation podem funcionar numa caixa de areia Azure ou num Trabalhador híbrido do livro de [corridas.](automation-hybrid-runbook-worker.md) 

Quando os livros são projetados para autenticar e correr contra recursos em Azure, eles funcionam numa caixa de areia Azure, que é um ambiente partilhado que vários trabalhos podem usar. Os trabalhos que utilizam a mesma caixa de areia estão ligados às limitações de recursos da caixa de areia. O ambiente de caixa de areia Azure não suporta operações interativas. Impede o acesso a todos os servidores COM fora de processo. Também requer o uso de ficheiros MOF locais para livros de execução que fazem chamadas Win32.

Você também pode usar um Trabalhador de [Runbook Híbrido](automation-hybrid-runbook-worker.md) para executar livros de execução diretamente no computador que acolhe o papel e contra os recursos locais no ambiente. A Azure Automation armazena e gere os livros de execução e depois entrega-os a um ou mais computadores atribuídos.

>[!NOTE]
>Para funcionar num Trabalhador de Runbook Híbrido Linux, os seus scripts devem ser assinados e o trabalhador configurado em conformidade. Em alternativa, a [validação](automation-linux-hrw-install.md#turn-off-signature-validation)da assinatura deve ser desligada . 

A tabela seguinte lista algumas tarefas de execução do livro de execução com o ambiente de execução recomendado listado para cada um.

|Tarefa|Recomendação|Notas|
|---|---|---|
|Integrar com recursos Azure|Caixa de areia azure|Hospedada em Azure, a autenticação é mais simples. Se estiver a utilizar um Trabalhador de Livro Híbrido num VM Azure, pode utilizar a autenticação do [livro de corridas com identidades geridas](automation-hrw-run-runbooks.md#runbook-auth-managed-identities).|
|Obtenha um desempenho ideal para gerir os recursos do Azure|Caixa de areia azure|O guião é executado no mesmo ambiente, que tem menos latência.|
|Minimizar os custos operacionais|Caixa de areia azure|Não há despesas de cálculo nem necessidade de um VM.|
|Executar script de longa duração|Função de Trabalho de Runbook Híbrida|As caixas de areia azure têm limites de [recursos.](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Interagir com os serviços locais|Função de Trabalho de Runbook Híbrida|Aceda diretamente à máquina hospedeira, ou recursos em outros ambientes de nuvem ou no ambiente no local. |
|Exigir software e executáveis de terceiros|Função de Trabalho de Runbook Híbrida|Gere o sistema operativo e pode instalar software.|
|Monitorize um ficheiro ou pasta com um livro de execução|Função de Trabalho de Runbook Híbrida|Use uma [tarefa observadora](automation-watchers-tutorial.md) num trabalhador híbrido do livro de corridas.|
|Executar um roteiro intensivo de recursos|Função de Trabalho de Runbook Híbrida| As caixas de areia azure têm limites de [recursos.](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Utilizar módulos com requisitos específicos| Função de Trabalho de Runbook Híbrida|Alguns exemplos incluem:</br> WinSCP - dependência de winscp.exe </br> Administração IIS - dependência de permitir ou gerir o IIS|
|Instale um módulo com um instalador|Função de Trabalho de Runbook Híbrida|Os módulos para caixa de areia devem suportar a cópia.|
|Utilize livros ou módulos que exijam versão .NET Framework diferente de 4.7.2|Função de Trabalho de Runbook Híbrida|As caixas de areia Azure suportam .NET Framework 4.7.2 e a atualização para uma versão diferente não é suportada.|
|Executar scripts que requerem elevação|Função de Trabalho de Runbook Híbrida|As caixas de areia não permitem a elevação. Com um Trabalhador híbrido do livro, pode desligar o UAC e utilizar o [Comando Invocado](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/invoke-command?view=powershell-7) ao executar o comando que requer elevação.|
|Executar scripts que requerem acesso a Instrumentação de Gestão do Windows (WMI)|Função de Trabalho de Runbook Híbrida|Os empregos em caixas de areia na nuvem não podem aceder ao fornecedor de WMI. |

## <a name="resources"></a>Recursos

Os seus livros devem incluir lógica para lidar com [recursos](https://docs.microsoft.com/rest/api/resources/resources), por exemplo, VMs, rede e recursos na rede. Os recursos estão ligados a uma subscrição do Azure, e os livros recorrem que requerem credenciais adequadas para aceder a qualquer recurso. Para um exemplo de manipulação de recursos num livro de rumeiro, consulte [os recursos do Handle](manage-runbooks.md#handle-resources). 

## <a name="security"></a>Segurança

A Azure Automation utiliza o [Azure Security Center (ASC)](../security-center/security-center-intro.md) para garantir a segurança dos seus recursos e detetar compromissos nos sistemas Linux. A segurança é fornecida através das suas cargas de trabalho, quer os recursos estejam ou não em Azure. Ver [Introdução à autenticação na Automação Azure.](automation-security-overview.md)

O ASC coloca constrangimentos aos utilizadores que possam executar quaisquer scripts, assinados ou não assinados, num VM. Se for um utilizador com acesso à raiz a um VM, deve configurar explicitamente a máquina com uma assinatura digital ou desligá-la. Caso contrário, só é possível executar um script para aplicar atualizações do sistema operativo após a criação de uma conta Automation e ativar a funcionalidade adequada.

## <a name="subscriptions"></a>Subscrições

Uma [subscrição](https://docs.microsoft.com/office365/enterprise/subscriptions-licenses-accounts-and-tenants-for-microsoft-cloud-offerings) do Azure é um acordo com a Microsoft para utilizar um ou mais serviços baseados na nuvem, para os quais é cobrado. Para a Automatização Azure, cada subscrição está ligada a uma conta Azure Automation, podendo [criar várias subscrições](manage-runbooks.md#work-with-multiple-subscriptions) na conta.

## <a name="credentials"></a>Credenciais

Um livro de execução requer [credenciais](shared-resources/credentials.md) adequadas para aceder a qualquer recurso, seja para sistemas Azure ou de terceiros. Estas credenciais são armazenadas em Azure Automation, Key Vault, etc.  

## <a name="azure-monitor"></a>Azure Monitor

A Azure Automation utiliza o [Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/overview) para monitorizar as suas operações de máquina. As operações requerem um espaço de trabalho log Analytics e [agentes de Log Analytics.](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)

### <a name="log-analytics-agent-for-windows"></a>Agente de Log Analytics para Windows

O [agente Log Analytics para windows](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows) trabalha com o Monitor Azure para gerir VMs windows e computadores físicos. As máquinas podem estar a funcionar em Azure ou num ambiente não Azure, como um datacenter local. Tem de configurar o agente para reportar a um ou mais espaços de trabalho do Log Analytics. 

>[!NOTE]
>O agente Log Analytics para Windows era anteriormente conhecido como o Microsoft Monitoring Agent (MMA).

### <a name="log-analytics-agent-for-linux"></a>Agente de Log Analytics para Linux

O [agente Log Analytics para o Linux](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux) funciona de forma semelhante ao agente para windows, mas liga os computadores Linux ao Monitor Azure. O agente é instalado com uma conta de utilizador **de nxautomation** que permite a execução de comandos que requerem permissões de raiz, por exemplo, num Trabalhador de Livro Híbrido. A conta **de nxautomation** é uma conta do sistema que não requer uma senha. 

A conta **de nxautomation** com as permissões sudo correspondentes deve estar presente durante a instalação de um trabalhador do [Livro de Execução Híbrido Linux](automation-linux-hrw-install.md). Se tentar instalar o trabalhador e a conta não estiver presente ou não tiver as permissões adequadas, a instalação falha.

Os registos disponíveis para o agente Log Analytics e a conta **de nxautomation** são:

* /var/opt/microsoft/omsagent/log/omsagent.log - Log Analytics agent log 
* /var/opt/microsoft/omsagent/run/automationworker/worker.log - Automação registo de trabalhadores

>[!NOTE]
>O utilizador **de nxautomation** ativado como parte da Atualização executa apenas livros de execução assinados.

## <a name="runbook-permissions"></a>Permissões do Runbook

Um livro de recortes precisa de permissões para autenticação para O Azure, através de credenciais. Ver Gerir a Execução de [Automação Azure Como contas](manage-runas-account.md). 

## <a name="modules"></a>Módulos

A Azure Automation suporta uma série de módulos predefinidos, incluindo alguns módulos AzureRM (AzureRM.Automation) e um módulo contendo vários cmdlets internos. Também são suportados módulos instalados, incluindo os módulos Az (Az.Automation), atualmente a ser utilizados em preferência aos módulos AzureRM. Para mais detalhes sobre os módulos disponíveis para os seus livros de execução e configurações DSC, consulte [Gerir módulos em Automação Azure](shared-resources/modules.md).

## <a name="certificates"></a>Certificados

A Azure Automation utiliza [certificados](shared-resources/certificates.md) para autenticação no Azure ou adiciona-os a recursos Azure ou de terceiros. Os certificados são armazenados de forma segura para acesso por livros de execução e configurações dSC. 

Os seus livros de execução podem utilizar certificados auto-assinados, que não são assinados por uma autoridade de certificados (CA). Ver [Criar um novo certificado.](shared-resources/certificates.md#create-a-new-certificate)

## <a name="jobs"></a>Tarefas

A Azure Automation apoia um ambiente para executar postos de trabalho a partir da mesma conta automation. Um único livro de corridas pode ter muitos empregos a funcionar de uma só vez. Quanto mais empregos se gere ao mesmo tempo, mais frequentemente podem ser enviados para a mesma caixa de areia. 

Os empregos a funcionar no mesmo processo de caixa de areia podem afetar-se mutuamente. Um exemplo é executar o cmdlet [Disconnect-AzAccount.](https://docs.microsoft.com/powershell/module/az.accounts/disconnect-azaccount?view=azps-3.7.0) A execução deste cmdlet desliga cada trabalho de livro de execução no processo de caixa de areia partilhada. Para um exemplo de trabalhar com este cenário, consulte [Prevenir empregos simultâneos](manage-runbooks.md#prevent-concurrent-jobs).

>[!NOTE]
>Os trabalhos da PowerShell começaram a partir de um livro de corridas que funciona numa caixa de areia Azure pode não funcionar em todo o [modo de idioma PowerShell](/powershell/module/microsoft.powershell.core/about/about_language_modes). 

### <a name="job-statuses"></a>Estatuto laboral

A tabela seguinte descreve os estatutos que são possíveis para um trabalho. Pode ver um resumo de estado para todos os trabalhos de livro ou perfurar detalhes de um trabalho específico de livro de corridas no portal Azure. Também pode configurar a integração com o seu espaço de trabalho Log Analytics para reencaminhar o estado do trabalho do livro de corridas e os fluxos de emprego. Para obter mais informações sobre a integração com os registos do Monitor Azure, consulte o [estado de trabalho avançado e os fluxos de trabalho da Automação para os registos do Monitor Azure](automation-manage-send-joblogs-log-analytics.md). Consulte também [a obtenção](manage-runbooks.md#obtain-job-statuses) de estatutos de emprego para um exemplo de trabalhar com estatutos num livro de execução.

| Estado | Descrição |
|:--- |:--- |
| Concluído |A tarefa foi concluída com êxito. |
| Falhou |Um livro de execução de fluxo de trabalho gráfico ou PowerShell não compilou. Um livro de execução da PowerShell não começou ou o trabalho teve uma exceção. Consulte os tipos de [livro de execução da Automação Azure](automation-runbook-types.md).|
| Falhou, à espera de recursos |O trabalho falhou porque atingiu o limite [de ações justas](#fair-share) três vezes e partiu do mesmo posto de controlo ou desde o início do livro de corridas de cada vez. |
| Em fila |O trabalho está à espera que os recursos de um trabalhador da Automação fiquem disponíveis para que possa ser iniciado. |
| A retomar |O sistema está a retomar o trabalho depois de ter sido suspenso. |
| A executar |A tarefa está a ser executada. |
| Correndo, esperando por recursos |O trabalho foi descarregado porque atingiu o limite de ações justas. Será retomado em breve do seu último posto de controlo. |
| A iniciar |O trabalho foi atribuído a um trabalhador, e o sistema está a iniciá-lo. |
| Parada |A tarefa foi parada pelo utilizador antes de ser concluída. |
| A parar |O sistema está a parar o trabalho. |
| Suspenso |Aplica-se apenas aos [livros de execução gráfico e powerShell Workflow.](automation-runbook-types.md) A tarefa foi suspensa pelo utilizador, pelo sistema ou por um comando no runbook. Se um livro não tem um posto de controlo, começa desde o início. Se tiver um posto de controlo, pode recomeçar e retomar a partir do seu último posto de controlo. O sistema só suspende o livro de execução quando ocorre uma exceção. Por predefinição, a `ErrorActionPreference` variável está definida para Continuar, indicando que o trabalho continua a funcionar com um erro. Se a variável de preferência for definida para parar, o trabalho suspende-se por um erro.  |
| A suspender |Aplica-se apenas aos [livros de execução gráfico e powerShell Workflow.](automation-runbook-types.md) O sistema está a tentar suspender o trabalho a pedido do utilizador. O runbook tem de atingir o próximo ponto de verificação antes de poder ser suspenso. Se já passou o seu último posto de controlo, completa antes de poder ser suspenso. |

## <a name="activity-logging"></a>Registo de atividades

A execução de livros de execução na Azure Automation escreve detalhes num registo de atividade para a conta Automation. Para obter detalhes sobre a utilização do registo, consulte [recuperar detalhes do registo de atividade](manage-runbooks.md#retrieve-details-from-activity-log). 

## <a name="exceptions"></a>Exceções

Esta secção descreve algumas formas de lidar com exceções ou problemas intermitentes nos seus livros de execução. Um exemplo é uma exceção WebSocket. O manuseamento correto da exceção impede que falhas de rede transitórias causem a falha dos seus livros de execução. 

### <a name="erroractionpreference"></a>ErrorActionPreference

A variável [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) determina como o PowerShell responde a um erro não terminando. Os erros de terminação terminam sempre e não são afetados por `ErrorActionPreference` .

Quando o livro de execução utiliza `ErrorActionPreference` , um erro normalmente não terminante, como `PathNotFound` o da cmdlet [Get-ChildItem,](https://docs.microsoft.com/powershell/module/microsoft.powershell.management/get-childitem?view=powershell-7) impede que o livro de execução esteja concluído. O exemplo que se segue mostra a utilização de `ErrorActionPreference` . O comando final [de Write-Output](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/write-output?view=powershell-7) nunca executa, à medida que o guião para.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

### <a name="try-catch-finally"></a>Tente apanhar finalmente

[Experimente o Catch Finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) é utilizado em scripts PowerShell para lidar com erros de terminação. O script pode usar este mecanismo para capturar exceções específicas ou exceções gerais. A `catch` declaração deve ser utilizada para rastrear ou tentar lidar com erros. O exemplo que se segue tenta descarregar um ficheiro que não existe. Apanha a `System.Net.WebException` exceção e devolve o último valor para qualquer outra exceção.

```powershell-interactive
try
{
   $wc = new-object System.Net.WebClient
   $wc.DownloadFile("http://www.contoso.com/MyDoc.doc")
}
catch [System.Net.WebException]
{
    "Unable to download MyDoc.doc from http://www.contoso.com."
}
catch
{
    "An error occurred that could not be resolved."
}
```

### <a name="throw"></a>Arremesso

[O lançamento](/powershell/module/microsoft.powershell.core/about/about_throw) pode ser usado para gerar um erro de terminação. Este mecanismo pode ser útil ao definir a sua própria lógica num livro de corridas. Se o guião cumprir um critério que deve detê-lo, pode usar a `throw` declaração para parar. O exemplo seguinte utiliza esta declaração para mostrar um parâmetro de função necessário.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

## <a name="errors"></a>Erros

Os seus livros devem lidar com erros. A Azure Automation suporta dois tipos de erros PowerShell, terminando e não terminando. 

Os erros de terminação impedem a execução do livro quando ocorrem. O livro para com um estatuto de trabalho de Falhado.

Erros não terminadores permitem que um script continue mesmo depois de ocorrerem. Um exemplo de um erro não terminante é aquele que ocorre quando um livro de execução usa o `Get-ChildItem` cmdlet com um caminho que não existe. PowerShell vê que o caminho não existe, lança um erro e continua para a próxima pasta. O erro neste caso não define o estado do trabalho do livro de execução para falhado, e o trabalho pode até estar concluído. Para forçar um livro de ruma para parar num erro não terminante, pode utilizar `ErrorAction Stop` o cmdlet.

## <a name="calling-processes"></a>Processos de chamada

Os livros de execução que funcionam em caixas de areia Azure não suportam processos de chamada, tais como executáveis (ficheiros **.exe)** ou subprocessos. A razão para isso é que uma caixa de areia Azure é um processo partilhado executado num recipiente que pode não ser capaz de aceder a todas as APIs subjacentes. Para cenários que exijam software de terceiros ou chamadas para subprocessos, deve executar um livro de execução num [Trabalhador de Runbook Híbrido](automation-hybrid-runbook-worker.md).

## <a name="device-and-application-characteristics"></a>Características do dispositivo e da aplicação

Os trabalhos de livros de corridas em caixas de areia Azure não podem aceder a nenhum dispositivo ou características de aplicação. A API mais comum usada para consultar métricas de desempenho no Windows é o WMI, com algumas das métricas comuns a serem a memória e o uso do CPU. No entanto, não importa o que a API é usada, uma vez que os empregos que correm na nuvem não podem aceder à implementação da Microsoft de Web-Based Enterprise Management (WBEM). Esta plataforma baseia-se no Modelo de Informação Comum (CIM), fornecendo as normas da indústria para definir as características do dispositivo e da aplicação.

## <a name="webhooks"></a>Webhooks

Os serviços externos, por exemplo, a Azure DevOps Services e a GitHub, podem iniciar um livro de execução na Azure Automation. Para fazer este tipo de arranque, o serviço utiliza um [webhook](automation-webhooks.md) através de um único pedido HTTP. A utilização de um webhook permite que os livros de execução sejam iniciados sem a implementação de uma funcionalidade full Azure Automation. 

## <a name="shared-resources"></a><a name="fair-share"></a>Recursos partilhados

Para partilhar recursos entre todos os livros na nuvem, o Azure usa um conceito chamado fair share. Usando uma parte justa, o Azure descarrega temporariamente ou para qualquer trabalho que tenha funcionado por mais de três horas. Os postos de trabalho para os livros de [execução da PowerShell](automation-runbook-types.md#powershell-runbooks) e [os livros de execução python](automation-runbook-types.md#python-runbooks) são parados e não reiniciados, e o estado de trabalho torna-se parado.

Para tarefas de automação azure de longa duração, é aconselhável utilizar um Trabalhador híbrido do livro de corridas. Os trabalhadores híbridos não são limitados por ações justas, e não têm uma limitação de quanto tempo um livro de corridas pode executar. Os outros [limites](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) de trabalho aplicam-se tanto às caixas de areia Azure como aos Trabalhadores híbridos do livro de corridas. Embora os Trabalhadores híbridos não estejam limitados pelo limite de 3 horas de ações justas, você deve desenvolver livros de corridas para executar sobre os trabalhadores que apoiam reinícios de problemas inesperados de infraestruturas locais.

Outra opção é otimizar um livro de corridas utilizando livros infantis. Por exemplo, o seu livro de execução pode passar pela mesma função em vários recursos, por exemplo, com uma operação de base de dados em várias bases de dados. Pode mover esta função para um livro de [execução para crianças](automation-child-runbooks.md) e pedir ao seu livro de execução que o chame usando [o Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/start-azautomationrunbook?view=azps-3.7.0). Os livros de execução de crianças executam em paralelo em processos separados.

A utilização de livros infantis diminui o tempo total para que o livro de execução dos pais esteja concluído. O seu livro de execução pode utilizar o cmdlet [Get-AzAutomationJob](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationjob?view=azps-3.7.0) para verificar o estado de trabalho de um livro para crianças se ainda tiver mais operações após a conclusão da criança.

## <a name="next-steps"></a>Passos seguintes

* Para começar com um livro de corridas PowerShell, consulte [Tutorial: Create a PowerShell runbook](learn/automation-tutorial-runbook-textual-powershell.md).
* Para trabalhar com livros de execução, consulte [Gerir livros de execução em Automação Azure.](manage-runbooks.md)
* Para mais detalhes sobre powerShell, consulte [PowerShell Docs](https://docs.microsoft.com/powershell/scripting/overview).
* * Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).