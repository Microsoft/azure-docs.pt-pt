---
title: Visão geral de rastreio e inventário de alterações de automatização azure
description: Este artigo descreve a funcionalidade Change Tracking and Inventory, que o ajuda a identificar alterações de software e de serviço da Microsoft no seu ambiente.
services: automation
ms.subservice: change-inventory-management
ms.date: 01/28/2019
ms.topic: conceptual
ms.openlocfilehash: 7a1c5d5371663f3520e76060c9c2a8df0a18449c
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117534"
---
# <a name="change-tracking-and-inventory-overview"></a>Alterar visão geral do rastreio e do inventário

Este artigo apresenta-o ao Change Tracking and Inventory in Azure Automation. Esta funcionalidade rastreia alterações em máquinas virtuais e infraestrutura de servidores para ajudá-lo a identificar problemas operacionais e ambientais com software gerido pelo Gestor de Pacotes de Distribuição. Os itens que são rastreados por Change Tracking e Inventário incluem: 

- Software windows
- Software Linux (pacotes)
- Arquivos Windows e Linux
- Chaves de registo de janelas
- Serviços da Microsoft
- Daemons linux

> [!NOTE]
> Para acompanhar as mudanças de propriedade do Gestor de Recursos Azure, consulte o histórico de [alterações](../governance/resource-graph/how-to/get-resource-changes.md)do Gráfico de Recursos Azure.

Alterar rastreio e inventário obtém os seus dados do Monitor Azure. As máquinas virtuais ligadas aos espaços de trabalho do Log Analytics utilizam agentes Do Log Analytics para recolher dados sobre alterações a software instalado, serviços microsoft, registo e ficheiros windows e daemons Linux em servidores monitorizados. Quando os dados estão disponíveis, os agentes enviam-nos para o Monitor Azure para processamento. O Monitor Azure aplica lógica aos dados recebidos, regista-os e disponibiliza-os. 

> [!NOTE]
> Para utilizar a funcionalidade De Rastreio e Inventário de Alterações, tem de localizar todos os seus VMs na mesma subscrição e região da conta Automation.

Alterar rastreio e inventário atualmente não suporta os seguintes itens:

* Recursion para rastreio de registo do Windows
* Sistemas de ficheiros de rede
* Diferentes métodos de instalação
* ***.exe** ficheiros para Windows

Outras limitações:

* A coluna max **file size** e os valores não são utilizados na implementação atual.
* Se recolher mais de 2500 ficheiros num ciclo de recolha de 30 minutos, o desempenho de Change Tracking e Inventário poderá ser degradado.
* Quando o tráfego da rede é elevado, os registos de alteração podem demorar até seis horas a ser exibidos.
* Se modificar uma configuração enquanto um computador é desligado, o computador pode publicar alterações pertencentes à configuração anterior.

Change Tracking and Inventory está atualmente a ter os seguintes problemas:

