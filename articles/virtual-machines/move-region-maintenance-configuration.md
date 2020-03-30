---
title: Mova uma configuração de manutenção para outra região do Azure
description: Saiba como mover uma configuração de manutenção VM para outra região do Azure
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: fe03bead238d3fb7bda3ee685bd5587c3e0dbc58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304462"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>Mova uma configuração de Controlo de Manutenção para outra região

Siga este artigo para mover uma configuração de Controlo de Manutenção para uma região azure diferente. Talvez queira mover uma configuração por uma série de razões. Por exemplo, aproveitar uma nova região, implantar funcionalidades ou serviços disponíveis numa região específica, satisfazer os requisitos de política interna e governação, ou em resposta ao planeamento de capacidades.

O controlo de manutenção, com configurações de manutenção personalizadas, permite controlar a forma como as atualizações da plataforma são aplicadas aos VMs [Windows](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) e [Linux,](https://docs.microsoft.com/azure/virtual-machines/maintenance-control-cli?toc=%2Fazure%2Fvirtual-machines%2Flinux%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Flinux%2Fbreadcrumb%2Ftoc.json&view=azure-java-stable) e aos Anfitriões Dedicados do Azure. Existem alguns cenários para mover o controlo de manutenção em todas as regiões:

- Para mover a configuração do seu controlo de manutenção, mas não os recursos associados à configuração, siga as instruções deste artigo.
- Para mover os recursos associados a uma configuração de manutenção, mas não à configuração em si, siga [estas instruções](move-region-maintenance-configuration-resources.md).
- Para mover tanto a configuração de manutenção como os recursos associados, siga primeiro as instruções deste artigo. Em seguida, siga [estas instruções](move-region-maintenance-configuration-resources.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a mover uma configuração de controlo de manutenção:

- As configurações de manutenção estão associadas com VMs Azure ou Anfitriões Dedicados Azure. Certifique-se de que existem recursos VM/host na nova região antes de começar.
- Identificar: 
    - Configurações de controlo de manutenção existentes.
    - Os grupos de recursos em que as configurações existentes residem atualmente. 
    - Os grupos de recursos aos quais as configurações serão adicionadas após a mudança para a nova região. 
    - Os recursos associados à configuração de manutenção que pretende mover.
    - Verifique se os recursos da nova região são os mesmos que os associados às configurações de manutenção atuais. As configurações podem ter os mesmos nomes na nova região que na antiga, mas isso não é necessário.

## <a name="prepare-and-move"></a>Preparar e mover 

1. Recupere todas as configurações de manutenção em cada subscrição. Execute o comando da lista de configuração de manutenção CLI [az](https://docs.microsoft.com/cli/azure/ext/maintenance/maintenance/configuration?view=azure-cli-latest#ext-maintenance-az-maintenance-configuration-list) para o fazer, substituindo $subId pelo seu ID de subscrição.

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. Reveja a lista de registos de configuração retornado dentro da subscrição. Veja a seguir um exemplo. A sua lista conterá valores para o seu ambiente específico.

    **Nome** | **Localização** | **Grupo de recursos**
    --- | --- | ---
    Manutenção de Salto | eastus2 | grupo de recursos de configuração
    IgniteDemoConfig | eastus2 | grupo de recursos de configuração
    padrãoManutençãoConfiguração-leste | eastus | configuração de teste
    

3. Guarde a sua lista para referência. À medida que move as configurações, ajuda-o a verificar se tudo foi movido.
4. Como referência, mapeie cada grupo de configuração/recurso para o novo grupo de recursos na nova região.
5. Crie novas configurações de manutenção na nova região utilizando [powerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration), ou [CLI](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration).
6. Associe as configurações com os recursos da nova região, utilizando [powerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration), ou [CLI](../virtual-machines/maintenance-control-cli.md#assign-the-configuration).


## <a name="verify-the-move"></a>Verifique o movimento

Depois de mover as configurações, compare configurações e recursos na nova região com a lista de tabelas que preparou.


## <a name="clean-up-source-resources"></a>Limpar os recursos de origem

Após a mudança, considere apagar as configurações de manutenção em movimento na região de origem, [PowerShell](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration), ou [CLI](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration).


## <a name="next-steps"></a>Passos seguintes

Siga [estas instruções](move-region-maintenance-configuration-resources.md) se precisar de movimentar recursos associados às configurações de manutenção. 
