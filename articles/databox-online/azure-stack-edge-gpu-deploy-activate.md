---
title: Tutorial para ativar dispositivo Azure Stack Edge Pro com GPU no portal Azure Microsoft Docs
description: Tutorial para implementar Azure Stack Edge Pro GPU instrui-o a ativar o seu dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 6807174f374e75f826001afecc1eccf8ba1a0603
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91743391"
---
# <a name="tutorial-activate-azure-stack-edge-pro-with-gpu"></a>Tutorial: Ativar Azure Stack Edge Pro com GPU

Este tutorial descreve como pode ativar o seu dispositivo Azure Stack Edge Pro com uma GPU a bordo utilizando a UI web local.

O processo de ativação pode demorar cerca de 5 minutos a ser concluído.

Neste tutorial, aprendeu sobre:

> [!div class="checklist"]
> * Pré-requisitos
> * Ativar o dispositivo físico

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar e configurar o seu dispositivo Azure Stack Edge Pro com GPU, certifique-se de que:

* Para o seu dispositivo físico: 
    
    - Instalou o dispositivo físico conforme detalhado no [Install Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md).
    - Configuraste as definições de rede e de rede de cálculo como detalhado na [rede de configuração, rede de cálculo, procuração web](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
    - Fez o upload dos seus próprios certificados ou gerou os certificados do dispositivo no seu dispositivo se alterou o nome do dispositivo ou o domínio DNS através da página **Dispositivo.** Se não tiver feito este passo, verá um erro durante a ativação do dispositivo e a ativação será bloqueada. Para mais informações, aceda aos [certificados de configuração.](azure-stack-edge-gpu-deploy-configure-certificates.md)
    
* Tem a chave de ativação do serviço Azure Stack Edge que criou para gerir o dispositivo Azure Stack Edge Pro. Para mais informações, vá a [Prepare-se para implementar o Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md).


## <a name="activate-the-device"></a>Ativar o dispositivo

1. Na uI web local do dispositivo, vá para **começar** a página.
2. No azulejo de **ativação,** **selecione Ativar**. 

    ![Página local de UI "Cloud details"](./media/azure-stack-edge-gpu-deploy-activate/activate-1.png)
    
3. No painel **Ativar,** introduza a **tecla de ativação** que obteve em [Obter a chave de ativação para Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key).

4. Selecione **Aplicar**.

    ![Web local UI "Cloud details" página 2](./media/azure-stack-edge-gpu-deploy-activate/activate-2.png)


5. Primeiro o dispositivo é ativado. Em seguida, é solicitado que descarregue o ficheiro chave.
    
    ![Web local UI "Cloud details" página 3](./media/azure-stack-edge-gpu-deploy-activate/activate-3.png)
    
    Selecione **Baixar e continuar** e guardar asdevice-serial-no.js* no* ficheiro num local seguro fora do dispositivo. **Este ficheiro-chave contém as teclas de recuperação do disco de so e discos de dados no seu dispositivo.** Estas chaves podem ser necessárias para facilitar uma futura recuperação do sistema.

    Aqui está o conteúdo do ficheiro *json:*

        
    ```json
    {
      "Id": "<Device ID>",
      "DataVolumeBitLockerExternalKeys": {
        "hcsinternal": "<BitLocker key for data disk>",
        "hcsdata": "<BitLocker key for data disk>"
      },
      "SystemVolumeBitLockerRecoveryKey": "<BitLocker key for system volume>",
      "ServiceEncryptionKey": "<Azure service encryption key>"
    }
    ```
        
 
    A tabela seguinte explica as várias teclas:
    
    |Campo  |Descrição  |
    |---------|---------|
    |`Id`    | Esta é a identificação do dispositivo.        |
    |`DataVolumeBitLockerExternalKeys`|Estas são as teclas BitLockers para os discos de dados e são usadas para recuperar os dados locais no seu dispositivo.|
    |`SystemVolumeBitLockerRecoveryKey`| Esta é a chave BitLocker para o volume do sistema. Esta chave ajuda na recuperação da configuração do sistema e dados do sistema para o seu dispositivo. |
    |`ServiceEncryptionKey`| Esta chave protege os dados que fluem através do serviço Azure. Esta chave garante que um compromisso do serviço Azure não resultará num compromisso de informação armazenada. |

6. Vá à página **geral.** O estado do dispositivo deve indicar como **Ativado**.

    ![Web local UI "Cloud details" página 4](./media/azure-stack-edge-gpu-deploy-activate/activate-4.png)
 
A ativação do dispositivo está completa. Agora pode adicionar ações no seu dispositivo.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre:

> [!div class="checklist"]
> * Pré-requisitos
> * Ativar o dispositivo físico

Para aprender a transferir dados com o seu dispositivo Azure Stack Edge Pro, consulte:

> [!div class="nextstepaction"]
> [Dados de transferência com Azure Stack Edge Pro](./azure-stack-edge-j-series-deploy-add-shares.md)
