---
title: Mover VMs Azure em regiões com Azure Resource Mover
description: Saiba como mover os VMs do Azure para outra região com o Azure Resource Mover
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: d208a4a86896c81982aa2b10ca7ce5e7a6773c05
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820217"
---
# <a name="tutorial-move-azure-vms-across-regions"></a>Tutorial: Mover VMs Azure em regiões

Neste artigo, aprenda a mover VMs Azure, e recursos relacionados de rede/armazenamento, para uma região Azure diferente, utilizando [o Azure Resource Mover](overview.md).

> [!NOTE]
> A Azure Resource Mover está atualmente em pré-visualização pública.


Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Verifique os pré-requisitos e requisitos.
> * Selecione os recursos que pretende mover.
> * Resolver as dependências de recursos.
> * Prepare e mova o grupo de recursos de origem. 
> * Prepare e mova os outros recursos.
> * Decida se quer descartar ou cometer o movimento. 
> * Opcionalmente, remova recursos na região de origem após a mudança.

> [!NOTE]
> Os tutoriais mostram o caminho mais rápido para experimentar um cenário e usam opções padrão. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar. Em seguida, inscreva-se no [portal Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Pré-requisitos

-  Verifique se tem acesso *ao Proprietário* na subscrição que contém os recursos que pretende mover.
    - A primeira vez que adiciona um recurso para um par de origem e destino específico numa subscrição do Azure, o Resource Mover cria uma [identidade gerida atribuída ao sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (anteriormente conhecida como Managed Service Identify (MSI)) que é fidedigna pela subscrição.
    - Para criar a identidade e atribuir-lhe a função necessária (administrador de Acesso ao Utilizador ou Colaborador na subscrição de origem), a conta que utiliza para adicionar recursos necessita de permissões *do Proprietário* na subscrição. [Saiba mais](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) sobre os papéis do Azure.
- A subscrição precisa de quota suficiente para criar os recursos que está a mover na região alvo. Se não tiver quota, [solicite limites adicionais.](../azure-resource-manager/management/azure-subscription-service-limits.md)
- Verifique os preços e os encargos associados à região-alvo para a qual está a mover VMs. Use a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para ajudá-lo.
    

## <a name="check-vm-requirements"></a>Verifique os requisitos de VM

1. Verifique se os VMs que pretende mover são suportados.

    - [Verifique os](support-matrix-move-region-azure-vm.md#windows-vm-support) VMs do Windows suportados.
    - [Verifique as](support-matrix-move-region-azure-vm.md#linux-vm-support) versões de Linux VMs e kernel suportadas.
    - Verifique as definições [de computação suportada,](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings) [armazenamento](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)e [rede.](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings)
2. Verifique se os VMs que pretende mover estão ligados.
3. Certifique-se de que os VMs têm os mais recentes certificados de raiz fidedignos e uma lista atualizada de revogação de certificados (CRL). Para efetuar este procedimento:
    - Nos VMs do Windows, instale as atualizações mais recentes do Windows.
    - No Linux VMs, siga a orientação do distribuidor para que as máquinas tenham os certificados mais recentes e CRL. 
4. Permitir a conectividade de saída a partir de VMs:
    - Se estiver a usar um proxy de firewall baseado em URL para controlar a conectividade de saída, permita o acesso a estes [URLs](support-matrix-move-region-azure-vm.md#url-access)
    - Se estiver a utilizar as regras do Grupo de Segurança de Rede (NSG) para controlar a conectividade de saída, crie estas [regras de marcação de serviço](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources"></a>Selecione recursos 

Selecione os recursos que pretende mover.

- Todos os tipos de recursos suportados em grupos de recursos dentro da região de origem selecionada são apresentados.
- Não são mostrados recursos que já tenham sido adicionados para atravessar regiões.
- Você move recursos para uma região alvo na mesma subscrição que a região de origem. Se quiser alterar a subscrição, pode fazê-lo depois de os recursos serem transferidos.

1. No portal Azure, procure *por recursos.* Em seguida, em **Serviços**, selecione **Azure Resource Mover**.

    ![Pesquisar resultados para o transportador de recursos no portal Azure](./media/tutorial-move-region-virtual-machines/search.png)

2. In **Overview**, clique **em Começar**.

    ![Botão para adicionar recursos para se mudar para outra região](./media/tutorial-move-region-virtual-machines/get-started.png)

3. Em **Move resources** Fonte +  >  **destino,** selecione a subscrição de origem e região.
4. No **Destino**, selecione a região para a qual pretende mover os VMs. Em seguida, clique em **Seguinte**.

    ![Página para selecionar origem e região de destino](./media/tutorial-move-region-virtual-machines/source-target.png)

6. Em **Recursos a mover,** clique em **Selecionar recursos.**
7. Em **Recursos Selecionados,** selecione o VM. Só é possível adicionar [recursos suportados para a mudança.](#check-vm-requirements) Em seguida, clique em **Fazer**.

    ![Página para selecionar VMs para mover](./media/tutorial-move-region-virtual-machines/select-vm.png)

8.  Em **Recursos a mover- se,** clique em **Seguinte**.
9. Em **Comentário + Adicionar,** verifique as definições de origem e destino. 

    ![Página para rever definições e proceder com movimento](./media/tutorial-move-region-virtual-machines/review.png)
10. Clique **em Continuar,** para começar a adicionar os recursos.
11. Após o processo de adição terminar com sucesso, clique em **Adicionar recursos para se mover** no ícone de notificação.
12. Depois de clicar na notificação, reveja os recursos na página **das regiões.**

> [!NOTE]
> - Os recursos adicionais estão num estado pendente de *preparação.*
> - Se quiser remover um recurso de uma recolha de movimentos, o método para o fazer depende de onde estiver no processo de movimento. [Saiba mais](remove-move-resources.md).

## <a name="resolve-dependencies"></a>Resolver dependências

1. Se os recursos mostrarem uma mensagem *de dependências validadas* na coluna **'Problemas',** clique no botão **'Validar dependências'.** O processo de validação começa.
2. Se forem encontradas dependências, clique em **Adicionar dependências**. 
3. Nas **dependências de adicionar**, selecione os recursos dependentes > Adicionar **dependências**. Monitorize o progresso nas notificações.

    ![Adicionar dependências](./media/tutorial-move-region-virtual-machines/add-dependencies.png)

4. Adicione dependências adicionais se necessário e valide as dependências novamente. 
    ![Página para adicionar dependências adicionais](./media/tutorial-move-region-virtual-machines/add-additional-dependencies.png)

4. Na página **do Across regions,** verifique se os recursos estão agora num Estado pendente de *Preparação,* sem problemas.

    ![Página mostrando recursos na preparação do estado pendente](./media/tutorial-move-region-virtual-machines/prepare-pending.png)

> [!NOTE]
> Se pretender editar as definições do alvo antes de iniciar o movimento, selecione o link na coluna **de configuração destino** para o recurso e edite as definições. Se editar as definições de VM do alvo, o tamanho do VM alvo não deve ser menor do que o tamanho do VM de origem.  

## <a name="move-the-source-resource-group"></a>Mover o grupo de recursos de origem 

Antes de poder preparar e mover VMs, o grupo de recursos VM deve estar presente na região alvo. 

### <a name="prepare-to-move-the-source-resource-group"></a>Preparar para mover o grupo de recursos de origem

Durante o processo de preparação, o Resource Mover gera modelos do Azure Resource Manager (ARM) utilizando as definições do grupo de recursos. Os recursos dentro do grupo de recursos não são afetados.

Prepare-se da seguinte forma:

1. Em **Regiões ,** selecione o grupo de recursos de origem > **Prepare- se**.
2. Na **preparação dos recursos,** clique **em Preparar.**

    ![Preparar grupo de recursos](./media/tutorial-move-region-virtual-machines/prepare-resource-group.png)

> [!NOTE]
> Depois de preparar o grupo de recursos, está no *movimento Iniciar pendente.* 

 
### <a name="move-the-source-resource-group"></a>Mover o grupo de recursos de origem

Inicie o movimento da seguinte forma:

1. Em **Regiões,** selecione o grupo de recursos > **Iniciar Movimento**
2. In **Move Resources,** clique em **Iniciar movimento**. O grupo de recursos move-se para um *estado iniciado em* progresso.
3. Após o início do movimento, o grupo de recursos-alvo é criado, com base no modelo ARM gerado. O grupo de recursos de origem move-se para um *movimento comprometendo-se pendente* estado.

    ![Clique no botão de movimento iniciado](./media/tutorial-move-region-virtual-machines/commit-move-pending.png)

Para comprometer e terminar o processo de mudança:

1. Em **Regiões ,** selecione o grupo de recursos > **Empenhar-se**.
2. In **Move Resources,** clique **em Cometer**.

> [!NOTE]
> Depois de cometer o movimento, o grupo de recursos de origem encontra-se num estado *pendente de origem Delete.*

## <a name="prepare-resources-to-move"></a>Preparar recursos para mover

Agora que o grupo de recursos de origem está movido, pode preparar-se para mover os outros recursos.

1. Em **Regiões,** selecione os recursos que pretende preparar. 

    ![Página para selecionar preparar para outros recursos](./media/tutorial-move-region-virtual-machines/prepare-other.png)

2. **Selecione Preparar**. 

> [!NOTE]
> - Durante o processo de preparação, o agente de mobilidade de recuperação do local Azure é instalado em VMs, para os replicar.
> - Os dados de VM são replicados periodicamente na região alvo. Isto não afeta a fonte VM.
> - O Movimento de Recursos gera modelos ARM para os outros recursos de origem.
> - Depois de preparar recursos, estão numa *ação iniciada pendente.*

![Página mostrando recursos no início do movimento pendente estado](./media/tutorial-move-region-virtual-machines/initiate-move-pending.png)


## <a name="initiate-the-move"></a>Inicie o movimento

Com os recursos preparados, pode agora iniciar a mudança. 

1. Em **Todas as regiões**, selecione recursos com *movimento de iniciado* do estado pendente . Em seguida, clique em **Iniciar movimento**.
2. Em **Recursos Move,** clique em **Iniciar movimento**.

    ![Clique para o botão de movimento iniciado](./media/tutorial-move-region-virtual-machines/initiate-move.png)

3. O progresso da pista na barra de notificações.

> [!NOTE]
> - Para os VMs, as VM réplicas são criadas na região alvo. A fonte VM é desligada e ocorre algum tempo de paragem (geralmente minutos).
> - O Resource Mover recria outros recursos utilizando os modelos ARM que foram preparados. Normalmente não há tempo de descanso.
> - Depois de moverem recursos, estão numa *ação pendente.*

![Página mostrando recursos em *Eliminar fonte pendente* estado](./media/tutorial-move-region-virtual-machines/delete-source-pending.png)


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
> Após a descarte dos recursos, os VMs estão em *movimento iniciado pendente.*

## <a name="commit-the-move"></a>Cometa o movimento

Se quiser concluir o processo de mudança, cometa o movimento. 

1. Em **Todas as regiões**, selecione recursos com movimento de compromisso do estado *pendente*, e clique em **Cometer movimento**.
2. Nos **recursos de cometer,** clique **em Cometer**.

    ![Página para comprometer recursos para finalizar movimento](./media/tutorial-move-region-virtual-machines/commit-resources.png)

3. Acompanhe o progresso do compromisso na barra de notificações.

> [!NOTE]
> - Depois de cometer o movimento, os VMs param de se replicar. A fonte VM não é afetada pelo compromisso.
> - Cometer não afeta recursos de rede de fontes.
> - Após a sua commissão, os recursos encontram-se numa *fonte de Exclusão pendente.*

![Página mostrando recursos em *Eliminar fonte pendente* estado](./media/tutorial-move-region-virtual-machines/delete-source-pending.png)


## <a name="configure-settings-after-the-move"></a>Configurar definições após o movimento

- O serviço de Mobilidade não é desinstalado automaticamente a partir de VMs. Desinstale-o manualmente ou deixe-o se planeia mover o servidor novamente.
- Modifique as regras de controlo de acesso baseado em funções (Azure RBAC) após a mudança.


## <a name="delete-source-resources-after-commit"></a>Eliminar recursos de origem após cometer

Após a mudança, pode opcionalmente apagar recursos na região de origem. 

> [!NOTE]
> Alguns recursos, por exemplo, cofres-chave e servidores SQL Server, não podem ser eliminados do portal e devem ser eliminados da página de propriedade de recursos.

1. Em **Regiões Diferentes,** clique no nome do recurso de origem que pretende eliminar.
2. **Selecione Eliminar a fonte**.

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

Neste tutorial:

> [!div class="checklist"]
> * Mudou os VMs de Azure para outra região de Azure.
> * Os recursos transferidos associados aos VMs para outra região.

Agora, tentando mover bases de dados Azure SQL e piscinas elásticas para outra região.

> [!div class="nextstepaction"]
> [Mover recursos Azure SQL](./tutorial-move-region-sql.md)