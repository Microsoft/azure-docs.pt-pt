---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: a4c9ec133b3686a92cec7e7c8d4552c1302e3074
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58125065"
---
Antes de poder utilizar a CLI do Azure com comandos e modelos do Resource Manager para implementar recursos e cargas de trabalho do Azure, precisa de uma conta no Azure. Se não tiver uma conta, pode obter uma [avaliação gratuita do Azure, aqui](https://azure.microsoft.com/pricing/free-trial/).

Se ainda não tiver instalado a CLI do Azure e não tiver ligado à sua subscrição, veja o artigo [Instalar a CLI do Azure](../articles/cli-install-nodejs.md), defina o modo para `arm` com `azure config mode arm` e ligue ao Azure com o comando `azure login`.

## <a name="cli-versions-to-complete-the-task"></a>Versões CLI para concluir a tarefa
Pode concluir a tarefa utilizando uma das seguintes versões CLI:

- CLI clássica do Azure – CLI para as clássica e de recursos gestão modelos de implementação (Este artigo)
- [CLI do Azure](../articles/virtual-machines/linux/cli-manage.md) -CLI de próxima geração para o modelo de implementação de gestão de recursos

## <a name="basic-azure-resource-manager-commands-in-azure-classic-cli"></a>Comandos básicos do Azure Resource Manager na CLI clássica do Azure

Este artigo abrange os comandos básicos que pretende utilizar com a CLI clássica do Azure para gerir e interagir com os seus recursos (principalmente, VMs) na sua subscrição do Azure.  Para obter ajuda mais detalhada sobre os parâmetros e opções específicos da linha de comandos, pode utilizar a ajuda e opções de comando online ao escrever `azure <command> <subcommand> --help` ou `azure help <command> <subcommand>`.

> [!NOTE]
> Estes exemplos não incluem operações baseadas em modelos que, geralmente, são recomendadas para implementações de VM no Resource Manager. Para obter mais informações, veja os artigos [Utilizar a CLI do Azure com o Azure Resource Manager](../articles/xplat-cli-azure-resource-manager.md) e [Implementar e gerir máquinas virtuais com os modelos do Azure Resource Manager e a CLI do Azure](../articles/virtual-machines/linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

| Tarefa | Resource Manager |
| --- | --- |
| Criar a VM mais básica |`azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`<br/><br/>(Obter `image-urn` do comando `azure vm image list`. Veja [este artigo](../articles/virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter exemplos). |
| Criar uma VM do Linux |`azure  vm create [options] <resource-group> <name> <location> -y "Linux"` |
| Criar uma VM do Windows |`azure  vm create [options] <resource-group> <name> <location> -y "Windows"` |
| Listar VMs |`azure  vm list [options]` |
| Obter informações sobre uma VM |`azure  vm show [options] <resource_group> <name>` |
| Iniciar uma VM |`azure vm start [options] <resource_group> <name>` |
| Parar uma VM |`azure vm stop [options] <resource_group> <name>` |
| Desalocar uma VM |`azure vm deallocate [options] <resource-group> <name>` |
| Reiniciar uma VM |`azure vm restart [options] <resource_group> <name>` |
| Eliminar uma VM |`azure vm delete [options] <resource_group> <name>` |
| Capturar uma VM |`azure vm capture [options] <resource_group> <name>` |
| Criar uma VM a partir de uma imagem de utilizador |`azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>` |
| Criar uma VM a partir de um disco especializado |`azure  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>` |
| Adicionar um disco de dados a uma VM |`azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]` |
| Remover um disco de dados de uma VM |`azure  vm disk detach [options] <resource-group> <vm-name> <lun>` |
| Adicionar uma extensão genérica a uma VM |`azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>` |
| Adicionar a extensão de Acesso da VM a uma VM |`azure vm reset-access [options] <resource-group> <name>` |
| Adicionar a extensão do Docker a uma VM |`azure  vm docker create [options] <resource-group> <name> <location> <os-type>` |
| Remover uma extensão de VM |`azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>` |
| Obter a utilização de recursos de VM |`azure vm list-usage [options] <location>` |
| Obter todos os tamanhos de VM disponíveis |`azure vm sizes [options]` |

## <a name="next-steps"></a>Passos Seguintes
* Para obter exemplos adicionais dos comandos da CLI, para além dos da gestão básica de VMs, veja o artigo [Utilizar a CLI do Azure com o Azure Resource Manager](../articles/virtual-machines/azure-cli-arm-commands.md).
