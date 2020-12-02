---
title: Tutorial para configurar certificados para dispositivo Azure Stack Edge Mini R no portal Azure / Microsoft Docs
description: Tutorial para implantar O Azure Stack Edge Mini R instrui-o a configurar certificados no seu dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge Mini R  so I can use it to transfer data to Azure.
ms.openlocfilehash: 1e81ab61b04f9cca6aff57de1736eac25a871c97
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96468980"
---
# <a name="tutorial-configure-certificates-vpn-encryption-for-your-azure-stack-edge-mini-r"></a>Tutorial: Configure certificados, VPN, encriptação para o seu Azure Stack Edge Mini R

Este tutorial descreve como pode configurar certificados, VPN e encriptação-em-repouso para o seu dispositivo Azure Stack Edge Mini R utilizando a UI web local.

O tempo necessário para este passo pode variar dependendo da opção específica que escolher e da forma como o fluxo de certificado é estabelecido no seu ambiente.

Neste tutorial, ficará a saber mais sobre:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Certificados de configuração para o dispositivo físico
> * Configurar o VPN
> * Encriptação configurada em repouso

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar e configurar o seu dispositivo Azure Stack Edge Mini R, certifique-se de que:

* Instalou o dispositivo físico conforme detalhado na [Instalação Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-install.md).

* Se pretender trazer os seus próprios certificados:
    - Deve ter os seus certificados prontos no formato apropriado, incluindo o certificado de cadeia de assinatura. Para mais detalhes sobre o certificado, aceda a [Gerir certificados](azure-stack-edge-j-series-manage-certificates.md)

    - Se o seu dispositivo for implantado no Azure Government ou no Azure Government Secret ou na Azure Government top secret cloud e não for implantado na nuvem pública do Azure, é necessário um certificado de cadeia de assinatura antes de poder ativar o seu dispositivo. 
    Para mais informações sobre o certificado, aceda a [Certificados de Gestão](azure-stack-edge-j-series-manage-certificates.md).


## <a name="configure-certificates-for-device"></a>Certificados de configuração para dispositivo

