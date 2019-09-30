---
title: 'Início rápido: Criar uma DSVM do Windows'
description: Configurar e criar uma máquina de Virtual de ciência de dados no Azure para análise e machine learning.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/10/2019
ms.openlocfilehash: fcd115b672e4e2677cb7ad48fc94905747d66781
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71675116"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-windows"></a>Início rápido: Configurar o Máquina Virtual de Ciência de Dados para Windows

Comece a executar com um Máquina Virtual de Ciência de Dados do Windows.

## <a name="prerequisite"></a>Pré-requisito

Para criar um Máquina Virtual de Ciência de Dados do Windows, você deve ter uma assinatura do Azure. [Experimente o Azure gratuitamente](https://azure.com/free).
Observe que as contas gratuitas do Azure não dão suporte a SKUs de máquina virtual habilitadas para GPU.

## <a name="create-your-dsvm"></a>Criar seu DSVM

Para criar uma instância de DSVM:

1. Vá para a [portal do Azure](https://portal.azure.com) você pode ser solicitado a entrar em sua conta do Azure, se você ainda não tiver entrado.
1. Localize a listagem de máquinas virtuais digitando "máquina virtual de ciência de dados" e selecionando "Máquina Virtual de Ciência de Dados-Windows 2016".

    ![Listagem de VM do Windows](./media/provision-vm/search-windows.png)

1. Selecione o botão **criar** na parte inferior.

    [![](media/provision-vm/create-windows.png "Botão para criar um computador Windows")](media/provision-vm/create-windows-expanded.png#lightbox)

1. Você deve ser redirecionado para a folha "criar uma máquina virtual".
   ![Guia básico correspondente à máquina virtual do Windows](./media/provision-vm/review-create-windows.png)

1. Preencha a guia **noções básicas** :
      * **Subscrição**: Se você tiver mais de uma assinatura, selecione aquela em que a máquina será criada e cobrada. Tem de ter privilégios de criação de recursos para esta subscrição.
      * **Grupo de recursos**: Crie um novo grupo ou use um existente.
      * **Nome da máquina virtual**: Insira o nome da máquina virtual. É assim que ela aparecerá na sua portal do Azure.
      * **Local**: Selecione o datacenter mais apropriado. Para acesso mais rápido à rede, é o datacenter que tem a maior parte dos seus dados ou está mais próximo de sua localização física. Saiba mais sobre [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/).
      * **Imagem**: Deixe o valor predefinido.
      * **Tamanho**: Isso deve ser preenchido automaticamente com um tamanho apropriado para cargas de trabalho gerais. Leia mais sobre os [tamanhos de VM do Windows no Azure](../../virtual-machines/windows/sizes.md).
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
* Saiba mais sobre o serviço de Azure Machine Learning lendo [o que é o serviço de Azure Machine Learning?](../service/overview-what-is-azure-ml.md) e experimentando [tutoriais](../index.yml).
* No explorador de arquivos, navegue até C:\Program Programas\microsoft\ml Server\R_SERVER\library\RevoScaleR\demoScripts para obter exemplos que usam a biblioteca RevoScaleR em R que dá suporte à análise de dados em escala empresarial. 
* Leia o artigo [dez coisas que você pode fazer no máquina virtual de ciência de dados](https://aka.ms/dsvmtenthings).
* Aprenda a criar soluções de análise de ponto-a-ponto sistematicamente utilizando o [Team Data Science Process](../team-data-science-process/index.yml).
* Visite o [Galeria de IA do Azure](https://gallery.cortanaintelligence.com) para machine learning e os dados analytics de exemplo que utilizam o Azure Machine Learning e os dados relacionados de serviços no Azure. Também fornecemos um ícone para essa galeria no menu **Iniciar** e na área de trabalho da máquina virtual.
* Consulte a [documentação de referência](./reference-windows-vm.md) apropriada para esta máquina virtual.

