---
title: Ativar a replicação de VMs Azure encriptados na recuperação do site Azure
description: Este artigo descreve como configurar a replicação para VMs ativados por encriptação de disco azure de uma região azure para outra usando a Recuperação do Site.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/08/2019
ms.author: sutalasi
ms.openlocfilehash: 2bbb02df782439d934e96e7c16f28b9c11cc01fe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81408626"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>Replicate Azure Disk Cryptigrafed máquinas virtuais para outra região do Azure

Este artigo descreve como replicar VMs Azure com encriptação de disco azure (ADE) habilitado, de uma região azure para outra.

>[!NOTE]
> ATualmente, a Recovery do Site suporta a ADE, com e sem Diretório Ativo Azure (AAD) para VMs que executam sistemas operativos Windows. Para os sistemas operativos Linux, só suportamos a ADE sem AAD. Além disso, para as máquinas que executam a ADE 1.1 (sem AAD), os VMs devem estar a utilizar discos geridos. VMs com discos não geridos não são suportados. Se mudar de ADE 0.1 (com AAD) para 1.1, tem de desativar a replicação e ativar a replicação de um VM após ativar 1.1.


## <a name="required-user-permissions"></a><a id="required-user-permissions"></a>Permissões de utilizador necessárias
A Recuperação do Site requer que o utilizador tenha permissões para criar o cofre chave na região alvo e copiar chaves do cofre chave da região de origem para o cofre chave da região alvo.

Para permitir a replicação de VMs ativados por encriptação de disco do portal Azure, o utilizador necessita das seguintes permissões tanto na região de origem como nos cofres-chave da **região alvo.**

- Permissões de cofre chave
    - Lista, Criar e Obter
    
- Permissões secretas do cofre chave
    - Operações secretas de Gestão
        - Obter, Listar e Definir
    
- Permissões de chave do cofre (necessárias apenas se os VMs usarem a chave de encriptação da chave para encriptar as chaves de encriptação do disco)
    - Operações de Gestão Chave
        - Obter, listar e criar
    - Operações Criptográficas
        - Desencriptar e encriptar

Para gerir permissões, vá ao recurso chave do cofre no portal. Adicione as permissões necessárias para o utilizador. O exemplo que se segue mostra como permitir permissões ao cofre chave *ContosoWeb2Keyvault*, que está na região de origem.

