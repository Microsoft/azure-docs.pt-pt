---
title: 'Quickstart: Criar uma máquina virtual de ciência de dados do Windows'
titleSuffix: Azure Data Science Virtual Machine
description: Configure e crie uma máquina virtual de ciência de dados em Azure para análise e machine learning.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 12/31/2019
ms.openlocfilehash: b36e6ce09cd4356a9892a5be9cbe7046cf7fd40e
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97094561"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-windows"></a>Quickstart: Configurar a máquina virtual da Ciência de Dados para windows

Levante-se e em funcionamento com uma máquina virtual de ciência de dados do Windows Server 2019.

## <a name="prerequisite"></a>Pré-requisito

Para criar uma Máquina Virtual da Ciência dos Dados do Windows, tem de ter uma subscrição do Azure. [Experimente Azure de graça.](https://azure.com/free)
Tenha em atenção que as contas gratuitas do Azure não suportam SKUs de máquina virtual preparadas para GPU.

## <a name="create-your-dsvm"></a>Crie o seu DSVM

Para criar um exemplo DSVM:

1. Vá ao [portal Azure](https://portal.azure.com) Poderá ser solicitado a inscrever-se na sua conta Azure se ainda não tiver assinado.
1. Encontre a listagem de máquinas virtuais digitando em "data science virtual machine" e selecionando "Data Science Virtual Machine - Windows 2019".

1. Selecione o botão **Criar** na parte inferior.

1. Deve ser redirecionado para a lâmina "Criar uma máquina virtual".

1. Preencha o separador **Básicos:**
      * **Subscrição**: Se tiver mais de uma subscrição, selecione aquela em que a máquina será criada e faturada. Tem de ter privilégios de criação de recursos para esta subscrição.
      * **Grupo de recursos**: Criar um novo grupo ou utilizar um existente.
      * **Nome da máquina virtual**: Introduza o nome da máquina virtual. É assim que vai aparecer no seu portal Azure.
      * **Localização**: Selecione o datacenter que é mais apropriado. Para um acesso mais rápido à rede, é o datacenter que tem a maioria dos seus dados ou está mais próximo da sua localização física. Saiba mais sobre [as Regiões Azure.](https://azure.microsoft.com/global-infrastructure/regions/)
      * **Imagem**: Deixe o valor predefinido.
      * **Tamanho**: Isto deve povoar automaticamente com um tamanho adequado para cargas de trabalho gerais. Leia mais sobre [os tamanhos do Windows VM em Azure](../../virtual-machines/sizes.md).
      * **Nome de utilizador**: Introduza o nome de utilizador do administrador. Este é o nome de utilizador que irá utilizar para iniciar sessão na sua máquina virtual, e não precisa de ser o mesmo que o seu nome de utilizador Azure.
      * **Palavra-passe**: Introduza a palavra-passe que utilizará para iniciar sessão na sua máquina virtual.    
1. Selecione **Rever + criar**.
1. **Review+criar**
   * Verifique se toda a informação que introduziu está correta. 
   * Selecione **Criar**.


> [!NOTE]
> * Não paga taxas de licenciamento pelo software que vem pré-carregado na máquina virtual. Paga o custo de cálculo pelo tamanho do servidor que escolheu no passo **Tamanho.**
> * O provisionamento leva de 10 a 20 minutos. Pode ver o estado do seu VM no portal Azure.

## <a name="access-the-dsvm"></a>Aceda ao DSVM

Depois de o VM ser criado e provisionado, siga os passos listados para [ligar à sua máquina virtual baseada em Azure.](../../marketplace/azure-vm-create-using-approved-base.md) Use as credenciais de conta de administração que configuraste no passo **Básico** de criar uma máquina virtual. 

Está pronto para começar a usar as ferramentas instaladas e configuradas no VM. Muitas das ferramentas podem ser acedidas através de telhas de menu **Iniciar** e ícones de ambiente de trabalho.

Também pode anexar um DSVM a Azure Notebooks para executar os cadernos Jupyter no VM e contornar as limitações do nível de serviço gratuito. Para mais informações, consulte [Gerir e configurar projetos de Cadernos.](../../notebooks/configure-manage-azure-notebooks-projects.md)

<a name="tools"></a>


## <a name="next-steps"></a>Passos seguintes

* Explore as ferramentas no DSVM abrindo o menu **Iniciar.**
* Saiba mais sobre o Azure Machine Learning lendo O [](../index.yml) [que é Azure Machine Learning?](../overview-what-is-azure-ml.md)
* Leia o artigo [Data Science com uma máquina virtual de ciência de dados do Windows em Azure](./vm-do-ten-things.md)
