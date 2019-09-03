---
title: Tutorial para enviar Disco do Azure Data Box de volta | Microsoft Docs
description: Utilize este tutorial para aprender enviar o Azure Data Box Disk para a Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 09/03/2019
ms.localizationpriority: high
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 259c20aead3bbe7b44c59efe031a079ba80e2905
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232964"
---
::: zone target="chromeless"

## <a name="return-azure-data-box-disk"></a>Disco do Azure Data Box de retorno 

::: zone-end

::: zone target="docs"

# <a name="tutorial-return-azure-data-box-disk"></a>Tutorial: Disco do Azure Data Box de retorno 

Este tutorial descreve como agendar uma seleção para retornar o Disco do Azure Data Box. As instruções de seleção dependem de onde você está retornando o dispositivo. 

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Enviar o Data Box Disk para a Microsoft
> * Pegue Disco do Data Box em regiões diferentes

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você concluiu o [tutorial: Copie dados para Disco do Azure Data Box e verifique](data-box-disk-deploy-copy-data.md).


## <a name="ship-data-box-disk-back"></a>Enviar o Data Box Disk de volta

::: zone-end

1. Após a validação de dados ser concluída, desligue os discos. Remova os cabos de ligação.
2. Embrulhe todos os discos e os cabos de ligação em plástico-bolha e coloque-os na caixa de envio. Os encargos poderão ser aplicados se os acessórios estiverem ausentes.
    - Reutilize o empacotamento da remessa inicial.  
    - É recomendável que você empacote os discos usando um encapsulamento bolha bem protegido.
    - Certifique-se de que o ajuste esteja ajustado para reduzir os movimentos na caixa.

As próximas etapas são determinadas por onde você está retornando o dispositivo. As instruções são diferentes para nós/Canadá, União Europeia (UE), Austrália ou países na Ásia.

### <a name="pick-up-in-us-canada"></a>Escolha nos EUA, Canadá

Execute as etapas a seguir se retornar o dispositivo nos EUA ou no Canadá.

1. Utilize a etiqueta de envio para devolução, na capa de plástico afixada na caixa. Se o rótulo estiver danificado ou perdido:
    - Acesse **visão geral > baixar o rótulo de envio** e baixar um rótulo de remessa de retorno.
    - Afixar o rótulo no dispositivo.

2. Feche a caixa de envio e certifique-se de que a etiqueta para envio de devolução se encontra visível.
3. Agendar uma retirada com UPS. Para agendar uma retirada:

    - Chame o no-break local (número gratuito de ligação específica de país/região).
    - Em sua chamada, entre em contato com o número de acompanhamento de remessa reversa, conforme mostrado no seu rótulo impresso.
    - Se o número de rastreamento não estiver entre aspas, o UPS exigirá que você pague um encargo adicional durante a retirada.
    - Em vez de agendar a retirada, você também pode remover o Disco do Data Box no local de distribuição mais próximo.

### <a name="pick-up-in-europe"></a>Escolha na Europa

Execute as etapas a seguir se retornar o dispositivo na Europa.

1. Utilize a etiqueta de envio para devolução, na capa de plástico afixada na caixa. Se o rótulo estiver danificado ou perdido:
    - Acesse **visão geral > baixar o rótulo de envio** e baixar um rótulo de remessa de retorno.
    - Afixar o rótulo no dispositivo.

2. Feche a caixa de envio e certifique-se de que a etiqueta para envio de devolução se encontra visível.
3. Se estiver a devolver o dispositivo na Europa com a DHL, solicite uma recolha à DHL acedendo ao respetivo site e especificando o número de carta de porte aéreo.
4. Vá para o site país/região DHL Express e escolha **agendar uma coleção do Courier > remessa do eReturn**.    
3. Especifique o número de carta de porte aéreo e clique em **Schedule Pickup (Agendar Recolha)** para marcar uma recolha.

### <a name="pick-up-in-australia"></a>Escolha na Austrália

Os data centers do Azure na Austrália têm uma notificação de segurança adicional. Todas as remessas de entrada devem ter uma notificação avançada. Execute as etapas a seguir para a retirada na Austrália.

1. Email `adbops@microsoft.com` para solicitar o rótulo de remessa com ID de entrada exclusiva ou o código Tau. Coloque a solicitação pelo menos três dias antes da data de remessa planejada para obter o rótulo no tempo.
2. O assunto do email deve ser *solicitação de etiqueta de envio reversa com código Tau*. Certifique-se de incluir os seguintes detalhes no email: 

    - Nome do pedido
    - Endereço
    - Nome do contacto

