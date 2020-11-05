---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 6b716640bc8117ccc2d473b337ee3e555e8d9b0a
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376043"
---
1. Inscreva-se no dispositivo Data Box. Certifique-se de que está desbloqueado.

    ![Painel de caixa de dados](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Ir para **definir interfaces de rede**. Tome nota do endereço IP do dispositivo para a interface de rede utilizada para ligar ao cliente.

    ![Painel de instrumentos da Caixa de Dados 2](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Vá para **Ligar e copiar** e clique em **Descansar.**

    ![Painel de instrumentos da Caixa de Dados 3](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. A partir da **conta de Armazenamento de Acesso e do diálogo de dados de upload,** copie o **Ponto final do Serviço Blob**.

    ![Painel de instrumentos da Caixa de Dados 4](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Inicie **o Bloco de Notas** como administrador e, em seguida, abra o ficheiro de **anfitriões** localizado em `C:\Windows\System32\Drivers\etc` .
6. Adicione o seguinte ficheiro de entrada no ficheiro **dos anfitriões:**`<device IP address> <Blob service endpoint>`
7. Para referência, utilize a seguinte imagem. Guarde o ficheiro dos **anfitriões.**

    ![Painel de instrumentos da Caixa de Dados 5](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
