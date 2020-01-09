---
title: 'Início rápido: criar um DSVM do Windows'
description: Configurar e criar uma máquina de Virtual de ciência de dados no Azure para análise e machine learning.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 12/31/2019
ms.openlocfilehash: f498c2cd6906d1966ab5dfd1f7547686163fe2ce
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75611922"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-windows"></a>Início rápido: configurar o Máquina Virtual de Ciência de Dados para Windows

Comece a executar com um Máquina Virtual de Ciência de Dados do Windows Server 2019.

## <a name="prerequisite"></a>Pré-requisito

Para criar um Máquina Virtual de Ciência de Dados do Windows, você deve ter uma assinatura do Azure. [Experimente o Azure gratuitamente](https://azure.com/free).
Observe que as contas gratuitas do Azure não dão suporte a SKUs de máquina virtual habilitadas para GPU.

## <a name="create-your-dsvm"></a>Criar seu DSVM

Para criar uma instância de DSVM:

1. Vá para a [portal do Azure](https://portal.azure.com) você pode ser solicitado a entrar em sua conta do Azure, se você ainda não tiver entrado.
1. Localize a listagem de máquinas virtuais digitando "máquina virtual de ciência de dados" e selecionando "Máquina Virtual de Ciência de Dados-Win 2019 (versão prévia)".

1. Selecione o botão **criar** na parte inferior.

1. Você deve ser redirecionado para a folha "criar uma máquina virtual".

1. Preencha a guia **noções básicas** :
      * **Assinatura**: se você tiver mais de uma assinatura, selecione aquela em que a máquina será criada e cobrada. Tem de ter privilégios de criação de recursos para esta subscrição.
      * **Grupo de recursos**: Crie um novo grupo ou use um existente.
      * **Nome da máquina virtual**: Insira o nome da máquina virtual. É assim que ela aparecerá na sua portal do Azure.
      * **Local**: selecione o datacenter mais apropriado. Para acesso mais rápido à rede, é o datacenter que tem a maior parte dos seus dados ou está mais próximo de sua localização física. Saiba mais sobre [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/).
      * **Imagem**: Deixe o valor padrão.
      * **Tamanho**: isso deve ser preenchido automaticamente com um tamanho apropriado para cargas de trabalho gerais. Leia mais sobre os [tamanhos de VM do Windows no Azure](../../virtual-machines/windows/sizes.md).
      * **Nome de usuário**: Insira o nome de usuário do administrador. Esse é o nome de usuário que você usará para fazer logon em sua máquina virtual e não precisa ser o mesmo que o seu nome de usuário do Azure.
      * **Senha**: Insira a senha que você usará para fazer logon em sua máquina virtual.    
1. Selecione **Rever + criar**.
1. **Examinar + criar**
   * Certifique-se de que todas as informações que introduziu estão corretas. 
   * Selecione **Criar**.


> [!NOTE]
> * Você não paga taxas de licenciamento para o software que vem pré-carregado na máquina virtual. Você paga o custo de computação para o tamanho do servidor que você escolheu na etapa **tamanho** .
> * O provisionamento leva de 10 a 20 minutos. Você pode exibir o status de sua VM no portal do Azure.

## <a name="access-the-dsvm"></a>Acessar o DSVM

Depois que a VM for criada e provisionada, siga as etapas listadas para [conectar-se à sua máquina virtual baseada no Azure](../../marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm.md). Use as credenciais da conta de administrador que você configurou na etapa **básico** da criação de uma máquina virtual. 

Está pronto para começar a utilizar as ferramentas que são instaladas e configuradas na VM. Muitas das ferramentas podem ser acessadas por meio de blocos do menu **Iniciar** e ícones da área de trabalho.

Você também pode anexar um DSVM ao Azure Notebooks para executar blocos de anotações do Jupyter na VM e ignorar as limitações da camada de serviço gratuita. Para obter mais informações, consulte [gerenciar e configurar projetos de blocos de anotações](../../notebooks/configure-manage-azure-notebooks-projects.md#manage-and-configure-projects).

<a name="tools"></a>


## <a name="next-steps"></a>Passos seguintes

* Explore as ferramentas no DSVM abrindo o menu **Iniciar** .
* Saiba mais sobre o Azure Machine Learning lendo [o que é Azure Machine Learning?](../overview-what-is-azure-ml.md) e experimentando [tutoriais](../index.yml).
* Leia o artigo [dez coisas que você pode fazer no máquina virtual de ciência de dados](https://aka.ms/dsvmtenthings).
* Visite o [Galeria de IA do Azure](https://gallery.cortanaintelligence.com) para machine learning e os dados analytics de exemplo que utilizam o Azure Machine Learning e os dados relacionados de serviços no Azure. Também fornecemos um ícone para essa galeria no menu **Iniciar** e na área de trabalho da máquina virtual.

