---
title: 'Tornar a unidade D: de uma VM um disco de dados '
description: 'Descreve como alterar as letras das unidades de uma VM do Windows para que você possa usar a unidade D: como uma unidade de dados.'
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 0867a931-0055-4e31-8403-9b38a3eeb904
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: 491e7b7be084017cc370fe431c3175ac5b2673f9
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033660"
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Usar a unidade D: como uma unidade de dados em uma VM do Windows
Se seu aplicativo precisar usar a unidade D para armazenar dados, siga estas instruções para usar uma letra de unidade diferente para o disco temporário. Nunca use o disco temporário para armazenar dados que você precisa manter.

Se você redimensionar ou **parar (desalocar)** uma máquina virtual, isso poderá disparar o posicionamento da máquina virtual em um novo hipervisor. Um evento de manutenção planejado ou não planejado também pode disparar esse posicionamento. Nesse cenário, o disco temporário será reatribuído à primeira letra da unidade disponível. Se você tiver um aplicativo que exige especificamente a unidade D:, será necessário seguir estas etapas para mover temporariamente o arquivo pagefile. sys, anexar um novo disco de dados e atribuir a ele a letra D e, em seguida, mover o pagefile. sys de volta para a unidade temporária. Depois de concluído, o Azure não retirará o D: se a VM for movida para um hipervisor diferente.

Para obter mais informações sobre como o Azure usa o disco temporário, consulte [noções básicas sobre a unidade temporária no máquinas virtuais do Microsoft Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="attach-the-data-disk"></a>Anexar o disco de dados
Primeiro, você precisará anexar o disco de dados à máquina virtual. Para fazer isso usando o portal, consulte [como anexar um disco de dados gerenciado na portal do Azure](attach-managed-disk-portal.md).

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Mover temporariamente o pagefile. sys para a unidade C
1. Ligue à máquina virtual. 
2. Clique com o botão direito do mouse no menu **Iniciar** e selecione **sistema**.
3. No menu à esquerda, selecione **Configurações avançadas do sistema**.
4. Na seção **desempenho** , selecione **configurações**.
5. Selecione a guia **avançado** .
6. Na seção **memória virtual** , selecione **alterar**.
7. Selecione a unidade **C** e clique em **tamanho gerenciado pelo sistema** e, em seguida, clique em **definir**.
8. Selecione a unidade **D** e clique em **nenhum arquivo de paginação** e, em seguida, clique em **definir**.
9. Clique em aplicar. Você receberá um aviso de que o computador precisa ser reiniciado para que as alterações entrem em vigor.
10. Reinicie a máquina virtual.

## <a name="change-the-drive-letters"></a>Alterar as letras da unidade
1. Depois que a VM for reiniciada, faça logon novamente na VM.
2. Clique no menu **Iniciar** e digite **diskmgmt. msc** e pressione Enter. O gerenciamento de disco será iniciado.
3. Clique com o botão direito do mouse em **D**, a unidade de armazenamento temporário e selecione **alterar a letra da unidade e os caminhos**.
4. Em letra da unidade, selecione uma nova unidade, como **T** , e clique em **OK**. 
5. Clique com o botão direito do mouse no disco de dados e selecione **alterar a letra da unidade e os caminhos**.
6. Em letra da unidade, selecione a unidade **D** e clique em **OK**. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Mover o pagefile. sys de volta para a unidade de armazenamento temporário
1. Clique com o botão direito do mouse no menu **Iniciar** e selecione **sistema**
2. No menu à esquerda, selecione **Configurações avançadas do sistema**.
3. Na seção **desempenho** , selecione **configurações**.
4. Selecione a guia **avançado** .
5. Na seção **memória virtual** , selecione **alterar**.
6. Selecione a unidade do sistema operacional **C** e clique em **nenhum arquivo de paginação** e clique em **definir**.
7. Selecione a unidade de armazenamento temporário **T** e clique em **tamanho gerenciado pelo sistema** e, em seguida, clique em **definir**.
8. Clique em **Aplicar**. Você receberá um aviso de que o computador precisa ser reiniciado para que as alterações entrem em vigor.
9. Reinicie a máquina virtual.

## <a name="next-steps"></a>Passos seguintes
* Você pode aumentar o armazenamento disponível para sua máquina virtual [anexando um disco de dados adicional](attach-managed-disk-portal.md).

