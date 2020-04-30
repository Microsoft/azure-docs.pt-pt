---
title: Visão geral do rastreio de mudanças e inventário na automação azure
description: Alterar o Rastreio e O Inventário ajuda-o a identificar as alterações de software e de serviço da Microsoft que ocorrem no seu ambiente.
services: automation
ms.subservice: change-inventory-management
ms.date: 01/28/2019
ms.topic: conceptual
ms.openlocfilehash: 1208e08f7b85e893ba754bdbdf71a2da4f68c90a
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509075"
---
# <a name="overview-of-change-tracking-and-inventory"></a>Visão geral do rastreio e inventário de alterações

Este artigo apresenta-o ao Change Tracking and Inventory in Azure Automation. Esta funcionalidade rastreia alterações em máquinas virtuais e infraestrutura de servidores para ajudá-lo a identificar problemas operacionais e ambientais com software gerido pelo Gestor de Pacotes de Distribuição. Os itens que são rastreados por Change Tracking e Inventário incluem: 

- Software windows
- Software Linux (pacotes)
- Arquivos Windows e Linux
- Chaves de registo de janelas
- Serviços da Microsoft
- Daemons linux

Alterar rastreio e inventário obtém os seus dados do serviço Azure Monitor na nuvem. O Azure envia alterações ao software instalado, serviços microsoft, registo e ficheiros do Windows e daemons Linux em servidores monitorizados para o Monitor Azure para processamento. O serviço na nuvem aplica lógica aos dados recebidos, regista-os e disponibiliza-os. 

> [!NOTE]
> Para acompanhar as mudanças de propriedade do Gestor de Recursos Azure, consulte o histórico de [alterações](../governance/resource-graph/how-to/get-resource-changes.md)do Gráfico de Recursos Azure.

Alterar rastreio e inventário atualmente não suporta os seguintes itens:

* Recursion para rastreio de registo do Windows
* Sistemas de ficheiros de rede
* Diferentes métodos de instalação
* ***.exe** ficheiros para Windows

Outras limitações:

* A coluna max **file size** e os valores não são utilizados na implementação atual.
* Se recolher mais de 2500 ficheiros num ciclo de recolha de 30 minutos, o desempenho da solução poderá ser degradado.
* Quando o tráfego da rede é elevado, os registos de alteração podem demorar até seis horas a ser exibidos.
* Se modificar uma configuração enquanto um computador é desligado, o computador pode publicar alterações pertencentes à configuração anterior.

Change Tracking and Inventory está atualmente a ter os seguintes problemas:

* As atualizações hotfix não são recolhidas nas máquinas Core RS3 do Windows Server 2016.
* Os daemons linux podem mostrar um estado mudado, mesmo que não tenha ocorrido nenhuma mudança. Este problema surge devido à `SvcRunLevels` forma como os dados no registo de [Configuração](https://docs.microsoft.com/azure/azure-monitor/reference/tables/configurationchange) do Monitor Azure são capturados.

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

O Rastreio e Inventário de Alterações e os agentes Azure Monitor Log Analytics são suportados tanto nos sistemas operativos Windows como Linux.

### <a name="windows-operating-systems"></a>Sistemas operativos Windows

A versão do sistema operativo Windows que é suportada oficialmente é o Windows Server 2008 R2 ou mais tarde.

### <a name="linux-operating-systems"></a>Sistemas operativos Linux

As distribuições linux discutidas abaixo são oficialmente suportadas para o agente Log Analytics para linux. No entanto, o agente Linux também pode funcionar com outras distribuições não listadas. Salvo indicação em contrário, todas as versões menores são suportadas para cada versão principal listada.

#### <a name="64-bit-linux-operating-systems"></a>Sistemas operativos Linux de 64 bits

* CentOS 6 e 7
* Amazon Linux 2017.09
* Oracle Linux 6 e 7
* Red Hat Enterprise Linux Server 6 e 7
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS e 18.04 LTS
* SUSE Linux Enterprise Server 12

#### <a name="32-bit-linux-operating-systems"></a>Sistemas operativos Linux de 32 bits

* Centos 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14.04 LTS e 16.04 LTS

## <a name="network-requirements"></a>Requisitos da rede

Alterar o Rastreio e o Inventário requer especificamente os endereços de rede listados na tabela seguinte. As comunicações para estes endereços utilizam a porta 443.

|Azure Público  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|*.azure-automation.net|*.azure-automation.us|

## <a name="change-tracking-and-inventory-user-interface"></a>Alterar a interface de utilizador de rastreio e inventário

Utilize o Rastreio e O Inventário de Alterações no portal Azure para ver o resumo das alterações para computadores monitorizados. A funcionalidade está disponível selecionando o **rastreio de alteração** sob gestão de **configuração** na sua conta Deautomação. 

![Alterar painel de rastreio](./media/change-tracking/change-tracking-dash01.png)

As desistências estão disponíveis na parte superior do painel de instrumentos para limitar o gráfico de rastreio de alterações e informações detalhadas com base nos intervalos de tipo e tempo de mudança. Também pode clicar e arrastar na tabela para selecionar um intervalo de tempo personalizado. 

Pode clicar numa alteração ou evento para apresentar os seus detalhes. Os tipos de alterações disponíveis são:

* Eventos
* Daemons
* Ficheiros
* Registo
* Software
* Serviços da Microsoft

É capaz de adicionar, modificar ou remover cada alteração. No exemplo abaixo, pode ver uma alteração no tipo de arranque de um serviço de Manual para Auto.

![Alterar detalhes de rastreio](./media/change-tracking/change-tracking-details.png)

## <a name="tracking-of-file-changes"></a>Rastreio de alterações de ficheiros

Para rastrear alterações nos ficheiros tanto no Windows como no Linux, o Change Tracking e o Inventory utiliza hashes MD5 dos ficheiros. A funcionalidade utiliza os hashes para detetar se foram feitas alterações desde o último inventário.

## <a name="tracking-of-file-content-changes"></a>Rastreio das alterações de conteúdo de ficheiros

Alterar o Rastreio e o Inventário permite-lhe visualizar o conteúdo de um ficheiro Windows ou Linux antes e depois de uma alteração de ficheiro. Para cada alteração a um ficheiro, alterar o rastreio e o inventário armazena o conteúdo do ficheiro numa [conta de Armazenamento Azure](../storage/common/storage-create-storage-account.md). Quando estiver a seguir o ficheiro, pode ver o seu conteúdo antes ou depois de uma alteração. Pode ver o conteúdo dentro ou lado a lado. 

![Ver alterações num ficheiro](./media/change-tracking/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>Rastreio das chaves do registo

Alterar rastreio e inventário permite a monitorização das alterações às teclas de registo. A monitorização permite identificar pontos de exsensibilidade onde código e malware de terceiros podem ser ativados. As seguintes listas de tabelas pré-configuradas (mas não ativadas) chaves de registo. Para rastrear estas teclas, tem de ativar cada uma delas.

> [!div class="mx-tdBreakAll"]
> |Chave do Registo | Objetivo |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitoriza entradas automáticas comuns que se ligam diretamente ao Windows Explorer e geralmente são executadas em processo com **explorer.exe**.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Monitoriza scripts que funcionam no arranque.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Monitoriza os scripts que funcionam no encerramento.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Monitorize as teclas que são carregadas antes de o utilizador entrar na conta Do Windows. A chave é usada para aplicações de 32 bits em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Monitoriza alterações nas definições de aplicação.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Monitoriza entradas automáticas comuns que se ligam diretamente ao Windows Explorer e geralmente são executadas em processo com **explorer.exe**.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Monitoriza entradas automáticas comuns que se ligam diretamente ao Windows Explorer e geralmente são executadas em processo com **explorer.exe**.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitores para registo de manipulador de sobreposição de ícones.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Monitores para registo de manipulador de sobreposição de ícones para aplicações de 32 bits em execução em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitores para novos plugins de objetos de ajuda de navegador para o Internet Explorer. Utilizado para aceder ao Modelo de Objeto sinuoso (DOM) da página atual e para controlar a navegação.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Monitores para novos plugins de objetos de ajuda de navegador para o Internet Explorer. Utilizado para aceder ao Modelo de Objeto sinuoso (DOM) da página atual e para controlar a navegação para aplicações de 32 bits em execução em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Monitores para novas extensões do Internet Explorer, tais como menus de ferramentas personalizadas e botões de barra de ferramentas personalizados.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | Monitores para novas extensões do Internet Explorer, tais como menus de ferramentas personalizadas e botões de barra de ferramentas personalizados para aplicações de 32 bits em execução em computadores de 64 bits.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitores condutores de 32 bits associados a wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc. Semelhante à secção [dos condutores] no ficheiro **system.ini.**
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | Monitores condutores de 32 bits associados a wavemapper, wave1 e wave2, msacm.imaadpcm, .msadpcm, .msgsm610 e vidc para aplicações de 32 bits em execução em computadores de 64 bits. Semelhante à secção [dos condutores] no ficheiro **system.ini.**
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Monitoriza a lista de DLLs conhecidos ou comumente utilizados do sistema. Este sistema impede que as pessoas explorem permissões de diretório sinuosas, deixando cair em versões de cavalos de Troia de DLLs do sistema.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Monitoriza a lista de pacotes que podem receber notificações de eventos a partir de **winlogon.exe**, o modelo de suporte de logon interativo para Windows.

## <a name="support-for-file-integrity-monitoring-in-azure-security-center"></a>Suporte para monitorização da integridade de ficheiros no Centro de Segurança Azure

Alterar rastreio e inventário faz uso da Monitorização de Integridade do Ficheiro do Centro de [Segurança Azure (FIM)](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring). Enquanto o FIM monitoriza apenas ficheiros e registos, a funcionalidade completa de Rastreio e Inventário de Alterações também inclui rastreio para:

- Alterações de software
- Serviços da Microsoft
- Daemons linux

## <a name="recursion-support"></a>Apoio à recursão

Alterar rastreio e inventário suporta a recursição, o que lhe permite especificar wildcards para simplificar o rastreio entre diretórios. A recursion também fornece variáveis ambientais que lhe permitem rastrear ficheiros em ambientes com nomes de unidade múltiplos ou dinâmicos. A lista que se segue inclui informações comuns que deve saber ao configurar a recursão:

* Os wildcards são necessários para rastrear vários ficheiros.
* Os wildcards só podem ser utilizados no último segmento de um caminho, por exemplo, **c:\ficheiro de\\pasta*** ou **/etc/*.conf**.
* Se uma variável ambiental tem um caminho inválido, a validação é bem sucedida, mas o caminho falha durante a execução.
* Evite nomes de caminhos gerais ao definir o caminho, uma vez que este tipo de definição pode fazer com que muitas pastas sejam atravessadas.

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

O uso médio de dados do Log Analytics para uma máquina que utilize o Change Tracking and Inventory é de aproximadamente 40 MB por mês. Este valor é apenas uma aproximação e está sujeito a alterações com base no seu ambiente. Recomenda-se que monitorize o seu ambiente para ver o uso exato que tem.

### <a name="microsoft-service-data"></a>Dados do serviço da Microsoft

A frequência de recolha padrão dos serviços da Microsoft é de 30 minutos. Pode configurar a frequência utilizando um slider no separador de **serviços** da Microsoft em definições de **edição**. 

![Slider de serviços da Microsoft](./media/change-tracking/windowservices.png)

Para otimizar o desempenho, o agente Log Analytics apenas rastreia alterações. A fixação de um limiar elevado pode falhar alterações se o serviço reverter para o seu estado original. A definição da frequência para um valor menor permite-lhe apanhar alterações que podem ser perdidas de outra forma.

> [!NOTE]
> Enquanto o agente pode rastrear as alterações para um intervalo de 10 segundos, os dados ainda demoram alguns minutos a ser exibidos no portal Azure. As alterações que ocorrem durante o tempo de visualização no portal ainda são rastreadas e registadas.

## <a name="support-for-alerts-on-configuration-state"></a>Suporte para alertas sobre estado de configuração

Uma capacidade chave de Change Tracking and Inventory está a alertar sobre alterações ao estado de configuração do seu ambiente híbrido. Muitas ações úteis estão disponíveis para desencadear em resposta a alertas, por exemplo, ações sobre funções Azure, livros de automação, webhooks, e similares. Alertar sobre as alterações ao ficheiro **C:\windows\system32\drivers\etc\hosts** para uma máquina é uma boa aplicação de alertas para dados de Rastreio de Alterações e Inventário. Há muitos mais cenários para alertar também, incluindo os cenários de consulta definidos na tabela seguinte. 

|Consulta  |Descrição  |
|---------|---------|
|ConfiguraçãoChange <br>&#124; onde configChangeType == "Ficheiros" e\\FileSystemPath contém\\" c: controladores do sistema de janelas32\\\\"|Útil para rastrear alterações em ficheiros críticos do sistema.|
|ConfiguraçãoChange <br>&#124; onde FieldsChanged contém "FileContentChecksum" e FileSystemPath\\==\\\\"c:\\\\windows system32 drivers etc hosts"|Útil para rastrear modificações em ficheiros de configuração chave.|
|ConfiguraçãoChange <br>&#124; onde configChangeType == "Serviços Microsoft" e SvcName contém "w3svc" e SvcState == "Parou"|Útil para rastrear alterações nos serviços críticos do sistema.|
|ConfiguraçãoChange <br>&#124; onde ConfigChangeType == "Daemons" e SvcName contém "ssh" e SvcState!= "Running"|Útil para rastrear alterações nos serviços críticos do sistema.|
|ConfiguraçãoChange <br>&#124; onde configChangeType == "Software" e ChangeCategory == "Adicionado"|Útil para ambientes que precisam de configurações de software bloqueados.|
|ConfiguraçãoData <br>&#124; onde o SoftwareName contém "Monitoring Agent" e CurrentVersion!= "8.0.11081.0"|Útil para ver quais as máquinas que têm versão de software desatualizada ou não conforme instalada. Esta consulta relata o último estado de configuração relatado, mas não reporta alterações.|
|ConfiguraçãoChange <br>&#124; onde registryKey ==\\\\@"HKEY_LOCAL_MACHINE SOFTWARE Microsoft\\Windows\\CurrentVersion\\QualityCompat"| Útil para rastrear alterações em teclas antivírus cruciais.|
|ConfiguraçãoChange <br>&#124; onde o RegistoKey\\contém\\serviços\\\\de\\controlo\\partilhado de parâmetros de acesso partilhados do sistema de acesso partilhado HKEY_LOCAL_MACHINE do sistema de acesso partilhado do sistema de acesso ao sistema de acesso @".| Útil para rastrear alterações nas definições de firewall.|

## <a name="next-steps"></a>Passos seguintes

* Para trabalhar com o Change Tracking e Inventário nos seus livros de execução, consulte Gerir o Rastreio e O Inventário de [Alterações](change-tracking-file-contents.md).
* Para resolver erros com rastreio e inventário de alterações, consulte Rastreio e Inventário de Resolução de Resolução de [Problemas](automation-tutorial-troubleshoot-changes.md).
* Utilize as pesquisas de [registo nos registos do Monitor Azure](../log-analytics/log-analytics-log-searches.md) para visualizar dados de rastreio de alterações detalhadas.
