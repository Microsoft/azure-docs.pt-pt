---
title: Tutorial para configurar certificados para dispositivo Azure Stack Edge Pro com GPU no portal Azure | Microsoft Docs
description: Tutorial para implantar Azure Stack Edge Pro com GPU instrui-o a configurar certificados no seu dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: alkohli
ms.openlocfilehash: 8b1203693b67bb1a8b9699b84dd3a437027e4c3d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055319"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-pro-with-gpu"></a>Tutorial: Configure certificados para o seu Azure Stack Edge Pro com GPU

Este tutorial descreve como pode configurar certificados para o seu dispositivo Azure Stack Edge Pro com uma GPU a bordo utilizando a UI web local.

O tempo necessário para este passo pode variar dependendo da opção específica que escolher e da forma como o fluxo de certificado é estabelecido no seu ambiente.

Neste tutorial, ficará a saber mais sobre:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Certificados de configuração para o dispositivo físico

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar e configurar o seu dispositivo Azure Stack Edge Pro com GPU, certifique-se de que:

* Instalou o dispositivo físico conforme detalhado no [Install Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md).
* Se pretender trazer os seus próprios certificados:
    - Deve ter os seus certificados prontos no formato apropriado, incluindo o certificado de cadeia de assinatura. Para mais detalhes sobre o certificado, aceda a [Gerir certificados](azure-stack-edge-gpu-manage-certificates.md)

<!--    - If your device is deployed in Azure Government or Azure Government Secret or Azure Government top secret cloud and not deployed in Azure public cloud, a signing chain certificate is required before you can activate your device. 
    For details on certificate, go to [Manage certificates](azure-stack-edge-gpu-manage-certificates.md).-->


## <a name="configure-certificates-for-device"></a>Certificados de configuração para dispositivo

