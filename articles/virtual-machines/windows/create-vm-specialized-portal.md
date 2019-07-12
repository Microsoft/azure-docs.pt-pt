---
title: Criar uma VM do Windows a partir de um VHD especializado no portal do Azure | Documentos da Microsoft
description: Crie uma nova VM do Windows a partir de um VHD no portal do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/18/2019
ms.author: cynthn
ms.openlocfilehash: cadd4b16ab111f46e49429c6d99e0e692325b3b1
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718932"
---
# <a name="create-a-vm-from-a-vhd-by-using-the-azure-portal"></a>Criar uma VM a partir de um VHD com o portal do Azure

Existem várias formas de criar uma máquina virtual (VM) no Azure: 

- Se já tiver um disco rígido virtual (VHD) para utilizar ou que pretende copiar o VHD a partir de uma VM existente para utilizar, pode criar uma nova VM pelo *anexar* o VHD para a nova VM como um disco de SO. 

- Pode criar uma nova VM a partir de VHD de uma VM que tenha sido eliminada. Por exemplo, se tiver uma VM do Azure que não está a funcionar corretamente, pode eliminar a VM e utilize o VHD para criar uma nova VM. Pode reutilizar o mesmo VHD ou criar uma cópia do VHD ao criar um instantâneo e, em seguida, criar um novo disco gerido a partir do instantâneo. Apesar de a criar instantâneo de um processo mais algumas etapas, preserva o VHD e fornece-lhe uma contingência.

- Tirar uma VM clássica e utilizar o VHD para criar uma nova VM que utiliza o modelo de implementação do Resource Manager e os discos geridos. Para obter melhores resultados, **parar** a VM clássica no portal do Azure antes de criar o instantâneo.
 
- Pode criar uma VM do Azure a partir de um VHD no local ao carregar o VHD no local e ligá-la para uma nova VM. Utilizar o PowerShell ou outra ferramenta para carregar o VHD para uma conta de armazenamento e, em seguida, criar um disco gerido a partir do VHD. Para obter mais informações, consulte [carregar um VHD especializado](create-vm-specialized.md#option-2-upload-a-specialized-vhd). 

Não utilize um disco especializado, se quiser criar múltiplas VMs. Em vez disso, para implementações maiores, [criar uma imagem](capture-image-resource.md) e, em seguida [utilizar essa imagem para criar múltiplas VMs](create-vm-generalized-managed.md).


## <a name="copy-a-disk"></a>Copiar um disco

Criar um instantâneo e, em seguida, criar um disco a partir do instantâneo. Esta estratégia permite-lhe manter o VHD como contingência:

1. Partir do [portal do Azure](https://portal.azure.com), no menu da esquerda, selecione **todos os serviços**.
2. Na **todos os serviços** caixa de pesquisa, introduza **discos** e, em seguida, selecione **discos** para apresentar a lista de discos disponíveis.
3. Selecione o disco que pretende utilizar. O **disco** página para esse disco utilizado é apresentado.
4. No menu na parte superior, selecione **criar instantâneo**. 
5. Introduza um **nome** para o instantâneo.
6. Escolher uma **grupo de recursos** para o instantâneo. Pode utilizar qualquer grupo de recursos existente ou crie um novo.
7. Para **tipo de conta**, escolha o **Standard (HDD)** ou **Premium (SSD)** armazenamento.
8. Quando tiver terminado, selecione **criar** para criar o instantâneo.
9. Depois do instantâneo foi criado, selecione **criar um recurso** no menu à esquerda.
10. Na caixa de pesquisa, introduza **disco gerido** e, em seguida, selecione **Managed Disks** da lista.
11. Sobre o **Managed Disks** página, selecione **criar**.
12. Introduza um **nome** para o disco.
13. Escolher uma **grupo de recursos** para o disco. Pode utilizar qualquer grupo de recursos existente ou crie um novo. Esta seleção também vai ser utilizada como o grupo de recursos onde criar a VM a partir do disco.
14. Para **tipo de conta**, escolha o **Standard (HDD)** ou **Premium (SSD)** armazenamento.
15. Na **tipo de origem**, certifique-se **instantâneo** está selecionada.
16. Na **instantâneo de origem** pendente, selecione o instantâneo que pretende utilizar.
17. Faça os outros ajustes conforme necessário e, em seguida, selecione **criar** para criar o disco.

## <a name="create-a-vm-from-a-disk"></a>Criar uma VM a partir de um disco

Depois de ter o disco gerido VHD que pretende utilizar, pode criar a VM no portal do:

1. Partir do [portal do Azure](https://portal.azure.com), no menu da esquerda, selecione **todos os serviços**.
2. Na **todos os serviços** caixa de pesquisa, introduza **discos** e, em seguida, selecione **discos** para apresentar a lista de discos disponíveis.
3. Selecione o disco que pretende utilizar. O **disco** a página abre esse disco.
4. Na **descrição geral** página, certifique-se de que **estado do disco** está listado como **anexado**. Caso contrário, poderá ter de desanexar o disco da VM ou elimine a VM para libertar o disco.
4. No menu na parte superior da página, selecione **Create VM**.
5. Sobre o **Noções básicas** página para a nova VM, introduza um **nome da Máquina Virtual** e selecione um existente **grupo de recursos** ou criar um novo.
6. Para **tamanho**, selecione **alterar tamanho** para o acesso a **tamanho** página.
7. Selecione uma linha de tamanho VM e, em seguida, escolha **selecione**.
8. Sobre o **Networking** página, pode optar por permitir que o portal criar todos os novos recursos ou pode selecionar um existente **rede Virtual** e **grupo de segurança de rede**. O portal sempre cria uma nova interface de rede e o endereço IP público para a nova VM. 
9. Sobre o **gestão** página, efetue as alterações para as opções de monitorização.
10. Sobre o **convidado config** página, adicione quaisquer extensões conforme necessário.
11. Quando tiver terminado, selecione **rever + criar**. 
12. Se a configuração da VM passar na validação, selecione **criar** para iniciar a implementação.

## <a name="next-steps"></a>Passos Seguintes

Também pode utilizar o PowerShell para [carregar um VHD para o Azure e criar uma VM especializada](create-vm-specialized.md).


