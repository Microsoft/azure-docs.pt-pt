---
title: Azure VMware Solution by CloudSimple - Gerir VMs de nuvem privada em Azure
description: Descreve como gerir os CloudSimple Private Cloud VMs no portal Azure, incluindo a adição de discos, alteração da capacidade de VM e a adição de interfaces de rede
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 090b7711ab061b989eae13113fe7048e8dd875ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895194"
---
# <a name="manage-your-cloudsimple-private-cloud-virtual-machines-in-azure"></a>Gerencie as suas máquinas virtuais CloudSimple Cloud em Azure

Para gerir as máquinas virtuais que [criou para a sua CloudSimple Private Cloud,](azure-create-vm.md)assine o portal [Azure](https://portal.azure.com). Procure e selecione o virtual (procure em **Todos os Serviços** ou **Máquinas Virtuais** no menu lateral).

## <a name="control-virtual-machine-operation"></a>Controlar o funcionamento da máquina virtual

Os seguintes controlos estão disponíveis na página **'Vista Geral'** para a sua máquina virtual selecionada.

| Controlar | Description |
| ------------ | ------------- |
| Ligar | Ligue-se ao VM especificado.  |
| Iniciar | Inicie o VM especificado.  |
| Reiniciar | Desligue e, em seguida, ligue o VM especificado.  |
| Parar | Desligue o VM específico.  |
| Recolha | Capture uma imagem do VM especificado para que possa ser usado como uma imagem para criar outros VMs. Ver [Criar uma imagem gerida de um VM generalizado em Azure](../virtual-machines/windows/capture-image-resource.md).   |
| Mover | Mova-se para o VM especificado.  |
| Eliminar | Remova o VM especificado.  |
| Atualizar | Refresque os dados no visor.  |

### <a name="view-performance-information"></a>Ver informações de desempenho

Os gráficos na área inferior da página de **visão geral** apresentam dados de desempenho para o intervalo selecionado (última hora para durar 30 dias; o padrão é a última hora). Dentro de cada gráfico, pode apresentar os valores numéricos durante qualquer momento dentro do intervalo, movendo o cursor para trás e para a frente sobre a tabela.

São apresentados os seguintes gráficos.

| Item | Descrição |
| ------------ | ------------- |
| CPU (média) | Utilização média do CPU em percentagem ao longo do intervalo selecionado.   |
| Rede | Tráfego dentro e fora da rede (MB) durante o intervalo selecionado.  |
| Bytes de disco | Total de dados lidos a partir de disco e escritos para disco (MB) durante o intervalo selecionado.  |
| Operações de Disco | Taxa média de operações de disco (operações/segundo) durante o intervalo selecionado. |

## <a name="manage-vm-disks"></a>Gerir discos VM

Para adicionar um disco VM, abra a página **Discos** para o VM selecionado. Para adicionar um disco, clique **em Adicionar disco**. Configure cada uma das seguintes definições introduzindo ou selecionando uma opção inline. Clique em **Guardar**.

   | Item | Descrição |
   | ------------ | ------------- |
   | Nome | Insira um nome para identificar o disco.  |
   | Tamanho | Selecione um dos tamanhos disponíveis.  |
   | Controlador SCSI | Selecione um controlador SCSI. Os controladores disponíveis variam para os diferentes sistemas operativos suportados.  |
   | Modo | Determina como o disco participa em instantâneos. Escolha uma destas opções: <br> - Persistente independente: Todos os dados escritos no disco são escritos permanentemente.<br> - Independente, não persistente: As alterações escritas no disco são descartadas quando desliga ou repõe a máquina virtual.  Este modo permite-lhe reiniciar sempre o VM no mesmo estado. Para mais informações, consulte a [documentação VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html). |

Para eliminar um disco, selecione-o e clique em **Eliminar**.

## <a name="change-the-capacity-of-the-vm"></a>Alterar a capacidade do VM

Para alterar a capacidade do VM, abra a página **Tamanho** para o VM selecionado. Especifique qualquer um dos seguintes e clique em **Guardar.**

| Item | Descrição |
| ------------ | ------------- |
| Número de núcleos | Número de núcleos atribuídos ao VM.  |
| Virtualização de hardware | Selecione a caixa de verificação para expor a virtualização do hardware ao so convidado. Ver o artigo VMware [Expor Virtualização Assistida de Hardware VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html). |
| Tamanho da memória | Selecione a quantidade de memória a atribuir ao VM.  

## <a name="manage-network-interfaces"></a>Gerir interfaces de rede

Para adicionar uma interface, clique em **Adicionar interface de rede**. Configure cada uma das seguintes definições introduzindo ou selecione uma opção inline. Clique em **Guardar**.

   | Controlar | Descrição |
   | ------------ | ------------- |
   | Nome | Insira um nome para identificar a interface.  |
   | Rede | Selecione a partir da lista de redes configuradas na sua Nuvem Privada vSphere.  |
   | Adaptador | Selecione um adaptador vSphere da lista de tipos disponíveis configurados para o VM. Para obter mais informações, consulte o artigo base de conhecimento VMware [Escolher um adaptador de rede para a sua máquina virtual.](https://kb.vmware.com/s/article/1001805) |
   | Energia ligado no Boot | Escolha se ativa o hardware NIC quando o VM for iniciado. O padrão é **Ativar**. |

Para eliminar uma interface de rede, selecione-a e clique em **Eliminar**.
