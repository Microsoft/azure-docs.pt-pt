---
title: Tutorial para configurar certificados para dispositivo Azure Stack Edge Pro R no portal Azure | Microsoft Docs
description: Tutorial para implantar Azure Stack Edge Pro R instrui-o a configurar certificados no seu dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/19/2020
ms.author: alkohli
ms.openlocfilehash: 99a0f93b73e3aa3d1cb031301150ec528870ab38
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059977"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-pro-r"></a>Tutorial: Configure certificados para o seu Azure Stack Edge Pro R

Este tutorial descreve como pode configurar certificados para o seu dispositivo Azure Stack Edge Pro R utilizando a UI web local.

O tempo necessário para este passo pode variar dependendo da opção específica que escolher e da forma como o fluxo de certificado é estabelecido no seu ambiente.

Neste tutorial, ficará a saber mais sobre:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Certificados de configuração para o dispositivo físico
> * Configurar o VPN
> * Configurar encriptação em repouso

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar e configurar o seu dispositivo Azure Stack Edge Pro R, certifique-se de que:

* Instalou o dispositivo físico conforme detalhado na [Instalação Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-install.md).
* Se pretender trazer os seus próprios certificados:
    - Deve ter os seus certificados prontos no formato apropriado, incluindo o certificado de cadeia de assinatura. Para mais detalhes sobre o certificado, aceda a [Gerir certificados](azure-stack-edge-gpu-manage-certificates.md)



## <a name="configure-certificates-for-device"></a>Certificados de configuração para dispositivo

