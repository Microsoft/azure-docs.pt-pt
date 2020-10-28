---
title: Tutorial - Restaurar ficheiros a um VM com backup Azure
description: Saiba como realizar restauros ao nível do ficheiro numa VM do Azure com Serviços de Cópia de Segurança e de Recuperação.
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: cf55b9d64d7d716aee9862b0e1e3e24966629286
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746698"
---
# <a name="restore-files-to-a-virtual-machine-in-azure"></a>Restaurar ficheiros para uma máquina virtual no Azure

O Azure Backup cria pontos de recuperação que são armazenados em cofres de recuperação georredundantes. Quando restaurar a partir de um ponto de recuperação, pode restaurar a VM completa ou ficheiros individuais. Este artigo dispõe de detalhes sobre como restaurar ficheiros individuais. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Listar e selecionar pontos de recuperação
> * Ligar um ponto de recuperação a uma VM
> * Restaurar ficheiros a partir de um ponto de recuperação

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o CLI localmente, este tutorial requer que esteja a executar a versão 2.0.18 do CLI Azure ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer uma VM Linux que tenha sido protegida com o Azure Backup. Para simular um processo de recuperação e eliminação acidental de ficheiro, elimine uma página de um servidor Web. Se precisar de uma VM Linux que execute um servidor Web e tenha sido protegida com o Azure Backup, veja [Realizar uma cópia de segurança de uma máquina virtual no Azure com a CLI](quick-backup-vm-cli.md).

## <a name="backup-overview"></a>Descrição geral da Cópia de Segurança

Quando o Azure inicia uma cópia de segurança, a extensão da cópia de segurança na VM tira um instantâneo de ponto no tempo. A extensão da cópia de segurança é instalada na VM quando a primeira cópia de segurança é pedida. O Azure Backup também pode tirar uma foto do armazenamento subjacente se o VM não estiver a funcionar quando a cópia de segurança ocorrer.

Por predefinição, o Azure Backup cria uma cópia de segurança consistente com o sistema de ficheiros. Assim que o Azure Backup tira o instantâneo, os dados são transferidos para o cofre dos Serviços de Recuperação. Para maximizar a eficiência, o Azure Backup identifica e transfere apenas os blocos de dados que foram alterados desde a cópia de segurança anterior.

Quando a transferência de dados estiver concluída, o instantâneo é removido e é criado um ponto de recuperação.

## <a name="delete-a-file-from-a-vm"></a>Eliminar um ficheiro de uma VM

Se acidentalmente eliminou ou realizou alterações a um ficheiro, pode restaurar ficheiros individuais a partir de um ponto de recuperação. Este processo permite-lhe procurar os ficheiros com cópia de segurança num ponto de recuperação e restaurar apenas os ficheiros que precisa. Neste exemplo, eliminamos um ficheiro a partir de um servidor Web para demonstrar o processo de recuperação ao nível do ficheiro.

