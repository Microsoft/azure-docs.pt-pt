---
title: Tutorial para ativar dispositivo Azure Stack Edge com GPU no portal Azure Microsoft Docs
description: Tutorial para implementar Azure Stack Edge GPU instrui-o a ativar o seu dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to activate Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: 88be4d9753e48f70dae5666e800a54209ed6ba3f
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267943"
---
# <a name="tutorial-activate-azure-stack-edge-with-gpu"></a>Tutorial: Ativar Azure Stack Edge com GPU

Este tutorial descreve como pode ativar o seu dispositivo Azure Stack Edge com uma GPU a bordo utilizando a UI web local.

O processo de ativação pode demorar cerca de 5 minutos a ser concluído.

Neste tutorial, aprendeu sobre:

> [!div class="checklist"]
> * Pré-requisitos
> * Ativar o dispositivo físico

## <a name="prerequisites"></a>Pré-requisitos

Antes de configurar e configurar o seu dispositivo Azure Stack Edge com GPU, certifique-se de que:

* Para o seu dispositivo físico: 
    
    - Instalou o dispositivo físico conforme detalhado na [Instalação Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).
    - Configuraste as definições de rede e de rede de cálculo como detalhado na [rede de configuração, rede de cálculo, procuração web](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
    - Fez o upload dos seus próprios certificados ou gerou os certificados do dispositivo no seu dispositivo se alterou o nome do dispositivo ou o domínio DNS através da página **Dispositivo.** Se não tiver feito este passo, verá um erro durante a ativação do dispositivo e a ativação será bloqueada. Para mais informações, aceda aos [certificados de configuração.](azure-stack-edge-gpu-deploy-configure-certificates.md)
    
* Tem a chave de ativação do serviço Azure Stack Edge que criou para gerir o dispositivo Azure Stack Edge. Para mais informações, vá a [Preparar-se para implementar o Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md).


## <a name="activate-the-device"></a>Ativar o dispositivo

1. Na uI web local do dispositivo, vá para **começar** a página.
2. No azulejo de **ativação,** **selecione Ativar**. 

    ![Página local de UI "Cloud details"](./media/azure-stack-edge-gpu-deploy-activate/activate-1.png)
    
3. No painel **Ativar,** introduza a **tecla de ativação** que obteve em [Obter a chave de ativação para Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key).

4. Selecione **Aplicar**.

    ![Página local de UI "Cloud details"](./media/azure-stack-edge-gpu-deploy-activate/activate-2.png)


5. Primeiro o dispositivo é ativado. Em seguida, é solicitado que descarregue o ficheiro chave.
    
    ![Página local de UI "Cloud details"](./media/azure-stack-edge-gpu-deploy-activate/activate-3.png)
    
    Selecione **Descarregue** o ficheiro de chave e guarde as *keys.jsem* ficheiro num local seguro fora do dispositivo. **Este ficheiro-chave contém as teclas de recuperação do disco de so e discos de dados no seu dispositivo.** Aqui está o conteúdo do *keys.jsem* arquivo:

        
    ```json
    {
      "Id": "1ab3fe39-26e6-4984-bb22-2e02d3fb147e",
      "DataVolumeBitLockerExternalKeys": {
        "hcsinternal": "C086yg1DrPo0DuZB/a7hUh+kBWj804coJfBA9LDzZqw=",
        "hcsdata": "8ohX9bG3YSZl9DZmZLkYl//L9dXi1XiQrqza+iSd64Q="
      },
      "SystemVolumeBitLockerRecoveryKey": "105347-156739-594473-151107-005082-252604-471955-439395",
      "ServiceEncryptionKey": "oEwxNJeULzGRFt6DsLgcLw=="
    }
    ```
        
 
    A tabela a seguir explica as várias chaves aqui:
    
    |Campo  |Descrição  |
    |---------|---------|
    |`Id`    | Esta é a identificação do dispositivo.        |
    |`DataVolumeBitLockerExternalKeys`|Estas são as teclas BitLockers para os discos de dados e são usadas para recuperar os dados locais no seu dispositivo.|
    |`SystemVolumeBitLockerRecoveryKey`| Esta é a chave BitLocker para o volume do sistema. Esta chave ajuda na recuperação da configuração do sistema e dados do sistema para o seu dispositivo. |
    |`ServiceEncryptionKey`| Esta chave protege os dados que fluem através do serviço Azure. Esta chave garante que um compromisso do serviço Azure não resultará num compromisso de informação armazenada. |

6. Vá à página **geral.** O estado do dispositivo deve indicar como **Ativado**.

    ![Página local de UI "Cloud details"](./media/azure-stack-edge-gpu-deploy-activate/activate-4.png)
 
A ativação do dispositivo está completa. Agora pode adicionar ações no seu dispositivo.


## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre:

> [!div class="checklist"]
> * Pré-requisitos
> * Ativar o dispositivo físico

Para aprender a transferir dados com o seu dispositivo Azure Stack Edge, consulte:

> [!div class="nextstepaction"]
> [Dados de transferência com Azure Stack Edge](./azure-stack-edge-j-series-deploy-add-shares.md)
