---
title: Tutorial para enviar Azure Data Box de volta | Microsoft Docs
description: Aprenda a enviar o Azure Data Box para a Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 8/27/2019
ms.author: alkohli
ms.openlocfilehash: 368439d6e15d6c94bbb96d67fcb48ab006234c95
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098833"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Tutorial: Retornar Azure Data Box e verificar o carregamento de dados no Azure

::: zone-end

::: zone target="chromeless"

# <a name="return-data-box-and-verify-data-upload-to-azure"></a>Retornar Data Box e verificar o carregamento de dados no Azure

::: zone-end

::: zone target="docs"

Este tutorial descreve como devolver o Azure Data Box e certificar-se de que os dados foram carregados para o Azure.

Neste tutorial, irá obter informações sobre tópicos, como:

> [!div class="checklist"]
> * Pré-requisitos
> * preparação para envioPreparação para envio
> * Enviar o Data Box para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminação de dados do Data Box

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se:

- Você concluiu o [tutorial: Copie dados para Azure Data Box e verifique](data-box-deploy-copy-data.md). 
- Trabalhos de cópia concluídos. Preparação para o envio não poderá ser executado se os trabalhos de cópia estiverem em andamento.

## <a name="prepare-to-ship"></a>preparação para envioPreparação para envio

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

::: zone-end

::: zone target="chromeless"

Após a conclusão da cópia de dados, você prepara e envia o dispositivo. Quando o dispositivo atinge o datacenter do Azure, os dados são carregados automaticamente no Azure.

## <a name="prepare-to-ship"></a>preparação para envioPreparação para envio

Antes de se preparar para enviar, verifique se os trabalhos de cópia estão concluídos.

1. Aceda à página **Preparar para enviar** na IU da Web local e comece a preparação do envio. 
2. Desative o dispositivo a partir da IU da Web local. Remova os cabos do dispositivo. 

As próximas etapas são determinadas por onde você está retornando o dispositivo.

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>Enviar o Data Box de volta

Verifique se a cópia de dados para o dispositivo foi concluída e **preparação para o envio** execução foi bem-sucedida. Com base na região em que você está enviando o dispositivo, o procedimento é diferente.

::: zone-end

## <a name="ship-in-us-canada-europe"></a>Envie para nós, Canadá, Europa

Siga as etapas abaixo se retornar o dispositivo nos EUA, no Canadá ou na Europa.

1. Verifique se o dispositivo está desligado e se os cabos foram removidos. 
2. Coloque em segurança o cabo de alimentação enviado com o dispositivo atrás do mesmo.
3. Certifique-se de que a etiqueta de envio é apresentada no ecrã de tinta eletrónica e agende uma recolha com a sua operadora. Se o rótulo estiver danificado ou perdido ou não for exibido na tela de E-Ink, entre em contato com Suporte da Microsoft. Se o suporte sugerir, você poderá ir para **visão geral > baixar o rótulo de envio** no portal do Azure. Baixe o rótulo de envio e afixado no dispositivo. 
4. Agendar uma retirada com UPS se retornar o dispositivo. Para agendar uma retirada:

    - Chame o no-break local (número gratuito de ligação específica de país/região).
    - Em sua chamada, entre em contato com o número de rastreamento de remessa reversa, conforme mostrado na exibição de E-Ink ou no rótulo impresso.
    - Se o número de rastreamento não estiver entre aspas, o UPS exigirá que você pague um encargo adicional durante a retirada.

    Em vez de agendar a retirada, você também pode remover o Data Box no local de distribuição mais próximo.
4. Após a sua transportadora recolher e analisar o Data Box, o estado de encomenda no portal será atualizado para **Picked up (Recolhido)** . É também mostrado um ID de controlo.


## <a name="ship-in-australia"></a>Lançamento na Austrália

Os data centers do Azure na Austrália têm uma notificação de segurança adicional. Todas as remessas de entrada devem ter uma notificação avançada. Execute as etapas a seguir para enviar na Austrália.


1. Mantenha a caixa original usada para enviar o dispositivo para a remessa de retorno.
2. Verifique se a cópia de dados para o dispositivo foi concluída e **preparação para o envio execução** foi bem-sucedida.
3. Desligue o dispositivo e remova os cabos.
4. Coloque o spool e posicione com segurança o cabo de alimentação que foi fornecido com o dispositivo na parte posterior do dispositivo.
5. Envie por email Quantium soluções para solicitar uma retirada. Consulte o número de referência de serviço especificado na portal do Azure. Use o seguinte modelo de email:- *solicitação de rótulo de remessa reversa com código Tau*. Certifique-se de incluir os seguintes detalhes no email: 

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
6. As soluções Quantium Austrália enviarão um email para um rótulo de envio de devolução.
7. Imprima o rótulo de retorno e o afixa na caixa de envio.
8. Passe o pacote para a Courier.

