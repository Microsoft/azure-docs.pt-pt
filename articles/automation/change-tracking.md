---
title: Rastreio de alteração de alteração de automação Azure e visão geral do inventário
description: Este artigo descreve a funcionalidade De Rastreio e Inventário de Alterações, que o ajuda a identificar o software e as alterações de serviço da Microsoft no seu ambiente.
services: automation
ms.subservice: change-inventory-management
ms.date: 08/17/2020
ms.topic: conceptual
ms.openlocfilehash: 2fe92942e263cf53b9827ccbcb13a2d7bafc367c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88511053"
---
# <a name="change-tracking-and-inventory-overview"></a>Alterar rastreio e visão geral do inventário

Este artigo apresenta-lhe a Alteração tracking and Inventory in Azure Automation. Esta funcionalidade rastreia alterações em máquinas virtuais e infraestruturas de servidores para ajudá-lo a identificar problemas operacionais e ambientais com software gerido pelo Gestor de Pacotes de Distribuição. Os itens que são rastreados por Change Tracking e Inventory incluem: 

- Software Windows
- Software Linux (pacotes)
- Ficheiros Windows e Linux
- Chaves de registo do Windows
- Serviços da Microsoft
- Daemons linux

> [!NOTE]
> Para acompanhar as alterações de propriedade do Azure Resource Manager, consulte o histórico de [alteração](../governance/resource-graph/how-to/get-resource-changes.md)do Gráfico de Recursos Azure .

O Change Tracking and Inventory obtém os seus dados do Azure Monitor. Máquinas virtuais ligadas aos espaços de trabalho do Log Analytics utilizam o agente Log Analytics para recolher dados sobre alterações a software instalado, serviços microsoft, registo do Windows e ficheiros e daemons Linux em servidores monitorizados. Quando os dados estão disponíveis, o agente envia-os para o Azure Monitor para tratamento. O Azure Monitor aplica lógica aos dados recebidos, regista-os e disponibiliza-os.

> [!NOTE]
> Alterar o Tracking and Inventory requer a ligação de um espaço de trabalho log Analytics à sua conta de Automação. Para obter uma lista definitiva de regiões apoiadas, consulte [os mapeamentos do Espaço de Trabalho Azure.](./how-to/region-mappings.md) Os mapeamentos da região não afetam a capacidade de gerir VMs numa região separada da sua conta de Automação.

Alterar rastreio e inventário atualmente não suporta os seguintes itens:

- Recursão para rastreio do registo do Windows
- Sistemas de ficheiros de rede
- Diferentes métodos de instalação
- ***.exe** ficheiros para Windows

Outras limitações:

- A coluna e os valores **do Tamanho do Ficheiro Max** não são bem-utados na implementação atual.
- Se recolher mais de 2500 ficheiros num ciclo de recolha de 30 minutos, o desempenho do Change Tracking e do Inventário poderá ser degradado.
- Quando o tráfego da rede é elevado, os registos de alteração podem demorar até seis horas a ser exibidos.
- Se modificar uma configuração enquanto um computador é desligado, o computador pode registar alterações pertencentes à configuração anterior.

O Change Tracking and Inventory está atualmente a experimentar as seguintes questões:

- As atualizações do Hotfix não são recolhidas nas máquinas Core RS3 do Windows Server 2016.

- Os daemons de Linux podem mostrar um estado alterado, mesmo que não tenha ocorrido qualquer alteração. Este problema surge devido à forma como os `SvcRunLevels` dados no registo Azure Monitor [ConfigurationChange são capturados.](/azure/azure-monitor/reference/tables/configurationchange)

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

O Change Tracking and Inventory é suportado em todos os sistemas operativos que satisfaçam os requisitos do agente Log Analytics. As versões oficiais do sistema operativo são o Windows Server 2008 SP1 ou mais tarde e o Windows 7 SP1 ou mais tarde. A funcionalidade também é suportada em vários sistemas operativos Linux. Para obter uma lista de sistemas operativos suportados, consulte [a visão geral do agente Log Analytics](../azure-monitor/platform/log-analytics-agent.md).

