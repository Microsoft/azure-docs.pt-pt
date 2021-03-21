---
ms.openlocfilehash: dfb887004cd29b5bd9f1d9886b7dfa5f43c83dbe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99531801"
---
Os ficheiros MP4 são escritos num diretório no dispositivo de borda que configuraste no ficheiro *.env* utilizando a tecla VIDEO_OUTPUT_FOLDER_ON_DEVICE. Se utilizou o valor predefinido, os resultados devem estar na pasta */var/media/.*

Para reproduzir o clipe MP4:

1. Vá ao seu grupo de recursos, encontre o VM e, em seguida, conecte-se utilizando O Bastião Azure.

    ![Grupo de recursos](../../../media/quickstarts/resource-group.png)
    
    ![VM](../../../media/quickstarts/virtual-machine.png)
1. Faça o sômedísmos utilizando as credenciais que foram geradas quando [configurar os seus recursos Azure.](../../../detect-motion-emit-events-quickstart.md#set-up-azure-resources) 
1. No comando, vá ao diretório relevante. A localização predefinida é */var/media.* Devia ver os ficheiros MP4 no diretório.

    ![Saída](../../../media/quickstarts/samples-output.png) 

1. Utilize [a Cópia Segura (SCP)](../../../../../virtual-machines/linux/copy-files-to-linux-vm-using-scp.md) para copiar os ficheiros para a sua máquina local. 
1. Reproduz os ficheiros utilizando o [leitor de mídia VLC](https://www.videolan.org/vlc/) ou qualquer outro leitor de MP4.
