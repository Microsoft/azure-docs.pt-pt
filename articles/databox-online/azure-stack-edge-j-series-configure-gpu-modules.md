---
title: Executar um módulo GPU no dispositivo GPU do Microsoft Azure Stack Edge Pro/ Microsoft Docs
description: Descreve como configurar e executar um módulo na GPU num dispositivo Azure Stack Edge Pro através do portal Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/25/2020
ms.author: alkohli
ms.openlocfilehash: 8b9f1180639f638e72fdea2f87958628a2e9e86b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90891466"
---
# <a name="configure-and-run-a-module-on-gpu-on-azure-stack-edge-pro-device"></a>Configure e execute um módulo em GPU no dispositivo Azure Stack Edge Pro

O seu dispositivo Azure Stack Edge Pro contém uma ou mais Unidade de Processamento de Gráficos (GPU). As GPUs são uma escolha popular para computações de IA, uma vez que oferecem capacidades de processamento paralelos e são mais rápidas na renderização de imagem do que as Unidades Centrais de Processamento (CPUs). Para obter mais informações sobre a GPU contida no seu dispositivo Azure Stack Edge Pro, aceda às especificações técnicas do [dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-technical-specifications-compliance.md).

Este artigo descreve como configurar e executar um módulo na GPU no seu dispositivo Azure Stack Edge Pro. Neste artigo, utilizará um módulo de contentores publicamente disponível **Digits** escrito para GPUs Nvidia T4. Este procedimento pode ser usado para configurar quaisquer outros módulos publicados pela Nvidia para estas GPUs.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Tem acesso a um dispositivo GPU ativado por 1 nó Azure Stack Edge Pro. Este dispositivo é ativado com um recurso em Azure.  

## <a name="configure-module-to-use-gpu"></a>Módulo de configuração para utilizar GPU

Para configurar um módulo para utilizar a GPU no seu dispositivo Azure Stack Edge Pro para executar um módulo, siga estes passos.

1. No portal Azure, aceda ao recurso associado ao seu dispositivo. 

2. Vá ao **Edge compute > Começar**. No azulejo **computacional Configure Edge,** selecione Configure.

    ![Módulo de configuração para utilizar GPU 1](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-1.png)

3. Na lâmina de **computação Configure Edge:**

    1. Para **IoT Hub,** escolha **Criar novo**.
    2. Forneça um nome para o recurso IoT Hub que pretende criar para o seu dispositivo. Para utilizar um nível livre, selecione um recurso existente. 
    3. Tome nota do dispositivo IoT Edge e do dispositivo IoT Gateway que são criados com o recurso IoT Hub. Utilizará esta informação nos passos posteriores.

    ![Módulo de configuração para utilizar GPU 2](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-2.png)

4. Leva vários minutos para criar o recurso IoT Hub. Após a criação do recurso, no azulejo **computacional Configure Edge,** selecione **Ver config** para ver os detalhes do recurso IoT Hub.

    ![Módulo de configuração para utilizar GPU 4](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-4.png)

5. Aceda à **gestão automática de dispositivos > IoT Edge**.

    ![Módulo de configuração para utilizar GPU 6](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-2.png)

    No painel direito, vê o dispositivo IoT Edge associado ao seu dispositivo Azure Stack Edge Pro. Isto corresponde ao dispositivo IoT Edge que criou no passo anterior ao criar o recurso IoT Hub. 
    
6. Selecione este dispositivo IoT Edge.

   ![Módulo de configuração para utilizar GPU 7](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-3.png)

7.  Selecione **Definir módulos**.

    ![Módulo de configuração para utilizar GPU 8](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-4.png)

8. **Selecione + Adicione** e, em seguida, selecione + módulo **IoT Edge**. 

    ![Módulo de configuração para utilizar GPU 9](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-5.png)

9. No separador **Módulo de Borda IoT:**

    1. Forneça o **URI de imagem.** Você usará o módulo Nvidia publicamente disponível **Digits** aqui. 
    
    2. Desace **a política de reiniciar** para **sempre**.
    
    3. Definir **o estado desejado** para **executar**.
    
    ![Módulo de configuração para utilizar GPU 10](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-6.png)

10. No separador **de variáveis Ambiente,** forneça o Nome da variável e o valor correspondente. 

    1. Para que o módulo atual utilize uma GPU neste dispositivo, utilize o NVIDIA_VISIBLE_DEVICES. 

    2. Desa um valor para 0 ou 1. Isto garante que, pelo menos, uma GPU é utilizada pelo dispositivo para este módulo. Quando define o valor para 0, 1, isso implica que ambas as GPUs do seu dispositivo estão a ser utilizadas por este módulo.

        ![Módulo de configuração para utilizar GPU 11](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-7.png)

        Para obter mais informações sobre variáveis ambientais que você pode usar com o GPU da Nvidia, vá para o [tempo de funcionação do contentor nVidia](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).

    > [!NOTE]
    > Uma GPU só pode ser mapeada para um módulo. No entanto, um módulo pode usar um, ambos ou nenhum GPUs. 

11. Insira um nome para o seu módulo. Neste ponto pode optar por fornecer ao recipiente criar opção e modificar as definições gémeas do módulo ou, se for feito, selecione **Adicionar**. 

    ![Módulo de configuração para utilizar GPU 12](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-8.png)

12. Certifique-se de que o módulo está em execução e selecione **Review + Create**.    

    ![Módulo de configuração para utilizar GPU 13](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-9.png)

13. No **separador 'Rever + Criar',** as opções de implantação selecionadas são apresentadas. Reveja as opções e **selecione Criar**.
    
    ![Módulo de configuração para utilizar GPU 14](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-10.png)

14. Tome nota do estado de **funcionação** do módulo. 
    
    ![Módulo de configuração para utilizar GPU 15](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-11.png)

    Leva alguns minutos para o módulo ser implantado. Selecione **Refresh** e deverá ver a atualização do **estado de funcionamento** para **executar**.

    ![Módulo de configuração para utilizar GPU 16](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-12.png)


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [as variáveis ambientais que pode utilizar com o GPU da Nvidia.](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec)
