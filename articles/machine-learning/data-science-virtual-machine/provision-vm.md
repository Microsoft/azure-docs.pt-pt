---
title: 'Quickstart: Criar uma máquina virtual de ciência de dados do Windows'
titleSuffix: Azure Data Science Virtual Machine
description: Configurar e criar uma máquina de Virtual de ciência de dados no Azure para análise e machine learning.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 12/31/2019
ms.openlocfilehash: 1fdf8eae5d19a2d43499d1984f4dea834d8a61d8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79241109"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-windows"></a>Quickstart: Configurar a máquina virtual de ciência de dados para windows

Prepare-se e esteja a funcionar com uma Máquina Virtual de Ciência de Dados do Windows Server 2019.

## <a name="prerequisite"></a>Pré-requisito

Para criar uma Máquina Virtual de Ciência de Dados do Windows, deve ter uma subscrição Azure. [Experimente Azure de graça.](https://azure.com/free)
Por favor, note que as contas gratuitas do Azure não suportam a gpu ativada pela máquina virtual SKUs.

## <a name="create-your-dsvm"></a>Crie o seu DSVM

Para criar uma instância DSVM:

1. Vá ao [portal Azure](https://portal.azure.com) Poderá ser solicitado a fazer sessão na sua conta Azure se ainda não tiver assinado.
1. Encontre a listagem de máquinas virtuais digitando em "data science virtual machine" e selecionando "Data Science Virtual Machine - Win 2019 (Preview)."

1. Selecione o botão **Criar** na parte inferior.

1. Deve ser redirecionado para a lâmina "Criar uma máquina virtual".

1. Preencha o separador **Basics:**
      * **Subscrição**: Se tiver mais de uma subscrição, selecione aquela em que a máquina será criada e faturada. Tem de ter privilégios de criação de recursos para esta subscrição.
      * **Grupo de recursos**: Criar um novo grupo ou utilizar um existente.
      * **Nome**da máquina virtual : Introduza o nome da máquina virtual. É assim que vai aparecer no seu portal Azure.
      * **Localização**: Selecione o datacenter mais adequado. Para acesso mais rápido à rede, é o datacenter que tem a maioria dos seus dados ou está mais próximo da sua localização física. Saiba mais sobre [as Regiões Azure.](https://azure.microsoft.com/global-infrastructure/regions/)
      * **Imagem**: Deixe o valor predefinido.
      * **Tamanho**: Isto deve povoar automaticamente com um tamanho adequado para cargas de trabalho gerais. Leia mais sobre os tamanhos de [VM do Windows em Azure](../../virtual-machines/windows/sizes.md).
      * **Nome de utilizador**: Introduza o nome de utilizador do administrador. Este é o nome de utilizador que utilizará para iniciar sessão na sua máquina virtual, e não precisa de ser o mesmo que o seu nome de utilizador Azure.
      * **Palavra-passe**: Introduza a palavra-passe que utilizará para iniciar sessão na sua máquina virtual.    
1. Selecione **Rever + criar**.
1. **Review+criar**
   * Certifique-se de que todas as informações que introduziu estão corretas. 
   * Selecione **Criar**.


> [!NOTE]
> * Não paga taxas de licenciamento pelo software que vem pré-carregado na máquina virtual. Paga-se o custo da computação pelo tamanho do servidor que escolheu no passo **tamanho.**
> * O provisionamento demora 10 a 20 minutos. Pode ver o estado do seu VM no portal Azure.

## <a name="access-the-dsvm"></a>Acesso ao DSVM

Depois de o VM ser criado e aprovisionado, siga os passos listados para [ligar à sua máquina virtual baseada em Azure.](../../marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm.md) Utilize as credenciais de conta de administração que configurado no passo **Básico** de criação de uma máquina virtual. 

Está pronto para começar a utilizar as ferramentas que são instaladas e configuradas na VM. Muitas das ferramentas podem ser acedidas através de azulejos de menu **Iniciar** e ícones de ambiente de trabalho.

Também pode anexar um DSVM aos Cadernos Azure para executar cadernos Jupyter no VM e contornar as limitações do nível de serviço gratuito. Para mais informações, consulte [Gerir e configurar projetos de Cadernos.](../../notebooks/configure-manage-azure-notebooks-projects.md#manage-and-configure-projects)

<a name="tools"></a>


## <a name="next-steps"></a>Passos seguintes

* Explore as ferramentas no DSVM abrindo o menu **Iniciar.**
* Saiba mais sobre o Azure Machine Learning lendo o [](../index.yml) [que é Azure Machine Learning?](../overview-what-is-azure-ml.md)
* Leia o artigo [dez coisas que pode fazer na Máquina Virtual da Ciência dos Dados](https://aka.ms/dsvmtenthings).
* Visite a [Galeria Azure AI](https://gallery.cortanaintelligence.com) para obter amostras de machine learning e análise de dados que utilizam o Azure Machine Learning e serviços de dados relacionados no Azure. Também fornecemos um ícone para esta galeria no menu **Iniciar** e no ambiente de trabalho da máquina virtual.

