---
title: Introdução às Máquinas Virtuais para o Azure Stack
description: Saiba mais sobre as máquinas virtuais do Azure Stack
services: azure-stack
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.topic: conceptual
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: bf075372da4cd23c89c24dcb30ffbcfac6775fe4
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775089"
---
# <a name="introduction-to-azure-stack-virtual-machines"></a>Introdução às Máquinas Virtuais para o Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

O Azure Stack oferece máquinas virtuais (VMs) como um tipo de um recurso de computação a pedido e dimensionável. Pode escolher uma VM quando precisar de mais controlo sobre o ambiente de computação. Este artigo fornece detalhes antes de criar a sua primeira VM.

VM do Azure Stack dá-lhe a flexibilidade da virtualização, sem a necessidade de gerir clusters ou máquinas individuais. No entanto, ainda tem de manter a VM a realizar tarefas como a configuração, aplicação de patches e instalar o software que é executado no mesmo.

Pode utilizar as máquinas virtuais do Azure Stack de várias formas. Por exemplo:

- **Desenvolvimento e teste**  
    VMs de pilha do Azure permitem-lhe criar um computador com uma configuração específica necessária para codificar e testar uma aplicação.

- **Aplicações na cloud**  
    Uma vez que pode variar a pedido para a sua aplicação, poderá fazer sentido em termos económico executá-lo numa VM no Azure Stack. Paga pelas VMs adicionais quando precisar delas e encerrá-las quando não o fizer.

- **Datacenter expandido**  
    Máquinas virtuais numa rede virtual do Azure Stack podem ser ligadas à rede da sua organização ou para o Azure.

As VMs que o aplicativo usar pode dimensionar vertical ou aumentar horizontalmente, para tudo o que é necessário para atender às suas necessidades.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>O que é necessário pensar antes de criar uma VM

Sempre há considerações de design ao criar uma infraestrutura de aplicação no Azure Stack. Esses aspectos de uma VM são importantes a considerar antes de começar a criar a sua infraestrutura:

- Os nomes dos recursos da aplicação.
- O tamanho da VM.
- O número máximo de VMs que podem ser criadas.
- O sistema operativo que executa a VM.
- A configuração da VM depois de iniciar.
- Os recursos relacionados que a VM precisa.

### <a name="naming"></a>Atribuição de nomes

Uma máquina virtual tem um nome atribuído à mesma e ter um nome de computador configurado como parte do sistema operativo. O nome de uma VM pode ter até 15 carateres.

Se utilizar o Azure Stack para criar o disco do sistema operativo, o nome do computador e o nome da máquina virtual são os mesmos. Se carrega e usar sua própria imagem que contém um sistema de operativo configurado anteriormente e utilizá-lo para criar uma máquina virtual, os nomes podem ser diferentes. Ao carregar o seu próprio ficheiro de imagem, como melhor prática, certifique-se o nome do computador do sistema operativo e o nome de máquina virtual são os mesmos.

### <a name="vm-size"></a>Tamanhos de VM

O tamanho da VM que utiliza é determinado pela carga de trabalho que pretende executar. O tamanho que escolher determina fatores como o poder de processamento, a memória e capacidade de armazenamento. O Azure Stack oferece uma variedade de tamanhos para suportar muitos tipos de utilizações.

### <a name="vm-limits"></a>Limites de VM

A sua subscrição tem limites de quota predefinidos num local que pode afetar a implementação de VMs para o seu projeto. O limite atual numa base por subscrição é de 20 VMs por região.

### <a name="operating-system-disks-and-images"></a>Discos do sistema operativo e imagens

As máquinas virtuais utilizam discos rígidos virtuais (VHDs) para armazenar o respetivo sistema operativo (SO) e dados. Os VHDs também são utilizados para as imagens que pode escolher para instalar um SO. O Azure Stack fornece um mercado para utilizar com diversas versões e tipos de sistemas operativos. Imagens do Marketplace são identificadas pelo publicador da imagem, oferta, SKU e versão (normalmente, a versão é especificada como **mais recente**.)

A tabela seguinte mostra como localizar as informações de uma imagem:

|Método|Descrição|
|---------|---------|
|Portal do Azure Stack|Os valores são especificados automaticamente ao selecionar uma imagem a utilizar.|
|Azure Stack do PowerShell|`Get-AzureRMVMImagePublisher -Location "location"`<br>`Get-AzureRMVMImageOffer -Location "location" -Publisher "publisherName"`<br>`Get-AzureRMVMImageSku -Location "location" -Publisher "publisherName" -Offer "offerName"`|
|APIs REST     |[Listar publicadores de imagem](/rest/api/compute/platformimages/platformimages-list-publishers)<br>[Listar ofertas da imagem](/rest/api/compute/platformimages/platformimages-list-publisher-offers)<br>[Listar SKUs de imagem](/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus)|

Pode optar por carregar e utilizar a sua própria imagem. Se o fizer, o nome do publicador, oferta, SKU e não são utilizados.

