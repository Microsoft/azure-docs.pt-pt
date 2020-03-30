---
title: Ativar a virtualização aninhada num modelo VM em Serviços de Laboratório Azure [ Microsoft Docs
description: Aprenda a criar um Modelo VM com vários VMs no interior.  Por outras palavras, permita a virtualização aninhada num modelo VM nos Serviços de Laboratório Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 59b32834369f76d39bb4a253dad4ec541e7ef999
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502005"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Ativar a virtualização aninhada em uma máquina virtual modelo em Serviços de Laboratório Azure

Atualmente, o Azure Lab Services permite-lhe configurar uma máquina virtual modelo num laboratório e disponibilizar uma única cópia a cada um dos seus utilizadores. Se você é um professor ensinando aulas de networking, segurança ou TI, você pode precisar fornecer a cada um dos seus alunos um ambiente em que várias máquinas virtuais podem falar entre si sobre uma rede.

A virtualização aninhada permite criar um ambiente multi-VM dentro da máquina virtual modelo de um laboratório. A publicação do modelo fornecerá a cada utilizador do laboratório uma máquina virtual configurada com vários VMs dentro dele.  Este artigo abrange como configurar a virtualização aninhada numa máquina de modelos em Serviços de Laboratório Azure.

## <a name="what-is-nested-virtualization"></a>O que é a virtualização aninhada?

A virtualização aninhada permite-lhe criar máquinas virtuais dentro de uma máquina virtual. A virtualização aninhada é feita através do Hyper-V, e só está disponível em VMs do Windows.

Para obter mais informações sobre a virtualização aninhada, consulte os seguintes artigos:

- [Virtualização Aninhada em Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Como permitir a virtualização aninhada num VM Azure](../../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>Considerações

Antes de criar um laboratório com virtualização aninhada, aqui estão algumas coisas a ter em conta.

- Ao criar um novo laboratório, selecione **tamanhos médios (virtualização aninhada)** ou **large (virtualização aninhada)** para o tamanho da máquina virtual. Estes tamanhos de máquina virtual suportam a virtualização aninhada.
- Escolha um tamanho que proporcione um bom desempenho tanto para o anfitrião como para as máquinas virtuais do cliente.  Lembre-se que, ao utilizar a virtualização, o tamanho que escolher deve ser adequado não apenas para uma máquina, mas para o anfitrião, bem como para quaisquer máquinas de cliente que devem ser executadas simultaneamente.
- As máquinas virtuais dos clientes não terão acesso aos recursos do Azure, como servidores DNS na rede virtual Azure.
- A máquina virtual do hospedeiro requer configuração para permitir que a máquina cliente tenha conectividade com a Internet.
- As máquinas virtuais dos clientes são licenciadas como máquinas independentes. Consulte o [Microsoft Licensing](https://www.microsoft.com/licensing/default) para obter informações sobre o licenciamento para sistemas e produtos de operação da Microsoft. Verifique os acordos de licenciamento para qualquer outro software que esteja a ser utilizado antes de configurar a máquina do modelo.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Ativar a virtualização aninhada numa VM de modelo

Este artigo assume que criou uma conta de laboratório e laboratório.  Para obter mais informações sobre a criação de uma nova conta de laboratório, consulte [o tutorial para criar uma Conta lab.](tutorial-setup-lab-account.md) Para mais informações sobre como criar laboratório, consulte [a criação de um tutorial](tutorial-setup-classroom-lab.md)de laboratório em sala de aula.

>[!IMPORTANT]
>Selecione **Large (virtualização aninhada)** ou **Média (virtualização aninhada)** para o tamanho da máquina virtual ao criar o laboratório.  A virtualização aninhada não funcionará de outra forma.  

Para se ligar à máquina do modelo, consulte [criar e gerir um modelo](how-to-create-manage-template.md)de sala de aula .

### <a name="using-script-to-enable-nested-virtualization"></a>Usando o script para permitir a virtualização aninhada

Para utilizar a configuração automatizada para virtualização aninhada com o Windows Server 2016 ou windows Server 2019, consulte [a virtualização aninhada de Enable numa máquina virtual modelo em Serviços de Laboratório Azure utilizando um script](how-to-enable-nested-virtualization-template-vm-using-script.md). Utilizará scripts de [scripts Hyper-V](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) de Serviços de Laboratório para instalar a função Hyper-V.  Os scripts também vão configurar a rede para que as máquinas virtuais Hyper-V possam ter acesso à Internet.

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>Utilização de ferramentas windows para permitir a virtualização aninhada

A virtualização aninhada de configuração para windows Server 2016 ou Windows Server 2019 utilizando funções do Windows e ferramentas administrativas, ver [Enable aninhadamente virtualização em uma máquina virtual modelo em Serviços de Laboratório Azure manualmente](how-to-enable-nested-virtualization-template-vm-ui.md).  As instruções também abrangerão como configurar a rede para que as máquinas virtuais Hyper-V possam ter acesso à Internet.
