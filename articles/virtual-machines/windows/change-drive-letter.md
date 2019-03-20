---
title: 'Tornar a unidade d: de uma VM de um disco de dados | Documentos da Microsoft'
description: 'Descreve como alterar as letras de unidade para uma VM do Windows para que possa utilizar a unidade d: como uma unidade de dados.'
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 0867a931-0055-4e31-8403-9b38a3eeb904
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: cfd46d5e9750a81d89ed6d3a79bcc9bffdc3d0dd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58007290"
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Utilizar a unidade d: como uma unidade de dados numa VM do Windows
Se a sua aplicação precisa para utilizar a unidade D para armazenar dados, siga estas instruções para utilizar uma letra de unidade diferente para o disco temporário. Nunca utilize o disco temporário para armazenar dados que precisa de manter.

Se o redimensionamento ou **Stop (Desalocação)** uma máquina virtual, isto pode acionar a colocação da máquina virtual para um hipervisor de novo. Um evento de manutenção planeada ou também pode acionar Esta colocação. Neste cenário, o disco temporário será reatribuído à primeira letra de unidade disponível. Se tiver um aplicativo que requer especificamente a unidade d:, tem de seguir estes passos para temporariamente mover o Pagefile. sys, anexar um disco de dados novo e atribua-a letra D e, em seguida, regresse a Pagefile. sys para a unidade temporária. Quando tiver terminado, Azure será não voltar a d: se a VM passa para um hipervisor de diferente.

Para obter mais informações sobre como o Azure utiliza o disco temporário, consulte [Noções básicas sobre a unidade temporária em máquinas virtuais do Microsoft Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="attach-the-data-disk"></a>Anexar o disco de dados
Em primeiro lugar, terá de anexar o disco de dados para a máquina virtual. Para fazer isso com o portal, consulte [como anexar um disco de dados geridos no portal do Azure](attach-managed-disk-portal.md).

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Transfira temporariamente Pagefile. sys para a unidade C
1. Ligue à máquina virtual. 
2. Com o botão direito a **começar** menu e selecione **sistema**.
3. No menu da esquerda, selecione **configurações avançadas do sistema**.
4. Na **desempenho** secção, selecione **definições**.
5. Selecione o **avançadas** separador.
6. Na **Memória Virtual** secção, selecione **alteração**.
7. Selecione o **C** unidade e, em seguida, clique em **sistema gerido tamanho** e, em seguida, clique em **definir**.
8. Selecione o **1!d** unidade e, em seguida, clique em **nenhum ficheiro de paginação** e, em seguida, clique em **definir**.
9. Clique em aplicar. Receberá um aviso de que o computador tem de ser reiniciado para que as alterações tenham efeito.
10. Reinicie a máquina virtual.

## <a name="change-the-drive-letters"></a>Alterar as letras de unidade
1. Assim que a VM é reiniciada, inicie novamente sessão na VM.
2. Clique nas **começar** menu e escreva **Diskmgmt. msc** e prima Enter. Gestão de discos será iniciado.
3. Com o botão direito no **1!d**, a unidade de armazenamento temporário e selecione **Alterar letra de unidade e caminhos**.
4. Em letra de unidade, selecione uma nova unidade, como **T** e, em seguida, clique em **OK**. 
5. Com o botão direito no disco de dados e selecione **Alterar letra de unidade e caminhos**.
6. Em letra de unidade, selecione a unidade **1!d** e, em seguida, clique em **OK**. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Regresse Pagefile. sys para a unidade de armazenamento temporário
1. Com o botão direito a **começar** menu e selecione **sistema**
2. No menu da esquerda, selecione **configurações avançadas do sistema**.
3. Na **desempenho** secção, selecione **definições**.
4. Selecione o **avançadas** separador.
5. Na **Memória Virtual** secção, selecione **alteração**.
6. Selecione a unidade do SO **C** e clique em **nenhum ficheiro de paginação** e, em seguida, clique em **definir**.
7. Selecione a unidade de armazenamento temporário **T** e, em seguida, clique em **sistema gerido tamanho** e, em seguida, clique em **definir**.
8. Clique em **Aplicar**. Receberá um aviso de que o computador tem de ser reiniciado para que as alterações tenham efeito.
9. Reinicie a máquina virtual.

## <a name="next-steps"></a>Passos Seguintes
* Pode aumentar a memória disponível para a máquina virtual através do [anexar um disco de dados adicionais](attach-managed-disk-portal.md).

