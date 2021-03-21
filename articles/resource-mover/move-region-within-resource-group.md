---
title: Mover recursos para outra região com Azure Resource Mover
description: Saiba como mover recursos dentro de um grupo de recursos para outra região com a Azure Resource Mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/08/2020
ms.author: raynew
ms.openlocfilehash: 79224c14fc5182df7a699864af3d78c9be36259f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97797285"
---
# <a name="move-resources-across-regions-from-resource-group"></a>Mover recursos através das regiões (do grupo de recursos)

Neste artigo, aprenda a mover recursos num grupo de recursos específicos para uma região de Azure diferente. No grupo de recursos, seleciona os recursos que pretende mover. Em seguida, movê-los usando [Azure Resource Mover](overview.md).

> [!IMPORTANT]
> A Azure Resource Mover está atualmente em pré-visualização pública.


## <a name="prerequisites"></a>Pré-requisitos

- Precisa do acesso *do Proprietário* à subscrição em que estão localizados os recursos que pretende mover.
    - A primeira vez que adiciona um recurso para uma fonte específica e mapeamento de destino numa subscrição do Azure, o Resource Mover cria uma [identidade gerida atribuída ao sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (anteriormente conhecida como Managed Service Identify (MSI)) que é fidedigna pela subscrição.
    - Para criar a identidade e atribuir-lhe a função necessária (administrador de Acesso ao Utilizador ou Colaborador na subscrição de origem), a conta que utiliza para adicionar recursos necessita de permissões *do Proprietário* na subscrição. [Saiba mais](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) sobre os papéis do Azure.
- A subscrição necessita de quotas suficientes para criar os recursos de origem na região-alvo. Se não, peça limites adicionais. [Saiba mais](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Verifique os preços e os encargos associados à região-alvo para a qual está a mover VMs. Use a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para ajudá-lo.
- Verifique se os recursos que pretende mover são suportados pela Resource Mover:
    - VMs Azure e discos associados
    - NICs
    - Conjuntos de disponibilidade
    - Redes virtuais do Azure
    - Endereços IP públicos
    - Grupos de segurança de rede (NSGs)
    - Equilibradores de carga interna e pública
    - Bases de dados Azure SQL e piscinas elásticas


## <a name="check-vm-requirements"></a>Verifique os requisitos de VM

1. Verifique se os VMs que pretende mover são suportados.

    - [Verifique os](support-matrix-move-region-azure-vm.md#windows-vm-support) VMs do Windows suportados.
    - [Verifique as](support-matrix-move-region-azure-vm.md#linux-vm-support) versões de Linux VMs e kernel suportadas.
    - Verifique as definições [de computação suportada,](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings) [armazenamento](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)e [rede.](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings)
2. Certifique-se de que os VMs têm os mais recentes certificados de raiz fidedignos e uma lista atualizada de revogação de certificados (CRL). 
    - Nos VMs Azure que executam o Windows, instale as atualizações mais recentes do Windows.
    - Em VMs que executam o Linux, siga a orientação do distribuidor Linux para garantir que a máquina tem os certificados mais recentes e CRL. 
3. Permitir a conectividade de saída a partir de VMs:
    - Se estiver a usar um proxy de firewall baseado em URL para controlar a conectividade de saída, permita o acesso a estes [URLs](support-matrix-move-region-azure-vm.md#url-access)
    - Se estiver a utilizar as regras do Grupo de Segurança de Rede (NSG) para controlar a conectividade de saída, crie estas [regras de marcação de serviço](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Selecione recursos para mover

Selecione os recursos que pretende mover. Você move recursos para uma região alvo na subscrição da região de origem. Se quiser alterar a subscrição, pode fazê-lo depois de os recursos serem transferidos.

> [!NOTE]
>  Não selecione discos associados ou a operação falhará. Os discos associados são automaticamente incluídos num movimento VM.

1. No portal Azure, abra o grupo de recursos relevante.
2. Na página do grupo de recursos, selecione os recursos que pretende mover.
3. Selecione   >  **Mover-se para outra região**.

    ![Seleção para transferir recursos para uma região diferente](./media/move-region-within-resource-group/select-move-region.png)
    
4. Na **Fonte + destino,** selecione a região alvo para a qual pretende mover os recursos. Em seguida, selecione **Seguinte**.


    ![Página de origem e destino para selecionar região alvo](./media/move-region-within-resource-group/source-target.png)


7. Em **Recursos a mover- ,** selecione **Seguinte**.  
8. Em **Selecione recursos,** selecione o recurso que pretende mover. Só é possível adicionar recursos apoiados para a mudança. Em seguida, selecione **Done** (Concluído).
9. Em **Movimento recursos**, selecione **Next**. 
10. Em **Review + Add**, verifique os detalhes da origem e do alvo.
11. Confirme que compreende que os metadados sobre os recursos que estão a ser movidos serão armazenados num grupo de recursos criado para o efeito, e que permite que o Resource Mover crie uma identidade gerida pelo sistema para aceder aos recursos de subscrição.
1. **Selecione Proceder** para começar a adicionar os recursos.

    ![Página de resumo para verificar detalhes e proceder com movimento](./media/move-region-within-resource-group/summary.png)    

11. A operação de recursos adicionais começa. Quando a operação termina, as notificações mostram que os recursos foram adicionados e a implantação foi bem sucedida.
13. Nas notificações, selecione **Adicionar recursos para movimento**.

    ![Mensagem mostrada nas notificações](./media/move-region-within-resource-group/notification.png)    


14. Após a seleção da notificação, os recursos selecionados são adicionados a uma coleção de movimentos no hub Azure Resource Mover.  O Resource Mover ajuda-o a verificar as dependências e, em seguida, a mover recursos para a região alvo.

## <a name="resolve-dependencies"></a>Resolver dependências

Os recursos que está a mover aparecem na página **das regiões,** num estado pendente de *preparação.* Iniciar a validação da seguinte forma:

1. Se os recursos mostrarem uma mensagem *de dependências validadas* na coluna **'Problemas',** selecione o botão **'Validar dependências'.** O processo de validação começa.

    ![Botão para validar dependências](./media/move-region-within-resource-group/validate-dependencies.png)

2. Se forem encontradas dependências, **selecione Adicionar dependências**. 
3. Nas **dependências de adicionar**, selecione os recursos dependentes > Adicionar **dependências**. Monitorize o progresso nas notificações.

    ![Botão para adicionar dependências](./media/move-region-within-resource-group/add-dependencies.png)

3. Adicione dependências adicionais se necessário e valide as dependências conforme necessário. Selecione **Refresh** para garantir que os recursos mostram um estado atualizado.

4. Na página **do Across regions,** verifique se os recursos estão agora num Estado pendente de *Preparação,* sem problemas.

    ![Página para mostrar preparar estado pendente para todos os recursos](./media/move-region-within-resource-group/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>Mover o grupo de recursos de origem 

Antes de poder preparar e mover recursos, o grupo de recursos de origem deve estar presente na região alvo. 

### <a name="prepare-to-move-the-source-resource-group"></a>Preparar para mover o grupo de recursos de origem

Prepare-se da seguinte forma:

1. Em **Regiões ,** selecione o grupo de recursos de origem > **Prepare- se**.
2. Na **preparação dos recursos,** selecione **Prepare- se**.
1. 
    ![Botão para preparar o grupo de recursos de origem](./media/move-region-within-resource-group/prepare-source-resource-group.png)

    Durante o processo de preparação, o Resource Mover gera modelos do Azure Resource Manager (ARM) utilizando as definições do grupo de recursos. Os recursos dentro do grupo de recursos não são afetados.
    
> [!NOTE]
>  Depois de preparar o grupo de recursos, está no *movimento Iniciar pendente.* Refresque-se para mostrar o último estado.

![Estado que mostra início pendente estado](./media/move-region-within-resource-group/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>Mover o grupo de recursos de origem

Inicie o movimento da seguinte forma:

1. Em **Regiões,** selecione o grupo de recursos > **Iniciar Movimento**
2. In **Move Resources**, selecione **Iniciar movimento**. O grupo de recursos move-se para um *estado iniciado em* progresso.
3. Após o início do movimento, o grupo de recursos-alvo é criado, com base no modelo ARM gerado. O grupo de recursos de origem move-se para um *movimento comprometendo-se pendente* estado.

![Estado mostrando movimento de compromisso](./media/move-region-availability-zone/commit-move-pending.png)

Para comprometer e terminar o processo de mudança:

1. Em **Todas as regiões**, selecione o grupo de recursos > **Cometer movimento**
2. In **Move Resources**, selecione **Commit**.

> [!NOTE]
> Depois de cometer o movimento, o grupo de recursos de origem encontra-se num estado *pendente de origem Delete.*

## <a name="modify-target-settings"></a>Modificar as definições de destino

Se não quiser mover um recurso de origem, pode fazer qualquer um dos seguintes:

- Criar um recurso na região alvo com o mesmo nome e configurações que o recurso na região de origem.
- Criar um novo recurso equivalente na região alvo. Com exceção das definições especificadas, o recurso-alvo é criado com as mesmas definições que a fonte.
- Utilize um recurso existente na região alvo.

Modificar uma definição da seguinte forma:

1. Para modificar uma definição, selecione a entrada na coluna **de configuração destino** para o recurso.
2. Na página de **configuração destino,** especifique as definições de destino que pretende utilizar.
    As alterações são feitas apenas para o recurso que está a editar. É necessário atualizar os recursos dependentes separadamente.   
    
As definições exatas que modifica dependem do tipo de recurso. [Saiba mais](modify-target-settings.md) sobre a edição das definições do alvo.

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

1. Em **Todas as regiões**, selecione recursos com *movimento de iniciado* do estado pendente . Em seguida, **selecione Iniciar movimento**.
2. Em **Recursos move,** selecione **Iniciar movimento**.

    ![Selecione para o botão de movimento iniciado](./media/move-region-within-resource-group/initiate-move.png)

3. O progresso da pista na barra de notificações.


> [!NOTE]
> - Para os VMs, as VM réplicas são criadas na região alvo. A fonte VM é desligada e ocorre algum tempo de paragem (geralmente minutos).<br/>
> - O Resource Mover recria outros recursos utilizando os modelos ARM que foram preparados. Normalmente não há tempo de descanso.<br/> 
> - Para os equilibradores de carga, as regras da NAT não são copiadas. Crie-os na região alvo depois de cometer o movimento.
> - Para endereços IP públicos, a etiqueta de nome DNS não é copiada. Recrie a etiqueta depois de cometer o movimento.
> - Depois de prepararem os recursos, estão numa *ação pendente.*


## <a name="discard-or-commit"></a>Descartar ou comprometer-se?

Após o movimento inicial, pode decidir se quer cometer o movimento ou descartá-lo. 

- **Descartar:** Pode descartar um movimento se estiver a testar e não pretende mover o recurso de origem. Descartar o movimento devolve o recurso a um estado de *movimento iniciado pendente*.
- **Compromete-se**: Compromete-se a concluir a mudança para a região alvo. Após o compromisso, um recurso de origem estará num estado de *Eliminação de fonte pendente*, e pode decidir se pretende eliminá-lo.


## <a name="discard-the-move"></a>Descartar o movimento 

Pode descartar o movimento da seguinte forma:

1. Em **Todas as regiões**, selecione recursos com movimento de compromisso do estado *pendente*, e selecione Fora de **jogo .**
2. Em **Descartar movimento**, selecione **Descartar**.
3. O progresso da pista na barra de notificações.
4. Quando as notificações mostrarem que o movimento foi bem sucedido, selecione **Refresh**. 

> [!NOTE]
> Para os VMs, depois de descartarem os recursos, estão numa *ação iniciada pendente.*

## <a name="commit-the-move"></a>Cometa o movimento

Se quiser concluir o processo de mudança, cometa o movimento. 


1. Em **Todas as regiões**, selecione recursos com movimento de compromisso do estado *pendente*, e selecione **Commit move**.
2. Nos **recursos de compromisso**, selecione **Commit**.

    ![Página para comprometer recursos para finalizar movimento](./media/move-region-within-resource-group/commit-resources.png)

3. Acompanhe o progresso do compromisso na barra de notificações.

> [!NOTE]
> - Depois de cometer o movimento, os VMs param de se replicar. A fonte VM não é afetada pelo compromisso.
> - Cometer não afeta recursos de rede de fontes.
> - Após a sua commissão, os recursos encontram-se numa *fonte de Exclusão pendente.*

## <a name="configure-settings-after-the-move"></a>Configurar definições após o movimento

1. Uma vez que as etiquetas de nome DNS não são copiadas para endereços IP públicos, após a mudança é feita, navegue para os recursos-alvo e atualize o rótulo. 
2. Para os equilibradores de carga internos, uma vez que as regras da NAT não são copiadas, navegue para os recursos criados na região alvo e atualize as regras da NAT.
3. O serviço de Mobilidade não é desinstalado automaticamente a partir de VMs. Desinstale-o manualmente ou deixe-o se planeia mover o servidor novamente.
## <a name="delete-source-resources-after-commit"></a>Eliminar recursos de origem após cometer

Após a mudança, pode opcionalmente apagar recursos na região de origem. 

1. Em **Regiões Diferentes,** selecione o nome de cada recurso de origem que pretende eliminar.
2. Na página de propriedades para cada recurso, selecione **Delete**.

## <a name="delete-additional-resources-created-for-move"></a>Eliminar recursos adicionais criados para a mudança

Após a mudança, pode eliminar manualmente a recolha de movimentos e os recursos de Recuperação do Local que foram criados.

- A coleção de movimentos é escondida por defeito. Para vê-lo, precisa ligar recursos escondidos.
- O armazenamento da cache tem uma fechadura que deve ser eliminada, antes de poder ser eliminada.

Eliminar da seguinte forma: 

1. Localize os recursos no grupo de ```RegionMoveRG-<sourceregion>-<target-region>``` recursos, na região nascente.
2. Verifique se todos os VM e outros recursos de origem na recolha de movimentos foram movidos/eliminados. Isto garante que não existem recursos pendentes que os utilizem.
2. Eliminar os recursos:

    - O nome da coleção move é ```movecollection-<sourceregion>-<target-region>``` .
    - O nome da conta de armazenamento de cache é ```resmovecache<guid>```
    - O nome do cofre ```ResourceMove-<sourceregion>-<target-region>-GUID``` é.

## <a name="next-steps"></a>Passos seguintes


[Conheça](about-move-process.md) o processo de mudança.