Para compreender os requisitos do cliente para tLS 1.2, consulte [a aplicação TLS 1.2 para a Azure Automation](automation-managing-data.md#tls-12-enforcement-for-azure-automation).

## <a name="network-requirements"></a>Requisitos de rede

Alterar o Tracking e o Inventário requer especificamente os endereços de rede listados na tabela seguinte. As comunicações a estes endereços utilizam a porta 443.

|Azure Público  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com    | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|
|*.azure-automation.net | *.azure-automation.us|

## <a name="change-tracking-and-inventory-user-interface"></a>Alterar interface de utilizador de rastreio e inventário

Utilize o Change Tracking and Inventory no portal Azure para ver o resumo das alterações para computadores monitorizados. A funcionalidade está disponível selecionando uma das opções de VMs adicionais para **alterar o rastreio** ou o **inventário** em **Gestão de Configuração** na sua conta de Automação.  

![Alterar painel de rastreio](./media/change-tracking/change-tracking-dash01.png)

As dropdowns estão disponíveis no topo do painel de instrumentos para limitar o gráfico de rastreio de alterações e informações detalhadas com base no tipo de alteração e intervalos de tempo. Também pode clicar e arrastar na tabela para selecionar um intervalo de tempo personalizado. 

Pode clicar numa alteração ou evento para apresentar os seus detalhes. Os tipos de alteração disponíveis são:

- Eventos
- Daemons
- Ficheiros
- Registo
- Software
- Serviços da Microsoft

Pode adicionar, modificar ou remover cada alteração. O exemplo abaixo mostra uma alteração no tipo de arranque de um serviço de Manual para Automóvel.

![Alterar detalhes de rastreio e inventário](./media/change-tracking/change-tracking-details.png)

## <a name="fim-support-in-azure-security-center"></a>Suporte FIM no Centro de Segurança Azure

O Rastreio e Inventário de Alteração faz uso da Monitorização da Integridade do Ficheiro do Centro de [Segurança Azure (FIM)](../security-center/security-center-file-integrity-monitoring.md). Enquanto o FIM monitoriza apenas ficheiros e registos, a funcionalidade completa de Rastreio e Inventário de Alterações também inclui rastreio para:

- Alterações de software
- Serviços da Microsoft
- Daemons linux

> [!NOTE]
> Ativar a funcionalidade completa de Rastreio e Inventário de Alterações pode causar custos adicionais. Ver [Preços de Automação](https://azure.microsoft.com/pricing/details/automation/). É possível eliminar o FIM da [lista de soluções de monitorização instaladas](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions) disponíveis no portal Azure. Ver [Remover uma solução de monitorização](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).

## <a name="tracking-of-file-changes"></a>Rastreio de alterações de ficheiros

Para rastrear alterações em ficheiros tanto no Windows como no Linux, o Change Tracking e o Inventário utilizam hashes MD5 dos ficheiros. A funcionalidade utiliza os hashes para detetar se foram feitas alterações desde o último inventário.

## <a name="tracking-of-file-content-changes"></a>Rastreio das alterações do conteúdo de ficheiros

Alterar o Tracking e o Inventário permite-lhe visualizar o conteúdo de um ficheiro Windows ou Linux. Para cada alteração a um ficheiro, o Change Tracking and Inventory armazena o conteúdo do ficheiro numa [conta de Armazenamento Azure](../storage/common/storage-account-create.md). Quando está a seguir um ficheiro, pode ver o seu conteúdo antes ou depois de uma alteração. O conteúdo do ficheiro pode ser visto dentro ou lado a lado. 

![Ver alterações num ficheiro](./media/change-tracking/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>Rastreio de chaves de registo

O Change Tracking and Inventory permite monitorizar as alterações nas chaves do registo do Windows. A monitorização permite identificar pontos de extensibilidade onde o código e o malware de terceiros podem ser ativados. As seguintes tabelas listam chaves de registo pré-configuradas (mas não ativadas). Para rastrear estas chaves, tem de ativar cada uma delas.

> [!div class="mx-tdBreakAll"]
> |Chave do Registo | Objetivo |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Monitoriza scripts que funcionam no arranque.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Monitoriza os scripts que funcionam no encerramento.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Monitoriza as teclas que são carregadas antes do utilizador entrar na conta do Windows. A chave é usada para aplicações de 32 bits em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Monitorize alterações nas definições de aplicações.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitoriza os manipuladores de menus de contexto que se ligam diretamente ao Windows Explorer e normalmente executam em processo com **explorer.exe**.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Monitora manipuladores de ganchos de cópia que se ligam diretamente ao Windows Explorer e normalmente executam em processo com **explorer.exe**.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitores para o registo do manipulador de sobreposição de ícones.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitores para o registo do manipulador de sobreposição de ícones para aplicações de 32 bits em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitores para novos plugins de objetos de ajuda ao navegador para o Internet Explorer. Utilizado para aceder ao Modelo de Objeto de Documento (DOM) da página atual e para controlar a navegação.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitores para novos plugins de objetos de ajuda ao navegador para o Internet Explorer. Utilizado para aceder ao Modelo de Objeto de Documento (DOM) da página atual e para controlar a navegação para aplicações de 32 bits em execução em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Monitores para novas extensões do Internet Explorer, tais como menus de ferramentas personalizados e botões personalizados da barra de ferramentas.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Monitores para novas extensões do Internet Explorer, tais como menus de ferramentas personalizados e botões de barra de ferramentas personalizados para aplicações de 32 bits em execução em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitores condutores de 32 bits associados com amapper, onda1 e onda2, msacm.imaadpcm, .msadpcm, .msgsm610, e vidc. Semelhante à secção [de motoristas] no ficheiro **system.ini.**
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitores condutores de 32 bits associados com massa de onda, onda1 e onda2, msacm.imaadpcm, .msadpcm, .msgsm610, e vidc para aplicações de 32 bits em computadores de 64 bits. Semelhante à secção [de motoristas] no ficheiro **system.ini.**
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Monitoriza a lista de DLLs de sistema conhecidos ou comumente utilizados. A monitorização impede que as pessoas explorem permissões fracas de diretório de aplicações, largando nas versões de cavalos de Troia dos DLLs do sistema.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Monitoriza a lista de pacotes que podem receber notificações de eventos ** dewinlogon.exe, **o modelo de suporte de início de são interativo para o Windows.

## <a name="recursion-support"></a>Suporte à recursão

O Change Tracking and Inventory suporta a recursão, que permite especificar wildcards para simplificar o rastreio em todos os diretórios. A recursão também fornece variáveis ambientais que permitem rastrear ficheiros em ambientes com nomes de unidades múltiplos ou dinâmicos. A lista que se segue inclui informações comuns que deve saber ao configurar a recursão:

- Wildcards são necessários para rastrear vários ficheiros.

- Só pode utilizar wildcards no último segmento de um percurso de ficheiro, por exemplo, ** \\ c:\ficheiro de pasta*** ou **/etc//**.conf**.

- Se uma variável ambiental tem um caminho inválido, a validação tem sucesso, mas o caminho falha durante a execução.

- Deve evitar nomes gerais do caminho ao definir o caminho, uma vez que este tipo de regulação pode fazer com que demasiadas pastas sejam atravessadas.

## <a name="change-tracking-and-inventory-data-collection"></a>Alterar recolha de dados de rastreio e inventário

A tabela seguinte mostra a frequência de recolha de dados para os tipos de alterações suportadas por Change Tracking e Inventory. Para cada tipo, a imagem instantânea do estado atual também é atualizada pelo menos a cada 24 horas.

| **Alterar Tipo** | **Frequência** |
| --- | --- |
| Registo de janelas | 50 minutos |
| Ficheiro Windows | 30 minutos |
| Arquivo Linux | 15 minutos |
| Serviços da Microsoft | 10 segundos a 30 minutos</br> Predefinição: 30 minutos |
| Daemons linux | 5 minutos |
| Software Windows | 30 minutos |
| Software Linux | 5 minutos |

A tabela a seguir mostra os limites de produto rastreado por máquina para Change Tracking and Inventory.

| **Recurso** | **Limite** |
|---|---|---|
|Ficheiro|500|
|Registo|250|
|Software Windows (sem incluir hotfixes) |250|
|Pacotes Linux|1250|
|Serviços|250|
|Daemons|250|

O uso médio de dados do Log Analytics para uma máquina que utiliza o Change Tracking and Inventory é de aproximadamente 40 MB por mês, dependendo do seu ambiente. Com a funcionalidade de Utilização e Custos Estimados do espaço de trabalho Log Analytics, pode ver os dados ingeridos por Change Tracking e Inventory num gráfico de utilização. Utilize esta visão de dados para avaliar o uso dos seus dados e determinar como isso afeta a sua conta. Consulte [Compreender os seus custos de utilização e estimativa](../azure-monitor/platform/manage-cost-storage.md#understand-your-usage-and-estimate-costs).

### <a name="microsoft-service-data"></a>Dados do serviço da Microsoft

A frequência de recolha padrão dos serviços da Microsoft é de 30 minutos. Pode configurar a frequência utilizando um slider no separador **de serviços** da Microsoft em **Definições de Edição**.

![Slider de serviços da Microsoft](./media/change-tracking/windowservices.png)

Para otimizar o desempenho, o agente Log Analytics apenas rastreia as alterações. A fixação de um limiar elevado pode falhar alterações se o serviço reverter para o seu estado original. Definir a frequência para um valor menor permite-lhe apanhar alterações que podem não ser perdidas de outra forma.

> [!NOTE]
> Embora o agente possa rastrear as alterações até um intervalo de 10 segundos, os dados ainda demoram alguns minutos a ser exibidos no portal Azure. As alterações que ocorrem durante o tempo de visualização no portal ainda são rastreadas e registadas.

## <a name="support-for-alerts-on-configuration-state"></a>Suporte para alertas no estado de configuração

Uma capacidade chave do Change Tracking and Inventory está a alertar para as alterações ao estado de configuração do seu ambiente híbrido. Muitas ações úteis estão disponíveis para desencadear em resposta a alertas, por exemplo, ações em funções Azure, runbooks de automação, webhooks, e similares. Alertar sobre as alterações ao ficheiro **c:\windows\system32\drivers\etc\hosts** para uma máquina é uma boa aplicação de alertas para os dados de Change Tracking e Inventory. Há muitos mais cenários para alertar também, incluindo os cenários de consulta definidos na tabela seguinte.

|Consulta  |Descrição  |
|---------|---------|
|ConfiguraçãoChange <br>&#124; onde ConfigChangeType == "Ficheiros" e FileSystemPath contém " c: \\ sistema de \\ janelas32 \\ controladores \\ "|Útil para rastrear alterações em ficheiros críticos do sistema.|
|ConfiguraçãoChange <br>&#124; onde FieldsChanged contém "FileContentChecksum" e FileSystemPath == "c: \\ \\ windows system32 \\ drivers etc \\ \\ hosts"|Útil para rastrear modificações em ficheiros de configuração de chaves.|
|ConfiguraçãoChange <br>&#124; onde ConfigChangeType == "WindowsServices" e SvcName contém "w3svc" e SvcState == "Parado"|Útil para rastrear alterações em serviços críticos do sistema.|
|ConfiguraçãoChange <br>&#124; onde ConfigChangeType == "Daemons" e SvcName contém "ssh" e SvcState!= "Running"|Útil para rastrear alterações em serviços críticos do sistema.|
|ConfiguraçãoChange <br>&#124; onde ConfigChangeType == "Software" e ChangeCategory == "Adicionado"|Útil para ambientes que precisam de configurações de software bloqueadas.|
|Data de Configuração <br>&#124; onde o SoftwareName contém "Monitoring Agent" e CurrentVersion!= "8.0.11081.0"|Útil para ver quais máquinas têm versão de software desatualizada ou não conforme instalada. Esta consulta reporta o último estado de configuração relatado, mas não reporta alterações.|
|ConfiguraçãoChange <br>&#124; onde o RegistryKey == @"HKEY_LOCAL_MACHINE \\ SOFTWARE Microsoft Windows \\ \\ \\ CurrentVersion \\ QualityCompat"| Útil para rastrear alterações em chaves antivírus cruciais.|
|ConfiguraçãoChange <br>&#124; onde o RegistryKey contém @"HKEY_LOCAL_MACHINE \\ SYSTEM \\ CurrentControlSet \\ Services \\ SharedAccess \\ Parameters \\ FirewallPolicy"| Útil para rastrear alterações nas definições de firewall.|

## <a name="next-steps"></a>Passos seguintes

- Para ativar a funcionalidade a partir de uma conta de Automação, consulte [Ativar o Tracking e o Inventário de Mudança a partir de uma conta de Automação.](automation-enable-changes-from-auto-acct.md)

- Para ativar a funcionalidade navegando no portal Azure, consulte [Ativar o Tracking e o Inventário de Alterações a partir do portal Azure](automation-enable-changes-from-browse.md).

- Para ativar a funcionalidade a partir de um livro de recortes, consulte [Ativar o Tracking e o Inventário de Alterações a partir de um livro de recortes.](automation-enable-changes-from-runbook.md)

- Para ativar a funcionalidade a partir de um VM Azure, consulte [Ativar o Tracking e o Inventário de Alterações a partir de um VM Azure](automation-enable-changes-from-vm.md).
