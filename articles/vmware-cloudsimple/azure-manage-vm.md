---
title: Solução Azure VMware by CloudSimple - Gerencie VMs de nuvem privada em Azure
description: Describes how to manage CloudSimple Private Cloud VMs in the Azure portal, including adding disks, changing VM capacity, and adding network interfaces
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 44a0b8fe56477620c0ac47d5c5de8830dac46214
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77015002"
---
# <a name="manage-your-cloudsimple-private-cloud-virtual-machines-in-azure"></a>Gerencie as suas máquinas virtuais CloudSimple Private Cloud em Azure

Para gerir as máquinas virtuais que criou para a [sua CloudSimple Private Cloud,](azure-create-vm.md)assine para o [portal Azure](https://portal.azure.com). Procure e selecione o virtual (procure em **Todos os Serviços** ou **Máquinas Virtuais** no menu lateral).

## <a name="control-virtual-machine-operation"></a>Controlar o funcionamento da máquina virtual

Os seguintes controlos estão disponíveis na página **'Overview'** para a sua máquina virtual selecionada.

| Controlo | Descrição |
| ------------ | ------------- |
| Ligar | Ligue-se ao VM especificado.  |
| Iniciar | Inicie o VM especificado.  |
| Reiniciar | Desligue e, em seguida, desligue o VM especificado.  |
| Parar | Desligue o VM específico.  |
| Captura | Capture uma imagem do VM especificado para que possa ser usado como uma imagem para criar outros VMs. Ver [Criar uma imagem gerida de um VM generalizado em Azure](../virtual-machines/windows/classic/capture-image.md).   |
| Mover | Mova-se para o VM especificado.  |
| Eliminar | Retire o VM especificado.  |
| Atualizar | Refresque os dados no visor.  |

### <a name="view-performance-information"></a>Ver informações sobre desempenho

Os gráficos na área inferior da página **de visão geral** apresentam dados de desempenho para o intervalo selecionado (última hora a 30 dias; o padrão é de última hora). Dentro de cada gráfico, pode apresentar os valores numéricos durante qualquer momento dentro do intervalo, movendo o cursor para trás e para a frente sobre a tabela.

São apresentados os seguintes gráficos.

| Item | Descrição |
| ------------ | ------------- |
| CPU (média) | Utilização média do CPU em percentagem sobre o intervalo selecionado.   |
| Rede | Tráfego dentro e fora da rede (MB) durante o intervalo selecionado.  |
| Bytes de disco | Dados totais lidos a partir do disco e escritos ao disco (MB) sobre o intervalo selecionado.  |
| Operações de Disco | Taxa média de operações em disco (operações/segundo) ao longo do intervalo selecionado. |

## <a name="manage-vm-disks"></a>Gerir discos VM

Para adicionar um disco VM, abra a página **De Discos** para o VM selecionado. Para adicionar um disco, clique em **Adicionar disco**. Configure cada uma das seguintes definições, entrando ou selecionando uma opção inline. Clique em **Guardar**.

   | Item | Descrição |
   | ------------ | ------------- |
   | Nome | Introduza um nome para identificar o disco.  |
   | Tamanho | Selecione um dos tamanhos disponíveis.  |
   | Controlador SCSI | Selecione um controlador SCSI. Os controladores disponíveis variam para os diferentes sistemas operativos suportados.  |
   | Modo | Determina como o disco participa em instantâneos. Escolha uma destas opções: <br> - Persistente independente: Todos os dados escritos ao disco são escritos permanentemente.<br> - Independente, não persistente: As alterações escritas no disco são descartadas quando se desliga ou reinstala a máquina virtual.  Este modo permite-lhe reiniciar sempre o VM no mesmo estado. Para mais informações, consulte a [documentação vMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html). |

Para eliminar um disco, selecione-o e clique em **Eliminar**.

## <a name="change-the-capacity-of-the-vm"></a>Alterar a capacidade do VM

Para alterar a capacidade do VM, abra a página **Tamanho** para o VM selecionado. Especifique qualquer um dos seguintes e clique em **Guardar**.

| Item | Descrição |
| ------------ | ------------- |
| Número de núcleos | Número de núcleos atribuídos ao VM.  |
| Virtualização de hardware | Selecione a caixa de verificação para expor a virtualização do hardware ao osso convidado. Ver o artigo VMware [Exponha a Virtualização Assistida de Hardware vMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html). |
| Tamanho da memória | Selecione a quantidade de memória a atribuir ao VM.  

## <a name="manage-network-interfaces"></a>Gerir interfaces de rede

Para adicionar uma interface, clique em **Adicionar interface de rede**. Configure cada uma das seguintes definições inserindo ou selecionada uma opção inline. Clique em **Guardar**.

   | Controlo | Descrição |
   | ------------ | ------------- |
   | Nome | Introduza um nome para identificar a interface.  |
   | Rede | Selecione na lista de redes configuradas na sua nuvem privada vSphere.  |
   | Adaptador | Selecione um adaptador vSphere da lista de tipos disponíveis configurados para o VM. Para mais informações, consulte o artigo base de conhecimento VMware [Escolhendo um adaptador](https://kb.vmware.com/s/article/1001805)de rede para a sua máquina virtual . |
   | Poder ligado no Boot | Escolha se ativa o hardware NIC quando o VM é iniciado. O predefinido é **Ativar**. |

Para eliminar uma interface de rede, selecione-a e clique em **Eliminar**.
