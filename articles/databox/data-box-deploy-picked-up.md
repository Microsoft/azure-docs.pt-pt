---
title: Tutorial para enviar o Azure Data Box de volta | Microsoft Docs
description: Neste tutorial, saiba como devolver o Azure Data Box, incluindo preparar o envio, enviar o Data Box, verificar o carregamento de dados e apagar os dados do Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 72575725c0924b8ec5f9466f4ffbba86bf071db4
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631545"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Tutorial: Devolver o Azure Data Box e verificar os dados carregados para o Azure

::: zone-end

::: zone target="chromeless"

## <a name="return-data-box-and-verify-data-upload-to-azure"></a>Devolver o Data Box e verificar os dados carregados para o Azure

::: zone-end

::: zone target="docs"

Este tutorial descreve como devolver o Azure Data Box e certificar-se de que os dados foram carregados para o Azure.

Neste tutorial, irá obter informações sobre tópicos, como:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Preparar para enviar
> * Enviar o Data Box para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminação de dados do Data Box

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, confirme que:

* Concluiu o [Tutorial: Copiar dados para o Azure Data Box e verificar](data-box-deploy-copy-data.md).
* Os trabalhos de cópia estão concluídos e não há erros na página **Ligar e copiar** . Se os trabalhos de cópia estiverem em curso ou se existirem erros na página **Ligar e copiar** , a preparação para envio não pode ser executada.

## <a name="prepare-to-ship"></a>Preparar para enviar

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

Após a conclusão da cópia de dados, prepare e envie o dispositivo. Quando o dispositivo chegar ao datacenter do Azure, os dados são carregados automaticamente para o Azure.

## <a name="prepare-to-ship"></a>Preparar para enviar

Antes da preparação para envio, verifique se as tarefas de cópia foram concluídas.

1. Aceda à página **Preparar para enviar** na IU da Web local e comece a preparação do envio. 
2. Desative o dispositivo a partir da IU da Web local. Remova os cabos do dispositivo. 

A localização a partir da qual pretende devolver o dispositivo determina os passos seguintes.

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>Enviar o Data Box de volta

Verifique se a cópia de dados para o dispositivo foi concluída e a **Preparação para envio** foi executada com êxito. O procedimento é diferente com base na região a partir da qual envia o dispositivo.

::: zone-end

## <a name="us-canada-europe"></a>[EUA, Canadá, Europa](#tab/in-us-canada-europe)

Se quiser devolver o dispositivo nos EUA, Canadá ou Europa, siga os passos abaixo.

1. Confirme que o dispositivo está desligado e os cabos foram removidos. 
2. Coloque em segurança o cabo de alimentação enviado com o dispositivo atrás do mesmo.
3. Certifique-se de que a etiqueta de envio é apresentada no ecrã de tinta eletrónica e agende uma recolha com a sua operadora. Se a guia estiver danificada, tiver sido perdida ou não aparecer na apresentação da E-ink, contacte o Suporte da Microsoft. Se a equipa do Suporte o sugerir, poderá aceder a **Visão geral > Transferir guia de remessa** no portal do Azure. Transfira a guia de remessa e cole-a no dispositivo. 
4. Se estiver a devolver o dispositivo, contacte a UPS para agendar uma recolha. Para agendar uma recolha:

    * Ligue para o ponto UPS local (número gratuito específico do seu país/região).
    * Durante a chamada, indique o número de rastreio do envio de devolução mostrado na apresentação da E-ink ou na guia impressa.
    * Se não indicar o número de rastreio, terá de pagar uma taxa adicional à UPS na recolha.

    Em vez de agendar a recolha, também pode entregar o Data Box na localização de entrega mais próxima.
4. Após a sua transportadora recolher e analisar o Data Box, o estado de encomenda no portal será atualizado para **Picked up (Recolhido)** . É também mostrado um ID de controlo.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Eliminação de dados do Data Box

