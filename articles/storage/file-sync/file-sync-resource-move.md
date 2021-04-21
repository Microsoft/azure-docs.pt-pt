---
title: Movimentos de recursos de Arquivo Azure e alterações na topologia
description: Saiba como mover recursos sincronizados entre grupos de recursos, subscrições e inquilinos do Azure Ative Directory (AAD).
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fb59b8d48d8411ade620304fbd143d86ac5ae919
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797284"
---
# <a name="move-azure-file-sync-resources-to-a-different-resource-group-subscription-or-aad-tenant"></a>Mover recursos Azure File Sync para um grupo de recursos diferente, subscrição ou inquilino AAD

Este artigo descreve como fazer alterações ao grupo de recursos, subscrição ou inquilino do Azure Ative Directory (AAD) para os seus recursos em nuvem Azure File Sync e contas de armazenamento Azure.

Ao planear fazer alterações nos recursos da nuvem Azure File Sync, é importante considerar os recursos de armazenamento ao mesmo tempo. Existem os seguintes recursos:

**Recursos Azure File Sync (em ordem hierárquica)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-sync-service.png" border="false"::: Serviço de Sincronização de Armazenamento
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-registered-servers.png" border="false"::: Servidor registado
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-sync-group.png" border="false"::: Grupo sincronizado
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-cloud-endpoint.png" border="false"::: Ponto final da nuvem
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-server-endpoint.png" border="false"::: Ponto final do servidor

No Azure File Sync, o único recurso capaz de mover é o recurso Storage Sync Service. Quaisquer subreurendas estão ligadas ao seu progenitor e não podem mudar-se para outro Serviço de Sincronização de Armazenamento.

**Recursos de armazenamento Azure (em ordem hierárquica)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-account.png" border="false"::: Conta de armazenamento
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-file-share.png" border="false"::: Partilha de ficheiros

No Azure Storage, o único recurso capaz de mover é a conta de armazenamento. Uma partilha de ficheiros Azure, como subresource, não pode mover-se para uma conta de armazenamento diferente.

## <a name="supported-combinations"></a>Combinações suportadas

Ao planear um movimento de recursos, a conta de armazenamento e o recurso Azure File Sync de nível superior, chamado *De Serviço de Sincronização de Armazenamento,* devem ser considerados em conjunto.

Como uma boa prática, o Serviço de Sincronização de Armazenamento e as contas de armazenamento que têm partilhas de ficheiros sincronizadas, devem sempre residir na mesma subscrição. Estas combinações são suportadas:

* O Serviço de Sincronização de Armazenamento e as contas de armazenamento estão localizados em **diferentes grupos de recursos** (o mesmo inquilino Azure)
* O Serviço de Sincronização de Armazenamento e as contas de armazenamento estão localizados em **diferentes subscrições** (o mesmo inquilino do Azure)

> [!IMPORTANT]
> Através de diferentes combinações de movimentos, um Serviço de Sincronização de Armazenamento e contas de armazenamento podem acabar em diferentes subscrições, governadas por diferentes inquilinos da AAD. O Sync até parece estar a funcionar, mas esta não é uma configuração suportada. O Sync pode parar no futuro sem capacidade de voltar a trabalhar.

