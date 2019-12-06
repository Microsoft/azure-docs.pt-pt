---
title: Controlar alterações com a automação do Azure
description: A solução Controle de Alterações ajuda a identificar as alterações no software e no serviço Windows que ocorrem em seu ambiente.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 04/29/2019
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1fd800062c4a8362919b1818550b2fca9fa3eb88
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850555"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Controlar alterações em seu ambiente com a solução Controle de Alterações

Este artigo ajuda você a usar a solução de Controle de Alterações para identificar facilmente as alterações em seu ambiente. A solução acompanha as alterações no software Windows e Linux, arquivos Windows e Linux, chaves de registro do Windows, serviços do Windows e daemons do Linux. Identificar alterações de configuração pode ajudá-lo a identificar problemas operacionais.

As alterações no software instalado, nos serviços do Windows, no registro e nos arquivos do Windows e nos daemons do Linux nos servidores monitorados são enviadas para o serviço de Azure Monitor na nuvem para processamento. A lógica é aplicada aos dados recebidos e o serviço cloud regista os dados. Usando as informações no painel Controle de Alterações, você pode ver facilmente as alterações feitas na sua infraestrutura de servidor.

> [!NOTE]
> A automação do Azure Controle de Alterações controla as alterações nas máquinas virtuais. Para rastrear Azure Resource Manager alterações de propriedade, consulte [histórico de alterações](../governance/resource-graph/how-to/get-resource-changes.md)do grafo de recursos do Azure.

## <a name="supported-windows-operating-systems"></a>Sistemas de operativos do Windows

As seguintes versões do sistema operativo Windows são suportadas oficialmente para o agente do Windows:

* Windows Server 2008 R2 ou posterior

## <a name="supported-linux-operating-systems"></a>Sistemas operativos Linux suportados

As seguintes distribuições do Linux são oficialmente suportadas. No entanto, o agente do Linux também pode ser executado em outras distribuições não listadas. Salvo indicação em contrário, todas as versões secundárias têm suporte para cada versão principal listada.

### <a name="64-bit"></a>64 bits

* CentOS 6 e 7
* Amazon Linux 2017, 9
* Oracle Linux 6 e 7
* Red Hat Enterprise Linux Server 6 e 7
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14, 4 LTS, 16, 4 LTS e 18, 4 LTS
* SUSE Linux Enterprise Server 12

### <a name="32-bit"></a>32 bits

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14, 4 LTS e 16, 4 LTS

## <a name="onboard"></a>Habilitar Controle de Alterações e inventário

Para começar a controlar as alterações, você precisa habilitar a solução de Controle de Alterações e inventário. Há várias maneiras de carregar computadores para Controle de Alterações e inventário. A seguir estão as maneiras recomendadas e com suporte para integrar a solução.

* [De uma máquina virtual](automation-onboard-solutions-from-vm.md)
* [De navegar por vários computadores](automation-onboard-solutions-from-browse.md)
* [Da sua conta de automação](automation-onboard-solutions-from-automation-account.md)
* [Com um runbook de automação do Azure](automation-onboard-solutions.md)

## <a name="configuring-change-tracking-and-inventory"></a>Configurando Controle de Alterações e inventário

Para saber como carregar computadores para a solução, visite: [integração de soluções de automação](automation-onboard-solutions-from-automation-account.md). Quando você tiver uma integração de máquina com a solução de Controle de Alterações e inventário, poderá configurar os itens a serem acompanhados. Quando você habilita um novo arquivo ou chave do registro a ser acompanhado, ele é habilitado para Controle de Alterações e inventário.

Para controlar as alterações nos arquivos no Windows e no Linux, os hashes MD5 dos arquivos são usados. Esses hashes são então usados para detectar se uma alteração foi feita desde o último inventário.

### <a name="file-integrity-monitoring-in-azure-security-center"></a>Monitoramento de integridade de arquivo na central de segurança do Azure

A central de segurança do Azure adicionou o FIM (monitoramento de integridade de arquivo) criado no Azure Controle de Alterações. Embora o FIM monitore arquivos e registros apenas, a solução de Controle de Alterações completa também inclui:

- Alterações de software
- Serviços Windows
- Daemons do Linux

