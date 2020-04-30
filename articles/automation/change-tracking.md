---
title: Mudanças na pista com a Automação Azure
description: A solução Change Tracking ajuda-o a identificar as alterações de software e do Windows Service que ocorrem no seu ambiente.
services: automation
ms.subservice: change-inventory-management
ms.date: 01/28/2019
ms.topic: conceptual
ms.openlocfilehash: d84566c7680081561f60d4825f25a9ce19e02b24
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81682979"
---
# <a name="track-environment-changes-with-change-tracking"></a>Acompanhar alterações ambientais com rastreio de mudanças

Este artigo ajuda-o a usar a solução Change Tracking para identificar facilmente alterações no seu ambiente. A solução rastreia as seguintes alterações de configuração para ajudá-lo a identificar problemas operacionais:

- Software windows
- Software Linux (pacotes)
    >[!NOTE]
    >Change Tracking apenas rastreia o software que é gerido com o gestor de pacotes de distribuição.

- Arquivos Windows e Linux
- Chaves de registo de janelas
- Serviços Windows
- Daemons linux

Depois de ativada a solução, pode visualizar o resumo das alterações para os computadores monitorizados selecionando o **Change Tracking** sob a Gestão de **Configuração** na sua conta Deautomação.

> [!NOTE]
> O rastreio de mudanças de automatização azure rastreia as mudanças nas máquinas virtuais. Para acompanhar as mudanças de propriedade do Gestor de Recursos Azure, consulte o histórico de [mudança](../governance/resource-graph/how-to/get-resource-changes.md)do Azure Resource Graph.

Pode visualizar alterações nos seus computadores e, em seguida, perfurar detalhes para cada evento. As quedas estão disponíveis no topo da tabela para limitar o gráfico e informações detalhadas com base nos intervalos de tipo e tempo de alteração. Também pode clicar e arrastar na tabela para selecionar um intervalo de tempo personalizado. **O Tipo de Alteração** será um dos seguintes valores **Eventos**, **Daemons,** **Ficheiros,** **Registo,** **Software,** **Serviços Windows**. A categoria mostra-lhe o tipo de alteração e pode ser **adicionada,** **modificada**ou **removida**.

![imagem do painel de rastreio de mudança](./media/change-tracking/change-tracking-dash01.png)

Clicar numa mudança ou evento traz a informação detalhada sobre essa alteração. Como pode ver pelo exemplo, o tipo de arranque do serviço foi alterado de Manual para Auto.

![imagem de detalhes de rastreamento de mudança](./media/change-tracking/change-tracking-details.png)

As alterações ao software instalado, serviços Windows, registo e ficheiros windows e daemons Linux nos servidores monitorizados são enviados para o serviço Azure Monitor na nuvem para processamento. A lógica é aplicada aos dados recebidos e o serviço cloud regista os dados. Ao utilizar as informações no painel de rastreio de alterações, pode facilmente ver as alterações que foram feitas na infraestrutura do servidor.

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

### <a name="windows-operating-systems"></a>Sistemas operativos Windows

As seguintes versões do sistema operativo Windows são oficialmente suportadas para o agente Windows:

* Windows Server 2008 R2 ou posterior

### <a name="linux-operating-systems"></a>Sistemas operativos Linux

As seguintes distribuições linux são oficialmente apoiadas. No entanto, o agente Linux também pode funcionar com outras distribuições não listadas. Salvo indicação em contrário, todas as versões menores são suportadas para cada versão principal listada.

#### <a name="64-bit"></a>64 bits

* CentOS 6 e 7
* Amazon Linux 2017.09
* Oracle Linux 6 e 7
* Red Hat Enterprise Linux Server 6 e 7
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS e 18.04 LTS
* SUSE Linux Enterprise Server 12

#### <a name="32-bit"></a>32 bits

* Centos 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14.04 LTS e 16.04 LTS

## <a name="limitations"></a>Limitações

A solução Change Tracking não suporta atualmente os seguintes itens:

* Recursion para rastreio de registo do Windows
* Sistemas de ficheiros de rede
* Diferentes métodos de instalação
* ***.exe** ficheiros para Windows

Outras limitações:

