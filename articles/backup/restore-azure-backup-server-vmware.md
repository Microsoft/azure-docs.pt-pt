---
title: Restaurar VMs VMware com o Azure Backup Server
description: Use Servidor de Backup do Azure (MABS) para restaurar VMs VMware em execução em um servidor VMware vCenter/ESXi.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 08/18/2019
ms.author: dacurwin
ms.openlocfilehash: 7d267fcd27a96c5ee7820a5d9fb73c6bbdb80695
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210141"
---
# <a name="restore-vmware-virtual-machines"></a>Restaurar máquinas virtuais VMware

Este artigo explica como usar o MABS (servidor de Backup do Microsoft Azure) para restaurar os pontos de recuperação de VM do VMware. Para obter uma visão geral sobre como usar o MABS para recuperar dados, consulte [recuperar dados protegidos](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server). No Console do Administrador MABS, há duas maneiras de localizar dados recuperáveis-pesquisa ou navegação. Ao recuperar dados, você pode ou não querer restaurar dados ou uma VM para o mesmo local. Por esse motivo, o MABS dá suporte a três opções de recuperação para backups de VM VMware:

• **OLR (recuperação de local original)** – use OLR para restaurar uma VM protegida para seu local original. Você poderá restaurar uma VM para seu local original somente se nenhum disco tiver sido adicionado ou excluído, desde que o backup ocorreu. Se discos tiverem sido adicionados ou excluídos, você deverá usar a recuperação de local alternativo.

• **ALR (recuperação de local alternativo)** -quando a VM original estiver ausente ou você não quiser perturbar a VM original, recupere a VM para um local alternativo. Para recuperar uma VM em um local alternativo, você deve fornecer o local de um host ESXi, um pool de recursos, uma pasta e o armazenamento e o caminho do armazenamento de repositório. Para ajudar a diferenciar a VM restaurada da VM original, MABS acrescenta "-Recovered" ao nome da VM.

• **ILR (recuperação de local de arquivo individual)** – se a VM protegida for uma VM do Windows Server, os arquivos/pastas individuais dentro da VM poderão ser recuperados usando o recurso ILR do mAbs. Para recuperar arquivos individuais, consulte o procedimento mais adiante neste artigo.


## <a name="restore-a-recovery-point"></a>Restaurar um ponto de recuperação

1.  No Console do Administrador MABS, clique em modo de exibição de recuperação.

2.  Usando o painel procurar, procure ou filtre para localizar a VM que você deseja recuperar. Depois de selecionar uma VM ou pasta, os pontos de recuperação para o painel exibem os pontos de recuperação disponíveis. 

    ![Pontos de recuperação disponíveis](./media/restore-azure-backup-server-vmware/recovery-points.png)

3.  No campo **pontos de recuperação para** , use os menus de calendário e suspenso para selecionar uma data em que um ponto de recuperação foi tirado. As datas do calendário em negrito têm pontos de recuperação disponíveis.

4.  Na faixa de ferramentas da ferramenta, clique em **recuperar** para abrir o **Assistente de recuperação**. 

    ![Assistente de recuperação, examinar a seleção de recuperação](./media/restore-azure-backup-server-vmware/recovery-wizard.png)

5.  Clique em **Avançar** para ir para a tela **especificar opções de recuperação** .

6.  Na tela **especificar opções de recuperação** , se você quiser habilitar a limitação de largura de banda de rede, clique em **Modificar**. Para deixar a limitação de rede desabilitada, clique em **Avançar**. Nenhuma outra opção nesta tela do assistente está disponível para VMs VMware. Se você optar por modificar o acelerador de largura de banda de rede, na caixa de diálogo limitação, selecione **habilitar limitação de uso de largura de banda** para ativá-la. Uma vez habilitado, defina as **configurações** e a **agenda de trabalho**.

7.  Na tela **Selecionar tipo de recuperação** , escolha se deseja recuperar na instância original ou em um novo local e clique em **Avançar**.

     * Se você escolher **recuperar na instância original**, não precisará fazer mais nenhuma opção no assistente. Os dados da instância original são usados.

    * Se você escolher **recuperar como máquina virtual em qualquer host**, na tela **especificar destino** , forneça as informações para **host ESXi, pool de recursos, pasta** e **caminho**. 

      ![Selecionar tipo de recuperação](./media/restore-azure-backup-server-vmware/recovery-type.png)

8.    Na tela **Resumo** , examine as configurações e clique em **recuperar** para iniciar o processo de recuperação. A tela **status da recuperação** mostra a progressão da operação de recuperação.

