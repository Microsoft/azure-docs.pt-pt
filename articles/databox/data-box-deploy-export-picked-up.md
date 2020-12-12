---
title: Tutorial para enviar Azure Data Box de volta em ordem de exportação / Microsoft Docs
description: Saiba como enviar a sua Caixa de Dados Azure para a Microsoft depois de a encomenda de exportação estar completa
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 12/11/2020
ms.author: alkohli
ms.openlocfilehash: 1035f7fb34ed5b2b11e3ef472ccda7cccc30f652
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359035"
---
# <a name="tutorial-return-azure-data-box-preview"></a>Tutorial: Caixa de Dados do Azure (Pré-visualização)

Este tutorial descreve como devolver a Caixa de Dados do Azure e os dados são apagados assim que o dispositivo é recebido nos dados do Azure.

Neste tutorial, irá obter informações sobre tópicos, como:

> [!div class="checklist"]
>
> * Pré-requisitos
> * Preparar para enviar
> * Enviar o Data Box para a Microsoft
> * Eliminação de dados do Data Box

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, confirme que:

* Completou o [Tutorial: Copiar dados da Caixa de Dados Azure.](data-box-deploy-export-copy-data.md)
* As tarefas de cópia foram concluídas. A preparação para envio não poderá ser executada se as tarefas de cópia estiverem em curso.

## <a name="prepare-to-ship"></a>Preparar para enviar

[!INCLUDE [data-box-export-prepare-to-ship](../../includes/data-box-export-prepare-to-ship.md)]

A localização a partir da qual pretende devolver o dispositivo determina os passos seguintes.

## <a name="ship-data-box-back"></a>Enviar o Data Box de volta

Certifique-se de que a cópia de dados do dispositivo está completa e **prepare-se para o funcionaamento do navio** com sucesso. Com base na região onde está a enviar o dispositivo, o procedimento é diferente.

## <a name="us-canada-europe"></a>[EUA, Canadá, Europa](#tab/in-us-canada-europe)

Se quiser devolver o dispositivo nos EUA, Canadá ou Europa, siga os passos abaixo.

1. Confirme que o dispositivo está desligado e os cabos foram removidos. 
2. Coloque em segurança o cabo de alimentação enviado com o dispositivo atrás do mesmo.
3. Certifique-se de que a etiqueta de envio está no visor da tinta E e agende uma recolha com a sua transportadora. Se a etiqueta estiver danificada ou perdida ou não for apresentada no visor de tinta E, contacte o Microsoft Support. Se o Suporte sugere, então pode ir ao **Overview > Baixar** a etiqueta de envio no portal Azure. Descarregue a etiqueta de envio e afixe-a no dispositivo. 
4. Se estiver a devolver o dispositivo, contacte a UPS para agendar uma recolha. Para agendar uma recolha:

   - Ligue para o ponto UPS local (número gratuito específico do seu país/região).
   - Durante a chamada, indique o número de rastreio do envio de devolução mostrado na apresentação da E-ink ou na guia impressa. Se não citar o número de rastreio, o UPS necessitará de uma taxa adicional durante a recolha.
   - Se surgirem problemas durante o agendamento de uma recolha, ou se lhe pedirem para pagar taxas adicionais, contacte as Operações da Caixa de Dados do Azure. Envie o e-mail para [adbops@microsoft.com](mailto:adbops@microsoft.com).

    Em vez de agendar a recolha, também pode entregar o Data Box na localização de entrega mais próxima.
4. Após a sua transportadora recolher e analisar o Data Box, o estado de encomenda no portal será atualizado para **Picked up (Recolhido)** . É também mostrado um ID de controlo.

## <a name="australia"></a>[Austrália](#tab/in-australia)

Os datacenters do Azure na Austrália têm uma notificação de segurança adicional. Para todos os envios dirigidos aos mesmos, é necessária uma notificação antecipada. Siga os passos abaixo para os envios na Austrália.

1. Guarde a caixa original usada para enviar o dispositivo para envio de devolução.
2. Verifique se a cópia de dados para o dispositivo foi concluída e a **Preparação para envio** foi executada com êxito.
3. Desligue o dispositivo e remova os cabos.
4. Coloque em segurança o cabo de alimentação enviado com o dispositivo atrás do mesmo.
5. Marque uma recolha online através da [Ligação da DHL](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference).

## <a name="japan"></a>[Japão](#tab/in-japan)

1. Guarde a caixa original usada para enviar o dispositivo para envio de devolução.
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

## <a name="singapore"></a>[Singapura](#tab/in-singapore)

