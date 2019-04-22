---
title: Configurar a replicação de VMs habilitadas para o Azure Disk Encryption no Azure Site Recovery | Documentos da Microsoft
description: Este artigo descreve como configurar a replicação de VMs ativada a encriptação de disco do Azure de uma região do Azure para outra com o Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 4943b730bb46ee00200d84faf95a7ccb069d3aa8
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678960"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>Replicar máquinas virtuais do Azure Disk Encryption ativadas para outra região do Azure

Este artigo descreve como replicar VMs habilitadas para o Azure Disk Encryption de uma região do Azure para outra.

>[!NOTE]
>Atualmente, o Azure Site Recovery suporta apenas VMs do Azure que executam um sistema operacional do Windows e que estejam [ativada para a encriptação com o Azure Active Directory (Azure AD)](https://aka.ms/ade-aad-app).

## <a name="required-user-permissions"></a>Permissões de utilizador necessário
Recuperação de site requer que o utilizador tenha permissões para criar o Cofre de chaves nas chaves de região e cópia de destino para a região.

Para ativar a replicação de VMs com capacidade de encriptação de disco do portal do Azure, o utilizador tem as seguintes permissões:

- Permissões do Cofre de chaves
    - Lista
    - Criar
    - Get

-   Permissões de segredos do Cofre de chaves
    - Lista
    - Criar
    - Get

- Permissões de chave de Cofre de chaves (necessárias apenas se as VMs utilizam a chave de encriptação de chave para criptografar chaves de encriptação de disco)
    - Lista
    - Get
    - Criar
    - Encriptar
    - Desencriptar

Para gerir as permissões, vá para o recurso do Cofre de chaves no portal. Adicione as permissões necessárias para o utilizador. O exemplo seguinte mostra como ativar permissões ao Cofre de chaves *ContosoWeb2Keyvault*, que se encontra na região de origem.

1. Aceda a **home page** > **Keyvaults** > **ContosoWeb2KeyVault > políticas de acesso**.

   ![Janela de permissões do Cofre de chaves](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. Pode ver que existem não existem permissões de utilizador. Selecione **adicionar novo**. Introduza as informações de utilizador e permissões.

   ![permissões do Cofre de chaves](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

Se o utilizador que é permitir que a recuperação após desastre (DR) não tiver permissões para copiar as chaves, um administrador de segurança que tem as permissões apropriadas pode utilizar o seguinte script para copiar as chaves e segredos de encriptação para a região de destino.

Para resolver problemas de permissões, consulte [problemas de permissão do Cofre de chaves](#trusted-root-certificates-error-code-151066) mais adiante neste artigo.

>[!NOTE]
>Para ativar a replicação de VMs com capacidade de encriptação de disco do portal, precisa de, pelo menos, "List" permissões sobre os cofres de chaves, segredos e chaves.

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>Copiar chaves de encriptação de disco para a região de DR com o script do PowerShell

1. [Abra o código de script não processados "CopyKeys"](https://aka.ms/ade-asr-copy-keys-code).
2. Copie o script para um ficheiro e designe- **cópia keys.ps1**.
3. Abra a aplicação do Windows PowerShell e aceda à pasta onde guardou o ficheiro.
4. Execute cópia keys.ps1.
5. Forneça as credenciais do Azure para iniciar sessão.
6. Selecione o **subscrição do Azure** das suas VMs.
7. Aguarde que os grupos de recursos carregar e, em seguida, selecione o **grupo de recursos** das suas VMs.
8. Selecione as VMs a partir da lista que é apresentada. Apenas as VMs que estão ativadas para encriptação de disco estejam na lista.
9. Selecione o **localização de destino**.

    - **Cofres de chaves de encriptação de disco**
    - **Cofres de chaves de encriptação de chave**

   Por predefinição, o Site Recovery cria um novo cofre de chaves na região de destino. O nome do cofre tem um sufixo "asr" que baseia-se as chaves de encriptação de disco VM de origem. Se um cofre de chaves já existe e que foi criado pelo Site Recovery, este é reutilizado. Selecione um cofre de chaves diferente na lista, se necessário.

## <a name="enable-replication"></a>Ativar a replicação

Neste exemplo, a região primária do Azure é a Ásia Oriental e a região secundária é Sudeste asiático.

1. No cofre, selecione **+ replicar**.
2. Tenha em atenção os seguintes campos.
    - **origem**: O ponto de origem das VMs, que neste caso é **Azure**.
    - **Localização de origem**: A região do Azure em que pretende proteger as suas máquinas virtuais. Neste exemplo, a localização de origem é "Ásia Oriental."
    - **Modelo de implementação**: O modelo de implementação do Azure, máquinas de origem.
    - **Subscrição de origem**: A subscrição ao qual pertencem a suas máquinas de virtuais de origem. Pode ser qualquer subscrição que está no mesmo inquilino do Azure Active Directory como o seu Cofre de serviços de recuperação.
    - **Grupo de recursos**: O grupo de recursos ao qual pertencem a suas máquinas de virtuais de origem. Todas as VMs no grupo de recursos selecionada são listadas para proteção no próximo passo.

3. Na **máquinas virtuais** > **selecionar máquinas virtuais**, selecione cada VM que pretende replicar. Só pode selecionar máquinas para as quais a replicação pode ser ativada. Em seguida, selecione **OK**.

4. Na **definições**, pode configurar as seguintes definições de site de destino.

    - **Localização de destino**: A localização onde os dados de máquina virtual de origem serão replicados. Recuperação de sites fornece uma lista de regiões de destino adequado com base na localização da máquina selecionada. Recomendamos que utilize a mesma localização como localização do cofre dos serviços de recuperação.
    - **Subscrição de destino**: A subscrição de destino que é utilizada para recuperação após desastre. Por predefinição, a subscrição de destino é o mesmo que a subscrição de origem.
    - **Grupo de recursos de destino**: O grupo de recursos para que todos os seus máquinas virtuais replicadas pertence. Por predefinição, o Site Recovery cria um novo grupo de recursos na região de destino. O nome obtém o sufixo "asr". Se um grupo de recursos já existe e que foi criado pelo Azure Site Recovery, este é reutilizado. Também é possível personalizá-lo, conforme mostrado na secção seguinte. A localização do grupo de recursos de destino pode ser qualquer região do Azure, exceto a região onde estão alojadas as máquinas de virtuais de origem.
    - **Rede virtual de destino**: Por predefinição, o Site Recovery cria uma nova rede virtual na região de destino. O nome obtém o sufixo "asr". É mapeado para a sua rede de origem e utilizado para qualquer proteção futura. [Saiba mais](site-recovery-network-mapping-azure-to-azure.md) sobre mapeamento de rede.
    - **Contas de armazenamento de destino (se a sua origem de VM não utilizar discos geridos)**: Por predefinição, o Site Recovery cria uma nova conta de armazenamento de destino por imitando a configuração de armazenamento VM de origem. Se já existir uma conta de armazenamento, este é reutilizado.
    - **Discos de geridos de réplica (se a VM de origem utilizar discos geridos)**: Site Recovery cria novos discos geridos de réplica na região de destino para espelhar os discos geridos da VM de origem do mesmo tipo de armazenamento (standard ou premium) como os discos geridos da VM de origem.
    - **Contas de armazenamento em cache**: Recuperação de sites precisa de uma conta de armazenamento extra chamada *armazenamento em cache* na região de origem. Todas as alterações nas VMs de origem são controladas e enviadas para a conta de armazenamento de cache. Eles são, em seguida, replicados para a localização de destino.
    - **Conjunto de disponibilidade**: Por predefinição, o Site Recovery cria uma novo conjunto de disponibilidade na região de destino. O nome tem o sufixo "asr". Se um conjunto de disponibilidade que foi criado pelo Site Recovery já existir, este é reutilizado.
    - **Cofres de chaves de encriptação de disco**: Por predefinição, o Site Recovery cria um novo cofre de chaves na região de destino. Ele tem um sufixo "asr" que baseia-se as chaves de encriptação de disco VM de origem. Se um cofre de chaves que foi criado pelo Azure Site Recovery já existir, este é reutilizado.
    - **Cofres de chaves de encriptação da chave**: Por predefinição, o Site Recovery cria um novo cofre de chaves na região de destino. O nome tem um sufixo "asr" com base nas chaves de encriptação de chave de VM de origem. Se um cofre de chaves criado pelo Azure Site Recovery já existir, este é reutilizado.
    - **Política de replicação**: Define as definições de histórico de retenção do ponto de recuperação e frequência de instantâneo consistente com a aplicação. Por predefinição, o Site Recovery cria uma nova política de replicação com as predefinições de *24 horas* retenção do ponto de recuperação e *60 minutos* para a frequência de instantâneo consistente da aplicação.

## <a name="customize-target-resources"></a>Personalizar os recursos de destino

Siga estes passos para modificar as predefinições de destino do Site Recovery.

1. Selecione **personalizar** junto a "Subscrição de destino" para modificar a subscrição de destino predefinido. Selecione a subscrição da lista de subscrições que estão disponíveis no inquilino do Azure AD.

2. Selecione **personalizar** junto a "grupo de recursos, rede, armazenamento e conjuntos de disponibilidade" para modificar as seguintes predefinições:
    - Para **grupo de recursos de destino**, selecione o grupo de recursos da lista de grupos de recursos na localização de destino da subscrição.
    - Para **rede virtual de destino**, selecione a rede de uma lista de redes virtuais na localização de destino.
    - Para **conjunto de disponibilidade**, pode adicionar as definições de conjunto de disponibilidade para a VM, se eles são parte de um conjunto de disponibilidade na região de origem.
    - Para **contas de armazenamento de destino**, selecione a conta a utilizar.

2. Selecione **personalizar** junto a "Definições de encriptação" para modificar as seguintes predefinições:
   - Para **Cofre de chaves de encriptação de disco de destino**, selecione o Cofre de chaves de encriptação de disco destino na lista de cofres de chaves na localização de destino da subscrição.
   - Para **Cofre de chaves de encriptação de chave de destino**, selecione o Cofre de chaves de encriptação de chave de destino na lista de cofres de chaves na localização de destino da subscrição.

3. Selecione **criar o recurso de destino** > **ativar replicação**.
4. Depois das VMs estiverem ativadas para replicação, pode verificar o estado de funcionamento de VMs em **itens replicados**.

>[!NOTE]
>Durante a replicação inicial, o estado pode demorar algum tempo para atualizar, sem progresso aparente. Clique em **atualizar** para obter o estado mais recente.

## <a name="update-target-vm-encryption-settings"></a>Atualizar definições de encriptação de VM de destino
Nos seguintes cenários, precisará para atualizar as definições de encriptação da VM de destino:
  - Ativou a replicação do Site Recovery na VM. Mais tarde, ativou a encriptação de discos na VM de origem.
  - Ativou a replicação do Site Recovery na VM. Posteriormente, alterar a chave de encriptação de disco ou a chave de encriptação de chave na VM de origem.

Pode usar [um script](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script) para copiar as chaves de encriptação para a região de destino e, em seguida, Atualize as definições de encriptação de destino na **cofre dos Recovery services** > *doitemreplicado*  >  **Propriedades** > **computação e rede**.

![Atualizar a janela de caixa de diálogo de definições de ADE](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a id="trusted-root-certificates-error-code-151066"></a>Resolver problemas de permissão do Cofre de chaves durante a replicação de VM do Azure para o Azure

**Fazer com que 1:** Poderá ter selecionado da região de destino um cofre de chaves já criado que não tem as permissões necessárias em vez de permitir que o Site Recovery criá-lo. Certifique-se de que o Cofre de chaves tem o necessitam de permissões, conforme descrito anteriormente.

*Por exemplo*: Tentar replicar uma VM com o Cofre de chaves *ContososourceKeyvault* numa região de origem.
Ter todas as permissões no Cofre de chaves de região de origem. Mas durante a proteção, selecione o Cofre de chaves já criado ContosotargetKeyvault, que não tem permissões. Ocorre um erro.

**Como corrigir:** Aceda a **home page** > **Keyvaults** > **ContososourceKeyvault** > **políticas de acesso** e adicione as permissões adequadas.

**Causa 2:** Poderá ter selecionado da região de destino um cofre de chaves já criado que não tem encriptar desencriptação de permissões em vez de permitir que o Site Recovery criá-lo. Certifique-se de que tem desencriptação-criptografe permissões também está a encriptar a chave na região de origem.</br>

*Por exemplo*: Tentar replicar uma VM que tenha um cofre de chaves *ContososourceKeyvault* na região de origem. Tem a permissão necessária no Cofre de chaves de região de origem. Mas durante a proteção, selecione o Cofre de chaves já criado ContosotargetKeyvault, que não tem permissões para desencriptar e encriptar. Ocorre um erro.</br>

**Como corrigir:** Aceda a **home page** > **Keyvaults** > **ContososourceKeyvault** > **políticas de acesso**. Adicionar permissões sob **permissões da chave** > **operações criptográficas**.

## <a name="next-steps"></a>Passos Seguintes

[Saiba mais](site-recovery-test-failover-to-azure.md) sobre como executar uma ativação pós-falha de teste.
