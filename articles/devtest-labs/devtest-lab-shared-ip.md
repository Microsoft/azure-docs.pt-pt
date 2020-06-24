---
title: Compreenda os endereços IP partilhados na Azure DevTest Labs Microsoft Docs
description: Saiba como a Azure DevTest Labs utiliza endereços IP partilhados para minimizar os endereços IP públicos necessários para aceder aos seus VMs de laboratório.
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
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84896789"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Compreenda endereços IP partilhados em Azure DevTest Labs

A Azure DevTest Labs permite que os VMs de laboratório partilhem o mesmo endereço IP público para minimizar o número de endereços IP públicos necessários para aceder aos seus VMs de laboratório individuais.  Este artigo descreve como os IPs partilhados funcionam e as suas opções de configuração relacionadas.

## <a name="shared-ip-setting"></a>Definição de IP compartilhada

Quando se cria um laboratório, é criado numa sub-rede de uma rede virtual.  Por predefinição, esta sub-rede é criada com **Enable public IP** definido para *Sim*.  Esta configuração cria um endereço IP público para toda a sub-rede.  Para obter mais informações sobre a configuração de redes virtuais e sub-redes, consulte [configurar uma rede virtual em Azure DevTest Labs](devtest-lab-configure-vnet.md).

![Nova sub-rede de laboratório](media/devtest-lab-shared-ip/lab-subnet.png)

Para laboratórios existentes, pode ativar esta opção selecionando **Configuração e políticas > Redes Virtuais.** Em seguida, selecione uma rede virtual da lista e escolha **ENABLE SHARED PUBLIC IP** para uma sub-rede selecionada. Também pode desativar esta opção em qualquer laboratório se não quiser partilhar um endereço IP público através de VMs de laboratório.

Quaisquer VMs criados neste laboratório padrão para usar um IP compartilhado.  Ao criar o VM, esta definição pode ser observada na página **de definições avançadas** na **configuração do endereço IP**.

![Novo VM](media/devtest-lab-shared-ip/new-vm.png)

- **Partilhado:** Todos os VMs criados como **Shared** são colocados num único grupo de recursos (RG). Um único endereço IP é atribuído para esse RG e todos os VMs no RG usarão esse endereço IP.
- **Público:** Cada VM que cria tem o seu próprio endereço IP e é criado no seu próprio grupo de recursos.
- **Particular:** Cada VM que cria utiliza um endereço IP privado. Não é possível ligar-se a este VM diretamente da internet com o Remote Desktop.

Sempre que um VM com IP partilhado é adicionado à sub-rede, a DevTest Labs adiciona automaticamente o VM a um esquilibrista de carga e atribui um número de porta TCP no endereço IP público, reencaminhando para a porta RDP no VM.  

## <a name="using-the-shared-ip"></a>Usando o IP compartilhado

- **Utilizadores linux:** SSH para o VM utilizando o endereço IP ou o nome de domínio totalmente qualificado, seguido de um cólon, seguido da porta. Por exemplo, na imagem abaixo, o endereço RDP para ligar ao VM é `mydevtestlab597975021002.eastus.cloudapp.azure.com:50661` .

  ![Exemplo VM](media/devtest-lab-shared-ip/vm-info.png)

- **Utilizadores do Windows:** Selecione o botão **Ligar** no portal Azure para descarregar um ficheiro RDP pré-configurado e aceder ao VM.

## <a name="next-steps"></a>Passos seguintes

* [Definir políticas de laboratório em Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Configurar uma rede virtual em Azure DevTest Labs](devtest-lab-configure-vnet.md)





