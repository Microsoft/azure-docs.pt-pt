---
title: Permitir a replicação de VMs Azure encriptados na recuperação do local de Azure
description: Este artigo descreve como configurar a replicação de VMs com chave gerida pelo cliente (CMK) ativado discos de uma região de Azure para outra utilizando a Recuperação do Site.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/10/2020
ms.author: mayg
ms.openlocfilehash: 9f9052f51c5bab0ea738e9fd15d8f62f45ff0c9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93146540"
---
# <a name="replicate-machines-with-customer-managed-keys-cmk-enabled-disks"></a>Replicar máquinas virtuais com discos ativados com Chaves Geridas pelo Cliente (CMK)

Este artigo descreve como replicar VMs Azure com Customer-Managed Keys (CMK) habilitados discos geridos, de uma região de Azure para outra.

## <a name="prerequisite"></a>Pré-requisito
Tem de criar os conjuntos de encriptação do disco na região alvo para a subscrição-alvo antes de permitir a replicação para as suas máquinas virtuais que tenham discos geridos com CMK.

## <a name="enable-replication"></a>Ativar a replicação

Por exemplo, a principal região de Azure é a Ásia Oriental, e a região secundária é o Sudeste Asiático.

1. No cofre, selecione **+Replicate.**
2. Reparem nos seguintes campos.
    - **Fonte**: O ponto de origem dos VMs, que neste caso é **Azure**.
    - **Localização da origem**: A região de Azure onde pretende proteger as suas máquinas virtuais. Por exemplo, a localização da fonte é "Ásia Oriental".
    - **Modelo de implantação**: O modelo de implantação Azure das máquinas de origem.
    - **Assinatura de origem**: A subscrição a que pertencem as máquinas virtuais de origem. Pode ser qualquer subscrição que esteja no mesmo inquilino do Azure Ative Directory que o cofre dos serviços de recuperação.
    - **Grupo de Recursos**: O grupo de recursos a que pertencem as máquinas virtuais de origem. Todos os VMs do grupo de recursos selecionados estão listados para proteção no passo seguinte.

3. Em **Máquinas Virtuais**  >  **Selecione máquinas virtuais,** selecione cada VM que pretende replicar. Só pode selecionar máquinas para as quais a replicação pode ser ativada. Em seguida, selecione **OK**.

4. Nas **Definições,** pode configurar as seguintes definições do local-alvo.

    - **Localização do alvo**: O local onde os dados da sua máquina virtual de origem serão replicados. A Recuperação do Local fornece uma lista de regiões-alvo adequadas com base na localização da máquina selecionada. Recomendamos que utilize o mesmo local que a localização do cofre dos Serviços de Recuperação.
    - **Subscrição-alvo**: A subscrição-alvo que é usada para a recuperação de desastres. Por padrão, a subscrição-alvo é a mesma que a subscrição de origem.
    - **Grupo de recursos-alvo**: O grupo de recursos ao qual todas as suas máquinas virtuais replicadas pertencem. Por predefinição, a Recuperação do Site cria um novo grupo de recursos na região alvo. O nome recebe o `asr` sufixo. Se já existe um grupo de recursos que foi criado pela Azure Site Recovery, é reutilizado. Também pode optar por personalizá-lo, como mostra a seguinte secção. A localização do grupo de recursos-alvo pode ser qualquer região de Azure, exceto a região onde as máquinas virtuais de origem estão hospedadas.
    - **Rede virtual alvo**: Por padrão, a Recuperação do Site cria uma nova rede virtual na região alvo. O nome recebe o `asr` sufixo. Está mapeado para a sua rede de origem e usado para qualquer proteção futura. [Saiba mais](./azure-to-azure-network-mapping.md) sobre mapeamento de rede.
    - **Contas de armazenamento de destino (se a sua fonte VM não utilizar discos geridos)**: Por padrão, a Recuperação do Site cria uma nova conta de armazenamento-alvo imitando a configuração de armazenamento de VM de origem. Se uma conta de armazenamento já existe, é reutilizada.
    - **Replica discos geridos (se a sua fonte VM utiliza discos geridos)**: A Recuperação do Local cria novos discos geridos por réplicas na região alvo para espelhar os discos geridos da fonte VM do mesmo tipo de armazenamento (padrão ou premium) que os discos geridos pela VM de origem.
    - **Contas de armazenamento de cache**: A Recuperação do Local necessita de uma conta de armazenamento extra chamada armazenamento *cache* na região de origem. Todas as alterações nos VMs de origem são rastreadas e enviadas para a conta de armazenamento de cache. Depois são replicados no local do alvo.
    - **Conjunto de disponibilidade**: Por predefinição, a Recuperação do Site cria uma nova disponibilidade definida na região alvo. O nome tem o `asr` sufixo. Se já existe um conjunto de disponibilidade que foi criado pela Recuperação do Site, é reutilizado.
    - **Conjuntos de encriptação de disco (DES)**: A Recuperação do Local necessita do conjunto de encriptação do disco para ser utilizado para discos geridos por réplicas e alvos. Deve pré-criar o DES na subscrição-alvo e na região alvo antes de permitir a replicação. Por predefinição, um DES não é selecionado. Tem de clicar em 'Personalizar' para escolher um DISCO DES por fonte.
    - **Política de replicação**: Define as definições para o histórico de retenção de pontos de recuperação e frequência de instantâneo consistente com aplicações. Por predefinição, a Recuperação do Site cria uma nova política de replicação com definições predefinidos de *24 horas* para a retenção de pontos de recuperação e *60 minutos* para a frequência de instantâneo consistente com aplicações.

    ![Ativar a replicação para máquina com discos com CMK ativados](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-enable-dr.png)

