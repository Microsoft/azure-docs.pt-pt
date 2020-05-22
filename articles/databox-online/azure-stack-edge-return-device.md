---
title: Volte ou substitua o seu dispositivo Azure Stack Edge / Microsoft Docs
description: Descreve como devolver ou substituir o dispositivo Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/26/2020
ms.author: alkohli
ms.openlocfilehash: bb73494dd5fe22c3be645f732f9d0958e48edb64
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743644"
---
# <a name="return-or-replace-your-azure-stack-edge-device"></a>Volte ou substitua o seu dispositivo Azure Stack Edge

Este artigo descreve como limpar os dados e depois devolver o seu dispositivo Azure Stack Edge. Depois de ter devolvido o dispositivo, também pode eliminar o recurso associado ao dispositivo ou encomendar um dispositivo de substituição.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Limpe os dados dos discos de dados do dispositivo
> * Abra um bilhete de suporte para devolver o seu dispositivo
> * Embale o dispositivo e agende uma recolha
> * Eliminar o recurso no portal Azure
> * Obtenha um dispositivo de substituição

## <a name="erase-data-from-the-device"></a>Apagar dados do dispositivo

Para limpar os dados dos discos de dados do seu dispositivo, é necessário redefinir o seu dispositivo. Pode redefinir o seu dispositivo utilizando o UI web local ou a interface PowerShell.

Antes de redefinir, crie uma cópia dos dados locais no dispositivo, se necessário. Pode copiar os dados do dispositivo para um recipiente de armazenamento Azure.

Para redefinir o seu dispositivo utilizando a UI web local, tome os seguintes passos.

1. Na web local UI, vá ao **reset de Manutenção > Dispositivo**.
2. Selecione **o dispositivo reset**.

    ![Repor dispositivo](media/azure-stack-edge-return-device/device-reset-1.png)

3. Quando solicitado para confirmação, reveja o aviso e selecione **Sim** para continuar.

    ![Confirmar reset](media/azure-stack-edge-return-device/device-reset-2.png)  

O reset apaga os dados dos discos de dados do dispositivo. Dependendo da quantidade de dados no seu dispositivo, este processo demora cerca de 30 a 40 minutos.