* A coluna max **file size** e os valores não são utilizados na implementação atual.
* Se recolher mais de 2500 ficheiros num ciclo de recolha de 30 minutos, o desempenho da solução poderá ser degradado.
* Quando o tráfego da rede é elevado, os registos de alteração podem demorar até seis horas a ser exibidos.
* Se modificar a configuração enquanto um computador é desligado, o computador pode publicar alterações pertencentes à configuração anterior.

## <a name="known-issues"></a>Problemas conhecidos

A solução Change Tracking está atualmente a ter os seguintes problemas:

* As atualizações hotfix não são recolhidas nas máquinas Core RS3 do Windows Server 2016.
* Linux Daemons pode mostrar um estado mudado, mesmo que não tenha havido mudanças. Isto deve-se `SvcRunLevels` à forma como o campo é capturado.

## <a name="network-requirements"></a>Requisitos da rede

Alterar o Rastreio requer especificamente os seguintes endereços. As comunicações para estes endereços utilizam a porta 443.

|Azure Público  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

## <a name="wildcard-recursion-and-environment-settings"></a>Wildcard, recursição e ambiente

A recursição permite especificar wildcards para simplificar o rastreio entre diretórios e variáveis ambientais para permitir rastrear ficheiros em ambientes com nomes de unidade múltiplos ou dinâmicos. A lista que se segue mostra informações comuns que deve saber ao configurar a recursão:

