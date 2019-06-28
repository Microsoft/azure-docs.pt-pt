---
title: Criar um anfitrião de bastião do Azure | Documentos da Microsoft
description: Neste artigo, saiba como criar um anfitrião de bastião do Azure
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 5fbd64d2f001a0fbe91de2d954889816a1819479
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67339440"
---
# <a name="create-an-azure-bastion-host-preview"></a>Criar um anfitrião de bastião do Azure (pré-visualização)

Este artigo mostra-lhe como criar um anfitrião de bastião do Azure. Depois de aprovisionar o serviço de Bastion do Azure na sua rede virtual, a experiência totalmente integrada de RDP/SSH está disponível para todas as suas VMs na mesma rede virtual. Esta implementação é realizada por rede virtual e não por subscrição/conta ou máquina virtual.

Existem duas formas que pode criar um recurso de anfitrião de bastião:

* Crie um recurso de Bastion com o portal do Azure.
* Crie um recurso de Bastion no portal do Azure utilizando as definições de VM existentes.

> [!IMPORTANT]
> Esta pré-visualização pública é disponibilizada sem um contrato de nível de serviço e não deve ser utilizada para cargas de trabalho de produção. Algumas funcionalidades podem não ser suportadas, podem ter capacidades restringidas ou podem não estar disponíveis em todas as localizações do Azure. Veja os [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para obter mais informações.
>

## <a name="before-you-begin"></a>Antes de começar

A pré-visualização pública está limitado às regiões públicas do Azure seguintes:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

Para participar nesta pré-visualização, terá de se registar. Utilize estes passos para se registar na pré-visualização:

[!INCLUDE [register](../../includes/bastion-preview-register-include.md)]

## <a name="createhost"></a>Criar um anfitrião de bastião

Esta secção ajuda-o a criar um novo recurso de Bastion do Azure no portal do Azure.

1. Na home page no [ portal do Azure - pré-visualização](https://aka.ms/BastionHost), clique em **+ criar um recurso**. Certifique-se de que utilizar a ligação fornecida para aceder ao portal para esta pré-visualização, não o portal do Azure normal.

1. No **New** página, além do *pesquisar no Marketplace* , digite **Bastion**, em seguida, clique em **Enter** para obter os resultados da pesquisa.

1. Nos resultados, clique em **Bastion (pré-visualização)** . Certifique-se de que o publicador *Microsoft* e a categoria é *Networking*.

1. Sobre o **Bastion (pré-visualização)** página, clique em **criar** para abrir o **criar um bastion** página.

1. Sobre o **criar um bastion** página, configurar um novo recurso de Bastion. Especifique as definições de configuração para o seu recurso de Bastion.

    ![criar um bastion](./media/bastion-create-host-portal/settings.png)

    * **Subscrição**: A subscrição do Azure que pretende utilizar para criar um novo recurso de Bastion.
    * **Grupo de recursos**: O grupo de recursos do Azure na qual o novo recurso de Bastion será criado na. Se não tiver um grupo de recursos existente, pode criar um novo.
    * **Nome**: O nome do novo recurso de Bastion
    * **Região**: A região pública do Azure que o recurso será criado.
    * **Rede virtual**: A rede virtual na qual o recurso de Bastion será criado na. Pode criar uma nova rede virtual no portal do durante este processo, caso não tem ou não pretende utilizar uma rede virtual existente. Se estiver a utilizar uma rede virtual existente, certifique-se que a rede virtual existente tem suficiente espaço de endereço livre para acomodar os requisitos de sub-rede de Bastion.
    * **Sub-rede**: A sub-rede na sua rede virtual em que o novo recurso de anfitrião de bastião será implementado. Tem de criar uma sub-rede com o valor de nome **AzureBastionSubnet**. Este valor permite que o Azure saiba qual a sub-rede para implementar os recursos de Bastion. Isto é diferente de uma sub-rede de Gateway. É altamente recomendável que utilize, pelo menos, / 27 ou sub-rede maior (/ 27, / 26 e assim por diante). Criar a **AzureBastionSubnet** sem quaisquer grupos de segurança de rede, encaminhar tabelas ou delegações.
    * **Endereço IP público**: O IP público do recurso de Bastion no qual será acedido RDP/SSH (através da porta 443). Criar um novo IP público ou utilize um já existente. O endereço IP público tem de ser na mesma região que o recurso de Bastion que está a criar.
    * **Nome do endereço IP público**: O nome do recurso de endereço IP público.
    * **Endereço IP público SKU**: Preenchidas previamente pelo padrão para **padrão**.
    * **Atribuição**: Preenchidas previamente pelo padrão para **estático**.

1. Quando tiver terminado de especificar as definições, clique em **rever + criar**. Isso valida os valores. Assim que a validação é bem-sucedida, pode iniciar o processo de criação.
1. Em criar uma página de bastion, clique em **criar**.
1. Verá uma mensagem a indicar-lhe que a implementação está em curso. Estado irá apresentar nesta página, como os recursos são criados. Demora cerca de 5 minutos para o recurso de Bastion ser criada e implementada.

## <a name="createvmset"></a>Criar um anfitrião de bastião com as definições da VM

Se criar um anfitrião de bastião no portal com uma VM existente, várias definições serão automaticamente predefinido correspondente à sua máquina virtual e/ou a rede virtual.

1. Na [portal de pré-visualização](https://aka.ms/BastionHost), navegue até à sua máquina virtual, em seguida, clique em **Connect**.

    ![Ligação da VM](./media/bastion-create-host-portal/vmsettings.png)

1. Na barra lateral direita, clique em **Bastion**, em seguida, **utilização Bastion**.

    ![Bastion](./media/bastion-create-host-portal/vmbastion.png)

1. Na página de Bastion, preencha os campos de definições seguintes:

    * **Nome**: O nome do anfitrião de bastião que pretende criar.
    * **Sub-rede**: A sub-rede na rede virtual em que Bastion recurso será implementado. A sub-rede tem de ser criada com o nome **AzureBastionSubnet**. Isso permite que o Azure saiba qual a sub-rede para implementar o recurso de Bastion a. Isto é diferente de uma sub-rede de Gateway. Clique em **configuração de sub-rede de gerir** para criar a sub-rede de Bastion do Azure. É altamente recomendável que utilize, pelo menos, / 27 ou sub-rede maior (/ 27, / 26, etc.). Criar a **AzureBastionSubnet** sem quaisquer grupos de segurança de rede, encaminhar tabelas ou delegações. Clique em **criar** para criar a sub-rede, em seguida, continuar com as definições do seguintes.

      ![Bastion](./media/bastion-create-host-portal/subnet.png)
      
    * **Endereço IP público**: O IP público do recurso de Bastion no qual será acedido RDP/SSH (através da porta 443). Criar um novo IP público ou utilize um já existente. O endereço IP público tem de ser na mesma região que o recurso de Bastion que está a criar.
    * **Nome do endereço IP público**: O nome do recurso de endereço IP público.
1. No ecrã da validação, clique em **criar**. Aguarde cerca de 5 minutos para o recurso de Bastion ser criada e implementada.

## <a name="next-steps"></a>Passos Seguintes

Leia o [Bastion FAQ](bastion-faq.md)