1. Na página **Certificados,** irá configurar os seus certificados. Dependendo se alterou o nome do dispositivo ou o domínio DNS na página **Dispositivo,** pode escolher uma das seguintes opções para os seus certificados.

    - Se não tiver alterado o nome do dispositivo predefinido ou o domínio DNS predefinido no passo anterior e não pretender trazer os seus próprios certificados, então pode saltar este passo e seguir para o passo seguinte. O dispositivo gerou automaticamente certificados auto-assinados para começar. 

       <!-- ![Local web UI "Certificates" page](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-1.png)-->

    - Se alterar o nome do dispositivo ou o domínio DNS, verá que o estado dos certificados não será **válido**. 

        ![Web local UI "Certificados" página 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-2.png)    

        Selecione um certificado para ver os detalhes do estado.

        <!--![Local web UI "Certificates" page 3](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1a.png)-->  

        O estado do certificado não é **válido** porque os certificados não refletem o nome do dispositivo atualizado e o domínio DNS (que são utilizados no nome do assunto e na alternativa ao assunto). Para ativar com sucesso o seu dispositivo, pode trazer os seus próprios certificados de ponto final assinados e as cadeias de assinatura correspondentes. Primeiro adicione a cadeia de assinaturas e, em seguida, carre fique com os certificados de ponto final. Para mais informações, vá [ao Bring your own certificates no seu dispositivo Azure Stack Edge Mini R](#bring-your-own-certificates).


    - Se alterar o nome do dispositivo ou o domínio DNS e não trouxer os seus próprios certificados, então a **ativação será bloqueada**.


#### <a name="bring-your-own-certificates"></a>Traga os seus próprios certificados

Já adicionou a cadeia de assinaturas num passo anterior neste dispositivo. Pode agora enviar os certificados de ponto final, o certificado de nó, o certificado local de UI e o certificado VPN. Siga estes passos para adicionar os seus próprios certificados.

1. Para fazer o upload do certificado, na página **'Certificado',** selecione **+ Adicionar o certificado**.

    [![Web local UI "Certificados" página 4](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png#lightbox)


1. Pode fazer o upload de outros certificados. Por exemplo, pode carregar os certificados de ponto final de armazenamento Azure Resource Manager e Blob.

    ![Web local UI "Certificados" página 6](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

1. Também pode fazer o upload do certificado de UI web local. Depois de fazer o upload deste certificado, será obrigado a iniciar o seu navegador e a limpar a cache. Em seguida, terá de se ligar ao dispositivo web UI local.  

    ![Web local UI "Certificados" página 7](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

1. Também pode carregar o certificado de nó.


    ![Web local UI "Certificados" página 8](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

1. Finalmente, pode carregar o certificado VPN.
        
    ![Página local de UI "Certificados"](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-6.png)

1. A qualquer momento, pode selecionar um certificado e ver os detalhes para garantir que estes correspondam ao certificado que fez o upload.

    [![Web local UI "Certificados" página 9](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png#lightbox)

    A página do certificado deve atualizar para refletir os certificados recém-adicionados.

    [![Web local UI "Certificados" página 10](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png#lightbox)  

    > [!NOTE]
    > Com exceção da nuvem pública Azure, os certificados de cadeia de assinatura são necessários para serem trazidos antes da ativação para todas as configurações em nuvem (Azure Government ou Azure Stack Hub).


## <a name="configure-vpn"></a>Configurar o VPN

1. No azulejo **de segurança,** selecione **Configure** para VPN. 

    Para configurar a VPN, primeiro terá de garantir que tem todas as configurações necessárias feitas em Azure. Para mais detalhes, consulte [VPN Configure via PowerShell para o seu dispositivo Azure Stack Edge Mini R](azure-stack-edge-placeholder.md). Uma vez concluído, pode fazer a configuração na UI local.
    
    1. Na página VPN, selecione **Configure**.
        ![Configure VPN uI local 1](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-1.png)

    1. Na lâmina **VPN configurada:**

        1. Forneça a lista telefónica como entrada.
        2. Forneça o ficheiro IP do Centro de Dados Azure como entrada. Descarregue este ficheiro a partir de: [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519) .
        3. **Selecione eastus** como a região.
        4. Selecione **Aplicar**.

        ![Configure VPN uI local 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-2.png)
    
    1. Configure os intervalos de endereço IP a aceder apenas VPN. 
    
        - Nos **intervalos de endereços IP a aceder apenas através de VPN,** selecione **Configure**.
        - Insira a gama VNET IPv4 que escolheu para a sua Rede Virtual Azure.
        - Selecione **Aplicar**.
    
        ![Configure VPN uI local 3](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-3.png)

O seu dispositivo está agora pronto para ser encriptado. Configure a encriptação em repouso.


## <a name="enable-encryption"></a>Ativar a encriptação

1. No azulejo **de Segurança,** selecione **Configure** para encriptação em repouso. Esta é uma definição necessária e até que esta seja configurada com sucesso, não é possível ativar o dispositivo. 

    ![Web local UI "Encriptação em repouso" página 1](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-1.png)

    Na fábrica, uma vez que os dispositivos são visualizados, a encriptação bitLocker de nível de volume está ativada. Depois de receber o dispositivo, tem de configurar a encriptação em repouso. O pool de armazenamento e volumes são recriados e você pode fornecer chaves BitLocker para ativar a encriptação em repouso e assim criar uma segunda camada de encriptação para os seus dados em repouso.

1. No painel **de encriptação-em-repouso,** introduza uma chave codificada Base-64 de 32 caracteres (AES-256 bit). Esta é uma configuração única e esta chave é usada para proteger a chave de encriptação real. 

    ![Web local UI "Encriptação em repouso" página 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-2.png)

    Também pode optar por gerar automaticamente esta tecla.

    ![Web local UI "Encriptação em repouso" página 3](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. Selecione **Aplicar**. Esta operação demora vários minutos e o estado de funcionamento é apresentado no **telha de segurança.**

    ![Web local UI "Encriptação em repouso" página 4](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. Depois de o estado aparecer como **Concluído,** volte para **começar**.

O seu dispositivo está agora pronto para ser ativado.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficará a saber mais sobre:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Certificados de configuração para o dispositivo físico
> * Configurar o VPN
> * Encriptação configurada em repouso

Para aprender a ativar o seu dispositivo Azure Stack Edge Mini R, consulte:

> [!div class="nextstepaction"]
> [Ativar o dispositivo Azure Stack Edge Mini R](./azure-stack-edge-mini-r-deploy-activate.md)