### <a name="extensions"></a>Extensões

Extensões de VM fornecem capacidades adicionais sua VM, através da configuração pós-implementação e tarefas automatizadas.
Estas tarefas comuns podem ser realizadas com extensões:

- **Executar scripts personalizados**  
    A extensão de Script personalizado ajuda-o a configurar cargas de trabalho na VM ao executar o script quando a VM está aprovisionada.

- **Implementar e gerir configurações**  
    A extensão de PowerShell Desired State Configuration (DSC) ajuda-o a configurar o DSC numa VM para gerir configurações e ambientes.

- **Recolher dados de diagnóstico**  
    A extensão de diagnóstico do Azure ajuda-o a configurar a VM para recolher dados de diagnóstico que podem ser utilizados para monitorizar o estado de funcionamento da sua aplicação.

### <a name="related-resources"></a>Recursos relacionados

Os recursos na tabela seguinte são utilizados pela VM e tem de existir ou ser criados quando a VM é criada:

|Recurso|Necessário|Descrição|
|---------|---------|---------|
|Grupo de recursos|Sim|A VM tem de estar contida num grupo de recursos.|
|Conta de armazenamento|Não|A VM não precisa da conta de armazenamento para armazenar os respetivos discos rígidos virtuais se utilizar Managed Disks. <br>A VM tem da conta de armazenamento para armazenar os respetivos discos rígidos virtuais se utilizar discos não geridos.|
|Rede virtual|Sim|A VM tem de ser um membro de uma rede virtual.|
|Endereço IP público|Não|A VM pode ter um endereço IP público atribuído para aceder ao mesmo remotamente.|
|Interface de rede|Sim|A VM precisa da interface de rede para comunicar na rede.|
|Discos de dados|Não|A VM pode incluir discos de dados para expandir as capacidades de armazenamento.|

## <a name="create-your-first-vm"></a>Criar a sua primeira VM

Existem várias opções para criar uma VM. À sua escolha depende do seu ambiente. A tabela seguinte fornece informações para ajudar a começar a criar a sua VM:

|Método|Artigo|
|---------|---------|
|Portal do Azure Stack|Criar uma máquina virtual do Windows com o portal do Azure Stack<br>[Criar uma máquina virtual do Linux através do portal do Azure Stack](azure-stack-quick-linux-portal.md)|
|Modelos|Modelos de início rápido da pilha do Azure estão localizados em:<br> [https://github.com/Azure/AzureStack-QuickStart-Templates](https://github.com/Azure/AzureStack-QuickStart-Templates)|
|PowerShell|[Criar uma máquina virtual do Windows com o PowerShell no Azure Stack](azure-stack-quick-create-vm-windows-powershell.md)<br>[Criar uma máquina virtual Linux com o PowerShell no Azure Stack](azure-stack-quick-create-vm-linux-powershell.md)|
|CLI|[Criar uma máquina virtual do Windows utilizando o CLI do Azure Stack](azure-stack-quick-create-vm-windows-cli.md)<br>[Criar uma máquina virtual Linux com a CLI no Azure Stack](azure-stack-quick-create-vm-linux-cli.md)|

## <a name="manage-your-vm"></a>Gerir a VM

Pode gerir VMs com um portal baseada no browser, ferramentas de linha de comandos com suporte para processamento de scripts ou diretamente através de APIs. Algumas tarefas de gestão típicas que pode executar são:

- Obter informações sobre uma VM
- Ligar a uma VM
- Gerir a disponibilidade
- Efetuar cópias de segurança

### <a name="get-information-about-your-vm"></a>Obter informações sobre a sua VM

A tabela seguinte mostra algumas das formas de que obter informações sobre uma VM.

|Método|Descrição|
|---------|---------|
|Portal do Azure Stack|No hub menu, clique em máquinas virtuais e, em seguida, selecione a VM a partir da lista. Na página para a VM, terá acesso a informações de descrição geral, valores de definição e métricas de monitorização.|
|Azure PowerShell|Gerir VMs é semelhante no Azure e o Azure Stack. Para obter mais informações sobre como utilizar o PowerShell, consulte o tópico do Azure seguinte:<br>[Criar e gerir VMs do Windows com o módulo Azure PowerShell](../../virtual-machines/windows/tutorial-manage-vm.md#understand-vm-sizes)|
|SDKs do Cliente|Utilizar c# para gerir VMs é semelhante no Azure e o Azure Stack. Para obter mais informações, consulte o tópico do Azure seguinte:<br>[Criar e gerir VMs do Windows no Azure com c#](../../virtual-machines/windows/csharp.md)|

### <a name="connect-to-your-vm"></a>Ligar à VM

Pode utilizar o **Connect** botão no portal do Azure Stack para ligar à sua VM.

## <a name="next-steps"></a>Passos Seguintes

- [Considerações para máquinas virtuais no Azure Stack](azure-stack-vm-considerations.md)
