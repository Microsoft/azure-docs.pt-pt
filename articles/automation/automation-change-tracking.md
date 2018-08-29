---
title: Registar alterações com a automatização do Azure
description: A solução de controlo de alterações ajuda-o a identificar o software e as alterações de serviço do Windows que ocorrem no seu ambiente.
services: automation
ms.service: automation
ms.component: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 08/27/2018
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8066612db20d1569920835a67d84b27d1b852e6e
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128131"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Controlar as alterações no seu ambiente com a solução de controlo de alterações

Este artigo ajuda-o a utilizar a solução de controlo de alterações para o identificar facilmente as alterações no seu ambiente. A solução controla as alterações ao software do Windows e Linux, Windows e Linux ficheiros, chaves de registo do Windows, serviços do Windows e dos daemons do Linux. Identificar as alterações de configuração pode ajudá-lo a localizar problemas operacionais.

Alterações ao software instalado, serviços do Windows, registro do Windows e arquivos e os daemons Linux nos servidores monitorizados são enviadas para o serviço do Log Analytics na cloud para processamento. Lógica é aplicada para os dados recebidos e o serviço em nuvem regista os dados. Ao utilizar as informações no dashboard do controlo de alterações, pode ver facilmente as alterações efetuadas na sua infraestrutura de servidor.

## <a name="supported-windows-operating-systems"></a>Sistemas de operativos do Windows

As seguintes versões do sistema operativo Windows são suportadas oficialmente para o agente do Windows:

* Windows Server 2008 Service Pack 1 (SP1) ou posterior
* Windows 7 SP1 e posterior.

## <a name="supported-linux-operating-systems"></a>Sistemas operativos Linux suportados

As seguintes distribuições de Linux são suportadas oficialmente. No entanto, o agente Linux também pode executar em outras distribuições não listadas. Salvo indicação em contrário, todas as versões secundárias são suportadas para cada versão principal listado.  

* Amazon Linux 2012.09 para 2015.09 (x86/x64)
* Linux centOS 5, 6 e 7 (x86/x64)  
* Oracle Linux 5, 6 e 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 e 7 (x86/x64)
* Debian GNU/Linux 6, 7 e 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 e 12 (x86/x64)

## <a name="enable-change-tracking-and-inventory"></a>Ativar o Controlo de Alterações e Inventário

Para iniciar o controle de alterações, terá de ativar a solução de controlo de alterações e inventário para a sua conta de automatização.

1. No portal do Azure, navegue até à sua conta de automatização
1. Selecione **controlo de alterações** sob **configuração**.
1. Selecione uma área de trabalho existente do Log analytics ou **criar nova área de trabalho** e clique em **ativar**.

Isto permite que a solução para a sua conta de automatização. A solução pode demorar até 15 minutos a ativar. A faixa azul notifica-o quando a solução está ativada. Navegue de volta para o **controlo de alterações** página para gerir a solução.

## <a name="configuring-change-tracking-and-inventory"></a>Configurar o controlo de alterações e inventário

Para saber como para integrar computadores para a solução visite: [soluções automatizadas de integração](automation-onboard-solutions-from-automation-account.md). Assim que tiver uma máquina a integração com a solução de controlo de alterações e inventário pode configurar os itens para controlar. Quando ativar um novo ficheiro ou a chave de registo para acompanhar, ele é habilitado para controlo de alterações e inventário.

Para controlar alterações em arquivos no Windows e Linux, são usados hashes MD5 dos ficheiros. Theses hashes, em seguida, são utilizados para detetar se uma alteração foi efetuada desde o último inventário.

### <a name="configure-linux-files-to-track"></a>Configurar ficheiros do Linux a controlar

Utilize os seguintes passos para configurar o controlo de ficheiros em computadores Linux:

1. Na sua conta de automatização, selecione **controlo de alterações** sob **gestão de configuração**. Clique em **editar definições de** (símbolo de engrenagem).
2. Sobre o **controlo de alterações** página, selecione **ficheiros do Linux**, em seguida, clique em **+ adicionar** para adicionar um novo ficheiro para controlar.
3. Sobre o **adicionar ficheiro do Linux para controlo de alterações**, introduza as informações para o ficheiro ou diretório a controlar e clique em **guardar**.

