---
title: Ativar a virtualização aninhada num modelo VM em Azure Lab Services | Microsoft Docs
description: Neste artigo, aprenda a configurar a virtualização aninhada numa máquina de modelo nos Serviços de Laboratório Azure.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 319695088e967dc3156ecab5c1b9458e77fcc186
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91251479"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Ativar a virtualização aninhada em uma máquina virtual de modelo nos Serviços de Laboratório Azure

Atualmente, o Azure Lab Services permite-lhe configurar uma máquina virtual modelo num laboratório e disponibilizar uma única cópia a cada um dos seus utilizadores. Se você é um educador ensinando networking, segurança ou aulas de TI, você pode precisar fornecer a cada um dos seus alunos um ambiente em que várias máquinas virtuais podem falar entre si em uma rede.

A virtualização aninhada permite-lhe criar um ambiente multi-VM dentro da máquina virtual de modelo de um laboratório. A publicação do modelo fornecerá a cada utilizador em laboratório uma máquina virtual configurada com vários VMs dentro do mesmo.  Este artigo abrange como configurar a virtualização aninhada numa máquina de modelo nos Serviços de Laboratório Azure.

## <a name="what-is-nested-virtualization"></a>O que é a virtualização aninhada?

A virtualização aninhada permite-lhe criar máquinas virtuais dentro de uma máquina virtual. A virtualização aninhada é feita através do Hyper-V, e só está disponível em VMs do Windows.

Para obter mais informações sobre a virtualização aninhada, consulte os seguintes artigos:

- [Virtualização Aninhada em Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Como permitir a virtualização aninhada num VM Azure](../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>Considerações

Antes de criar um laboratório com virtualização aninhada, aqui estão algumas coisas a ter em conta.

- Ao criar um novo laboratório, selecione tamanhos **de média (virtualização aninhada)** ou **grandes (virtualização aninhada)** para o tamanho da máquina virtual. Estes tamanhos de máquina virtual suportam a virtualização aninhada.
- Escolha um tamanho que proporcione um bom desempenho tanto para as máquinas virtuais do anfitrião como para o cliente.  Lembre-se, ao utilizar a virtualização, o tamanho que escolher deve ser adequado não apenas para uma máquina, mas para o hospedeiro, bem como para quaisquer máquinas Hyper-V que estejam a funcionar simultaneamente.
- As máquinas virtuais dos clientes não terão acesso aos recursos do Azure, como servidores DNS, na rede virtual Azure.
- A máquina virtual hospedeira requer configuração para permitir que a máquina do cliente tenha conectividade com a internet.
- As máquinas virtuais do cliente são licenciadas como máquinas independentes. Consulte [o Microsoft Licensing](https://www.microsoft.com/licensing/default) para obter informações sobre o licenciamento para sistemas e produtos de operação da Microsoft. Verifique os contratos de licenciamento de qualquer outro software que esteja a ser utilizado antes de configurar a máquina de modelo.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Ativar a virtualização aninhada numa VM de modelo

Este artigo assume que criou uma conta de laboratório e laboratório.  Para obter mais informações sobre a criação de uma nova conta de laboratório, consulte [tutorial para criar uma Conta de Laboratório.](tutorial-setup-lab-account.md) Para obter mais informações sobre como criar laboratório, consulte [configurar um tutorial de laboratório em sala de aula.](tutorial-setup-classroom-lab.md)

>[!IMPORTANT]
>Selecione **Large (virtualização aninhada)** ou **Medium (virtualização aninhada)** para o tamanho da máquina virtual ao criar o laboratório.  A virtualização aninhada não funcionará de outra forma.  

Para ligar à máquina do modelo, consulte [criar e gerir um modelo de sala de aula.](how-to-create-manage-template.md)

Para permitir a virtualização aninhada, existem algumas tarefas a realizar.  

- **Ativar o papel de Hiper-V**. A função Hyper-V deve ser ativada para a criação e funcionamento de máquinas virtuais Hyper-V na máquina virtual lab Services.
- **Ativar o DHCP**.  Quando a máquina virtual lab Services tem a função DHCP ativada, as máquinas virtuais Hyper-V podem ser automaticamente atribuídas a um endereço IP.
- **Criar rede NAT para Hiper-VMs**.  A rede NAT está configurada para permitir que as máquinas virtuais Hyper-V tenham acesso à Internet.  As máquinas virtuais Hyper-V podem comunicar entre si.

>[!NOTE]
>A rede NAT criada no Lab Services VM permitirá que um Hiper-VM aceda à internet e a outros VMs Hiper-V no mesmo VM de Serviços de Laboratório.  O Hiper-V VM não será capaz de aceder a recursos Azure, como servidores DNS, na rede virtual Azure.

A realização das tarefas acima listadas pode ser feita utilizando um script ou utilizando ferramentas do Windows.  Leia as secções abaixo para mais detalhes.

### <a name="using-script-to-enable-nested-virtualization"></a>Usando o script para permitir a virtualização aninhada

Para utilizar a configuração automatizada para virtualização aninhada com o Windows Server 2016 ou Windows Server 2019, consulte [Ativar a virtualização aninhada numa máquina virtual de modelo nos Serviços Azure Lab utilizando um script](how-to-enable-nested-virtualization-template-vm-using-script.md). Você usará scripts de [scripts Hiper-V de Serviços de Laboratório](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) para instalar a função Hyper-V.  Os scripts também irão configurar a rede para que as máquinas virtuais Hyper-V possam ter acesso à Internet.

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>Utilização de ferramentas Windows para permitir a virtualização aninhada

A virtualização aninhada de configuração para Windows Server 2016 ou Windows Server 2019 utilizando funções e ferramentas administrativas do Windows, consulte [Ativar a virtualização aninhada numa máquina virtual de modelo nos Serviços Azure Lab manualmente](how-to-enable-nested-virtualization-template-vm-ui.md).  As instruções também abrangerão como configurar a rede para que as máquinas virtuais Hiper-V possam ter acesso à Internet.
