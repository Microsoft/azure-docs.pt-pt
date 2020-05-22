---
title: Gerir o rastreio e o inventário de alterações na automação azure
description: Este artigo diz como usar o Change Tracking e o Inventário para rastrear as mudanças de software e o serviço da Microsoft no seu ambiente.
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 8e5ee8df1dfd250a6713d832bf176daecdaef7ea
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744395"
---
# <a name="manage-change-tracking-and-inventory"></a>Gerir o Controlo de Alterações e o Inventário

A Azure Automation permite a funcionalidade de rastreio e inventário de [alterações](change-tracking.md) para máquinas no seu ambiente. A funcionalidade rastreia e disponibiliza alterações nas teclas de registo, ficheiros, conteúdos e similares. Este artigo inclui procedimentos para trabalhar com esta funcionalidade.

## <a name="enable-the-full-change-tracking-and-inventory-feature"></a>Ativar a funcionalidade completa de rastreio e inventário de alterações

Se tiver ativado a Monitorização da Integridade do Ficheiro do Centro de [Segurança Azure (FIM),](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring)pode utilizar a funcionalidade completa de Rastreio e Inventário de Alterações para as suas máquinas, conforme descrito abaixo. As suas definições não são removidas por este processo.

> [!NOTE]
> Ativar a funcionalidade completa de rastreio e inventário de alterações pode causar encargos adicionais. Ver [Preços de Automação](https://azure.microsoft.com/pricing/details/automation/).

1. Retire a solução de monitorização navegando para o espaço de trabalho e localizando-a na [lista de soluções de monitorização instaladas](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions).
2. Clique no nome da solução para abrir a sua página sumária e, em seguida, clique em **Delete**, conforme detalhado em [Remover uma solução de monitorização](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution).
3. Para reativar o Rastreio e O Inventário de Alterações, navegue para a conta Desativação e selecione **'Change tracking'** ou **Inventário** sob gestão de **configuração**.
4. Escolha o espaço de trabalho e a conta de Automação do Log Analytics, confirme as definições do espaço de trabalho e clique em **Ativar**.

## <a name="enable-machines-for-change-tracking-and-inventory"></a><a name="onboard"></a>Ativar máquinas para rastreio e inventário de alterações

Para começar a rastrear alterações, deve ativar o Rastreio de Alterações e O Inventário na Automação Azure. Aqui estão as formas recomendadas e suportadas de ativar esta funcionalidade para as suas máquinas: 

* [Ativar a partir de uma máquina virtual](automation-onboard-solutions-from-vm.md)
* [Ativar a partir da navegação de várias máquinas](automation-onboard-solutions-from-browse.md)
* [Ativar a partir da sua conta Deautomação](automation-onboard-solutions-from-automation-account.md)
* [Ativar num livro de execução da Automação Azure](automation-onboard-solutions.md)

## <a name="track-files"></a>Rastrear ficheiros

### <a name="configure-file-tracking-on-windows"></a>Configure o rastreio de ficheiros no Windows

Utilize os seguintes passos para configurar o rastreio de ficheiros nos computadores Windows:

1. Na sua conta de Automação, selecione **'Localizar rastreio sob** **Gestão de Configuração**.' 
2. Clique em **Definições de Edição** (o símbolo da engrenagem).
3. Na página de Configuração workspace, selecione **Ficheiros Windows**e, em seguida, clique **em + Adicionar** um novo ficheiro para rastrear.
4. No painel adicionar windows para alterar, introduza as informações para o ficheiro rastrear e clicar em **Guardar**. A tabela a seguir define as propriedades que pode utilizar para a informação.

    |Propriedade  |Descrição  |
    |---------|---------|
    |Ativado     | É verdade se a definição for aplicada, e falsa de outra forma.        |
    |Nome do Item     | Nome amigável do ficheiro a ser rastreado.        |
    |Grupo     | Um nome de grupo para agrupar ficheiros logicamente.        |
    |Introduzir o Caminho     | O caminho para verificar o ficheiro, por exemplo, **c:\temp \\ \* .txt**. Também pode utilizar variáveis ambientais, tais como `%winDir%\System32\\\*.*` .       |
    |Tipo de Caminho     | O tipo de caminho. Os valores possíveis são File e Diretório.        |    
    |Recursão     | É verdade que se a recursion é usada quando se procura o item a ser rastreado, e Falso de outra forma.        |    
    |Carregar conteúdo de ficheiro | Fiel ao upload do conteúdo do ficheiro em alterações rastreadas, e Falso de outra forma.|

5. Certifique-se de que especifica true for **Upload file content**. Esta definição permite o rastreio de conteúdo de ficheiros para o caminho de ficheiro indicado.

### <a name="configure-file-tracking-on-linux"></a>Configure rastreio de ficheiros em Linux

Utilize os seguintes passos para configurar o rastreio de ficheiros nos computadores Linux:

1. Na sua conta de Automação, selecione **'Localizar rastreio sob** **Gestão de Configuração**.' 
2. Clique em **Definições de Edição** (o símbolo da engrenagem).
3. Na página de Configuração workspace, selecione **Ficheiros Linux**e, em seguida, clique **em adicionar** um novo ficheiro para rastrear.
4. No painel Add Linux para alterar o rastreio, introduza as informações para o ficheiro ou diretório para rastrear e clicar em **Guardar**. A tabela a seguir define as propriedades que pode utilizar para a informação.

    |Propriedade  |Descrição  |
    |---------|---------|
    |Ativado     | É verdade se a definição for aplicada, e falsa de outra forma.        |
    |Nome do Item     | Nome amigável do ficheiro a ser rastreado.        |
    |Grupo     | Um nome de grupo para agrupar ficheiros logicamente.        |
    |Introduzir o Caminho     | O caminho para verificar o ficheiro, por exemplo, **/etc/*.conf**.       |
    |Tipo de Caminho     | O tipo de caminho. Os valores possíveis são File e Diretório.        |
    |Recursão     | É verdade que se a recursion é usada quando se procura o item a ser rastreado, e Falso de outra forma.        |
    |Utilizar o Sudo     | É verdade que se utiliza o sudo quando se verifica o artigo, e falso de outra forma.         |
    |Ligações     | Definir isso determina como lidar com ligações simbólicas ao atravessar diretórios. Os valores possíveis são:<br> Ignore - Ignora links simbólicos e não inclui os ficheiros/diretórios referenciados.<br>Siga as ligações simbólicas durante a recursão e inclui também os ficheiros/diretórios referenciados.<br>Gerir - Segue as ligações simbólicas e permite alterar o conteúdo devolvido. **Nota** - Esta opção não é recomendada, uma vez que não suporta a recuperação de conteúdo de ficheiros.    |
    |Carregar conteúdo de ficheiro | Fiel ao upload do conteúdo do ficheiro em alterações rastreadas, e Falso de outra forma. |

5. Certifique-se de que especifica true for **Upload file content**. Esta definição permite o rastreio de conteúdo de ficheiros para o caminho de ficheiro indicado.

   ![Adicionar ficheiro Linux](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>Rastrear o conteúdo dos ficheiros

O rastreio de conteúdo de ficheiros permite-lhe visualizar o conteúdo de um ficheiro antes e depois de uma alteração rastreada. A funcionalidade guarda o conteúdo do ficheiro numa conta de armazenamento após cada alteração. Aqui estão algumas regras a seguir para rastrear conteúdos de ficheiros:

* É necessária uma conta de armazenamento padrão utilizando o modelo de implementação do Gestor de Recursos para armazenar conteúdo de ficheiros. 

* Não utilize contas de armazenamento de modelos de implementação premium e clássicas. Ver sobre as contas de [Armazenamento Azure](../storage/common/storage-create-storage-account.md).

* A conta de armazenamento que utiliza pode ser ligada apenas a uma conta De automatização.

* [Alterar o Rastreio e O Inventário](change-tracking.md) está ativado na sua conta De automação.

### <a name="enable-tracking-for-file-content-changes"></a>Ativar o rastreio de alterações de conteúdo de ficheiros

1. No portal Azure, abra a sua conta de Automação e, em seguida, selecione **o rastreio de alteração** sob gestão de **configuração**.
2. Clique em **Definições de Edição** (o símbolo da engrenagem).
3. Selecione **Conteúdo de Ficheiro** e clique em **Link**. Esta seleção abre o Add Content Location para o painel de rastreio de alterações.

   ![Ativar a localização do conteúdo](./media/change-tracking-file-contents/enable.png)

4. Selecione a conta de subscrição e armazenamento para armazenar o conteúdo do ficheiro. 

5. Se pretender ativar o rastreio de conteúdos de ficheiros para todos os ficheiros rastreados existentes, selecione o conteúdo do ficheiro **On** for **Upload para todas as definições**. Mais tarde, pode alterar esta definição para cada caminho de ficheiro.

   ![Definir conta de armazenamento](./media/change-tracking-file-contents/storage-account.png)

6. Alterar rastreio e inventário mostra conta de armazenamento e URIs de Assinatura de Acesso Partilhado (SAS) quando permite o rastreio de alteração de conteúdo de ficheiros. As assinaturas expiram após 365 dias, e pode recriá-las clicando em **Regenerar**.

   ![Lista de chaves de conta](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>Ver o conteúdo de um ficheiro rastreado

Assim que o Change Tracking and Inventory detetar uma alteração para um ficheiro rastreado, pode visualizar o conteúdo do ficheiro no painel de Dados de Alteração.  

![Alterações na lista](./media/change-tracking-file-contents/change-list.png)

1. No portal Azure, abra a sua conta de Automação e, em seguida, selecione **o rastreio de alteração** sob gestão de **configuração**.

2. Escolha um ficheiro na lista de alterações e selecione **Ver Alterações** de Conteúdo de Ficheiro para ver o conteúdo do ficheiro. O painel de Detalhes de Alteração mostra-lhe a informação padrão antes e depois do ficheiro.

   ![Alterar detalhes](./media/change-tracking-file-contents/change-details.png)

3. Estás a ver o conteúdo do ficheiro numa vista lado a lado. Pode selecionar **Inline** para ver uma visão inline das alterações.

## <a name="track-registry-keys"></a>Chaves de registo de rastreio

Utilize os seguintes passos para configurar o rastreio da chave de registo nos computadores Windows:

1. Na sua conta de Automação, selecione **'Localizar rastreio sob** **Gestão de Configuração**.' 
2. Clique em **Definições de Edição** (o símbolo da engrenagem).
3. Na página de Configuração workspace, selecione **Registo do Windows**.
4. Clique **+ Adicione** para adicionar uma nova chave de registo para rastrear.
5. No painel adicionar registo do Windows para alterar o rastreio, introduza as informações para a tecla rastrear e, em seguida, clique em **Guardar**. A tabela a seguir define as propriedades que pode utilizar para a informação.

    |Propriedade  |Descrição  |
    |---------|---------|
    |Ativado     | É verdade se uma definição é aplicada, e falsa de outra forma.        |
    |Nome do Item     | Nome amigável da chave do registo para rastrear.        |
    |Grupo     | Nome de grupo para chaves de registo de agrupamento logicamente.        |
    |Chave do Registo do Windows   | Nome chave com caminho, por exemplo, **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup**.      |

## <a name="search-logs-for-change-records"></a>Pesquisar registos de registos de alteração

Pode fazer várias pesquisas contra os registos do Monitor Azure para alterar registos. Com a página de rastreio de alterar aberta, clique **em Log Analytics** para abrir a página De registos. A tabela seguinte fornece pesquisas de registo de amostras para registos de alteração.

|Consulta  |Descrição  |
|---------|---------|
|ConfiguraçãoData<br>&#124; onde configDataType == "Serviços Microsoft" e SvcStartupType == "Auto"<br>&#124; onde svcState == "Parou"<br>&#124; resumir arg_max (TimeGenerated, *) por SoftwareName, Computador         | Mostra os registos de inventário mais recentes dos serviços da Microsoft que foram definidos para a Auto, mas foram reportados como sendo parados. Os resultados limitam-se ao registo mais recente do nome e computador especificados do software.    |
|ConfiguraçãoChange<br>&#124; onde configChangeType == "Software" e ChangeCategory == "Removido"<br>&#124; ordem por TimeGenerated desc|Mostra alterações nos registos de software removido.|

## <a name="create-alerts-on-changes"></a>Criar alertas sobre alterações

O exemplo seguinte mostra que o ficheiro **C:\windows\system32\drivers\etc\hosts** foi modificado numa máquina. Este ficheiro é importante porque o Windows o utiliza para resolver os nomes dos anfitriões para endereços IP. Esta operação tem precedência sobre o DNS, e pode resultar em problemas de conectividade. Também pode levar à reorientação do tráfego para sites maliciosos ou de outra forma perigosos.

![Um gráfico que mostra a mudança de ficheiro dos anfitriões](./media/change-tracking-file-contents/changes.png)

Vamos usar este exemplo para discutir os passos para criar alertas sobre uma mudança.

1. Na sua conta De automatização, selecione **'Localizar rastreio sob** gestão de **configuração'** e, em seguida, selecione **Log Analytics**. 
2. Na pesquisa de Registos, procure alterações de conteúdo no ficheiro dos **anfitriões** com a consulta `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` . Esta consulta procura uma alteração de conteúdo para ficheiros com um caminho totalmente qualificado contendo a palavra "anfitriões". Também pode solicitar um ficheiro específico alterando a parte do caminho para a sua forma totalmente qualificada, por exemplo, utilizando `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"` .

3. Depois da consulta devolver os resultados desejados, clique em **Nova regra** de alerta na pesquisa de registo para abrir a página de criação de alerta. Também pode navegar para esta página através do **Monitor Azure** no portal Azure. 

4. Verifique novamente a sua consulta e modifique a lógica de alerta. Neste caso, quer que o alerta seja desencadeado se houver mesmo uma alteração detetada em todas as máquinas do ambiente.

    ![Alteração à consulta para rastrear alterações no ficheiro dos anfitriões](./media/change-tracking-file-contents/change-query.png)

5. Após a definição da lógica de alerta, atribua grupos de ação para realizar ações em resposta ao alerta que está a ser desencadeado. Neste caso, estamos a criar e-mails a enviar e a criar um bilhete de Gestão de Serviços de TI (ITSM). 

    ![Configurar grupo de ação para alertar sobre a mudança](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>Passos seguintes

* [Alterar visão geral do rastreio e do inventário](change-tracking.md)
* [Problemas de mudança de mudança e problemas de inventário](troubleshoot/change-tracking.md)
* [Pesquisas de log nos registos do Monitor Azure](../log-analytics/log-analytics-log-searches.md)