---
title: 'Início rápido: Configurar uma inteligência artificial geográfica'
titleSuffix: Azure Data Science Virtual Machine
description: Saiba como criar e configurar a máquina de Virtual de ciência de dados de IA geográfica. Máquina de Virtual de ciência de dados de IA geográfica fornece as ferramentas para criar soluções de IA e ML com dados geográficos.
keywords: IA, ferramentas de ciência de dados, a máquina de virtual de ciência de dados, a análise Geoespacial, aprendizagem aprofundada
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: quickstart
ms.date: 03/05/2018
ms.openlocfilehash: 7afeec3f71cd1af30093801fedabf3f0357ae3d0
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208056"
---
# <a name="quickstart-set-up-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Início rápido: Configurar uma máquina virtual de inteligência artificial geográfica no Azure 

O Máquina Virtual de Ciência de Dados de ia geográfico (Geo-DSVM) é uma extensão da popular [máquina virtual de ciência de dados do Azure](https://aka.ms/dsvm) que é especialmente configurada para combinar ia e análise geoespacial. A análise geoespacial na VM têm a tecnologia [ArcGIS Pro](https://www.arcgis.com/features/index.html). O Máquina Virtual de Ciência de Dados (DSVM) permite o treinamento rápido de aprendizado de máquina e até mesmo modelos de aprendizado profundo. Para desenvolver esses modelos, ele usa dados que são aprimorados com informações geográficas. O DSVM de área geográfica tem suporte apenas no Windows 2016 DSVM. 

As ferramentas de ia incluídas no DSVM geográfico incluem o seguinte:

- Edições de GPU de estruturas populares de aprendizado profundo como Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2 e Chainer
- Ferramentas para adquirir e pré-processar dados de imagem e textuais
- Ferramentas para atividades de desenvolvimento, como Microsoft Machine Learning Server Developer Edition, Anaconda Python, notebooks Jupyter para Python e R, IDEs para Python e R e bancos de dados SQL
- Software de desktop ArcGIS pro da ESRI, juntamente com as interfaces Python e R que podem trabalhar com os dados geoespaciais de seus aplicativos de ia
 

## <a name="create-your-geo-ai-data-science-vm"></a>Criar a sua VM de ciência dados de IA geográfica

Para criar uma instância do VM de Ciência de Dados de ia geográfico, siga estas etapas:

1. Vá para a lista de máquinas virtuais no [portal do Azure](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows).
1. Selecione **criar** na parte inferior para gerar um assistente:

   ![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)

1. O assistente requer entrada para cada uma das quatro etapas. Para obter informações detalhadas sobre essa entrada, consulte a seção a seguir.

### <a name="wizard-details"></a>Detalhes do assistente ###

**Noções básicas**:

- **Nome**: O nome do servidor de ciência de dados que está a criar.
    
- **Nome de usuário**: ID de entrada da conta do administrador.
    
- **Senha**: Senha da conta do administrador.
    
- **Subscrição**: Se tiver mais de uma subscrição, selecione aquele no qual a máquina está a ser criado e faturadas.
    
- **Grupo de recursos**: Você pode criar um novo ou usar um grupo de recursos existente do Azure **vazio** em sua assinatura.
    
- **Local**: Selecione o data center mais apropriado. Normalmente, é o que tem a maioria de seus dados ou mais próximo do seu local físico para acesso mais rápido à rede. Se você planeja executar o aprendizado profundo em uma GPU, deverá escolher um dos locais no Azure que tenha instâncias de VM da GPU da série NC. Atualmente, esses locais são: **Leste dos EUA, norte EUA Central, sul EUA Central, oeste dos EUA 2, Europa Setentrional Europa Ocidental**. Para obter a lista mais recente, marque a página [produtos do Azure por região](https://azure.microsoft.com/regions/services/) e procure por **NC-Series** em **computação**. 
    
    
**Definições**: Selecione um dos tamanhos de máquina virtual da GPU da série NC se você planeja executar o aprendizado profundo em uma GPU em seu DSVM geográfico. Caso contrário, você pode escolher uma das instâncias baseadas em CPU. Crie uma conta de armazenamento para a sua VM. 
       
**Resumo**: Certifique-se de que todas as informações que introduziu estão corretas.
    
**Comprar**: Para iniciar o processo de provisionamento, clique em **comprar**. É fornecida uma ligação para os termos do serviço. A VM não tem encargos adicionais além dos encargos de computação para o tamanho do servidor escolhido na etapa **tamanho** . 
 
 >[!NOTE]
 > O provisionamento deve levar cerca de 20 a 30 minutos. O estado do provisionamento é apresentado no portal do Azure.

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Como aceder a máquina de Virtual de ciência de dados de IA geográfica

 Depois que a VM for criada, você estará pronto para começar a usar as ferramentas que estão instaladas e pré-configuradas nela. Há blocos do menu iniciar e ícones da área de trabalho para muitas das ferramentas. Você pode acessar a VM pela área de trabalho remota usando as credenciais da conta de administrador que você configurou na seção **noções básicas** .

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>Utilizar o ArcGIS Pro instalado na VM

Na área geográfica DSVM, o ArcGIS pro desktop é pré-instalado e o ambiente é pré-configurado para funcionar com todas as ferramentas no DSVM. Ao iniciar o ArcGIS, você será solicitado a fornecer credenciais para sua conta do ArcGIS. Se já tiver uma conta do ArcGIS e tiver licenças do software, pode utilizar as suas credenciais existentes.  

![Arc-GIS-Logon](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

Caso contrário, você pode se inscrever para uma nova conta e licença do ArcGIS ou obter uma [avaliação gratuita](https://www.arcgis.com/features/free-trial.html). 

![O ArcGIS-Free-Trial](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

Depois de se inscrever para uma conta padrão do ArcGIS ou uma avaliação gratuita, você pode autorizar o ArcGIS pro para sua conta seguindo as instruções em [introdução ao ArcGIS pro](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf).

Depois de entrar na área de trabalho do ArcGIS pro por meio de sua conta do ArcGIS, você estará pronto para começar a usar as ferramentas de ciência de dados que estão instaladas e configuradas na VM para seus projetos de análise geoespacial e aprendizado de máquina.

## <a name="next-steps"></a>Passos Seguintes

Comece a usar o VM de Ciência de Dados de ia geográfico com diretrizes do seguinte recurso:

* [Utilizar a VM de ciência dados de IA geográfica](use-geo-ai-dsvm.md)
