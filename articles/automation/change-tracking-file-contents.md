---
title: Gerir o Rastreio e Inventário de Mudanças na Automação Azure
description: Este artigo diz como usar Change Tracking and Inventory para rastrear o software e as alterações de serviço da Microsoft no seu ambiente.
services: automation
ms.subservice: change-inventory-management
ms.date: 06/15/2020
ms.topic: conceptual
ms.openlocfilehash: eab509e389c074232526aa93fcebb72f3bc986c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185607"
---
# <a name="manage-change-tracking-and-inventory"></a>Gerir o Controlo de Alterações e o Inventário

Quando adiciona um novo ficheiro ou chave de registo para rastrear, a Azure Automation permite-o para [o Rastreio e Inventário de Alterações.](change-tracking.md) Este artigo descreve como configurar o rastreio, rever os resultados de rastreio e lidar com alertas quando as alterações são detetadas.

Antes de utilizar os procedimentos deste artigo, certifique-se de que ativou o Rastreio e Inventário de Alterações nos seus VMs utilizando uma destas técnicas:

* [Ativar o Controlo de Alterações e Inventário a partir de uma conta de Automatização](automation-enable-changes-from-auto-acct.md)
* [Ativar o rastreio e inventário de alterações navegando no portal Azure](automation-enable-changes-from-browse.md)
* [Ativar o Controlo de alterações e Inventário a partir de um runbook](automation-enable-changes-from-runbook.md)
* [Ativar o Controlo de Alterações e Inventário a partir da VM do Azure](automation-enable-changes-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Limitar o âmbito de implantação

Alterar O Rastreio e o Inventário utiliza uma configuração de âmbito dentro do espaço de trabalho para direcionar os computadores para receber alterações. Para obter mais informações, consulte [o âmbito de rastreio de alterações de limite e o âmbito de implantação do inventário.](automation-scope-configurations-change-tracking.md)

## <a name="track-files"></a>Rastrear ficheiros

Pode utilizar o Change Tracking and Inventory para rastrear alterações em ficheiros e pastas/diretórios. Esta secção diz como configurar o rastreio de ficheiros no Windows e no Linux.

### <a name="configure-file-tracking-on-windows"></a>Configurar o rastreio de ficheiros no Windows

Utilize os seguintes passos para configurar o rastreio de ficheiros nos computadores Windows:

1. Na sua conta Demômes automática, selecione **'Alterar o rastreio** em **Gestão de Configuração'.** 
2. Clique **em Editar Definições** (o símbolo da engrenagem).
3. Na página de Configuração do Espaço de Trabalho, selecione **Ficheiros do Windows**e clique **em + Adicionar** para adicionar um novo ficheiro para rastrear.
4. No ficheiro 'Adicionar o Ficheiro do Windows' para alterar o painel de rastreio, introduza as informações para o ficheiro ou pasta para rastrear e clicar **em Guardar**. A tabela a seguir define as propriedades que pode usar para a informação.

    |Propriedade  |Descrição  |
    |---------|---------|
    |Ativado     | Verdade se a definição for aplicada, e falso de outra forma.        |
    |Nome do Item     | Nome amigável do ficheiro a ser rastreado.        |
    |Group     | Um nome de grupo para agrupar logicamente ficheiros.        |
    |Introduzir o Caminho     | O caminho para verificar o ficheiro, por exemplo, **c:\temp \\ \* .txt**. Também pode utilizar variáveis ambientais, tais `%winDir%\System32\\\*.*` como.       |
    |Tipo de Caminho     | O tipo de caminho. Os valores possíveis são Ficheiro e Pasta.        |    
    |Recursão     | É verdade que se a recursão for usada quando se procura que o item seja rastreado, e falso de outra forma.        |    
    |Carregar o conteúdo do ficheiro | Fiel ao upload de conteúdo de ficheiros em alterações rastreadas, e Falso de outra forma.|

5. Certifique-se de que especifica True for **Upload file content**. Esta definição permite o rastreio do conteúdo do ficheiro para a trajetória de ficheiro indicada.

### <a name="configure-file-tracking-on-linux"></a>Configurar o rastreio de ficheiros no Linux

Utilize os seguintes passos para configurar o rastreio de ficheiros nos computadores Linux:

1. Na sua conta Demômes automática, selecione **'Alterar o rastreio** em **Gestão de Configuração'.** 
2. Clique **em Editar Definições** (o símbolo da engrenagem).
3. Na página de Configuração do Espaço de Trabalho, selecione **Ficheiros Linux**e clique **em + Adicionar** para adicionar um novo ficheiro para rastrear.
4. No ficheiro Add Linux para change tracking, insira as informações para o ficheiro ou diretório para rastrear e clicar **em Guardar**. A tabela a seguir define as propriedades que pode usar para a informação.

    |Propriedade  |Descrição  |
    |---------|---------|
    |Ativado     | Verdade se a definição for aplicada, e falso de outra forma.        |
    |Nome do Item     | Nome amigável do ficheiro a ser rastreado.        |
    |Group     | Um nome de grupo para agrupar logicamente ficheiros.        |
    |Introduzir o Caminho     | O caminho para verificar o ficheiro, por exemplo, **/etc/*.conf**.       |
    |Tipo de Caminho     | O tipo de caminho. Os valores possíveis são Arquivo e Diretório.        |
    |Recursão     | É verdade que se a recursão for usada quando se procura que o item seja rastreado, e falso de outra forma.        |
    |Utilizar o Sudo     | Fiel ao uso de sudo ao verificar o item, e falso de outra forma.         |
    |Ligações     | Definição que determina como lidar com ligações simbólicas ao atravessar diretórios. Os valores possíveis são:<br> Ignore - Ignora ligações simbólicas e não inclui os ficheiros/diretórios referenciados.<br>Seguir - Segue os links simbólicos durante a recursão e inclui também os ficheiros/diretórios referenciados.<br>Gerir - Segue os links simbólicos e permite a alteração do conteúdo devolvido.<br>**Nota:** A opção Manage não é recomendada, uma vez que não suporta a recuperação de conteúdos de ficheiros.    |
    |Carregar o conteúdo do ficheiro | Fiel ao upload de conteúdo de ficheiros em alterações rastreadas, e Falso de outra forma. |

5. Certifique-se de que especifica True for **Upload file content**. Esta definição permite o rastreio do conteúdo do ficheiro para a trajetória de ficheiro indicada.

   ![Adicionar ficheiro Linux](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="track-file-contents"></a>Rastrear o conteúdo do ficheiro

O rastreio de conteúdos de ficheiros permite-lhe visualizar o conteúdo de um ficheiro antes e depois de uma alteração rastreada. A funcionalidade guarda o conteúdo do ficheiro numa [conta de armazenamento](../storage/common/storage-account-overview.md) depois de ocorrer cada alteração. Aqui estão algumas regras a seguir para rastrear o conteúdo do ficheiro:

* É necessária uma conta de armazenamento padrão utilizando o modelo de implementação do Gestor de Recursos para armazenar o conteúdo dos ficheiros. 
* Não utilize contas de armazenamento de modelos de implantação premium e clássicas. Consulte [as contas de armazenamento Azure](../storage/common/storage-account-create.md).
* Pode ligar a conta de armazenamento a apenas uma conta De Automação.
* [O Rastreio e Inventário](change-tracking.md) de Alteração devem ser ativados na sua conta de Automação.

### <a name="enable-tracking-for-file-content-changes"></a>Ativar o rastreio de alterações de conteúdo de ficheiros

Utilize as seguintes etapas para permitir o rastreio de alterações ao conteúdo do ficheiro:

1. No portal Azure, abra a sua conta Demôm automação e, em seguida, selecione **Alterar o rastreio** em **Gestão de Configuração**.
2. Clique **em Editar Definições** (o símbolo da engrenagem).
3. Selecione **O Conteúdo do Ficheiro** e clique em **Link**. Esta seleção abre a localização do conteúdo do add para o painel de rastreio de alterações.

   ![Adicionar localização de conteúdo](./media/change-tracking-file-contents/enable.png)

4. Selecione a conta de subscrição e armazenamento para utilizar para armazenar o conteúdo do ficheiro. 

5. Se pretender ativar o rastreio de conteúdos de ficheiros para todos os ficheiros existentes, selecione **On** for **Upload file content for all settings**. Pode alterar esta definição para cada caminho de ficheiro mais tarde.

   ![Definir conta de armazenamento](./media/change-tracking-file-contents/storage-account.png)

6. O Change Tracking and Inventory mostra a conta de armazenamento e os URIs de assinatura de acesso partilhado (SAS) quando permite o rastreio de alterações de conteúdo de ficheiros. As assinaturas expiram após 365 dias, e pode recriá-las clicando em **Regenerar.**

   ![Chaves da conta da lista](./media/change-tracking-file-contents/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>Ver o conteúdo de um ficheiro rastreado

Assim que o 'Tracking and Inventory' de alteração detetar uma alteração para um ficheiro rastreado, pode visualizar o conteúdo do ficheiro no painel 'Alterar detalhes'.  

![Alterações na lista](./media/change-tracking-file-contents/change-list.png)

1. No portal Azure, abra a sua conta Demôm automação e, em seguida, selecione **Alterar o rastreio** em **Gestão de Configuração**.

2. Escolha um ficheiro na lista de alterações e selecione **Ver Alterar o Conteúdo** do Ficheiro para ver o conteúdo do ficheiro. O painel de detalhes da alteração mostra-lhe o padrão antes e depois das informações de arquivo para cada propriedade.

   ![Alterar detalhes](./media/change-tracking-file-contents/change-details.png)

3. Está a ver o conteúdo do ficheiro numa vista lado a lado. Pode selecionar **Inline** para ver uma visão inline das alterações.

## <a name="track-registry-keys"></a>Chaves de registo de rastreá-lo

Utilize os seguintes passos para configurar o rastreio das chaves de registo nos computadores Windows:

1. No portal Azure, abra a sua conta Demôm automação e, em seguida, selecione **Alterar o rastreio** em **Gestão de Configuração**. 
2. Clique **em Editar Definições** (o símbolo da engrenagem).
3. Na página de Configuração do Espaço de Trabalho, selecione **Registo do Windows**.
4. Clique **+ Adicione** para adicionar uma nova chave de registo para rastrear.
5. No registo do Bloco de Adicionar para alterar o painel de rastreio, introduza as informações para a chave a seguir e clique em **Guardar**. A tabela a seguir define as propriedades que pode usar para a informação.

    |Propriedade  |Descrição  |
    |---------|---------|
    |Ativado     | Verdade se uma definição é aplicada, e falso de outra forma.        |
    |Nome do Item     | Nome amigável da chave de registo para rastrear.        |
    |Group     | Nome do grupo para agrupar logicamente chaves de registo.        |
    |Chave do Registo do Windows   | Nome chave com caminho, por `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup` exemplo, .      |

## <a name="search-logs-for-change-records"></a>Registos de pesquisa para registos de alteração

Pode fazer várias pesquisas contra os registos do Azure Monitor para alterar registos. Com a página de rastreio de Alteração aberta, clique em **Registar Analytics** para abrir a página 'Registar'. A tabela seguinte fornece pesquisas de registo de amostras para registos de alteração.

|Consulta  |Descrição  |
|---------|---------|
|`ConfigurationData`<br>&#124; `where ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"`<br>&#124; `where SvcState == "Stopped"`<br>&#124; `summarize arg_max(TimeGenerated, *) by SoftwareName, Computer`         | Mostra os registos de inventário mais recentes dos serviços da Microsoft que foram definidos para Auto, mas foram reportados como sendo parados. Os resultados estão limitados ao registo mais recente do nome e computador especificados.    |
|`ConfigurationChange`<br>&#124; `where ConfigChangeType == "Software" and ChangeCategory == "Removed"`<br>&#124; `order by TimeGenerated desc`|Mostra registos de alteração para software removido.|

## <a name="create-alerts-on-changes"></a>Criar alertas sobre alterações

O exemplo a seguir mostra que o ficheiro **c:\windows\system32\drivers\etc\hosts** foi modificado numa máquina. Este ficheiro é importante porque o Windows o utiliza para resolver os nomes dos anfitriões nos endereços IP. Esta operação tem precedência sobre o DNS, e pode resultar em problemas de conectividade. Também pode levar à reorientação do tráfego para sites maliciosos ou de outra forma perigosos.

![Gráfico mostrando a mudança de ficheiro dos anfitriões](./media/change-tracking-file-contents/changes.png)

Vamos usar este exemplo para discutir os passos para criar alertas sobre uma mudança.

1. Na sua conta Demômes automática, selecione **'Alterar o rastreio** em **Gestão de Configuração'** e, em seguida, selecione **'Registar Analytics'.** 
2. Na pesquisa de Logs, procure alterações de conteúdo no ficheiro dos **anfitriões** com a consulta `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"` . Esta consulta procura alterações de conteúdo para ficheiros com nomes de caminhos totalmente qualificados que contenham a palavra `hosts` . Também pode solicitar um ficheiro específico alterando a parte do caminho para a sua forma totalmente qualificada, por exemplo, utilizando `FileSystemPath == "c:\windows\system32\drivers\etc\hosts"` .

3. Após a consulta retornar os seus resultados, clique em **Nova regra de alerta** na pesquisa de registo para abrir a página de criação de alerta. Também pode navegar para esta página através do **Azure Monitor** no portal Azure. 

4. Verifique novamente a sua consulta e modifique a lógica de alerta. Neste caso, pretende que o alerta seja acionado se houver uma alteração detetada em todas as máquinas do ambiente.

    ![Alterar para consulta para rastrear alterações no ficheiro dos anfitriões](./media/change-tracking-file-contents/change-query.png)

5. Após a definição da lógica de alerta, atribua grupos de ação para executarem ações em resposta ao desencadeamento do alerta. Neste caso, estamos a configurar e-mails a serem enviados e um bilhete de Gestão de Serviços de TI (ITSM) a ser criado. 

    ![Configurar grupo de ação para alertar sobre a mudança](./media/change-tracking/action-groups.png)

## <a name="next-steps"></a>Passos seguintes

* Para obter informações sobre configurações de âmbito, consulte [o âmbito de rastreio de alterações de limite e o âmbito de implantação do inventário](automation-scope-configurations-change-tracking.md).
* Se necessitar de pesquisar registos armazenados no seu espaço de trabalho Log Analytics, consulte [as pesquisas de Registo nos registos do Azure Monitor](../azure-monitor/log-query/log-query-overview.md).
* Se terminar com as implementações, consulte [o espaço de trabalho Desvinculação da Conta de Automação para Change Tracking and Inventory](automation-unlink-workspace-change-tracking.md).
* Para eliminar os seus VMs de Change Tracking and Inventory, consulte [remover VMs do Change Tracking and Inventory](automation-remove-vms-from-change-tracking.md).
* Para resolver erros de funcionalidades, consulte [problemas de rastreio e inventário de alterações de resolução de problemas.](troubleshoot/change-tracking.md)
