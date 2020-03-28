---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: e4b366075cb16f62a0e16b5b06da6fb19ffefdb9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "67184924"
---
1. Assine o dispositivo Data Box. Certifique-se de que está desbloqueado.

    ![Painel de instrumentos da Caixa de Dados](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Vá para **Definir interfaces de rede**. Tome nota do endereço IP do dispositivo para a interface de rede utilizada para ligar ao cliente.

    ![Painel de instrumentos da Caixa de Dados](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Vá para **Ligar e copiar** e clique em **Descansar**.

    ![Painel de instrumentos da Caixa de Dados](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. A partir da conta de Armazenamento de **Acesso e diálogo de dados de upload,** copie o ponto final do serviço **Blob**.

    ![Painel de instrumentos da Caixa de Dados](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Inicie o Bloco de **Notas** como administrador e, `C:\Windows\System32\Drivers\etc`em seguida, abra o ficheiro dos **anfitriões** localizado em .
6. Adicione a seguinte entrada ao ficheiro dos **anfitriões:**`<device IP address> <Blob service endpoint>`
7. Para referência, utilize a seguinte imagem. Guarde o ficheiro dos **anfitriões.**

    ![Painel de instrumentos da Caixa de Dados](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
