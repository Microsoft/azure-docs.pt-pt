---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: d01cf90c42efbe611748027d0ea47ff8af3e5621
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553160"
---
1. Inscreva-se no dispositivo Data Box. Certifique-se de que está desbloqueado.

    ![A screenshot mostra o seu painel de instrumentos com o dispositivo apresentado como Desbloqueado.](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Ir para **definir interfaces de rede**. Tome nota do endereço IP do dispositivo para a interface de rede utilizada para ligar ao cliente.

    ![A screenshot mostra as Definições de Rede onde pode ver o endereço I P.](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Vá para **Ligar e copiar** e clique em **Descansar.**

    ![A screenshot mostra o painel de ligação e cópia onde pode selecionar REST como uma definição de acesso.](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. A partir da **conta de Armazenamento de Acesso e do diálogo de dados de upload,** copie o **Ponto final do Serviço Blob**.

    ![A screenshot mostra a conta de armazenamento Access e a caixa de diálogo de dados de upload onde pode copiar o Ponto final do Serviço Blob.](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Inicie **o Bloco de Notas** como administrador e, em seguida, abra o ficheiro de **anfitriões** localizado em `C:\Windows\System32\Drivers\etc` .
6. Adicione o seguinte ficheiro de entrada no ficheiro **dos anfitriões:**`<device IP address> <Blob service endpoint>`
7. Para referência, utilize a seguinte imagem. Guarde o ficheiro dos **anfitriões.**

    ![O Screenshot mostra um documento do Bloco de Notas com o endereço I P e o ponto final do serviço blob adicionados.](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
