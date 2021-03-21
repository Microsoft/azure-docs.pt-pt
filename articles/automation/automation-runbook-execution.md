---
title: Execução de runbooks na Automatização do Azure
description: Este artigo fornece uma visão geral do processamento de livros de execução na Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 10/06/2020
ms.topic: conceptual
ms.openlocfilehash: ca28d5829689dca46bbf3a94ce7c1591c20cf7b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586046"
---
# <a name="runbook-execution-in-azure-automation"></a>Execução de runbooks na Automatização do Azure

A automatização de processos na Azure Automation permite-lhe criar e gerir powerShell, PowerShell Workflow e runbooks gráficos. Para mais informações, consulte [os livros de execução da Azure Automation](automation-runbook-types.md). 

A automatização executa os seus livros com base na lógica definida dentro deles. Se um livro de bordo for interrompido, reinicia no início. Este comportamento requer que escreva livros que suportem a ser reiniciados se ocorrerem problemas transitórios.

Iniciar um livro de bordo na Azure Automation cria um trabalho, que é uma única instância de execução do runbook. Cada trabalho acede aos recursos da Azure fazendo uma ligação com a sua subscrição Azure. O trabalho só pode aceder a recursos no seu datacenter se esses recursos estiverem acessíveis a partir da nuvem pública.

A Azure Automation designa um trabalhador para gerir cada trabalho durante a execução do livro de corridas. Enquanto os trabalhadores são partilhados por muitas contas Azure, os empregos de diferentes contas de Automação são isolados uns dos outros. Não pode controlar que trabalhadores prestam os seus pedidos de trabalho.

Ao ver a lista de runbooks no portal Azure, mostra o estado de cada trabalho que foi iniciado para cada livro de aplicação. A Azure Automation armazena registos de trabalho por um período máximo de 30 dias.