Ao planear a sua mudança de recursos, existem diferentes considerações para [se deslocar dentro do mesmo inquilino da AAD](#move-within-the-same-azure-active-directory-tenant) e passar para um inquilino diferente da [AAD.](#move-to-a-new-azure-active-directory-tenant) Ao mover os inquilinos da AAD, mova sempre os recursos de sincronização e armazenamento juntos.

### <a name="move-within-the-same-azure-active-directory-tenant"></a>Mover-se dentro do mesmo inquilino do Azure Ative Directory

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-small.png" alt-text="Uma imagem que mostra o portal Azure para um recurso de Serviço de Sincronização de Armazenamento, com o comando Move expandido. Mostra as opções de movimento do grupo de recursos e de movimento de subscrição." lightbox="media/storage-sync-resource-move/storage-sync-resource-move.png":::
    :::column-end:::
    :::column:::
        Uma forma conveniente de mover um recurso do Serviço de Sincronização de Armazenamento é utilizar o portal Azure. Navegue para o Serviço de Sincronização de Armazenamento que pretende mover-se e selecione **Mover-se** da barra de comando. Os mesmos passos aplicam-se à movimentação de uma conta de armazenamento. Você também pode mover todos os recursos em um grupo de recursos desta forma. A movimentação de todo um grupo de recursos é recomendada quando se tem o Serviço de Sincronização de Armazenamento e todas as suas contas de armazenamento utilizadas neste grupo de recursos.
    :::column-end:::
:::row-end:::

> [!WARNING]
> Quando se move um recurso de conta de armazenamento, a sincronização para imediatamente. Tem de autorizar manualmente a sincronização para aceder às contas de armazenamento relevantes na nova subscrição. A secção [de autorização de acesso ao armazenamento Azure File Sync](#azure-file-sync-storage-access-authorization) fornecerá as etapas necessárias.

### <a name="move-to-a-new-azure-active-directory-tenant"></a>Mude-se para um novo inquilino do Azure Ative Directory

Recursos individuais como um Serviço de Sincronização de Armazenamento ou contas de armazenamento, não podem mover-se por si mesmos para um inquilino AAD diferente. Apenas as assinaturas Azure podem mover inquilinos da AAD. Pense na sua estrutura de subscrição no novo inquilino da AAD. Pode utilizar uma subscrição dedicada para Azure File Sync. 

1. Crie uma subscrição Azure (ou determine uma existente no antigo inquilino que deve mover-se.
1. [Realize um movimento de subscrição dentro do mesmo inquilino AAD](#move-within-the-same-azure-active-directory-tenant) do seu Serviço de Sincronização de Armazenamento e de todas as contas de armazenamento associadas.
1. O Sync vai parar. Complete o seu movimento de inquilino imediatamente ou [restaure a capacidade de sincronização de aceder às contas de armazenamento que se moveram.](#azure-file-sync-storage-access-authorization) Você pode então mudar-se para o novo inquilino da AAD mais tarde.

Uma vez que todos os recursos relacionados Azure File Sync tenham sido sequestrados na sua própria subscrição, você está pronto para mover toda a subscrição para o inquilino AAD alvo. O [guia de subscrição](../../role-based-access-control/transfer-subscription.md) de transferência permite-lhe planear e executar tal transferência.

> [!WARNING]
> Quando transfere uma subscrição de um inquilino para outro, a sincronização para imediatamente. Tem de autorizar manualmente a sincronização para aceder às contas de armazenamento relevantes na nova subscrição. A secção [de autorização de acesso ao armazenamento Azure File Sync](#azure-file-sync-storage-access-authorization) fornecerá as etapas necessárias.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant.png" alt-text="Uma imagem mostrando o portal Azure, a lâmina de visão geral da subscrição, destacando o comando da barra de ferramentas change directy no centro, topo da página." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant-expanded.png":::
    :::column-end:::
    :::column:::
        Está pronto para iniciar a migração assim que tiver um plano e as permissões necessárias:
        1. No portal Azure, navegue para a sua subscrição, **folha de visão geral.**
        1. Selecione **Alterar o diretório**
        1. Siga os passos do assistente para atribuir o novo inquilino da AAD.
    :::column-end:::
:::row-end:::

## <a name="azure-file-sync-storage-access-authorization"></a>Autorização de acesso ao armazenamento de Azure File Sync

Quando as contas de armazenamento são transferidas para uma nova subscrição ou são transferidas dentro de uma subscrição para um novo inquilino do Azure Ative Directory (AAD), a sincronização irá parar. O acesso baseado em funções (RBAC) é usado para autorizar o Azure File Sync a aceder a uma conta de armazenamento e estas atribuições de funções não são migradas com os recursos.

### <a name="azure-file-sync-service-principal"></a>Diretor de serviço Azure File Sync

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered-small.png" alt-text="Uma imagem mostrando o portal Azure, gestão de subscrição, fornecedores de recursos registados." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered.png":::
    :::column-end:::
    :::column:::
        O principal do serviço Azure File Sync deve existir no seu inquilino AAD antes de autorizar o acesso sincronizado a uma conta de armazenamento. </br></br> Quando cria hoje uma nova subscrição do Azure, o fornecedor de recursos Azure File Sync *Microsoft.StorageSync* é automaticamente registado na sua subscrição. O registo do fornecedor de recursos disponibilizará um *principal serviço* para sincronização no inquilino do Azure Ative Directory que rege a subscrição. Um titular de serviço é semelhante a uma conta de utilizador no seu AAD. Pode utilizar o principal do serviço Azure File Sync para autorizar o acesso aos recursos através do controlo de acesso baseado em funções (RBAC). A única sincronização de recursos a que precisa de acesso são as suas contas de armazenamento que contêm as ações de ficheiros que supostamente sincronizam. *Microsoft.StorageSync* deve ser atribuído à função incorporada **Leitor e acesso de dados** na conta de armazenamento. </br></br> Esta atribuição é feita automaticamente através do contexto de utilizador do utilizador registado quando adiciona uma partilha de ficheiros a um grupo de sincronização, ou seja, cria um ponto final em nuvem. Quando uma conta de armazenamento se desloca para uma nova subscrição, ou inquilino da AAD, esta atribuição de funções é perdida e [deve ser restabelecida manualmente.](#establish-sync-access-to-a-storage-account)
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Se a subscrição target Azure não foi criada recentemente, verifique se o fornecedor de recursos *Microsoft.StorageSync* está registado na subscrição. Se não for, adicione manualmente na mesma lâmina do portal.

### <a name="establish-sync-access-to-a-storage-account"></a>Estabelecer acesso sincronizado a uma conta de armazenamento

O [principal do serviço Azure File Sync](#azure-file-sync-service-principal) deve ser utilizado para autorizar o acesso a uma conta de armazenamento através de um controlo de acesso baseado em funções (RBAC). *Microsoft.StorageSync* deve ser atribuído à função incorporada **Leitor e acesso de dados** na conta de armazenamento. 

Esta atribuição é normalmente feita automaticamente através do contexto de utilizador do utilizador iniciado quando adiciona uma partilha de ficheiros a um grupo de sincronização, ou seja, cria um ponto final em nuvem. No entanto, quando uma conta de armazenamento se muda para uma nova subscrição ou inquilino da AAD, esta atribuição de funções é perdida e deve ser restabelecida manualmente.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-assign-rbac.png" alt-text="Uma imagem que exibe o principal do serviço Microsoft.StorageSync atribuído à função de acesso ao Leitor e aos Dados numa conta de armazenamento":::
    :::column-end:::
    :::column:::
        No portal Azure, navegue para a conta de armazenamento a que necessita para reautorizar o acesso sincronizado. <ol><li>Selecione **o controlo de acesso (IAM)** na tabela de conteúdos à esquerda.</li><li>Selecione o separador de atribuições de Função para a lista dos utilizadores e aplicações (principais serviços) que têm acesso à sua conta de armazenamento.</li><li>Selecione **Adicionar**</li><li>No **campo Role**, selecione **Reader and Data Access**.</li><li>No **campo Select**, *digite Microsoft.StorageSync,* selecione a função e clique em **Guardar**.</li></ol>
    :::column-end:::
:::row-end:::

## <a name="move-to-a-different-azure-region"></a>Mude-se para uma região de Azure diferente

O Serviço de *Sincronização de Armazenamento de* Recursos de Arquivo Azure e as contas de armazenamento que contêm ações de ficheiros que estão a sincronizar, têm uma região Azure em que estão implantadas. Determina-se essa região quando se cria um recurso. A região do Serviço de Sincronização de Armazenamento e os recursos da conta de armazenamento devem coincidir. Estas regiões não podem ser alteradas em qualquer tipo de recurso após a sua criação.

Atribuir uma região diferente a um recurso é diferente de uma região de [fail-over](#region-fail-over), que pode ser suportada, dependendo da definição de redundância da sua conta de armazenamento.

## <a name="region-fail-over"></a>Região desabar

[O armazenamento Azure oferece opções de geo-redundância](../common/storage-redundancy.md#geo-redundant-storage) para uma conta de armazenamento. Estas opções de redundância podem criar problemas para contas de armazenamento utilizadas com a Azure File Sync. A razão principal é que a replicação entre regiões geograficamente distantes não é realizada pelo Azure File Sync, mas por uma tecnologia de replicação de armazenamento incorporada no subsistema de armazenamento em Azure. Não pode ter uma compreensão do estado de aplicação e o Azure File Sync é uma aplicação com ficheiros sincronizados de e para ações de ficheiros Azure a qualquer momento. Se optar por alguma destas opções de redundância de armazenamento geograficamente desembolsadas, não perderá todos os seus dados num desastre em larga escala. No entanto, é necessário antecipar a perda de [dados](../common/storage-disaster-recovery-guidance.md#anticipate-data-loss).

> [!CAUTION]
> O fail-over nunca é um substituto adequado para o fornecimento dos seus recursos na região de Azure correta. Se os seus recursos estiverem na região "errada", tem de considerar parar a sincronização e configurar novamente a sincronização para novas ações de ficheiros Azure que são implantadas na região desejada.

Uma falha regional pode ser iniciada pela Microsoft num evento catastrófico que tornará os centros de dados numa região de Azure incapacitados por um longo período de tempo. A definição de tempo de inatividade que o seu negócio pode sustentar pode ser menor do que o tempo que a Microsoft está preparada para deixar passar antes de iniciar uma falha regional. Para uma situação [destas, as falhas também podem ser iniciadas pelos clientes.](../common/storage-initiate-account-failover.md)

> [!IMPORTANT]
> Em caso de falha, precisa de apresentar um bilhete de apoio para os seus Serviços de Sincronização de Armazenamento impactados para que a sincronização volte a funcionar.

## <a name="see-also"></a>Ver também

- [Visão geral da partilha de ficheiros Azure e sincroniza os guias de migração](../files/storage-files-migration-overview.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)
- [Resolver problemas do Azure File Sync](file-sync-troubleshoot.md)
- [Planear uma implementação do Azure File Sync](file-sync-planning.md)