---
title: Aprovisionar uma máquina de Virtual de Inteligência Artificial Georreplicação no Azure – Azure | Documentos da Microsoft
description: Saiba como criar e configurar a máquina de Virtual de ciência de dados de IA geográfica. Máquina de Virtual de ciência de dados de IA geográfica fornece as ferramentas para criar soluções de IA e ML com dados geográficos.
keywords: IA, ferramentas de ciência de dados, a máquina de virtual de ciência de dados, a análise Geoespacial, aprendizagem aprofundada
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/05/2018
ms.author: vijetaj
ms.openlocfilehash: 4772bf8341196485a91b3df30801b9714a4a64a8
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68591869"
---
# <a name="provision-a-geo-artificial-intelligence-virtual-machine-on-azure"></a>Aprovisionar uma máquina de Virtual de Inteligência Artificial Georreplicação no Azure 

Máquina de Virtual de ciência de dados de IA geográfica (Geo-DSVM) é uma extensão das populares [máquina de Virtual de ciência de dados do Azure](https://aka.ms/dsvm) especialmente configurado para combinar a análise de IA e geoespaciais. A análise geoespacial na VM têm a tecnologia [ArcGIS Pro](https://www.arcgis.com/features/index.html). A VM de ciência de dados permite que o treinamento rápido da máquina, modelos de aprendizagem e até mesmo de deep learning modelos, nos dados que são enriquecidos com informações geográficas. É suportada apenas no Windows 2016 DSVM. 

A Geo-DSVM contém várias ferramentas para IA, incluindo:

- Edições de GPU de estruturas de aprendizagem profunda populares, como Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2, Chainer; 
- ferramentas para adquirir e pré-processar imagem, os dados textuais, 
- ferramentas para atividades de desenvolvimento, como o Microsoft R Server Developer Edition, Anaconda Python, blocos de notas do Jupyter para o Python e R, IDEs Python e R, SQL, bases de dados
- Software de hora desktop do ArcGIS Pro da ESRI, juntamente com o Python e R interfaces que pode trabalhar com os dados geoespaciais das suas aplicações de IA. 
 

## <a name="create-your-geo-ai-data-science-vm"></a>Criar a sua VM de ciência dados de IA geográfica

Este é o procedimento para criar uma instância da VM de ciência de dados de IA geográfica: 


1. Navegue para a máquina virtual listagem no [portal do Azure](https://ms.portal.azure.com/#create/microsoft-ads.geodsvmwindows).
2. Selecione o **criar** na parte inferior para ser levados num assistente.
![create-geo-ai-dsvm](./media/provision-geo-ai-dsvm/Create-Geo-AI.png)
3. O assistente utilizado para criar a Geo-DSVM requer **entradas** para cada um da **quatro passos** enumerado à direita da figura. Seguem-se as entradas necessárias para configurar cada um destes passos:



   - **Noções básicas**

      1. **Nome**: Nome do servidor de ciência de dados que você está criando.

      2. **Nome de usuário**: ID de logon da conta do administrador.

      3. **Senha**: Senha da conta do administrador.

      4. **Assinatura**: Se tiver mais de uma subscrição, selecione aquele no qual a máquina está a ser criado e faturadas.

      5. **Grupo de recursos**: Você pode criar um novo ou usar um grupo de recursos existente do Azure **vazio** em sua assinatura.

      6. **Local**: Selecione o data center mais apropriado. Normalmente, é o Centro de dados que tem a maior parte dos seus dados ou mais se aproxima-se à sua localização física para acesso de rede mais rápido. Se precisar de fazer aprendizagem aprofundada em GPU, tem de escolher uma das localizações no Azure com as instâncias de VM de GPU da série NC. Atualmente, os locais que têm VMs de GPU são: **Leste dos EUA, norte EUA Central, sul EUA Central, oeste dos EUA 2, Europa Setentrional Europa Ocidental**. Para a lista mais recente, consulte a [produtos do Azure pela página de região](https://azure.microsoft.com/regions/services/) e procure **série NC** sob **computação**. 


   - **Definições**: Selecione um tamanho de máquina virtual da GPU da série NC se você planeja executar o aprendizado profundo na GPU em seu DSVM geográfico. Caso contrário, pode escolher um da CPU com a base de instância.  Crie uma conta de armazenamento para a sua VM. 
   
   - **Resumo**: Certifique-se de que todas as informações que introduziu estão corretas.

   - **Comprar**: Clique em **comprar** para iniciar o provisionamento. É fornecida uma ligação para os termos do serviço. A VM não tem quaisquer custos adicionais para além de computação para o tamanho de servidor que selecionou no **tamanho** passo. 
 
>[!NOTE]
> O aprovisionamento deve demorar cerca de 20 a 30 minutos. O estado do provisionamento é apresentado no portal do Azure.

 
## <a name="how-to-access-the-geo-ai-data-science-virtual-machine"></a>Como aceder a máquina de Virtual de ciência de dados de IA geográfica

 Assim que a VM é criada, está pronto para começar a utilizar as ferramentas que são instaladas e pré-configuradas no mesmo. Existem mosaicos do menu Iniciar e ícones para muitas das ferramentas. É possível o ambiente de trabalho remoto nela, usando as credenciais da conta de administrador que configurou no anterior **Noções básicas** secção. 

 
## <a name="using-arcgis-pro-installed-in-the-vm"></a>Utilizar o ArcGIS Pro instalado na VM

A Geo-DSVM já tem o ambiente de trabalho de ArcGIS Pro previamente instalado e o ambiente de pré-configurado para trabalhar com todas as ferramentas a DSVM. Quando inicia o ArcGIS ele pede-lhe um início de sessão para a sua conta do ArcGIS. Se já tiver uma conta do ArcGIS e tiver licenças do software, pode utilizar as suas credenciais existentes.  

![Arc-GIS-Logon](./media/provision-geo-ai-dsvm/ArcGISLogon.png)

Caso contrário, pode inscrever-se para a nova conta do ArcGIS e licença ou obter um [avaliação gratuita](https://www.arcgis.com/features/free-trial.html). 

![O ArcGIS-Free-Trial](./media/provision-geo-ai-dsvm/ArcGIS-Free-Trial.png)

Depois de se inscrever para uma conta de avaliação paga ou gratuita do ArcGIS, você pode autorizar o ArcGIS pro para sua conta seguindo as instruções em [introdução com ArcGIS pro](https://www.esri.com/library/brochures/getting-started-with-arcgis-pro.pdf). 

Depois de iniciar sessão com a sua conta do ArcGIS ambiente de trabalho do ArcGIS Pro, está pronto para começar a utilizar as ferramentas de ciência de dados que são instaladas e configuradas na VM para a sua análise Geoespacial e projetos de aprendizagem automática.

## <a name="next-steps"></a>Passos Seguintes

Começar a utilizar a VM de ciência de dados de IA Geo com orientação dos seguintes tópicos:

* [Utilizar a VM de ciência dados de IA geográfica](use-geo-ai-dsvm.md)