1. Na página **Certificados,** irá configurar os seus certificados. Dependendo se alterou o nome do dispositivo ou o domínio DNS na página **Dispositivo,** pode escolher uma das seguintes opções para os seus certificados.

    - Se não tiver alterado o nome do dispositivo ou o domínio DNS no passo anterior, então pode saltar este passo e seguir para o passo seguinte. O dispositivo gerou automaticamente certificados auto-assinados para começar. 

    - Se alterar o nome do dispositivo ou o domínio DNS, verá que o estado dos certificados não será **válido**. 

        ![Web local UI "Certificados" página 2](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1.png)    

        Selecione um certificado para ver os detalhes do estado.

        ![Web local UI "Certificados" página 3](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1a.png)  

        Isto porque os certificados não refletem o nome do dispositivo atualizado e o domínio DNS (que são utilizados em nome do sujeito e alternativa de assunto). Para ativar com sucesso o seu dispositivo, pode trazer os seus próprios certificados de ponto final assinados e as cadeias de assinatura correspondentes. Primeiro adicione a cadeia de assinaturas e, em seguida, carre fique com os certificados de ponto final. Para mais informações, vá [buscar os seus próprios certificados no seu dispositivo Azure Stack Edge Pro R](#bring-your-own-certificates).

    - Se alterar o nome do dispositivo ou o domínio DNS e não trouxer os seus próprios certificados, então a **ativação será bloqueada**.

    
#### <a name="bring-your-own-certificates"></a>Traga os seus próprios certificados

Siga estes passos para adicionar os seus próprios certificados, incluindo a cadeia de assinaturas.

1. Para fazer o upload do certificado, na página **'Certificado',** selecione **+ Adicionar o certificado**.

    ![Web local UI "Certificados" página 4](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)

2. Faça o upload da cadeia de assinaturas primeiro e **selecione Valide & adicionar**.

    ![Web local UI "Certificados" página 5](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-2.png)

3. Agora pode fazer o upload de outros certificados. Por exemplo, pode carregar os certificados de ponto final de armazenamento Azure Resource Manager e Blob.

    ![Web local UI "Certificados" página 6](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

    Também pode fazer o upload do certificado de UI web local. Depois de fazer o upload deste certificado, será obrigado a iniciar o seu navegador e a limpar a cache. Em seguida, terá de se ligar ao dispositivo web UI local.  

    ![Web local UI "Certificados" página 7](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

    Também pode carregar o certificado de nó.

    ![Web local UI "Certificados" página 8](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    Finalmente, pode carregar o certificado VPN.

    ![Web local UI "Certificados" página 9](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    A qualquer momento, pode selecionar um certificado e ver os detalhes para garantir que estes correspondam ao certificado que fez o upload.

    A página do certificado deve atualizar para refletir os certificados recém-adicionados.

    ![Web local UI "Certificados" página 10](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)  

    > [!NOTE]
    > Com exceção da nuvem pública Azure, os certificados de cadeia de assinatura são necessários para serem trazidos antes da ativação para todas as configurações em nuvem (Azure Government ou Azure Stack).

1. Selecione **< Back to Get iniciou**.

## <a name="configure-vpn"></a>Configurar o VPN

1. No azulejo **de segurança,** selecione **Configure** para VPN.

    ![Página local da UI "VPN"](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-1.png)  

    Para configurar a VPN, primeiro terá de garantir que tem todas as configurações necessárias feitas em Azure. Para mais informações, consulte [os pré-requisitos](azure-stack-edge-placeholder.md) de configuração e [os recursos configure Azure para VPN](azure-stack-edge-placeholder.md). Uma vez concluído, pode fazer a configuração na UI local.
    
    1. Na página VPN, selecione **Configure**.
    2. Na lâmina **VPN configurada:**

    - Ativar **as definições VPN**.
    - Forneça o **segredo partilhado da VPN.** Esta é a chave partilhada que forneceu ao criar o objeto de conexão Azure VPN.
    - Forneça o endereço **IP de gateway VPN.** Este é o endereço IP da rede local Azure.
    - Para **o grupo PFS**, selecione **Nenhum**. 
    - Para **o grupo DH**, selecione **Group2**.
    - Para **o método de integridade do IPsec,** selecione **SHA256**.
    - Para **as constantes de transformação de IPseccipher**, selecione **GCMAES256**.
    - Para **as constantes de transformação de autenticação IPsec,** selecione **GCMAES256**.
    - Para **o método de encriptação IKE**, selecione **AES256**.
    - Selecione **Aplicar**.

        ![Configurar uI local 2](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-2.png)

    3. Para carregar o ficheiro de configuração da rota VPN, selecione **Upload**. 
    
        ![Configurar uI local 3](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-3.png)
    
        - Navegue no ficheiro *json* de configuração VPN que descarregou no seu sistema local no passo anterior.
        - Selecione a região como a região Azure associada ao dispositivo, rede virtual e gateways.
        - Selecione **Aplicar**.
    
            ![Configurar uI local 4](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-4.png)
    
    4. Para adicionar rotas específicas ao cliente, configurar os intervalos de endereço IP a serem acedidos apenas com VPN. 
    
        - Nos **intervalos de endereços IP a aceder apenas através de VPN,** selecione **Configure**.
        - Forneça uma gama IPv4 válida e selecione **Adicionar**. Repita os passos para adicionar outros intervalos.
        - Selecione **Aplicar**.
    
            ![Configurar uI local 5](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-5.png)

1. Selecione **< Back to Get iniciou**.

## <a name="configure-encryption-at-rest"></a>Configurar encriptação em repouso

1. No azulejo **de Segurança,** selecione **Configure** para encriptação em repouso. Esta é uma definição necessária e até que esta seja configurada com sucesso, não é possível ativar o dispositivo. 

    Na fábrica, uma vez que os dispositivos são visualizados, a encriptação bitLocker de nível de volume está ativada. Depois de receber o dispositivo, tem de configurar a encriptação em repouso. O pool de armazenamento e volumes são recriados e você pode fornecer chaves BitLocker para ativar a encriptação em repouso e assim criar uma segunda camada de encriptação para os seus dados em repouso.

1. No painel **de encriptação-no-descanso,** forneça uma chave codificada Base-64 de 32 caracteres. Esta é uma configuração única e esta chave é usada para proteger a chave de encriptação real. Pode optar por gerar automaticamente esta tecla ou introduzir uma.

    ![Painel de internet local UI "Encriptação em repouso"](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-key-1.png)

    A chave é guardada num ficheiro chave na página **de detalhes** da Cloud após a ativação do dispositivo.

1. Selecione **Aplicar**. Esta operação demora vários minutos e o estado de funcionamento é apresentado no **telha de segurança.**

    ![Página local de UI "Encriptação em repouso"](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-status-1.png)

1. Depois de o estado aparecer como **concluído,** selecione **< Back to Get iniciou**.

O seu dispositivo está agora pronto para ser ativado. 


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficará a saber mais sobre:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Certificados de configuração para o dispositivo físico
> * Configurar o VPN
> * Configurar encriptação em repouso

Para aprender a ativar o seu dispositivo Azure Stack Edge Pro R, consulte:

> [!div class="nextstepaction"]
> [Ativar o dispositivo Azure Stack Edge Pro R](./azure-stack-edge-pro-r-deploy-activate.md)
