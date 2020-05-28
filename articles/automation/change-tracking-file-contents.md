---
title: Gerir o rastreio e o inventário de alterações na automação azure
description: Este artigo diz como usar o Change Tracking e o Inventário para rastrear as mudanças de software e o serviço da Microsoft no seu ambiente.
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 6afa3d4d2d62541a51c3bab85843d41b48397100
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118780"
---
# <a name="manage-change-tracking-and-inventory"></a>Gerir o Controlo de Alterações e o Inventário

Quando adiciona uma nova chave de ficheiros ou registos para rastrear, a Automatização Azure permite-lhe rastreio e inventário de [alterações](change-tracking.md). Este artigo descreve como configurar o rastreio, rever os resultados do rastreio e lidar com alertas quando as alterações são detetadas.

Antes de utilizar os procedimentos neste artigo, certifique-se de que ativou o Rastreio e Inventário de Alterações nos seus VMs utilizando uma destas técnicas:

* [Ativar o Controlo de Alterações e Inventário a partir de uma conta de Automatização](automation-enable-changes-from-auto-acct.md)
* [Ativar o Rastreio e Inventário de Alterações navegando no portal Azure](automation-enable-changes-from-browse.md)
* [Ativar o Controlo de alterações e Inventário a partir de um runbook](automation-enable-changes-from-runbook.md)
* [Ativar o Controlo de Alterações e Inventário a partir da VM do Azure](automation-enable-changes-from-vm.md)

## <a name="track-files"></a>Rastrear ficheiros

Pode utilizar o Change Tracking e o Inventário para rastrear alterações em ficheiros e pastas/diretórios. Esta secção diz como configurar o rastreio de ficheiros no Windows e no Linux.

### <a name="configure-file-tracking-on-windows"></a>Configure o rastreio de ficheiros no Windows

Utilize os seguintes passos para configurar o rastreio de ficheiros nos computadores Windows:

1. Na sua conta de Automação, selecione **'Localizar rastreio sob** **Gestão de Configuração**.' 
2. Clique em **Definições de Edição** (o símbolo da engrenagem).
3. Na página de Configuração workspace, selecione **Ficheiros Windows**e, em seguida, clique **em + Adicionar** um novo ficheiro para rastrear.
4. No painel 'Adicionar Ficheiro Windows' para alterar, introduza as informações para o ficheiro ou pasta para rastrear e clicar em **Guardar**. A tabela a seguir define as propriedades que pode utilizar para a informação.

    |Propriedade  |Descrição  |
    |---------|---------|
    |Ativado     | É verdade se a definição for aplicada, e falsa de outra forma.        |
    |Nome do Item     | Nome amigável do ficheiro a ser rastreado.        |
    |Grupo     | Um nome de grupo para agrupar ficheiros logicamente.        |
    |Introduzir o Caminho     | O caminho para verificar o ficheiro, por exemplo, **c:\temp \\ \* .txt**. Também pode utilizar variáveis ambientais, tais como `%winDir%\System32\\\*.*` .       |
    |Tipo de Caminho     | O tipo de caminho. Os valores possíveis são Arquivo e Pasta.        |    
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
    |Ligações     | Definir isso determina como lidar com ligações simbólicas ao atravessar diretórios. Os valores possíveis são:<br> Ignore - Ignora links simbólicos e não inclui os ficheiros/diretórios referenciados.<br>Siga as ligações simbólicas durante a recursão e inclui também os ficheiros/diretórios referenciados.<br>Gerir - Segue as ligações simbólicas e permite a alteração do conteúdo devolvido.<br>**Nota:** A opção Gerir não é recomendada, uma vez que não suporta a recuperação de conteúdo de ficheiros.    |
    |Carregar conteúdo de ficheiro | Fiel ao upload do conteúdo do ficheiro em alterações rastreadas, e Falso de outra forma. |