### <a name="pick-up-in-asia"></a>Escolha na Ásia

As instruções de retirada são diferentes para o Japão, a Coreia e a Cingapura.

#### <a name="pick-up-in-japan"></a>Escolha no Japão

1. Grave as informações de nome e endereço da sua empresa na nota de consignação como as informações do remetente.
2. Envie uma solução de Quantium de email usando o modelo de email a seguir.

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
    - **Se você estiver selecionando em Osaka**, modifique o assunto no modelo de email para: `Pickup request for Microsoft Azure OSA`.
    - Se a nota de consignação do Chakubarai do Japão não tiver sido incluída ou estiver ausente, observe que neste email. As soluções Quantium do Japão solicitarão o lançamento do Japão para trazer a nota de consignação na retirada.
    - Se você tiver vários pedidos, envie um email para garantir a retirada individual.

3. Receba uma confirmação por email das soluções Quantium depois de ter reservado uma retirada. A confirmação por email também inclui informações sobre a nota de consignação do Chakubarai.

Se necessário, você pode entrar em contato com o suporte da solução Quantium (idioma japonês) nas seguintes informações: 

- Email:Customerservice.JP@quantiumsolutions.com 
- Telefone: 03-5755-0150 

#### <a name="pick-up-in-korea"></a>Escolha na Coreia

1. Certifique-se de incluir a nota de consignação de retorno.
2. Para solicitar retirada quando a nota de consignação estiver presente:
    1. Ligue para a linha direta do *Quantium Solutions* em 070-8231-1418 durante as horas do escritório (10 a.m. às 17:00, de segunda-feira a sexta-feira). Quote *Microsoft Azure retirada* e o número da solicitação de serviço para organizar uma coleção.  
    2. Se a linha direta estiver ocupada, `microsoft@rocketparcel.com`envie um email com o assunto do email *Microsoft Azure a retirada* e o número da solicitação de serviço como referência.
    3. Se a Courier não chegar à coleta, chame a linha direta do *Quantium Solutions International* para obter as disposições alternativas. 
    4. Você receberá uma confirmação por email para a agenda de retirada.
3. Execute esta etapa somente se a nota de consignação não estiver presente. Para solicitar retirada:
    1. Ligue para a linha direta do *Quantium Solutions* em 070-8231-1418 durante as horas do escritório (10 a.m. às 17:00, de segunda-feira a sexta-feira). Quote *Microsoft Azure retirada* e o número da solicitação de serviço para organizar uma coleção. Especifique que você precisa de uma nova nota de assinatura para organizar uma coleção. Forneça o remetente (cliente), as informações do receptor (Datacenter do Azure) e o número de referência (número da solicitação de serviço). 
    2. Se a linha direta estiver ocupada, `microsoft@rocketparcel.com`envie um email com o assunto do email *Microsoft Azure a retirada* e o número da solicitação de serviço como referência.
    3. Se a Courier não chegar à coleta, chame a linha direta do *Quantium Solutions International* para obter as disposições alternativas. 
    4. Você receberá uma confirmação verbal se a solicitação for feita por telefone.

#### <a name="pick-up-in-singapore"></a>Escolha em Cingapura

1. Imprima o rótulo de remessa e anexe na caixa. Se o rótulo estiver danificado ou perdido:
    - Acesse **visão geral > baixar o rótulo de envio** e obter um rótulo de envio de devolução.
    - Afixar o rótulo no dispositivo. Verifique se o rótulo está visível.

2. Para solicitar retirada:
    - Ligue para a linha direta da **Singpost** às **6845 6485** durante as horas do escritório (9h às 17:00, de segunda-feira a sexta-feira).  
    - Quote *Microsoft Azure retirada* e o número da solicitação de serviço (número de rastreamento no rótulo de envio de retorno) para organizar uma coleção. 
    - Você receberá uma confirmação verbal para a agenda de retirada. 
    - Se a Courier não chegar à coleção, chame **Singpost** em **6845 6485** para organizações alternativas. 
3. Passe para a Courier. 


::: zone target="docs"

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box Disk, como:

> [!div class="checklist"]
> * Enviar o Data Box Disk para a Microsoft
> * Pegue Disco do Data Box em regiões diferentes

Avance para o próximo passo para saber como verificar o carregamento de dados de Disco do Data Box para a conta de armazenamento do Azure.

> [!div class="nextstepaction"]
> [Verificar o carregamento de dados do Disco do Azure Data Box](./data-box-disk-deploy-picked-up.md)

::: zone-end




