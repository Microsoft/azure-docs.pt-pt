---
title: Configurar endereços IP privados para VMs (clássicas) – CLI clássica do Azure
titlesuffix: Azure Virtual Network
description: Saiba como configurar os endereços IP privados para máquinas virtuais (clássico) usando a interface de linha de comandos clássica (CLI) do Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 64342c6dcf4c9a052442a6f4b0afb7a56994f496
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059228"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-classic-cli"></a>Configurar endereços IP privados para uma máquina virtual (clássico) com a CLI clássica do Azure

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo abrange o modelo de implementação clássica. Também pode [gerir um endereço IP privado estático no modelo de implementação do Resource Manager](virtual-networks-static-private-ip-arm-cli.md).

O exemplo de que comandos do CLI clássica do Azure que se seguem esperar um ambiente simples já criado. Se quiser executar os comandos à medida que são apresentadas neste documento, primeiro crie o ambiente de teste descrito em [criar uma vnet](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Como especificar um endereço IP privado estático ao criar uma VM
Para criar uma nova VM com o nome *DNS01* num novo serviço cloud com o nome *TestService* com base no cenário acima, siga estes passos:

1. Se nunca tiver utilizado a CLI do Azure, veja [Install and Configure the Azure CLI (Instalar e Configurar a CLI do Azure)](/cli/azure/install-cli-version-1.0) e siga as instruções até ao ponto onde poderá selecionar a sua conta e subscrição do Azure.
2. Executar o **criar o serviço do azure** comando para criar o serviço em nuvem.
   
        azure service create TestService --location uscentral
   
    Resultado esperado:
   
        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
3. Executar o **azure-criar vm** comando para criar a VM. Tenha em atenção o valor para um endereço IP privado estático. A lista apresentada depois do resultado explica os parâmetros utilizados.
   
        azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd
   
    Resultado esperado:
   
        info:    Executing command vm create
        warn:    --vm-size has not been specified. Defaulting to "Small".
        info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
        info:    Looking up virtual network
        info:    Looking up cloud service
        warn:    --location option will be ignored
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Retrieving storage accounts
        info:    Creating VM
        info:    OK
        info:    vm create command OK
   
   * **-l (ou --location)** . Região do Azure onde a VM será criada. Para o nosso cenário *centralus*.
   * **-n (ou - vm-name)** . Nome da VM a ser criada.
   * **-w (ou ----nome de rede virtual)** . Nome da VNet onde a VM será criada. 
   * **-S (ou --ip estático)** . Endereço de IP privado estático para a VM.
   * **TestService**. Nome do serviço cloud em que a VM será criada.
   * **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2**. Imagem utilizada para criar a VM.
   * **adminuser**. Administrador local para a VM do Windows.
   * <strong>AdminP@ssw0rd</strong>. Palavra-passe de administrador local para a VM do Windows.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Como obter estáticas informações de endereço IP privadas para uma VM
Para ver as informações de endereço IP privadas estáticas para a VM criada com o script acima, execute o seguinte comando da CLI do Azure e observe o valor para *rede StaticIP*:

    azure vm static-ip show DNS01

Resultado esperado:

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Como remover um endereço IP privado estático a partir de uma VM
Para remover o endereço IP privado estático adicionado à VM no script acima, execute o seguinte comando da CLI do Azure:

    azure vm static-ip remove DNS01

Resultado esperado:

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## <a name="how-to-add-a-static-private-ip-to-an-existing-vm"></a>Como adicionar um IP privado estático a uma VM existente
Para adicionar um endereço IP privado estático à VM criada usando o script acima, execute o seguinte comando:

    azure vm static-ip set DNS01 192.168.1.101

Resultado esperado:

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="set-ip-addresses-within-the-operating-system"></a>Conjunto de endereços IP no sistema operativo

É recomendável que não atribuir estaticamente IP privado atribuído à máquina virtual do Azure no sistema operativo de uma VM, a menos que necessário. Se definir manualmente o endereço IP privado no sistema operativo, certifique-se de que é o mesmo endereço como o endereço IP privado atribuído à VM do Azure ou pode perder a conectividade para a máquina virtual. Não atribua manualmente o endereço IP público atribuído a uma máquina virtual do Azure no sistema operacional da máquina virtual.

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [reservado de IP público](virtual-networks-reserved-public-ip.md) endereços.
* Saiba mais sobre [IP público (ILPIP) de nível de instância](virtual-networks-instance-level-public-ip.md) endereços.
* Consulte a [reservado IP REST APIs](https://msdn.microsoft.com/library/azure/dn722420.aspx).