|Propriedade  |Descrição  |
|---------|---------|
|Ativado     | Determina se a configuração é aplicada.        |
|Nome do Item     | Nome amigável do ficheiro a ser monitorizado.        |
|Grupo     | Um nome de grupo para agrupar ficheiros logicamente.        |
|Introduzir o Caminho     | O caminho para verificar o ficheiro. Por exemplo: "/etc/*.conf"       |
|Tipo de Caminho     | Tipo de item a ser controladas valores possíveis são ficheiro e diretório.        |
|Recursão     | Determina se recursão é utilizada ao procurar o item a controlar.        |
|Utilizar o Sudo     | Esta definição determina se o sudo é utilizado ao verificar o item.         |
|Ligações     | Esta definição determina como as ligações simbólicas são processadas ao atravessar diretórios.<br> **Ignorar** - ignora as ligações simbólicas e não inclui os ficheiros/diretórios referenciados.<br>**Siga** - segue as ligações simbólicas durante a recursão e também inclui os ficheiros/diretórios referenciados.<br>**Gerir** - segue as ligações simbólicas e permite a alteração do conteúdo devolvido.     |
|Carregar conteúdo do ficheiro para todas as definições| Ativa ou desativa o carregamento de conteúdo do ficheiro em alterações registadas. Opções disponíveis: **Verdadeiro** ou **Falso**.|

> [!NOTE]
> A opção “Gerir” ligações não é recomendada. A obtenção de conteúdo do ficheiro não é suportada.

### <a name="configure-windows-files-to-track"></a>Configurar ficheiros do Windows para controlar

Utilize os seguintes passos para configurar arquivos de controle em computadores Windows:

1. Na sua conta de automatização, selecione **controlo de alterações** sob **gestão de configuração**. Clique em **editar definições de** (símbolo de engrenagem).
2. Na **controlo de alterações** página, selecione **ficheiros do Windows**, em seguida, clique em **+ adicionar** para adicionar um novo ficheiro para controlar.
3. Sobre o **adicionar ficheiro do Windows para controlo de alterações**, introduza as informações para o ficheiro controlar e clique em **guardar**.

|Propriedade  |Descrição  |
|---------|---------|
|Ativado     | Determina se a configuração é aplicada.        |
|Nome do Item     | Nome amigável do ficheiro a ser monitorizado.        |
|Grupo     | Um nome de grupo para agrupar ficheiros logicamente.        |
|Introduzir o Caminho     | O caminho para verificar o ficheiro, por exemplo: "c:\temp\\\*. txt"<br>Também pode utilizar variáveis de ambiente, tais como "%winDir%\System32\\\*. *"       |
|Recursão     | Determina se recursão é utilizada ao procurar o item a controlar.        |
|Carregar conteúdo do ficheiro para todas as definições| Ativa ou desativa o carregamento de conteúdo do ficheiro em alterações registadas. Opções disponíveis: **Verdadeiro** ou **Falso**.|

## <a name="wildcard-recursion-and-environment-settings"></a>Definições de caráter universal, recursão e ambiente

Recursão permite-lhe especificar carateres universais para simplificar o controle em diretórios e variáveis de ambiente para que possa controlar os ficheiros em ambientes com múltiplos ou dinâmico nomes da unidade. Segue-se uma lista de informações comuns que deve saber quando configurar a recursão:

