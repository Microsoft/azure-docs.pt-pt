---
title: Mova os VMs Azure para outra região com recuperação do site Azure
description: Utilize a Recuperação do Sítio Azure para mover Os VMs Azure IaaS de uma região de Azure para outra.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 4882206692c334d6ab6af28feb5d2cba5277eea1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78303941"
---
# <a name="move-azure-vms-to-another-region"></a>Mover VMs do Azure para outra região

Existem vários cenários em que você gostaria de mover as suas máquinas virtuais Azure IaaS existentes (VMs) de uma região para outra. Por exemplo, pretende melhorar a fiabilidade e disponibilidade dos seus VMs existentes, melhorar a gestão ou mover-se por razões de governação. Para mais informações, consulte a visão geral do [Azure VM](azure-to-azure-move-overview.md). 

Você pode usar o serviço de recuperação de [sites Azure](site-recovery-overview.md) para gerir e orquestrar a recuperação de desastres de máquinas no local e VMs Azure para continuidade de negócios e recuperação de desastres (BCDR). Também pode utilizar a Recuperação do Site para gerir a mudança de VMs Azure para uma região secundária.

Neste tutorial, irá:

> [!div class="checklist"]
> 
> * Verifique os pré-requisitos para a mudança
> * Prepare as VMs de origem e a região alvo
> * Copiar os dados e permitir a replicação
> * Teste a configuração e execute o movimento
> * Eliminar os recursos na região nascente
> 
> [!NOTE]
> Este tutorial mostra-lhe como mover VMs Azure de uma região para outra como está. Se precisar de melhorar a disponibilidade movendo VMs numa disponibilidade definida para VMs de zona fixada numa região diferente, consulte os [VMs Move Azure para](move-azure-vms-avset-azone.md)o tutorial de Zonas de Disponibilidade .

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que os VMs Azure estão na região de Azure, de onde pretende mover-se.
- Verifique se a sua escolha da [região-alvo - a combinação](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)da região alvo é apoiada - e tome uma decisão informada sobre a região alvo.
- Certifique-se de que compreende a [arquitetura e os componentes do cenário](azure-to-azure-architecture.md).
- Veja os [requisitos e limitações de suporte](azure-to-azure-support-matrix.md).
- Verifique as permissões da conta. Se criou a sua conta Azure gratuita, é o administrador da sua subscrição. Se não for o administrador de subscrição, trabalhe com o administrador para atribuir as permissões de que necessita. Para permitir a replicação de um VM e essencialmente copiar dados utilizando a Recuperação do Site Azure, deve ter:

    - Permissões para criar uma VM nos recursos do Azure. O papel integrado da Máquina Virtual tem estas permissões, que incluem:
    - Permissão para criar uma VM no grupo de recursos selecionado
    - Permissão para criar uma VM na rede virtual selecionada
    - Permissão para escrever na conta de armazenamento selecionada
    
    - Permissões para gerir operações de recuperação do site azure. O papel do Colaborador de Recuperação do Site tem todas as permissões necessárias para gerir as operações de Recuperação do Local num cofre dos Serviços de Recuperação.

- Certifique-se de que todos os certificados de raiz mais recentes estão nos VMs Azure que pretende mover. Se os certificados de raiz mais recentes não estiverem no VM, as restrições de segurança impedirão a cópia dos dados para a região alvo.

- Para VMs do Windows, instale todas as atualizações mais recentes do Windows na VM, para que todos os certificados de raiz fidedigna fiquem na máquina. Num ambiente desligado, siga o padrão do Windows Update e os processos de atualização de certificados da sua organização.
    
- Para os VMs Linux, siga as orientações fornecidas pelo seu distribuidor Linux para obter os mais recentes certificados de raiz fidedignos e lista de revogação de certificados no VM.
- Certifique-se de que não está a utilizar um proxy de autenticação para controlar a conectividade da rede para VMs que pretende mover.

- Se o VM que está a tentar mover não tem acesso à internet, ou está a usar um representante de firewall para controlar o acesso de saída, [verifique os requisitos](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms).

- Identifique o layout de rede de origem e todos os recursos que está a utilizar. Isto inclui, mas não se limita a equilibradores de carga, grupos de segurança de rede (NSGs) e IPs públicos.

- Verifique se a sua subscrição Azure permite criar VMs na região alvo que é usada para a recuperação de desastres. Contacte o suporte para ativar a quota necessária.

- Certifique-se de que a sua subscrição dispõe de recursos suficientes para suportar VMs com tamanhos que correspondam aos seus VMs de origem. Se estiver a utilizar a Recuperação do Site para copiar dados para o alvo, a Recuperação do Site escolhe o mesmo tamanho ou o tamanho mais próximo possível para o VM alvo.

