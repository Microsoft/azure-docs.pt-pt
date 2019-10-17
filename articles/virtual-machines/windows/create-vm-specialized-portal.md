---
title: Criar uma VM do Windows de um VHD especializado no portal do Azure | Microsoft Docs
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
ms.topic: article
ms.date: 01/18/2019
ms.author: cynthn
ms.openlocfilehash: d79cefa9adec403ebbb5105351d2909eac9d02a4
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390491"
---
# <a name="create-a-vm-from-a-vhd-by-using-the-azure-portal"></a>Criar uma VM de um VHD usando o portal do Azure

Há várias maneiras de criar uma VM (máquina virtual) no Azure: 

- Se você já tiver um VHD (disco rígido virtual) para usar ou se quiser copiar o VHD de uma VM existente para usar, poderá criar uma nova VM *anexando* o VHD à nova VM como um disco do sistema operacional. 

- Você pode criar uma nova VM do VHD de uma VM que foi excluída. Por exemplo, se você tiver uma VM do Azure que não está funcionando corretamente, você poderá excluir a VM e usar seu VHD para criar uma nova VM. Você pode reutilizar o mesmo VHD ou criar uma cópia do VHD criando um instantâneo e, em seguida, criando um novo disco gerenciado a partir do instantâneo. Embora a criação de um instantâneo leve mais algumas etapas, ela preserva o VHD original e fornece um fallback.

- Pegue uma VM clássica e use o VHD para criar uma nova VM que usa o modelo de implantação do Resource Manager e Managed disks. Para obter os melhores resultados, **pare** a VM clássica no portal do Azure antes de criar o instantâneo.
 
- Você pode criar uma VM do Azure de um VHD local carregando o VHD local e anexando-o a uma nova VM. Você usa o PowerShell ou outra ferramenta para carregar o VHD em uma conta de armazenamento e, em seguida, cria um disco gerenciado a partir do VHD. Para obter mais informações, consulte [carregar um VHD especializado](create-vm-specialized.md#option-2-upload-a-specialized-vhd). 

Não use um disco especializado se desejar criar várias VMs. Em vez disso, para implantações maiores, [crie uma imagem](capture-image-resource.md) e, em seguida, [Use essa imagem para criar várias VMs](create-vm-generalized-managed.md).

Recomendamos que você limite o número de implantações simultâneas a 20 VMs de um único instantâneo ou VHD. 

## <a name="copy-a-disk"></a>Copiar um disco

Crie um instantâneo e, em seguida, crie um disco a partir do instantâneo. Essa estratégia permite que você mantenha o VHD original como um fallback:

1. No [portal do Azure](https://portal.azure.com), no menu à esquerda, selecione **todos os serviços**.
2. Na caixa de pesquisa **todos os serviços** , insira **discos** e, em seguida, selecione **discos** para exibir a lista de discos disponíveis.
3. Selecione o disco que você deseja usar. A página **disco** para esse disco é exibida.
4. No menu na parte superior, selecione **criar instantâneo**. 
5. Insira um **nome** para o instantâneo.
6. Escolha um **grupo de recursos** para o instantâneo. Você pode usar um grupo de recursos existente ou criar um novo.
7. Para **tipo de conta**, escolha o armazenamento **padrão (HDD)** ou **Premium (SSD)** .
8. Quando terminar, selecione **criar** para criar o instantâneo.
9. Depois que o instantâneo tiver sido criado, selecione **criar um recurso** no menu à esquerda.
10. Na caixa de pesquisa, insira **disco gerenciado** e, em seguida, selecione **Managed disks** na lista.
11. Na página **Managed disks** , selecione **criar**.
12. Insira um **nome** para o disco.
13. Escolha um **grupo de recursos** para o disco. Você pode usar um grupo de recursos existente ou criar um novo. Essa seleção também será usada como o grupo de recursos em que você cria a VM do disco.
14. Para **tipo de conta**, escolha o armazenamento **padrão (HDD)** ou **Premium (SSD)** .
15. Em **tipo de origem**, verifique se **instantâneo** está selecionado.
16. Na lista suspensa **instantâneo de origem** , selecione o instantâneo que você deseja usar.
17. Faça quaisquer outros ajustes conforme necessário e, em seguida, selecione **criar** para criar o disco.

## <a name="create-a-vm-from-a-disk"></a>Criar uma VM de um disco

Depois de ter o VHD de disco gerenciado que você deseja usar, você pode criar a VM no Portal:

1. No [portal do Azure](https://portal.azure.com), no menu à esquerda, selecione **todos os serviços**.
2. Na caixa de pesquisa **todos os serviços** , insira **discos** e, em seguida, selecione **discos** para exibir a lista de discos disponíveis.
3. Selecione o disco que você deseja usar. A página **disco** desse disco é aberta.
4. Na página **visão geral** , verifique se o **estado do disco** está listado como **desanexado**. Se não for, talvez seja necessário desanexar o disco da VM ou excluir a VM para liberar o disco.
4. No menu na parte superior da página, selecione **criar VM**.
5. Na página **noções básicas** da nova VM, insira um **nome de máquina virtual** e selecione um grupo de **recursos** existente ou crie um novo.
6. Para **tamanho**, selecione **alterar tamanho** para acessar a página **tamanho** .
7. Selecione uma linha de tamanho de VM e escolha **selecionar**.
8. Na página **rede** , você pode permitir que o portal crie todos os novos recursos ou pode selecionar uma **rede virtual** existente e um **grupo de segurança de rede**. O portal sempre cria uma nova interface de rede e um endereço IP público para a nova VM. 
9. Na página **Gerenciamento** , faça as alterações nas opções de monitoramento.
10. Na página **configuração do convidado** , adicione todas as extensões, conforme necessário.
11. Quando terminar, selecione **revisar + criar**. 
12. Se a configuração da VM passar na validação, selecione **criar** para iniciar a implantação.


## <a name="next-steps"></a>Passos seguintes

Você também pode usar o PowerShell para [carregar um VHD no Azure e criar uma VM especializada](create-vm-specialized.md).


