---
title: Ativar a replicação para VMs Azure encriptados na recuperação do local de Azure
description: Este artigo descreve como configurar a replicação para VMs ativados por encriptação de disco Azure de uma região de Azure para outra, utilizando a Recuperação do Site.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/08/2019
ms.author: sutalasi
ms.openlocfilehash: fa4d61599e102f9a2580e704ee7a02486067daa2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86135786"
---
# <a name="replicate-azure-disk-encryption-enabled-virtual-machines-to-another-azure-region"></a>Replicar máquinas virtuais com o Azure Disk Encryption ativado para outra região do Azure

Este artigo descreve como replicar VMs Azure com Encriptação do Disco Azure (ADE) ativada, de uma região de Azure para outra.

>[!NOTE]
> A Recuperação do Site suporta atualmente o ADE, com e sem Diretório Ativo Azure (AAD) para VMs que executam sistemas operativos Windows. Para os sistemas operativos Linux, só suportamos ADE sem AAD. Além disso, para as máquinas que executam ADE 1.1 (sem AAD), os VMs devem estar a utilizar discos geridos. VMs com discos não geridos não são suportados. Se mudar de ADE 0.1 (com AAD) para 1.1, tem de desativar a replicação e ativar a replicação para um VM depois de ativar 1.1.


## <a name="required-user-permissions"></a><a id="required-user-permissions"></a> Permissões necessárias do utilizador
A Recuperação do Site requer que o utilizador tenha permissões para criar o cofre chave na região alvo e copiar chaves do cofre chave da região de origem para o cofre chave da região alvo.

Para permitir a replicação de VMs ativados por encriptação de disco a partir do portal Azure, o utilizador precisa das seguintes permissões tanto na **região de origem** como nos cofres-chave da região alvo.

- Permissões de cofre chave
    - Lista, Criar e Obter
    
- Permissões secretas do cofre chave
    - Operações de Gestão Secreta
        - Obter, Lista e Conjunto
    
- Permissões chave do cofre (necessárias apenas se os VMs usarem a chave de encriptação chave para encriptar chaves de encriptação do disco)
    - Principais Operações de Gestão
        - Obter, Listar e Criar
    - Operações Criptográficas
        - Desencriptação e Encriptação

Para gerir permissões, aceda ao recurso do cofre chave no portal. Adicione as permissões necessárias para o utilizador. O exemplo a seguir mostra como permitir permissões para o *cofre-chave ContosoWeb2Keyvault*, que está na região de origem.

1. Ir para **casa**  >  **Keyvaults**  >  **ContosoWeb2KeyVault > As políticas de acesso**.

   ![Janela de permissões de cofre chave](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-1.png)

2. Pode ver que não existem permissões de utilizador. **Selecione Adicionar novo**. Insira a informação do utilizador e permissões.

   ![Permissões keyvault](./media/azure-to-azure-how-to-enable-replication-ade-vms/key-vault-permission-2.png)

Se o utilizador que está a permitir a recuperação de desastres (DR) não tiver permissões para copiar as chaves, um administrador de segurança que tenha permissões apropriadas pode usar o seguinte script para copiar os segredos de encriptação e as chaves da região alvo.