- Certifique-se de que cria um recurso-alvo para cada componente identificado no layout de rede de origem. Este passo é importante para garantir que os seus VMs possuam todas as funcionalidades e funcionalidades na região alvo que você tinha na região de origem.

     > [!NOTE] 
     > A Recuperação do Site Azure descobre e cria automaticamente uma rede virtual quando ativa a replicação para o VM de origem. Também pode pré-criar uma rede e atribuí-la ao VM no fluxo do utilizador para facilitar a replicação. Como mencionado mais tarde, é necessário criar manualmente quaisquer outros recursos na região alvo.

    Para criar os recursos de rede mais utilizados que são relevantes para si com base na configuração vm de origem, consulte a seguinte documentação:
    - [Grupos de segurança da rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Balanceadores de carga](https://docs.microsoft.com/azure/load-balancer)
    -  [IP público](../virtual-network/virtual-network-public-ip-address.md)
    - Para qualquer outro componente de rede, consulte a [documentação](https://docs.microsoft.com/azure/?pivot=products&panel=network)de rede .



## <a name="prepare"></a>Preparação
Os seguintes passos mostram como preparar a máquina virtual para o movimento utilizando a Recuperação do Site Azure como solução. 

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Crie o cofre em qualquer região, exceto a região de origem

1. Inscreva-se nos Serviços de > **Recuperação**do [portal Azure.](https://portal.azure.com)
1. Selecione Criar uma cópia de segurança**e recuperação**de**ferramentas** > de gestão de **recursos.** > 
1. No **Nome**, especifique o nome amigável **ContosoVMVault**. Se tiver mais do que uma subscrição, selecione a que for adequada.
1. Crie o grupo de recursos **ContosoRG.**
1. Selecione uma região do Azure. Para verificar as regiões apoiadas, consulte a disponibilidade geográfica nos detalhes de preços de recuperação do [site do Azure.](https://azure.microsoft.com/pricing/details/site-recovery/)
1. Nos **cofres dos Serviços de Recuperação,** selecione **Overview** > **ContosoVMVault** > **+Replicate**.
1. Em **Origem**, selecione **Azure**.
1. Em **Localização de origem**, selecione a região do Azure de origem onde as VMs estão atualmente a ser executadas.
1. Selecione o modelo de implementação do Resource Manager. Em seguida, selecione a **subscrição Fonte** e o **grupo de recursos Source**.
1. Selecione **OK** para guardar as definições.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Ativar a replicação para VMs Azure e começar a copiar os dados

A Recuperação do Site recupera uma lista dos VMs que estão associados ao grupo de subscrição e recursos.

1. No próximo passo, selecione o VM que pretende mover e, em seguida, selecione **OK**.
1. Em **Definições,** selecione recuperação de **desastres**.
1. Na > **região-alvo**de recuperação de **desastres configure,** selecione a região alvo para a qual irá replicar.
1. Para este tutorial, aceite as outras predefinições.
1. Selecione **Ativar replicação**. Este passo inicia um trabalho para permitir a replicação para o VM.

    ![Ativar a replicação](media/tutorial-migrate-azure-to-azure/settings.png)

## <a name="move"></a>Mover

Os seguintes passos mostram como executar a mudança para a região alvo.

1. Vai para o cofre. Em **Definições** > **Itens replicados,** selecione o VM e, em seguida, selecione **Failover**.
2. Em **Ativação pós-falha**, selecione **Mais recente**.
3. Selecione **Encerrar a máquina antes de iniciar a ativação pós-falha**. O Site Recovery tenta encerrar a VM de origem antes de acionar a ativação pós-falha. A ativação pós-falha continua, mesmo que o encerramento falhe. Pode acompanhar o progresso da falha na página **Jobs.**
4. Depois de terminar o trabalho, verifique se o VM aparece na região-alvo de Azure, como esperado.


## <a name="discard"></a>Eliminar 

Caso verifique o VM em movimento e precise de fazer alterações para um ponto de falha ou queira voltar a um ponto anterior, nos **itens Replicados,** selecione o ponto de **recuperação**VM > Change . Este passo proporciona-lhe a opção de especificar um ponto de recuperação diferente e falhar com esse. 


## <a name="commit"></a>Consolidação 

Depois de verificar o VM em mudança e estiver pronto para cometer a alteração, nos **itens Replicados,** selecione à direita o VM > **Commit**. Este passo termina o processo de mudança para a região alvo. Espere até que o trabalho de compromisso termine.

## <a name="clean-up"></a>Limpeza

Os seguintes passos irão guiá-lo através de como limpar a região de origem, bem como recursos relacionados que foram usados para a mudança.

Para todos os recursos que foram usados para a mudança:

- Vai para a VM. Selecione **Desativar a replicação**. Este passo impede que o processo copie os dados para o VM.

   > [!IMPORTANT]
   > É importante realizar este passo para evitar ser cobrado para a replicação da Recuperação do Site Azure.

Se não tiver planos para reutilizar nenhum dos recursos de origem, complete estes passos adicionais:

1. Elimine todos os recursos de rede relevantes na região de origem que identificou nos [pré-requisitos](#prerequisites).
1. Eliminar a conta de armazenamento correspondente na região fonte.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, mudou um VM Azure para uma região azure diferente. Agora pode configurar a recuperação de desastres para o VM que mudou.

> [!div class="nextstepaction"]
> [Configurar a recuperação após desastre após a migração](azure-to-azure-quickstart.md)

