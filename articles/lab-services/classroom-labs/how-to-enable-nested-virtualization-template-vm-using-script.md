---
title: Ativar a virtualização aninhada num modelo VM em Serviços de Laboratório Azure (Script) / Microsoft Docs
description: Aprenda a criar um Modelo VM com vários VMs no interior.  Por outras palavras, permita a virtualização aninhada num modelo VM nos Serviços de Laboratório Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 56e5ad21f94521565b4df193b2450a1c994b66f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79503039"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>Ativar a virtualização aninhada em uma máquina virtual modelo em Serviços de Laboratório Azure usando um script

A virtualização aninhada permite criar um ambiente multi-VM dentro da máquina virtual modelo de um laboratório. A publicação do modelo fornecerá a cada utilizador do laboratório uma máquina virtual configurada com vários VMs dentro dele.  Para obter mais informações sobre a virtualização aninhada e os serviços de laboratório do Azure, consulte [Enable aninhadamente virtualização em uma máquina virtual modelo em Serviços de Laboratório Azure](how-to-enable-nested-virtualization-template-vm.md).

Os passos neste artigo focam-se na criação de uma virtualização aninhada para o Windows Server 2016 ou windows Server 2019. Utilizará um script para configurar a máquina de modelocom Hyper-V.  Os seguintes passos irão guiá-lo sobre como usar os [scripts Hyper-V](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)dos Serviços de Laboratório .

>[!IMPORTANT]
>Selecione **Large (virtualização aninhada)** ou **Média (virtualização aninhada)** para o tamanho da máquina virtual ao criar o laboratório.  A virtualização aninhada não funcionará de outra forma.  

## <a name="run-script"></a>Executar o script

1. Se estiver a utilizar o Internet Explorer, poderá ter de adicionar `https://github.com` à lista de sites fidedignos.
    1. Abra o Internet Explorer.
    1. Selecione o ícone da engrenagem e escolha **as opções**de Internet.  
    1. Quando o diálogo das **Opções** de Internet aparecer, selecione **Security**, selecione **Sites Fidedignos,** clique no botão **Sites.**
    1. Quando o diálogo de sites `https://github.com` **Fidedignos** aparecer, adicione à lista de sites fidedignos e selecione **Fechar**.

        ![Sites fidedignos](../media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. Descarregue os ficheiros de repositório Git conforme descrito nos seguintes passos.
    1. Vai [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/)para.
    1. Clique no botão **Clone ou Descarregar.**
    1. Clique em **Baixar ZIP**.
    1. Extrair o ficheiro ZIP

    >[!TIP]
    >Também pode clonar o repositório [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git)Git em .

1. Lançamento **PowerShell** no modo **Administrador.**
1. Na janela PowerShell, navegue para a pasta com o script descarregado. Se estiver a navegar a partir da pasta superior dos ficheiros `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`de repositório, o script está localizado em .
1. Pode ter que mudar a política de execução para executar com sucesso o guião. Execute o seguinte comando:

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. Execute o script:

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > O guião pode exigir que a máquina seja reiniciada. Siga as instruções do script e reeexecute o script até que o **Script esteja concluído** na saída.
1. Não se esqueça de redefinir a política de execução. Execute o seguinte comando:

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>Conclusão

Agora a sua máquina de modelo está pronta para criar máquinas virtuais Hyper-V. Consulte [Criar uma máquina virtual em Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) para obter instruções sobre como criar máquinas virtuais Hyper-V. Consulte também o [Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/) para verificar os sistemas operativos e software disponíveis.  

## <a name="next-steps"></a>Passos seguintes

Os próximos passos são comuns à criação de qualquer laboratório.

- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir quota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um horário](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de inscrição de e-mail para estudantes](how-to-configure-student-usage.md#send-invitations-to-users)