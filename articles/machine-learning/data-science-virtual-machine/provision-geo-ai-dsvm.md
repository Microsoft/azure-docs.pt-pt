---
title: 'Quickstart: Criar uma máquina virtual geo-AI data science'
titleSuffix: Azure Data Science Virtual Machine
description: Configure e crie uma Máquina Virtual geo-AI data Science em Azure para análise geoespacial e machine learning.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/13/2019
ms.openlocfilehash: f3ff9bd64f54d8f83fd1889078e8a4c01827d135
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77525894"
---
# <a name="quickstart-set-up-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Quickstart: Criar uma máquina virtual de inteligência geoartificial em Azure 

A Máquina Virtual geo-AI Data Science (Geo-DSVM) é uma extensão da popular Máquina Virtual de Ciência de [Dados Azure](https://aka.ms/dsvm) que está especialmente configurada para combinar IA e análise geoespacial. As análises geoespaciais no VM são alimentadas pelo [ArcGIS Pro](https://www.arcgis.com/features/index.html). A Máquina Virtual da Ciência dos Dados (DSVM) permite a formação rápida de modelos de aprendizagem automática e até de aprendizagem profunda. Para desenvolver estes modelos, utiliza dados que são enriquecidos com informação geográfica. O Geo-DSVM é suportado apenas no Windows 2016 DSVM. 

As ferramentas de IA incluídas no Geo-DSVM incluem o seguinte:

- Edições GPU de estruturas populares de deep learning como Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2 e Chainer
- Ferramentas para adquirir e pré-processar dados de imagem e textual
- Ferramentas para atividades de desenvolvimento como Microsoft Machine Learning Server Developer Edition, Anaconda Python, cadernos Jupyter para Python e R, IDEs para Python e R, e bases de dados SQL
- Software de desktop ArcGIS Pro da ESRI, juntamente com interfaces Python e R que podem trabalhar com os dados geoespaciais das suas aplicações de IA
 

## <a name="create-your-geo-ai-data-science-vm"></a>Crie o seu VM geo-AI Data Science

Para criar uma instância do VM geo-AI Data Science, siga estes passos:

1. Vá à listagem de máquinas virtuais no [portal Azure.](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows)
1. Selecione **Criar** na parte inferior para gerar um assistente:

   ![criar-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)

1. O assistente requer entrada para cada um dos quatro passos. Para obter informações detalhadas sobre esta entrada, consulte a secção seguinte.

### <a name="wizard-details"></a>Detalhes do assistente ###

**Bases:**

- **Nome**: O nome do servidor de ciência de dados que está a criar.
    
- **Nome do utilizador**: Id de intenção de conta Deadministrador.
    
- **Palavra-passe**: Palavra-passe da conta de administrador.
    
- **Subscrição**: Se tiver mais de uma subscrição, selecione aquela em que a máquina será criada e faturada.
    
- **Grupo de recursos:** Pode criar um novo ou utilizar **um** grupo de recursos Azure existente vazio na sua subscrição.
    
- **Localização**: Selecione o centro de dados mais adequado. Normalmente, é aquele que tem a maioria dos seus dados ou que está mais próximo da sua localização física para o acesso mais rápido à rede. Se planeia fazer uma aprendizagem profunda numa GPU, tem de escolher um dos locais em Azure que tenha casos de VM GPU da Série NC. Atualmente, esses locais são: **Leste dos EUA, Norte Dos EUA, Centro-Sul dos EUA, Oeste DOS 2, Norte da Europa, Europa Ocidental.** Para obter a lista mais recente, consulte a página [dos Produtos Azure por Região](https://azure.microsoft.com/regions/services/) e procure a **NC-Series** no **âmbito da Compute**. 
    
    
**Definições**: Selecione um dos tamanhos da máquina virtual GPU da Série NC se planeia fazer uma aprendizagem profunda numa GPU no seu Geo DSVM. Caso contrário, pode escolher uma das instâncias baseadas em CPU. Crie uma conta de armazenamento para o seu VM. 
       
**Resumo**: Verifique se todas as informações que inseriu estão corretas.
    
**Comprar**: Para iniciar o processo de provisionamento, clique em **Comprar**. É fornecido um link para os termos do serviço. O VM não tem quaisquer encargos adicionais para além das cargas de cálculo para o tamanho do servidor que escolheu no passo **tamanho.** 
 
 >[!NOTE]
 > O provisionamento deve demorar cerca de 20 a 30 minutos. O estado do provisionamento é apresentado no portal Azure.

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Como aceder à Máquina Virtual geo-AI Data Science

 Depois de criado o seu VM, está pronto para começar a utilizar as ferramentas instaladas e pré-configuradas. Existem azulejos de menu Iniciar e ícones de ambiente de trabalho para muitas das ferramentas. Pode aceder ao VM por ambiente de trabalho remoto utilizando as credenciais de conta Admin que configurana na secção **Basics.**

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>Utilização do ArcGIS Pro instalado no VM

No Geo-DSVM, o ambiente de trabalho ArcGIS Pro é pré-instalado e o ambiente é reconfigurado para funcionar com todas as ferramentas do DSVM. Quando iniciar o ArcGIS, é solicitado credenciais para a sua conta ArcGIS. Se já tem uma conta ArcGIS e tem licenças para o software, pode utilizar as suas credenciais existentes.  

![Arc-GIS-Logon](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

Caso contrário, pode inscrever-se para uma nova conta ArcGIS e licença, ou obter um [teste gratuito.](https://www.arcgis.com/features/free-trial.html) 

![ArcGIS-Free-Trial](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

Depois de se inscrever numa conta ArcGIS padrão ou num teste gratuito, pode autorizar o ArcGIS Pro para a sua conta seguindo as instruções de [Início com ArcGIS Pro](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf).

Depois de iniciar sessão no ambiente de trabalho Do ArcGIS Pro através da sua conta ArcGIS, está pronto para começar a utilizar as ferramentas de ciência de dados que estão instaladas e configuradas no VM para os seus projetos de análise geoespacial e de machine-learning.

## <a name="next-steps"></a>Passos seguintes

Comece a utilizar o VM geo-AI data science com orientação do seguinte recurso:

* [Utilize o Geo AI Data Science VM](use-geo-ai-dsvm.md)
