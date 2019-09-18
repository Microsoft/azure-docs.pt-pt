---
title: Solução do Azure VMware por CloudSimple-gerenciar VMs de nuvem privada no Azure
description: Descreve como gerenciar VMs de nuvem privada do CloudSimple no portal do Azure, incluindo adicionar discos, alterar a capacidade da VM e adicionar interfaces de rede
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 44a0b8fe56477620c0ac47d5c5de8830dac46214
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71037475"
---
# <a name="manage-your-cloudsimple-private-cloud-virtual-machines-in-azure"></a>Gerenciar suas máquinas virtuais de nuvem privada do CloudSimple no Azure

Para gerenciar as máquinas virtuais que você [criou para sua nuvem privada do CloudSimple](azure-create-vm.md), assine o [portal do Azure](https://portal.azure.com). Pesquise e selecione o virtual (Pesquisar em **todos os serviços** ou **máquinas virtuais** no menu lateral).

## <a name="control-virtual-machine-operation"></a>Operação de controle de máquina virtual

Os seguintes controles estão disponíveis na página **visão geral** da máquina virtual selecionada.

| Controlar | Descrição |
| ------------ | ------------- |
| Ligar | Conecte-se à VM especificada.  |
| Start | Inicie a VM especificada.  |
| Reiniciar | Desligue e, em seguida, ligue a VM especificada.  |
| Parar | Desligue a VM específica.  |
| Captura | Capture uma imagem da VM especificada para que ela possa ser usada como uma imagem para criar outras VMs. Consulte [criar uma imagem gerenciada de uma VM generalizada no Azure](../virtual-machines/windows/classic/capture-image.md).   |
| Mover | Mover para a VM especificada.  |
| Eliminar | Remova a VM especificada.  |
| Atualizar | Atualize os dados na exibição.  |

### <a name="view-performance-information"></a>Exibir informações de desempenho

Os gráficos na área inferior da página **visão geral** apresentam dados de desempenho para o intervalo selecionado (última hora para os últimos 30 dias; o padrão é a última hora). Dentro de cada gráfico, você pode exibir os valores numéricos para qualquer hora dentro do intervalo movendo o cursor para frente e para trás sobre o gráfico.

Os gráficos a seguir são exibidos.

| Item | Descrição |
| ------------ | ------------- |
| CPU (média) | Utilização média da CPU em porcentagem sobre o intervalo selecionado.   |
| Rede | Tráfego de entrada e saída da rede (MB) no intervalo selecionado.  |
| Bytes de disco | Total de dados lidos do disco e gravados no disco (MB) no intervalo selecionado.  |
| Operações de Discos | Taxa média de operações de disco (operações/segundo) no intervalo selecionado. |

## <a name="manage-vm-disks"></a>Gerir discos de VM

Para adicionar um disco de VM, abra a página **discos** da VM selecionada. Para adicionar um disco, clique em **adicionar disco**. Defina cada uma das configurações a seguir inserindo ou selecionando uma opção embutida. Clique em **Guardar**.

   | Item | Descrição |
   | ------------ | ------------- |
   | Name | Insira um nome para identificar o disco.  |
   | Size | Selecione um dos tamanhos disponíveis.  |
   | Controlador SCSI | Selecione um controlador SCSI. Os controladores disponíveis variam para os diferentes sistemas operacionais com suporte.  |
   | Modo | Determina como o disco participa em instantâneos. Escolha uma destas opções: <br> – Persistente independente: Todos os dados gravados no disco são gravados permanentemente.<br> -Independente, não persistente: As alterações gravadas no disco são descartadas quando você desliga ou redefine a máquina virtual.  Esse modo permite que você sempre reinicie a VM no mesmo estado. Para obter mais informações, consulte a [documentação do VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html). |

Para excluir um disco, selecione-o e clique em **excluir**.

## <a name="change-the-capacity-of-the-vm"></a>Alterar a capacidade da VM

Para alterar a capacidade da VM, abra a página **tamanho** da VM selecionada. Especifique qualquer um dos itens a seguir e clique em **salvar**.

| Item | Descrição |
| ------------ | ------------- |
| Número de núcleos | Número de núcleos atribuídos à VM.  |
| Virtualização de hardware | Marque a caixa de seleção para expor a virtualização de hardware para o SO convidado. Consulte o artigo do VMware [expor virtualização assistida por hardware VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html). |
| Tamanho da Memória | Selecione a quantidade de memória a ser alocada à VM.  

## <a name="manage-network-interfaces"></a>Gerenciar interfaces de rede

Para adicionar uma interface, clique em **Adicionar interface de rede**. Defina cada uma das configurações a seguir inserindo ou selecionou uma opção embutida. Clique em **Guardar**.

   | Controlar | Descrição |
   | ------------ | ------------- |
   | Name | Insira um nome para identificar a interface.  |
   | Rede | Selecione na lista de redes configuradas em sua nuvem privada vSphere.  |
   | Placas | Selecione um adaptador vSphere na lista de tipos disponíveis configurados para a VM. Para obter mais informações, consulte o artigo da base de dados de conhecimento do VMware [escolhendo um adaptador de rede para sua máquina virtual](https://kb.vmware.com/s/article/1001805). |
   | Ligar na inicialização | Escolha se deseja habilitar o hardware NIC quando a VM for inicializada. O padrão é **habilitar**. |

Para excluir uma interface de rede, selecione-a e clique em **excluir**.