* Os wildcards são necessários para rastrear vários ficheiros.
* Wildcards apenas ser usado no último segmento de um caminho, por exemplo, c:\ficheiro de pasta*\\ou /etc/*.conf.
* Se uma variável ambiental tem um caminho inválido, a validação sucede, mas esse caminho falha quando o Inventário corre.
* Evite caminhos gerais ao definir o caminho, uma vez que este tipo de regulação pode fazer com que muitas pastas sejam atravessadas.

## <a name="change-tracking-data-collection-details"></a>Alterar detalhes de recolha de dados de rastreio

O quadro seguinte mostra a frequência de recolha de dados para os tipos de alterações. Para cada tipo, o instantâneo de dados do estado atual também é atualizado pelo menos a cada 24 horas:

| **Alterar tipo** | **Frequência** |
| --- | --- |
| Registo de janelas | 50 minutos |
| Arquivo windows | 30 minutos |
| Arquivo Linux | 15 minutos |
| Serviços Windows | 10 segundos a 30 minutos</br> Padrão: 30 minutos |
| Daemons linux | 5 minutos |
| Software windows | 30 minutos |
| Software Linux | 5 minutos |

A tabela seguinte mostra os limites de item rastreados por máquina para o Rastreio de Alterações.

| **Recurso** | **Limite**| **Notas** |
|---|---|---|
|Ficheiro|500||
|Registo|250||
|Software windows|250|Não inclui hotfixes de software|
|Pacotes Linux|1250||
|Serviços|250||
|Daemon|250||

O uso médio de dados do Log Analytics para uma máquina que utilize o Change Tracking é de aproximadamente 40MB por mês. Este valor é apenas uma aproximação e está sujeito a alterações com base no seu ambiente. Recomenda-se que monitorize o seu ambiente para ver o uso exato que tem.

### <a name="windows-service-tracking"></a>Rastreio do serviço windows

A frequência de recolha padrão dos serviços Windows é de 30 minutos. Para configurar a frequência, vá ao **Change Tracking**. Em **Definições de Edição** no separador **Serviços Windows,** existe um slider que lhe permite alterar a frequência de recolha dos serviços Windows de 10 segundos para 30 minutos. Mova a barra de deslize para a frequência que deseja e salva-a automaticamente.

![Deslizador de serviços windows](./media/change-tracking/windowservices.png)

O agente só rastreia as alterações, isto otimiza o desempenho do agente. A fixação de um limiar elevado pode falhar alterações se o serviço reverter para o seu estado original. A definição da frequência para um valor menor permite-lhe apanhar alterações que podem ser perdidas de outra forma.

> [!NOTE]
> Enquanto o agente pode rastrear as alterações para um intervalo de 10 segundos, os dados ainda demoram alguns minutos a serem exibidos no portal. As alterações durante o tempo de visualização no portal ainda são rastreadas e registadas.

### <a name="registry-key-change-tracking"></a>Rastreio de mudança de chave de registo

O objetivo de monitorizar as alterações às teclas de registo é identificar pontos de extebilidade onde o código e malware de terceiros podem ser ativados. A lista que se segue mostra a lista de chaves de registo pré-configuradas. Estas teclas estão configuradas, mas não ativadas. Para rastrear estas chaves de registo, tem de ativar cada uma delas.

> [!div class="mx-tdBreakAll"]
> |Chave do Registo | Objetivo |
> |---------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitoriza entradas automáticas comuns que se ligam diretamente ao Windows Explorer e geralmente são executadas em processo com explorer.exe.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Monitoriza scripts que funcionam no arranque.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Monitoriza os scripts que funcionam no encerramento.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Monitorize as teclas que são carregadas antes de o utilizador entrar na sua conta Windows. A chave é usada para programas de 32 bits em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Monitoriza alterações nas definições de aplicação.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitoriza entradas automáticas comuns que se ligam diretamente ao Windows Explorer e geralmente são executadas em processo com explorer.exe.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Monitoriza entradas automáticas comuns que se ligam diretamente ao Windows Explorer e geralmente são executadas em processo com explorer.exe.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitores para registo de manipulador de sobreposição de ícones.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitores para registo de manipulador de sobreposição de ícones para programas de 32 bits em execução em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitores para novos plugins de objetos de ajuda de navegador para o Internet Explorer. Utilizado para aceder ao Modelo de Objeto sinuoso (DOM) da página atual e para controlar a navegação.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitores para novos plugins de objetos de ajuda de navegador para o Internet Explorer. Usado para aceder ao Modelo de Objeto sinuoso (DOM) da página atual e para controlar a navegação para programas de 32 bits em execução em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Monitores para novas extensões do Internet Explorer, tais como menus de ferramentas personalizadas e botões de barra de ferramentas personalizados.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Monitores para novas extensões do Internet Explorer, tais como menus de ferramentas personalizadas e botões de barra de ferramentas personalizados para programas de 32 bits em execução em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitoriza os condutores de 32 bits associados com wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc. Semelhante à secção [dos condutores] no SISTEMA. Arquivo INI.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitoriza os condutores de 32 bits associados a wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc para programas de 32 bits em execução em computadores de 64 bits. Semelhante à secção [dos condutores] no SISTEMA. Arquivo INI.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Monitoriza a lista de DLLs conhecidos ou comumente utilizados; este sistema impede as pessoas de explorar em fracas permissões de diretório de aplicações, largando em versões de cavalos de Troia de DLLs do sistema.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Monitoriza a lista de pacotes capazes de receber notificações de eventos do Winlogon, o modelo de suporte de logon interativo para o sistema operativo Windows.

## <a name="enable-change-tracking"></a><a name="onboard"></a>Ativar o rastreio de alterações

Para começar a rastrear alterações, tem de ativar a solução De rastreio de alterações. Há muitas maneiras de embarcar nas máquinas para mudar de rastreamento. Seguem-se as formas recomendadas e apoiadas de embarcar na solução.

* [De uma máquina virtual](automation-onboard-solutions-from-vm.md)
* [Desde navegar em várias máquinas](automation-onboard-solutions-from-browse.md)
* [Da sua conta de Automação](automation-onboard-solutions-from-automation-account.md)
* [Com um livro de execução azure Automation](automation-onboard-solutions.md)

## <a name="configure-change-tracking"></a>Configurar o rastreio de alterações

Para aprender a embarcar computadores para a solução, consulte [soluções de Automação onboarding](automation-onboard-solutions-from-automation-account.md). Assim que tiver uma máquina a embarcar com a solução Change Tracking, pode configurar os itens para rastrear. Quando ativa um novo ficheiro ou chave de registo, está ativado para ambos os Change Tracking.

Para rastrear alterações nos ficheiros tanto no Windows como no Linux, são utilizados hashes MD5 dos ficheiros. As hashes teses são então usadas para detetar se uma mudança foi feita desde o último inventário.

## <a name="enable-file-integrity-monitoring-in-azure-security-center"></a>Ativar a monitorização da integridade do ficheiro no Centro de Segurança Azure

O Azure Security Center adicionou monitorização da integridade do ficheiro (FIM), que é construída em Azure Change Tracking. Enquanto o FIM monitoriza apenas ficheiros e registos, a solução completa de Rastreio de Alterações também inclui:

- Alterações de software
- Serviços Windows
- Linux Daemons

Se já ativou o FIM e gostaria de experimentar a solução completa de Rastreio de Mudanças, tem de realizar os seguintes passos. As definições não são removidas por este processo.

> [!NOTE]
> Ativar a solução completa de rastreio de mudanças pode causar encargos adicionais, para mais informações, ver [Preços de Automação](https://azure.microsoft.com/pricing/details/automation/).

1. Retire a solução de monitorização navegando para o espaço de trabalho e localizando-a na [Lista de soluções de monitorização instaladas](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions).
2. Clique no nome da solução para abrir a sua página sumária e, em seguida, clique em Eliminar, conforme detalhado em [Remover uma solução de monitorização](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).
3. Reativar a solução navegando para a conta Automation e selecionando **o Change Tracking** sob gestão de **configuração**.
4. Confirme os detalhes de definição do espaço de trabalho e clique em **Ativar**.

## <a name="configure-file-content-change-tracking"></a>Configurar o rastreio de alteração de conteúdo de ficheiro

Pode visualizar o conteúdo antes e depois da alteração do ficheiro com o rastreio da alteração do conteúdo do ficheiro. Esta funcionalidade está disponível para ficheiros Windows e Linux. Para cada alteração a um ficheiro, o conteúdo do ficheiro é armazenado numa conta de armazenamento. O ficheiro é mostrado antes e depois da alteração, inline ou lado a lado. Para saber mais, consulte [Ver o conteúdo de um ficheiro rastreado](change-tracking-file-contents.md).

![ver alterações em um arquivo](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="configure-windows-registry-keys-to-track"></a>Configure as chaves de registo do Windows para rastrear

Utilize os seguintes passos para configurar o rastreio da chave de registo nos computadores Windows:

1. Na sua conta de Automação, selecione **'Localizar rastreio sob** **Gestão de Configuração**.' Clique em **Definições de Edição** (o símbolo da engrenagem).
2. Na página 'Change Tracking', selecione **Registo do Windows**e, em seguida, clique em + **Adicionar** para adicionar uma nova chave de registo para rastrear.
3. No **Registo do Windows adicionar para rastrear alterações,** introduza as informações para a tecla rastrear e clicar em **Guardar**.

|Propriedade  |Descrição  |
|---------|---------|
|Ativado     | Determina se a regulação é aplicada.        |
|Nome do Item     | Nome amigável da chave do registo a ser rastreado.        |
|Grupo     | Um nome de grupo para agrupar logicamente chaves de registo.        |
|Chave do Registo do Windows   | O caminho para verificar a chave do registo. Por exemplo: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

## <a name="configure-file-tracking-on-windows"></a>Configure o rastreio de ficheiros no Windows

Utilize os seguintes passos para configurar o rastreio de ficheiros nos computadores Windows:

1. Na sua conta de Automação, selecione **'Localizar rastreio sob** **Gestão de Configuração**.' Clique em **Definições de Edição** (o símbolo da engrenagem).
2. Na página 'Change Tracking', selecione **Ficheiros Windows**e, em seguida, clique em **+ Adicionar** para adicionar um novo ficheiro para rastrear.
3. No **Ficheiro Adicionar Windows para Localizar**, introduza as informações para o ficheiro rastrear e clicar em **Guardar**.

|Propriedade  |Descrição  |
|---------|---------|
|Ativado     | É verdade se a definição for aplicada, e falsa de outra forma.        |
|Nome do Item     | Nome amigável do ficheiro a ser rastreado.        |
|Grupo     | Um nome de grupo para agrupar ficheiros logicamente.        |
|Introduzir o Caminho     | O caminho para verificar o ficheiro, por exemplo, **c:\temp\\\*.txt**<br>Também pode utilizar variáveis ambientais, tais como `%winDir%\System32\\\*.*`.       |
|Recursão     | É verdade que se a recursion é usada quando se procura o item a ser rastreado, e Falso de outra forma.        |
|Carregar conteúdo do ficheiro para todas as definições| Fiel ao upload do conteúdo do ficheiro em alterações rastreadas, e Falso de outra forma.|

## <a name="configure-file-tracking-on-linux"></a>Configure rastreio de ficheiros em Linux

Utilize os seguintes passos para configurar o rastreio de ficheiros nos computadores Linux:

1. Na sua conta de Automação, selecione **'Localizar rastreio sob** **Gestão de Configuração**.' Clique em **Definições de Edição** (o símbolo da engrenagem).
2. Na página 'Change Tracking', selecione **Ficheiros Linux**e, em seguida, clique em **+ Adicionar** um novo ficheiro para rastrear.
3. No **ficheiro Add Linux para alterar**o rastreio, introduza as informações para o ficheiro ou diretório para rastrear e clicar em **Guardar**.

|Propriedade  |Descrição  |
|---------|---------|
|Ativado     | Determina se a regulação é aplicada.        |
|Nome do Item     | Nome amigável do ficheiro a ser rastreado.        |
|Grupo     | Um nome de grupo para agrupar ficheiros logicamente.        |
|Introduzir o Caminho     | O caminho para verificar o ficheiro. Por exemplo: "/etc/*.conf"       |
|Tipo de Caminho     | Tipo de item a ser rastreado, valores possíveis são Arquivo e Diretório.        |
|Recursão     | Determina se recursão é utilizada ao procurar o item a controlar.        |
|Utilizar o Sudo     | Esta definição determina se o sudo é utilizado ao verificar o item.         |
|Ligações     | Esta definição determina como as ligações simbólicas são processadas ao atravessar diretórios.<br> **Ignore** - Ignora links simbólicos e não inclui os ficheiros/diretórios referenciados.<br>**Siga** as ligações simbólicas durante a recursão e inclui também os ficheiros/diretórios referenciados.<br>**Gerir** - Segue as ligações simbólicas e permite alterar o conteúdo devolvido.     |
|Carregar conteúdo do ficheiro para todas as definições| Ativa ou desativa o carregamento de conteúdo do ficheiro em alterações registadas. Opções disponíveis: **Verdadeiro** ou **Falso**.|

> [!NOTE]
> A opção “Gerir” ligações não é recomendada. A obtenção de conteúdo do ficheiro não é suportada.

## <a name="search-logs"></a>Registos de pesquisa

Pode fazer várias pesquisas contra os registos para alterar registos. Com a página 'Change Tracking' aberta, clique em **Log Analytics,** isto abre a página De Registos. A tabela seguinte fornece pesquisas de registo de amostras para registos de alterações recolhidos por esta solução:

|Consulta  |Descrição  |
|---------|---------|
|ConfiguraçãoData<br>&#124; onde ConfigDataType == "WindowsServices" e SvcStartupType == "Auto"<br>&#124; onde svcState == "Parou"<br>&#124; resumir arg_max (TimeGenerated, *) por SoftwareName, Computador         | Mostra os registos de inventário mais recentes dos Serviços Windows que foram definidos para auto mas foram reportados como sendo parados<br>Os resultados estão limitados ao registo mais recente para esse SoftwareName e Computador      |
|ConfiguraçãoChange<br>&#124; onde configChangeType == "Software" e ChangeCategory == "Removido"<br>&#124; ordem por TimeGenerated desc|Mostra os registos de alteração para software removido|

## <a name="alert-on-changes"></a>Alerta sobre alterações

Uma capacidade chave do Change Tracking está a alertar sobre o estado de configuração e quaisquer alterações ao estado de configuração do seu ambiente híbrido. O exemplo seguinte mostra que o ficheiro **C:\windows\system32\drivers\etc\hosts** foi modificado numa máquina. Este ficheiro é importante porque o Windows o utiliza para resolver nomes de anfitriões para endereços IP. Esta operação tem precedência sobre o DNS, e pode resultar em problemas de conectividade ou na reorientação do tráfego para sites maliciosos ou de outra forma perigosos.

![Um gráfico que mostra a mudança de ficheiro dos anfitriões](./media/change-tracking/changes.png)

Para analisar mais além desta alteração, aceda à pesquisa de Registo a partir de clicar em **Log Analytics**. Uma vez em Registo de pesquisa, procure alterações `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`de conteúdo no ficheiro Anfitriões com a consulta . Esta consulta procura alterações que incluam uma alteração do conteúdo dos ficheiros para ficheiros cujo caminho totalmente qualificado contém a palavra "anfitriões". Também pode solicitar um ficheiro específico, alterando a parte do `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`caminho para a sua forma totalmente qualificada (como).

Depois da consulta devolver os resultados desejados, clique em **Nova regra** de alerta na pesquisa de registo para abrir a página de criação de alerta. Também pode navegar para esta experiência através do **Monitor Azure** no portal Azure. 

Verifique novamente a sua consulta e modifique a lógica de alerta. Neste caso, quer que o alerta seja desencadeado se houver mesmo uma alteração detetada em todas as máquinas do ambiente.

![Uma imagem que mostra a consulta de mudança para rastrear alterações no ficheiro dos anfitriões](./media/change-tracking/change-query.png)

Após a definição da lógica da condição, atribua grupos de ação para realizar ações em resposta ao alerta que está a ser desencadeado. Neste caso, criei e-mails a enviar e um bilhete ITSM a ser criado.  Muitas outras ações úteis também podem ser tomadas, tais como desencadear uma Função Azure, livro de automação, webhook ou Logic App.

![Uma imagem configurando um grupo de ação para alertar sobre a mudança](./media/change-tracking/action-groups.png)

Depois de todos os parâmetros e lógicas serem definidos, podemos aplicar o alerta ao ambiente.

### <a name="alert-suggestions"></a>Sugestões de alerta

Embora alertar sobre as alterações ao ficheiro dos anfitriões seja uma boa aplicação de alertas para dados de Rastreio de Alterações ou Inventário, existem muitos mais cenários para alertar, incluindo os casos definidos juntamente com as suas consultas de exemplo na secção abaixo.

|Consulta  |Descrição  |
|---------|---------|
|ConfiguraçãoChange <br>&#124; onde configChangeType == "Ficheiros" e\\FileSystemPath contém\\" c: controladores do sistema de janelas32\\\\"|Útil para rastrear alterações nos ficheiros críticos do sistema|
|ConfiguraçãoChange <br>&#124; onde FieldsChanged contém "FileContentChecksum" e FileSystemPath\\==\\\\"c:\\\\windows system32 drivers etc hosts"|Útil para rastrear modificações em ficheiros de configuração chave|
|ConfiguraçãoChange <br>&#124; onde configChangeType == "WindowsServices" e SvcName contém "w3svc" e SvcState == "Parou"|Útil para rastrear alterações nos serviços críticos do sistema|
|ConfiguraçãoChange <br>&#124; onde configChangeType == "Daemons" e SvcName contém "ssh" e SvcState != "Running"|Útil para rastrear alterações nos serviços críticos do sistema|
|ConfiguraçãoChange <br>&#124; onde configChangeType == "Software" e ChangeCategory == "Adicionado"|Útil para ambientes que precisam de configurações de software bloqueadas|
|ConfiguraçãoData <br>&#124; onde o SoftwareName contém "Monitoring Agent" e CurrentVersion != "8.0.11081.0"|Útil para ver quais as máquinas que têm uma versão de software desatualizada ou não conforme instalada. Relata o último estado de configuração relatado, não alterações.|
|ConfiguraçãoChange <br>&#124; onde registryKey ==\\\\@"HKEY_LOCAL_MACHINE SOFTWARE Microsoft\\Windows\\CurrentVersion\\QualityCompat"| Útil para rastrear alterações em chaves antivírus cruciais|
|ConfiguraçãoChange <br>&#124; onde o RegistoKey\\contém\\serviços\\\\de\\controlo\\partilhado de parâmetros de acesso partilhados do sistema de acesso partilhado HKEY_LOCAL_MACHINE do sistema de acesso partilhado do sistema de acesso ao sistema de acesso @".| Útil para rastrear alterações nas definições de firewall|

## <a name="next-steps"></a>Passos seguintes

Visite o tutorial sobre O Rastreio de Mudanças para saber mais sobre a utilização da solução:

> [!div class="nextstepaction"]
> [Resolver problemas relacionados com alterações no seu ambiente](automation-tutorial-troubleshoot-changes.md)

* Utilize as pesquisas de [registo nos registos do Monitor Azure](../log-analytics/log-analytics-log-searches.md) para visualizar dados de rastreio de alterações detalhadas.