1. Guarde a caixa original usada para enviar o dispositivo para envio de devolução.
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
   >
   > * Antes das 15:00, a recolha será agendada para o dia útil seguinte entre as 9:00 e as 13:00.
   > * Depois das 15:00, a recolha será agendada para o dia útil seguinte entre as 14:00 e as 18:00.  

## <a name="south-africa"></a>[África do Sul](#tab/in-sa)

1. Guarde a caixa original utilizada para embalar o dispositivo para envio de devolução.
2. Note o número de referência (número waybill) mostrado na UI web local do dispositivo. Este número é apresentado após o **funcionar do prepare-se para o navio** ter sido bem sucedido.
3. Descarregue e imprima a etiqueta de envio que está disponível na UI web local do dispositivo e afixe-a no pacote de envio.
4. Para reservar uma pick-up com DHL, escolha uma das seguintes opções:

    * Ligue para o centro de contacto de atendimento ao cliente antes das 14:00 em **+27(0) 11 9213600**, selecione a opção 1 e, em seguida, especifique o número de waybill.
    * Envie um e-mail para [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) com o seguinte modelo:

    ```output
    To: Priority.Support@dhl.com
    Subject: Pickup request for Microsoft Azure
    Body: Need pick up for the below shipment
      *  DHL tracking number (reference number/waybill number)
      *  Requested pickup date: yyyy/mm/dd;time:HH MM
    ```

    * Em vez disso, pode deixar o pacote no ponto de serviço DHL mais próximo.

5. Se encontrar algum problema, envie um e-mail [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) com detalhes da(s) edição que encontrou e coloque o número de waybill no Assunto: linha. Também pode ligar para +27(0)119213902.

## <a name="hong-kong"></a>[RAE de Hong Kong](#tab/in-hk)

1. Embale o dispositivo para devolução na embalagem original.
2. Note o número de referência (número de rastreio para envio inverso) mostrado na UI web local do dispositivo. Este número é apresentado após o **funcionar do prepare-se para o navio** ter sido bem sucedido.
3. Descarregue e imprima a etiqueta de envio que está disponível na UI web local do dispositivo e afixe-a no pacote de envio.
4. Coloque em segurança o cabo de alimentação enviado com o dispositivo atrás do mesmo.
5. Ligue para a linha direta da **Quantium Solutions** através do número **(852) 2318 1213** durante o horário de expediente (das 9:00 às 18:00, de segunda a sexta).  
6. Indique Microsoft Azure pickup (Recolha do Microsoft Azure) e o número de controlo (acima do código de barras) na guia de remessa para devolução para combinar uma recolha.
7. Recebe uma confirmação verbal do horário da recolha. Se o estafeta não chegar para recolha, ligue para a linha telefónica da Quantium Solutions para arranjos alternativos.
8. Ao reservar uma recolha com a Quantium, partilhe a confirmação com o [Microsoft Data Box Operations Asia](mailto:adbo@microsoft.com) com o seguinte modelo:

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

Caso se depare com algum problema, envie um e-mail para a Data Box Operations [adbo@microsoft.com](mailto:adbo@microsoft.com) Asia, fornecendo o nome do emprego no cabeçalho do assunto e o problema encontrado.

## <a name="self-managed"></a>[Envio Autónomo](#tab/in-selfmanaged)

Se estiver a utilizar o Data Box no Japão, Singapura, Coreia, Índia, África do Sul ou Europa Ocidental e tiver selecionado a opção de envio autónomo durante a criação da encomenda, siga estas instruções.

1. Tome nota do Código de autorização apresentado na página Preparação para envio da IU da Web local do Data Box após este passo ser concluído com êxito.
2. Desligue o dispositivo e remova os cabos. Coloque em segurança o cabo de alimentação enviado com o dispositivo na parte de trás do mesmo.
3. Envie um e-mail para a equipa de Operações da Caixa de Dados Azure utilizando o modelo abaixo quando estiver pronto para devolver o dispositivo.
    
    ```
    To: adbops@microsoft.com 
    Subject: Request for Azure Data Box drop-off for order: ‘orderName’ 
    Body: 
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```

---

## <a name="erasure-of-data-from-data-box"></a>Eliminação de dados do Data Box

Assim que o dispositivo chegar ao centro de dados Azure, a Caixa de Dados apaga os dados dos seus discos de acordo com as [diretrizes da Revisão 1 do NIST SP 800-88](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu sobre temas como:

> [!div class="checklist"]
> * Pré-requisitos
> * Preparar para enviar
> * Enviar o Data Box para a Microsoft
> * Eliminação de dados do Data Box

Avance para o próximo artigo para aprender a gerir a sua Caixa de Dados.

> [!div class="nextstepaction"]
> [Gerir a Caixa de Dados através do portal Azure](./data-box-portal-admin.md)