## <a name="restore-an-individual-file-from-a-vm"></a>Restaurar um arquivo individual de uma VM

Você pode restaurar arquivos individuais de um ponto de recuperação de VM protegido. Esse recurso só está disponível para VMs do Windows Server. A restauração de arquivos individuais é semelhante à restauração de toda a VM, exceto que você navega para o VMDK e localiza os arquivos desejados antes de iniciar o processo de recuperação. Para recuperar um arquivo individual ou selecionar arquivos de uma VM do Windows Server:

1.  No Console do Administrador MABS, clique em modo de exibição de **recuperação** .

2.  Usando o painel **procurar** , procure ou filtre para localizar a VM que você deseja recuperar. Depois de selecionar uma VM ou pasta, os pontos de recuperação para o painel exibem os pontos de recuperação disponíveis.

    ![Pontos de recuperação disponíveis](./media/restore-azure-backup-server-vmware/recovery-points.png)
 
3.  No painel **pontos de recuperação para:** , use o calendário para selecionar a data que contém os pontos de recuperação desejados. Dependendo de como a política de backup foi configurada, as datas podem ter mais de um ponto de recuperação. Depois de selecionar o dia em que o ponto de recuperação foi feito, verifique se você escolheu o **tempo de recuperação**correto. Se a data selecionada tiver vários pontos de recuperação, escolha o ponto de recuperação selecionando-o no menu suspenso tempo de recuperação. Depois de escolher o ponto de recuperação, a lista de itens recuperáveis aparece no painel **caminho:** .

4.  Para localizar os arquivos que você deseja recuperar, no painel **caminho** , clique duas vezes no item na coluna **Item recuperável** para abri-lo. Selecione o arquivo, os arquivos ou as pastas que você deseja recuperar. Para selecionar vários itens, pressione a tecla **Ctrl** enquanto seleciona cada item. Use o painel **caminho** para pesquisar a lista de arquivos ou pastas que aparecem na coluna **Item recuperável** . A **lista de pesquisa abaixo** não pesquisa em subpastas. Para pesquisar em subpastas, clique duas vezes na pasta. Use o botão para **cima** para mover de uma pasta filho para a pasta pai. Você pode selecionar vários itens (arquivos e pastas), mas eles devem estar na mesma pasta pai. Não é possível recuperar itens de várias pastas no mesmo trabalho de recuperação.

5.  Quando você tiver selecionado os itens para recuperação, na faixa de Console do Administrador da ferramenta, clique em **recuperar** para abrir o **Assistente de recuperação**. No assistente de recuperação, a tela revisar **seleção de recuperação** mostra os itens selecionados a serem recuperados. 
    ![Examinar a seleção de recuperação](./media/restore-azure-backup-server-vmware/review-recovery.png)

6.  Na tela **especificar opções de recuperação** , se você quiser habilitar a limitação de largura de banda de rede, clique em **Modificar**. Para deixar a limitação de rede desabilitada, clique em **Avançar**. Nenhuma outra opção nesta tela do assistente está disponível para VMs VMware. Se você optar por modificar o acelerador de largura de banda de rede, na caixa de diálogo limitação, selecione **habilitar limitação de uso de largura de banda** para ativá-la. Uma vez habilitado, defina as **configurações** e a **agenda de trabalho**.
7.  Na tela **Selecionar tipo de recuperação** , clique em **Avançar**. Você só pode recuperar seus arquivos ou pastas em uma pasta de rede.
8.  Na tela **especificar destino** , clique em **procurar** para localizar um local de rede para seus arquivos ou pastas. MABS cria uma pasta na qual todos os itens recuperados são copiados. O nome da pasta tem o prefixo MABS_day-mês-year. Quando você seleciona um local para os arquivos ou a pasta recuperados, os detalhes desse local (destino, caminho de destino e espaço disponível) são fornecidos. 

       ![Especificar o local para recuperar arquivos](./media/restore-azure-backup-server-vmware/specify-destination.png)

9.  Na tela **especificar opções de recuperação** , escolha qual configuração de segurança aplicar. Você pode optar por modificar a limitação do uso de largura de banda da rede, mas a limitação está desabilitada por padrão. Além disso, a recuperação e a **notificação** de **San** não estão habilitadas.
10. Na tela **Resumo** , examine as configurações e clique em **recuperar** para iniciar o processo de recuperação. A tela **status da recuperação** mostra a progressão da operação de recuperação.


## <a name="next-steps"></a>Passos Seguintes
Para solucionar problemas ao usar Servidor de Backup do Azure, consulte o [Guia de solução de problemas para servidor de backup do Azure](./backup-azure-mabs-troubleshoot.md).