Se necessário, você pode enviar por email o suporte Azure@quantiumsolutions.com de solução de Quantium em ou telefone.

Para consulta em relação ao seu pedido por telefone:

- Envie um email para retirada primeiro.
- Forneça o nome do seu pedido no telefone.

## <a name="ship-in-japan"></a>Lançamento no Japão 

1. Mantenha a caixa original usada para enviar o dispositivo para a remessa de retorno.
2. Desligue o dispositivo e remova os cabos.
3. Coloque o spool e posicione com segurança o cabo de alimentação que foi fornecido com o dispositivo na parte posterior do dispositivo.
4. Grave as informações de nome e endereço da sua empresa na nota de consignação como as informações do remetente.
5. Envie uma solução de Quantium de email usando o modelo de email a seguir.

    - Se a nota de consignação do Chakubarai do Japão não tiver sido incluída ou estiver ausente, observe que neste email. As soluções Quantium do Japão solicitarão o lançamento do Japão para trazer a nota de consignação na retirada.
    - Se você tiver vários pedidos, envie um email para garantir a retirada individual.

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

3. Receba uma confirmação por email das soluções Quantium depois de ter reservado uma retirada. A confirmação por email também inclui informações sobre a nota de consignação do Chakubarai.

Se necessário, você pode entrar em contato com o suporte da solução Quantium (idioma japonês) nas seguintes informações: 

- Email:Customerservice.JP@quantiumsolutions.com 
- Telefone: 03-5755-0150 

::: zone target="docs"

## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

Quando a Microsoft receber e analisar o dispositivo, o estado da encomenda será atualizado para **Received (Recebido)** . O dispositivo, em seguida, passa pela verificação física para verificar os danos ou sinais de adulteração.

Depois de a verificação estar concluída, o Data Box é ligado à rede no datacenter do Azure. A cópia de dados é iniciada automaticamente. Consoante o tamanho dos dados, a operação de cópia poderá demorar algumas horas a dias para concluir. Pode monitorizar o progresso da tarefa de cópia no portal.

Quando a cópia for concluída, o estado da encomenda será atualizado para **Completed (Concluído)** .

Verifique se os dados são carregados no Azure antes de excluí-los da origem. Seus dados podem estar em:

- Suas contas de armazenamento do Azure. Quando copia os dados para o Data Box, consoante o tipo, os dados são carregados para um dos seguintes caminhos na sua conta de Armazenamento do Azure.

  - Para blobs de blocos e blobs de páginas: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Para Ficheiros do Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Em alternativa, pode aceder à sua conta de armazenamento do Azure no portal do Azure e navegar a partir daí.

- Seus grupos de recursos de disco gerenciado. Ao criar discos gerenciados, os VHDs são carregados como BLOBs de páginas e convertidos em discos gerenciados. Os discos gerenciados são anexados aos grupos de recursos especificados no momento da criação do pedido. 

    - Se sua cópia para discos gerenciados no Azure tiver sido bem-sucedida, você poderá ir para os **detalhes do pedido** no portal do Azure e anotar os grupos de recursos especificados para discos gerenciados.

        ![Identificar grupos de recursos de disco gerenciado](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Vá para o grupo de recursos observado e localize os discos gerenciados.

        ![Disco gerenciado anexado a grupos de recursos](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Se você copiou um VHDX ou um VHD dinâmico/diferencial, o VHDX/VHD será carregado para a conta de armazenamento de preparo como um blob de páginas, mas a conversão de VHD em disco gerenciado falhará. Acesse sua conta de **armazenamento** de preparo > BLOBs e, em seguida, selecione o SSD Standard de contêiner apropriado, HDD Standard ou SSD Premium. Os VHDs são carregados como BLOBs de páginas em sua conta de armazenamento de preparo.

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
> * preparação para envioPreparação para envio
> * Enviar o Data Box para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminação de dados do Data Box

Avance para o artigo seguinte para saber como gerir o Data Box através da IU da Web local.

> [!div class="nextstepaction"]
> [Utilizar a IU da Web local para administrar um Azure Data Box](./data-box-local-web-ui-admin.md)

::: zone-end


