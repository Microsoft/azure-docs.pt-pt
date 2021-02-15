---
title: Mover VMs Azure encriptados em regiões com Azure Resource Mover
description: Saiba como mover VMs Azure encriptados para outra região com Azure Resource Mover
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 014b4d09a991ae4d0bb31ec0b9adee0c9e3b3553
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361014"
---
# <a name="tutorial-move-encrypted-azure-vms-across-regions"></a>Tutorial: Mover VMs Azure encriptados em regiões

Neste artigo, aprenda a mover VMs Azure encriptados para uma região azure diferente usando [o Azure Resource Mover](overview.md). Eis o que queremos dizer com encriptação:

- VMs que têm discos com encriptação de disco Azure ativados. [Saiba mais](../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- Ou, VMs que usam chaves geridas pelo cliente (CMKs) para encriptação em repouso (encriptação do lado do servidor). [Saiba mais](../virtual-machines/disks-enable-customer-managed-keys-portal.md)


Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Verifique os pré-requisitos. 
> * Para VMs com encriptação de disco Azure ativada, copiar chaves e segredos do cofre chave da região de origem para o cofre chave da região de destino.
> * Prepare os VMs para movê-los e selecione recursos na região de origem que pretende mover.
> * Resolver as dependências de recursos.
> * Para VMs com encriptação de disco Azure ativada, atribua manualmente o cofre da chave de destino. Para VMs que utilizam encriptação do lado do servidor com teclas geridas pelo cliente, atribua manualmente um conjunto de encriptação de disco na região de destino.
> * Mova o conjunto de encriptação do cofre da chave e/ou do disco.
> * Prepare e mova o grupo de recursos de origem. 
> * Prepare e mova os outros recursos.
> * Decida se quer descartar ou cometer o movimento. 
> * Opcionalmente, remova recursos na região de origem após a mudança.

> [!NOTE]
> Os tutoriais mostram o caminho mais rápido para experimentar um cenário e usam opções padrão. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar. Em seguida, inscreva-se no [portal Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Pré-requisitos

**Requisito** |**Detalhes**
--- | ---
**Permissões de subscrição** | Verifique se tem acesso *ao Proprietário* na subscrição que contém os recursos que pretende mover.<br/><br/> **Por que preciso de acesso ao proprietário?** A primeira vez que adiciona um recurso para um par de origem e destino específico numa subscrição do Azure, o Resource Mover cria uma [identidade gerida atribuída ao sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (anteriormente conhecida como Managed Service Identify (MSI)) que é fidedigna pela subscrição. Para criar a identidade e atribuir-lhe a função necessária (administrador de dados e acesso ao utilizador na subscrição de origem), a conta que utiliza para adicionar recursos necessita de permissões *do Proprietário* na subscrição. [Saiba mais](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) sobre os papéis do Azure.
**Suporte VM** | Verifique se os VMs que pretende mover são suportados.<br/><br/> - [Verifique os](support-matrix-move-region-azure-vm.md#windows-vm-support) VMs do Windows suportados.<br/><br/> - [Verifique as](support-matrix-move-region-azure-vm.md#linux-vm-support) versões de Linux VMs e kernel suportadas.<br/><br/> - Verifique as definições [de computação](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings)suportada, [armazenamento](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)e [rede.](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings)
**Requisitos de cofre chave (encriptação do disco Azure)** | Se tiver encriptação de disco Azure ativada para VMs, além do cofre chave na região de origem, precisa de um cofre chave na região de destino. [Criar um cofre de chaves.](../key-vault/general/quick-create-portal.md)<br/><br/> Para os cofres-chave na região de origem e alvo, você precisa destas permissões:<br/><br/> - Permissões-chave: Principais Operações de Gestão (Obter, Lista); Operações criptográficas (Desencriptação e Encriptação).<br/><br/> - Permissões secretas: Operações de Gestão Secreta (Obter, Lista e Conjunto)<br/><br/> - Certificado (Lista e Obter).
**Conjunto de encriptação de disco (encriptação do lado do servidor com CMK)** | Se estiver a utilizar VMs com encriptação do lado do servidor utilizando um CMK, para além da encriptação do disco definida na região de origem, precisa de uma encriptação de disco definida na região de destino. [Criar um conjunto de encriptação de disco](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set).<br/><br/> Mover-se entre regiões não é suportado se estiver a usar chaves HSM para chaves geridas pelo cliente.
**Quota-alvo da região** | A subscrição precisa de quota suficiente para criar os recursos que está a mover na região alvo. Se não tiver quota, [solicite limites adicionais.](../azure-resource-manager/management/azure-subscription-service-limits.md)
**Taxas da região alvo** | Verifique os preços e os encargos associados à região-alvo para a qual está a mover VMs. Use a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para ajudá-lo.


## <a name="verify-user-permissions-on-key-vault-for-vms-using-azure-disk-encryption-ade"></a>Verifique as permissões do utilizador no cofre de chaves para VMS utilizando encriptação de disco Azure (ADE)

Se estiver a mover VMs que tenham encriptação de disco Azure ativada, tem de executar um script como mencionado [abaixo](#copy-the-keys-to-the-destination-key-vault) para o qual o utilizador que executa o script deve ter permissões apropriadas. Por favor, consulte a tabela abaixo para saber mais sobre as permissões necessárias. As opções para alterar as permissões podem ser encontradas navegando para o cofre chave no portal Azure, Em **Definições,** selecione **políticas de acesso**.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png" alt-text="Botão para abrir as políticas de acesso ao cofre." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png":::

Se não houver permissões do utilizador, selecione **Adicionar Política de Acesso** e especifique as permissões. Se a conta de utilizador já tiver uma política, no **Utilizador,** desa estale as permissões de acordo com a tabela abaixo.

Os VMs Azure que utilizam a ADE podem ter as seguintes variações e as permissões devem ser definidas em conformidade para os componentes relevantes.
- Opção padrão onde o disco é encriptado usando apenas segredos
- Segurança adicionada usando [chave de encriptação](../virtual-machines/windows/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek)

### <a name="source-region-keyvault"></a>Keyvault da região de origem

As permissões abaixo precisam de ser definidas para o utilizador executar o script 

**Componente** | **Permissão necessária**
--- | ---
Segredos|  Obter permissão <br> </br> Em **permissões secretas** >   **Operações de Gestão Secreta,** selecione **Get** 
Chaves <br> </br> Se estiver a usar a chave de encriptação chave (KEK) precisa desta permissão para além de segredos| Obter e Desencriptar permissão <br> </br> Nas   >  **Principais Permissões Operações de Gestão de Chaves**, selecione **Get**. Em **Operações Criptográficas,** selecione **Desencript**.

### <a name="destination-region-keyvault"></a>Keyvault da região de destino

Nas **políticas de Acesso,** certifique-se de que **a encriptação do disco Azure para encriptação de volume** está ativada. 

As permissões abaixo precisam de ser definidas para o utilizador executar o script 

**Componente** | **Permissão necessária**
--- | ---
Segredos|  Definir permissão <br> </br> Em **permissões secretas** >   **Operações de Gestão Secreta,** selecione **Conjunto** 
Chaves <br> </br> Se estiver a usar a chave de encriptação chave (KEK) precisa desta permissão para além de segredos| Obter, Criar e Encriptar a permissão <br> </br> Em **Principais Permissões**  >  **Operações de Gestão de Chaves,** selecione **Get** and **Create** . Em **Operações Criptográficas,** selecione **Encrypt**.

Além das permissões acima referidas, no cofre chave de destino é necessário adicionar permissões para a [Identidade do Sistema Gerido](./common-questions.md#how-is-managed-identity-used-in-resource-mover) que o Resource Mover utiliza para aceder aos recursos do Azure em seu nome. 

1. Em **Definições**, **selecione Adicionar Políticas de Acesso**. 
2. Em **Select principal,** procure o MSI. O nome MSI é ```movecollection-<sourceregion>-<target-region>-<metadata-region>``` . 
3. Adicione as permissões abaixo para o MSI

**Componente** | **Permissão necessária**
--- | ---
Segredos|  Obter e Listar permissão <br> </br> Em **permissões secretas** >   **Operações de Gestão Secreta,** selecione **Get** and **List** 
Chaves <br> </br> Se estiver a usar a chave de encriptação chave (KEK) precisa desta permissão para além de segredos| Obter, Obter, Lista de permissão <br> </br> Em **Principais Permissões**  >  **Operações de Gestão de Chaves**, selecione **Get** and **List**



### <a name="copy-the-keys-to-the-destination-key-vault"></a>Copie as chaves do cofre da chave de destino

Você precisa copiar os segredos de encriptação e chaves do cofre da chave de origem para o cofre da chave de destino, usando um script que fornecemos.

- Tens o guião no PowerShell. Recomendamos a execução da versão mais recente do PowerShell.
- Especificamente, o script requer estes módulos:
    - Az.Compute
    - Az.KeyVault (versão 3.0.0
    - Contas Az.(versão 2.2.3)

Execute o comando da seguinte forma:

1. Navegue para o [guião](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1) no GitHub.
2. Copie o conteúdo do script para um ficheiro local e nomeie-o *Copy-keys.ps1*.
3. Execute o script.
4. Inscreva-se no Azure.
5. No pop-up **user Input,** selecione a subscrição de origem, o grupo de recursos e o VM de origem. Em seguida, selecione a localização do alvo e os cofres-alvo para o disco e encriptação das chaves.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/script-input.png" alt-text="Apareça para inserir valores de script." :::


6. Quando o script termina, a saída do ecrã indica que copyKeys foi bem sucedido.

## <a name="prepare-vms"></a>Preparar VMs

1. Depois de [verificar se os VM satisfazem os requisitos,](#prerequisites)certifique-se de que os VMs que pretende mover estão ligados. Todos os discos VMs que pretende estar disponíveis na região de destino devem ser ligados e rubricados no VM.
3. Verifique se os VMs têm os mais recentes certificados de raiz fidedignos e uma lista atualizada de revogação de certificados (CRL). Para efetuar este procedimento:
    - Nos VMs do Windows, instale as atualizações mais recentes do Windows.
    - No Linux VMs, siga a orientação do distribuidor para que as máquinas tenham os certificados mais recentes e CRL. 
4. Permitir a conectividade de saída a partir de VMs da seguinte forma:
    - Se estiver a usar um proxy de firewall baseado em URL para controlar a conectividade de saída, permita o acesso a estes [URLs](support-matrix-move-region-azure-vm.md#url-access)
    - Se estiver a utilizar as regras do Grupo de Segurança de Rede (NSG) para controlar a conectividade de saída, crie estas [regras de marcação de serviço](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Selecione recursos para mover


- Pode selecionar qualquer tipo de recurso suportado em qualquer um dos grupos de recursos da região de origem que seleciona.  
- Move-se recursos para uma região-alvo que está na mesma subscrição que a região de origem. Se quiser alterar a subscrição, pode fazê-lo depois de os recursos serem transferidos.

Selecione os recursos da seguinte forma:

1. No portal Azure, procure *por recursos.* Em seguida, em **Serviços**, selecione **Azure Resource Mover**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/search.png" alt-text="Pesquisa de resultados para o transportador de recursos no portal Azure." :::

2. Em **Visão Geral,** clique **em Mover-se através das regiões**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png" alt-text="Botão para adicionar recursos para se mudar para outra região." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png":::

3. Em **Move resources** Fonte +  >  **destino,** selecione a subscrição de origem e região.
4. No **Destino**, selecione a região para a qual pretende mover os VMs. Em seguida, clique em **Seguinte**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/source-target.png" alt-text="Página para selecionar a região de origem e destino.." :::

5. Em **Recursos a mover,** clique em **Selecionar recursos.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-resources.png" alt-text="Botão para selecionar o recurso para mover.]." :::

6. Em **Recursos Selecionados,** selecione os VMs. Só é possível adicionar recursos que sejam [suportados para a mudança.](#prepare-vms) Em seguida, clique em **Fazer**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-vm.png" alt-text="Página para selecionar VMs para mover." :::

    > [!NOTE]
    >  Neste tutorial estamos a selecionar um VM que utiliza encriptação do lado do servidor (rayne-vm) com uma chave gerida pelo cliente, e um VM com encriptação de disco ativado (rayne-vm-ade).

7.  Em **Recursos a mover- se,** clique em **Seguinte**.
8. Em **Revisão,** verifique as definições de origem e destino. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/review.png" alt-text="Página para rever as definições e proceder com movimento." :::

9. Clique **em Continuar,** para começar a adicionar os recursos.
10. Selecione o ícone de notificações para acompanhar o progresso. Após o processo de adição terminar com sucesso, selecione **recursos adicionais para mover-se** nas notificações.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png" alt-text="A notificação para confirmar os recursos foi adicionada com sucesso." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png":::
    
    
11. Depois de clicar na notificação, reveja os recursos na página **das regiões.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-prepare-pending.png" alt-text="Páginas mostrando recursos adicionados com preparação pendente." :::

> [!NOTE]
> - Os recursos que adicionar são colocados num estado *de preparação pendente.*
> - O grupo de recursos para os VMs é adicionado automaticamente.
> - Se modificar as entradas de **configuração destino** para utilizar um recurso que já existe na região de destino, o estado de recurso está definido para *Comprometer pendente,* uma vez que não precisa de iniciar um movimento para o mesmo.
> - Se quiser remover um recurso que foi adicionado, o método para o fazer depende de onde está no processo de movimento. [Saiba mais](remove-move-resources.md).


## <a name="resolve-dependencies"></a>Resolver dependências

1. Se algum dos recursos mostrar uma mensagem *de dependências validadas* na coluna **'Problemas',** selecione o botão **'Validar dependências'.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png" alt-text="NButton para verificar dependências." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png":::

    O processo de validação começa.
2. Se as dependências forem encontradas, clique em **Adicionar dependências**  

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png" alt-text="Botão para adicionar dependências." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png":::


3. In **Add dependencies,** deixe a opção padrão **Mostrar todas as dependências.**

    - **Mostrar todas as dependências** iterates através de todas as dependências diretas e indiretas de um recurso. Por exemplo, para um VM mostra o NIC, a rede virtual, os grupos de segurança de rede (NSGs) etc.
    - **Mostrar que as dependências do primeiro nível só** mostram dependências diretas. Por exemplo, para um VM mostra o NIC, mas não a rede virtual.
 
4. Selecione os recursos dependentes que pretende adicionar > **Adicionar dependências**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png" alt-text="Selecione dependências da lista de dependências." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png":::

5. Validar as dependências novamente. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png" alt-text="Página para validar novamente." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png":::

## <a name="assign-destination-resources"></a>Atribuir recursos de destino

Os recursos de destino associados à encriptação precisam de atribuição manual.

- Se estiver a mover um VM que tem encriptação de disco Azure (ADE), o cofre chave na sua região de destino aparecerá como uma dependência.
- Se estiver a mover um VM que tenha encriptação do lado do servidor que utiliza teclas geridas à medida (CMKs), então a encriptação do disco definida na região de destino aparece como uma dependência. 
- Uma vez que este tutorial está a mover um VM com ADE ativado, e um VM usando um CMK, tanto o cofre chave de destino como o conjunto de encriptação de disco aparecem como dependências.

Atribuir manualmente da seguinte forma:

1. Na entrada do conjunto de encriptação do disco, selecione **Recurso não atribuído** na coluna de **configuração destino.**
2. Nas **definições de configuração**, selecione o conjunto de encriptação do disco de destino. Em seguida, **selecione Guardar as alterações**.
3. Pode selecionar para guardar e validar dependências para o recurso que está a modificar, ou pode simplesmente guardar as alterações e validar tudo o que modifica de uma só vez.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png" alt-text="Página para selecionar conjunto de encriptação de disco na região de destino." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png":::

    Depois de adicionar o recurso de destino, o estado do conjunto de encriptação do disco volta-se para *Cometer movimento pendente*.
3. Na entrada do cofre da chave, selecione **Recurso não atribuído** na coluna de **configuração destino.** **Definições de configuração**, selecione o cofre da chave de destino. Guarde as alterações. 

Nesta fase, tanto o conjunto de encriptação do disco como o estado do cofre da chave voltam-se para *o movimento Depenar pendente*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png" alt-text="Página para selecionar preparar para outros recursos." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png":::

Para comprometer e terminar o processo de movimento para recursos de encriptação.

1. Em **Regiões ,** selecione o recurso (conjunto de encriptação de disco ou cofre de chaves) > **Empenhar-se**.
2. In **Move Resources,** clique **em Cometer**.

> [!NOTE]
> Depois de cometer o movimento, o recurso encontra-se numa *fonte de Exclusão pendente.*


## <a name="move-the-source-resource-group"></a>Mover o grupo de recursos de origem 

Antes de poder preparar e mover VMs, o grupo de recursos VM deve estar presente na região alvo. 

### <a name="prepare-to-move-the-source-resource-group"></a>Preparar para mover o grupo de recursos de origem

Durante o processo de preparação, o Resource Mover gera modelos do Azure Resource Manager (ARM) utilizando as definições do grupo de recursos. Os recursos dentro do grupo de recursos não são afetados.

Prepare-se da seguinte forma:

1. Em **Regiões ,** selecione o grupo de recursos de origem > **Prepare- se**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png" alt-text="Prepare o grupo de recursos." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png":::

2. Na **preparação dos recursos,** clique **em Preparar.**

> [!NOTE]
> Depois de preparar o grupo de recursos, está no *movimento Iniciar pendente.* 

 
### <a name="move-the-source-resource-group"></a>Mover o grupo de recursos de origem

Inicie o movimento da seguinte forma:

1. Em **Regiões,** selecione o grupo de recursos > **Iniciar Movimento**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png" alt-text="Botão para iniciar movimento." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png":::

2. In **Move Resources,** clique em **Iniciar movimento**. O grupo de recursos move-se para um *estado iniciado em* progresso.   
3. Após o início do movimento, o grupo de recursos-alvo é criado, com base no modelo ARM gerado. O grupo de recursos de origem move-se para um *movimento comprometendo-se pendente* estado.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png" alt-text="Reveja o movimento de compromisso pendente de estado." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png":::

Para comprometer e terminar o processo de mudança:

1. Em **Regiões ,** selecione o grupo de recursos > **Empenhar-se**.
2. In **Move Resources,** clique **em Cometer**.

> [!NOTE]
> Depois de cometer o movimento, o grupo de recursos de origem encontra-se num estado *pendente de origem Delete.*

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png" alt-text="Reveja o movimento de eliminação pendente." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png":::

## <a name="prepare-resources-to-move"></a>Preparar recursos para mover

Agora que os recursos de encriptação e o grupo de recursos de origem são movidos, você pode se preparar para mover outros recursos que estão no Estado *de Preparação pendente.*


1. Em **Regiões,** valide novamente e resolva quaisquer problemas.
2. Se pretender editar as definições do alvo antes de iniciar o movimento, selecione o link na coluna **de configuração destino** para o recurso e edite as definições. Se editar as definições de VM do alvo, o tamanho do VM alvo não deve ser menor do que o tamanho do VM de origem.
3. **Selecione Prepare-se** para os recursos na *Prepareção pendente* estado que pretende mover.
3. Na **preparação de recursos,** selecione **Prepare**

    - Durante o processo de preparação, o agente de mobilidade de recuperação do local Azure é instalado em VMs, para os replicar.
    - Os dados de VM são replicados periodicamente na região alvo. Isto não afeta a fonte VM.
    - O Movimento de Recursos gera modelos ARM para os outros recursos de origem.

Depois de preparar recursos, estão numa *ação iniciada pendente.*

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png" alt-text="Página mostrando recursos em movimento iniciado pendente estado." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png":::



## <a name="initiate-the-move"></a>Inicie o movimento

Com os recursos preparados, pode agora iniciar a mudança. 

1. Em **Todas as regiões**, selecione recursos com *movimento de iniciado* do estado pendente . Em seguida, clique em **Iniciar movimento**.
2. Em **Recursos Move,** clique em **Iniciar movimento**.
3. O progresso da pista na barra de notificações.

    - Para os VMs, as VM réplicas são criadas na região alvo. A fonte VM é desligada e ocorre algum tempo de paragem (geralmente minutos).
    - O Resource Mover recria outros recursos utilizando os modelos ARM que foram preparados. Normalmente não há tempo de descanso.
    - Depois de moverem recursos, estão numa *ação pendente.*

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" alt-text="Página mostrando recursos em um movimento de compromisso pendente estado." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" :::


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

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" alt-text="Página para comprometer recursos para finalizar o movimento." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" :::

3. Acompanhe o progresso do compromisso na barra de notificações.

> [!NOTE]
> - Depois de cometer o movimento, os VMs param de se replicar. A fonte VM não é afetada pelo compromisso.
> - Cometer não afeta recursos de rede de fontes.
> - Após a sua commissão, os recursos encontram-se numa *fonte de Exclusão pendente.*



## <a name="configure-settings-after-the-move"></a>Configurar definições após o movimento

- O serviço de Mobilidade não é desinstalado automaticamente a partir de VMs. Desinstale-o manualmente ou deixe-o se planeia mover o servidor novamente.
- Modifique as regras de controlo de acesso baseado em funções (Azure RBAC) após a mudança.

## <a name="delete-source-resources-after-commit"></a>Eliminar recursos de origem após cometer

Após a mudança, pode opcionalmente apagar recursos na região de origem. 

1. Em **Regiões,** selecione cada recurso de origem que pretende eliminar. em seguida, **selecione Eliminar fonte**.
2. Na **fonte Delete,** reveja o que pretende eliminar e em **Confirmar eliminar**, escreva **sim**. A ação é irreversível, por isso verifique minuciosamente!
3. Depois de escrever **sim**, selecione **Eliminar fonte**.

> [!NOTE]
>  No portal Movimento de Recursos, não é possível eliminar grupos de recursos, cofres-chave ou servidores SQL Server. É necessário eliminá-los individualmente da página de propriedades para cada recurso.


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
> * Moveu VMs Azure encriptados e seus recursos dependentes para outra região de Azure.


Agora, tentando mover bases de dados Azure SQL e piscinas elásticas para outra região.

> [!div class="nextstepaction"]
> [Mover recursos Azure SQL](./tutorial-move-region-sql.md)