Para obter permissões de resolução de problemas, consulte [os principais problemas](#trusted-root-certificates-error-code-151066) de permissão do cofre mais tarde neste artigo.

>[!NOTE]
>Para ativar a replicação de VMs ativados por encriptação de disco a partir do portal, precisa de pelo menos permissões de "Lista" nos cofres, segredos e chaves das chaves.

## <a name="copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script"></a>Copiar chaves de encriptação do disco para a região DR utilizando o script PowerShell

1. [Abra o código de script em bruto "CopyKeys".](https://aka.ms/ade-asr-copy-keys-code)
2. Copie o script para um ficheiro e nomeie-o **Copy-keys.ps1**.
3. Abra a aplicação Windows PowerShell e vá para a pasta onde guardou o ficheiro.
4. Executa Copy-keys.ps1.
5. Forneça credenciais de Azure para se inscrever.
6. Selecione a **subscrição Azure** dos seus VMs.
7. Aguarde que os grupos de recursos carreguem e, em seguida, selecione o grupo de **Recursos** dos seus VMs.
8. Selecione os VMs da lista que está exposta. Apenas VMs que estão habilitados para encriptação de disco estão na lista.
9. Selecione a **localização do alvo.**

    - **Cofres de chaves de encriptação de disco**
    - **Cofres chave de encriptação chave**

   Por predefinição, a Recuperação do Local cria um novo cofre chave na região alvo. O nome do cofre tem um sufixo "asr" baseado nas chaves de encriptação do disco VM de origem. Se já existe um cofre chave que foi criado pela Recuperação do Local, é reutilizado. Selecione um cofre de chave diferente da lista, se necessário.

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
    - **Grupo de recursos-alvo**: O grupo de recursos ao qual todas as suas máquinas virtuais replicadas pertencem. Por predefinição, a Recuperação do Site cria um novo grupo de recursos na região alvo. O nome recebe o sufixo "asr". Se já existe um grupo de recursos que foi criado pela Azure Site Recovery, é reutilizado. Também pode optar por personalizá-lo, como mostra a seguinte secção. A localização do grupo de recursos-alvo pode ser qualquer região de Azure, exceto a região onde as máquinas virtuais de origem estão hospedadas.
    - **Rede virtual alvo**: Por padrão, a Recuperação do Site cria uma nova rede virtual na região alvo. O nome recebe o sufixo "asr". Está mapeado para a sua rede de origem e usado para qualquer proteção futura. [Saiba mais](./azure-to-azure-network-mapping.md) sobre mapeamento de rede.
    - **Contas de armazenamento de destino (se a sua fonte VM não utilizar discos geridos)**: Por padrão, a Recuperação do Site cria uma nova conta de armazenamento-alvo imitando a configuração de armazenamento de VM de origem. Se uma conta de armazenamento já existe, é reutilizada.
    - **Replica discos geridos (se a sua fonte VM utiliza discos geridos)**: A Recuperação do Local cria novos discos geridos por réplicas na região alvo para espelhar os discos geridos da fonte VM do mesmo tipo de armazenamento (padrão ou premium) que os discos geridos pela VM de origem.
    - **Contas de armazenamento de cache**: A Recuperação do Local necessita de uma conta de armazenamento extra chamada armazenamento *cache* na região de origem. Todas as alterações nos VMs de origem são rastreadas e enviadas para a conta de armazenamento de cache. Depois são replicados no local do alvo.
    - **Conjunto de disponibilidade**: Por predefinição, a Recuperação do Site cria uma nova disponibilidade definida na região alvo. O nome tem o sufixo "asr". Se já existe um conjunto de disponibilidade que foi criado pela Recuperação do Site, é reutilizado.
    - **Cofres-chave de encriptação do** disco : Por padrão, a Recuperação do Local cria um novo cofre chave na região alvo. Tem um sufixo "asr" que é baseado nas chaves de encriptação do disco VM de origem. Se um cofre chave que foi criado pela Azure Site Recovery já existe, é reutilizado.
    - **Cofres chave de encriptação** chave : Por padrão, a Recuperação do Local cria um novo cofre chave na região alvo. O nome tem um sufixo "asr" que é baseado nas chaves de encriptação da chave VM de origem. Se um cofre-chave criado pela Azure Site Recovery já existe, é reutilizado.
    - **Política de replicação**: Define as definições para o histórico de retenção de pontos de recuperação e frequência de instantâneo consistente com aplicações. Por predefinição, a Recuperação do Site cria uma nova política de replicação com definições predefinidos de *24 horas* para a retenção de pontos de recuperação e *60 minutos* para a frequência de instantâneo consistente com aplicações.

## <a name="customize-target-resources"></a>Personalizar recursos-alvo

Siga estes passos para modificar as definições de alvo padrão de recuperação do site.

1. **Selecione Personalizar** ao lado de "Assinatura alvo" para modificar a subscrição de destino padrão. Selecione a subscrição da lista de subscrições disponíveis no inquilino AZure AD.

2. **Selecione Personalizar** ao lado de "Conjuntos de recursos, rede, armazenamento e disponibilidade" para modificar as seguintes definições predefinidos:
    - Para **o grupo de recursos Target,** selecione o grupo de recursos da lista de grupos de recursos na localização alvo da subscrição.
    - Para **a rede virtual Target,** selecione a rede a partir de uma lista de redes virtuais no local alvo.
    - Para **disponibilidade definida,** pode adicionar definições de disponibilidade ao VM, se fizerem parte de um conjunto de disponibilidade na região de origem.
    - Para **contas de Armazenamento Alvo,** selecione a conta a utilizar.

2. **Selecione Personalizar** ao lado de "Definições de encriptação" para modificar as seguintes definições predefinidos:
   - Para **o cofre da chave de encriptação** do disco Target, selecione o cofre da chave de encriptação do disco alvo a partir da lista de cofres-chave na localização alvo da subscrição.
   - Para **o cofre da chave de encriptação do target,** selecione o cofre da chave de encriptação do alvo a partir da lista de cofres-chave na localização alvo da subscrição.

3. **Selecione Criar recurso-alvo**  >  **Ativar a replicação**.
4. Depois de os VMs estarem ativados para replicação, pode verificar o estado de saúde dos VMs em **itens replicados**.

>[!NOTE]
>Durante a replicação inicial, o estado pode demorar algum tempo a refrescar-se, sem progressos aparentes. Clique **em Refresh**  para obter o estado mais recente.

## <a name="update-target-vm-encryption-settings"></a>Atualizar as definições de encriptação VM alvo
Nos seguintes cenários, será necessário atualizar as definições de encriptação VM alvo:
  - Ativou a replicação da recuperação do local no VM. Mais tarde, ativou a encriptação do disco na fonte VM.
  - Ativou a replicação da recuperação do local no VM. Mais tarde, alterou a chave de encriptação do disco ou chave de encriptação na fonte VM.

Pode utilizar [um script](#copy-disk-encryption-keys-to-the-dr-region-by-using-the-powershell-script) para copiar as chaves de encriptação para a região alvo e, em seguida, atualizar as definições de encriptação alvo no cofre de **serviços recoverysesso**  >    >  **item Properties**  >  **Compute e Network**.

![Atualizar janela de diálogo de definições ADE](./media/azure-to-azure-how-to-enable-replication-ade-vms/update-ade-settings.png)

## <a name="troubleshoot-key-vault-permission-issues-during--azure-to-azure-vm-replication"></a><a id="trusted-root-certificates-error-code-151066"></a>Problemas de permissão de cofre de resolução de problemas durante a replicação do VM Azure-to-Azure

A recuperação do local de Azure requer pelo menos ler a permissão no cofre chave da região source e escrever permissão no cofre chave da região alvo para ler o segredo e copiá-lo para o cofre chave da região alvo. 

**Causa 1:** Não tem permissão "GET" na **região de origem Key vault** para ler as chaves. </br>
**Como corrigir:** Independentemente de ser administrador de subscrição ou não, é importante que obtenha permissão no cofre da chave.

1. Vá para a região de origem Cofre chave que neste exemplo é "ContososourceKeyvault" > **Políticas de Acesso** 
2. Em **Select Principal** adicione o seu nome de utilizador, por exemplo: dradmin@contoso.com "
3. Sob **as permissões chave** selecione GET 
4. Sob **Permissão Secreta** selecione GET 
5. Salvar a política de acesso

**Causa 2:** Não precisa de permissão no cofre da chave da **região alvo** para escrever as chaves. </br>

*Por exemplo:* Tente replicar um VM que tem o cofre *chave ContososourceKeyvault* numa região de origem.
Tem todas as permissões no cofre da região. Mas durante a proteção, seleciona o cofre chave já criado ContosotargetKeyvault, que não tem permissões. Ocorre um erro.

Permissão necessária no [cofre da chave alvo](#required-user-permissions)

**Como corrigir:** Vá a **casa**  >  **Keyvaults**  >  **ContosotargetKeyvault** As políticas de  >  **acesso** e adicione as permissões apropriadas.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais](site-recovery-test-failover-to-azure.md) sobre a execução de um teste falhado.
