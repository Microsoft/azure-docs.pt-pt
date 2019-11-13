---
title: Habilitar a replicação para VMs do Azure criptografadas no Azure Site Recovery
description: Este artigo descreve como configurar a replicação para VMs habilitadas para Azure Disk Encryption de uma região do Azure para outra usando o Site Recovery.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/08/2019
ms.author: sutalasi
ms.openlocfilehash: 2b6c6f2882701515d868e96ae10af85890004587
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954674"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>Replicar máquinas virtuais habilitadas para Azure Disk Encryption para outra região do Azure

Este artigo descreve como replicar VMs do Azure com o Azure Disk Encryption (ADE) habilitado, de uma região do Azure para outra.

>[!NOTE]
> O Site Recovery atualmente dá suporte a ADE, com e sem Azure Active Directory (AAD) para VMs que executam o Windows.  Para máquinas que executam o ADE 1,1 (sem AAD), as VMs do Windows devem estar usando discos gerenciados. Não há suporte para VMs com discos não gerenciados. Se você alternar de ADE 0,1 (com AAD) para 1,1, será necessário desabilitar a replicação e habilitar a replicação para uma VM depois de habilitar a 1,1.


## <a id="required-user-permissions"></a>Permissões de usuário necessárias
Site Recovery exige que o usuário tenha permissões para criar o cofre de chaves na região de destino e copiar chaves do cofre de chaves de região de origem para o cofre de chaves da região de destino.

Para habilitar a replicação de VMs habilitadas para criptografia de disco do portal do Azure, o usuário precisa das seguintes permissões na **região de origem e** nos cofres de chaves de região de destino.

- Permissões do Key Vault
    - Listar, criar e obter
    
- Permissões de segredo do Key Vault
    - Operações de gerenciamento de segredo
        - Obter, listar e definir
    
- Permissões de chave do Key Vault (necessárias somente se as VMs usarem chave de criptografia de chave para criptografar chaves de criptografia de disco)
    - Operações de gerenciamento de chaves
        - Obter, listar e criar
    - Operações criptográficas
        - Descriptografar e criptografar

Para gerenciar permissões, vá para o recurso do Key Vault no Portal. Adicione as permissões necessárias para o usuário. O exemplo a seguir mostra como habilitar permissões para o Key Vault *ContosoWeb2Keyvault*, que está na região de origem.

