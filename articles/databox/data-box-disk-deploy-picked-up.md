---
title: Tutorial para enviar o Azure Data Box Disk de volta | Microsoft Docs
description: Neste tutorial, saiba como devolver o seu Azure Data Box Disk. As instruções de recolha dependem da localização a partir da qual quer devolver o dispositivo.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 11/20/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: f1d667f55a8be1b701fda6240895dd8f96315cff
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94986322"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-disk"></a>Tutorial: Devolver o Azure Data Box Disk

Este tutorial descreve como devolver o seu Azure Data Box Disk. As instruções de recolha dependem da localização a partir da qual quer devolver o dispositivo.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
>
> * Enviar o Data Box Disk para a Microsoft

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que concluiu o [Tutorial: Copiar dados para o Azure Data Box Disk e verificar](data-box-disk-deploy-copy-data.md).

## <a name="ship-data-box-disk-back"></a>Enviar o Data Box Disk de volta

::: zone-end

::: zone target="chromeless"

## <a name="return-azure-data-box-disk"></a>Devolver o Azure Data Box Disk

::: zone-end

1. Após a validação de dados ser concluída, desligue os discos. Remova os cabos de ligação.
2. Embrulhe todos os discos e os cabos de ligação em plástico-bolha e coloque-os na caixa de envio. Podem aplicar-se custos se faltarem os acessórios.
    - Reutilize a embalagem do envio inicial.  
    - Recomendamos que embrulhe os discos em plástico-bolha e que o prenda bem.
    - Assegure que o plástico-bolha envolve bem os discos para reduzir os movimentos dentro da caixa.

A localização a partir da qual pretende devolver o dispositivo determina os passos seguintes. As instruções são diferentes para os E.U.A./Canadá, a União Europeia (UE), a Austrália ou os países/regiões da Ásia.

### <a name="us-or-canada"></a>[EUA ou Canadá](#tab/in-us-or-canada)

Se pretender devolver o dispositivo nos E.U.A. ou no Canadá, siga os passos abaixo.

1. Utilize a guia de remessa para devolução com a capa de plástico afixada à caixa. Se a guia estiver danificada ou a tiver perdido:
    - Aceda a **Descrição Geral > Transferir guia de remessa** e transfira uma guia de remessa para devolução.
    - Afixe a guia ao dispositivo.

2. Feche a caixa de envio e certifique-se de que a etiqueta para envio de devolução se encontra visível.
3. Agende uma recolha com a UPS. Para agendar uma recolha:

    - Ligue para o ponto UPS local (número gratuito específico do seu país/região).
    - Durante a chamada, indique o número de rastreio do envio de devolução apresentado na guia impressa.
    - Se não indicar o número de rastreio, terá de pagar uma taxa adicional à UPS na recolha.
    - Em vez de agendar a recolha, também pode entregar o Data Box Disk na localização de entrega mais próxima.


### <a name="europe-or-uk"></a>[Europa ou Reino Unido](#tab/in-europe-or-uk)

Se pretender devolver o dispositivo na Europa ou no Reino Unido, siga os passos abaixo.

1. Utilize a guia de remessa para devolução com a capa de plástico afixada à caixa. Se a guia estiver danificada ou a tiver perdido:
    - Aceda a **Descrição Geral > Transferir guia de remessa** e transfira uma guia de remessa para devolução.
    - Afixe a guia ao dispositivo.

2. Feche a caixa de envio e certifique-se de que a guia de remessa está visível.
3. Aceda ao site da DHL Express no seu país/região e selecione **Agendar uma recolha**. Em **Necessita de criar uma etiqueta de envio**, selecione **Não** > **Eu tenho um número de carta de porte DHL**.
4. Especifique o número de carta de porte e clique em **Agendar uma recolha** para marcar a recolha.

### <a name="australia"></a>[Austrália](#tab/in-australia)

Os datacenters do Azure na Austrália têm uma notificação de segurança adicional. Para todos os envios dirigidos aos mesmos, é necessária uma notificação antecipada. Siga os passos abaixo para recolhas na Austrália.

1. Utilize a guia de remessa para devolução fornecida e confirme que o código TAU (número de referência) se encontra na mesma. Se não tiver a guia de remessa fornecida ou se deparar com outros problemas, envie um e-mail para o departamento de [Operações Data Box na Ásia](mailto:adbo@microsoft.com). Indique o nome da encomenda no assunto e os detalhes do seu problema.
2. Afixe a guia à caixa.
3. Marque uma recolha online através da ligação https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference.

### <a name="japan"></a>[Japão](#tab/in-japan)

