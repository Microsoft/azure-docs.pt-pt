---
title: Devolva o seu dispositivo Azure Stack Edge Pro | Microsoft Docs
description: Saiba como limpar os dados e devolver o seu dispositivo Azure Stack Edge Pro e, em seguida, elimine o recurso associado ao dispositivo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/27/2020
ms.author: alkohli
ms.openlocfilehash: 0c35bde40cac9629f084d69d52f119651b5655f7
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784482"
---
# <a name="return-your-azure-stack-edge-pro-device"></a>Devolva o seu dispositivo Azure Stack Edge Pro

Este artigo descreve como limpar os dados e, em seguida, devolver o seu dispositivo Azure Stack Edge Pro. Depois de ter devolvido o dispositivo, também pode eliminar o recurso associado ao dispositivo.

Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> * Limpe os dados dos discos de dados do dispositivo
> * Inicie a devolução do dispositivo no portal do Azure
> * Embale o dispositivo e agende uma recolha
> * Eliminar o recurso no portal Azure

## <a name="erase-data-from-the-device"></a>Apague os dados do dispositivo

Para eliminar os dados dos discos de dados do seu dispositivo, é necessário reiniciar o seu dispositivo. Pode reiniciar o seu dispositivo utilizando a UI web local ou a interface PowerShell.

Antes de reiniciar, crie uma cópia dos dados locais do dispositivo, se necessário. Pode copiar os dados do dispositivo para um recipiente de armazenamento Azure.

Pode iniciar a reposição do dispositivo mesmo antes de o dispositivo ser reiniciado. 

Para reiniciar o seu dispositivo utilizando a UI web local, tome os seguintes passos.

1. Na UI web local, vá ao **reset do Dispositivo de Manutenção >**.
2. Selecione **o dispositivo Reset**.

    ![Repor dispositivo](media/azure-stack-edge-return-device/device-reset-1.png)

3. Quando solicitado para confirmação, reveja o aviso e selecione **Sim** para continuar.

    ![Confirmar reset](media/azure-stack-edge-return-device/device-reset-2.png)  

O reset apaga os dados dos discos de dados do dispositivo. Dependendo da quantidade de dados no seu dispositivo, este processo demora cerca de 30 a 40 minutos.

