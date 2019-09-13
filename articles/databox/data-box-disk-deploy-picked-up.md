---
title: Tutorial para enviar o Azure Data Box Disk de volta | Microsoft Docs
description: Utilize este tutorial para aprender enviar o Azure Data Box Disk para a Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 09/11/2019
ms.author: alkohli
ms.localizationpriority: high
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 03ca6163b4f788d50f7f27a2fcaf2c9b6c02d84c
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910107"
---
::: zone target="chromeless"

## <a name="return-azure-data-box-disk"></a>Devolver o Azure Data Box Disk 

::: zone-end

::: zone target="docs"

# <a name="tutorial-return-azure-data-box-disk"></a>Tutorial: Devolver o Azure Data Box Disk 

Este tutorial descreve como agendar uma recolha para devolver o seu Azure Data Box Disk. As instruções de recolha dependem da localização a partir da qual pretende devolver o dispositivo. 

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Enviar o Data Box Disk para a Microsoft
> * Recolha do Data Box Disk em diferentes regiões

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que concluiu o [Tutorial: Copiar dados para o Azure Data Box Disk e verificar](data-box-disk-deploy-copy-data.md).


## <a name="ship-data-box-disk-back"></a>Enviar o Data Box Disk de volta

::: zone-end

1. Após a validação de dados ser concluída, desligue os discos. Remova os cabos de ligação.
2. Embrulhe todos os discos e os cabos de ligação em plástico-bolha e coloque-os na caixa de envio. Podem aplicar-se custos se faltarem os acessórios.
    - Reutilize a embalagem do envio inicial.  
    - Recomendamos que embrulhe os discos em plástico-bolha e que o prenda bem.
    - Assegure que o plástico-bolha envolve bem os discos para reduzir os movimentos dentro da caixa.

A localização a partir da qual pretende devolver o dispositivo determina os passos seguintes. As instruções são diferentes para os E.U.A./Canadá, a União Europeia (UE), a Austrália ou os países da Ásia.

### <a name="in-us-or-canadatabin-us-or-canada"></a>[Nos E.U.A. ou Canadá](#tab/in-us-or-canada)

Se pretender devolver o dispositivo nos E.U.A. ou no Canadá, siga os passos abaixo.

1. Utilize a etiqueta de envio para devolução, na capa de plástico afixada na caixa. Se a guia estiver danificada ou a tiver perdido:
    - Aceda a **Descrição Geral > Transferir guia de remessa** e transfira uma guia de remessa para devolução.
    - Afixe a guia ao dispositivo.

2. Feche a caixa de envio e certifique-se de que a etiqueta para envio de devolução se encontra visível.
3. Agende uma recolha com a UPS. Para agendar uma recolha:

    - Ligue para o ponto UPS local (número gratuito específico do seu país/região).
    - Durante a chamada, indique o número de rastreio do envio de devolução apresentado na guia impressa.
    - Se não indicar o número de rastreio, terá de pagar uma taxa adicional à UPS na recolha.
    - Em vez de agendar a recolha, também pode entregar o Data Box Disk na localização de entrega mais próxima.

### <a name="in-europetabin-europe"></a>[Na Europa](#tab/in-europe)

Se pretender devolver o dispositivo na Europa, siga os passos abaixo.

1. Utilize a etiqueta de envio para devolução, na capa de plástico afixada na caixa. Se a guia estiver danificada ou a tiver perdido:
    - Aceda a **Descrição Geral > Transferir guia de remessa** e transfira uma guia de remessa para devolução.
    - Afixe a guia ao dispositivo.

2. Feche a caixa de envio e certifique-se de que a etiqueta para envio de devolução se encontra visível.
3. Se estiver a devolver o dispositivo na Europa com a DHL, solicite uma recolha à DHL acedendo ao respetivo site e especificando o número de carta de porte aéreo.
4. Aceda ao site da DHL Express do seu país/região e selecione **Book a Courier Collection (Agendar uma Recolha por Estafeta) > eReturn Shipment (Envio eReturn)** .    
3. Especifique o número de carta de porte aéreo e clique em **Schedule Pickup (Agendar Recolha)** para marcar uma recolha.

### <a name="in-australiatabin-australia"></a>[Na Austrália](#tab/in-australia)

Os datacenters do Azure na Austrália têm uma notificação de segurança adicional. Para todos os envios dirigidos aos mesmos, é necessária uma notificação antecipada. Siga os passos abaixo para recolhas na Austrália.

1. Utilize a guia de remessa para devolução fornecida e confirme que o código TAU (número de referência) se encontra na mesma. Se não tiver a guia de remessa fornecida ou se deparar com outros problemas, envie um e-mail para o departamento de [Operações Data Box na Ásia](mailto:adbo@microsoft.com). Indique o nome da encomenda no assunto e os detalhes do seu problema.
3. Afixe a guia à caixa. 
4. Marque uma recolha online através da ligação https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference. 

### <a name="in-japan-koreatabin-japan-korea"></a>[No Japão, Coreia do Sul](#tab/in-japan-korea)

Eis as instruções de recolha para o Japão e Coreia do Sul.

#### <a name="pick-up-in-japan"></a>Recolha no Japão

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
    - Caso a nota de expedição Chakubarai da Japan Post não tenha sido incluída ou esteja em falta, indique-o neste e-mail. A Quantium Solutions Japan irá solicitar à Japan Post que apresente a nota de expedição na recolha.
    - Se tiver várias encomendas, envie um e-mail para garantir a recolha individual.

3. Depois de marcar uma recolha, recebe uma confirmação por e-mail da Quantium Solutions. Esta confirmação também inclui informações sobre a nota de expedição Chakubarai.

Se necessário, pode contactar o Apoio ao Cliente da Quantium Solutions (em japonês) por: 

- E-mail：Customerservice.JP@quantiumsolutions.com 
- Telefone: 03-5755-0150 

#### <a name="pick-up-in-korea"></a>Recolha na Coreia

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


### <a name="in-singaporetabin-singapore"></a>[Em Singapura](#tab/in-singapore)

1. Imprima a guia de remessa e afixe-a à caixa. Se a guia estiver danificada ou a tiver perdido:
    - Aceda a **Descrição Geral > Transferir guia de remessa** e obtenha uma guia de remessa para devolução.
    - Afixe a guia ao dispositivo. Certifique-se de que a guia está visível.

2. Para solicitar a recolha:
    - Ligue para a linha direta da **SingPost** através do número **6845 6485** durante o horário de expediente (das 09:00 às 17:00, de segunda a sexta-feira).  
    - Indique *Microsoft Azure pickup* (Recolha do Microsoft Azure) e o número do pedido de serviço (número de rastreio na guia de remessa para devolução) para combinar uma recolha. 
    - Recebe uma confirmação verbal do horário da recolha. 
    - Se o estafeta não efetuar a recolha, ligue para a **SingPost** através do número **6845 6485** para combinar uma alternativa. 
3. Entregue a caixa ao estafeta. 


::: zone target="docs"

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box Disk, como:

> [!div class="checklist"]
> * Enviar o Data Box Disk para a Microsoft
> * Recolha do Data Box Disk em diferentes regiões

Avance para os procedimentos seguintes para saber como verificar o carregamento de dados do Data Box Disk para a conta de Armazenamento do Microsoft Azure.

> [!div class="nextstepaction"]
> [Verificar o carregamento de dados do Azure Data Box Disk](./data-box-disk-deploy-picked-up.md)

::: zone-end