O diagrama seguinte mostra o ciclo de vida de um trabalho de runbook para [os livros de execução PowerShell,](automation-runbook-types.md#powershell-runbooks) [os livros de fluxo de trabalho PowerShell](automation-runbook-types.md#powershell-workflow-runbooks)e [os runbooks gráficos](automation-runbook-types.md#graphical-runbooks).

![Estatuto de Trabalho - Fluxo de Trabalho PowerShell](./media/automation-runbook-execution/job-statuses.png)

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="runbook-execution-environment"></a>Ambiente de execução de runbook

Os runbooks na Azure Automation podem funcionar numa caixa de areia Azure ou num [Trabalhador De Runbook Híbrido.](automation-hybrid-runbook-worker.md) 

Quando os runbooks são projetados para autenticar e correr contra recursos em Azure, eles funcionam numa caixa de areia Azure, que é um ambiente compartilhado que vários empregos podem usar. Os postos de trabalho que utilizam a mesma caixa de areia estão vinculados às limitações de recursos da caixa de areia. O ambiente da caixa de areia Azure não suporta operações interativas. Impede o acesso a todos os servidores COM fora do processo. Também requer a utilização de ficheiros MOF locais para runbooks que esjodem chamadas Win32.

Também pode utilizar um [Trabalhador De Runbook Híbrido](automation-hybrid-runbook-worker.md) para executar livros de corrida diretamente no computador que hospeda o papel e contra recursos locais no ambiente. A Azure Automation armazena e gere os runbooks e depois entrega-os a um ou mais computadores atribuídos.

>[!NOTE]
>Para correr num Trabalhador de Runbook Híbrido Linux, os seus scripts devem ser assinados e o trabalhador configurado em conformidade. Em alternativa, [a validação da assinatura deve ser desligada](automation-linux-hrw-install.md#turn-off-signature-validation).

A tabela que se segue lista algumas tarefas de execução do livro com o ambiente de execução recomendado listado para cada um.

|Tarefa|Recomendação|Notas|
|---|---|---|
|Integrar-se com recursos Azure|Caixa de areia Azure|Hospedado em Azure, a autenticação é mais simples. Se estiver a utilizar um Trabalhador de Runbook Híbrido num VM Azure, pode [utilizar a autenticação de runbook com identidades geridas.](automation-hrw-run-runbooks.md#runbook-auth-managed-identities)|
|Obtenha o melhor desempenho para gerir os recursos do Azure|Caixa de areia Azure|O script é executado no mesmo ambiente, que tem menos latência.|
|Minimizar os custos operacionais|Caixa de areia Azure|Não há sobrecarga de cálculo nem necessidade de um VM.|
|Execute script de longa duração|Função de Trabalho de Runbook Híbrida|As caixas de areia Azure têm [limites de recursos.](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Interagir com os serviços locais|Função de Trabalho de Runbook Híbrida|Aceda diretamente à máquina hospedeira, ou recursos em outros ambientes em nuvem ou no ambiente no local. |
|Requerer software de terceiros e executáveis|Função de Trabalho de Runbook Híbrida|Gere o sistema operativo e pode instalar software.|
|Monitorize um ficheiro ou pasta com um livro de bordo|Função de Trabalho de Runbook Híbrida|Utilize uma [tarefa de Observador](automation-watchers-tutorial.md) num Trabalhador De Runbook Híbrido.|
|Executar um script intensivo de recursos|Função de Trabalho de Runbook Híbrida| As caixas de areia Azure têm [limites de recursos.](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)|
|Utilizar módulos com requisitos específicos| Função de Trabalho de Runbook Híbrida|Alguns exemplos incluem:</br> WinSCP - dependência de winscp.exe </br> Administração IIS - dependência de permitir ou gerir o IIS|
|Instale um módulo com um instalador|Função de Trabalho de Runbook Híbrida|Os módulos para caixa de areia devem suportar a cópia.|
|Utilize livros ou módulos que exijam a versão .NET Framework diferente de 4.7.2|Função de Trabalho de Runbook Híbrida|O suporte de caixas de areia Azure .NET Framework 4.7.2 e a atualização para uma versão diferente não é suportada.|
|Executar scripts que requerem elevação|Função de Trabalho de Runbook Híbrida|Caixas de areia não permitem elevação. Com um Trabalhador De Runbook Híbrido, pode desligar o UAC e utilizar [o Invoke-Command](/powershell/module/microsoft.powershell.core/invoke-command) ao executar o comando que requer elevação.|
|Executar scripts que requerem acesso à Instrumentação de Gestão do Windows (WMI)|Função de Trabalho de Runbook Híbrida|Os empregos que correm em caixas de areia na nuvem não podem aceder ao fornecedor de WMI. |

## <a name="temporary-storage-in-a-sandbox"></a>Armazenamento temporário em uma caixa de areia

Se precisar de criar ficheiros temporários como parte da lógica do seu runbook, pode utilizar a pasta Temp (isto `$env:TEMP` é,) na caixa de areia Azure para os livros de execução em Azure. A única limitação é que não pode utilizar mais de 1 GB de espaço em disco, que é a quota para cada caixa de areia. Ao trabalhar com fluxos de trabalho PowerShell, este cenário pode causar um problema porque os fluxos de trabalho powerShell usam pontos de verificação e o script pode ser novamente experimentado em uma caixa de areia diferente.

Com a caixa de areia híbrida, pode utilizar `C:\temp` com base na disponibilidade de armazenamento num Trabalhador De Runbook Híbrido. No entanto, de acordo com as recomendações do Azure VM, não deve utilizar o [disco temporário](../virtual-machines/managed-disks-overview.md#temporary-disk) no Windows ou linux para obter dados que precisem de ser persistidos.

## <a name="resources"></a>Recursos

Os seus runbooks devem incluir lógica para lidar com [recursos](/rest/api/resources/resources), por exemplo, VMs, a rede, e recursos na rede. Os recursos estão ligados a uma subscrição do Azure, e os livros requerem credenciais apropriadas para aceder a qualquer recurso. Para um exemplo de manipulação de recursos num livro de bordo, consulte [os recursos do Handle](manage-runbooks.md#handle-resources).

## <a name="security"></a>Segurança

A Azure Automation utiliza o [Azure Security Center (ASC)](../security-center/security-center-introduction.md) para fornecer segurança aos seus recursos e detetar compromissos nos sistemas Linux. A segurança é fornecida através das suas cargas de trabalho, quer os recursos estejam em Azure ou não. Ver [Introdução à autenticação na Azure Automation.](automation-security-overview.md)

A ASC coloca restrições aos utilizadores que possam executar quaisquer scripts, assinados ou não assinados, num VM. Se for um utilizador com acesso de raiz a um VM, tem de configurar explicitamente a máquina com uma assinatura digital ou desligá-la. Caso contrário, só é possível executar um script para aplicar atualizações do sistema operativo depois de criar uma conta Demôm automação e ativar a funcionalidade adequada.

## <a name="subscriptions"></a>Subscrições

Uma [subscrição](/office365/enterprise/subscriptions-licenses-accounts-and-tenants-for-microsoft-cloud-offerings) do Azure é um acordo com a Microsoft para utilizar um ou mais serviços baseados na nuvem, para os quais é cobrado. Para a Azure Automation, cada subscrição está ligada a uma conta Azure Automation, e pode [criar várias subscrições](manage-runbooks.md#work-with-multiple-subscriptions) na conta.

## <a name="credentials"></a>Credenciais

Um livro de aplicação requer [credenciais](shared-resources/credentials.md) adequadas para aceder a qualquer recurso, seja para sistemas Azure ou de terceiros. Estas credenciais são armazenadas em Azure Automation, Key Vault, etc.  

## <a name="azure-monitor"></a>Azure Monitor

A Azure Automation utiliza o [Azure Monitor](../azure-monitor/overview.md) para monitorizar as suas operações de máquinas. As operações requerem um espaço de trabalho log analytics e um [agente Log Analytics](../azure-monitor/agents/log-analytics-agent.md).

### <a name="log-analytics-agent-for-windows"></a>Log Analytics agente para Windows

O [agente Log Analytics para o Windows](../azure-monitor/agents/agent-windows.md) trabalha com o Azure Monitor para gerir VMs do Windows e computadores físicos. As máquinas podem estar a funcionar em Azure ou num ambiente não-Azure, como um datacenter local.

>[!NOTE]
>O agente Log Analytics para windows era anteriormente conhecido como o Agente de Monitorização da Microsoft (MMA).

### <a name="log-analytics-agent-for-linux"></a>Agente de Log Analytics para Linux

O [agente Log Analytics do Linux](../azure-monitor/agents/agent-linux.md) funciona da mesma forma que o agente para o Windows, mas liga os computadores Linux ao Azure Monitor. O agente é instalado com uma conta de utilizador **de nxautomation** que permite a execução de comandos que requerem permissões de raiz, por exemplo, num Trabalhador de Runbook Híbrido. A **conta de nxautomation** é uma conta do sistema que não requer uma senha.

A conta **de nxautomação** com as permissões de sudo correspondentes deve estar presente durante [a instalação de um trabalhador do Linux Hybrid Runbook](automation-linux-hrw-install.md). Se tentar instalar o trabalhador e a conta não estiver presente ou não tiver as permissões adequadas, a instalação falha.

Não deve alterar as permissões da pasta ou a `sudoers.d` sua propriedade. A permissão de sudo é necessária para a conta **de nxautomation** e as permissões não devem ser removidas. Restringir isto a determinadas pastas ou comandos pode resultar numa mudança de rutura.

Os registos disponíveis para o agente Log Analytics e a conta **de nxautomation** são:

* /var/opt/microsoft/omsagent/log/omsagent.log - Log Analytics agent log
* /var/opt/microsoft/omsagent/run/automationworker/worker.log - Registo de trabalhadores de automação

>[!NOTE]
>O utilizador **de nxautomation** ativado como parte da Atualização Management executa apenas livros de execução assinados.

## <a name="runbook-permissions"></a>Permissões do Runbook

Um livro de aplicação necessita de permissões para autenticação ao Azure, através de credenciais. Consulte [a visão geral da autenticação da Azure Automation](automation-security-overview.md).

## <a name="modules"></a>Módulos

A Azure Automation suporta uma série de módulos predefinidos, incluindo alguns módulos AzureRM (AzureRM.Automation) e um módulo contendo vários cmdlets internos. Também suportados são módulos instalados, incluindo os módulos Az (Az.Automation), atualmente a ser utilizados preferencialmente aos módulos AzureRM. Para mais detalhes sobre os módulos que estão disponíveis para os seus runbooks e configurações DSC, consulte [Gerir módulos em Azure Automation](shared-resources/modules.md).

## <a name="certificates"></a>Certificados

A Azure Automation utiliza [certificados](shared-resources/certificates.md) de autenticação ao Azure ou adiciona-os a recursos Azure ou de terceiros. Os certificados são armazenados de forma segura para acesso por runbooks e configurações DSC.

Os seus livros podem utilizar certificados auto-assinados, que não são assinados por uma autoridade de certificados (CA). Ver [Criar um novo certificado.](shared-resources/certificates.md#create-a-new-certificate)

## <a name="jobs"></a>Tarefas

A Azure Automation suporta um ambiente para gerir postos de trabalho a partir da mesma conta Demômes. Um único livro pode ter muitos empregos a funcionar ao mesmo tempo. Quanto mais trabalhos se gere ao mesmo tempo, mais frequentemente podem ser enviados para a mesma caixa de areia. 

Os empregos que correm no mesmo processo de caixa de areia podem afetar-se mutuamente. Um exemplo é executar o [cmdlet Disconnect-AzAccount.](/powershell/module/az.accounts/disconnect-azaccount) A execução deste cmdlet desliga cada trabalho de runbook no processo de caixa de areia partilhada. Para um exemplo de trabalhar com este cenário, consulte [Prevenir empregos simultâneos.](manage-runbooks.md#prevent-concurrent-jobs)

>[!NOTE]
>Os trabalhos powerShell começaram a partir de um livro de bordo que funciona numa caixa de areia Azure pode não funcionar em todo o [modo de linguagem PowerShell](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="job-statuses"></a>Estatuto de emprego

A tabela seguinte descreve os estatutos que são possíveis para um trabalho. Pode ver um resumo de estado para todos os trabalhos de runbook ou perfurar detalhes de um trabalho específico no portal Azure. Também pode configurar a integração com o seu espaço de trabalho Log Analytics para encaminhar o estado do trabalho do runbook e os fluxos de emprego. Para obter mais informações sobre a integração com os registos do Azure Monitor, consulte [o estado do trabalho a prazo e os fluxos de trabalho da Automação para os registos do Azure Monitor](automation-manage-send-joblogs-log-analytics.md). Consulte também [o estatuto de profissional](manage-runbooks.md#obtain-job-statuses) para um exemplo de trabalhar com status num livro de recortes.

| Estado | Descrição |
|:--- |:--- |
| Ativação |O trabalho está a ser ativado. |
| Concluído |A tarefa foi concluída com êxito. |
| Com falhas |Um livro gráfico ou powerShell Workflow não conseguiu compilar. Um livro de corridas da PowerShell não começou ou o trabalho teve uma exceção. Consulte os [tipos de runbook da Azure Automation](automation-runbook-types.md).|
| Falhado, à espera de recursos |O trabalho falhou porque atingiu o limite [de quota justa](#fair-share) três vezes e começou a partir do mesmo ponto de verificação ou desde o início do livro de cada vez. |
| Em fila |O trabalho está à espera que os recursos de um trabalhador da Automação estejam disponíveis para que possa ser iniciado. |
| A retomar |O sistema está a retomar o trabalho depois de ter sido suspenso. |
| Em Execução |A tarefa está a ser executada. |
| Correr, esperar por recursos |O trabalho foi descarregado porque atingiu o limite de quota-parte justa. Será retomado em breve do seu último posto de controlo. |
| A iniciar |O trabalho foi atribuído a um trabalhador, e o sistema está a iniciá-lo. |
| Parada |A tarefa foi parada pelo utilizador antes de ser concluída. |
| A parar |O sistema está a parar o trabalho. |
| Suspenso |Aplica-se apenas a [livros gráficos e powerShell workflow.](automation-runbook-types.md) A tarefa foi suspensa pelo utilizador, pelo sistema ou por um comando no runbook. Se um livro não tem um posto de controlo, começa desde o início. Se tiver um ponto de verificação, pode recomeçar e retomar a partir do seu último ponto de verificação. O sistema só suspende o livro de recortes quando ocorre uma exceção. Por predefinição, a `ErrorActionPreference` variável é definida para Continuar, indicando que o trabalho continua a funcionar com um erro. Se a variável de preferência estiver definida para Parar, o trabalho suspende um erro.  |
| A suspender |Aplica-se apenas a [livros gráficos e powerShell workflow.](automation-runbook-types.md) O sistema está a tentar suspender o trabalho a pedido do utilizador. O runbook tem de atingir o próximo ponto de verificação antes de poder ser suspenso. Se já passou o seu último posto de controlo, completa-se antes de poder ser suspenso. |

## <a name="activity-logging"></a>Registo de atividades

A execução de runbooks na Azure Automation escreve detalhes num registo de atividade para a conta Automation. Para obter detalhes da utilização do registo, consulte [os detalhes da Recuperação do registo de atividades](manage-runbooks.md#retrieve-details-from-activity-log).

## <a name="exceptions"></a>Exceções

Esta secção descreve algumas formas de lidar com exceções ou problemas intermitentes nos seus livros de execução. Um exemplo é uma exceção WebSocket. O manuseamento correto da exceção evita que falhas de rede transitórias causem falhas nos seus livros de execução.

### <a name="erroractionpreference"></a>ErrorActionPreference

A variável [ErrorActionPreference](/powershell/module/microsoft.powershell.core/about/about_preference_variables#erroractionpreference) determina como o PowerShell responde a um erro não-terminal. Os erros de terminação terminam sempre e não são afetados por `ErrorActionPreference` .

Quando o livro de recortes utiliza `ErrorActionPreference` , um erro normalmente não terminante, como `PathNotFound` o cmdlet [Get-ChildItem,](/powershell/module/microsoft.powershell.management/get-childitem) impede que o livro de recortes se complete. O exemplo que se segue mostra a utilização de `ErrorActionPreference` . O comando final [de Write-Output](/powershell/module/microsoft.powershell.utility/write-output) nunca executa, pois o script para.

```powershell-interactive
$ErrorActionPreference = 'Stop'
Get-ChildItem -path nofile.txt
Write-Output "This message will not show"
```

### <a name="try-catch-finally"></a>Experimente finalmente catch

[Try Catch Finally](/powershell/module/microsoft.powershell.core/about/about_try_catch_finally) é usado em scripts PowerShell para lidar com erros de terminação. O script pode usar este mecanismo para capturar exceções específicas ou exceções gerais. A `catch` declaração deve ser usada para rastrear ou tentar lidar com erros. O exemplo a seguir tenta descarregar um ficheiro que não existe. Apanha a `System.Net.WebException` exceção e devolve o último valor a qualquer outra exceção.

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

### <a name="throw"></a>Jogar

[O lançamento](/powershell/module/microsoft.powershell.core/about/about_throw) pode ser utilizado para gerar um erro de terminação. Este mecanismo pode ser útil ao definir a sua própria lógica num livro de bordo. Se o script cumprir um critério que deve detê-lo, pode usar a `throw` declaração para parar. O exemplo a seguir utiliza esta afirmação para mostrar um parâmetro de função necessário.

```powershell-interactive
function Get-ContosoFiles
{
  param ($path = $(throw "The Path parameter is required."))
  Get-ChildItem -Path $path\*.txt -recurse
}
```

## <a name="errors"></a>Erros

Os seus livros de execução devem lidar com erros. A Azure Automation suporta dois tipos de erros PowerShell, terminando e não terminando. 

Os erros de terminação param a execução do livro de execução quando ocorrem. O livro para com um estatuto de "Falhado".

Os erros de não rescisão permitem que um script continue mesmo depois de ocorrerem. Um exemplo de um erro não-terminal é aquele que ocorre quando um livro de bordo usa o `Get-ChildItem` cmdlet com um caminho que não existe. PowerShell vê que o caminho não existe, lança um erro, e continua para a próxima pasta. O erro neste caso não define o estado de trabalho do runbook para Falhado, e o trabalho pode mesmo ser concluído. Para forçar um livro de execução a parar um erro de não rescisão, pode utilizar `ErrorAction Stop` no cmdlet.

## <a name="calling-processes"></a>Processos de chamada

Os runbooks que funcionam em caixas de areia Azure não suportam processos de chamada, tais como executáveis **(.exe** ficheiros) ou subprocessos. A razão para isso é que uma caixa de areia Azure é um processo compartilhado executado em um recipiente que pode não ser capaz de aceder a todas as APIs subjacentes. Para cenários que exijam software de terceiros ou chamadas para subprocessos, deverá executar um livro de execução num [Trabalhador de Runbook Híbrido](automation-hybrid-runbook-worker.md).

## <a name="device-and-application-characteristics"></a>Características do dispositivo e da aplicação

Os trabalhos de runbook em caixas de areia Azure não podem aceder a nenhum dispositivo ou características de aplicação. A API mais comum usada para consultar métricas de desempenho no Windows é o WMI, com algumas das métricas comuns a serem a memória e o uso do CPU. No entanto, não importa o que a API é usada, uma vez que os empregos que correm na nuvem não conseguem aceder à implementação da Microsoft de Web-Based Enterprise Management (WBEM). Esta plataforma é construída sobre o Modelo Comum de Informação (CIM), fornecendo os padrões da indústria para definir características de dispositivo e aplicação.

## <a name="webhooks"></a>Webhooks

Os serviços externos, por exemplo, os Serviços Azure DevOps e o GitHub, podem iniciar um runbook na Azure Automation. Para fazer este tipo de arranque, o serviço utiliza um [webhook](automation-webhooks.md) através de um único pedido HTTP. A utilização de um webhook permite que os livros de execução sejam iniciados sem a implementação de uma funcionalidade completa da Azure Automation.

## <a name="shared-resources"></a><a name="fair-share"></a>Recursos partilhados

Para partilhar recursos entre todos os runbooks na nuvem, a Azure usa um conceito chamado fair share. Usando uma parte justa, a Azure descarrega temporariamente ou para qualquer trabalho que tenha funcionado por mais de três horas. Os postos de trabalho para [os livros powerShell](automation-runbook-types.md#powershell-runbooks) e [os runbooks python](automation-runbook-types.md#python-runbooks) são interrompidos e não reiniciados, e o estado do trabalho torna-se interrompido.

Para tarefas de automação Azure de longa duração, recomenda-se a utilização de um Trabalhador De Runbook Híbrido. Os Trabalhadores de Runbook Híbridos não são limitados por uma parte justa, e não têm uma limitação de quanto tempo um runbook pode executar. Os [outros limites](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) de emprego aplicam-se tanto às caixas de areia Azure como aos Trabalhadores De Runbook Híbridos. Embora os Trabalhadores de Runbook Híbridos não estejam limitados pelo limite de três horas de quota justa, você deve desenvolver runbooks para executar os trabalhadores que suportam o recomeço de problemas inesperados de infraestruturas locais.

Outra opção é otimizar um runbook utilizando livros infantis. Por exemplo, o seu runbook pode circular pela mesma função em vários recursos, por exemplo, com uma operação de base de dados em várias bases de dados. Pode mover esta função para um livro de [crianças](automation-child-runbooks.md) e pedir ao seu livro de execução que o chame de [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook). Os livros infantis executam em paralelo em processos separados.

A utilização de livros infantis diminui o tempo total para o livro de bordo dos pais completar. O seu runbook pode utilizar o [cmdlet Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) para verificar o estado do trabalho de um livro de aplicação de crianças se ainda tiver mais operações após a conclusão da criança.

## <a name="next-steps"></a>Passos seguintes

* Para começar com um livro de execução PowerShell, consulte [Tutorial: Crie um livro de execução PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
* Para trabalhar com runbooks, consulte [Gerir os runbooks na Azure Automation.](manage-runbooks.md)
* Para mais detalhes sobre o PowerShell, consulte [o PowerShell Docs](/powershell/scripting/overview).
* Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](/powershell/module/az.automation#automation).
