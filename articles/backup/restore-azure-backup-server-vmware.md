---
title: Restaurar VMs VMware com o Azure Backup Server
description: Utilize o Azure Backup Server (MABS) para restaurar vMware VMware em execução num servidor VMware vCenter/ESXi.
ms.topic: conceptual
ms.date: 08/18/2019
ms.openlocfilehash: b3f61aa828db39aeb11b1ce46a850d9a5b868653
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88263525"
---
# <a name="restore-vmware-virtual-machines"></a>Restaurar máquinas virtuais VMware

Este artigo explica como usar o Microsoft Azure Backup Server (MABS) para restaurar os pontos de recuperação de VMware VMware. Para obter uma visão geral sobre a utilização do MABS para recuperar dados, consulte [recuperar dados protegidos](./backup-azure-alternate-dpm-server.md). Na Consola de Administrador MABS, existem duas formas de encontrar dados recuperáveis - pesquisar ou navegar. Ao recuperar dados, pode ou não querer restaurar dados ou um VM no mesmo local. Por esta razão, o MABS suporta três opções de recuperação para cópias de segurança VMware VM:

* **Recuperação original da localização (OLR)** - Utilize o OLR para restaurar um VM protegido na sua localização original. Só pode restaurar um VM na sua localização original se não tiverem sido adicionados ou eliminados discos, uma vez que a cópia de segurança ocorreu. Se os discos tiverem sido adicionados ou eliminados, deve utilizar a recuperação alternativa da localização.

* **Recuperação de localização alternativa (ALR)** - Quando o VM original estiver em falta, ou não quiser perturbar o VM original, recupere o VM para um local alternativo. Para recuperar um VM para um local alternativo, deve fornecer a localização de um anfitrião ESXi, piscina de recursos, pasta e a loja de dados de armazenamento e caminho. Para ajudar a diferenciar o VM restaurado do VM original, o MABS anexa "-Recuperado" ao nome do VM.

* **Recuperação individual da localização do ficheiro (ILR)** - Se o VM protegido for um VM do Servidor do Windows, ficheiros/pastas individuais dentro do VM podem ser recuperados utilizando a capacidade ILR do MABS. Para recuperar ficheiros individuais, consulte o procedimento mais tarde neste artigo.

## <a name="restore-a-recovery-point"></a>Restaurar um ponto de recuperação

1. Na Consola de Administrador MABS, selecione **a vista recovery**.

2. Utilizando o painel de navegação, navegue ou filtre para encontrar o VM que pretende recuperar. Uma vez selecionado um VM ou uma pasta, os pontos de recuperação do painel de pontos mostram os pontos de recuperação disponíveis.

    ![Pontos de recuperação disponíveis](./media/restore-azure-backup-server-vmware/recovery-points.png)

3. Nos **pontos de recuperação para** o campo, utilize os menus de calendário e suspensos para selecionar uma data quando um ponto de recuperação foi tomado. As datas do calendário em negrito têm pontos de recuperação disponíveis.

4. Na fita da ferramenta, selecione **Recuperar** para abrir o **Assistente de Recuperação**.

    ![Assistente de recuperação, seleção de recuperação de revisão](./media/restore-azure-backup-server-vmware/recovery-wizard.png)

5. Selecione **Seguinte** para avançar para o ecrã **de Opções de Recuperação de Especificações.**

6. No ecrã **'Opções de Recuperação de Especificações',** se pretender ativar o estrangulamento da largura de banda da rede, selecione **Modificar**. Para deixar o estrangulamento da rede desativado, selecione **Next**. Não há outras opções neste ecrã de assistentes disponíveis para VMware VMs. Se optar por modificar o acelerador de largura de banda da rede, no diálogo de aceleração, selecione Ativar o **acelerador de utilização da largura de banda** da rede para o ligar. Uma vez ativado, configurar as **Definições** e a **Programação de Trabalho**.

7. No ecrã **Select Recovery Type,** escolha se deve recuperar para a instância original ou para um novo local. e selecione **Seguinte**.

     * Se escolher **Recuperar para instância original,** não precisa de fazer mais escolhas no assistente. Os dados relativos à instância original são utilizados.

     * Se escolher **Recuperar como máquina virtual em qualquer anfitrião,** em seguida, no ecrã De destino **especifique,** forneça as informações para **anfitrião ESXi, Pool de Recursos, Pasta** e **Caminho**.

      ![Selecione tipo de recuperação](./media/restore-azure-backup-server-vmware/recovery-type.png)

8. No ecrã **Resumo,** reveja as definições e selecione **Recuperar** para iniciar o processo de recuperação. O ecrã **de estado de recuperação** mostra a progressão da operação de recuperação.

## <a name="restore-an-individual-file-from-a-vm"></a>Restaurar um ficheiro individual a partir de um VM

