---
title: Tutorial para enviar o Azure Data Box de volta | Microsoft Docs
description: Aprenda a enviar o Azure Data Box para a Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 1c375370ec6bae2775ad758688825b92cbbbca50
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74407029"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Tutorial: Devolver o Azure Data Box e verificar os dados carregados para o Azure

::: zone-end

::: zone target="chromeless"

# <a name="return-data-box-and-verify-data-upload-to-azure"></a>Devolver o Data Box e verificar os dados carregados para o Azure

::: zone-end

::: zone target="docs"

Este tutorial descreve como devolver o Azure Data Box e certificar-se de que os dados foram carregados para o Azure.

Neste tutorial, irá obter informações sobre tópicos, como:

> [!div class="checklist"]
> * Pré-requisitos
> * Preparar para enviar
> * Enviar o Data Box para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminação de dados do Data Box

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, confirme que:

- Concluiu o [Tutorial: Copiar dados para o Azure Data Box e verificar](data-box-deploy-copy-data.md). 
- As tarefas de cópia foram concluídas. A preparação para envio não poderá ser executada se as tarefas de cópia estiverem em curso.

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

## <a name="in-us-canada-europetabin-us-canada-europe"></a>[Nos E.U.A., Canadá, Europa](#tab/in-us-canada-europe)

Se quiser devolver o dispositivo nos EUA, Canadá ou Europa, siga os passos abaixo.

1. Confirme que o dispositivo está desligado e os cabos foram removidos. 
2. Coloque em segurança o cabo de alimentação enviado com o dispositivo atrás do mesmo.
3. Certifique-se de que a etiqueta de envio é apresentada no ecrã de tinta eletrónica e agende uma recolha com a sua operadora. Se a guia estiver danificada, tiver sido perdida ou não aparecer na apresentação da E-ink, contacte o Suporte da Microsoft. Se a equipa do Suporte o sugerir, poderá aceder a **Visão geral > Transferir guia de remessa** no portal do Azure. Transfira a guia de remessa e cole-a no dispositivo. 
4. Se estiver a devolver o dispositivo, contacte a UPS para agendar uma recolha. Para agendar uma recolha:

    - Ligue para o ponto UPS local (número gratuito específico do seu país/região).
    - Durante a chamada, indique o número de rastreio do envio de devolução mostrado na apresentação da E-ink ou na guia impressa.
    - Se não indicar o número de rastreio, terá de pagar uma taxa adicional à UPS na recolha.

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


## <a name="in-australiatabin-australia"></a>[Na Austrália](#tab/in-australia)

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

## <a name="in-japantabin-japan"></a>[No Japão](#tab/in-japan) 

1. Mantenha a embalagem original utilizada para enviar o dispositivo para a devolução.
2. Desligue o dispositivo e remova os cabos.
3. Coloque em segurança o cabo de alimentação enviado com o dispositivo atrás do mesmo.
4. Escreva o nome e o endereço da sua empresa na nota de expedição como as informações do remetente.
5. Utilize o modelo que se segue para enviar um e-mail à Quantium Solutions.

    - Caso a nota de expedição Chakubarai da Japan Post não tenha sido incluída ou esteja em falta, indique-o neste e-mail. A Quantium Solutions Japan irá solicitar à Japan Post que apresente a nota de expedição na recolha.
    - Se tiver várias encomendas, envie um e-mail para garantir a recolha individual.

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

3. Depois de marcar uma recolha, recebe uma confirmação por e-mail da Quantium Solutions. Esta confirmação também inclui informações sobre a nota de expedição Chakubarai.

Se necessário, pode contactar o Apoio ao Cliente da Quantium Solutions (em japonês) por: 

- E-mail：Customerservice.JP@quantiumsolutions.com 
- Telefone: 03-5755-0150 

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>Eliminação de dados do Data Box
 
Após o carregamento para o Azure, o Data Box elimina os dados dos discos de acordo com as [diretrizes SP 800-88 Revision 1 da NIST](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end