Em alternativa, ligue-se à interface PowerShell do dispositivo e utilize o `Reset-HcsAppliance` cmdlet para apagar os dados dos discos de dados. Para obter mais informações, consulte [redefinir o seu dispositivo](azure-stack-edge-connect-powershell-interface.md#reset-your-device).

> [!NOTE]
> - Se estiver a trocar ou a atualizar para um novo dispositivo, recomendamos que reinicie o seu dispositivo apenas depois de receber o novo dispositivo.
> - O reset do dispositivo apenas elimina todos os dados locais do dispositivo. Os dados que estão na nuvem não são apagados e recolhem [encargos.](https://azure.microsoft.com/pricing/details/storage/) Estes dados devem ser eliminados separadamente utilizando uma ferramenta de gestão de armazenamento em nuvem como [o Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="initiate-device-return"></a>Iniciar a devolução do dispositivo

Para iniciar o processo de devolução, tome os seguintes passos.

1. Aceda ao seu recurso Azure Stack Edge Pro/Data Box Gateway no portal Azure. No **resumo,** vá à barra de comando no painel direito e selecione **Dispositivo de retorno**. 

    ![Dispositivo de devolução 1](media/azure-stack-edge-return-device/return-device-1.png)  

2. Na lâmina do **dispositivo Return,** em **detalhes básicos:**

    1. Forneça o número de série do dispositivo. Para obter o número de série do dispositivo, vá à uI web local do dispositivo e, em seguida, vá ao **Overview**.  
    
    ![Série do dispositivo número 1](media/azure-stack-edge-return-device/device-serial-number-1.png) 

    2. Introduza o número da etiqueta de serviço que é um identificador de cinco ou mais caracteres que é exclusivo do seu dispositivo. A etiqueta de serviço está localizada no canto inferior direito do dispositivo (à medida que se encontra virado para o dispositivo). Retire a etiqueta de informação (é um painel de etiqueta de deslizamento). Este painel contém informações do sistema, tais como etiqueta de serviço, endereço NIC, MAC, e assim por diante. 
    
    ![Etiqueta de serviço número 1](media/azure-stack-edge-return-device/service-tag-number-1.png)

    3. Da lista de desistes, escolha uma razão para a devolução.

    ![Dispositivo de devolução 2](media/azure-stack-edge-return-device/return-device-2.png) 

3. Sob **detalhes do envio:**

    1. Forneça o seu nome, nome da empresa e endereço completo da empresa. Introduza um telefone de trabalho, incluindo o código de área e um ID de e-mail para notificação.
    2. Se precisar de uma caixa de envio de devolução, pode solicitá-la. Resposta **Sim** à pergunta **Precisa de uma caixa vazia para devolver**.

    ![Dispositivo de devolução 3](media/azure-stack-edge-return-device/return-device-3.png)

4. Reveja os **termos de Privacidade** e selecione a caixa de verificação contra a nota que reviu e concorde com os termos de privacidade.

5. Selecione **Iniciar a devolução**.

    ![Dispositivo de devolução 4](media/azure-stack-edge-return-device/return-device-4.png) 

6. Assim que os dados de retorno do seu dispositivo forem capturados, pode notificar a equipa de operações do Azure Stack Edge Pro através de um e-mail. Pode utilizar a sua aplicação de e-mail assumindo que a aplicação de e-mail está instalada e configurada. Também pode copiar os dados para criar e enviar um e-mail.

    ![Dispositivo de devolução 5](media/azure-stack-edge-return-device/return-device-5.png) 

7. Assim que a equipa de operações do Azure Stack Edge Pro receber o e-mail, eles enviar-lhe-ão uma etiqueta de envio inversa. Quando receber esta etiqueta, pode agendar a recolha do dispositivo com a transportadora. 

## <a name="schedule-a-pickup"></a>Agende uma recolha

Para agendar uma recolha, tome os seguintes passos.

1. Encerre o dispositivo. Na uI web local, vá para as **definições de Manutenção > Energia**.
2. Selecione **Desligar**. Quando solicitado para confirmação, clique em **Sim** para continuar. Para obter mais informações, consulte [Gerir a energia](../databox-gateway/data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Desligue os cabos de alimentação e retire todos os cabos de rede do aparelho.
4. Prepare o pacote de envio usando a sua própria caixa ou a caixa vazia que recebeu da Azure. Coloque o dispositivo e os cabos de alimentação que foram enviados com o dispositivo na caixa.
5. Afixe a etiqueta de envio que recebeu da Azure na embalagem.
6. Agende uma recolha com a transportadora regional. Se devolver o dispositivo nos EUA, a sua transportadora poderá ser UPS ou FedEx. Para agendar uma recolha com UPS:

    1. Ligue para o ponto UPS local (número gratuito específico do seu país/região).
    2. Na sua chamada, cite o número de rastreio de envio invertido como mostrado na sua etiqueta impressa.
    3. Se o número de rastreio não for citado, a UPS exigirá que pague uma taxa adicional durante a recolha.

    Em vez de agendar a recolha, também pode deixar o Azure Stack Edge Pro no local de entrega mais próximo.

## <a name="delete-the-resource"></a>Eliminar o recurso

Depois de o dispositivo ser recebido no centro de dados Azure, o dispositivo é inspecionado para verificar se existem danos ou sinais de adulteração.

- Se o dispositivo chegar intacto e estiver em bom estado, o medidor de faturação para para esse recurso. A equipa de operações do Azure Stack Edge Pro entrará em contacto consigo para confirmar que o dispositivo foi devolvido. Em seguida, pode eliminar o recurso associado ao dispositivo no portal Azure.
- Se o aparelho chegar significativamente danificado, podem ser aplicadas coimas. Para mais informações, consulte as [FAQ sobre](https://azure.microsoft.com/pricing/details/databox/edge/) o dispositivo perdido ou danificado e [os Termos de Serviço do Produto.](https://www.microsoft.com/licensing/product-licensing/products)  


Pode eliminar o dispositivo no portal Azure:

- Depois de ter feito a encomenda e antes de o dispositivo ser preparado pela Microsoft.
- Depois de ter devolvido o dispositivo à Microsoft, passa a inspeção física no datacenter Azure e a equipa de operações do Azure Stack Edge Pro liga para confirmar que o dispositivo foi devolvido.

Se ativou o dispositivo contra outra subscrição ou localização, a Microsoft irá mover a sua encomenda para a nova subscrição ou localização dentro de um dia útil. Depois de a encomenda ser movida, pode eliminar este recurso.


Tome as seguintes medidas para eliminar o dispositivo e o recurso no portal Azure.

1. No portal Azure, vá ao seu recurso e depois ao **Overview**. A partir da barra de comando, **selecione Delete**.

    ![Selecione eliminar](media/azure-stack-edge-return-device/delete-resource-1.png)

2. Na lâmina do **dispositivo Eliminar,** digite o nome do dispositivo que pretende eliminar e selecione **Delete**.

    ![Confirmar eliminação](media/azure-stack-edge-return-device/delete-resource-2.png)

É notificado após o dispositivo e o recurso associado ser eliminado com sucesso.


## <a name="next-steps"></a>Próximos passos

- Saiba como [obter um dispositivo Azure Stack Edge Pro de substituição](azure-stack-edge-replace-device.md).