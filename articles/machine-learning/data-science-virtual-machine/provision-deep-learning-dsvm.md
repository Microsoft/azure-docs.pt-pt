---
title: Criar uma máquina de Virtual de ciência de dados de aprendizagem profunda
titleSuffix: Azure
description: Configurar e criar uma profunda de aprendizagem máquina ciência de dados Virtual no Azure para análise e machine learning.
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: quickstart
ms.date: 03/16/2018
ms.author: vijetaj
ms.openlocfilehash: b9d0f9aead6e2cedd3ca0884273bac0106a925a0
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68591904"
---
# <a name="provision-a-deep-learning-virtual-machine-on-azure"></a>Aprovisionar uma Máquina Virtual no Azure de aprendizagem profunda 

O Deep Learning Virtual Machine (DLVM) é uma variante especialmente configurada das populares [máquina de Virtual de ciência de dados](https://aka.ms/dsvm) (DSVM) para que seja mais fácil de usar a VM baseada em GPU instâncias para modelos de aprendizagem profunda de formação rapidamente. É suportado com o Windows 2016 ou o DSVM em Ubuntu como base. O DLVM compartilha as mesmo imagens de VM de núcleo e, portanto, todo o conjunto de ferramentas avançado que está disponível no DSVM. 

O DLVM contém várias ferramentas de IA, inclusive edições de GPU de estruturas de aprendizagem profunda populares, como o Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2, Chainer; ferramentas para adquirir e pré-processar imagem, dados textuais, ferramentas de ciência modelação e desenvolvimento para atividades de dados, como o Microsoft R Server Developer Edition, Anaconda Python, blocos de notas do Jupyter para o Python e R, IDEs Python e R, SQL e muitos outros a bases de dados Ciência de dados e as ferramentas de ML. 

## <a name="create-your-deep-learning-virtual-machine"></a>Criar a sua máquina Virtual de aprendizagem profunda
Eis os passos para criar uma instância do Deep Learning Virtual Machine: 

1. Navegue para a máquina virtual listagem no [portal do Azure](https://portal.azure.com/#create/microsoft-ads.dsvm-deep-learningtoolkit
).
2. Selecione o **Create** na parte inferior para ser levados num assistente.![ dlvm criar](./media/dlvm-provision-wizard.PNG)
3. O assistente utilizado para criar o DLVM requer **entradas** para cada um da **quatro passos** enumerado à direita da figura. Seguem-se as entradas necessárias para configurar cada um destes passos:

   <a name="basics"></a>   
   1. **Noções básicas**
      
      1. **Nome**: Nome do servidor de ciência de dados que você está criando.
      2. **Selecione o tipo de so para a VM de aprendizado profundo**: Escolha Windows ou Linux (para Windows 2016 e Ubuntu Linux base DSVM)
      2. **Nome de usuário**: ID de logon da conta do administrador.
      3. **Senha**: Senha da conta do administrador.
      4. **Assinatura**: Se tiver mais de uma subscrição, selecione aquele no qual a máquina está a ser criado e faturadas.
      5. **Grupo de recursos**: Você pode criar um novo ou usar um grupo de recursos existente do Azure **vazio** em sua assinatura.
      6. **Local**: Selecione o data center mais apropriado. Normalmente, é o Centro de dados que tem a maior parte dos seus dados ou mais se aproxima-se à sua localização física para acesso de rede mais rápido. 
      
      > [!NOTE]
      > O DLVM suporta todos os NC e ND série instâncias de GPU VM. Quando o DLVM de aprovisionamento, tem de escolher uma das localizações no Azure com GPUs. Verifique os [produtos do Azure pela página de região](https://azure.microsoft.com/regions/services/) página para as localizações disponíveis e procure **série NC**, **série NCv2**, **série NCv3** , ou **série ND** sob **computação**. 

   1. **Definições**: Selecione um dos tamanhos de máquina virtual de GPU da série NC (NC, NCv2, NCv3) ou ND Series que atenda às suas necessidades funcionais e restrições de custo. Crie uma conta de armazenamento para a sua VM.  ![dlvm-settings](./media/dlvm-provision-step-2.PNG)
   
   1. **Resumo**: Certifique-se de que todas as informações que introduziu estão corretas.

   1. **Comprar**: Clique em **comprar** para iniciar o provisionamento. Receberá uma ligação para os termos da transação. A VM não tem quaisquer custos adicionais para além de computação para o tamanho de servidor que selecionou no **tamanho** passo. 

> [!NOTE]
> O aprovisionamento deve demorar cerca de 10 a 20 minutos. O estado do provisionamento é apresentado no portal do Azure.
> 


## <a name="how-to-access-the-deep-learning-virtual-machine"></a>Como acessar o Deep Learning Virtual Machine

### <a name="windows-edition"></a>Edição do Windows
Depois da VM é criada, o ambiente de trabalho remoto, pode nela, usando as credenciais da conta de administrador que configurou no anterior **Noções básicas** secção. 

### <a name="linux-edition"></a>Edição do Linux

Depois da VM é criada, pode iniciar sessão na mesma ao utilizar SSH. Use as credenciais de conta que você criou na seção [**noções básicas**](#basics) da etapa 3 para a interface do Shell de texto. Para obter mais informações sobre conexões SSH com VMs do Azure, consulte [instalar e configurar área de trabalho remota para se conectar a uma VM do Linux no Azure](../../virtual-machines/linux/use-remote-desktop.md). Em um cliente do Windows, você pode baixar uma ferramenta de cliente [SSH como saída](https://www.putty.org). Se preferir um ambiente de trabalho gráfico (sistema de Windows X), pode utilizar o Putty de reencaminhamento de X11 ou instalar o cliente de X2Go. 

> [!NOTE]
> O cliente de X2Go executada melhor do que no nosso teste de reencaminhamento de X11. Recomendamos que utilize o cliente de X2Go para uma interface gráfica de área de trabalho.
> 
> 

#### <a name="installing-and-configuring-x2go-client"></a>Instalar e configurar o cliente de X2Go
O DLVM Linux já está a ser aprovisionado com o servidor de X2Go e pronto para aceitar ligações de cliente. Para ligar-se na área de trabalho de gráfico de VM do Linux, execute o seguinte procedimento no cliente:

1. Transferir e instalar o cliente de X2Go para a sua plataforma de cliente do [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Executar o cliente de X2Go e selecione **nova sessão**. Ele abre uma janela de configuração com vários separadores. Introduza os seguintes parâmetros de configuração:
   * **Separador de sessão**:
     * **Host**: O nome do host ou endereço IP do seu VM de Ciência de Dados Linux.
     * **Logon**: Nome de usuário na VM Linux.
     * **Porta SSH**: Deixe em 22, o valor padrão.
     * **Tipo de sessão**: Altere o valor para **Xfce**. Atualmente a DSVM do Linux só suporta o ambiente de trabalho XFCE.
   * **Guia mídia**: Você pode desativar o suporte a som e a impressão do cliente se não precisar usá-los.
   * **Pastas**compartilhadas: Se você quiser diretórios de seus computadores cliente montados na VM Linux, adicione os diretórios de computador cliente que você deseja compartilhar com a VM nessa guia.

Depois de iniciar sessão na VM com o cliente SSH ou de uma área de trabalho gráfica do XFCE por meio do cliente de X2Go, está pronto para começar a utilizar as ferramentas que são instaladas e configuradas na VM. No XFCE, pode ver atalhos no menu aplicações e ícones para muitas das ferramentas.

Assim que a VM é criada e provisionada, está pronto para começar a utilizar as ferramentas de que estão instaladas e configuradas no mesmo. Existem mosaicos do menu Iniciar e ícones para muitas das ferramentas. 
