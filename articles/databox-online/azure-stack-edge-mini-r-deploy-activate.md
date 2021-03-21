---
title: Tutorial para encriptar e ativar o dispositivo Azure Stack Edge Mini R no portal Azure | Microsoft Docs
description: Tutorial para implementar O Azure Stack Edge Mini R instrui-o a encriptar e ativar o seu dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/22/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge Mini R so I can use it to transfer data to Azure.
ms.openlocfilehash: e5076888e692ad16e89f30d455e7f8c4e22a41b9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96468998"
---
# <a name="tutorial-activate-azure-stack-edge-mini-r"></a>Tutorial: Ativar Azure Stack Edge Mini R

Este tutorial descreve como pode ativar o seu dispositivo Azure Stack Edge Mini R utilizando a UI web local.

O processo de ativação pode demorar cerca de 15 minutos a ser concluído.

Neste tutorial, aprendeu sobre:

> [!div class="checklist"]
> * Pré-requisitos
> * Ativar o dispositivo físico

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar e configurar o seu dispositivo Azure Stack Edge Mini R, certifique-se de que:

* Para o seu dispositivo físico: 
    
    - Instalou o dispositivo físico conforme detalhado na [Instalação Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-install.md).
    - Configuraste as definições de rede e de rede de cálculo como detalhado na [rede de configuração, rede de cálculo, procuração web](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)
    - Fez o upload dos seus próprios certificados no seu dispositivo se alterar o nome do dispositivo ou o domínio DNS através da página **Dispositivo.** Estes passos são detalhados em [certificados de configuração, VPN e encriptação em repouso.](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md) Se ainda não deu este passo, a ativação será bloqueada.
    - Configuraste a encriptação em repouso para o teu dispositivo. Se não tiver feito este passo, verá um erro durante a ativação do dispositivo e a ativação será bloqueada. Para mais informações, aceda aos [certificados de configuração, VPN e encriptação em repouso.](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md)
    
* Tem a chave de ativação do serviço Azure Stack Edge que criou para gerir o dispositivo Azure Stack Edge Mini R. Para mais informações, vá a [Preparar-se para implementar o Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-prep.md).


## <a name="activate-the-device"></a>Ativar o dispositivo

1. Na uI web local do dispositivo, vá para **começar** a página.
2. No azulejo de **ativação,** **selecione Ativar**. 

    ![Web local UI "Cloud details" página 1](./media/azure-stack-edge-mini-r-deploy-activate/activation-1.png)
    
3. No painel **Ativar:**
    1. Introduza a **tecla de ativação** que obteve em [Obter a chave de ativação para Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

    1. Pode permitir que a recolha proativa de registos permita à Microsoft recolher registos com base no estado de saúde do dispositivo. Os registos recolhidos desta forma são enviados para uma conta de Armazenamento Azure.
    
    1. Selecione **Aplicar**.

    ![Web local UI "Cloud details" página 2](./media/azure-stack-edge-mini-r-deploy-activate/activation-2.png)


5. Primeiro o dispositivo é ativado. Em seguida, é solicitado que descarregue o ficheiro chave.
    
    ![Web local UI "Cloud details" página 3](./media/azure-stack-edge-mini-r-deploy-activate/activation-3.png)
    
    Selecione **Baixar e continuar** e guardar asdevice-serial-no.js *no* ficheiro num local seguro fora do dispositivo. **Este ficheiro-chave contém as teclas de recuperação do disco de so e discos de dados no seu dispositivo.** Estas chaves podem ser necessárias para facilitar uma futura recuperação do sistema.

    Aqui está o conteúdo do ficheiro *json:*

        
    ```json
    {
      "Id": "<Device ID>",
      "DataVolumeBitLockerExternalKeys": {
        "hcsinternal": "<BitLocker key for data disk>",
        "hcsdata": "<BitLocker key for data disk>"
      },
      "SystemVolumeBitLockerRecoveryKey": "<BitLocker key for system volume>",
      "SEDEncryptionExternalKey": "xZM/vC7GxjqHZ3VMo7xSs/wH9rRsF/PNM+mOsZ+GaL0=",
      "ServiceEncryptionKey": "<Azure service encryption key>"
    }
    ```        
 
    A tabela seguinte explica as várias teclas:
    
    |Campo  |Descrição  |
    |---------|---------|
    |`Id`    | Esta é a identificação do dispositivo.        |
    |`DataVolumeBitLockerExternalKeys`|Estas são as teclas BitLocker para os discos de dados e são usadas para recuperar os dados locais no seu dispositivo.|
    |`SystemVolumeBitLockerRecoveryKey`| Esta é a chave BitLocker para o volume do sistema. Esta chave ajuda na recuperação da configuração do sistema e dados do sistema para o seu dispositivo. |
    |`SEDEncryptionExternalKey`| Esta chave gerada pelo utilizador ou sistema é usada para proteger as unidades de dados auto-encriptadas que têm uma encriptação incorporada. |
    |`ServiceEncryptionKey`| Esta chave protege os dados que fluem através do serviço Azure. Esta chave garante que um compromisso do serviço Azure não resultará num compromisso de informação armazenada. |

6. Vá à página **geral.** O estado do dispositivo deve indicar como **Ativado**.

    ![Web local UI "Cloud details" página 4](./media/azure-stack-edge-mini-r-deploy-activate/activation-4.png)
 
A ativação do dispositivo está completa. Agora pode adicionar ações no seu dispositivo.

Se encontrar algum problema durante a ativação, aceda à [ativação de resolução de problemas e aos erros do Cofre da Chave Azure](azure-stack-edge-gpu-troubleshoot-activation.md#activation-errors).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre:

> [!div class="checklist"]
> * Pré-requisitos
> * Ativar o dispositivo físico

Para aprender a transferir dados com o seu dispositivo Azure Stack Edge Mini R, consulte:

> [!div class="nextstepaction"]
> [Dados de transferência com Azure Stack Edge Mini R](./azure-stack-edge-j-series-deploy-add-shares.md)