## <a name="customize-target-resources"></a>Personalizar recursos-alvo

Siga estes passos para modificar as definições de alvo padrão de recuperação do site.

1. **Selecione Personalizar** ao lado de "Assinatura alvo" para modificar a subscrição de destino padrão. Selecione a subscrição da lista de subscrições disponíveis no inquilino AZure AD.

2. **Selecione Personalizar** ao lado de "Conjuntos de recursos, rede, armazenamento e disponibilidade" para modificar as seguintes definições predefinidos:
    - Para **o grupo de recursos Target,** selecione o grupo de recursos da lista de grupos de recursos na localização alvo da subscrição.
    - Para **a rede virtual Target,** selecione a rede a partir de uma lista de redes virtuais no local alvo.
    - Para **disponibilidade definida,** pode adicionar definições de disponibilidade ao VM, se fizerem parte de um conjunto de disponibilidade na região de origem.
    - Para **contas de Armazenamento Alvo,** selecione a conta a utilizar.

3. **Selecione Personalizar** ao lado de "Definições de encriptação de armazenamento" para selecionar o des dom alvo para cada chave gerida pelo cliente (CMK) de fonte gerida por fonte. No momento da seleção, você também será capaz de ver a que chave-alvo o DES está associado.

4. **Selecione Criar recurso-alvo**  >  **Ativar a replicação**.
5. Depois de os VMs estarem ativados para replicação, pode verificar o estado de saúde dos VMs em **itens replicados**.

![Screenshot que mostra onde verificar o estado de saúde dos VMs.](./media/azure-to-azure-how-to-enable-replication-cmk-disks/cmk-customize-target-disk-properties.png)

>[!NOTE]
>Durante a replicação inicial, o estado pode demorar algum tempo a refrescar-se, sem progressos aparentes. Clique **em Refresh**  para obter o estado mais recente.

## <a name="faqs"></a>FAQs

* Permiti à CMK um item replicado existente, como posso assegurar que a CMK também seja aplicada na região-alvo?

    Pode descobrir o nome do disco gerido por réplica (criado pela Azure Site Recovery na região alvo) e anexar o DES a este disco de réplica. No entanto, não poderá ver os detalhes do DES na lâmina dos Discos assim que o prender. Em alternativa, pode optar por desativar a replicação do VM e ativá-la novamente. Garantirá que vê DES e detalhes chave do cofre na lâmina dos Discos para o item replicado.

* Adicionei um novo disco com ativação CMK ao item replicado. Como posso replicar este disco com a Recuperação do Local Azure?

    A adição de um novo disco ativado pela CMK a um item replicado existente não é suportada. Desative a replicação e volte a ativar a replicação para a máquina virtual.

* Permiti que ambas as chaves da plataforma e do cliente gerissem as chaves, como posso proteger os meus discos?

    Ativar a dupla encriptação com as chaves geridas pela plataforma e pelo cliente é suplicada pela Recuperação do Site. Siga as instruções deste artigo para proteger a sua máquina. É necessário criar uma encriptação dupla ativada deDes na região alvo com antecedência. No momento de permitir a replicação para tal VM, pode fornecer este DES à Recuperação do Local.

