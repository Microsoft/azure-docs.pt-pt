---
title: Ativar a replicação de VMs Azure encriptados na recuperação do site Azure
description: Este artigo descreve como configurar a replicação para VMs com chave gerida pelo cliente (CMK) habilitado discos de uma região azure para outra utilizando a Recuperação do Site.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/10/2020
ms.author: mayg
ms.openlocfilehash: 367f29237a3f2a634f209026df47b0cbd6ffc97c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75897965"
---
# <a name="replicate-machines-with-customer-managed-keys-cmk-enabled-disks"></a>Replicar máquinas com chaves geridas pelo cliente (CMK) discos habilitados

Este artigo descreve como replicar VMs Azure com Chaves Geridas pelo Cliente (CMK) discos geridos por cliente, de uma região azure para outra.

## <a name="prerequisite"></a>Pré-requisito
Deve criar os conjuntos de encriptação do disco na região alvo para a subscrição do alvo antes de permitir a replicação para as suas máquinas virtuais que tenham discos geridos por CMK.

## <a name="enable-replication"></a>Ativar a replicação

Por este exemplo, a principal região de Azure é a Ásia Oriental, e a região secundária é o Sudeste Asiático.

1. No cofre, selecione **+Replicate**.
2. Reparem nos seguintes campos.
    - **Fonte**: O ponto de origem dos VMs, que neste caso é **O Azure**.
    - **Localização da fonte**: A região de Azure onde pretende proteger as suas máquinas virtuais. Para este exemplo, a localização de origem é "Leste asiático".
    - **Modelo de implantação**: O modelo de implantação Azure das máquinas de origem.
    - **Subscrição de origem**: A subscrição à qual pertencem as suas máquinas virtuais de origem. Pode ser qualquer subscrição que esteja no mesmo inquilino do Azure Ative Directory que o seu cofre de serviços de recuperação.
    - **Grupo de Recursos**: O grupo de recursos a que pertencem as suas máquinas virtuais de origem. Todos os VMs do grupo de recursos selecionados estão listados para proteção no próximo passo.

3. Em **Máquinas** > Virtuais**Selecione máquinas virtuais,** selecione cada VM que pretende replicar. Só pode selecionar máquinas para as quais a replicação pode ser ativada. Em seguida, selecione **OK**.

4. Em **Definições,** pode configurar as seguintes definições do site-alvo.

    - **Localização do alvo**: A localização onde os dados da máquina virtual de origem serão replicados. A Recuperação do Site fornece uma lista de regiões-alvo adequadas com base na localização da máquina selecionada. Recomendamos que use o mesmo local que a localização do cofre dos Serviços de Recuperação.
    - **Subscrição do alvo**: A subscrição do alvo que é usada para a recuperação de desastres. Por predefinição, a subscrição do alvo é a mesma que a subscrição de origem.
    - **Grupo de recursos-alvo**: O grupo de recursos ao qual todas as suas máquinas virtuais replicadas pertencem. Por padrão, a Recuperação do Site cria um novo grupo de recursos na região alvo. O nome `asr` recebe o sufixo. Se já existe um grupo de recursos que foi criado pela Azure Site Recovery, é reutilizado. Também pode optar por personalizá-lo, como mostra a seguinte secção. A localização do grupo de recursos alvo pode ser qualquer região azure, exceto a região onde estão hospedadas as máquinas virtuais de origem.
    - **Rede virtual alvo**: Por padrão, a Recuperação do Site cria uma nova rede virtual na região alvo. O nome `asr` recebe o sufixo. Está mapeado na sua rede de origem e usado para qualquer proteção futura. [Saiba mais](site-recovery-network-mapping-azure-to-azure.md) sobre o mapeamento da rede.
    - Contas de armazenamento de alvos **(se a sua fonte VM não utilizar discos geridos)**: Por padrão, a Recuperação do Site cria uma nova conta de armazenamento alvo imitando a configuração de armazenamento vM de origem. Se uma conta de armazenamento já existe, é reutilizada.
    - **Réplica de discos geridos (se a sua fonte VM utilizar discos geridos)**: A Recuperação do Site cria novos discos geridos por réplicana região alvo para espelhar os discos geridos pela Fonte VM do mesmo tipo de armazenamento (padrão ou premium) como os discos geridos pela VM.
    - **Contas**de armazenamento de cache : A Recuperação do Site precisa de uma conta de armazenamento extra chamada armazenamento de *cache* na região fonte. Todas as alterações nas VMs de origem são rastreadas e enviadas para a conta de armazenamento de cache. São então replicados para o local do alvo.
    - **Conjunto de disponibilidade**: Por padrão, a Recuperação do Site cria um novo conjunto de disponibilidade na região alvo. O nome `asr` tem o sufixo. Se já existe um conjunto de disponibilidades que foi criado pela Recovery do Site, é reutilizado.
    - Conjuntos de encriptação de **disco (DES)**: A recuperação do site precisa que os conjuntos de encriptação do disco sejam utilizados para a replicação e para os discos geridos pelo alvo. Deve pré-criar O DES na subscrição-alvo e na região-alvo antes de permitir a replicação. Por predefinição, não é selecionado um DES. Tem de clicar em 'Personalizar' para escolher um DES por disco de origem.
    - **Política de replicação**: Define as definições para o histórico de retenção de pontos de recuperação e frequência instantânea consistente com aplicações. Por padrão, a Recuperação do Site cria uma nova política de replicação com configurações padrão de *24 horas* para retenção de pontos de recuperação e *60 minutos* para frequência instantânea consistente com aplicações.

    ![Ativar a replicação para máquina com discos ativados pela CMK](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-enable-dr.png)