1. Para ligar à VM, obtenha o endereço IP da VM com [az vm show](/cli/azure/vm#az-vm-show):

     ```azurecli-interactive
     az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
     ```

2. Para confirmar que o site funciona atualmente, abra um browser no endereço IP público da sua VM. Deixe a janela do browser aberta.

    ![Página Web NGINX predefinida](./media/tutorial-restore-files/nginx-working.png)

3. Ligar à VM com SSH. Substitua *publicIpAddress* pelo endereço IP público que obteve num comando anterior:

    ```bash
    ssh publicIpAddress
    ```

4. Elimine a página predefinida do servidor Web em */var/www/html/index.nginx-debian.html* da seguinte forma:

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```

5. No browser, atualize a página Web. O site já não carrega a página, conforme mostrado no exemplo seguinte:

    ![O site NGINX já não carrega a página predefinida](./media/tutorial-restore-files/nginx-broken.png)

6. Feche a sessão SSH para a VM da seguinte forma:

    ```bash
    exit
    ```

## <a name="generate-file-recovery-script"></a>Gerar script de recuperação de ficheiros

Para restaurar ficheiros, o Azure Backup oferece um script para executar na sua VM que liga o ponto de recuperação como uma unidade local. Pode procurar esta unidade local, restaurar ficheiros para a própria VM e, em seguida, desligar o ponto de recuperação. O Azure Backup continua a fazer a cópia de segurança dos seus dados na política atribuída para agendamento e retenção.

1. Para listar pontos de recuperação para a sua VM, utilize [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list). Neste exemplo, selecionamos o mais recente ponto de recuperação para o VM nomeado *myVM* que está protegido no *myRecoveryServicesVault:*

    ```azurecli-interactive
    az backup recoverypoint list \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --query [0].name \
        --output tsv
    ```

2. Para obter o script que liga, ou monta, o ponto de recuperação à VM, utilize [az backup restore files mount-rp](/cli/azure/backup/restore/files#az-backup-restore-files-mount-rp). O exemplo seguinte obtém o script para o VM nomeado *myVM* que está protegido no *meuRecoveryServicesVault* .

    Substitua *myRecoveryPointName* pelo nome do ponto de recuperação que obteve no comando anterior:

    ```azurecli-interactive
    az backup restore files mount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

    O script é transferido e é apresentada uma palavra-passe, como no exemplo seguinte:

    ```output
    File downloaded: myVM_we_1571974050985163527.sh. Use password c068a041ce12465
    ```

3. Para transferir o script para a VM, utilize a Cópia Segura (SCP). Indique o nome do seu script transferido e substitua *publicIpAddress* pelo endereço IP público da sua VM. Certifique-se de que inclui `:` à direita no fim do comando SCP da seguinte forma:

    ```bash
    scp myVM_we_1571974050985163527.sh 52.174.241.110:
    ```

## <a name="restore-file-to-your-vm"></a>Restaurar ficheiro para a VM

Com o script de recuperação copiado para a VM, já pode ligar o ponto de recuperação e restaurar ficheiros.

>[!NOTE]
> Consulte [aqui](backup-azure-restore-files-from-vm.md#selecting-the-right-machine-to-run-the-script) para ver se consegue executar o script no seu VM antes de continuar.

1. Ligar à VM com SSH. Substitua *publicIPAddress* pelo endereço IP público da VM da seguinte forma:

    ```bash
    ssh publicIpAddress
    ```

2. Para permitir que o script seja executado corretamente, adicione permissões de execução com **chmod** . Introduza o nome do seu próprio script:

    ```bash
    chmod +x myVM_we_1571974050985163527.sh
    ```

3. Para montar o ponto de recuperação, execute o script. Introduza o nome do seu próprio script:

    ```bash
    ./myVM_we_1571974050985163527.sh
    ```

    À medida que o script é executado, é-lhe pedido que introduza uma palavra-passe para aceder ao ponto de recuperação. Introduza a palavra-passe apresentada no resultado do comando [az backup restore files mount-rp](/cli/azure/backup/restore/files#az-backup-restore-files-mount-rp) anterior que gerou o script de recuperação.

    O resultado do script indica-lhe o caminho para o ponto de recuperação. O seguinte resultado de exemplo mostra que o ponto de recuperação está montado em */home/azureuser/myVM-20170919213536/Volume1* :

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : c068a041ce12465

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170919213536/Volume1

    ************ Open File Explorer to browse for files. ************
    ```

4. Utilize **cp** para copiar a página Web NGINX predefinida a partir do ponto de recuperação montado novamente na localização original do ficheiro. Substitua o ponto de montagem */home/azureuser/myVM-20170919213536/Volume1* pela sua própria localização:

    ```bash
    sudo cp /home/azureuser/myVM-20170919213536/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```

5. No browser, atualize a página Web. O site é agora novamente carregado corretamente, conforme mostrado no exemplo seguinte:

    ![O site do NGINX carrega agora corretamente](./media/tutorial-restore-files/nginx-restored.png)

6. Feche a sessão SSH para a VM da seguinte forma:

    ```bash
    exit
    ```

7. Desmonte o ponto de recuperação da VM com [az backup restore files unmount-rp](/cli/azure/backup/restore/files#az-backup-restore-files-unmount-rp). O exemplo seguinte desmonta o ponto de recuperação da VM denominada *myVM* em *myRecoveryServicesVault* .

    Substitua *myRecoveryPointName* pelo nome do ponto de recuperação que obteve nos comandos anteriores:

    ```azurecli-interactive
    az backup restore files unmount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ligou um ponto de recuperação a uma VM e restaurou ficheiros para um servidor Web. Aprendeu a:

> [!div class="checklist"]
>
> * Listar e selecionar pontos de recuperação
> * Ligar um ponto de recuperação a uma VM
> * Restaurar ficheiros a partir de um ponto de recuperação

Avance para o próximo tutorial para saber mais sobre como fazer cópias de segurança do Windows Server para o Azure.

> [!div class="nextstepaction"]
> [Cópia de Segurança do Windows Server para o Azure](tutorial-backup-windows-server-to-azure.md)
