---
title: Mover uma configuração de manutenção para outra região do Azure
description: Saiba como mover uma configuração de manutenção VM para outra região do Azure
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 91a6adecc9cf0db56fa4c433f388b05aa1bdef6a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98202917"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>Mover uma configuração de Controlo de Manutenção para outra região

Siga este artigo para mover uma configuração de Controlo de Manutenção para uma região Azure diferente. É melhor mover uma configuração por várias razões. Por exemplo, aproveitar uma nova região, implantar características ou serviços disponíveis numa região específica, satisfazer os requisitos de política interna e de governação, ou em resposta ao planeamento de capacidades.

[O controlo de manutenção,](maintenance-control.md)com configurações de manutenção personalizadas, permite-lhe controlar a forma como as atualizações da plataforma são aplicadas em VMs e em Azure Dedicated Hosts. Existem alguns cenários para mover o controlo de manutenção em todas as regiões:

- Para mover a configuração do controlo de manutenção, mas não os recursos associados à configuração, siga as instruções deste artigo.
- Para mover os recursos associados a uma configuração de manutenção, mas não a configuração em si, siga [estas instruções](move-region-maintenance-configuration-resources.md).
- Para mover tanto a configuração de manutenção como os recursos que lhe estão associados, siga primeiro as instruções deste artigo. Em seguida, siga [estas instruções.](move-region-maintenance-configuration-resources.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a mover uma configuração de controlo de manutenção:

- As configurações de manutenção estão associadas a VMs Azure ou Azure Dedicated Hosts. Certifique-se de que existem recursos VM/host na nova região antes de começar.
- Identificar: 
    - Configurações de controlo de manutenção existentes.
    - Os grupos de recursos em que as configurações existentes residem atualmente. 
    - Os grupos de recursos aos quais as configurações serão adicionadas após a mudança para a nova região. 
    - Os recursos associados à configuração de manutenção que pretende mover.
    - Verifique se os recursos na nova região são os mesmos que os associados às configurações de manutenção atuais. As configurações podem ter os mesmos nomes na nova região que na antiga, mas isso não é necessário.

## <a name="prepare-and-move"></a>Preparar e mover 

1. Recupere todas as configurações de manutenção em cada subscrição. Executar o comando da [lista de configuração de manutenção](/cli/azure/ext/maintenance/maintenance/configuration#ext-maintenance-az-maintenance-configuration-list) CLI az para o fazer, substituindo $subId pelo seu ID de subscrição.

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. Reveja a lista de registos de configuração devolvidos dentro da subscrição. Eis um exemplo. A sua lista conterá valores para o seu ambiente específico.

    **Nome** | **Localização** | **Grupo de recursos**
    --- | --- | ---
    Manutenção Skip | eastus2 | grupo de configuração-recursos
    IgniteDemoConfig | eastus2 | grupo de configuração-recursos
    defaultMaintenanceConfiguration-eastus | eastus | configuração de teste
    

3. Guarde a sua lista para referência. À medida que move as configurações, ajuda-o a verificar se tudo foi movido.
4. Como referência, mapear cada grupo de configuração/recursos para o novo grupo de recursos na nova região.
5. Criar novas configurações de manutenção na nova região utilizando [o PowerShell,](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)ou [CLI](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration).
6. Associe as configurações com os recursos da nova região, utilizando [o PowerShell,](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)ou [CLI.](../virtual-machines/maintenance-control-cli.md#assign-the-configuration)


## <a name="verify-the-move"></a>Verifique o movimento

Depois de mover as configurações, compare configurações e recursos na nova região com a lista de tabelas que preparou.


## <a name="clean-up-source-resources"></a>Limpar recursos de origem

Após o movimento, considere eliminar as configurações de manutenção em movimento na região de origem, [PowerShell,](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)ou [CLI](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration).


## <a name="next-steps"></a>Passos seguintes

Siga [estas instruções](move-region-maintenance-configuration-resources.md) se precisar de mover recursos associados às configurações de manutenção. 