* As atualizações hotfix não são recolhidas nas máquinas Core RS3 do Windows Server 2016.
* Os daemons linux podem mostrar um estado mudado, mesmo que não tenha ocorrido nenhuma mudança. Este problema surge devido à forma como os `SvcRunLevels` dados no registo de [Configuração](https://docs.microsoft.com/azure/azure-monitor/reference/tables/configurationchange) do Monitor Azure são capturados.

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

O Rastreio e Inventário de Alterações é suportado em todos os sistemas operativos que satisfaçam os requisitos do agente Log Analytics. As versões oficiais do sistema operativo são o Windows Server 2008 SP1 ou mais tarde e o Windows 7 SP1 ou mais tarde. A funcionalidade também é suportada em vários sistemas operativos Linux. Para sistemas operativos que suportem o Log Analytics, consulte a [visão geral do agente Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent). 

## <a name="network-requirements"></a>Requisitos de rede

Alterar o Rastreio e o Inventário requer especificamente os endereços de rede listados na tabela seguinte. As comunicações para estes endereços utilizam a porta 443.

|Azure Público  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|*.azure-automation.net | *.azure-automation.us|

## <a name="change-tracking-and-inventory-user-interface"></a>Alterar a interface de utilizador de rastreio e inventário

Utilize o Rastreio e O Inventário de Alterações no portal Azure para ver o resumo das alterações para computadores monitorizados. A funcionalidade está disponível selecionando uma das opções de VMs adicionais para **alterar o rastreio** ou o **inventário** sob gestão de **configuração** na sua conta Deautomação.  

![Alterar painel de rastreio](./media/change-tracking/change-tracking-dash01.png)

As desistências estão disponíveis na parte superior do painel de instrumentos para limitar o gráfico de rastreio de alterações e informações detalhadas com base nos intervalos de tipo e tempo de mudança. Também pode clicar e arrastar na tabela para selecionar um intervalo de tempo personalizado. 

Pode clicar numa alteração ou evento para apresentar os seus detalhes. Os tipos de alterações disponíveis são:

* Eventos
* Daemons
* Ficheiros
* Registo
* Software
* Serviços da Microsoft

Pode adicionar, modificar ou remover cada alteração. O exemplo abaixo mostra uma mudança no tipo de arranque de um serviço de Manual para Auto.

![Alterar detalhes de rastreio e inventário](./media/change-tracking/change-tracking-details.png)

## <a name="fim-support-in-azure-security-center"></a>Suporte FIM no Centro de Segurança Azure

Alterar rastreio e inventário faz uso da Monitorização de Integridade do Ficheiro do Centro de [Segurança Azure (FIM)](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring). Enquanto o FIM monitoriza apenas ficheiros e registos, a funcionalidade completa de Rastreio e Inventário de Alterações também inclui rastreio para:

- Alterações de software
- Serviços da Microsoft
- Daemons linux

> [!NOTE]
> Ativar a funcionalidade completa de rastreio e inventário de alterações pode causar encargos adicionais. Ver [Preços de Automação](https://azure.microsoft.com/pricing/details/automation/). É possível eliminar o FIM da [lista de soluções de monitorização instaladas](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions) disponíveis no portal Azure. Ver [Remover uma solução de monitorização](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).

## <a name="tracking-of-file-changes"></a>Rastreio de alterações de ficheiros

Para rastrear alterações nos ficheiros tanto no Windows como no Linux, o Change Tracking e o Inventory utiliza hashes MD5 dos ficheiros. A funcionalidade utiliza os hashes para detetar se foram feitas alterações desde o último inventário.

## <a name="tracking-of-file-content-changes"></a>Rastreio das alterações de conteúdo de ficheiros

Alterar rastreio e inventário permite-lhe visualizar o conteúdo de um ficheiro Windows ou Linux. Para cada alteração a um ficheiro, alterar o rastreio e o inventário armazena o conteúdo do ficheiro numa [conta de Armazenamento Azure](../storage/common/storage-create-storage-account.md). Quando estiver a seguir um ficheiro, pode ver o seu conteúdo antes ou depois de uma alteração. O conteúdo do ficheiro pode ser visualizado tanto em linha como lado a lado. 

![Ver alterações num ficheiro](./media/change-tracking/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>Rastreio das chaves do registo

Alterar o rastreio e o inventário permite a monitorização das alterações nas teclas de registo do Windows. A monitorização permite identificar pontos de exsensibilidade onde código e malware de terceiros podem ser ativados. As seguintes listas de tabelas pré-configuradas (mas não ativadas) chaves de registo. Para rastrear estas teclas, tem de ativar cada uma delas.

> [!div class="mx-tdBreakAll"]
> |Chave do Registo | Objetivo |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Monitoriza scripts que funcionam no arranque.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Monitoriza os scripts que funcionam no encerramento.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Monitorize as teclas que são carregadas antes de o utilizador entrar na conta Do Windows. A chave é usada para aplicações de 32 bits em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Monitoriza alterações nas definições de aplicação.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitoriza os manipuladores de menus de contexto que se ligam diretamente ao Windows Explorer e que normalmente são executados em processo com **explorer.exe**.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Monitores copiam manipuladores de gancho que se ligam diretamente ao Windows Explorer e geralmente são executados em processo com **explorer.exe**.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitores para registo de manipulador de sobreposição de ícones.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitores para registo de manipulador de sobreposição de ícones para aplicações de 32 bits em execução em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitores para novos plugins de objetos de ajuda de navegador para o Internet Explorer. Utilizado para aceder ao Modelo de Objeto sinuoso (DOM) da página atual e para controlar a navegação.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitores para novos plugins de objetos de ajuda de navegador para o Internet Explorer. Utilizado para aceder ao Modelo de Objeto sinuoso (DOM) da página atual e para controlar a navegação para aplicações de 32 bits em execução em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Monitores para novas extensões do Internet Explorer, tais como menus de ferramentas personalizadas e botões de barra de ferramentas personalizados.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Monitores para novas extensões do Internet Explorer, tais como menus de ferramentas personalizadas e botões de barra de ferramentas personalizados para aplicações de 32 bits em execução em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitores condutores de 32 bits associados a wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc. Semelhante à secção [dos condutores] no ficheiro **system.ini.**
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitores condutores de 32 bits associados a wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc para aplicações de 32 bits em execução em computadores de 64 bits. Semelhante à secção [dos condutores] no ficheiro **system.ini.**
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Monitoriza a lista de DLLs conhecidos ou comumente utilizados do sistema. A monitorização impede as pessoas de explorarem permissões fracas de diretório de aplicações, deixando cair em versões de cavalos de Troia de DLLs do sistema.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Monitoriza a lista de pacotes que podem receber notificações de eventos a partir de **winlogon.exe**, o modelo de suporte de logon interativo para Windows.

## <a name="recursion-support"></a>Apoio à recursão

Alterar rastreio e inventário suporta a recursição, o que lhe permite especificar wildcards para simplificar o rastreio entre diretórios. A recursion também fornece variáveis ambientais que lhe permitem rastrear ficheiros em ambientes com nomes de unidade múltiplos ou dinâmicos. A lista que se segue inclui informações comuns que deve saber ao configurar a recursão:

* Os wildcards são necessários para rastrear vários ficheiros.
* Só pode utilizar wildcards no último segmento de um caminho de ficheiros, por exemplo, ** \\ ficheiro c:\folder*** ou **/etc/*.conf**.
* Se uma variável ambiental tem um caminho inválido, a validação é bem sucedida, mas o caminho falha durante a execução.
* Deve evitar nomes de caminhos gerais ao definir o caminho, uma vez que este tipo de definição pode fazer com que muitas pastas sejam atravessadas.

## <a name="change-tracking-and-inventory-data-collection"></a>Alterar a recolha de dados de rastreio e inventário

O quadro seguinte mostra a frequência de recolha de dados para os tipos de alterações suportadas pelo Change Tracking e Pelo Inventário. Para cada tipo, o instantâneo de dados do estado atual também é atualizado pelo menos a cada 24 horas.

| **Alterar Tipo** | **Frequência** |
| --- | --- |
| Registo de janelas | 50 minutos |
| Arquivo windows | 30 minutos |
| Arquivo Linux | 15 minutos |
| Serviços da Microsoft | 10 segundos a 30 minutos</br> Padrão: 30 minutos |
| Daemons linux | 5 minutos |
| Software windows | 30 minutos |
| Software Linux | 5 minutos |

A tabela seguinte mostra os limites de item rastreados por máquina para rastreio e inventário de alterações.

| **Recurso** | **Limite** |
|---|---|---|
|Ficheiro|500|
|Registo|250|
|Software windows (sem incluir hotfixes) |250|
|Pacotes Linux|1250|
|Serviços|250|
|Daemons|250|

O uso médio de dados do Log Analytics para uma máquina que utilize o Change Tracking and Inventory é de aproximadamente 40 MB por mês, dependendo do seu ambiente. Com a funcionalidade de Utilização e Custos Estimados do espaço de trabalho log Analytics, pode ver os dados ingeridos pelo Change Tracking e Inventário num gráfico de utilização. Utilize esta visão de dados para avaliar a utilização dos seus dados e determinar como isso afeta a sua conta. Ver [Compreender o seu uso e estimar custos.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs) 

### <a name="microsoft-service-data"></a>Dados do serviço da Microsoft

A frequência de recolha padrão dos serviços da Microsoft é de 30 minutos. Pode configurar a frequência utilizando um slider no separador de **serviços** da Microsoft em definições de **edição**.

![Slider de serviços da Microsoft](./media/change-tracking/windowservices.png)

Para otimizar o desempenho, o agente Log Analytics apenas rastreia alterações. A fixação de um limiar elevado pode falhar alterações se o serviço reverter para o seu estado original. A definição da frequência para um valor menor permite-lhe apanhar alterações que podem ser perdidas de outra forma.

> [!NOTE]
> Enquanto o agente pode rastrear as alterações para um intervalo de 10 segundos, os dados ainda demoram alguns minutos a ser exibidos no portal Azure. As alterações que ocorrem durante o tempo de visualização no portal ainda são rastreadas e registadas.

## <a name="support-for-alerts-on-configuration-state"></a>Suporte para alertas sobre estado de configuração

Uma capacidade chave de Change Tracking and Inventory está a alertar sobre alterações ao estado de configuração do seu ambiente híbrido. Muitas ações úteis estão disponíveis para desencadear em resposta a alertas, por exemplo, ações sobre funções Azure, livros de automação, webhooks, e similares. Alertar sobre as alterações ao ficheiro **c:\windows\system32\drivers\etc\hosts** para uma máquina é uma boa aplicação de alertas para dados de Rastreio de Alterações e Inventário. Há muitos mais cenários para alertar também, incluindo os cenários de consulta definidos na tabela seguinte. 

|Consulta  |Descrição  |
|---------|---------|
|ConfiguraçãoChange <br>&#124; onde configChangeType == "Ficheiros" e FileSystemPath contém " c: \\ controladores do sistema de \\ \\ janelas32 \\ "|Útil para rastrear alterações em ficheiros críticos do sistema.|
|ConfiguraçãoChange <br>&#124; onde FieldsChanged contém "FileContentChecksum" e FileSystemPath == "c: \\ windows \\ system32 \\ drivers etc \\ \\ hosts"|Útil para rastrear modificações em ficheiros de configuração chave.|
|ConfiguraçãoChange <br>&#124; onde configChangeType == "Serviços Microsoft" e SvcName contém "w3svc" e SvcState == "Parou"|Útil para rastrear alterações nos serviços críticos do sistema.|
|ConfiguraçãoChange <br>&#124; onde ConfigChangeType == "Daemons" e SvcName contém "ssh" e SvcState!= "Running"|Útil para rastrear alterações nos serviços críticos do sistema.|
|ConfiguraçãoChange <br>&#124; onde configChangeType == "Software" e ChangeCategory == "Adicionado"|Útil para ambientes que precisam de configurações de software bloqueados.|
|ConfiguraçãoData <br>&#124; onde o SoftwareName contém "Monitoring Agent" e CurrentVersion!= "8.0.11081.0"|Útil para ver quais as máquinas que têm versão de software desatualizada ou não conforme instalada. Esta consulta relata o último estado de configuração relatado, mas não reporta alterações.|
|ConfiguraçãoChange <br>&#124; onde registryKey == @"HKEY_LOCAL_MACHINE \\ SOFTWARE \\ Microsoft \\ \\ CurrentVersion \\ QualityCompat"| Útil para rastrear alterações em teclas antivírus cruciais.|
|ConfiguraçãoChange <br>&#124; onde o RegistoKey contém serviços de controlo partilhado de parâmetros de acesso partilhados por parte do sistema de acesso partilhado do sistema de acesso partilhado @"HKEY_LOCAL_MACHINE system \\ \\ CurrentControlSet \\ \\ \\ \\ Services"| Útil para rastrear alterações nas definições de firewall.|

## <a name="next-steps"></a>Próximos passos

* Para ativar a funcionalidade a partir de uma conta Automation, consulte [Enable Change Tracking and Inventory a partir de uma conta De automação](automation-enable-changes-from-auto-acct.md).
* Para ativar a funcionalidade navegando no portal Azure, consulte [Enable Change Tracking and Inventory a partir do portal Azure](automation-onboard-solutions-from-browse.md).
* Para ativar a funcionalidade a partir de um livro de execução, consulte ativar o [rastreio e o inventário de alterações a partir de um livro](automation-enable-changes-from-runbook.md)de execução .
* Para ativar a funcionalidade a partir de um VM Azure, consulte [Enable Change Tracking and Inventory a partir de um Azure VM](automation-enable-changes-from-vm.md).