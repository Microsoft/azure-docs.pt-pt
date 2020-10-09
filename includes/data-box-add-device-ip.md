---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: e4b366075cb16f62a0e16b5b06da6fb19ffefdb9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67184924"
---
1. Inscreva-se no dispositivo Data Box. Certifique-se de que está desbloqueado.

    ![Painel de caixa de dados](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Ir para **definir interfaces de rede**. Tome nota do endereço IP do dispositivo para a interface de rede utilizada para ligar ao cliente.

    ![Painel de caixa de dados](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Vá para **Ligar e copiar** e clique em **Descansar.**

    ![Painel de caixa de dados](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. A partir da **conta de Armazenamento de Acesso e do diálogo de dados de upload,** copie o **Ponto final do Serviço Blob**.

    ![Painel de caixa de dados](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Inicie **o Bloco de Notas** como administrador e, em seguida, abra o ficheiro de **anfitriões** localizado em `C:\Windows\System32\Drivers\etc` .
6. Adicione o seguinte ficheiro de entrada no ficheiro **dos anfitriões:**`<device IP address> <Blob service endpoint>`
7. Para referência, utilize a seguinte imagem. Guarde o ficheiro dos **anfitriões.**

    ![Painel de caixa de dados](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
