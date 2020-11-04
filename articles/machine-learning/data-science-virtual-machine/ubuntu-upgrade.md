---
title: Como atualizar a sua Máquina Virtual de Ciência de Dados para Ubuntu 18.04
titleSuffix: Azure Data Science Virtual Machine
description: Saiba como fazer upgrade de CentOS e Ubuntu 16.04 para a mais recente Máquina Virtual Ubuntu 18.04 Data Science.
keywords: aprendizagem profunda, IA, ferramentas de ciência de dados, máquina virtual de ciência de dados, processo de ciência de dados de equipa
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 5b98384d4d735f4c124c6af40d6edbff896900ce
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93320987"
---
# <a name="upgrade-your-data-science-virtual-machine-to-ubuntu-1804"></a>Atualizar a Máquina Virtual de Ciência de Dados para o Ubuntu 18.04

Se tiver uma Máquina Virtual de Ciência de Dados a executar uma versão mais antiga, como Ubuntu 16.04 ou CentOS, deve migrar o seu DSVM para Ubuntu 18.04. A migração garantirá que obtém os mais recentes patches do sistema operativo, controladores, software pré-instalado e versões da biblioteca. Este documento diz-lhe como migrar de versões mais antigas de Ubuntu ou do CentOS. 

## <a name="prerequisites"></a>Pré-requisitos

- Familiaridade com o SSH e a linha de comando Linux

## <a name="overview"></a>Descrição geral

Há duas formas possíveis de migrar:

- Migração no local, também chamada de migração do "mesmo servidor". Esta migração atualiza o VM existente sem criar uma nova máquina virtual. A migração no local é a forma mais fácil de migrar de Ubuntu 16.04 para Ubuntu 18.04.
- Migração lado a lado, também chamada de migração "inter-servidor". Esta migração transfere dados da máquina virtual existente para um VM recém-criado. A migração lado a lado é a forma de migrar de Centos para Ubuntu 18.04. Pode preferir a migração lado a lado para upgrade entre versões Ubuntu se sentir que a sua antiga instalação ficou desnecessariamente desordenada.

## <a name="snapshot-your-vm-in-case-you-need-to-roll-back"></a>Snapshot o seu VM no caso de precisar de recuar

No portal Azure, utilize a barra de pesquisa para encontrar a funcionalidade **Snapshots.** 

:::image type="content" source="media/ubuntu_upgrade/azure-portal-search-bar.png" alt-text="Screenshot mostrando portal Azure e barra de pesquisa, com **Snapshots** em destaque":::

1. **Selecione Adicionar** , que irá levá-lo para a página **'Criar' instantâneo.** Selecione o grupo de subscrição e recursos da sua máquina virtual. Para **a Região** , selecione a mesma região em que o armazenamento alvo existe. Selecione o disco de armazenamento DSVM e opções de backup adicionais. **O HDD padrão** é um tipo de armazenamento apropriado para este cenário de backup.

:::image type="content" source="media/ubuntu_upgrade/create-snapshot-options.png" alt-text="Screenshot mostrando as opções de 'Criar instantâneo'":::

2. Uma vez preenchidos todos os detalhes e as validações passam, selecione **Review + create** para validar e criar o instantâneo. Quando o instantâneo estiver concluído com sucesso, verá uma mensagem a dizer-lhe que a implementação está completa.

## <a name="in-place-migration"></a>Migração no local