Em alternativa, ligue-se à interface PowerShell do dispositivo e utilize o `Reset-HcsAppliance` cmdlet para apagar os dados dos discos de dados. Para mais informações, consulte [Redefinir o seu dispositivo](azure-stack-edge-connect-powershell-interface.md#reset-your-device).

> [!NOTE]
> - Se estiver a trocar ou a atualizar para um novo dispositivo, recomendamos que reponha o seu dispositivo apenas depois de ter recebido o novo dispositivo.
> - O reset do dispositivo apenas elimina todos os dados locais do dispositivo. Os dados que estão na nuvem não são apagados e recolhem [encargos.](https://azure.microsoft.com/pricing/details/storage/) Estes dados precisam de ser eliminados separadamente utilizando uma ferramenta de gestão de armazenamento em nuvem como o [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="open-a-support-ticket"></a>Abra um bilhete de apoio

Para iniciar o processo de devolução, tome os seguintes passos.

1. Abra um bilhete de Suporte com suporte da Microsoft indicando que deseja devolver o dispositivo. Selecione o tipo de problema como **Hardware Azure Stack Edge**.

    ![Abrir Pedido de suporte](media/azure-stack-edge-return-device/open-support-ticket-1.png)  

2. Um engenheiro de Suporte microsoft irá contactá-lo. Forneça os detalhes do envio.
3. Se precisar de uma caixa de envio de devolução, pode solicitá-la. Resposta **Sim** à pergunta **Preciso de uma caixa vazia para voltar.**


## <a name="schedule-a-pickup"></a>Agende uma recolha

1. Encerre o dispositivo. Na web local UI, vá para **as definições**de Manutenção > Energia .
2. Selecione **Desligar**. Quando solicitado para confirmação, clique **em Sim** para continuar. Para mais informações, consulte [Gerir o poder](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Desligue os cabos de alimentação e retire todos os cabos de rede do dispositivo.
4. Prepare o pacote de envio utilizando a sua própria caixa ou a caixa vazia que recebeu do Azure. Coloque o dispositivo e os cabos de alimentação que foram enviados com o dispositivo na caixa.
5. Afixe a etiqueta de envio que recebeu do Azure no pacote.
6. Agende uma recolha com a transportadora regional. Se devolver o dispositivo nos EUA, a sua transportadora poderá ser UPS ou FedEx. Para agendar uma pick-up com ups:

    1. Ligue para o ponto UPS local (número gratuito específico do seu país/região).
    2. Na sua chamada, cite o número de rastreio de envio invertido, como mostrado na sua etiqueta impressa.
    3. Se o número de rastreio não for cotado, a UPS exigirá que pague uma taxa adicional durante a recolha.

    Em vez de agendar a pickup, também pode deixar o Azure Stack Edge no local de entrega mais próximo.

## <a name="delete-the-resource"></a>Eliminar o recurso

Depois de o dispositivo ser recebido no centro de dados Azure, o dispositivo é inspecionado para obter danos ou quaisquer sinais de adulteração.

- Se o dispositivo chegar intacto e em boa forma, o medidor de faturação para para esse recurso. O Microsoft Support entrará em contacto consigo para confirmar que o dispositivo foi devolvido. Em seguida, pode eliminar o recurso associado ao dispositivo no portal Azure.
- Se o dispositivo chegar significativamente danificado, podem ser aplicadas coimas. Para mais detalhes, consulte as [FAQ sobre o dispositivo perdido ou danificado](https://azure.microsoft.com/pricing/details/databox/edge/) e os Termos de Serviço do [Produto](https://www.microsoft.com/licensing/product-licensing/products).  


Pode eliminar o dispositivo no portal Azure:
-    Depois de ter feito a encomenda e antes de o dispositivo ser preparado pela Microsoft.
-    Depois de ter devolvido o dispositivo à Microsoft, passa na inspeção física do centro de dados Do Azure e nas chamadas do Microsoft Support para confirmar que o dispositivo foi devolvido.

Se tiver ativado o dispositivo contra outra subscrição ou localização, a Microsoft irá mover a sua encomenda para a nova subscrição ou localização dentro de um dia útil. Depois de a encomenda ser movida, pode eliminar este recurso.


Tome as seguintes medidas para eliminar o dispositivo e o recurso no portal Azure.

1. No portal Azure, vá ao seu recurso e depois à **visão geral.** A partir da barra de comando, selecione **Delete**.

    ![Selecione eliminar](media/azure-stack-edge-return-device/delete-resource-1.png)

2. Na lâmina do **dispositivo Eliminar,** escreva o nome do dispositivo que pretende eliminar e selecione **Eliminar**.

    ![Confirmar eliminação](media/azure-stack-edge-return-device/delete-resource-2.png)

É notificado após o dispositivo e o recurso associado ser eliminado com sucesso.

## <a name="get-a-replacement-device"></a>Obtenha um dispositivo de substituição

É necessário um dispositivo de substituição quando o dispositivo existente tem uma falha de hardware ou necessita de uma atualização. Tome os seguintes passos quando o seu dispositivo tiver um problema de hardware:

1. [Abra um bilhete de suporte para problemas de hardware.](#open-a-support-ticket) O Microsoft Support determinará que uma Unidade de Substituição de Campo (FRU) não está disponível para este caso ou que o dispositivo necessita de uma atualização de hardware. Em qualquer dos casos, o Suporte ordenará um dispositivo de substituição.
2. [Crie um novo recurso](azure-stack-edge-deploy-prep.md#create-a-new-resource) para o dispositivo de substituição. Certifique-se de selecionar a caixa de verificação contra **i have a Azure Stack Edge device**. 
3. Depois de receber um dispositivo de substituição, [instale](azure-stack-edge-deploy-install.md) e [ative](azure-stack-edge-deploy-connect-setup-activate.md) o dispositivo de substituição contra o novo recurso.
4. Siga todos os passos para devolver o dispositivo original:
    1. Abra mais um bilhete para devolver o dispositivo original.
    2. [Apagar os dados do dispositivo](#erase-data-from-the-device).
    3. [Agende uma recolha.](#schedule-a-pickup)
    5. [Elimine o recurso](#delete-the-resource) associado ao dispositivo devolvido.



## <a name="next-steps"></a>Passos seguintes

- Saiba como [Gerir a largura de banda](azure-stack-edge-manage-bandwidth-schedules.md).