Se você já tiver habilitado o FIM e quiser experimentar a solução de Controle de Alterações completa, você precisará executar as etapas a seguir. As configurações não são removidas por esse processo.

> [!NOTE]
> A habilitação da solução de Controle de Alterações completa pode causar encargos adicionais, para obter mais informações, consulte [preços de automação](https://azure.microsoft.com/pricing/details/automation/).

1. Remova a solução de monitoramento navegando até o espaço de trabalho e localizando-a na [lista de soluções de monitoramento instaladas](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions).
2. Clique no nome da solução para abrir sua página de resumo e, em seguida, clique em excluir, conforme detalhado em [remover uma solução de monitoramento](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).
3. Habilite novamente a solução navegando até a conta de automação e selecionando **controle de alterações** no menu de recursos em **Gerenciamento de configuração**.
4. Confirme os detalhes de configuração do espaço de trabalho e clique em **habilitar**.

### <a name="configure-linux-files-to-track"></a>Configurar arquivos do Linux para acompanhar

Use as etapas a seguir para configurar o rastreamento de arquivos em computadores Linux:

1. Em sua conta de automação, selecione **controle de alterações** em gerenciamento de **configuração**. Clique em **Editar configurações** (o símbolo de engrenagem).
2. Na página **controle de alterações** , selecione **arquivos do Linux**e clique em **+ Adicionar** para adicionar um novo arquivo a ser acompanhado.
3. Em **Adicionar arquivo do Linux para controle de alterações**, insira as informações para o arquivo ou diretório a ser acompanhado e clique em **salvar**.

|Propriedade  |Descrição  |
|---------|---------|
|Ativado     | Determina se a configuração é aplicada.        |
|Nome do Item     | Nome amigável do arquivo a ser acompanhado.        |
|Grupo     | Um nome de grupo para agrupar logicamente os arquivos.        |
|Introduzir o Caminho     | O caminho para verificar o arquivo. Por exemplo: "/etc/*. conf"       |
|Tipo de Caminho     | Tipo de item a ser acompanhado, os valores possíveis são arquivo e diretório.        |
|Recursão     | Determina se recursão é utilizada ao procurar o item a controlar.        |
|Utilizar o Sudo     | Esta definição determina se o sudo é utilizado ao verificar o item.         |
|Ligações     | Esta definição determina como as ligações simbólicas são processadas ao atravessar diretórios.<br> **Ignorar** – ignora links simbólicos e não inclui os arquivos/diretórios referenciados.<br>**Seguir** -segue os links simbólicos durante a recursão e também inclui os arquivos/diretórios referenciados.<br>**Gerenciar** -segue os links simbólicos e permite alterar o conteúdo retornado.     |
|Carregar conteúdo do ficheiro para todas as definições| Ativa ou desativa o carregamento de conteúdo do ficheiro em alterações registadas. Opções disponíveis: **Verdadeiro** ou **Falso**.|

> [!NOTE]
> A opção “Gerir” ligações não é recomendada. A obtenção de conteúdo do ficheiro não é suportada.

### <a name="configure-windows-files-to-track"></a>Configurar arquivos do Windows para acompanhar

Use as etapas a seguir para configurar o rastreamento de arquivos em computadores Windows:

1. Em sua conta de automação, selecione **controle de alterações** em gerenciamento de **configuração**. Clique em **Editar configurações** (o símbolo de engrenagem).
2. Na página **controle de alterações** , selecione **arquivos do Windows**e clique em **+ Adicionar** para adicionar um novo arquivo a ser acompanhado.
3. Em **Adicionar arquivo do Windows para controle de alterações**, insira as informações para o arquivo a ser acompanhado e clique em **salvar**.

|Propriedade  |Descrição  |
|---------|---------|
|Ativado     | Determina se a configuração é aplicada.        |
|Nome do Item     | Nome amigável do arquivo a ser acompanhado.        |
|Grupo     | Um nome de grupo para agrupar logicamente os arquivos.        |
|Introduzir o Caminho     | O caminho para verificar o ficheiro, por exemplo: "c:\temp\\\\*.txt"<br>Também pode utilizar variáveis de ambiente, tais como "%winDir%\System32\\\*.*"       |
|Recursão     | Determina se recursão é utilizada ao procurar o item a controlar.        |
|Carregar conteúdo do ficheiro para todas as definições| Ativa ou desativa o carregamento de conteúdo do ficheiro em alterações registadas. Opções disponíveis: **Verdadeiro** ou **Falso**.|

## <a name="wildcard-recursion-and-environment-settings"></a>Curinga, recursão e configurações de ambiente

A recursão permite que você especifique curingas para simplificar o rastreamento em diretórios e variáveis de ambiente para permitir que você rastreie arquivos entre ambientes com nomes de unidade ou vários. A lista a seguir mostra informações comuns que você deve saber ao configurar a recursão:

* Caracteres curinga são necessários para acompanhar vários arquivos
* Se você estiver usando caracteres curinga, eles só poderão ser usados no último segmento de um caminho. (como `c:\folder\*file*` ou `/etc/*.conf`)
* Se uma variável de ambiente tiver um caminho inválido, a validação terá êxito, mas esse caminho falhará quando o inventário for executado.
* Evite caminhos gerais, como `c:\*.*` ao definir o caminho, pois isso resultaria em muitas pastas sendo atravessadas.

## <a name="configure-file-content-tracking"></a>Configurar o rastreamento de conteúdo do arquivo

Você pode exibir o conteúdo antes e depois de uma alteração de um arquivo com o conteúdo do arquivo Controle de Alterações. Isso está disponível para arquivos Windows e Linux, para cada alteração no arquivo, o conteúdo do arquivo é armazenado em uma conta de armazenamento e mostra o arquivo antes e depois da alteração, embutido ou lado a lado. Para saber mais, consulte [Exibir o conteúdo de um arquivo rastreado](change-tracking-file-contents.md).

![Exibir alterações em um arquivo](./media/change-tracking-file-contents/view-file-changes.png)

### <a name="configure-windows-registry-keys-to-track"></a>Configurar chaves do registro do Windows para acompanhar

Use as etapas a seguir para configurar o rastreamento de chave do registro em computadores Windows:

1. Em sua conta de automação, selecione **controle de alterações** em gerenciamento de **configuração**. Clique em **Editar configurações** (o símbolo de engrenagem).
2. Na página **controle de alterações** , selecione **registro do Windows**e clique em **+ Adicionar** para adicionar uma nova chave do registro a ser controlada.
3. Em **adicionar registro do Windows para controle de alterações**, insira as informações da chave a ser acompanhada e clique em **salvar**.

|Propriedade  |Descrição  |
|---------|---------|
|Ativado     | Determina se a configuração é aplicada.        |
|Nome do Item     | Nome amigável da chave do registro a ser rastreada.        |
|Grupo     | Um nome de grupo para agrupar logicamente as chaves do registro.        |
|Chave do Registo do Windows   | O caminho para verificar a chave do registro. Por exemplo: "HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

## <a name="limitations"></a>Limitações

Atualmente, a solução de Controle de Alterações não oferece suporte aos seguintes itens:

* Recursão para rastreamento de registro do Windows
* Sistemas de arquivos de rede

Outras limitações:

* A coluna e os valores de **tamanho máximo do arquivo** não são usados na implementação atual.
* Se você coletar mais de 2500 arquivos no ciclo de coleta de 30 minutos, o desempenho da solução poderá ser degradado.
* Quando o tráfego de rede é alto, os registros de alteração podem levar até seis horas para serem exibidos.
* Se você modificar a configuração enquanto um computador for desligado, o computador poderá postar alterações que pertenciam à configuração anterior.

## <a name="known-issues"></a>Problemas Conhecidos

Atualmente, a solução Controle de Alterações está enfrentando os seguintes problemas:

* As atualizações de hotfix não são coletadas no Windows Server 2016 Core RS3 machines.
* Os daemons do Linux podem mostrar um estado alterado mesmo que não houvesse nenhuma alteração. Isso ocorre devido a como o campo de `SvcRunLevels` é capturado.

## <a name="change-tracking-data-collection-details"></a>Detalhes da coleta de dados de Controle de Alterações

A tabela a seguir mostra a frequência de coleta de dados para os tipos de alterações. Para cada tipo, o instantâneo de dados do estado atual também é atualizado pelo menos a cada 24 horas:

| **Alterar tipo** | **Frequência** |
| --- | --- |
| Registro do Windows | 50 minutos |
| Arquivo do Windows | 30 minutos |
| Arquivo do Linux | 15 minutos |
| Serviços Windows | 10 segundos a 30 minutos</br> Padrão: 30 minutos |
| Daemons do Linux | 5 minutos |
| Software do Windows | 30 minutos |
| Software Linux | 5 minutos |

A tabela a seguir mostra os limites de item acompanhados por computador para Controle de Alterações.

| **Recurso** | **Limite**| **Notas** |
|---|---|---|
|Ficheiros|500||
|Registo|250||
|Software do Windows|250|Não inclui hotfixes de software|
|Pacotes do Linux|1250||
|Serviços|250||
|Demonstração|250||

A média Log Analytics uso de dados para um computador usando Controle de Alterações e inventário é de aproximadamente 40 MB por mês. Esse valor é apenas uma aproximação e está sujeito a alterações com base em seu ambiente. É recomendável que você monitore seu ambiente para ver o uso exato que você tem.

### <a name="windows-service-tracking"></a>Rastreamento de serviços do Windows

A frequência de coleta padrão para serviços do Windows é de 30 minutos. Para configurar a frequência, acesse **controle de alterações**. Em **Editar configurações** na guia **Serviços do Windows** , há um controle deslizante que permite que você altere a frequência de coleta dos serviços do Windows da mesma forma que 10 segundos para até 30 minutos. Mova a barra deslizante para a frequência desejada e a salva automaticamente.

![Controle deslizante serviços do Windows](./media/change-tracking/windowservices.png)

O agente só controla as alterações, isso otimiza o desempenho do agente. Definir um limite alto pode perder alterações se o serviço for revertido para seu estado original. Definir a frequência como um valor menor permite que você pegue as alterações que podem ser perdidas caso contrário.

> [!NOTE]
> Embora o agente possa controlar as alterações até um intervalo de 10 segundos, os dados ainda levarão alguns minutos para serem exibidos no Portal. As alterações durante o tempo de exibição no portal ainda são rastreadas e registradas.

### <a name="registry-key-change-tracking"></a>Controle de alterações da chave do registro

A finalidade de monitorar alterações nas chaves do registro é identificar pontos de extensibilidade onde o código de terceiros e o malware podem ser ativados. A lista a seguir mostra a lista de chaves de registro pré-configuradas. Essas chaves estão configuradas, mas não habilitadas. Para controlar essas chaves do registro, você deve habilitar cada uma delas.

> [!div class="mx-tdBreakAll"]
> |Chave do Registo | Finalidade |
> |---------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitora as entradas comuns de inicialização automática que se conectam diretamente ao Windows Explorer e geralmente são executadas em processo com o Explorer. exe.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Monitora scripts que são executados na inicialização.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Monitora scripts que são executados no desligamento.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Monitora as chaves que são carregadas antes de o usuário entrar em sua conta do Windows. A chave é usada para programas de 32 bits em execução em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Monitora alterações nas configurações do aplicativo.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitora as entradas comuns de inicialização automática que se conectam diretamente ao Windows Explorer e geralmente são executadas em processo com o Explorer. exe.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Monitora as entradas comuns de inicialização automática que se conectam diretamente ao Windows Explorer e geralmente são executadas em processo com o Explorer. exe.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitora o registro do manipulador de sobreposição de ícone.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitora o registro do manipulador de sobreposição de ícone para programas de 32 bits em execução em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitora novos plug-ins de objeto auxiliar de navegador para o Internet Explorer. Usado para acessar o Modelo de Objeto do Documento (DOM) da página atual e para controlar a navegação.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitora novos plug-ins de objeto auxiliar de navegador para o Internet Explorer. Usado para acessar o Modelo de Objeto do Documento (DOM) da página atual e para controlar a navegação para programas de 32 bits executados em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Monitora novas extensões do Internet Explorer, como menus de ferramentas personalizadas e botões de barra de ferramenta personalizados.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Monitora novas extensões do Internet Explorer, como menus de ferramentas personalizadas e botões de barra de ferramenta personalizados para programas de 32 bits em execução em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitora os drivers de 32 bits associados a wavemapper, wave1 e wave2, MSACM. imaadpcm,. MSADPCM,. msgsm610 e vidc. Semelhante à seção [Drivers] no sistema. Arquivo INI.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitora os drivers de 32 bits associados a wavemapper, wave1 e wave2, MSACM. imaadpcm,. MSADPCM,. msgsm610 e vidc para programas de 32 bits em execução em computadores de 64 bits. Semelhante à seção [Drivers] no sistema. Arquivo INI.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Monitora a lista de DLLs de sistema conhecidas ou comumente usadas; Esse sistema impede que as pessoas explorem as permissões de diretório de aplicativo fracas descartando as versões do cavalo de Troia das DLLs do sistema.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Monitora a lista de pacotes capazes de receber notificações de eventos do Winlogon, o modelo de suporte de logon interativo para o sistema operacional Windows.

## <a name="network-requirements"></a>Requisitos de rede

Os endereços a seguir são necessários especificamente para Controle de Alterações. A comunicação com esses endereços é feita pela porta 443.

|Azure Público  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

## <a name="use-change-tracking"></a>Usar Controle de Alterações

Depois que a solução estiver habilitada, você poderá exibir o resumo das alterações para os computadores monitorados selecionando **controle de alterações** em **Gerenciamento de configuração** em sua conta de automação.

Você pode exibir as alterações em seus computadores e, em seguida, detalhar os detalhes de cada evento. Os menus suspensos estão disponíveis na parte superior do gráfico para limitar o gráfico e as informações detalhadas com base no tipo de alteração e nos intervalos de tempo. Você também pode clicar e arrastar o gráfico para selecionar um intervalo de tempo personalizado. **O tipo de alteração** será um dos seguintes **eventos**de valores, **daemons**, **arquivos**, **registro**, **software**e serviços do **Windows**. Categoria mostra o tipo de alteração e pode ser **adicionada**, **modificada**ou **removida**.

![imagem do painel de Controle de Alterações](./media/change-tracking/change-tracking-dash01.png)

Clicar em uma alteração ou evento exibe as informações detalhadas sobre essa alteração. Como você pode ver no exemplo, o tipo de inicialização do serviço foi alterado de manual para automático.

![imagem dos detalhes do controle de alterações](./media/change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Pesquisar logs

Além dos detalhes que são fornecidos no portal, as pesquisas podem ser feitas nos logs. Com a página **controle de alterações** aberta, clique em **log Analytics**, isso abrirá a página **logs** .

### <a name="sample-queries"></a>Amostras de consultas

A tabela a seguir fornece pesquisas de log de exemplo para registros de alteração coletados por essa solução:

|Consulta  |Descrição  |
|---------|---------|
|ConfigurationData<br>&#124;em que ConfigDataType = = "WindowsServices" e SvcStartupType = = "auto"<br>&#124;em que SvcState = = "Stopped"<br>&#124;resumir arg_max (TimeGenerated, *) por SoftwareName, computador         | Mostra os registros de inventário mais recentes para serviços do Windows que foram definidos como automáticos, mas foram relatados como sendo interrompidos<br>Os resultados são limitados ao registro mais recente para o SoftwareName e o computador      |
|ConfigurationChange<br>&#124;em que ConfigChangeType = = "software" e ChangeCategory = = "removida"<br>&#124;ordenar por TimeGenerated desc|Mostra os registros de alteração do software removido|

## <a name="alert-on-changes"></a>Alertar sobre alterações

Um recurso importante de Controle de Alterações e inventário é a capacidade de alertar sobre o estado de configuração e quaisquer alterações no estado de configuração do ambiente híbrido.

No exemplo a seguir, a captura de tela mostra que o arquivo `C:\windows\system32\drivers\etc\hosts` foi modificado em um computador. Esse arquivo é importante porque o arquivo de hosts é usado pelo Windows para resolver nomes de host para endereços IP e tem precedência sobre mesmo DNS, o que pode resultar em problemas de conectividade ou no redirecionamento de tráfego para sites mal-intencionados ou perigosos.

![Um gráfico mostrando a alteração do arquivo de hosts](./media/change-tracking/changes.png)

Para analisar essa alteração ainda mais, vá para pesquisa de logs em **log Analytics**. Uma vez na pesquisa de logs, pesquise alterações de conteúdo no arquivo de hosts com a consulta `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`. Essa consulta procura alterações que incluíram uma alteração de conteúdo de arquivo para arquivos cujo caminho totalmente qualificado contém a palavra "hosts". Você também pode solicitar um arquivo específico alterando a parte do caminho para sua forma totalmente qualificada (como `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`).

Depois que a consulta retornar os resultados desejados, clique no botão **nova regra de alerta** na experiência de pesquisa de logs para abrir a página de criação de alertas. Você também pode navegar até essa experiência por meio de **Azure monitor** no portal do Azure. Na experiência de criação de alerta, Verifique nossa consulta novamente e modifique a lógica de alerta. Nesse caso, você deseja que o alerta seja disparado se houver até mesmo uma alteração detectada em todos os computadores do ambiente.

![Uma imagem que mostra a consulta de alteração para controlar as alterações no arquivo de hosts](./media/change-tracking/change-query.png)

Depois que a lógica da condição for definida, atribua grupos de ações para executar ações em resposta ao alerta que está sendo disparado. Nesse caso, configurei emails a serem enviados e um tíquete de ITSM a ser criado.  Muitas outras ações úteis também podem ser tomadas como o disparo de uma função do Azure, runbook de automação, webhook ou aplicativo lógico.

![Uma imagem Configurando um grupo de ações para alertar sobre a alteração](./media/change-tracking/action-groups.png)

Depois que todos os parâmetros e a lógica são definidos, podemos aplicar o alerta ao ambiente.

### <a name="alert-suggestions"></a>Sugestões de alerta

Enquanto os alertas sobre alterações no arquivo de hosts são uma boa aplicação de alertas para dados de Controle de Alterações ou de inventário, há muitos outros cenários de alerta, incluindo os casos definidos junto com suas consultas de exemplo na seção abaixo.

|Consulta  |Descrição  |
|---------|---------|
|ConfigurationChange <br>&#124;em que ConfigChangeType = = "Files" e FileSystemPath contém "c:\\drivers do Windows\\system32\\\\"|Útil para controlar alterações em arquivos críticos do sistema|
|ConfigurationChange <br>&#124;onde Fields contém "FileContentChecksum" e FileSystemPath = = "c:\\Windows\\system32\\drivers\\etc\\hosts"|Útil para controlar as modificações nos principais arquivos de configuração|
|ConfigurationChange <br>&#124;em que ConfigChangeType = = "WindowsServices" e SvcName contêm "W3SVC" e SvcState = = "Stopped"|Útil para controlar alterações em serviços críticos do sistema|
|ConfigurationChange <br>&#124; where ConfigChangeType == "Daemons" and SvcName contains "ssh" and SvcState != "Running"|Útil para controlar alterações em serviços críticos do sistema|
|ConfigurationChange <br>&#124;em que ConfigChangeType = = "software" e ChangeCategory = = "Added"|Útil para ambientes que precisam bloquear configurações de software|
|ConfigurationData <br>&#124;onde SoftwareName contém "agente de monitoramento" e CurrentVersion! = "8.0.11081.0"|Útil para ver quais computadores têm uma versão de software desatualizada ou não compatível instalada. Ele relata o último estado de configuração relatado, não as alterações.|
|ConfigurationChange <br>&#124;onde RegistryKey = = @ "HKEY_LOCAL_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| Útil para controlar alterações em chaves antivírus cruciais|
|ConfigurationChange <br>&#124;onde RegistryKey contém @ "HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\SharedAccess\\Parameters\\FirewallPolicy"| Útil para controlar alterações nas configurações de firewall|

## <a name="next-steps"></a>Passos seguintes

Visite o tutorial em Controle de Alterações para saber mais sobre como usar a solução:

> [!div class="nextstepaction"]
> [Solucionar problemas de alterações em seu ambiente](automation-tutorial-troubleshoot-changes.md)

* Use [pesquisas de log em logs de Azure monitor](../log-analytics/log-analytics-log-searches.md) para exibir dados detalhados de controle de alterações.