Se estiver a migrar uma versão ubuntu mais antiga, pode optar por fazer uma migração no local. Esta migração não cria uma nova máquina virtual e tem menos passos do que uma migração lado a lado.  Se deseja fazer uma migração lado a lado porque quer mais controlo ou porque está a migrar de uma distribuição diferente, como o CentOS, salte para a secção [de migração lado](#side-by-side-migration) a lado. 

1. A partir do portal Azure, inicie o seu DSVM e inicie sSH. Para tal, selecione **Connect** e **SSH** e siga as instruções de ligação. 

1. Uma vez ligado a uma sessão terminal no seu DSVM, execute o seguinte comando de atualização:

    ```bash
    sudo do-release-upgrade
    ```

O processo de atualização levará algum tempo a ser concluído. Quando terminar, o programa pedirá permissão para reiniciar a máquina virtual. Resposta **Sim.** Será desligado da sessão SSH à medida que o sistema reinicia.

### <a name="if-necessary-regenerate-ssh-keys"></a>Se necessário, regenerar as teclas SSH

> [!IMPORTANT] 
> Depois de atualizar e reiniciar, poderá ter de regenerar as suas teclas SSH.

Depois de o seu VM ter atualizado e reiniciado, tente aceder-lhe novamente via SSH. O endereço IP pode ter mudado durante o reboot, por isso confirme-o antes de tentar ligar.

Se receber o erro **A identificação do anfitrião remoto mudou,** terá de regenerar as suas credenciais SSH.

:::image type="content" source="media/ubuntu_upgrade/remote-host-warning.png" alt-text="Imagem powerShell mostrando identificação remota do anfitrião mudou aviso":::

Para tal, na sua máquina local, faça o comando:

```bash
ssh-keygen -R "your server hostname or ip"
```

Deve agora ser capaz de se conectar com o SSH. Se ainda tiver problemas, na página **'Ligar'** siga o link para **problemas de conectividade SSH resolução de problemas**.

## <a name="side-by-side-migration"></a>Migração lado a lado

Se estiver a migrar do CentOS ou quiser uma instalação de SO limpa, pode fazer uma migração lado a lado. Este tipo de migração tem mais passos, mas dá-lhe controlo sobre quais os ficheiros que são transportados.

As migrações de outros sistemas com base no mesmo conjunto de pacotes de origem a montante devem ser relativamente simples, por [exemplo, as FAQ/CentOS3](https://wiki.centos.org/FAQ/CentOS3).

Pode optar por atualizar as partes do sistema operativo do sistema de ficheiros e deixar os diretórios dos utilizadores, como por exemplo `/home` no local. Se deixar os antigos diretórios domésticos do utilizador no local, espere alguns problemas com os menus GNOME/KDE e outros itens de ambiente de trabalho. Pode ser mais fácil criar novas contas de utilizador e montar os antigos diretórios em algum outro lugar do sistema de ficheiros para referência, cópia ou ligação do material dos utilizadores após a migração.

### <a name="migration-at-a-glance"></a>Migração num ápice

1.  Crie uma imagem do seu VM existente, conforme descrito anteriormente

1.  Criar um disco a partir desse instantâneo

1.  Criar uma nova Máquina Virtual de Ciência de Dados Ubuntu

1.  Recriar a conta de utilizador na nova máquina virtual

1.  Monte o disco do VM instantâneo como um disco de dados na sua nova Máquina Virtual de Ciência de Dados

1.  Copiar manualmente os dados desejados

### <a name="create-a-disk-from-your-vm-snapshot"></a>Crie um disco a partir do seu instantâneo VM

Se ainda não criou um instantâneo VM como descrito anteriormente, faça-o. 

1. No portal Azure, procure **discos** e selecione **Add** , que abrirá a página **do Disco.**

:::image type="content" source="media/ubuntu_upgrade/portal-disks-search.png" alt-text="Screenshot do portal Azure mostrando a pesquisa da página de Discos e do botão Adicionar":::

2. Desajuste a **Subscrição,** **grupo de recursos** e **Região** para os valores do seu instantâneo VM. Escolha um **Nome** para o disco a ser criado.

3. Selecione **o tipo de Origem** como **Snapshot** e selecione o instantâneo VM como o **instantâneo 'Source'.** Reveja e crie o disco. 

:::image type="content" source="media/ubuntu_upgrade/disk-create-options.png" alt-text="Screenshot do diálogo de criação de disco mostrando opções":::

### <a name="create-a-new-ubuntu-data-science-virtual-machine"></a>Criar uma nova Máquina Virtual de Ciência de Dados Ubuntu

Crie uma nova Máquina Virtual de Ciência de Dados Ubuntu utilizando o [portal Azure](https://portal.azure.com) ou um [modelo ARM](./dsvm-tutorial-resource-manager.md). 

### <a name="recreate-user-accounts-on-your-new-data-science-virtual-machine"></a>Recrie a conta(s) do utilizador na sua nova Máquina Virtual de Ciência de Dados

Uma vez que estará apenas a copiar dados do seu computador antigo, terá de recriar quaisquer contas de utilizador e ambientes de software que queira utilizar na nova máquina.

O Linux é suficientemente flexível para permitir que personalize diretórios e caminhos na sua nova instalação para seguir a sua antiga máquina. No entanto, em geral, é mais fácil usar o layout preferido do Ubuntu moderno e modificar o seu ambiente de utilizador e scripts para se adaptar.

Para obter mais informações, consulte [Quickstart: Configurar a Máquina Virtual de Ciência de Dados para Linux (Ubuntu)](./dsvm-ubuntu-intro.md).

### <a name="mount-the-disk-of-the-snapshotted-vm-as-a-data-disk-on-your-new-data-science-virtual-machine"></a>Monte o disco do VM instantâneo como um disco de dados na sua nova Máquina Virtual de Ciência de Dados

1. No portal Azure, certifique-se de que a sua Máquina Virtual de Ciência de Dados está em funcionamento.

2. No portal Azure, aceda à página da sua Máquina Virtual de Ciência de Dados. Escolha a lâmina **discos** no trilho esquerdo. Escolha **Selecionar Os discos existentes.**

3. No **dropdown** do nome do disco, selecione o disco que criou a partir da foto do seu antigo VM.

:::image type="content" source="media/ubuntu_upgrade/attach-data-disk.png" alt-text="Screenshot da página de opções DSVM mostrando opções de anexo de disco":::

4. **Selecione Guardar** para atualizar a sua máquina virtual.

> [!Important]
> O seu VM deve estar a funcionar no momento em que anexar o disco de dados. Se o VM não estiver em funcionamento, os discos podem ser adicionados numa ordem incorreta, conduzindo a um sistema confuso e potencialmente não inicializável. Se adicionar o disco de dados com o VM desligado, escolha o **X** ao lado do disco de dados, inicie o VM e reexmoda-o novamente.

### <a name="manually-copy-the-wanted-data"></a>Copiar manualmente os dados desejados 

1. Inscreva-se na sua máquina virtual em funcionamento utilizando o SSH.

2. Confirme que anexou o disco criado a partir da sua foto de VM antiga, executando o seguinte comando:

    ```bash
    lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i 'sd'
    ```
    
    Os resultados devem parecer-se com a seguinte imagem. Na imagem, o disco `sda1` é montado na raiz e é o disco de `sdb2` `/mnt` risco. O disco de dados criado a partir do instantâneo do seu antigo VM é identificado como `sdc1` mas ainda não está disponível, como evidenciado pela falta de uma localização de montagem. Os seus resultados podem ter identificadores diferentes, mas deve ver um padrão semelhante.
    
    :::image type="content" source="media/ubuntu_upgrade/lsblk-results.png" alt-text="Screenshot da saída de Isblk, mostrando unidade de dados não desmontada":::
    
3. Para aceder à unidade de dados, crie uma localização para ele e monte-a. `/dev/sdc1`Substitua-o pelo valor adequado devolvido `lsblk` por:

    ```bash
    sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
    ```
    
4. Agora, `/datadrive` contém os diretórios e ficheiros da sua antiga Máquina Virtual de Ciência de Dados. Mova ou copie os diretórios ou ficheiros que pretende da unidade de dados para o novo VM como desejar.

Para obter mais informações, consulte [utilizar o portal para anexar um disco de dados a um VM Linux](../../virtual-machines/linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk).

## <a name="connect-and-confirm-version-upgrade"></a>Ligar e confirmar atualização da versão

Quer tenha feito uma migração no local ou lado a lado, confirme que melhorou com sucesso. A partir de uma sessão terminal, corra: 

```bash
cat /etc/os-release
```

E devias ver que estás a comandar o Ubuntu 18.04.

:::image type="content" source="media/ubuntu_upgrade/ssh-os-release.png" alt-text="Screenshot do terminal Ubuntu mostrando dados da versão OS":::

A mudança de versão também é mostrada no portal Azure.

:::image type="content" source="media/ubuntu_upgrade/portal-showing-os-version.png" alt-text="Screenshot do portal mostrando propriedades DSVM, incluindo versão OS":::

## <a name="next-steps"></a>Passos seguintes

- [Ciência de dados com uma máquina de ciência de dados Ubuntu em Azure](./linux-dsvm-walkthrough.md)
- [Que ferramentas estão incluídas na Máquina Virtual de Ciência de Dados do Azure?](./tools-included.md)