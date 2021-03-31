---
title: Comandos comuns da CLI Azure
description: Aprenda alguns dos comandos comuns do Azure CLI para começar a gerir os seus VMs no modo Azure Resource Manager
author: RicksterCDN
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 05/12/2017
ms.author: rclaus
ms.openlocfilehash: 2084d79ecbbc53ef9e3c75bae0664eae7de0eccb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559635"
---
# <a name="common-azure-cli-commands-for-managing-azure-resources"></a>Comandos comuns da CLI do Azure para gerir recursos do Azure

O Azure CLI permite-lhe criar e gerir os seus recursos Azure em macOS, Linux e Windows. Este artigo detalha alguns dos comandos mais comuns para criar e gerir máquinas virtuais (VMs).

Este artigo requer a versão Azure CLI 2.0.4 ou posterior. Executar `az --version` para localizar a versão. Se precisar de atualizar, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli). Também pode utilizar [cloud Shell](../../cloud-shell/quickstart.md) a partir do seu navegador.

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Comandos básicos do Azure Resource Manager na CLI do Azure
Para uma ajuda mais detalhada com comutadores e opções específicos da linha de comando, pode utilizar a ajuda e opções de comando on-line digitando `az <command> <subcommand> --help` .

### <a name="create-vms"></a>Criar VMs
| Tarefa | Comandos da CLI do Azure |
| --- | --- |
| Criar um grupo de recursos | `az group create --name myResourceGroup --location eastus` |
| Criar uma VM do Linux | `az vm create --resource-group myResourceGroup --name myVM --image ubuntults` |
| Criar uma VM do Windows | `az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter` |

### <a name="manage-vm-state"></a>Gerir o estado VM
| Tarefa | Comandos da CLI do Azure |
| --- | --- |
| Iniciar uma VM | `az vm start --resource-group myResourceGroup --name myVM` |
| Parar uma VM | `az vm stop --resource-group myResourceGroup --name myVM` |
| Desalocar uma VM | `az vm deallocate --resource-group myResourceGroup --name myVM` |
| Reiniciar uma VM | `az vm restart --resource-group myResourceGroup --name myVM` |
| Reimplementar uma VM | `az vm redeploy --resource-group myResourceGroup --name myVM` |
| Eliminar uma VM | `az vm delete --resource-group myResourceGroup --name myVM` |

### <a name="get-vm-info"></a>Obtenha informações VM
| Tarefa | Comandos da CLI do Azure |
| --- | --- |
| Listar VMs | `az vm list` |
| Obter informações sobre uma VM | `az vm show --resource-group myResourceGroup --name myVM` |
| Obter a utilização de recursos de VM | `az vm list-usage --location eastus` |
| Obter todos os tamanhos de VM disponíveis | `az vm list-sizes --location eastus` |

## <a name="disks-and-images"></a>Discos e imagens
| Tarefa | Comandos da CLI do Azure |
| --- | --- |
| Adicionar um disco de dados a uma VM | `az vm disk attach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk --size-gb 128 --new` |
| Remover um disco de dados de uma VM | `az vm disk detach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk` |
| Redimensionar um disco | `az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 256` |
| Tirar um instantâneo de um disco | `az snapshot create --resource-group myResourceGroup --name mySnapshot --source myDataDisk` |
| Criar imagem de um VM | `az image create --resource-group myResourceGroup --source myVM --name myImage` |
| Criar VM a partir da imagem | `az vm create --resource-group myResourceGroup --name myNewVM --image myImage` |


## <a name="next-steps"></a>Passos seguintes
Para exemplos adicionais dos comandos CLI, consulte os [VMs Create e Manage Linux com o tutorial Azure CLI.](tutorial-manage-vm.md)