1. Na página **Certificados,** irá configurar os seus certificados. Dependendo se alterou o nome do dispositivo ou o domínio DNS na página **Dispositivo,** pode escolher uma das seguintes opções para os seus certificados.

    - Se não tiver alterado o nome do dispositivo ou o domínio DNS no passo anterior e não desejar trazer os seus próprios certificados, então pode saltar este passo e seguir para o passo seguinte. O dispositivo gerou automaticamente certificados auto-assinados para começar. 

        ![Página local de UI "Certificados"](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-2.png)

    - Se alterar o nome do dispositivo ou o domínio DNS, verá que o estado dos certificados não será **válido**. 

        ![Web local UI "Certificados" página 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1.png)    

        Selecione um certificado para ver os detalhes do estado.

        ![Web local UI "Certificados" página 3](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1a.png)  

        Isto porque os certificados não refletem o nome do dispositivo atualizado e o domínio DNS (que são utilizados em nome do sujeito e alternativa de assunto). Para ativar com sucesso o seu dispositivo, escolha uma das seguintes opções: 
    
        - **Gere todos os certificados do dispositivo.** Estes certificados do dispositivo só devem ser utilizados para testes e não utilizados com cargas de trabalho de produção. Para mais informações, aceda aos [certificados de dispositivo Gerar no seu Azure Stack Edge Pro](#generate-device-certificates).

        - **Traga os seus próprios certificados.** Pode trazer os seus próprios certificados de ponto final assinados e as respetivas cadeias de assinatura. Primeiro adicione a cadeia de assinaturas e, em seguida, carre fique com os certificados de ponto final. **Recomendamos que traga sempre os seus próprios certificados para cargas de trabalho de produção.** Para mais informações, vá [buscar os seus próprios certificados no seu dispositivo Azure Stack Edge Pro.](#bring-your-own-certificates)
    
        - Pode trazer alguns dos seus próprios certificados e gerar alguns certificados de dispositivo. A opção **gerar certificados** só regenerará os certificados do dispositivo.

    - Se alterar o nome do dispositivo ou o domínio DNS e não gerar certificados ou trazer os seus próprios certificados, então a **ativação será bloqueada**.


### <a name="generate-device-certificates"></a>Gerar certificados de dispositivo

Siga estes passos para gerar certificados de dispositivo.

Utilize estes passos para regenerar e descarregar os certificados de dispositivo Azure Stack Edge Pro:

1. Na UI local do seu dispositivo, aceda aos **Certificados de > de Configuração**. **Selecione Gerar certificados**.

    ![Gerar e baixar certificado 1](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-3.png)

2. Nos **certificados do dispositivo Gerar,** selecione **Gerar**. 

    ![Gerar e baixar certificado 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-4.png)

    Os certificados do dispositivo são agora gerados e aplicados. Leva alguns minutos para gerar e aplicar os certificados.
    
    > [!IMPORTANT]
    > Enquanto a operação de geração de certificados estiver em andamento, não traga os seus próprios certificados e tente adicioná-los através da opção **+ Adicionar certificado.**

    É notificado quando a operação estiver concluída com sucesso. **Para evitar potenciais problemas de cache, reinicie o seu navegador.**
    
    ![Gerar e baixar certificado 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-5.png)

3. Após a geração dos certificados: 

    - O estado de todos os certificados mostra como **Válido.** 

        ![Gerar e baixar certificado 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6.png)

    - Pode selecionar um nome de certificado específico e ver os dados do certificado. 

        ![Gerar e baixar certificado 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6a.png)

    - A coluna **Download** está agora povoada. Esta coluna tem links para descarregar os certificados regenerados. 

        ![Gerar e baixar certificado 7](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6b.png)


4. Selecione o link de descarregamento para um certificado e, quando solicitado, guarde o certificado. 

    ![Gerar e baixar certificado 8](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-7.png)

5. Repita este processo para todos os certificados que pretende descarregar. 
    
    ![Gerar e baixar certificado 9](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-8.png)

    Os certificados gerados pelo dispositivo são guardados como certificados DER com o seguinte formato de nome: 

    `<Device name>_<Endpoint name>.cer`. Estes certificados contêm a chave pública para os certificados correspondentes instalados no dispositivo. 

Terá de instalar estes certificados no sistema cliente que está a utilizar para aceder aos pontos finais do dispositivo ASE. Estes certificados estabelecem confiança entre o cliente e o dispositivo.

Para importar e instalar estes certificados no cliente que está a utilizar para aceder ao dispositivo, siga os passos nos [certificados de Importação dos clientes que acedem ao seu dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device). 

Se utilizar o Azure Storage Explorer, terá de instalar certificados no seu cliente em formato PEM e terá de converter os certificados gerados pelo dispositivo em formato PEM. 

> [!IMPORTANT]
> - O link de descarregamento só está disponível para os certificados gerados pelo dispositivo e não se você trouxer os seus próprios certificados.
> - Pode decidir ter uma mistura de certificados gerados pelo dispositivo e trazer os seus próprios certificados desde que sejam cumpridos outros requisitos de certificado. Para mais informações, aceda aos [requisitos do Certificado.](azure-stack-edge-gpu-certificate-requirements.md)
    

### <a name="bring-your-own-certificates"></a>Traga os seus próprios certificados

Siga estes passos para adicionar os seus próprios certificados, incluindo a cadeia de assinaturas.

1. Para fazer o upload do certificado, na página **'Certificado',** selecione **+ Adicionar o certificado**.

    ![Web local UI "Certificados" página 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-1.png)

2. Faça o upload da cadeia de assinaturas primeiro e **selecione Valide & adicionar**.

    ![Web local UI "Certificados" página 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-2.png)

3. Agora pode fazer o upload de outros certificados. Por exemplo, pode carregar os certificados de ponto final de armazenamento Azure Resource Manager e Blob.

    ![Web local UI "Certificados" página 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-3.png)

    Também pode fazer o upload do certificado de UI web local. Depois de fazer o upload deste certificado, será obrigado a iniciar o seu navegador e a limpar a cache. Em seguida, terá de se ligar ao dispositivo web UI local.  

    ![Web local UI "Certificados" página 7](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-5.png)

    Também pode carregar o certificado de nó.

    ![Web local UI "Certificados" página 8](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-4.png)

    A qualquer momento, pode selecionar um certificado e ver os detalhes para garantir que estes correspondam ao certificado que fez o upload.

    ![Web local UI "Certificados" página 9](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-6.png)

    A página do certificado deve atualizar para refletir os certificados recém-adicionados.

    ![Web local UI "Certificados" página 10](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-7.png)  

    > [!NOTE]
    > Com exceção da nuvem pública Azure, os certificados de cadeia de assinatura são necessários para serem trazidos antes da ativação para todas as configurações em nuvem (Azure Government ou Azure Stack).


O seu dispositivo está agora pronto para ser ativado. Selecione **< Back to Get iniciou**.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficará a saber mais sobre:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Certificados de configuração para o dispositivo físico

Para aprender a ativar o seu dispositivo Azure Stack Edge Pro, consulte:

> [!div class="nextstepaction"]
> [Ativar o dispositivo Azure Stack Edge Pro](./azure-stack-edge-gpu-deploy-activate.md)