5. Certifique-se de que especifica true for **Upload file content**. Esta definição permite o rastreio de conteúdo de ficheiros para o caminho de ficheiro indicado.

   ![Adicionar ficheiro Linux](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>Rastrear o conteúdo dos ficheiros

O rastreio de conteúdo de ficheiros permite-lhe visualizar o conteúdo de um ficheiro antes e depois de uma alteração rastreada. A funcionalidade guarda o conteúdo do ficheiro numa conta de [armazenamento](https://docs.microsoft.com/azure/storage/common/storage-account-overview) após cada alteração. Aqui estão algumas regras a seguir para rastrear conteúdos de ficheiros:

* É necessária uma conta de armazenamento padrão utilizando o modelo de implementação do Gestor de Recursos para armazenar conteúdo de ficheiros. 
* Não utilize contas de armazenamento de modelos de implementação premium e clássicas. Ver sobre as contas de [Armazenamento Azure](../storage/common/storage-create-storage-account.md).
* Pode ligar a conta de armazenamento a apenas uma conta de Automação.
* [Alterar rastreio e inventário](change-tracking.md) deve ser ativado na sua conta De automação.

### <a name="enable-tracking-for-file-content-changes"></a>Ativar o rastreio de alterações de conteúdo de ficheiros

Utilize os seguintes passos para permitir o rastreio de alterações ao conteúdo do ficheiro:

1. No portal Azure, abra a sua conta de Automação e, em seguida, selecione **o rastreio de alteração** sob gestão de **configuração**.
2. Clique em **Definições de Edição** (o símbolo da engrenagem).
3. Selecione **Conteúdo de Ficheiro** e clique em **Link**. Esta seleção abre o Add Content Location para o painel de rastreio de alterações.

   ![Adicionar localização de conteúdo](./media/change-tracking-file-contents/enable.png)

4. Selecione a conta de subscrição e armazenamento para armazenar o conteúdo do ficheiro. 

5. Se pretender ativar o rastreio de conteúdos de ficheiros para todos os ficheiros rastreados existentes, selecione o conteúdo do ficheiro **On** for **Upload para todas as definições**. Mais tarde, pode alterar esta definição para cada caminho de ficheiro.

   ![Definir conta de armazenamento](./media/change-tracking-file-contents/storage-account.png)

6. Alterar rastreio e inventário mostra conta de armazenamento e URIs de Assinatura de Acesso Partilhado (SAS) quando permite o rastreio de alteração de conteúdo de ficheiros. As assinaturas expiram após 365 dias, e pode recriá-las clicando em **Regenerar**.

   ![Lista de chaves de conta](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>Ver o conteúdo de um ficheiro rastreado

Assim que o Change Tracking and Inventory detetar uma alteração para um ficheiro rastreado, pode visualizar o conteúdo do ficheiro no painel de Dados de Alteração.  

![Alterações na lista](./media/change-tracking-file-contents/change-list.png)

1. No portal Azure, abra a sua conta de Automação e, em seguida, selecione **o rastreio de alteração** sob gestão de **configuração**.

2. Escolha um ficheiro na lista de alterações e selecione **Ver Alterações** de Conteúdo de Ficheiro para ver o conteúdo do ficheiro. O painel de detalhes da mudança mostra-lhe o padrão antes e depois de arquivar informações para cada propriedade.

   ![Alterar detalhes](./media/change-tracking-file-contents/change-details.png)

3. Estás a ver o conteúdo do ficheiro numa vista lado a lado. Pode selecionar **Inline** para ver uma visão inline das alterações.

## <a name="track-registry-keys"></a>Chaves de registo de rastreio

Utilize os seguintes passos para configurar o rastreio da chave de registo nos computadores Windows:

1. No portal Azure, abra a sua conta de Automação e, em seguida, selecione **o rastreio de alteração** sob gestão de **configuração**. 
2. Clique em **Definições de Edição** (o símbolo da engrenagem).
3. Na página de Configuração workspace, selecione **Registo do Windows**.
4. Clique **+ Adicione** para adicionar uma nova chave de registo para rastrear.
5. No painel adicionar registo do Windows para alterar o rastreio, introduza as informações para a tecla rastrear e, em seguida, clique em **Guardar**. A tabela a seguir define as propriedades que pode utilizar para a informação.

    |Propriedade  |Descrição  |
    |---------|---------|
    |Ativado     | É verdade se uma definição é aplicada, e falsa de outra forma.        |
    |Nome do Item     | Nome amigável da chave do registo para rastrear.        |
    |Grupo     | Nome de grupo para chaves de registo de agrupamento logicamente.        |
    |Chave do Registo do Windows   | Nome chave com caminho, por exemplo, `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup` .      |

## <a name="search-logs-for-change-records"></a>Pesquisar registos de registos de alteração

Pode fazer várias pesquisas contra os registos do Monitor Azure para alterar registos. Com a página de rastreio de alterar aberta, clique **em Log Analytics** para abrir a página De registos. A tabela seguinte fornece pesquisas de registo de amostras para registos de alteração.

|Consulta  |Descrição  |
|---------|---------|
|`ConfigurationData`<br>&#124;`where ConfigDataType == "Microsoft services" and SvcStartupType == "Auto"`<br>&#124;`where SvcState == "Stopped"`<br>&#124;`summarize arg_max(TimeGenerated, *) by SoftwareName, Computer`         | Mostra os registos de inventário mais recentes dos serviços da Microsoft que foram definidos para a Auto, mas foram reportados como sendo parados. Os resultados limitam-se ao registo mais recente do nome e computador especificados do software.    |
|`ConfigurationChange`<br>&#124;`where ConfigChangeType == "Software" and ChangeCategory == "Removed"`<br>&#124;`order by TimeGenerated desc`|Mostra alterações nos registos de software removido.|

## <a name="create-alerts-on-changes"></a>Criar alertas sobre alterações

O exemplo seguinte mostra que o ficheiro **c:\windows\system32\drivers\etc\hosts** foi modificado numa máquina. Este ficheiro é importante porque o Windows o utiliza para resolver os nomes dos anfitriões para endereços IP. Esta operação tem precedência sobre o DNS, e pode resultar em problemas de conectividade. Também pode levar à reorientação do tráfego para sites maliciosos ou de outra forma perigosos.

![Gráfico mostrando a mudança de ficheiro dos anfitriões](./media/change-tracking-file-contents/changes.png)

Vamos usar este exemplo para discutir os passos para criar alertas sobre uma mudança.

1. Na sua conta De automatização, selecione **'Localizar rastreio sob** gestão de **configuração'** e, em seguida, selecione **Log Analytics**. 
2. Na pesquisa de Registos, procure alterações de conteúdo no ficheiro dos **anfitriões** com a consulta `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` . Esta consulta procura alterações de conteúdo para ficheiros com nomes de caminho sinuosos que contenham a palavra `hosts` . Também pode solicitar um ficheiro específico alterando a parte do caminho para a sua forma totalmente qualificada, por exemplo, utilizando `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"` .

3. Depois da consulta devolver os seus resultados, clique em **Nova regra** de alerta na pesquisa de registo para abrir a página de criação de alerta. Também pode navegar para esta página através do **Monitor Azure** no portal Azure. 

4. Verifique novamente a sua consulta e modifique a lógica de alerta. Neste caso, quer que o alerta seja desencadeado se houver mesmo uma alteração detetada em todas as máquinas do ambiente.

    ![Alteração à consulta para rastrear alterações no ficheiro dos anfitriões](./media/change-tracking-file-contents/change-query.png)

5. Após a definição da lógica de alerta, atribua grupos de ação para realizar ações em resposta ao desencadeamento do alerta. Neste caso, estamos a criar e-mails a enviar e a criar um bilhete de Gestão de Serviços de TI (ITSM). 

    ![Configurar grupo de ação para alertar sobre a mudança](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>Próximos passos

* Se precisar de pesquisar registos armazenados no seu espaço de trabalho Log Analytics, consulte as pesquisas de [registo nos registos do Monitor Azure](../log-analytics/log-analytics-log-searches.md).
* Para resolver problemas de funcionalidades, consulte problemas de rastreio e inventário de alterações de resolução de [problemas.](troubleshoot/change-tracking.md)
