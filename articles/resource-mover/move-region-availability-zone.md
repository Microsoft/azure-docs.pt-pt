---
title: Mover VMs Azure para zonas de disponibilidade em outra região com Azure Resource Mover
description: Saiba como mover VMs Azure para zonas de disponibilidade com o Azure Resource Mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 88006fb354af2673496c6476090d7f73c8a005e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95543005"
---
# <a name="move-azure-vms-to-an-availability-zone-in-another-region"></a>Mover VMs Azure para uma zona de disponibilidade em outra região

Neste artigo, aprenda a mover VMs Azure (e recursos relacionados de rede/armazenamento) para uma zona de disponibilidade numa região de Azure diferente, utilizando [o Azure Resource Mover](overview.md).

[As zonas de disponibilidade do Azure](../availability-zones/az-overview.md#availability-zones) ajudam a proteger a sua implantação do Azure contra falhas no datacenter. Cada zona de disponibilidade é constituída por um ou mais datacenters equipados com energia, refrigeração e redes independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as [regiões ativadas.](../availability-zones/az-region.md) Utilizando o Resource Mover, pode mover-se:

- Um único exemplo VM para uma zona de disponibilidade/disponibilidade definida na região alvo.
- Um VM em uma disponibilidade definida para uma zona de disponibilidade/disponibilidade definida na região alvo.
- Um VM numa zona de disponibilidade de região de origem para uma zona de disponibilidade na região alvo.


> [!IMPORTANT]
> A Azure Resource Mover está atualmente em pré-visualização pública.

Se pretende mover VMs para uma zona de disponibilidade diferente na mesma região, [reveja este artigo.](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)

## <a name="prerequisites"></a>Pré-requisitos

- *O* acesso do proprietário à subscrição em que os recursos que pretende mover estão localizados.
    - A primeira vez que adiciona um recurso para uma fonte específica e mapeamento de destino numa subscrição do Azure, o Resource Mover cria uma [identidade gerida atribuída ao sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (anteriormente conhecida como Managed Service Identify (MSI)) que é fidedigna pela subscrição.
    - Para criar a identidade e atribuir-lhe a função necessária (administrador de Acesso ao Utilizador ou Colaborador na subscrição de origem), a conta que utiliza para adicionar recursos necessita de permissões *do Proprietário* na subscrição. [Saiba mais](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) sobre os papéis do Azure.
- A subscrição necessita de quotas suficientes para criar os recursos de origem na região-alvo. Se não, peça limites adicionais. [Saiba mais](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Verifique os preços e os encargos associados à região-alvo para a qual está a mover VMs. Use a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para ajudá-lo.
    


## <a name="check-vm-requirements"></a>Verifique os requisitos de VM

1. Verifique se os VMs que pretende mover são suportados.

    - [Verifique os](support-matrix-move-region-azure-vm.md#windows-vm-support) VMs do Windows suportados.
    - [Verifique as](support-matrix-move-region-azure-vm.md#linux-vm-support) versões de Linux VMs e kernel suportadas.
    - Verifique as definições [de computação suportada,](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings) [armazenamento](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)e [rede.](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings)
2. Verifique se os VMs que pretende mover estão ligados.
3. Certifique-se de que os VMs têm os mais recentes certificados de raiz fidedignos e uma lista atualizada de revogação de certificados (CRL). 
    - Nos VMs Azure que executam o Windows, instale as atualizações mais recentes do Windows.
    - Em VMs que executam o Linux, siga a orientação do distribuidor Linux para garantir que a máquina tem os certificados mais recentes e CRL. 
4. Permitir a conectividade de saída a partir de VMs:
    - Se estiver a usar um proxy de firewall baseado em URL para controlar a conectividade de saída, permita o acesso a estes [URLs](support-matrix-move-region-azure-vm.md#url-access)
    - Se estiver a utilizar as regras do Grupo de Segurança de Rede (NSG) para controlar a conectividade de saída, crie estas [regras de marcação de serviço](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Selecione recursos para mover

Selecione os recursos que pretende mover.

- Pode selecionar qualquer tipo de recurso suportado entre grupos de recursos na região de origem que seleciona.
- Você move recursos para uma região alvo na subscrição da região de origem. Se quiser alterar a subscrição, pode fazê-lo depois de os recursos serem transferidos.

1. No portal Azure, procure por recursos. Em seguida, em **Serviços**, selecione **Azure Resource Mover**.

    ![Pesquisa rumo ao Mover de Recursos](./media/move-region-availability-zone/search.png)

2. Em **Visão Geral**, selecione **Começar a trabalhar**.

    ![Botão para começar](./media/move-region-availability-zone/get-started.png)

3. Em **Move resources** Fonte +  >  **destino,** selecione a subscrição de origem e região.
4. No **Destino**, selecione a região para a qual pretende mover os VMs. Em seguida, clique em **Seguinte**.

     ![Página para preencher a subscrição/região de origem e destino](./media/move-region-availability-zone/source-target.png)

6. Em **Recursos a mover,** clique em **Selecionar recursos.**
7. Em **Recursos Selecionados,** selecione o VM. Só é possível adicionar recursos apoiados para a mudança. Em seguida, clique em **Fazer**. Em **Recursos a mover- se,** clique em **Seguinte**.

    ![Página para selecionar VMs para mover](./media/move-region-availability-zone/select-vm.png)
8. Em **Comentário + Adicionar,** verifique as definições de origem e destino.

    ![Página para rever definições e proceder com movimento](./media/move-region-availability-zone/review.png)

9. Clique **em Continuar,** para começar a adicionar os recursos.
10. Após o processo de adição terminar com sucesso, clique em **Adicionar recursos para se mover** no ícone de notificação.

    ![Mensagem em notificações](./media/move-region-availability-zone/notification.png)

Depois de clicar na notificação, os recursos aparecem na página **das regiões**

> [!NOTE]
> Depois de clicar na notificação, os recursos aparecem na página **de Regiões,** num estado pendente de *Preparação.*
> - Se quiser remover um recurso de uma recolha de movimentos, o método para o fazer depende de onde estiver no processo de movimento. [Saiba mais](remove-move-resources.md).

## <a name="resolve-dependencies"></a>Resolver dependências

1. Se os recursos mostrarem uma mensagem *de dependências validadas* na coluna **'Problemas',** clique no botão **'Validar dependências'.** Os seres do processo de validação.
2. Se forem encontradas dependências, clique em **Adicionar dependências**. 
3. Nas **dependências de adicionar**, selecione os recursos dependentes > Adicionar **dependências**. Monitorize o progresso nas notificações.

    ![Botão para adicionar dependências](./media/move-region-availability-zone/add-dependencies.png)

3. Adicione dependências adicionais se necessário e valide as dependências novamente. 

    ![Página para adicionar dependências adicionais](./media/move-region-availability-zone/add-additional-dependencies.png)

4. Na página **do Across regions,** verifique se os recursos estão agora num Estado pendente de *Preparação,* sem problemas.

    ![Página mostrando recursos na preparação do estado pendente](./media/move-region-availability-zone/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>Mover o grupo de recursos de origem 

Antes de poder preparar e mover VMs, o grupo de recursos de origem deve estar presente na região alvo. 

### <a name="prepare-to-move-the-source-resource-group"></a>Preparar para mover o grupo de recursos de origem

Prepare-se da seguinte forma:

1. Em **Regiões ,** selecione o grupo de recursos de origem > **Prepare- se**.
2. Na **preparação dos recursos,** clique **em Preparar.**

    ![Botão para preparar o grupo de recursos de origem](./media/move-region-availability-zone/prepare-resource-group.png)

    Durante o processo de preparação, o Resource Mover gera modelos do Azure Resource Manager (ARM) utilizando as definições do grupo de recursos. Os recursos dentro do grupo de recursos não são afetados.

> [!NOTE]
>  Depois de preparar o grupo de recursos, está no *movimento Iniciar pendente.* 

![Estado que mostra início pendente estado](./media/move-region-availability-zone/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>Mover o grupo de recursos de origem

Inicie o movimento da seguinte forma:

1. Em **Regiões,** selecione o grupo de recursos > **Iniciar Movimento**
2. In **Move Resources,** clique em **Iniciar movimento**. O grupo de recursos move-se para um *estado iniciado em* progresso.
3. Após o início do movimento, o grupo de recursos-alvo é criado, com base no modelo ARM gerado. O grupo de recursos de origem move-se para um *movimento comprometendo-se pendente* estado.

![Estado mostrando movimento de compromisso](./media/move-region-availability-zone/commit-move-pending.png)

Para comprometer e terminar o processo de mudança:

1. Em **Todas as regiões**, selecione o grupo de recursos > **Cometer movimento**
2. In **Move Resources,** clique **em Cometer**.

> [!NOTE]
> Depois de cometer o movimento, o grupo de recursos de origem encontra-se num estado *pendente de origem Delete.*


## <a name="add-a-target-availability-zone"></a>Adicionar uma zona de disponibilidade alvo

Antes de movermos o resto dos recursos, vamos definir uma zona de disponibilidade alvo para o VM.

1. Na página **Regiões Trans,** clique no link na coluna de **configuração destino** do VM que está a mover.

    ![Propriedades VM](./media/move-region-availability-zone/select-vm-settings.png)

3. Nas **definições de configuração**, especifique a definição para o VM de destino. Pode configurar um VM na região alvo da seguinte forma:
    -  Criar um novo recurso equivalente na região alvo. Com exceção das definições especificadas, o recurso-alvo é criado com as mesmas definições que a fonte.
    - Selecione um VM existente na região alvo. 

4. Em **Zonas,** selecione a zona em que pretende colocar o VM quando se mover.

    As alterações são feitas apenas para o recurso que está a editar. É necessário atualizar os recursos dependentes separadamente.

5. No **SKU,** especifique o [nível Azure](..//virtual-machines/sizes.md) que pretende atribuir ao VM alvo.
6. Em **Disponibilidade definida**, selecione um conjunto de disponibilidade se quiser que o VM alvo seja executado dentro de uma disponibilidade definida na zona de disponibilidade.
7. Selecione **Guardar alterações**.

    ![Definições de VM](./media/move-region-availability-zone/vm-settings.png)


## <a name="prepare-resources-to-move"></a>Preparar recursos para mover

Agora que o grupo de recursos de origem está movido, pode preparar-se para mover os outros recursos.

1. Em **Regiões,** selecione os recursos que pretende preparar. 

    ![Página para selecionar preparar para outros recursos](./media/move-region-availability-zone/prepare-other.png)

2. **Selecione Preparar**. 

> [!NOTE]
> - Durante o processo de preparação, o agente de mobilidade de recuperação do local Azure é instalado em VMs, para replicação.
> - Os dados de VM são replicados periodicamente na região alvo. Isto não afeta a fonte VM.
> - O Movimento de Recursos gera modelos ARM para os outros recursos de origem.
> - Depois de preparar recursos, estão numa *ação iniciada pendente.*

![Página mostrando recursos no início do movimento pendente estado](./media/move-region-availability-zone/initiate-move-pending.png)

## <a name="initiate-the-move"></a>Inicie o movimento

Com os recursos preparados, pode agora iniciar a mudança. 

1. Em **Todas as regiões**, selecione recursos com *movimento de iniciado* do estado pendente . Em seguida, clique em **Iniciar movimento**
2. Em **Recursos Move,** clique em **Iniciar movimento**.

    ![Página para iniciar movimento de recursos](./media/move-region-availability-zone/initiate-move.png)

3. O progresso da pista na barra de notificações.


> [!NOTE]
> - Para os VMs, as VM réplicas são criadas na região alvo. A fonte VM é desligada e ocorre algum tempo de paragem (geralmente minutos).
> - O Resource Mover recria outros recursos utilizando os modelos ARM que foram preparados. Normalmente não há tempo de descanso.
> - Depois de prepararem os recursos, estão numa *ação pendente.*


![Página para mostrar recursos em um movimento pendente estado](./media/move-region-availability-zone/resources-commit-move-pending.png)

## <a name="discard-or-commit"></a>Descartar ou comprometer-se?

Após o movimento inicial, pode decidir se quer cometer o movimento ou descartá-lo. 

- **Descartar:** Pode descartar um movimento se estiver a testar e não pretende mover o recurso de origem. Descartar o movimento devolve o recurso a um estado de *movimento iniciado pendente*.
- **Compromete-se**: Compromete-se a concluir a mudança para a região alvo. Após o compromisso, um recurso de origem estará num estado de *Eliminação de fonte pendente*, e pode decidir se pretende eliminá-lo.

## <a name="discard-the-move"></a>Descartar o movimento 

Pode descartar o movimento da seguinte forma:

1. Em **Todas as regiões**, selecione recursos com movimento de compromisso do estado *pendente*, e clique em **Descartar move**.
2. Em **Descartar movimento**, clique em **Descartar**.
3. O progresso da pista na barra de notificações.
 

> [!NOTE]
> Para os VMs, depois de descartarem os recursos, estão numa *ação iniciada pendente.*

## <a name="commit-the-move"></a>Cometa o movimento

Se quiser concluir o processo de mudança, cometa o movimento. 

1. Em **Todas as regiões**, selecione recursos com movimento de compromisso do estado *pendente*, e clique em **Cometer movimento**.
2. Nos **recursos de cometer,** clique **em Cometer**.

    ![Página para comprometer recursos para finalizar movimento](./media/move-region-availability-zone/commit-resources.png)

3. Acompanhe o progresso do compromisso na barra de notificações.

> [!NOTE]
> - Depois de cometer o movimento, os VMs param de se replicar. A fonte VM não é afetada pelo compromisso.
> - Cometer não afeta recursos de rede de fontes.
> - Após a sua commissão, os recursos encontram-se numa *fonte de Exclusão pendente.*

![Página mostrando recursos em *Eliminar fonte pendente* estado](./media/move-region-availability-zone/delete-source-pending.png)

## <a name="configure-settings-after-the-move"></a>Configurar definições após o movimento

O serviço de Mobilidade não é desinstalado automaticamente a partir de VMs. Desinstale-o manualmente ou deixe-o se planeia mover o servidor novamente.


## <a name="delete-source-resources-after-commit"></a>Eliminar recursos de origem após cometer

Após a mudança, pode opcionalmente apagar recursos na região de origem.

1. Em **Regiões Diferentes,** clique no nome de cada recurso de origem que pretende eliminar.
2. Na página de propriedades para cada recurso, selecione **Delete**.

## <a name="delete-additional-resources-created-for-move"></a>Eliminar recursos adicionais criados para a mudança

Após a mudança, pode eliminar manualmente a recolha de movimentos e os recursos de Recuperação do Local que foram criados.

- A coleção de movimentos é escondida por defeito. Para vê-lo, precisa ligar recursos escondidos.
- O armazenamento da cache tem uma fechadura que deve ser eliminada, antes de poder ser eliminada.

Eliminar da seguinte forma: 

1. Localize os recursos no grupo de ```RegionMoveRG-<sourceregion>-<target-region>``` recursos.
2. Verifique se todos os VM e outros recursos de origem na região de origem foram movidos ou eliminados. Isto garante que não existem recursos pendentes que os utilizem.
2. Eliminar os recursos:

    - O nome da coleção move é ```movecollection-<sourceregion>-<target-region>``` .
    - O nome da conta de armazenamento de cache é ```resmovecache<guid>```
    - O nome do cofre ```ResourceMove-<sourceregion>-<target-region>-GUID``` é.

## <a name="next-steps"></a>Passos seguintes

[Conheça](about-move-process.md) o processo de mudança.