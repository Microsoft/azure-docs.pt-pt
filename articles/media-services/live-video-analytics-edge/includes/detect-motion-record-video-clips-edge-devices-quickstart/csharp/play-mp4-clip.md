---
ms.openlocfilehash: f4a1cc432a50a555fe6e050ca318b4cfaf1092d4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88682251"
---
Os ficheiros MP4 são escritos num diretório no dispositivo de borda que configuraste no ficheiro *.env* utilizando a tecla OUTPUT_VIDEO_FOLDER_ON_DEVICE. Se utilizou o valor predefinido, os resultados devem estar na pasta */var/media/.*

Para reproduzir o clipe MP4:

1. Vá ao seu grupo de recursos, encontre o VM e, em seguida, conecte-se utilizando O Bastião Azure.

    ![Grupo de recursos](../../../media/quickstarts/resource-group.png)
    
    ![VM](../../../media/quickstarts/virtual-machine.png)
1. Faça o sômedísmos utilizando as credenciais que foram geradas quando [configurar os seus recursos Azure.](../../../detect-motion-emit-events-quickstart.md#set-up-azure-resources) 
1. No comando, vá ao diretório relevante. A localização predefinida é */var/media.* Devia ver os ficheiros MP4 no diretório.

    ![Saída](../../../media/quickstarts/samples-output.png) 

1. Utilize [a Cópia Segura (SCP)](../../../../../virtual-machines/linux/copy-files-to-linux-vm-using-scp.md) para copiar os ficheiros para a sua máquina local. 
1. Reproduz os ficheiros utilizando o [leitor de mídia VLC](https://www.videolan.org/vlc/) ou qualquer outro leitor de MP4.
