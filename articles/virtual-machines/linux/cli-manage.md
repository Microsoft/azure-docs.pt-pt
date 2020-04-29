---
title: Comandos comuns da CLI Azure
description: Aprenda alguns dos comandos comuns do Azure CLI para que você tenha começado a gerir os seus VMs no modo Azure Resource Manager
author: RicksterCDN
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 05/12/2017
ms.author: rclaus
ms.openlocfilehash: 253f2ab1b192d22f43e4082766adf4ec4f86fe71
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78969257"
---
# <a name="common-azure-cli-commands-for-managing-azure-resources"></a>Comandos comuns da CLI do Azure para gerir recursos do Azure

O Azure CLI permite-lhe criar e gerir os seus recursos Azure em macOS, Linux e Windows. Este artigo detalha alguns dos comandos mais comuns para criar e gerir máquinas virtuais (VMs).

Este artigo requer a versão Azure CLI 2.0.4 ou posterior. Executar `az --version` para localizar a versão. Se precisar de fazer o upgrade, consulte [Instalar o Azure CLI](/cli/azure/install-azure-cli). Também pode utilizar [a Cloud Shell](/azure/cloud-shell/quickstart) a partir do seu navegador.

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Comandos básicos do Azure Resource Manager na CLI do Azure
Para obter uma ajuda mais detalhada com interruptores e opções específicos `az <command> <subcommand> --help`da linha de comando, pode utilizar a ajuda e opções de comando on-line digitando .

### <a name="create-vms"></a>Criar VMs
| Tarefa | Comandos da CLI Azure |
| --- | --- |
| Criar um grupo de recursos | `az group create --name myResourceGroup --location eastus` |
| Criar uma VM do Linux | `az vm create --resource-group myResourceGroup --name myVM --image ubuntults` |
| Criar uma VM do Windows | `az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter` |

### <a name="manage-vm-state"></a>Gerir o estado VM
| Tarefa | Comandos da CLI Azure |
| --- | --- |
| Iniciar uma VM | `az vm start --resource-group myResourceGroup --name myVM` |
| Parar uma VM | `az vm stop --resource-group myResourceGroup --name myVM` |
| Desalocar uma VM | `az vm deallocate --resource-group myResourceGroup --name myVM` |
| Reiniciar uma VM | `az vm restart --resource-group myResourceGroup --name myVM` |
| Reimplementar uma VM | `az vm redeploy --resource-group myResourceGroup --name myVM` |
| Eliminar uma VM | `az vm delete --resource-group myResourceGroup --name myVM` |

### <a name="get-vm-info"></a>Obtenha informações vM
| Tarefa | Comandos da CLI Azure |
| --- | --- |
| Listar VMs | `az vm list` |
| Obter informações sobre uma VM | `az vm show --resource-group myResourceGroup --name myVM` |
| Obter a utilização de recursos de VM | `az vm list-usage --location eastus` |
| Obter todos os tamanhos de VM disponíveis | `az vm list-sizes --location eastus` |

## <a name="disks-and-images"></a>Discos e imagens
| Tarefa | Comandos da CLI Azure |
| --- | --- |
| Adicionar um disco de dados a uma VM | `az vm disk attach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk --size-gb 128 --new` |
| Remover um disco de dados de uma VM | `az vm disk detach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk` |
| Redimensionar um disco | `az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 256` |
| Tirar um instantâneo de um disco | `az snapshot create --resource-group myResourceGroup --name mySnapshot --source myDataDisk` |
| Criar imagem de um VM | `az image create --resource-group myResourceGroup --source myVM --name myImage` |
| Criar VM a partir da imagem | `az vm create --resource-group myResourceGroup --name myNewVM --image myImage` |


## <a name="next-steps"></a>Passos seguintes
Para exemplos adicionais dos comandos CLI, consulte os [VMs Create e Manage Linux com o tutorial Azure CLI.](tutorial-manage-vm.md)