## <a name="customize-target-resources"></a>Personalizar recursos-alvo

Siga estes passos para modificar as definições de alvo predefinidos de recuperação do site.

1. Selecione **Personalizar** ao lado da "subscrição target" para modificar a subscrição do alvo predefinido. Selecione a subscrição da lista de subscrições disponíveis no inquilino da AD Azure.

2. Selecione **Personalizar** ao lado de "Conjuntos de recursos, rede, armazenamento e disponibilidade" para modificar as seguintes definições predefinidas:
    - Para o **grupo de recursos Target,** selecione o grupo de recursos da lista de grupos de recursos na localização-alvo da subscrição.
    - Para **a rede virtual Target,** selecione a rede a partir de uma lista de redes virtuais na localização alvo.
    - Para o conjunto de **disponibilidade,** pode adicionar definições de disponibilidade ao VM, se fizerem parte de um conjunto de disponibilidade na região de origem.
    - Para **as contas de Armazenamento de Destino,** selecione a conta a utilizar.

3. Selecione **Personalizar** ao lado de "Definições de encriptação de armazenamento" para selecionar o DES do alvo para cada chave gerida pelo cliente (CMK) disco gerido por fonte. No momento da seleção, você também será capaz de ver qual o cofre chave alvo com que o DES está associado.

4. Selecione Criar a**replicação ativado** **por um recurso-alvo** > .
5. Depois de os VMs estarem ativados para a replicação, pode verificar o estado de saúde dos VMs em **itens replicados**.

![Ativar a replicação para máquina com discos ativados pela CMK](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-customize-target-disk-properties.png)

>[!NOTE]
>Durante a replicação inicial, o estado pode levar algum tempo a refrescar-se, sem progressos aparentes. Clique em **Refresh** para obter o estado mais recente.

## <a name="faqs"></a>FAQs

* Acionei a CMK num item replicado existente, como posso garantir que a CMK também seja aplicada na região-alvo?

    Pode descobrir o nome do disco gerido pela réplica (criado pela Azure Site Recovery na região alvo) e anexar o DES a este disco de réplica. No entanto, não poderá ver os detalhes do DES na lâmina dos Discos assim que o prender. Em alternativa, pode optar por desativar a replicação do VM e capacitá-la novamente. Irá garantir que você verá detalhes do des e do cofre chave na lâmina dos Discos para o item replicado.

* Adicionei um novo disco cmk habilitado ao item replicado. Como posso replicar este disco com a Recuperação do Site Azure?

    A adição de um novo disco ativado pela CMK a um item replicado existente não é suportada. Desative a replicação e volte a ativar a replicação para a máquina virtual.