1. Escreva o nome e o endereço da sua empresa na nota de expedição como as informações do remetente.
2. Utilize o modelo que se segue para enviar um e-mail à Quantium Solutions.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Microsoft Azure Data Box Disk｜Job Name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
        a. 08：00-13：00
        b. 13：00-15：00
        c. 15：00-17：00
        d. 17：00-19：00
    ```
    - **Se pretender que a recolha seja efetuada em Osaka**, modifique o assunto do modelo de e-mail para: `Pickup request for Microsoft Azure OSA`.
    - Caso a nota de expedição Chakubarai da Japan Post não tenha sido incluída ou esteja em falta, indique-o no e-mail. A Quantium Solutions Japan irá solicitar à Japan Post que apresente a nota de expedição na recolha.
    - Se tiver várias encomendas, envie um e-mail para garantir a recolha individual.

3. Depois de marcar uma recolha, recebe uma confirmação por e-mail da Quantium Solutions. Esta confirmação também inclui informações sobre a nota de expedição Chakubarai.

Se necessário, pode contactar o Apoio ao Cliente da Quantium Solutions (em japonês) por: 

- E-mail：[Customerservice.JP@quantiumsolutions.com](mailto:Customerservice.JP@quantiumsolutions.com)
- Telefone: 03-5755-0150 

### <a name="korea"></a>[Coreia](#tab/in-korea)

1. Certifique-se de que inclui a nota de expedição de devolução.
2. Para solicitar a recolha quando a nota de expedição estiver presente:
    1. Ligue para a linha direta da *Quantium Solutions International* através do número 070-8231-1418 durante o horário de expediente (das 10:00 às 17:00, de segunda a sexta-feira). Indique *Microsoft Azure pickup* (Recolha do Microsoft Azure) e o número do pedido de serviço para combinar uma recolha.  
    2. Se a linha direta estiver ocupada, envie um e-mail para `microsoft@rocketparcel.com` com o assunto *Microsoft Azure Pickup* (Recolha do Microsoft Azure) e o número do pedido de serviço como referência.
    3. Se o estafeta não efetuar a recolha, ligue para a linha direta da *Quantium Solutions International* para combinar uma alternativa.
    4. Recebe uma confirmação por e-mail do horário da recolha.
3. Siga este passo apenas se a nota de expedição não estiver presente. Para solicitar a recolha:
    1. Ligue para a linha direta da *Quantium Solutions International* através do número 070-8231-1418 durante o horário de expediente (das 10:00 às 17:00, de segunda a sexta-feira). Indique *Microsoft Azure pickup* (Recolha do Microsoft Azure) e o número do pedido de serviço para combinar uma recolha. Especifique que precisa de uma nova nota de expedição para combinar uma recolha. Forneça as informações do remetente (cliente), as informações do destinatário (datacenter do Azure) e o número de referência (número do pedido de serviço). 
    2. Se a linha direta estiver ocupada, envie um e-mail para `microsoft@rocketparcel.com` com o assunto *Microsoft Azure Pickup* (Recolha do Microsoft Azure) e o número do pedido de serviço como referência.
    3. Se o estafeta não efetuar a recolha, ligue para a linha direta da *Quantium Solutions International* para combinar uma alternativa.
    4. Se efetuar o pedido por telefone, recebe uma confirmação verbal.

### <a name="singapore"></a>[Singapura](#tab/in-singapore)

1. Imprima a guia de remessa e afixe-a à caixa. Se a guia estiver danificada ou a tiver perdido:
    - Aceda a **Descrição Geral > Transferir guia de remessa** e obtenha uma guia de remessa para devolução.
    - Afixe a guia ao dispositivo. Certifique-se de que a guia está visível.

2. Para pedir uma recolha, envie um e-mail para o Suporte ao Cliente da SingPost através do seguinte modelo com o número de encomenda (o número de encomenda encontra-se na guia de devolução incluída no pacote entregue).

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pickup - XZ00001234567
    Body:
     a.    Requestor name
     b.    Requestor contact number
     c.    Requestor collection address
     d.    Preferred collection date
    ```

   > [!NOTE]
   > Para pedidos de reserva recebidos num dia útil:
   >
   > * Antes das 15:00, a recolha será agendada para o dia útil seguinte entre as 9:00 e as 13:00.
   > * Depois das 15:00, a recolha será agendada para o dia útil seguinte entre as 14:00 e as 18:00.

   Se se deparar com algum problema, contacte o Data Box Operations Asia através de adbo@microsoft.com. Indique o nome da tarefa no cabeçalho do assunto e o problema encontrado.

3. Entregue a caixa ao estafeta.

### <a name="south-africa"></a>[África do Sul](#tab/in-sa)

Se quiser devolver o dispositivo na África do Sul, siga os passos abaixo.

1. Anexe a guia de remessa fornecida na embalagem. Esta etiqueta contém o número de controlo. Se faltar a guia de remessa, pode transferir uma nova em **Descrição geral > Transferir guia de remessa**.

2. Feche a caixa de envio e certifique-se de que a etiqueta para envio de devolução se encontra visível.

