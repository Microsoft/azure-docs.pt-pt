---
title: Voltar a proteger VMs do Azure para um site no local durante a recuperação após desastre de VMs de VMware e servidores físicos | Documentos da Microsoft
description: Após a ativação pós-falha para o Azure durante a recuperação após desastre de VMs de VMware e servidores físicos, Aprenda a reativação pós-falha do Azure para o site no local.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/12/2019
ms.author: mayg
ms.openlocfilehash: 4202d95b540efb98b526f8a8abd17da22a908ebe
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57771822"
---
# <a name="reprotect-and-fail-back-machines-to-an-on-premises-site-after-failover-to-azure"></a>Voltar a proteger e efetuar a ativação de back-computadores para um site no local após a ativação pós-falha para o Azure

Após [ativação pós-falha](site-recovery-failover.md) de VMs de VMware no local ou servidores físicos no Azure, é a primeira etapa da falha para seu site no local voltar a proteger as VMs do Azure que foram criadas durante a ativação pós-falha. Este artigo descreve como fazer isso. 

Para uma descrição geral, veja o seguinte vídeo sobre como fazer a ativação pós-falha do Azure para um site no local.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="before-you-begin"></a>Antes de começar

Se utilizou um modelo para criar as suas máquinas virtuais, certifique-se de que cada máquina virtual tem seu próprio UUID para os discos. Se UUID a máquina de virtual de locais entra em conflito com o UUID de destino principal porque ambos foram criadas a partir do mesmo modelo, a nova proteção falha. Implemente outro destino principal que não foi criado a partir do mesmo modelo. Tenha em atenção as seguintes informações:
- Se estiver a tentar a reativação pós-falha para um alternativo vCenter, certifique-se de que o seu novo vCenter e o servidor de destino principal são detetados. Um sintoma típico é que os arquivos de dados não estão acessíveis ou não são visíveis no **voltar a proteger** caixa de diálogo.
- Para replicar para o local, terá de uma política de reativação pós-falha. Esta política é criada automaticamente quando cria uma política de frente. Tenha em atenção as seguintes informações:
    - Esta política é automaticamente associado ao servidor de configuração durante a criação.
    - Esta política não é editável.
    - Os valores do conjunto da política são (limiar de RPO = 15 minutos, a retenção do ponto de recuperação = 24 horas, a frequência de instantâneo de consistência de aplicação = 60 minutos).
- Durante a reproteção e reativação pós-falha, o servidor de configuração no local tem de ser em execução e ligado.
- Se um servidor vCenter gere as máquinas virtuais para o qual irá efetuar a reativação pós-falha, certifique-se de que tem o [permissões obrigatórias](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) para a deteção de VMs nos servidores vCenter.
- Elimine instantâneos no servidor de destino principal antes de voltar a proteger. Se os instantâneos estão presentes no destino mestre no local ou na máquina virtual, a nova proteção falha. Os instantâneos da máquina virtual são mesclados automaticamente durante uma tarefa de reproteção.
- Todas as máquinas de virtuais de um grupo de replicação tem de ser do mesmo tipo de sistema operativo (todos os Windows ou Linux todos). Um grupo de replicação com sistemas de operativos mistos atualmente não é suportado para voltar a proteger e a reativação pós-falha para o local. Isto acontece porque o destino principal tem de ser do mesmo sistema operativo que a máquina virtual. Todas as máquinas de virtuais de um grupo de replicação tem de ter o mesmo destino mestre. 
- Um servidor de configuração é necessário no local quando efetuar a reativação pós-falha. Durante a reativação pós-falha, a máquina virtual tem de existir na base de dados de servidor de configuração. Caso contrário, a reativação pós-falha não for bem-sucedida. Certifique-se de que faça cópias de segurança agendadas regularmente de seu servidor de configuração. Se houver um desastre, restaure o servidor com o mesmo endereço IP para que funcione de reativação pós-falha.
- Reproteção e reativação pós-falha necessitam de uma VPN de site a site (S2S) replicar dados. Fornece a rede para que as máquinas de virtuais com ativação pós-falha no Azure pode alcançar o servidor de (ping) a configuração no local. Pode também querer implementar um servidor de processos na rede da máquina virtual com ativação pós-falha do Azure. Este servidor de processos também tem de ser capaz de comunicar com o servidor de configuração no local.
- Certifique-se de que abre as portas seguintes para ativação pós-falha e reativação pós-falha:

    ![Portas para a ativação pós-falha e reativação pós-falha](./media/vmware-azure-reprotect/failover-failback.png)

