---
title: Executar um módulo GPU no dispositivo GPU do Microsoft Azure Stack Edge Pro| Microsoft Docs
description: Descreve como configurar e executar um módulo na GPU num dispositivo Azure Stack Edge Pro através do portal Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: 348ddff56ed61cd608d6b9f28417e7cd4c4e6b13
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563968"
---
# <a name="configure-and-run-a-module-on-gpu-on-azure-stack-edge-pro-device"></a>Configure e execute um módulo em GPU no dispositivo Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

O seu dispositivo Azure Stack Edge Pro contém uma ou mais Unidade de Processamento de Gráficos (GPU). As GPUs são uma opção popular para cálculos de IA, pois oferecem capacidades de processamento paralelo e são mais rápidas na composição de imagens do que nas Unidades de Processamento Central (CPUs). Para obter mais informações sobre a GPU contida no seu dispositivo Azure Stack Edge Pro, aceda às especificações técnicas do [dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-technical-specifications-compliance.md).

Este artigo descreve como configurar e executar um módulo na GPU no seu dispositivo Azure Stack Edge Pro. Neste artigo, utilizará um módulo de contentores publicamente disponível **Digits** escrito para GPUs Nvidia T4. Este procedimento pode ser usado para configurar quaisquer outros módulos publicados pela Nvidia para estas GPUs.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que:

1. Tem acesso a um dispositivo GPU ativado por 1 nó Azure Stack Edge Pro. Este dispositivo está ativado com um recurso no Azure.  

## <a name="configure-module-to-use-gpu"></a>Configurar o módulo para utilizar a GPU

Para configurar um módulo para utilizar a GPU no seu dispositivo Azure Stack Edge Pro para executar um módulo,<!--Can it be simplified? "To configure a module to be run by the GPU on your Azure Stack Edge Pro device,"?--> seguir estes passos.

1. No portal Azure, aceda ao recurso associado ao seu dispositivo.

2. Em **Visão Geral**, selecione **IoT Edge**.

    ![Módulo de configuração para utilizar GPU 1](media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-1.png)

3. No **serviço Enable IoT Edge**, selecione **Add**.

   ![Módulo de configuração para utilizar GPU 2](media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-2.png)

4. No **serviço Create IoT Edge,** introduza as definições para o seu recurso IoT Hub:

   |Campo   |Valor    |
   |--------|---------|
   |Subscrição      | Subscrição utilizada pelo recurso Azure Stack Edge. |
   |Grupo de recursos    | Grupo de recursos utilizado pelo recurso Azure Stack Edge. |
   |IoT Hub           | Escolha entre **criar novo** ou **utilizar existente.** <br> Por predefinição, é utilizado um escalão Standard (S1) para criar um recurso IoT. Para utilizar um recurso IoT de escalão gratuito, crie um e, em seguida, selecione o recurso existente. <br> Em cada caso, o recurso do Hub IoT utiliza a mesma subscrição e o mesmo grupo de recursos utilizados pelo recurso do Azure Stack Edge.     |
   |Name              | Se não quiser utilizar o nome padrão fornecido para um novo recurso IoT Hub, insira um nome diferente. |

   Quando terminar as definições, selecione **Review + Create**. Reveja as definições do seu recurso IoT Hub e selecione **Criar**.

   ![Começar com o compute 2](./media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-3.png)

   A criação de recursos para um recurso IoT Hub demora vários minutos. Após a criação do recurso, a **Visão Geral** indica que o serviço IoT Edge está agora em execução.

   ![Começar com o computo 3](./media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-4.png)

5. Para confirmar que a função de computação Edge foi configurada, selecione **Properties**.

   ![Começar com o compute 4](./media/azure-stack-edge-gpu-configure-gpu-modules/configure-compute-5.png)

6. Em **Properties**, selecione o link para **o dispositivo IoT Edge**.

   ![Módulo de configuração para utilizar GPU 6](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-2.png)

   No painel direito, vê o dispositivo IoT Edge associado ao seu dispositivo Azure Stack Edge Pro. Este dispositivo corresponde ao dispositivo IoT Edge que criou ao criar o recurso IoT Hub.
 
7. Selecione este dispositivo IoT Edge.

   ![Módulo de configuração para utilizar GPU 7](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-3.png)

8. Selecione **Definir módulos**.

   ![Módulo de configuração para utilizar GPU 8](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-4.png)

9. **Selecione + Adicione** e, em seguida, selecione + módulo **IoT Edge**. 

    ![Módulo de configuração para utilizar GPU 9](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-5.png)

10. No separador **Módulo de Borda IoT:**

    1. Forneça o **URI de imagem.** Você usará o módulo Nvidia publicamente disponível **Digits** aqui. 
    
    2. Desace **a política de reiniciar** para **sempre**.
    
    3. Definir **o estado desejado** para **executar**.
    
    ![Módulo de configuração para utilizar GPU 10](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-6.png)

11. No separador **de variáveis Ambiente,** forneça o Nome da variável e o valor correspondente. 

    1. Para que o módulo atual utilize uma GPU neste dispositivo, utilize o NVIDIA_VISIBLE_DEVICES. 

    2. Desa um valor para 0 ou 1. Um valor de 0 ou 1 garante que pelo menos uma GPU é utilizada pelo dispositivo para este módulo. Quando define o valor para 0, 1, isso implica que ambas as GPUs do seu dispositivo estão a ser utilizadas por este módulo.

       ![Módulo de configuração para utilizar GPU 11](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-7.png)

       Para obter mais informações sobre variáveis ambientais que você pode usar com o GPU da Nvidia, vá para o [tempo de funcionação do contentor nVidia](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).

    > [!NOTE]
    > Um módulo pode usar um, ambos ou nenhum GPUs.

12. Insira um nome para o seu módulo. Neste ponto pode optar por fornecer ao recipiente criar opção e modificar as definições gémeas do módulo ou, se for feito, selecione **Adicionar**. 

    ![Módulo de configuração para utilizar GPU 12](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-8.png)

13. Certifique-se de que o módulo está em execução e selecione **Review + Create**.

    ![Módulo de configuração para utilizar GPU 13](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-9.png)

14. No **separador 'Rever + Criar',** as opções de implantação selecionadas são apresentadas. Reveja as opções e **selecione Criar**.
    
    ![Módulo de configuração para utilizar GPU 14](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-10.png)

15. Tome nota do estado de **funcionação** do módulo.
    
    ![Módulo de configuração para utilizar GPU 15](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-11.png)

    Leva alguns minutos para o módulo ser implantado. Selecione **Refresh** e deverá ver a atualização do **estado de funcionamento** para **executar**.

    ![Módulo de configuração para utilizar GPU 16](media/azure-stack-edge-gpu-configure-gpu-modules/configure-gpu-12.png)


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [as variáveis ambientais que pode utilizar com o GPU da Nvidia.](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec)