3. Peça um código de devolução às Operações do Azure Data Box. Para devolver a embalagem ao datacenter, é necessário um código de devolução. Envie o e-mail para [adbops@microsoft.com](mailto:adbops@microsoft.com). Aponte este código na guia de remessa, junto à morada de devolução, para que esteja claramente visível.

4. Agende a recolha com a DHL através de um dos seguintes métodos:
   * Agende uma recolha online em [DHL Express South Africa, **Schedule a Pickup**](https://mydhl.express.dhl/za/en/schedule-pickup.html#/schedule-pickup#label-reference) (Agendar Recolha).
   * Envie um e-mail para [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) com o seguinte modelo:

     ```output
     To: Priority.Support@dhl.com
     Subject: Pickup request for Microsoft Azure
     Body: Need pick up for the below shipment
       *  DHL tracking number: (reference number/waybill number)
       *  Requested pickup date: yyyy/mm/dd;time:HH MM
       *  Shipper contact: (company name)
       *  Contact person: 
       *  Phone number: 
       *  Full physical address: 
       *  Item to be collected: Azure Dt
     ```

    * Em alternativa, deixe a embalagem no ponto de serviço da DHL mais próximo.

5. Se encontrar algum problema, envie um e-mail [Priority.Support@dhl.com](mailto:Priority.Support@dhl.com) com detalhes sobre o problema encontrado e coloque o número da carta de porte na linha de assunto. Também pode ligar para +27(0)119213902.

### <a name="china"></a>[China](#tab/in-china)

Se quiser devolver o dispositivo na China, siga os passos abaixo.

1. Anexe a guia de remessa fornecida e cole-a na caixa. Esta etiqueta contém o número de controlo. Se faltar a guia de remessa, pode transferir uma nova em **Descrição geral > Transferir guia de remessa**.

2. Envie um e-mail para o Apoio ao Cliente Premier da FedEx e forneça-lhes o número de controlo (número de referência do envio) para organizar a recolha através do seguinte modelo de e-mail:  

   ```output
   To: ying.bao@fedex.com;739951@fedex.com
   Subject: Pickup request for Microsoft Azure : Order Name
   Body: Need pick up for the below shipment
   * FedEx tracking number (reference number)
   * Requested pickup date：yyyy/mm/dd; time: HH MM
   ```

3. Receberá uma confirmação por e-mail da FedEx após concluir a marcação da recolha.  

4. Se encontrar algum problema, envie um e-mail [DL-DC-SHA@oe.21vianet.com](mailto:DL-DC-SHA@oe.21vianet.com) com detalhes sobre o problema encontrado e o assunto com o nome da encomenda.

#### <a name="premier-customer-care-contact-information"></a>Informações de contacto do Apoio ao Cliente Premier

<ins>Principal</ins>

| Informações de contacto | Detalhes |
|---|---|
|Nome:       | Bao Ying|
|Designação | Representante Sénior na OneCall |
|Telefone:      | 400.889.6066 ext. 3693 |
|E-mail:     | [ying.bao@fedex.com](mailto:ying.bao@fedex.com) |

<ins>Cópia de segurança</ins>

| Informações de contacto | Detalhes |
|---|---|
|Nome:       | He Xun|
|Designação | Representante na OneCall |
|Telefone:      | 400.889.6066 ext. 3603 |
|E-mail:     | [739951@fedex.com](mailto:739951@fedex.com) |

### <a name="self-managed"></a>[Envio Autónomo](#tab/in-selfmanaged)

Se estiver a utilizar o Data Box Disk nas seguintes regiões: US Government, Japão, Singapura, Coreia, Europa Ocidental, África do Sul ou Índia, e tiver selecionado a opção de envio autónomo durante a criação da encomenda, siga estas instruções.

1. Aceda ao painel **Descrição geral** da encomenda no portal do Azure. Siga as instruções apresentadas quando selecionar **Agendar recolha**. Deverá ver um código de Autorização, o qual é utilizado no momento da entrega da encomenda.

2. Envie um e-mail para a equipa de Operações do Azure Data Box através do seguinte modelo quando estiver pronto para devolver o dispositivo.

    ```
    To: adbops@microsoft.com
    Subject: Request for Azure Data Box Disk drop-off for order: 'orderName'
    Body:
     a. Order name
     b. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```
3. A equipa de Operações do Azure Data Box trabalhará consigo para organizar a entrega no Datacenter do Azure.

---

::: zone target="docs"

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box Disk, como:

> [!div class="checklist"]
>
> * Enviar o Data Box Disk para a Microsoft

Avance para os procedimentos seguintes para saber como verificar o carregamento de dados do Data Box Disk para a conta de Armazenamento do Microsoft Azure.

> [!div class="nextstepaction"]
> [Verificar o carregamento de dados do Azure Data Box Disk](./data-box-disk-deploy-upload-verify.md)

::: zone-end