Pode restaurar ficheiros individuais a partir de um ponto de recuperação de VM protegido. Esta funcionalidade só está disponível para VMs do Windows Server. Restaurar ficheiros individuais é semelhante ao restauro de todo o VM, exceto navegar no VMDK e encontrar os ficheiros que pretende, antes de iniciar o processo de recuperação. Para recuperar um ficheiro individual ou selecionar ficheiros a partir de um VM do Servidor do Windows:

>[!NOTE]
>Restaurar um ficheiro individual a partir de um VM está disponível apenas para Pontos de Recuperação do Windows VM e Discos.

1. Na Consola de Administrador MABS, selecione a vista **recovery.**

2. Utilizando o painel **de navegação,** navegue ou filtre para encontrar o VM que pretende recuperar. Uma vez selecionado um VM ou uma pasta, os **pontos de recuperação do painel** de pontos mostram os pontos de recuperação disponíveis.

    ![Painel "Pontos de recuperação para"](./media/restore-azure-backup-server-vmware/vmware-rp-disk.png)

3. Nos **Pontos de Recuperação para:** painel, utilize o calendário para selecionar a data que contém o ou os pontos de recuperação pretendidos. Dependendo da configuração da política de backup, as datas podem ter mais do que um ponto de recuperação. Depois de ter selecionado o dia em que o ponto de recuperação foi tomado, certifique-se de que escolheu o tempo correto **de recuperação**. Se a data selecionada tiver vários pontos de recuperação, escolha o seu ponto de recuperação selecionando-o no menu de entrega do tempo de recuperação. Uma vez escolhido o ponto de recuperação, a lista de itens recuperáveis aparece no **Caminho:** painel.

4. Para encontrar os ficheiros que pretende recuperar, no painel **Path,** clique duas vezes no item na coluna **de item recuperável** para o abrir. Selecione o ficheiro, ficheiros ou pastas que pretende recuperar. Para selecionar vários itens, prima a tecla **Ctrl** enquanto seleciona cada item. Utilize o painel **'Caminho'** para pesquisar a lista de ficheiros ou pastas que aparecem na coluna **Item Recuperável.** **A lista de pesquisa abaixo** não procura sub-dobradeiras. Para pesquisar através de sub-14, clique duas vezes na pasta. Utilize o botão **Up** para passar de uma pasta para crianças para a pasta dos pais. Pode selecionar vários itens (ficheiros e pastas), mas devem estar na mesma pasta dos pais. Não é possível recuperar itens de várias pastas no mesmo trabalho de recuperação.

    ![Seleção de Recuperação de Revisão](./media/restore-azure-backup-server-vmware/vmware-rp-disk-ilr-2.png)

5. Uma vez selecionado o(s) item(s) para recuperação, na fita da ferramenta Consola de Administrador, selecione **Recuperar** para abrir o **Assistente de Recuperação**. No Assistente de Recuperação, o ecrã **de Seleção de Recuperação** de Revisão mostra os itens selecionados a recuperar.

6. No ecrã **'Opções de Recuperação de Especificações',** se pretender ativar o estrangulamento da largura de banda da rede, selecione **Modificar**. Para deixar o estrangulamento da rede desativado, selecione **Next**. Não há outras opções neste ecrã de assistentes disponíveis para VMware VMs. Se optar por modificar o acelerador de largura de banda da rede, no diálogo de aceleração, selecione Ativar o **acelerador de utilização da largura de banda** da rede para o ligar. Uma vez ativado, configurar as **Definições** e a **Programação de Trabalho**.
7. No ecrã **Select Recovery Type,** selecione **Seguinte**. Só pode recuperar os ficheiros ou pastas para uma pasta de rede.
8. No ecrã **'Destino Específico',** selecione **Procurar** para encontrar uma localização de rede para os seus ficheiros ou pastas. O MABS cria uma pasta onde todos os itens recuperados são copiados. O nome da pasta tem o prefixo, MABS_day mês. Quando seleciona uma localização para os ficheiros ou pasta recuperados, são fornecidos os detalhes desse local (Destino, destino e espaço disponível).

    ![Especificar a localização para recuperar ficheiros](./media/restore-azure-backup-server-vmware/specify-destination.png)

9. No ecrã **'Especificar Opções de Recuperação',** escolha qual a definição de segurança a aplicar. Pode optar por modificar o estrangulamento de utilização da largura de banda da rede, mas o estrangulamento é desativado por defeito. Além disso, **a RECUPERAção** e **Notificação** da SAN não estão ativadas.
10. No ecrã **Resumo,** reveja as definições e selecione **Recuperar** para iniciar o processo de recuperação. O ecrã **de estado de recuperação** mostra a progressão da operação de recuperação.

## <a name="next-steps"></a>Passos seguintes

Para problemas de resolução de problemas ao utilizar o Azure Backup Server, reveja o [guia de resolução de problemas para o Servidor de Backup do Azure](./backup-azure-mabs-troubleshoot.md).
