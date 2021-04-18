---
title: Mover VMs Azure encriptados em todas as regiões através do Azure Resource Mover
description: Aprenda a mover VMs Azure encriptados para outra região usando o Azure Resource Mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 457c4c4752b4d78434b1fb90710472b1998f1c4e
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600697"
---
# <a name="tutorial-move-encrypted-azure-vms-across-regions"></a>Tutorial: Mover VMs Azure encriptados em regiões

Este artigo discute como mover máquinas virtuais Azure encriptadas (VMs) para uma região de Azure diferente, utilizando [o Azure Resource Mover](overview.md). 

VMS encriptados podem ser descritos como:

- VMs que têm discos com encriptação do disco Azure ativados. Para obter mais informações, consulte [Criar e encriptar uma máquina virtual do Windows utilizando o portal Azure.](../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- VMs que usam chaves geridas pelo cliente (CMKs) para encriptação em repouso, ou encriptação do lado do servidor. Para obter mais informações, consulte [o portal Azure para ativar a encriptação do lado do servidor com as teclas geridas pelo cliente para discos geridos](../virtual-machines/disks-enable-customer-managed-keys-portal.md).


Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Verifique os pré-requisitos. 
> * Para VMs com Encriptação de Disco Azure ativada, copiar chaves e segredos do cofre chave da região de origem para o cofre chave da região de destino.
> * Prepare-se para mover VMs e selecionar recursos na região de origem de onde pretende movê-los.
> * Resolver as dependências de recursos.
> * Para VMs com Encriptação do Disco Azure ativada, atribua manualmente o cofre da chave de destino. Para VMs que utilizam encriptação do lado do servidor com teclas geridas pelo cliente, atribua manualmente um conjunto de encriptação de disco na região de destino.
> * Mova o cofre de chaves ou o conjunto de encriptação do disco.
> * Prepare e mova o grupo de recursos de origem. 
> * Prepare e mova os outros recursos.
> * Decida se descarta ou comete o movimento. 
> * Opcionalmente, remova recursos na região de origem após a mudança.

> [!NOTE]
> Este tutorial mostra o caminho mais rápido para experimentar um cenário. Utiliza apenas as opções predefinidos. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar. Em seguida, inscreva-se no [portal Azure.](https://portal.azure.com)

## <a name="prerequisites"></a>Pré-requisitos

Requisito |Detalhes
--- | ---
**Permissões de subscrição** | Verifique se tem acesso *ao Proprietário* na subscrição que contém os recursos que pretende mover.<br/><br/> *Por que preciso de acesso ao proprietário?* A primeira vez que adiciona um recurso para um par específico de origem e destino numa subscrição Azure, o Resource Mover cria uma [identidade gerida atribuída ao sistema,](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)anteriormente conhecida como identidade de serviço gerido (MSI). Esta identidade é fidedigna pela subscrição. Antes de poder criar a identidade e atribuir-lhe as funções necessárias *(administrador* de acesso *ao contribuinte* e ao utilizador na subscrição de origem), a conta que utiliza para adicionar recursos necessita de permissões *do Proprietário* na subscrição. Para obter mais informações, consulte [as funções de administrador de subscrição clássica, funções de Azure e Ad AD.](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)
**Suporte VM** | Verifique se os VMs que pretende mover são suportados fazendo o seguinte:<li>[Verifique os](support-matrix-move-region-azure-vm.md#windows-vm-support) VMs do Windows suportados.<li>[Verifique as](support-matrix-move-region-azure-vm.md#linux-vm-support) versões de Linux VMs e kernel suportadas.<li>Verifique as definições [de computação suportada,](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings) [armazenamento](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)e [rede.](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings)
**Requisitos de cofre chave (Encriptação do disco Azure)** | Se tiver encriptação de disco Azure ativada para VMs, precisa de um cofre chave nas regiões de origem e destino. Para obter mais informações, consulte [Criar um cofre de chaves.](../key-vault/general/quick-create-portal.md)<br/><br/> Para os cofres-chave nas regiões de origem e destino, você precisa destas permissões:<li>Permissões-chave: Principais Operações de Gestão (Obter, Lista) e Operações Criptográficas (Desencriptação e Encriptação)<li>Permissões secretas: Operações de Gestão Secreta (Obter, Lista e Conjunto)<li>Certificado (Lista e Obter)
**Conjunto de encriptação de disco (encriptação do lado do servidor com CMK)** | Se estiver a utilizar VMs com encriptação do lado do servidor que utiliza um CMK, precisa de uma encriptação de disco definida tanto nas regiões de origem como de destino. Para obter mais informações, consulte [Criar um conjunto de encriptação de disco](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set).<br/><br/> Mover-se entre regiões não é suportado se estiver a usar módulo de segurança de hardware (teclas HSM) para chaves geridas pelo cliente.
**Quota-alvo da região** | A subscrição precisa de quota suficiente para criar os recursos que está a mover na região alvo. Se não tiver quota, [solicite limites adicionais.](../azure-resource-manager/management/azure-subscription-service-limits.md)
**Taxas da região alvo** | Verifique os preços e encargos associados à região-alvo para a qual está a mover os VMs. Utilize a [calculadora de preços.](https://azure.microsoft.com/pricing/calculator/)


## <a name="verify-permissions-in-the-key-vault"></a>Verifique permissões no cofre da chave

Se estiver a mover VMs que tenham encriptação de disco Azure ativada, tem de executar um script como mencionado na secção [copiar as teclas para a](#copy-the-keys-to-the-destination-key-vault) secção do cofre de chaves de destino. Os utilizadores que executam o script devem ter permissões apropriadas para o fazer. Para entender quais as permissões necessárias, consulte a tabela seguinte. Encontrará as opções para mudar as permissões indo ao cofre chave do portal Azure. Em **Definições**, selecione **políticas de acesso**.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png" alt-text="Screenshot da ligação &quot;Políticas de acesso&quot; no painel de definições do cofre da chave." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png":::

Se as permissões do utilizador não estiverem em vigor, **selecione Adicionar A Política de Acesso** e, em seguida, especifique as permissões. Se a conta de utilizador já tiver uma política, no **Utilizador,** desa estale as permissões de acordo com as instruções na tabela seguinte.

Os VMs Azure que utilizam encriptação do disco Azure podem ter as seguintes variações, e terá de definir as permissões de acordo com os seus componentes relevantes. Os VMs podem ter:
- Uma opção padrão onde o disco é encriptado apenas com segredos.
- Segurança adicional que utiliza uma [chave de encriptação (KEK)](../virtual-machines/windows/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek).

### <a name="source-region-key-vault"></a>Cofre chave da região de origem

Para os utilizadores que executam o script, desa estale permissões para os seguintes componentes: 

Componente | Permissões necessárias
--- | ---
Segredos |  *Get* <br></br> Selecione **As permissões**  >  **Secretas Operações de Gestão Secreta** e, em seguida, selecione **Get**. 
Chaves <br></br> Se estiver a usar um KEK, precisa destas permissões para além das permissões para segredos. | *Obter* e *Desencript* <br></br> Selecione **As**  >  **Operações de Gestão de Chaves de Teclas** e, em seguida, selecione **Get**. Em **Operações Criptográficas,** selecione **Desencript**.

### <a name="destination-region-key-vault"></a>Cofre chave da região de destino

Nas **políticas de Acesso,** certifique-se de que **a encriptação do disco Azure para encriptação de volume** está ativada. 

Para os utilizadores que executam o script, desa estale permissões para os seguintes componentes: 

Componente | Permissões necessárias
--- | ---
Segredos |  *Definir* <br></br> Selecione   >  **Operações de Gestão Secretas** e, em seguida, selecione **set**. 
Chaves <br></br> Se estiver a usar um KEK, precisa destas permissões para além das permissões para segredos. | *Obter*, *Criar* e *Encriptar* <br></br> Selecione **As**  >  **Operações de Gestão de Chaves de Teclas** e, em seguida, selecione **Get** and **Create** . Em **Operações Criptográficas,** selecione **Encrypt**.

<br>

Além das permissões anteriores, no cofre chave de destino, precisa adicionar permissões para a [Identidade do Sistema Gerido](./common-questions.md#how-is-managed-identity-used-in-resource-mover) que o Resource Mover utiliza para aceder aos recursos do Azure em seu nome. 

1. Em **Definições**, **selecione Adicionar Políticas de Acesso**. 
1. Em **Select principal,** procure o MSI. O nome MSI é ```movecollection-<sourceregion>-<target-region>-<metadata-region>``` . 
1. Para o MSI, adicione as seguintes permissões:

    Componente | Permissões necessárias
    --- | ---
    Segredos|  *Obter* e *Lista* <br></br> Selecione **As permissões**  >  **Secretas Operações de Gestão Secreta** e, em seguida, selecione **Get** and **List**. 
    Chaves <br></br> Se estiver a usar um KEK, precisa destas permissões para além das permissões para segredos. | *Obter* e *Lista* <br></br> Selecione **As**  >  **Operações de Gestão de Chaves de Teclas** e, em seguida, selecione **Get** and **List**.

<br>

### <a name="copy-the-keys-to-the-destination-key-vault"></a>Copie as chaves do cofre da chave de destino

Copie os segredos e chaves de encriptação do cofre da chave de origem para o cofre da chave de destino usando um [script](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1) que fornecemos.

- Executar o guião no PowerShell. Recomendamos que utilize a versão mais recente do PowerShell.
- Especificamente, o script requer estes módulos:
    - Az.Compute
    - Az.KeyVault (versão 3.0.0)
    - Contas Az.(versão 2.2.3)

Para executar o script, faça o seguinte:

1. Abra o [guião](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1) no GitHub.
1. Copie o conteúdo do script para um ficheiro local e nomeie-o *Copy-keys.ps1*.
1. Execute o script.
1. Inicie sessão no portal do Azure.
1. Nas listas de drop-down na janela **'Entradas** do utilizador', selecione a subscrição de origem, o grupo de recursos e o VM de origem e, em seguida, selecione a localização do alvo e os cofres-alvo para encriptação do disco e da chave.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/script-input.png" alt-text="Screenshot da janela 'Entradas do utilizador' para introduzir os valores do script." :::

1. Selecione o botão **Selecionar**. 
   
   Quando o script terminar de ser lançado, uma mensagem notifica-o de que o CopyKeys foi bem sucedido.

## <a name="prepare-vms"></a>Preparar VMs

1. Depois de verificar se os VM satisfazem os [pré-requisitos,](#prerequisites)certifique-se de que os VMs que pretende mover estão ligados. Todos os discos VM que pretende estar disponíveis na região de destino devem ser ligados e rubricados no VM.
1. Para garantir que os VMs possuem os mais recentes certificados de raiz fidedignos e uma lista atualizada de revogação de certificados (CRL), faça o seguinte:
    - Nos VMs do Windows, instale as atualizações mais recentes do Windows.
    - No Linux VMs, siga a orientação do distribuidor para que as máquinas tenham os certificados mais recentes e CRL. 
1. Para permitir a conectividade de saída dos VMs, faça qualquer uma das seguintes:
    - Se estiver a utilizar um representante de firewall baseado em URL para controlar a conectividade de saída, [permita o acesso aos URLs](support-matrix-move-region-azure-vm.md#url-access).
    - Se estiver a utilizar as regras do Grupo de Segurança de Rede (NSG) para controlar a conectividade de saída, crie estas [regras de marcação de serviço](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-the-resources-to-move"></a>Selecione os recursos para mover

- Pode selecionar qualquer tipo de recurso suportado em qualquer um dos grupos de recursos da região de origem que seleciona.  
- Você pode mover recursos para uma região alvo que está na mesma subscrição que a região de origem. Se quiser alterar a subscrição, pode fazê-lo depois de os recursos serem transferidos.

Para selecionar os recursos, faça o seguinte:

1. No portal Azure, procure **por recursos.** Em seguida, em **Serviços**, selecione **Azure Resource Mover**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/search.png" alt-text="Screenshot dos resultados da pesquisa para Azure Resource Mover no portal Azure." :::

1. No painel de **visão geral** do Azure Resource Mover, selecione **Mover-se através das regiões**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png" alt-text="Screenshot do botão &quot;Mover-se através das regiões&quot; para adicionar recursos para se deslocar para outra região." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png":::

1. No **painel de recursos move,** selecione o **separador Fonte + destino.** Em seguida, nas listas de drop-down, selecione a subscrição de origem e a região.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/source-target.png" alt-text="Página para selecionar a região de origem e destino.." :::

1. Em **Destino**, selecione a região onde pretende mover os VMs e, em seguida, selecione **Next**.

1. Selecione os **Recursos para mover** o separador e, em seguida, **selecione Selecione recursos**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-resources.png" alt-text="Screenshot do painel &quot;Move resources&quot; e botão &quot;Selecione recursos&quot;.]." :::

1. No painel **de recursos Select,** selecione os VMs que pretende mover. Como mencionado na secção [Select the resources to move,](#select-the-resources-to-move) só pode adicionar recursos que são suportados para uma mudança.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-vm.png" alt-text="Screenshot do painel &quot;Selecione recursos&quot; para selecionar VMs para mover." :::

    > [!NOTE]
    >  Neste tutorial, está a selecionar um VM que utiliza encriptação do lado do servidor (rayne-vm) com uma chave gerida pelo cliente e um VM com encriptação de disco ativado (rayne-vm-ade).

1. Selecione **Concluído**.
1. Selecione os **Recursos para mover** o separador e, em seguida, selecione **Seguinte**.
1. Selecione o separador **'Rever'** e, em seguida, verifique as definições de origem e destino. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/review.png" alt-text="Screenshot do painel para rever as definições de origem e destino." :::

1. **Selecione Proceder** para começar a adicionar os recursos.
1. Selecione o ícone de notificações para acompanhar o progresso. Depois de o processo terminar com sucesso, no painel **de notificações,** selecione **Recursos Adicionais para movimento**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png" alt-text="Screenshot do painel de notificações para confirmar que os recursos foram adicionados com sucesso." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png":::
    
1. Depois de selecionar a notificação, reveja os recursos na página **de Regiões.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-prepare-pending.png" alt-text="Screenshot de recursos adicionados com um estado de &quot;Prepare-se pendente&quot;." :::

> [!NOTE]
> - Os recursos que adiciona são colocados num estado *de preparação pendente.*
> - O grupo de recursos para os VMs é adicionado automaticamente.
> - Se modificar as entradas de **configuração destino** para utilizar um recurso que já existe na região de destino, o estado de recursos está definido para *Comprometer pendente*, porque não precisa de iniciar um movimento para ele.
> - Se quiser remover um recurso que foi adicionado, o método que utilizará depende de onde está no processo de movimento. Para obter mais informações, consulte [Gerir recolhas de movimentos e grupos de recursos.](remove-move-resources.md)


## <a name="resolve-dependencies"></a>Resolver dependências

1. Se algum dos recursos mostrar uma mensagem *de dependências validadas* na coluna **'Problemas',** selecione o botão **'Validar dependências'.**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png" alt-text="Screenshot mostrando o botão 'Validar dependências'." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png":::

    O processo de validação começa.
1. Se forem encontradas dependências, **selecione Adicionar dependências**.  

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png" alt-text="Screenshot do botão 'Adicionar dependências'." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png":::


1. No painel **de dependências adicionar,** deixe a opção padrão **Mostrar todas as dependências.**

    - **Mostrar todas as dependências** iterates através de todas as dependências diretas e indiretas de um recurso. Por exemplo, para um VM, mostra o NIC, a rede virtual, os grupos de segurança de rede (NSGs), e assim por diante.
    - **Mostrar que as dependências de primeiro nível só** mostram dependências diretas. Por exemplo, para um VM mostra o NIC, mas não a rede virtual.
 
1. Selecione os recursos dependentes que pretende adicionar e, em seguida, **selecione Adicionar dependências**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png" alt-text="Screenshot da lista de dependências e do botão 'Adicionar dependências'." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png":::

1. Valide as dependências novamente. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png" alt-text="Screenshot do painel para revalidar as dependências." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png":::

## <a name="assign-destination-resources"></a>Atribuir recursos de destino

É necessário atribuir manualmente recursos de destino associados à encriptação.

- Se estiver a mover um VM que tenha encriptação de disco Azure ativada, o cofre chave na região de destino aparece como uma dependência.
- Se estiver a mover um VM com encriptação do lado do servidor que utiliza CMKs, o conjunto de encriptação do disco na região de destino aparece como uma dependência. 
- Porque este tutorial demonstra mover um VM que tem encriptação de disco Azure ativada e que usa um CMK, tanto o cofre chave de destino como o conjunto de encriptação do disco aparecem como dependências.

Para atribuir os recursos de destino manualmente, faça o seguinte:

1. Na entrada do conjunto de encriptação do disco, selecione **Recurso não atribuído** na coluna de **configuração destino.**
1. Nas **definições de configuração**, selecione o conjunto de encriptação do disco de destino e, em seguida, selecione **Guardar as alterações**.
1. Pode guardar e validar dependências para o recurso que está a modificar, ou pode guardar apenas as alterações e, em seguida, validar tudo o que modifica ao mesmo tempo.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png" alt-text="Screenshot do painel de &quot;Configuração destino&quot; para guardar alterações na região de destino." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png":::

    Depois de ter adicionado o recurso de destino, o estado do conjunto de encriptação do disco é alterado para *cometer movimento pendente*.

1. Na entrada do cofre da chave, selecione **Recurso não atribuído** na coluna de **configuração destino.** Nas **definições de Configuração**, selecione o cofre da chave de destino e, em seguida, guarde as suas alterações. 

Nesta fase, o conjunto de encriptação do disco e os estados do cofre chave são alterados para *cometer movimento pendente*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png" alt-text="Screenshot do painel para preparar outros recursos." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png":::

Para comprometer e terminar o processo de movimentação de recursos de encriptação, faça o seguinte:

1. Em **Regiões 10,** selecione o recurso (conjunto de encriptação do disco ou cofre de chaves) e, em seguida, selecione **Commit move**.
1. In **Move Resources**, selecione **Commit**.

> [!NOTE]
> Depois de ter cometido o movimento, o estado do recurso muda para *eliminar a fonte pendente*.


## <a name="move-the-source-resource-group"></a>Mover o grupo de recursos de origem 

Antes de poder preparar e mover VMs, o grupo de recursos VM deve estar presente na região alvo. 

### <a name="prepare-to-move-the-source-resource-group"></a>Preparar para mover o grupo de recursos de origem

Durante o processo de preparação, o Resource Mover gera modelos do Azure Resource Manager (ARM) a partir das definições do grupo de recursos. Os recursos dentro do grupo de recursos não são afetados.

Para se preparar para mover o grupo de recursos de origem, faça o seguinte:

1. Em **Regiões,** selecione o grupo de recursos de origem e, em seguida, **selecione Prepare -**

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png" alt-text="Screenshot do botão 'Prepare- se' no painel 'Prepare recursos'." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png":::

1. Na **preparação dos recursos,** selecione **Prepare- se**.

> [!NOTE]
> Depois de ter preparado a mudança, o estado do grupo de recursos altera o *movimento Iniciar pendente*. 

 
### <a name="move-the-source-resource-group"></a>Mover o grupo de recursos de origem

Comece a mover o grupo de recursos de origem fazendo o seguinte:

1. No painel **de regiões Do Outro,** selecione o grupo de recursos e, em seguida, selecione **Iniciar o movimento**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png" alt-text="Screenshot do botão 'Iniciar movimento' no painel 'Across regions'." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png":::

1. No **painel de recursos move,** selecione **Iniciar movimento**. O estado do grupo de recursos altera o *movimento iniciar em curso*.   
1. Depois de iniciar o movimento, o grupo de recursos-alvo é criado, com base no modelo ARM gerado. O estado do grupo de recursos de origem altera o *movimento de Compromisso pendente*.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png" alt-text="Screenshot do painel &quot;Move resources&quot; mostrando o estado do grupo de recursos alterado para &quot;Cometer movimento pendente&quot;." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png":::

Para cometer o movimento e terminar o processo, faça o seguinte:

1. No painel **de regiões do Outro** Lado, selecione o grupo de recursos e, em seguida, selecione Commit **move**.
1. No **painel de recursos** de movimento, selecione **Commit**.

> [!NOTE]
> Depois de ter cometido o movimento, o estado do grupo de recursos de origem muda para *eliminar a fonte pendente*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png" alt-text="Screenshot do grupo de recursos de origem mostrando o estado alterado para 'Eliminar fonte pendente'." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png":::

## <a name="prepare-resources-to-move"></a>Preparar recursos para mover

Agora que os recursos de encriptação e o grupo de recursos de origem são movidos, pode preparar-se para mover outros recursos cujo estado atual é *Prepare-se pendente*.


1. No painel do Outro Lado das **Regiões,** valide o movimento novamente e resolva quaisquer problemas.
1. Se pretender editar as definições do alvo antes de iniciar o movimento, selecione o link na coluna **de configuração destino** para o recurso e, em seguida, edite as definições. Se editar as definições de VM do alvo, o tamanho do VM alvo não deve ser menor do que o tamanho do VM de origem.
1. Para recursos com um *Prepare o* estado pendente que pretende mover, selecione **Prepare -se**.
1. No painel **de recursos preparar,** selecione **Prepare.**

    - Durante a preparação, o agente de mobilidade Azure Site Recovery é instalado nos VMs para os replicar.
    - Os dados VM são replicados periodicamente na região alvo. Isto não afeta a fonte VM.
    - O Movimento de Recursos gera modelos ARM para os outros recursos de origem.

> [!NOTE]
> Depois de ter preparado os recursos, o estado deles muda para *Iniciar a mudança pendente*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png" alt-text="Screenshot do painel &quot;Prepare recursos&quot;, mostrando os recursos em &quot;Iniciar o movimento pendente&quot;." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png":::



## <a name="initiate-the-move"></a>Inicie o movimento

Agora que preparou os recursos preparados, pode iniciar a mudança. 

1. No painel **de regiões do Outro** Lado, selecione os recursos cujo estado é Iniciar movimento *pendente*, e, em seguida, selecione **Iniciar movimento**.
1. No **painel de recursos move,** selecione **Iniciar movimento**.
1. Acompanhe o progresso do movimento na barra de notificações.

    - Para os VMs, as VM réplicas são criadas na região alvo. A fonte VM é desligada e ocorre algum tempo de paragem (geralmente minutos).
    - O Recurso Mover recria outros recursos utilizando os modelos ARM preparados. Normalmente não há tempo de descanso.
    - Depois de ter mudado os recursos, o estado deles muda para *o Movimento Desempenhado pendente.*

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" alt-text="Screenshot de uma lista de recursos com um estado de &quot;Comprometer-se pendente&quot;." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" :::


## <a name="discard-or-commit"></a>Descartar ou comprometer-se?

Após o movimento inicial, pode decidir se deve cometer o movimento ou descartá-lo. 

- **Descartar:** Pode descartar um movimento se estiver a testá-lo e não quiser mover o recurso de origem. Descartar o movimento devolve o recurso para *Iniciar o estado pendente.*
- **Compromete-se**: Compromete-se a concluir a mudança para a região alvo. Depois de ter cometido um recurso de origem, o seu estado muda para *eliminar a fonte pendente*, e pode decidir se pretende eliminá-lo.


## <a name="discard-the-move"></a>Descartar o movimento 

Para descartar o movimento, faça o seguinte:

1. No **painel de regiões do Outro** Lado, selecione recursos cujo estado é *Cometer movimento pendente*, e, em seguida, selecione Fora de **jogo .**
1. No painel **de movimento de devoluções,** selecione **Descartar**.
1. Acompanhe o progresso do movimento na barra de notificações.


> [!NOTE]
> Depois de ter descartado os recursos, os estatutos de VM mudam para *Iniciar movimento pendente*.

## <a name="commit-the-move"></a>Cometa o movimento

Para completar o processo de movimento, compromete-se a agir fazendo o seguinte: 

1. No **painel de regiões do Outro** Lado, selecione recursos cujo estado é *Cometer movimento pendente*, e, em seguida, selecione **Commit move**.
1. No painel **de recursos do Commit,** selecione **Commit**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" alt-text="Screenshot de uma lista de recursos para comprometer recursos para finalizar a mudança." lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" :::

1. Acompanhe o progresso do compromisso na barra de notificações.

> [!NOTE]
> - Depois de cometeres o movimento, os VMs param de se replicar. A fonte VM não é afetada pelo compromisso.
> - O processo de compromisso não afeta os recursos de rede de origem.
> - Depois de ter cometido a mudança, os estatutos dos recursos mudam para *Eliminar fonte pendente*.



## <a name="configure-settings-after-the-move"></a>Configurar definições após o movimento

- O serviço de mobilidade não é desinstalado automaticamente a partir de VMs. Desinstale-o manualmente ou deixe-o se planeia mover o servidor novamente.
- Modifique as regras de controlo de acesso baseado em funções (RBAC) após a mudança.

## <a name="delete-source-resources-after-commit"></a>Eliminar recursos de origem após cometer

Após a mudança, pode opcionalmente apagar recursos na região de origem. 

1. No painel **de regiões Do Outro,** selecione cada recurso de origem que pretende eliminar e, em seguida, selecione **Eliminar a fonte**.
1. Na **fonte Delete,** reveja o que pretende eliminar e, em **Confirmar eliminar,** escreva **sim**. A ação é irreversível, por isso verifique minuciosamente!
1. Depois de escrever **sim,** selecione **Eliminar a fonte**.

> [!NOTE]
>  No portal Movimento de Recursos, não é possível eliminar grupos de recursos, cofres-chave ou instâncias do SQL Server. Tem de eliminar cada um individualmente da página de propriedades para cada recurso.


## <a name="delete-resources-that-you-created-for-the-move"></a>Eliminar recursos que criou para o movimento

Após a mudança, pode eliminar manualmente os recursos de recolha de movimentos e recuperação de locais que criou durante este processo.

- A coleção de movimentos é escondida por defeito. Para vê-lo, precisa ligar recursos escondidos.
- O armazenamento da cache tem uma fechadura que deve ser eliminada, antes de poder ser eliminada.

Para eliminar os seus recursos, faça o seguinte: 
1. Localize os recursos no grupo de ```RegionMoveRG-<sourceregion>-<target-region>``` recursos.
1. Verifique se todos os VM e outros recursos de origem na região de origem foram deslocados ou eliminados. Este passo garante que não existem recursos pendentes que os utilizem.
1. Eliminar os recursos:

    - Nome da coleção move: ```movecollection-<sourceregion>-<target-region>```
    - Nome da conta de armazenamento cache: ```resmovecache<guid>```
    - Nome do cofre: ```ResourceMove-<sourceregion>-<target-region>-GUID```
## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Moveu VMs Azure encriptados e seus recursos dependentes para outra região de Azure.


Como próximo passo, tente mover bases de dados Azure SQL e piscinas elásticas para outra região.

> [!div class="nextstepaction"]
> [Mover recursos Azure SQL](./tutorial-move-region-sql.md)
