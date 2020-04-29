---
title: Restaurar VMs VMware com o Azure Backup Server
description: Utilize o Servidor de Backup Azure (MABS) para restaurar os VMware VMs em execução num servidor VMware vCenter/ESXi.
ms.topic: conceptual
ms.date: 08/18/2019
ms.openlocfilehash: ab2fb4f8f79fa5a664f5cb0ba1bb537c1df658c2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77212370"
---
# <a name="restore-vmware-virtual-machines"></a>Restaurar máquinas virtuais VMware

Este artigo explica como usar o Microsoft Azure Backup Server (MABS) para restaurar os pontos de recuperação VMware VM. Para uma visão geral sobre a utilização de MABS para recuperar dados, consulte [Recuperar dados protegidos](https://docs.microsoft.com/azure/backup/backup-azure-alternate-dpm-server). Na Consola de Administrador MABS, existem duas formas de encontrar dados recuperáveis - pesquisar ou navegar. Ao recuperar dados, pode ou não querer restaurar os dados ou um VM para o mesmo local. Por esta razão, o MABS suporta três opções de recuperação para backups VMware VM:

* **Recuperação original da localização (OLR)** - Utilize o OLR para restaurar um VM protegido à sua localização original. Só é possível restaurar um VM na sua localização original se não forem adicionados ou eliminados discos, uma vez que ocorreu a cópia de segurança. Se os discos tiverem sido adicionados ou eliminados, deve utilizar uma recuperação alternativa de localização.

* **Recuperação alternativa de localização (ALR)** - Quando o VM original estiver em falta, ou não quiser perturbar o VM original, recupere o VM para um local alternativo. Para recuperar um VM para um local alternativo, deve fornecer a localização de um hospedeiro ESXi, piscina de recursos, pasta e a loja de dados de armazenamento e caminho. Para ajudar a diferenciar o VM restaurado do VM original, o MABS anexa "-Recuperado" ao nome do VM.

* Recuperação individual da localização do **ficheiro (ILR)** - Se o VM protegido for um VM do Servidor do Windows, ficheiros/pastas individuais dentro do VM podem ser recuperados utilizando a capacidade ILR do MABS. Para recuperar ficheiros individuais, consulte o procedimento mais tarde neste artigo.

## <a name="restore-a-recovery-point"></a>Restaurar um ponto de recuperação

1. Na Consola de Administrador MABS, clique na visualização recovery.

2. Utilizando o painel de navegação, navegue ou filtro para encontrar o VM que pretende recuperar. Uma vez selecionada uma VM ou pasta, os pontos de recuperação para o painel mostram os pontos de recuperação disponíveis.

    ![Pontos de recuperação disponíveis](./media/restore-azure-backup-server-vmware/recovery-points.png)

3. Nos **pontos** de recuperação para campo, utilize os menus de calendário e drop-down para selecionar uma data em que foi tomado um ponto de recuperação. As datas de calendário em negrito têm pontos de recuperação disponíveis.

4. Na fita da ferramenta, clique em **Recuperar** para abrir o **Assistente de Recuperação**.

    ![Assistente de recuperação, seleção de recuperação de revisão](./media/restore-azure-backup-server-vmware/recovery-wizard.png)

5. Clique em **seguida** para avançar para o ecrã Deteção de Opções de **Recuperação.**

6. No ecrã **'Selecionar Opções** de Recuperação', se pretender ativar o estrangulamento da largura de banda da rede, clique em **Modificar**. Para deixar a rede a estrangular desativada, clique em **Next**. Não existem outras opções neste ecrã de assistentes disponíveis para VMware VMs. Se optar por modificar o acelerador de largura de banda da rede, no diálogo do acelerador, **selecione Ativar** o estrangulamento de utilização da largura de banda da rede para o ligar. Uma vez ativado, configure as **Definições** e **a Agenda de Trabalho**.

7. No ecrã **Select Recovery Type,** escolha se deve recuperar para a instância original, ou para um novo local, e clique em **Seguinte**.

     * Se escolher **recuperar para a instância original,** não precisa de fazer mais escolhas no assistente. Os dados relativos à instância original são utilizados.

     * Se escolher **recuperar como máquina virtual em qualquer anfitrião**, em seguida, no ecrã **'Destino Especificado',** forneça as informações para **o anfitrião ESXi, Pool de Recursos, Pasta** e **Caminho**.

      ![Selecionar Tipo de Recuperação](./media/restore-azure-backup-server-vmware/recovery-type.png)

8. No ecrã **Resumo,** reveja as definições e clique em **Recuperar** para iniciar o processo de recuperação. O ecrã do estado de **recuperação** mostra a progressão da operação de recuperação.

## <a name="restore-an-individual-file-from-a-vm"></a>Restaurar um ficheiro individual a partir de um VM

Pode restaurar ficheiros individuais a partir de um ponto de recuperação vm protegido. Esta funcionalidade só está disponível para VMs do Windows Server. Restaurar ficheiros individuais é semelhante ao restauro de todo o VM, exceto que você navega no VMDK e encontra o arquivo(s) que deseja, antes de iniciar o processo de recuperação. Para recuperar um ficheiro individual ou selecionar ficheiros a partir de um VM do Servidor do Windows:

>[!NOTE]
>Restaurar um ficheiro individual de um VM está disponível apenas para Windows VM e Pontos de Recuperação de Discos.

1. Na Consola de Administrador MABS, clique na **visualização recovery.**

2. Utilizando o painel **de navegação,** navegue ou filtro para encontrar o VM que pretende recuperar. Uma vez selecionada uma VM ou pasta, os pontos de recuperação para o painel mostram os pontos de recuperação disponíveis.

    ![Pontos de recuperação disponíveis](./media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

3. Nos **Pontos de Recuperação para:** painel, utilize o calendário para selecionar a data que contém os pontos de recuperação desejados. Dependendo da configuração da política de backup, as datas podem ter mais do que um ponto de recuperação. Depois de ter selecionado o dia em que o ponto de recuperação foi tomado, certifique-se de ter escolhido o tempo de **recuperação**correto. Se a data selecionada tiver vários pontos de recuperação, escolha o seu ponto de recuperação selecionando-o no menu de paragem do tempo de recuperação. Uma vez escolhido o ponto de recuperação, a lista de itens recuperáveis aparece no **Caminho:** pane.

4. Para encontrar os ficheiros que pretende recuperar, no painel **Path,** clique duas vezes no item na coluna de **itens Recuperável** para o abrir. Selecione o ficheiro, ficheiros ou pastas que pretende recuperar. Para selecionar vários itens, prima a tecla **CTRL** enquanto seleciona cada item. Utilize o painel **Caminho** para pesquisar a lista de ficheiros ou pastas que aparecem na coluna **Recoveryable Item.** **A lista de pesquisas abaixo** não procura em subpastas. Para pesquisar por subpastas, clique duas vezes na pasta. Utilize o botão **Up** para passar de uma pasta infantil para a pasta dos pais. Pode selecionar vários itens (ficheiros e pastas), mas devem estar na mesma pasta-mãe. Não é possível recuperar itens de várias pastas no mesmo trabalho de recuperação.

    ![Seleção de Recuperação de Avaliação](./media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

5. Quando tiver selecionado o (s) item(s) para recuperação, na fita de ferramenta siconsolado administrador, clique em **Recuperar** para abrir o **Assistente de Recuperação**. No Assistente de Recuperação, o ecrã de Seleção de **Recuperação** de Revisão mostra os itens selecionados a serem recuperados.

6. No ecrã **'Selecionar Opções** de Recuperação', se pretender ativar o estrangulamento da largura de banda da rede, clique em **Modificar**. Para deixar a rede a estrangular desativada, clique em **Next**. Não existem outras opções neste ecrã de assistentes disponíveis para VMware VMs. Se optar por modificar o acelerador de largura de banda da rede, no diálogo do acelerador, **selecione Ativar** o estrangulamento de utilização da largura de banda da rede para o ligar. Uma vez ativado, configure as **Definições** e **a Agenda de Trabalho**.
7. No ecrã **Select Recovery Type,** clique em **Seguinte**. Só pode recuperar os seus ficheiros ou pastas para uma pasta de rede.
8. No ecrã **'Especificar Destino',** clique em **Navegar** para encontrar uma localização de rede para os seus ficheiros ou pastas. O MABS cria uma pasta onde todos os itens recuperados são copiados. O nome da pasta tem o prefixo, MABS_day mês.ano. Quando seleciona um local para os ficheiros ou pastas recuperados, são fornecidos os detalhes para esse local (Destino, destino e espaço disponível).

    ![Especificar a localização para recuperar ficheiros](./media/restore-azure-backup-server-vmware/specify-destination.png)

9. No ecrã **'Especificar Opções de Recuperação',** escolha qual a definição de segurança a aplicar. Pode optar por modificar o estrangulamento da largura de banda da rede, mas o estrangulamento é desativado por defeito. Além disso, a Recuperação e **Notificação** **san** não estão ativadas.
10. No ecrã **Resumo,** reveja as definições e clique em **Recuperar** para iniciar o processo de recuperação. O ecrã do estado de **recuperação** mostra a progressão da operação de recuperação.

## <a name="next-steps"></a>Passos seguintes

Para problemas de resolução de problemas ao utilizar o Servidor de Backup Azure, reveja o guia de resolução de [problemas para o Servidor](./backup-azure-mabs-troubleshoot.md)de Backup Azure .
