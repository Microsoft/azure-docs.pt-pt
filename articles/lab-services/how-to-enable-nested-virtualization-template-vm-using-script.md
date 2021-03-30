---
title: Ativar a virtualização aninhada num modelo VM em Azure Lab Services (Script) | Microsoft Docs
description: Aprenda a criar um VM modelo com vários VMs no interior usando um script.  Por outras palavras, ative a virtualização aninhada num modelo VM em Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5ae50bd11ab9a8adb769920f6d473a2ff2ce9342
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91251500"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>Ativar a virtualização aninhada em uma máquina virtual de modelo em Azure Lab Services usando um script

A virtualização aninhada permite-lhe criar um ambiente multi-VM dentro da máquina virtual de modelo de um laboratório. A publicação do modelo fornecerá a cada utilizador em laboratório uma máquina virtual configurada com vários VMs dentro do mesmo.  Para obter mais informações sobre a virtualização aninhada e os Serviços de Laboratório Azure, consulte [Ativar a virtualização aninhada numa máquina virtual de modelo nos Serviços Azure Lab](how-to-enable-nested-virtualization-template-vm.md).

Os passos deste artigo focam-se na configuração da virtualização aninhada para o Windows Server 2016, Windows Server 2019 ou Windows 10. Você usará um script para configurar a máquina de modelo com Hyper-V.  Os seguintes passos irão guiá-lo através da utilização dos [scripts Hiper-V dos Serviços de Laboratório.](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)

>[!IMPORTANT]
>Selecione **Large (virtualização aninhada)** ou **Medium (virtualização aninhada)** para o tamanho da máquina virtual ao criar o laboratório.  A virtualização aninhada não funcionará de outra forma.  

## <a name="run-script"></a>Executar o script

1. Se estiver a utilizar o Internet Explorer, poderá ter de adicionar à `https://github.com` lista de sites fidedignos.
    1. Abra o Internet Explorer.
    1. Selecione o ícone de engrenagem e escolha **as opções de Internet**.  
    1. Quando o diálogo **'Opções de Internet'** aparecer, selecione **Segurança**, selecione **Sites Fidedignos**, clique no botão **Sites.**
    1. Quando o diálogo **de sites Fidedignos** aparecer, adicione `https://github.com` à lista de sites fidedignos e selecione **Close**.

        ![Sites fidedignos](./media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. Descarregue os ficheiros do repositório git como descrito nos passos seguintes.
    1. Vai  [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/) para.
    1. Clique no botão **Clone ou Download.**
    1. Clique **em Baixar ZIP**.
    1. Extrair o ficheiro ZIP

    >[!TIP]
    >Também pode clonar o repositório git em [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git) .

1. Lançar **PowerShell** no modo **administrador.**
1. Na janela PowerShell, navegue para a pasta com o script descarregado. Se estiver a navegar a partir da pasta superior dos ficheiros do repositório, o script está localizado em `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\` .
1. Pode ter de alterar a política de execução para executar o script com sucesso. Execute o seguinte comando:

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. Execute o script:

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > O script pode exigir que a máquina seja reiniciada. Siga as instruções do script e reencando o script até que o **Script seja concluído** na saída.
1. Não se esqueça de redefinir a política de execução. Execute o seguinte comando:

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>Conclusão

Agora a sua máquina de modelo está pronta para criar máquinas virtuais Hyper-V. Consulte [Criar uma máquina virtual em Hiper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) para obter instruções sobre como criar máquinas virtuais Hiper-V. Consulte também o [Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/) para verificar os sistemas operativos e o software disponíveis.  

## <a name="next-steps"></a>Passos seguintes

Os próximos passos são comuns para montar qualquer laboratório.

- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota definida](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um horário](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de inscrição de e-mail para estudantes](how-to-configure-student-usage.md#send-invitations-to-users)