---
title: Compreender endereços IP partilhados em Azure DevTest Labs / Microsoft Docs
description: Saiba como o Azure DevTest Labs utiliza endereços IP partilhados para minimizar os endereços IP públicos necessários para aceder aos seus VMs de laboratório.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/12/2019
ms.author: spelluru
ms.openlocfilehash: f7c9feedddab1aea031cb3a8879e868aae04df00
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "65236899"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Compreender endereços IP partilhados em Azure DevTest Labs

A Azure DevTest Labs permite que os VMs de laboratório partilhem o mesmo endereço IP público para minimizar o número de endereços IP públicos necessários para aceder aos seus VMs de laboratório individuais.  Este artigo descreve como os IPs partilhados funcionam e as suas opções de configuração relacionadas.

## <a name="shared-ip-setting"></a>Definição ip partilhada

Quando se cria um laboratório, é criado numa sub-rede de uma rede virtual.  Por padrão, esta sub-rede é criada com o conjunto **de IP público partilhado Enable** para *Sim*.  Esta configuração cria um endereço IP público para toda a subnet.  Para obter mais informações sobre a configuração de redes virtuais e subnets, consulte [Configure uma rede virtual em Azure DevTest Labs](devtest-lab-configure-vnet.md).

![Nova sub-rede de laboratório](media/devtest-lab-shared-ip/lab-subnet.png)

Para laboratórios existentes, pode ativar esta opção selecionando **Configuração e políticas > Redes Virtuais.** Em seguida, selecione uma rede virtual da lista e escolha **ENABLE SHARED PUBLIC IP** para uma sub-rede selecionada. Também pode desativar esta opção em qualquer laboratório se não quiser partilhar um endereço IP público através de VMs de laboratório.

Quaisquer VMs criados neste laboratório padrão para usar um IP partilhado.  Ao criar o VM, esta definição pode ser observada na página de **definições Avançadas** na **configuração do endereço IP**.

![Novo VM](media/devtest-lab-shared-ip/new-vm.png)

- **Partilhado:** Todos os VMs criados como **Shared** são colocados num grupo de recursos (RG). Um único endereço IP é atribuído para que RG e todos os VMs no RG usarão esse endereço IP.
- **Público:** Cada VM que cria tem o seu próprio endereço IP e é criado no seu próprio grupo de recursos.
- **Privado:** Cada VM que cria usa um endereço IP privado. Não é possível ligar-se a este VM diretamente a partir da internet com Remote Desktop.

Sempre que um VM com IP partilhado é adicionado à subnet, o DevTest Labs adiciona automaticamente o VM a um equilibrista de carga e atribui um número de porta TCP no endereço IP público, reencaminhamento para a porta RDP no VM.  

## <a name="using-the-shared-ip"></a>Usando o IP partilhado

- **Utilizadores linux:** SSH para o VM utilizando o endereço IP ou nome de domínio totalmente qualificado, seguido de um cólon, seguido da porta. Por exemplo, na imagem abaixo, o endereço RDP `mydevtestlab597975021002.eastus.cloudapp.azure.com:50661`para ligar ao VM é .

  ![Exemplo vm](media/devtest-lab-shared-ip/vm-info.png)

- **Utilizadores do Windows:** Selecione o botão **Connect** no portal Azure para descarregar um ficheiro RDP pré-configurado e aceder ao VM.

## <a name="next-steps"></a>Passos seguintes

* [Defina as políticas de laboratório em Laboratórios Azure DevTest](devtest-lab-set-lab-policy.md)
* [Configure uma rede virtual em Azure DevTest Labs](devtest-lab-configure-vnet.md)