1. Vá para **página inicial** > **keyvaults** > **políticas de acesso do ContosoWeb2KeyVault >** .

   ![Janela de permissões do Key Vault](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. Você pode ver que não há permissões de usuário. Selecione **Adicionar novo**. Insira as informações de usuário e permissões.

   ![Permissões do keyvault](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

Se o usuário que está habilitando a recuperação de desastres (DR) não tiver permissões para copiar as chaves, um administrador de segurança que tenha as permissões apropriadas poderá usar o script a seguir para copiar os segredos de criptografia e as chaves para a região de destino.

Para solucionar problemas de permissões, consulte os [problemas de permissão do Key Vault](#trusted-root-certificates-error-code-151066) posteriormente neste artigo.

>[!NOTE]
>Para habilitar a replicação de VMs habilitadas para criptografia de disco no portal, você precisa de pelo menos permissões de "lista" nos cofres de chaves, segredos e chaves.

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>Copiar chaves de criptografia de disco para a região de DR usando o script do PowerShell

1. [Abra o código de script bruto "CopyKeys"](https://aka.ms/ade-asr-copy-keys-code).
2. Copie o script em um arquivo e nomeie-o **Copy-Keys. ps1**.
3. Abra o aplicativo do Windows PowerShell e vá para a pasta em que você salvou o arquivo.
4. Execute Copy-Keys. ps1.
5. Forneça as credenciais do Azure para entrar.
6. Selecione a **assinatura do Azure** de suas VMs.
7. Aguarde até que os grupos de recursos sejam carregados e, em seguida, selecione o **grupo de recursos** de suas VMs.
8. Selecione as VMs na lista que é exibida. Somente as VMs habilitadas para criptografia de disco estão na lista.
9. Selecione o **local de destino**.

    - **Cofres da chave de criptografia de disco**
    - **Cofres da chave de criptografia de chave**

   Por padrão, Site Recovery cria um novo cofre de chaves na região de destino. O nome do cofre tem um sufixo "ASR" baseado nas chaves de criptografia de disco da VM de origem. Se um cofre de chaves já existir e criado por Site Recovery, ele será reutilizado. Selecione um cofre de chaves diferente na lista, se necessário.

## <a name="enable-replication"></a>Ativar a replicação

Para este exemplo, a região primária do Azure é Ásia Oriental e a região secundária é Ásia Oriental do Sul.

1. No cofre, selecione **+ replicar**.
2. Observe os campos a seguir.
    - **Origem**: o ponto de origem das VMs, que neste caso é o **Azure**.
    - **Local de origem**: a região do Azure onde você deseja proteger suas máquinas virtuais. Para este exemplo, o local de origem é "Ásia Oriental".
    - **Modelo de implantação**: o modelo de implantação do Azure dos computadores de origem.
    - **Assinatura de origem**: a assinatura à qual as máquinas virtuais de origem pertencem. Pode ser qualquer assinatura que esteja no mesmo locatário Azure Active Directory como seu cofre de serviços de recuperação.
    - **Grupo de recursos**: o grupo de recursos ao qual as máquinas virtuais de origem pertencem. Todas as VMs no grupo de recursos selecionado são listadas para proteção na próxima etapa.

3. Em **máquinas virtuais** > **selecionar máquinas virtuais**, selecione cada VM que você deseja replicar. Só pode selecionar máquinas para as quais a replicação pode ser ativada. Em seguida, selecione **OK**.

4. Em **configurações**, você pode definir as seguintes configurações de site de destino.

    - **Local de destino**: o local onde os dados da máquina virtual de origem serão replicados. Site Recovery fornece uma lista de regiões de destino adequadas com base no local da máquina selecionada. Recomendamos que você use o mesmo local que o local do cofre dos serviços de recuperação.
    - **Assinatura de destino**: a assinatura de destino que é usada para recuperação de desastre. Por padrão, a assinatura de destino é igual à assinatura de origem.
    - **Grupo de recursos de destino**: o grupo de recursos ao qual todas as máquinas virtuais replicadas pertencem. Por padrão, Site Recovery cria um novo grupo de recursos na região de destino. O nome Obtém o sufixo "ASR". Se já existir um grupo de recursos que foi criado por Azure Site Recovery, ele será reutilizado. Você também pode optar por personalizá-lo, conforme mostrado na seção a seguir. O local do grupo de recursos de destino pode ser qualquer região do Azure, exceto a região onde as máquinas virtuais de origem estão hospedadas.
    - **Rede virtual de destino**: por padrão, site Recovery cria uma nova rede virtual na região de destino. O nome Obtém o sufixo "ASR". Ele é mapeado para sua rede de origem e usado para qualquer proteção futura. [Saiba mais](site-recovery-network-mapping-azure-to-azure.md) sobre o mapeamento de rede.
    - **Contas de armazenamento de destino (se a VM de origem não usa discos gerenciados)** : por padrão, site Recovery cria uma nova conta de armazenamento de destino, imitandondo sua configuração de armazenamento de VM de origem. Se uma conta de armazenamento já existir, ela será reutilizada.
    - **Discos gerenciados de réplica (se a VM de origem usar discos gerenciados)** : Site Recovery cria novos discos gerenciados de réplica na região de destino para espelhar os discos gerenciados da VM de origem do mesmo tipo de armazenamento (Standard ou Premium) que os discos gerenciados da VM de origem.
    - **Contas de armazenamento em cache**: Site Recovery precisa de uma conta de armazenamento extra chamada *armazenamento em cache* na região de origem. Todas as alterações nas VMs de origem são rastreadas e enviadas para a conta de armazenamento de cache. Em seguida, eles são replicados para o local de destino.
    - **Conjunto de disponibilidade**: por padrão, site Recovery cria um novo conjunto de disponibilidade na região de destino. O nome tem o sufixo "ASR". Se um conjunto de disponibilidade criado pelo Site Recovery já existir, ele será reutilizado.
    - **Cofres da chave de criptografia de disco**: por padrão, site Recovery cria um novo cofre de chaves na região de destino. Ele tem um sufixo "ASR" baseado nas chaves de criptografia de disco da VM de origem. Se um cofre de chaves criado pelo Azure Site Recovery já existir, ele será reutilizado.
    - **Cofres de chave de criptografia de chave**: por padrão, site Recovery cria um novo cofre de chaves na região de destino. O nome tem um sufixo "ASR" baseado nas chaves de criptografia de chave de VM de origem. Se um cofre de chaves criado pelo Azure Site Recovery já existir, ele será reutilizado.
    - **Política de replicação**: define as configurações para o histórico de retenção do ponto de recuperação e a frequência do instantâneo consistente com o aplicativo. Por padrão, Site Recovery cria uma nova política de replicação com configurações padrão de *24 horas* para retenção de ponto de recuperação e *60 minutos* para frequência de instantâneo consistente com o aplicativo.

## <a name="customize-target-resources"></a>Personalizar recursos de destino

Siga estas etapas para modificar as Site Recovery configurações de destino padrão.

1. Selecione **Personalizar** ao lado de "assinatura de destino" para modificar a assinatura de destino padrão. Selecione a assinatura na lista de assinaturas que estão disponíveis no locatário do Azure AD.

2. Selecione **Personalizar** ao lado de "grupo de recursos, rede, armazenamento e conjuntos de disponibilidade" para modificar as seguintes configurações padrão:
    - Para **grupo de recursos de destino**, selecione o grupo de recursos na lista de grupos de recursos no local de destino da assinatura.
    - Para **rede virtual de destino**, selecione a rede em uma lista de redes virtuais no local de destino.
    - Para o **conjunto de disponibilidade**, você pode adicionar configurações de conjunto de disponibilidade à VM, se elas fizerem parte de um conjunto de disponibilidade na região de origem.
    - Para **contas de armazenamento de destino**, selecione a conta a ser usada.

2. Selecione **Personalizar** ao lado de "configurações de criptografia" para modificar as seguintes configurações padrão:
   - Para o **cofre de chaves de criptografia de disco de destino**, selecione o cofre de chaves de criptografia de disco de destino na lista de cofres de chaves no local de destino da assinatura.
   - Para o **cofre de chaves de criptografia de chave de destino**, selecione o cofre de chave de criptografia de chave de destino na lista de cofres de chaves no local de destino da assinatura.

3. Selecione **criar recurso de destino** > **habilitar a replicação**.
4. Depois que as VMs estiverem habilitadas para replicação, você poderá verificar o status de integridade das VMs em **itens replicados**.

>[!NOTE]
>Durante a replicação inicial, o status pode levar algum tempo para ser atualizado, sem um progresso aparente. Clique em **Atualizar** para obter o status mais recente.

## <a name="update-target-vm-encryption-settings"></a>Atualizar configurações de criptografia de VM de destino
Nos cenários a seguir, será necessário atualizar as configurações de criptografia de VM de destino:
  - Você habilitou Site Recovery replicação na VM. Posteriormente, você habilitou a criptografia de disco na VM de origem.
  - Você habilitou Site Recovery replicação na VM. Posteriormente, você alterou a chave de criptografia de disco ou a chave de criptografia de chave na VM de origem.

Você pode usar [um script](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script) para copiar as chaves de criptografia para a região de destino e, em seguida, atualizar as configurações de criptografia de destino no **cofre dos serviços de recuperação** > *item replicado* > **Propriedades** > **computação e rede**.

![Janela da caixa de diálogo atualizar configurações de ADE](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a id="trusted-root-certificates-error-code-151066"></a>Solucionar problemas de permissão do Key Vault durante a replicação de VM do Azure para o Azure

Azure Site Recovery requer pelo menos permissão de leitura no cofre de chaves da região de origem e a permissão de gravação no cofre de chaves da região de destino para ler o segredo e copiá-lo para o cofre de chaves da região de destino. 

**Causa 1:** Você não tem a permissão "GET" no **cofre de chaves da região de origem** para ler as chaves. </br>
**Como corrigir:** Independentemente de você ser um administrador de assinatura ou não, é importante que você tenha a permissão Get no cofre de chaves.

1. Acesse o cofre de chaves da região de origem que neste exemplo é "ContososourceKeyvault" > **políticas de acesso** 
2. Em **selecionar principal** , adicione seu nome de usuário, por exemplo: "dradmin@contoso.com"
3. Em **permissões de chave** , selecione obter 
4. Em **permissão de segredo** , selecione obter 
5. Salvar a política de acesso

**Causa 2:** Você não tem a permissão necessária no **cofre de chaves da região de destino** para gravar as chaves. </br>

*Por exemplo*: você tenta replicar uma VM que tem o Key Vault *ContososourceKeyvault* em uma região de origem.
Você tem todas as permissões no cofre de chaves da região de origem. Mas durante a proteção, você seleciona o ContosotargetKeyvault do Key Vault já criado, que não tem permissões. Ocorre um erro.

Permissão necessária no [cofre de chaves de destino](#required-user-permissions)

**Como corrigir:** Vá para **página inicial** > **keyvaults** > **ContosotargetKeyvault** > **políticas de acesso** e adicione as permissões apropriadas.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais](site-recovery-test-failover-to-azure.md) sobre como executar um failover de teste.