- Ler todos os [pré-requisitos de portas e listas de permissões de URL](vmware-azure-deploy-configuration-server.md#prerequisites).

## <a name="deploy-a-process-server-in-azure"></a>Implementar um servidor de processos no Azure

Poderá ter um servidor de processos no Azure antes de reativação pós-falha para o seu site no local:
- O servidor de processos recebe dados de máquina virtual protegida no Azure e, em seguida, envia dados para o site no local.
- Uma rede de baixa latência é necessária entre o servidor de processos e a máquina virtual protegida. Em geral, precisa considerar a latência ao decidir se precisa de um servidor de processos no Azure:
    - Se tiver uma ligação de ExpressRoute do Azure, configurar, pode utilizar um servidor de processos no local para enviar dados uma vez que a latência entre a máquina virtual e o servidor de processos é baixa.
    - No entanto, se tiver apenas uma VPN S2S, recomendamos que implementar o servidor de processos no Azure.
    - Recomendamos que utilize um servidor de processos baseado no Azure durante a reativação pós-falha. O desempenho de replicação é superior se o servidor de processos está mais próximo para a máquina virtual em replicação (a máquina de efetuada a ativação pós-falha no Azure). Para uma prova de conceito, pode utilizar o servidor de processos no local e o ExpressRoute com peering privado.

Para implementar um servidor de processos no Azure:

1. Se precisar de implementar um servidor de processos no Azure, veja [configurar um servidor de processos no Azure para reativação pós-falha](vmware-azure-set-up-process-server-azure.md).
2. As VMs do Azure envie dados de replicação para o servidor de processos. Configure redes para que as VMs do Azure podem aceder ao servidor de processo.
3. Lembre-se de que a replicação do Azure para o local pode ocorrer apenas através de S2S VPN ou através do peering privado da sua rede do ExpressRoute. Certifique-se de que está disponível a largura de banda suficiente sobre esse canal de rede.

## <a name="deploy-a-separate-master-target-server"></a>Implementar um servidor de destino principal separado

O servidor de destino mestre recebe dados da reativação pós-falha. Por predefinição, o servidor de destino mestre é executado no servidor de configuração no local. No entanto, dependendo do volume de tráfego de repetição de falha, poderá ter de criar um servidor de destino principal separado para reativação pós-falha. Eis como criar um:

* [Criar um servidor de destino mestre Linux](vmware-azure-install-linux-master-target.md) para reativação pós-falha de VMs do Linux. Isto é necessário. Tenha em atenção que, principal do servidor de destino no LVM não é suportado.
* Opcionalmente, crie um servidor de destino principal separado para reativação pós-falha da VM do Windows. Para tal, execute novamente a configuração unificada e selecione para criar um servidor de destino mestre. [Saiba mais](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers). 

Depois de criar um servidor de destino mestre, efetue as seguintes tarefas:

- Se a máquina virtual está presente no local no vCenter server, o servidor de destino mestre tem acesso ao ficheiro de disco de Máquina Virtual (VMDK) da máquina de virtual no local. É necessário acesso para escrever os dados replicados para os discos da máquina virtual. Certifique-se de que o arquivo de dados da máquina de virtual no local está montado no anfitrião de destino mestre com acesso de leitura/escrita.
- Se a máquina virtual não estiver presente no local no vCenter server, o serviço Site Recovery tem de criar uma nova máquina virtual durante a nova proteção. Esta máquina virtual é criada no anfitrião ESX no qual criou o destino principal. Escolha o anfitrião do ESX com cuidado, para que a máquina de virtual de reativação pós-falha é criada no anfitrião que pretende.
- Não é possível utilizar o Storage vMotion para o servidor de destino mestre. Utilizar o armazenamento vMotion para o servidor de destino mestre poderá provocar a reativação pós-falha efetuar a ativação. Não é possível iniciar a máquina virtual porque os discos não estão disponíveis para ele. Para impedir que isso ocorra, exclua os servidores de destino mestre da sua lista do vMotion.
- Se um destino mestre passar por uma tarefa de vMotion de armazenamento após a nova proteção, os discos de máquina virtual protegida que estão anexados ao destino principal migrar para o destino da tarefa vMotion. Se tentar novamente após a isso, a Desanexação do disco falha porque os discos não forem encontrados. Em seguida, fica difícil encontrar os discos nas contas de armazenamento. Terá de encontrá-los manualmente e anexe-os para a máquina virtual. Depois disso, pode arrancar a máquina virtual no local.
- Adicione uma unidade de retenção para o servidor de destino principal do Windows existente. Adicione um novo disco e formatar o disco. A unidade de retenção é utilizada para parar os pontos no tempo quando a máquina virtual são replicados para o site no local. Seguem-se alguns critérios de uma unidade de retenção. Se esses critérios não forem cumpridos, a unidade não estiver listada para o servidor de destino principal:
    - O volume não é utilizado para qualquer outra finalidade, como o destino de replicação.
    - O volume não está no modo de bloqueio.
    - O volume não é um volume de cache. A instalação de destino principal não pode existir nesse volume. O volume de instalação personalizada para o destino de servidor e o mestre de processo não é elegível para um volume de retenção. Quando o servidor de processos e o destino mestre estão instalados num volume, o volume é um volume de cache de destino mestre.
    - O tipo de sistema de ficheiros do volume não é FAT ou FAT32.
    - A capacidade do volume é diferente de zero.
    - O volume de retenção predefinido para o Windows é R volume.
    - O volume de retenção predefinido para Linux é /Mnt/retention.
- Tem de adicionar uma nova unidade se estiver a utilizar uma máquina de servidor de configuração do servidor de processo existente ou uma máquina de servidor do destino do servidor/principal dimensionamento ou processo. A nova unidade tem de cumprir os requisitos anteriores. Se a unidade de retenção não estiver presente, não aparece na lista pendente de seleção no portal. Depois de adicionar uma unidade ao destino principal no local, demora até 15 minutos para a unidade a aparecer na seleção no portal. Também pode atualizar o servidor de configuração se a unidade não aparecer após 15 minutos.
- Instale ferramentas VMware ou ferramentas da vm aberto no servidor de destino mestre. Sem as ferramentas, não não possível detetar os arquivos de dados no anfitrião do ESXi o destino principal.
- Definir o `disk.EnableUUID=true` definir os parâmetros de configuração da máquina de virtual de destino mestre no VMware. Se não existir esta linha, adicioná-lo. Esta definição é necessária para fornecer um UUID consistente para o VMDK para que ele monta corretamente.
- O anfitrião do ESX no qual é criado o destino principal tem de ter, pelo menos, uma máquina virtual ficheiro sistema (VMFS) arquivo de dados ligado ao mesmo. Se os arquivos de dados não VMFS estão ligados, o **arquivo de dados** entrada na página de reproteção está vazia e não pode continuar.
- O servidor de destino principal não pode ter instantâneos nos discos. Se existirem instantâneos, reproteção e reativação pós-falha falharem.
- O destino principal não pode ter um controlador Paravirtual SCSI. O controlador só pode ser um controlador de LSI Logic. Sem um controlador de LSI Logic nova proteção falha.
- Para qualquer instância, o destino principal pode ter no máximo, 60 discos ligados ao mesmo. Se o número de máquinas virtuais que está a ser protegida novamente ao destino principal no local tem mais do que um número total de 60 discos, reprotects ao destino principal começam a falhar. Certifique-se de que tem suficiente mestre blocos de disco de destino ou implementar servidores de destino mestre adicionais.
    

## <a name="enable-reprotection"></a>Ativar a nova proteção

Depois de uma máquina virtual é inicializado no Azure, demora algum tempo para o agente registar novamente para o servidor de configuração (até 15 minutos). Durante este período, não poderá voltar a proteger e uma mensagem de erro indica que o agente não está instalado. Se isto acontecer, aguarde alguns minutos e, em seguida, tente nova proteção novamente:


1. Selecione **cofre** > **itens replicados**. A máquina virtual que a ativação pós-falha com o botão direito e, em seguida, selecione **voltar a proteger**. Em alternativa, os botões de comando, selecione a máquina e, em seguida, selecione **voltar a proteger**.
2. Certifique-se de que o **do Azure para o local** direção de proteção está selecionada.
3. Na **servidor de destino mestre** e **servidor de processos**, selecione o servidor de destino mestre no local e o servidor de processos.  
4. Para **arquivo de dados**, selecione o arquivo de dados para o qual pretende recuperar os discos no local. Esta opção é utilizada quando a máquina de virtual no local é eliminada e tem de criar novos discos. Esta opção é ignorada se os discos já existirem. Ainda tem de especificar um valor.
5. Selecione a unidade de retenção.
6. A política de reativação pós-falha é selecionada automaticamente.
7. Selecione **OK** para começar a nova proteção. Um trabalho começa a replicar a máquina virtual do Azure para o site no local. Pode acompanhar o progresso no separador **Trabalhos**. Quando a nova proteção for bem-sucedida, o da máquina virtual entra num estado protegido.

Tenha em atenção as seguintes informações:
- Se pretender recuperar para uma localização alternativa (quando a máquina de virtual no local é eliminada), selecione a unidade de retenção e arquivo de dados que estão configurados para o servidor de destino mestre. Quando realizar a ativação pós-falha para o site no local, as máquinas de virtuais de VMware no plano de proteção de reativação pós-falha utilizar o mesmo arquivo de dados que o servidor de destino mestre. Uma nova máquina virtual, em seguida, é criada no vCenter.
- Se quiser recuperar a máquina virtual no Azure para uma máquina de virtual no local existente, Monte o arquivos de dados da máquina de virtual no local com acesso de leitura/escrita no anfitrião ESXi do servidor de destino mestre.

    ![Voltar a proteger a caixa de diálogo](./media/vmware-azure-reprotect/reprotectinputs.png)

- Pode também voltar a proteger ao nível de um plano de recuperação. Um grupo de replicação pode ser protegida novamente apenas por meio de um plano de recuperação. Quando voltar a proteger ao utilizar um plano de recuperação, tem de fornecer os valores para cada máquina protegida.
- Utilize o mesmo servidor de destino mestre para voltar a proteger um grupo de replicação. Se utilizar um servidor de destino mestre diferente para voltar a proteger um grupo de replicação, o servidor não pode fornecer um ponto comum no tempo.
- A máquina de virtual no local é desativada durante a nova proteção. Desta forma, garante-se a consistência dos dados durante a replicação. Não ative a máquina virtual após a conclusão.



## <a name="common-issues"></a>Problemas comuns

- Se executar uma deteção do vCenter de utilizador só de leitura e protege as máquinas virtuais, proteção com êxito e a ativação pós-falha funciona. Durante a nova proteção, a ativação pós-falha não consegue porque os arquivos de dados não puderem ser detetados. Um sintoma é que os arquivos de dados não estão listados durante a nova proteção. Para resolver este problema, pode atualizar as credenciais do vCenter com uma conta apropriada que tenha permissões e, em seguida, repita a tarefa. 
- Quando uma máquina virtual Linux a reativação pós-falha e a executa no local, pode ver que o pacote do Gestor de rede foi desinstalado da máquina. Esta desinstalação ocorre porque o pacote do Gestor de rede é removido quando a máquina virtual é recuperada no Azure.
- Quando uma máquina virtual Linux está configurada com um endereço IP estático e é a ativação pós-falha para o Azure, o endereço IP é obtido do DHCP. Quando efetuar a ativação pós-falha no local, a máquina virtual continua a utilizar DHCP para adquirir o endereço IP. Iniciar sessão manualmente máquina e, em seguida, defina o endereço IP para um endereço estático, se necessário. Uma máquina virtual do Windows pode adquirir novamente o seu endereço IP estático.
- Se usar a edição gratuita do ESXi 5.5 ou a edição gratuita do hipervisor vSphere 6, ativação pós-falha é bem-sucedida, mas a reativação pós-falha não for bem sucedido. Para ativar a reativação pós-falha, atualizar a licença de avaliação de qualquer programa.
- Se não é possível contactar o servidor de configuração do servidor de processos, utilize o Telnet para verificar a conectividade ao servidor de configuração na porta 443. Também pode tentar executar ping no servidor de configuração do servidor de processos. Um servidor de processos também deve ter um heartbeat quando está ligado ao servidor de configuração.
- Um servidor do Windows Server 2008 R2 SP1 que está protegido como um servidor físico no local não pode ser falhado pós-falha do Azure para um site no local.
- Não pode falhar novamente nas seguintes circunstâncias:
    - Tiver migrado máquinas para o Azure. [Saiba mais](migrate-overview.md#what-do-we-mean-by-migration).
    - Mover uma VM para outro grupo de recursos.
    - Eliminar a VM do Azure.
    - Desativar a proteção da VM.
    - Criado manualmente a VM no Azure. A máquina deve ter sido protegido inicialmente no local e a ativação pós-falha para o Azure antes de nova proteção.
    - Pode efetuar a ativação apenas para um anfitrião ESXi. Não é possível a reativação pós-falha VMs de VMware ou servidores físicos para anfitriões Hyper-V, máquinas físicas ou estações de trabalho VMware.


## <a name="next-steps"></a>Passos Seguintes

Depois da máquina virtual tem de introduzir um estado protegido, poderá [iniciar uma reativação pós-falha](vmware-azure-failback.md). A reativação pós-falha encerra a máquina virtual no Azure e efetua o arranque a máquina de virtual no local. Espere algum período de indisponibilidade para a aplicação. Escolha uma hora para a reativação pós-falha quando a aplicação pode tolerar o tempo de inatividade.