1. Vá a **Home** > **Keyvaults** > **ContosoWeb2KeyVault > Políticas**de acesso .

   ![Janela de permissões de cofre chave](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. Pode ver que não existem permissões de utilizador. **Selecione Adicionar novo**. Insira a informação do utilizador e permissões.

   ![Permissões do cofre](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

Se o utilizador que está a permitir a recuperação de desastres (DR) não tiver permissões para copiar as chaves, um administrador de segurança que tenha permissões adequadas pode usar o seguinte script para copiar os segredos de encriptação e as chaves da região alvo.

Para resolução de permissões, consulte os problemas de [permissão do cofre](#trusted-root-certificates-error-code-151066) mais tarde neste artigo.

>[!NOTE]
>Para permitir a replicação de VMs ativados por encriptação de disco a partir do portal, você precisa de pelo menos permissões "List" nos cofres, segredos e chaves chave.

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>Copiar chaves de encriptação do disco para a região DR utilizando o script PowerShell

1. [Abra o código de script cru "CopyKeys".](https://aka.ms/ade-asr-copy-keys-code)
2. Copie o guião para um ficheiro e **nomeie-o Copy-keys.ps1**.
3. Abra a aplicação Windows PowerShell e vá para a pasta onde guardou o ficheiro.
4. Execute Copy-keys.ps1.
5. Forneça credenciais azure para se inscrever.
6. Selecione a **subscrição Azure** dos seus VMs.
7. Aguarde que os grupos de recursos carreguem e, em seguida, selecione o **grupo de Recursos** dos seus VMs.
8. Selecione os VMs da lista que está exposta. Apenas VMs que estão habilitados para encriptação de disco estão na lista.
9. Selecione a localização do **Alvo**.

    - **Cofres de chave de encriptação de disco**
    - **Cofres chave de encriptação**

   Por padrão, a Recuperação do Site cria um novo cofre chave na região alvo. O nome do cofre tem um sufixo "asr" baseado nas chaves de encriptação do disco VM. Se já existe um cofre chave que foi criado pela Recuperação do Site, é reutilizado. Selecione um cofre chave diferente da lista, se necessário.

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
    - **Grupo de recursos-alvo**: O grupo de recursos ao qual todas as suas máquinas virtuais replicadas pertencem. Por padrão, a Recuperação do Site cria um novo grupo de recursos na região alvo. O nome recebe o sufixo "asr". Se já existe um grupo de recursos que foi criado pela Azure Site Recovery, é reutilizado. Também pode optar por personalizá-lo, como mostra a seguinte secção. A localização do grupo de recursos alvo pode ser qualquer região azure, exceto a região onde estão hospedadas as máquinas virtuais de origem.
    - **Rede virtual alvo**: Por padrão, a Recuperação do Site cria uma nova rede virtual na região alvo. O nome recebe o sufixo "asr". Está mapeado na sua rede de origem e usado para qualquer proteção futura. [Saiba mais](site-recovery-network-mapping-azure-to-azure.md) sobre o mapeamento da rede.
    - Contas de armazenamento de alvos **(se a sua fonte VM não utilizar discos geridos)**: Por padrão, a Recuperação do Site cria uma nova conta de armazenamento alvo imitando a configuração de armazenamento vM de origem. Se uma conta de armazenamento já existe, é reutilizada.
    - **Réplica de discos geridos (se a sua fonte VM utilizar discos geridos)**: A Recuperação do Site cria novos discos geridos por réplicana região alvo para espelhar os discos geridos pela Fonte VM do mesmo tipo de armazenamento (padrão ou premium) como os discos geridos pela VM.
    - **Contas**de armazenamento de cache : A Recuperação do Site precisa de uma conta de armazenamento extra chamada armazenamento de *cache* na região fonte. Todas as alterações nas VMs de origem são rastreadas e enviadas para a conta de armazenamento de cache. São então replicados para o local do alvo.
    - **Conjunto de disponibilidade**: Por padrão, a Recuperação do Site cria um novo conjunto de disponibilidade na região alvo. O nome tem o sufixo "asr". Se já existe um conjunto de disponibilidades que foi criado pela Recovery do Site, é reutilizado.
    - **Cofres de chave**de encriptação de disco : Por padrão, a Recuperação do Site cria um novo cofre chave na região alvo. Tem um sufixo "asr" baseado nas chaves de encriptação do disco VM de origem. Se já existe um cofre chave criado pela Azure Site Recovery, é reutilizado.
    - **Cofres chave de encriptação**: Por padrão, a Recuperação do Site cria um novo cofre chave na região alvo. O nome tem um sufixo "asr" baseado nas teclas de encriptação vm fonte. Se um cofre de chaves criado pela Azure Site Recovery já existe, é reutilizado.
    - **Política de replicação**: Define as definições para o histórico de retenção de pontos de recuperação e frequência instantânea consistente com aplicações. Por padrão, a Recuperação do Site cria uma nova política de replicação com configurações padrão de *24 horas* para retenção de pontos de recuperação e *60 minutos* para frequência instantânea consistente com aplicações.

## <a name="customize-target-resources"></a>Personalizar recursos-alvo

Siga estes passos para modificar as definições de alvo predefinidos de recuperação do site.

1. Selecione **Personalizar** ao lado da "subscrição target" para modificar a subscrição do alvo predefinido. Selecione a subscrição da lista de subscrições disponíveis no inquilino da AD Azure.

2. Selecione **Personalizar** ao lado de "Conjuntos de recursos, rede, armazenamento e disponibilidade" para modificar as seguintes definições predefinidas:
    - Para o **grupo de recursos Target,** selecione o grupo de recursos da lista de grupos de recursos na localização-alvo da subscrição.
    - Para **a rede virtual Target,** selecione a rede a partir de uma lista de redes virtuais na localização alvo.
    - Para o conjunto de **disponibilidade,** pode adicionar definições de disponibilidade ao VM, se fizerem parte de um conjunto de disponibilidade na região de origem.
    - Para **as contas de Armazenamento de Destino,** selecione a conta a utilizar.

2. Selecione **Personalizar** ao lado de "Definições de Encriptação" para modificar as seguintes definições predefinidas:
   - Para o cofre da chave de encriptação do **disco Target,** selecione o cofre da chave de encriptação do disco-alvo a partir da lista de cofres chave na localização-alvo da subscrição.
   - Para o cofre de chave de **encriptação target**, selecione o cofre chave de encriptação da chave alvo a partir da lista de cofres chave na localização-alvo da subscrição.

3. Selecione Criar a**replicação ativado** **por um recurso-alvo** > .
4. Depois de os VMs estarem ativados para a replicação, pode verificar o estado de saúde dos VMs em **itens replicados**.

>[!NOTE]
>Durante a replicação inicial, o estado pode levar algum tempo a refrescar-se, sem progressos aparentes. Clique em **Refresh** para obter o estado mais recente.

## <a name="update-target-vm-encryption-settings"></a>Atualizar as definições de encriptação VM do alvo
Nos seguintes cenários, será necessário atualizar as definições de encriptação VM do alvo:
  - Ativou a replicação da Recuperação do Local no VM. Mais tarde, ativou a encriptação do disco na Fonte VM.
  - Ativou a replicação da Recuperação do Local no VM. Mais tarde, alterou a chave de encriptação do disco ou a chave de encriptação da chave no VM de origem.

Pode utilizar [um script](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script) para copiar as chaves de encriptação da região alvo e, em seguida, atualizar as definições de encriptação do alvo no cofre > de serviços de **recuperação,***itens replicados* > **Properties** > **Compute e Network**.

![Atualizar a janela de diálogo de definições ADE](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a name="troubleshoot-key-vault-permission-issues-during--azure-to-azure-vm-replication"></a><a id="trusted-root-certificates-error-code-151066"></a>Problemas de autorização do cofre chave durante a replicação do VM Azure-to-Azure

A Recuperação do Sítio Azure requer pelo menos ler permissão no cofre chave da região fonte e escrever permissão no cofre chave da região alvo para ler o segredo e copiá-lo para o cofre chave da região alvo. 

**Causa 1:** Não tem permissão "GET" na **região fonte Key vault** para ler as chaves. </br>
**Como corrigir:** Independentemente de ser um administrador de subscrição ou não, é importante que obtenha permissão no cofre chave.

1. Vá para a região fonte Cofre chave que neste exemplo é "ContososourceKeyvault" > Políticas de **acesso** 
2. Em **'Selecionar Principal'** adicione odradmin@contoso.comseu nome de utilizador, por exemplo: " "
3. Sob **permissões chave** selecione GET 
4. Sob **permissão secreta** selecione GET 
5. Salvar a política de acesso

**Causa 2:** Não precisaste de autorização no cofre da **região alvo** para escrever as chaves. </br>

*Por exemplo:* Tenta replicar um VM que tem o cofre chave *ContososourceKeyvault* numa região de origem.
Tem todas as permissões no cofre da região. Mas durante a proteção, selecione o cofre chave já criado ContosotargetKeyvault, que não tem permissões. Ocorre um erro.

Permissão necessária no [cofre chave alvo](#required-user-permissions)

**Como corrigir:** Vá às políticas **de** > **acesso** a**Keyvaults** > Home**ContosotargetKeyvault** > e adicione as permissões apropriadas.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais](site-recovery-test-failover-to-azure.md) sobre executar um teste failover.
