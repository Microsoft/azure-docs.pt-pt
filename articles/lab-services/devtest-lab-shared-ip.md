---
title: Compreender endereços IP partilhados no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como o Azure DevTest Labs utiliza endereços IP partilhados para minimizar os endereços IP públicos necessários para aceder às suas VMs do laboratório.
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: e7080901118dde33ed07c8a80f254b9b0d2e221c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60623041"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Compreender endereços IP partilhados no Azure DevTest Labs

O Azure DevTest Labs permite que as VMs do laboratório compartilham o mesmo endereço IP público para minimizar o número de endereços IP públicos, necessária para acessar seu laboratório individual VMs.  Este artigo descreve como partilhado de IPs de trabalho e as opções de configuração relacionados.

## <a name="shared-ip-setting"></a>Partilhado a definição do IP

Quando criar um laboratório, ele reside numa sub-rede de uma rede virtual.  Por predefinição, esta sub-rede é criada com **Enable partilhado IP público** definida como *Sim*.  Esta configuração cria um endereço IP público para toda a rede.  Para obter mais informações sobre como configurar redes virtuais e sub-redes, veja [configurar uma rede virtual no Azure DevTest Labs](devtest-lab-configure-vnet.md).

![Nova sub-rede de laboratório](media/devtest-lab-shared-ip/lab-subnet.png)

Para obter laboratórios existentes, pode ativar esta opção, selecionando **Konfigurace a zásady > redes virtuais**. Em seguida, selecione uma rede virtual a partir da lista e escolha **ATIVAR PARTILHADO IP público** para uma sub-rede selecionada. Também pode desativar esta opção em qualquer laboratório se não quiser compartilhar um endereço IP público em VMs do laboratório.

Todas as VMs criadas nesse padrão de laboratório para utilizar um IP partilhado.  Ao criar a VM, esta definição pode ser observada na **definições avançadas** painel em **configuração do endereço IP**.

![Nova VM](media/devtest-lab-shared-ip/new-vm.png)

- **Partilhado:** Todas as VMs criadas como **partilhado** são colocados num grupo de recursos (RG). É atribuído um endereço IP único para que RG e todas as VMs no RG usará esse endereço IP.
- **Público:** Cada VM que cria tem seu próprio endereço IP e é criada no seu próprio grupo de recursos.
- **Privada:** Cada VM que criar utiliza um endereço IP privado. Não será capaz de ligar a esta VM diretamente a partir da internet com o ambiente de trabalho remoto.

Sempre que uma VM com IP partilhado ativado é adicionada à sub-rede, o DevTest Labs é automaticamente adiciona a VM a um balanceador de carga e atribui um número de porta TCP no endereço IP público, reencaminhamento para a porta RDP na VM.  

## <a name="using-the-shared-ip"></a>Utilizar o IP partilhado

- **Utilizadores do Linux:** SSH para a VM ao utilizar o endereço IP ou nome de domínio completamente qualificado, seguido por dois-pontos, seguido da porta. Por exemplo, na imagem abaixo, o endereço RDP para ligar à VM é `doclab-lab13998814308000.centralus.cloudapp.azure.com:51686`.

  ![Exemplo VM](media/devtest-lab-shared-ip/vm-info.png)

- **Usuários do Windows:** Selecione o **Connect** botão no portal do Azure para transferir um ficheiro RDP previamente configurado e aceder à VM.

## <a name="next-steps"></a>Passos Seguintes

* [Definir políticas de laboratório no Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Configurar uma rede virtual no Azure DevTest Labs](devtest-lab-configure-vnet.md)





