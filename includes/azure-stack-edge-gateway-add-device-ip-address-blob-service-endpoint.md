---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 11/21/2019
ms.author: alkohli
ms.openlocfilehash: 0c6845f081ccbe42e70964eaa939d58597e3b69b
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185548"
---
1. Vá à uI web local do seu dispositivo e inscreva-se no seu dispositivo. Certifique-se de que o aparelho está desbloqueado.

2. Aceda à página **de definições de Rede.** Tome nota do endereço IP do dispositivo para a interface de rede utilizada para ligar ao cliente.

3. Se trabalhar com um cliente remoto do Windows, inicie **o Notepad** como administrador e, em seguida, abra o ficheiro de anfitriões localizado em `C:\Windows\System32\Drivers\etc` .

4. Adicione o seguinte ficheiro de entrada no ficheiro dos anfitriões: `<Device IP address> <Blob service endpoint>`

    Obteve o ponto final do serviço blob da conta de armazenamento Edge criada no portal Azure. Utilizará apenas o sufixo do ponto final de serviço blob.

    Para referência, utilize a seguinte imagem. Guarde o ficheiro `hosts`.

    ![Modificar o ficheiro de anfitriões no cliente do Windows](media/azure-stack-edge-gateway-add-device-ip-address-blob-service-endpoint/hosts-file-1.png)