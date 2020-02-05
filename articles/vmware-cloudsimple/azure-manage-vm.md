---
title: Azure VMware Solutions (AVS) - Gerir VMs de nuvem privada AVS em Azure
description: Descreve como gerir VMs de nuvem privada AVS no portal Azure, incluindo adicionar discos, alterar a capacidade vM e adicionar interfaces de rede
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0cce1dc7ff3935a3174d4e96b553a5485950df73
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015002"
---
# <a name="manage-your-avs-private-cloud-virtual-machines-in-azure"></a>Gerencie as suas máquinas virtuais AVS Private Cloud em Azure

Para gerir as máquinas virtuais que criou para a [sua Nuvem Privada AVS,](azure-create-vm.md)assine para o [portal Azure.](https://portal.azure.com) Procure e selecione o virtual (procure em **Todos os Serviços** ou **Máquinas Virtuais** no menu lateral).

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
| Operações de Discos | Taxa média de operações em disco (operações/segundo) ao longo do intervalo selecionado. |

## <a name="manage-vm-disks"></a>Gerir discos de VM

Para adicionar um disco VM, abra a página **De Discos** para o VM selecionado. Para adicionar um disco, clique em **Adicionar disco**. Configure cada uma das seguintes definições, entrando ou selecionando uma opção inline. Clique em **Guardar**.

   | Item | Descrição |
   | ------------ | ------------- |
   | Nome | Insira um nome para identificar o disco.  |
   | Tamanho | Selecione um dos tamanhos disponíveis.  |
   | Controlador SCSI | Selecione um controlador SCSI. Os controladores disponíveis variam para os diferentes sistemas operativos suportados.  |
   | Modo | Determina como o disco participa em instantâneos. Escolha uma destas opções: <br> – Persistente independente: todos os dados gravados no disco são gravados permanentemente.<br> - Independente, não persistente: As alterações escritas no disco são descartadas quando se desliga ou reinstala a máquina virtual. Este modo permite-lhe reiniciar sempre o VM no mesmo estado. Para obter mais informações, consulte a [documentação do VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html). |

Para eliminar um disco, selecione-o e clique em **Eliminar**.

## <a name="change-the-capacity-of-the-vm"></a>Alterar a capacidade do VM

Para alterar a capacidade do VM, abra a página **Tamanho** para o VM selecionado. Especifique qualquer um dos seguintes e clique em **Guardar**.

| Item | Descrição |
| ------------ | ------------- |
| Número de núcleos | Número de núcleos atribuídos ao VM.  |
| Virtualização de hardware | Selecione a caixa de verificação para expor a virtualização do hardware ao osso convidado. Ver o artigo VMware [Exponha a Virtualização Assistida de Hardware vMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html). |
| Tamanho da Memória | Selecione a quantidade de memória a atribuir ao VM.  

## <a name="manage-network-interfaces"></a>Gerir interfaces de rede

Para adicionar uma interface, clique em **Adicionar interface de rede**. Configure cada uma das seguintes definições inserindo ou selecionada uma opção inline. Clique em **Guardar**.

   | Controlo | Descrição |
   | ------------ | ------------- |
   | Nome | Insira um nome para identificar a interface.  |
   | Rede | Selecione na lista de redes configuradas na sua Nuvem Privada AVS vSphere.  |
   | Adaptador | Selecione um adaptador vSphere na lista de tipos disponíveis configurados para a VM. Para obter mais informações, consulte o artigo da base de dados de conhecimento do VMware [escolhendo um adaptador de rede para sua máquina virtual](https://kb.vmware.com/s/article/1001805). |
   | Ligar na inicialização | Escolha se deseja habilitar o hardware NIC quando a VM for inicializada. O padrão é **habilitar**. |

Para eliminar uma interface de rede, selecione-a e clique em **Eliminar**.
