---
title: Devolver o seu dispositivo Edge de caixa de dados do Azure | Documentos da Microsoft
description: Descreve como devolver o dispositivo de limite de caixa de dados do Azure e exclusão do pedido para o dispositivo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/07/2019
ms.author: alkohli
ms.openlocfilehash: 9aeae0ab68d809b36a3316054f12a5a9657721f1
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65468608"
---
# <a name="return-your-azure-data-box-edge-device"></a>Devolver o seu dispositivo Edge de caixa de dados do Azure

Este artigo descreve como eliminar os dados e, em seguida, retornar o seu dispositivo Edge de caixa de dados do Azure. Depois de ter devolvido o dispositivo, também pode eliminar o recurso associado ao dispositivo.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Eliminar os dados dos discos de dados no dispositivo
> * Abra um pedido de suporte para retornar o seu dispositivo
> * Pacote do dispositivo e agendar uma recolha
> * Eliminar o recurso no portal do Azure

## <a name="erase-data-from-the-device"></a>Apagar dados do dispositivo

Para eliminar os dados dos discos de dados do seu dispositivo, terá de repor o seu dispositivo. Pode repor o dispositivo através da IU da web local ou da interface do PowerShell.

Antes de repor, crie uma cópia dos dados locais no dispositivo se for necessário. Pode copiar os dados do dispositivo para um contentor de armazenamento do Azure.

Para repor o dispositivo através da IU da web local, siga os passos seguintes.

1. Na IU da web local, aceda a **manutenção > Repor o dispositivo**.
2. Selecione **repor dispositivo**.

    ![Repor dispositivo](media/data-box-edge-return-device/device-reset-1.png)

3. Quando lhe for pedida confirmação, reveja o aviso e selecione **Sim** para continuar.

    ![Confirme a reposição](media/data-box-edge-return-device/device-reset-2.png)  

A reposição apaga os dados dos discos de dados do dispositivo. Dependendo da quantidade de dados no seu dispositivo, este processo demora cerca de 30 e 40 minutos.

Em alternativa, ligar a interface do PowerShell do dispositivo e utilizar o `Reset-HcsAppliance` cmdlet para apagar os dados dos discos de dados. Para obter mais informações, consulte [repor o seu dispositivo](data-box-edge-connect-powershell-interface.md#reset-your-device).

> [!NOTE]
> - Se estiver a troca ou a atualização para um novo dispositivo, recomendamos que redefina o seu dispositivo apenas depois que recebeu o novo dispositivo.
> - O dispositivo Repor apenas elimina todos os dados locais fora do dispositivo. Os dados na cloud não são eliminados e recolhe [encargos](https://azure.microsoft.com/pricing/details/storage/). Estes dados precisam de eliminar separadamente com uma ferramenta de gerenciamento de armazenamento na cloud, como [Explorador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="open-a-support-ticket"></a>Abra um pedido de suporte

Para iniciar o processo de retorno, siga os passos seguintes.

1. Abra um pedido de suporte com Support da Microsoft com a indicação de que pretende devolver o dispositivo. Selecione o tipo de problema como **dados de caixa de borda Hardware**.

    ![Abrir pedido de suporte](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. Um engenheiro de Support da Microsoft irá contactá-lo. Forneça os detalhes de envio.
3. Se precisar de uma caixa de envio de retorna, podendo solicitá-la. Resposta **Sim** à pergunta **precisa de uma caixa vazia, devolver**.


## <a name="schedule-a-pickup"></a>Agendar uma recolha

1. Encerre o dispositivo. Na IU da web local, aceda a **manutenção > definições de energia**.
2. Selecione **Encerrar**. Quando lhe for pedida confirmação, clique em **Sim** para continuar. Para obter mais informações, consulte [gerenciar a energia](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power).
3. Desligue os cabos de energia e remova todos os cabos de rede do dispositivo.
4. Prepare o pacote de envio utilizando a sua própria caixa ou caixa em branco que recebeu do Azure. Coloque o dispositivo e os cabos de energia que foram enviados com o dispositivo na caixa.
5. Affix a etiqueta que recebeu do Azure no pacote.
6. Agende uma recolha com a sua operadora regional. Se devolver o dispositivo nos EUA, a sua operadora é no-BREAK. Para agendar uma recolha:

    1. Chame no-BREAK local (número gratuito de incidir específico do país).
    2. Em sua chamada citar a remessa inversa número conforme mostrado na sua etiqueta impressa de controle.
    3. Se o número de controlo não é indicado, UPS exigirá que paga um custo adicional durante a recolha.

    Em vez da recolha de agendamento, também pode remover fora da área de caixa de dados no local de entrega mais próximo.

## <a name="delete-the-resource"></a>Eliminar o recurso

Depois do dispositivo é recebido no datacenter do Azure, o dispositivo é inspecionado para danos ou sinais de adulteração.

- Se o dispositivo chega intactos e em bom estado, o contador de faturação será interrompido para esse recurso. Support da Microsoft irá contactá-lo para confirmar que o dispositivo foi devolvido. Em seguida, pode eliminar o recurso associado ao dispositivo no portal do Azure.
- Se o dispositivo chega significativamente danificado, podem aplicar multas. Para obter detalhes, consulte a [perguntas frequentes sobre o dispositivo perdido ou danificado](https://azure.microsoft.com/pricing/details/databox/edge/) e [produto termos de serviço](https://www.microsoft.com/licensing/product-licensing/products).  


Pode eliminar o dispositivo no portal do Azure:
-   Depois de ter fez a encomenda e antes do dispositivo está preparado pela Microsoft.
-   Depois de ter devolvido o dispositivo para a Microsoft, ele passa a inspeção física no datacenter do Azure e Support da Microsoft chama para confirmar que o dispositivo foi devolvido.

Se ativar o dispositivo em relação a outra subscrição ou localização, o Microsoft irá mover o seu pedido para a nova subscrição ou localização num dia útil. Depois de mover a ordem, pode eliminar este recurso.


Siga os passos seguintes para eliminar o dispositivo e o recurso no portal do Azure.

1. No portal do Azure, aceda ao seu recurso e clique em **descrição geral**. Na barra de comandos, selecione **eliminar**.

    ![Selecione delete](media/data-box-edge-return-device/delete-resource-1.png)

2. Na **eliminar dispositivo** painel, escreva o nome do dispositivo que pretende eliminar e selecione **eliminar**.

    ![Confirmar eliminação](media/data-box-edge-return-device/delete-resource-2.png)

Foi notificado depois do dispositivo e o recurso associado é eliminado com êxito.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [Gerir a largura de banda](data-box-edge-manage-bandwidth-schedules.md).