* Carateres universais são necessários para vários ficheiros de controlo
* Se utilizar carateres universais, só pode ser utilizados no último segmento de um caminho. (por exemplo, C:\Folder\\**ficheiro** ou /etc/*.conf)
* Se uma variável de ambiente má neplatnou cestu, validação será concluída com êxito, mas esse caminho irão falhar quando o inventário é executado.
* Evitar caminhos gerais, tais como `c:\*.*` ao definir o caminho, como isso poderia resultar em demasiados pastas a ser percorridas.

## <a name="configure-file-content-tracking"></a>Configurar o controlo de conteúdo do ficheiro

Pode ver o conteúdo antes e após uma alteração de um ficheiro com o ficheiro de conteúdo controlo de alterações. Está disponível para os ficheiros do Windows e Linux, para cada alteração para o ficheiro, o conteúdo do arquivo é armazenada numa conta de armazenamento e mostra o arquivo antes e depois da alteração, embutidas ou lado pelo lado. Para obter mais informações, consulte [exibir o conteúdo de um ficheiro controlado](change-tracking-file-contents.md).

![Ver alterações num arquivo](./media/change-tracking-file-contents/view-file-changes.png)

### <a name="configure-windows-registry-keys-to-track"></a>Configurar chaves de registo do Windows para controlar

Utilize os seguintes passos para configurar o controlo de chave de registo em computadores Windows:

1. Na sua conta de automatização, selecione **controlo de alterações** sob **gestão de configuração**. Clique em **editar definições de** (símbolo de engrenagem).
2. Na **controlo de alterações** página, selecione **Registro do Windows**, em seguida, clique em **+ adicionar** para adicionar uma nova chave de registo para acompanhar.
3. Sobre o **adicionar registo do Windows para controlo de alterações**, introduza as informações da chave a controlar e clique em **guardar**.

|Propriedade  |Descrição  |
|---------|---------|
|Ativado     | Determina se a configuração é aplicada.        |
|Nome do Item     | Nome amigável do ficheiro a ser monitorizado.        |
|Grupo     | Um nome de grupo para agrupar ficheiros logicamente.        |
|Chave do Registo do Windows   | O caminho para verificar o ficheiro. Por exemplo: "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

## <a name="limitations"></a>Limitações

A solução de controlo de alterações não suporta atualmente os seguintes itens:

* Recursão para o registo do Windows de controlo
* Sistemas de ficheiros de rede

Outras limitações:

* O **tamanho máximo do ficheiro** coluna e os valores não são utilizados na implementação atual.
* Se recolher ficheiros de mais de 2500 no ciclo de coleção de 30 minutos, o desempenho da solução pode ser degradado.
* Quando o tráfego de rede é alto, os registos de alteração podem demorar até seis horas a apresentar.
* Se modificar a configuração, enquanto um computador é desligado, o computador poderá publicar as alterações que pertenciam a configuração anterior.

## <a name="known-issues"></a>Problemas Conhecidos

A solução de controlo de alterações está atualmente com os seguintes problemas:

* Atualizações de correção não são recolhidas para máquinas de atualização para criativos do Windows 10 e Windows Server 2016 Core RS3.

## <a name="change-tracking-data-collection-details"></a>Alterar detalhes de recolha de dados de controlo

A tabela seguinte mostra a frequência de recolha de dados para os tipos de alterações. Para cada tipo, o instantâneo do estado atual dos dados também é atualizado pelo menos a cada 24 horas:

| **Alterar tipo** | **Frequência** |
| --- | --- |
| Registo do Windows | 50 minutos |
| Ficheiro do Windows | 30 minutos |
| Ficheiro do Linux | 15 minutos |
| Serviços Windows | 10 segundos para 30 minutos</br> Predefinição: 30 minutos |
| Dos daemons do Linux | 5 minutos |
| Software do Windows | 30 minutos |
| Software do Linux | 5 minutos |

### <a name="windows-service-tracking"></a>Controlo de serviço do Windows

A frequência de recolha predefinido para os serviços do Windows é de 30 minutos. Para configurar a frequência, aceda a **controlo de alterações**. Sob **editar definições** no **serviços Windows** separador, existe um controlo de deslize que permite-lhe alterar a frequência de recolha para os serviços do Windows de mais depressa 10 segundos para, desde que 30 minutos. Mover a barra de controlo de deslize para a frequência que pretende e guarda-os automaticamente.

![Controlo de deslize de serviços de Windows](./media/automation-change-tracking/windowservices.png)

O agente apenas controla as alterações, esta ação otimiza o desempenho do agente. Ao definir um limiar demasiado elevado, as alterações poderão ser perdidas se o serviço foi revertida para o seu estado original. Definir a frequência para um valor menor permite-lhe capturar alterações que podem ser omitidas caso contrário.

> [!NOTE]
> Embora o agente pode registo de alterações para baixo para um segundo intervalo 10, os dados ainda demora alguns minutos a ser apresentado no portal. As alterações durante o período de tempo para apresentar no portal do ainda sejam rastreadas e com sessão iniciadas.
  
### <a name="registry-key-change-tracking"></a>Alteração de chaves de registo de controlo

A finalidade da monitorização das alterações para chaves de registo é identificar os pontos de extensibilidade onde podem ativar o código de terceiros e de software maligno. A lista seguinte mostra a lista de chaves do Registro previamente configurada. Estas chaves são configuradas, mas não ativadas. Para controlar estas chaves do registo, tem de ativar cada um deles.

> [!div class="mx-tdBreakAll"]
> |  |
> |---------|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitores comuns início automático entradas que se conectam diretamente para o Windows Explorer e, normalmente, a execução em processo com Explorer.exe.    |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Scripts de monitores que são executados na inicialização.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown**    |
|&nbsp;&nbsp;&nbsp;&nbsp;Scripts de monitores que são executados no desligamento.     |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitoriza as chaves que são carregadas antes do utilizador inicia a sessão na respetiva conta do Windows. A chave é utilizada para programas de 32 bits executados em computadores de 64 bits.    |
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Active Setup\Installed componentes**     |
|&nbsp;&nbsp;&nbsp;&nbsp;Monitores alterações às definições de aplicação.     |
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitores comuns início automático entradas que se conectam diretamente para o Windows Explorer e, normalmente, a execução em processo com Explorer.exe.|
> |**HKEY\_LOCAL\_MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitores comuns início automático entradas que se conectam diretamente para o Windows Explorer e, normalmente, a execução em processo com Explorer.exe.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitores para o ícone de sobreposição de registo do manipulador.|
|**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitores para o ícone de sobrepor o registro de Gerenciador para programas de 32 bits executados em computadores de 64 bits.|
> |**HKEY\_LOCAL\_objetos de programa auxiliar de MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitores para o novo plugins de objeto de programa auxiliar de navegador para o Internet Explorer. Usado para acessar o DOM Document Object Model () da página atual e para controlar a navegação.|
> |**HKEY\_LOCAL\_objetos de programa auxiliar de MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitores para o novo plugins de objeto de programa auxiliar de navegador para o Internet Explorer. Usado para acessar o DOM Document Object Model () da página atual e para controlar a navegação para programas de 32 bits executados em computadores de 64 bits.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitores para novas extensões do Internet Explorer, como menus de ferramenta personalizada e botões da barra de ferramentas personalizada.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitores para novas extensões do Internet Explorer, como menus de ferramenta personalizada e botões da barra de ferramentas personalizada para programas de 32 bits executados em computadores de 64 bits.|
> |**HKEY\_LOCAL\_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitoriza os controladores de 32 bits associados wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc. Semelhante à secção [drivers] no sistema. Ficheiro INI.|
> |**HKEY\_LOCAL\_MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitores para os controladores de 32 bits associados wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc para programas de 32 bits executados em computadores de 64 bits. Semelhante à secção [drivers] no sistema. Ficheiro INI.|
> |**HKEY\_LOCAL\_MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitoriza a lista de sistemas conhecidos ou usadas DLLs; Este sistema impede que pessoas explorando as permissões de diretório do aplicativo fraco soltando nas versões de cavalo de Tróia de DLLs do sistema.|
> |**HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify**|
|&nbsp;&nbsp;&nbsp;&nbsp;Monitoriza a lista de pacotes para receber notificações de eventos do Winlogon, o modelo de suporte de início de sessão interativo para o sistema operativo do Windows.|

## <a name="use-change-tracking"></a>Utilizar o controlo de alterações

Depois da solução está ativada, pode ver o resumo das alterações para os seus computadores monitorados, selecionando **controlo de alterações** sob **gestão de configuração** na conta de automatização.

Pode ver as alterações aos seus computadores e, em seguida, no teste de detalhes para cada evento. Listas pendentes estão disponíveis na parte superior do gráfico para limitar o gráfico e informações detalhadas com base em intervalos de tipo e a hora de alteração. Também pode clicar e arrastar no gráfico para selecionar um intervalo de tempo personalizado.

![imagem do dashboard de controlo de alterações](./media/automation-change-tracking/change-tracking-dash01.png)

Clicar numa alteração ou evento apresenta as informações detalhadas sobre essa alteração. Como pode ver no exemplo, o tipo de arranque do serviço foi alterado de Manual para automática.

![imagem dos detalhes de controlo de alterações](./media/automation-change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Registos de pesquisa

Além dos detalhes que são fornecidos no portal, pesquisas podem ser feitas com os registos. Com o **controlo de alterações** clique aberto, da página **do Log Analytics**, esta ação abre o **pesquisa de registos** página.

### <a name="sample-queries"></a>Amostras de consultas

A tabela seguinte disponibiliza pesquisas de registos de exemplo para alterar registos recolhidos por esta solução:

|Consulta  |Descrição  |
|---------|---------|
|ConfigurationData<br>&#124;onde ConfigDataType = = "WindowsServices" e SvcStartupType = = "Auto"<br>&#124;onde SvcState = = "Parada"<br>&#124;resumir arg_max(TimeGenerated, *) por SoftwareName, computador         | Mostra os registos de inventário de mais recentes para os serviços do Windows que estivesse definido para automático, mas foram comunicados como a ser parado<br>Os resultados estão limitados para o registo mais recente para essa SoftwareName e computador      |
|ConfigurationChange<br>&#124;onde ConfigChangeType = = "Software" e ChangeCategory = = "Removido"<br>&#124;Ordenar por TimeGenerated desc|Mostra os registos de alteração de software foi removido|

## <a name="next-steps"></a>Passos Seguintes

Visite o tutorial para saber mais sobre como utilizar a solução de controlo de alterações:

> [!div class="nextstepaction"]
> [Resolver problemas relacionados com alterações no seu ambiente](automation-tutorial-troubleshoot-changes.md)

* Uso [pesquisas de registos no Log Analytics](../log-analytics/log-analytics-log-searches.md) para ver dados de controlo de alterações detalhadas.
