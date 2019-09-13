---
title: Tutorial para enviar o Azure Data Box de volta | Microsoft Docs
description: Aprenda a enviar o Azure Data Box para a Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 194b90ab27d02c1fa3eb05bb3ddd78395d351599
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898182"
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


## <a name="in-australiatabin-australia"></a>[Na Austrália](#tab/in-australia)

Os datacenters do Azure na Austrália têm uma notificação de segurança adicional. Para todos os envios dirigidos aos mesmos, é necessária uma notificação antecipada. Siga os passos abaixo para os envios na Austrália.


1. Mantenha a embalagem original utilizada para enviar o dispositivo para a devolução.
2. Verifique se a cópia de dados para o dispositivo foi concluída e a **Preparação para envio** foi executada com êxito.
3. Desligue o dispositivo e remova os cabos.
4. Coloque em segurança o cabo de alimentação enviado com o dispositivo atrás do mesmo.
5. Envie um e-mail para a Quantium Solutions para solicitar a recolha. Mencione o número de referência do serviço especificado na portal do Azure. Utilize o seguinte modelo de e-mail: – *Solicitação de guia de remessa reversa com código TAU*. Confirme que inclui os seguintes detalhes no e-mail: 

    ```
    To: Azure@quantiumsolutions.com
    Subject: Pickup request for Azure｜Reference number：XXX XXX XXX
    Body: 
    - Company name：
    - Address:
    - Contact name:
    - Contact number:
    - Requested pickup date: mm/dd
    ```
6. A Quantium Solutions Australia enviará por e-mail uma guia de remessa de devolução.
7. Imprima a guia de devolução e cole-a na embalagem de envio.
8. Entregue o pacote à transportadora.

Se necessário, pode contactar o Apoio ao Cliente da Quantium Solutions através do e-mail Azure@quantiumsolutions.com ou por telefone.

Para questões relativas à encomenda por telefone:

- Envie, primeiro, um e-mail para recolha.
- Forneça o nome da encomenda por telefone.

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

::: zone target="docs"

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

Quando a Microsoft receber e analisar o dispositivo, o estado da encomenda será atualizado para **Received (Recebido)** . O dispositivo, em seguida, passa pela verificação física para verificar os danos ou sinais de adulteração.

Depois de a verificação estar concluída, o Data Box é ligado à rede no datacenter do Azure. A cópia de dados é iniciada automaticamente. Consoante o tamanho dos dados, a operação de cópia poderá demorar algumas horas a dias para concluir. Pode monitorizar o progresso da tarefa de cópia no portal.

Quando a cópia for concluída, o estado da encomenda será atualizado para **Completed (Concluído)** .

Confirme se os dados foram carregados para o Azure antes de os eliminar da origem. Os dados podem estar:

- Na(s) conta(s) do Armazenamento do Microsoft Azure. Quando copia os dados para o Data Box, consoante o tipo, os dados são carregados para um dos seguintes caminhos na sua conta de Armazenamento do Azure.

  - Para blobs de blocos e blobs de páginas: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Para Ficheiros do Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Em alternativa, pode aceder à sua conta de armazenamento do Azure no portal do Azure e navegar a partir daí.

- No(s) grupo(s) de recursos dos discos geridos. Ao criar discos geridos, os VHDs são carregados como blobs de páginas e convertidos em discos geridos. Os discos geridos são anexados aos grupos de recursos especificados no momento da criação da encomenda. 

    - Se a cópia para os discos geridos no Azure tiver sido realizada com êxito, poderá aceder aos **Detalhes da encomenda** no portal do Azure e anotar os grupos de recursos especificados para os discos geridos.

        ![Identificar os grupos de recursos dos discos geridos](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Aceda ao grupo de recursos anotado e localize os discos geridos.

        ![Disco gerido anexado aos grupos de recursos](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Se copiou um VHDX ou um VHD dinâmico/diferencial, o VHDX/VHD será carregado para a conta de armazenamento de teste como um blob de páginas, mas a conversão do VHD em disco gerido falhará. Aceda à **Conta de armazenamento > Blobs** de teste e, em seguida, selecione o contentor apropriado – SSD Standard, HDD Standard ou SSD Premium. Os VHDs são carregados como blobs de páginas na conta de armazenamento de teste.

::: zone-end

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

::: zone-end

## <a name="erasure-of-data-from-data-box"></a>Eliminação de dados do Data Box
 
Após o carregamento para o Azure, o Data Box elimina os dados dos discos de acordo com as [diretrizes SP 800-88 Revision 1 da NIST](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone target="docs"

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box, como:

> [!div class="checklist"]
> * Pré-requisitos
> * Preparar para enviar
> * Enviar o Data Box para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminação de dados do Data Box

Avance para o artigo seguinte para saber como gerir o Data Box através da IU da Web local.

> [!div class="nextstepaction"]
> [Utilizar a IU da Web local para administrar um Azure Data Box](./data-box-local-web-ui-admin.md)

::: zone-end