Após o carregamento para o Azure, o Data Box elimina os dados dos discos de acordo com as [diretrizes SP 800-88 Revision 1 da NIST](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

## <a name="australia"></a>[Austrália](#tab/in-australia)

Os datacenters do Azure na Austrália têm uma notificação de segurança adicional. Para todos os envios dirigidos aos mesmos, é necessária uma notificação antecipada. Siga os passos abaixo para os envios na Austrália.

1. Mantenha a embalagem original utilizada para enviar o dispositivo para a devolução.
2. Verifique se a cópia de dados para o dispositivo foi concluída e a **Preparação para envio** foi executada com êxito.
3. Desligue o dispositivo e remova os cabos.
4. Coloque em segurança o cabo de alimentação enviado com o dispositivo atrás do mesmo.
5. Marque uma recolha online através da [Ligação da DHL](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference).

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Eliminação de dados do Data Box

Após o carregamento para o Azure, o Data Box elimina os dados dos discos de acordo com as [diretrizes SP 800-88 Revision 1 da NIST](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

## <a name="japan"></a>[Japão](#tab/in-japan)

1. Mantenha a embalagem original utilizada para enviar o dispositivo para a devolução.
2. Desligue o dispositivo e remova os cabos.
3. Coloque em segurança o cabo de alimentação enviado com o dispositivo atrás do mesmo.
4. Escreva o nome e o endereço da sua empresa na nota de expedição como as informações do remetente.
5. Utilize o modelo que se segue para enviar um e-mail à Quantium Solutions.

    * Caso a nota de expedição Chakubarai da Japan Post não tenha sido incluída ou esteja em falta, indique-o neste e-mail. A Quantium Solutions Japan irá solicitar à Japan Post que apresente a nota de expedição na recolha.
    * Se tiver várias encomendas, envie um e-mail para garantir a recolha individual.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box｜Job name： 
    Body:
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
    a. 08：00-13：00 
    b. 13：00-15：00 
    c. 15：00-17：00 
    d. 17：00-19：00 
    ```

6. Depois de marcar uma recolha, recebe uma confirmação por e-mail da Quantium Solutions. Esta confirmação também inclui informações sobre a nota de expedição Chakubarai.

Se necessário, pode contactar o Apoio ao Cliente da Quantium Solutions (em japonês) por: 

* E-mail：Customerservice.JP@quantiumsolutions.com 
* Telefone: 03-5755-0150 

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Eliminação de dados do Data Box
 
Após o carregamento para o Azure, o Data Box elimina os dados dos discos de acordo com as [diretrizes SP 800-88 Revision 1 da NIST](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

## <a name="singapore"></a>[Singapura](#tab/in-singapore)

1. Mantenha a embalagem original utilizada para enviar o dispositivo para a devolução.
2. Tome nota do número de controlo (apresentado como número de referência na página Preparação para envio da IU da Web local do Data Box). Está disponível após o passo de preparação para envio ser concluído com êxito. Transfira o guia de remessa a partir desta página e cole na caixa da embalagem.
3. Desligue o dispositivo e remova os cabos.
4. Coloque em segurança o cabo de alimentação enviado com o dispositivo atrás do mesmo. 
5. Utilize o modelo que se segue com o número de controlo para enviar um e-mail para o Suporte ao Cliente da SingPost.

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pickup - OrderName 
    Body: 
        1. Requestor name  
        2. Requestor contact number
        3. Requestor collection address
        4. Preferred collection date
    ```

   > [!NOTE]
   > Para pedidos de reserva recebidos num dia útil:
   > * Antes das 15:00, a recolha será agendada para o dia útil seguinte entre as 9:00 e as 13:00.
   > * Depois das 15:00, a recolha será agendada para o dia útil seguinte entre as 14:00 e as 18:00.  

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Eliminação de dados do Data Box

Após o carregamento para o Azure, o Data Box elimina os dados dos discos de acordo com as [diretrizes SP 800-88 Revision 1 da NIST](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

## <a name="south-africa"></a>[África do Sul](#tab/in-sa)

1. Embale o dispositivo para devolução na embalagem original.
2. Coloque em segurança o cabo de alimentação enviado com o dispositivo atrás do mesmo.
3. Para agendar uma recolha com a DHL:

    * Ligue para o centro de contacto do atendimento ao cliente através do número +27(0) 11 9213600, selecione a opção 1 e, em seguida, especifique o número da carta de porte.
    * A chamada tem de ser feita antes das 14:00 para agendar uma recolha.
    * Envie um e-mail para [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) com o seguinte modelo:

    ```output
    To: Priority.Support@dhl.com
    Subject: Pickup request for Microsoft Azure
    Body: Need pick up for the below shipment
      *  DHL tracking number (reference number/waybill number)
      *  Requested pickup date: yyyy/mm/dd;time:HH MM
    ```

    * Em alternativa, pode levantar a embalagem no ponto de serviço da DHL mais próximo.

4. Se encontrar algum problema, envie um e-mail [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) com detalhes sobre o problema encontrado e coloque o número da carta de porte na linha de assunto. Também pode ligar para +27(0)119213902.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Eliminação de dados do Data Box

Após o carregamento para o Azure, o Data Box elimina os dados dos discos de acordo com as [diretrizes SP 800-88 Revision 1 da NIST](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

## <a name="hong-kong"></a>[RAE de Hong Kong](#tab/in-hk)

1. Embale o dispositivo para devolução na embalagem original.
2. Coloque em segurança o cabo de alimentação enviado com o dispositivo atrás do mesmo.
3. Ligue para a linha direta da **Quantium Solutions** através do número **(852) 2318 1213** durante o horário de expediente (das 9:00 às 18:00, de segunda a sexta).  
4. Indique Microsoft Azure pickup (Recolha do Microsoft Azure) e o número de controlo (acima do código de barras) na guia de remessa para devolução para combinar uma recolha.
5. Recebe uma confirmação verbal do horário da recolha. Se o estafeta não efetuar a recolha, ligue para a linha direta da Quantium Solutions para combinar uma alternativa.
6. Ao reservar uma recolha com a Quantium, partilhe a confirmação com o [Microsoft Data Box Operations Asia](mailto:adbo@microsoft.com) com o seguinte modelo:

    ```output
    To: adbo@microsoft.com
    Subject: Microsoft Data Box Job: [order name] has completed copy
    Body:
    We have confirmed the pickup details with Quantium.

       * Requestor name:
       * Requestor contact number:
       * Pickup Date:  
       * Pickup time:
    ```

Se encontrar qualquer problema, envie um e-mail para o Data Box Operations Asia [adbo@microsoft.com](mailto:adbo@microsoft.com) com o nome do trabalho no cabeçalho do assunto e o problema encontrado.

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Eliminação de dados do Data Box
 
Após o carregamento para o Azure, o Data Box elimina os dados dos discos de acordo com as [diretrizes SP 800-88 Revision 1 da NIST](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

<!--## [In Korea](#tab/in-korea) 

1. Retain the original box used to ship the device for return shipment.
2. Note down the tracking number (shown as reference number on the Prepare to Ship page of the Data Box local web UI). This is available after the prepare to ship step successfully completes. Download the shipping label from this page and paste on the packing box. 
3. Power off the device and remove the cables.
4. Spool and securely place the power cord that was provided with the device in the back of the device. 

Request pickup  
If consignment note is present:  

1. Call Quantium Solutions International hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote Microsoft Azure pickup and the service request number to arrange for a collection.
2. If the hotline is busy, email microsoft@rocketparcel.com, with the email subject Microsoft Azure Pickup and the service request number as reference.  
3. If the courier does not arrive for collection, call Quantium Solutions International hotline for alternate arrangements.  
4. You will receive an email confirmation for the pickup schedule.  

Exception process
If the consignment note is not present:
1. Call Quantium Solutions International hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote Microsoft Azure pickup and the service request number. Specify that you need a new consignment note to arrange for a collection. Provide sender (customer), receiver information (Azure datacenter), and reference number (service request number).
2. If the hotline is busy, email microsoft@rocketparcel.com, with the email subject Microsoft Azure Pickup and the service request number as reference.
3. If the courier does not arrive for collection, call Quantium Solutions International hotline for alternate arrangements.
4. You get a verbal confirmation if request is made via telephone.  
::: zone target="chromeless"

## Verify data upload to Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## Erasure of data from Data Box
 
Once the upload to Azure is complete, the Data Box erases the data on its disks as per the [NIST SP 800-88 Revision 1 guidelines](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end
-->

## <a name="self-managed"></a>[Envio Autónomo](#tab/in-selfmanaged)

Se estiver a utilizar o Data Box nas seguintes regiões: US Government, Japão, Singapura, Coreia, Índia, África do Sul ou Europa Ocidental, e tiver selecionado a opção de envio autónomo durante a criação da encomenda, siga estas instruções. 

1. Tome nota do Código de autorização apresentado na página Preparação para envio da IU da Web local do Data Box após este passo ser concluído com êxito.
2. Desligue o dispositivo e remova os cabos. Coloque em segurança o cabo de alimentação enviado com o dispositivo na parte de trás do mesmo.
3. Utilize o modelo abaixo para enviar um e-mail para a equipa de Operações do Azure Data Box quando estiver pronto para devolver o dispositivo.

    ```
    To: adbops@microsoft.com
    Subject: Request for Azure Data Box drop-off for order: 'orderName'
    Body:
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```

::: zone target="chromeless"

---

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Eliminação de dados do Data Box
 
Após o carregamento para o Azure, o Data Box elimina os dados dos discos de acordo com as [diretrizes SP 800-88 Revision 1 da NIST](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

