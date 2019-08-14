---
title: Tutorial para enviar Disco do Azure Data Box de volta | Microsoft Docs
description: Utilize este tutorial para aprender enviar o Azure Data Box Disk para a Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 9e3159a083d21e4a7a81df2fcf7a7d5db882b757
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976922"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-disk-and-verify-data-upload-to-azure"></a>Tutorial: Retornar Disco do Azure Data Box e verificar o carregamento de dados no Azure

Este é o último tutorial da série: Implantar Disco do Azure Data Box. Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Enviar o Data Box Disk para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminação de dados do Data Box Disk

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você concluiu o [tutorial: Copie dados para Disco do Azure Data Box e verifique](data-box-disk-deploy-copy-data.md).


## <a name="ship-data-box-disk-back"></a>Enviar o Data Box Disk de volta

1. Após a validação de dados ser concluída, desligue os discos. Remova os cabos de ligação.
2. Embrulhe todos os discos e os cabos de ligação em plástico-bolha e coloque-os na caixa de envio. Os encargos poderão ser aplicados se os acessórios estiverem ausentes.
    - Reutilize o empacotamento da remessa inicial.  
    - É recomendável que você empacote os discos usando um encapsulamento bolha bem protegido.
    - Certifique-se de que o ajuste esteja ajustado para reduzir os movimentos na caixa.

As próximas etapas são determinadas por onde você está retornando o dispositivo.

### <a name="pick-up-in-us-canada"></a>Escolha nos EUA, Canadá

Execute as etapas a seguir se retornar o dispositivo nos EUA ou no Canadá.

1. Utilize a etiqueta de envio para devolução, na capa de plástico afixada na caixa. Se o rótulo estiver danificado ou perdido:
    - Aceda a **Descrição Geral > Transferir etiqueta de envio**.

        ![Transferir etiqueta de envio](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Esta ação transfere uma etiqueta de envio para devolução, conforme mostrado abaixo.

        ![Etiqueta de envio de exemplo](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
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
    - Aceda a **Descrição Geral > Transferir etiqueta de envio**.

        ![Transferir etiqueta de envio](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Esta ação transfere uma etiqueta de envio para devolução, conforme mostrado abaixo.

        ![Etiqueta de envio de exemplo](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Afixar o rótulo no dispositivo.

2. Feche a caixa de envio e certifique-se de que a etiqueta para envio de devolução se encontra visível.
3. Se estiver a devolver o dispositivo na Europa com a DHL, solicite uma recolha à DHL acedendo ao respetivo site e especificando o número de carta de porte aéreo.
4. Vá para o site país/região DHL Express e escolha **agendar uma coleção do Courier > remessa do eReturn**.

    ![A DHL retorna a remessa](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
3. Especifique o número de carta de porte aéreo e clique em **Schedule Pickup (Agendar Recolha)** para marcar uma recolha.

      ![Agendar recolha](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

### <a name="pick-up-in-asia-pacific-region"></a>Escolha na região do Pacífico Asiático

Esta região inclui instruções para retirada no Japão, na Coreia, na Austrália e na Cingapura.

#### <a name="pick-up-in-australia"></a>Escolha na Austrália

Os data centers do Azure na Austrália têm uma notificação de segurança adicional. Todas as remessas de entrada devem ter uma notificação avançada. Execute as etapas a seguir para a retirada na Austrália.

1. Email `adbops@microsoft.com` para solicitar o rótulo de remessa com ID de entrada exclusiva ou o código Tau. Coloque a solicitação pelo menos três dias antes da data de remessa planejada para obter o rótulo no tempo.
2. O assunto do email deve ser *solicitação de etiqueta de envio reversa com código Tau*. Certifique-se de incluir os seguintes detalhes no email: 

    - Nome do pedido
    - Endereço
    - Nome do contacto

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

### <a name="pick-up-in-singapore"></a>Escolha em Cingapura

1. Imprima o rótulo de remessa e anexe na caixa. Se o rótulo estiver danificado ou perdido:
    - Aceda a **Descrição Geral > Transferir etiqueta de envio**.

        ![Transferir etiqueta de envio](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Esta ação transfere uma etiqueta de envio para devolução, conforme mostrado abaixo.

        ![Etiqueta de envio de exemplo](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Afixar o rótulo no dispositivo. Verifique se o rótulo está visível.

2. Para solicitar retirada:
    - Ligue para a linha direta da **Singpost** às **6845 6485** durante as horas do escritório (9h às 17:00, de segunda-feira a sexta-feira).  
    - Quote *Microsoft Azure retirada* e o número da solicitação de serviço (número de rastreamento no rótulo de envio de retorno) para organizar uma coleção. 
    - Você receberá uma confirmação verbal para a agenda de retirada. 
    - Se a Courier não chegar à coleção, chame **Singpost** em **6845 6485** para organizações alternativas. 
3. Passe para a Courier. 


## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

Após a sua transportadora recolher os discos, o estado de encomenda no portal será atualizado para **Picked up (Recolhido)** . É também mostrado um ID de controlo.

![Discos recolhidos](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Quando a Microsoft receber e analisar o disco, o estado da tarefa será alterado para **Received (Recebido)** . 

![Discos recebidos](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Os dados são automaticamente copiados após os discos serem ligados a um servidor no datacenter do Azure. Consoante o tamanho dos dados, a operação de cópia poderá demorar algumas horas a dias para concluir. Pode monitorizar o progresso da tarefa de cópia no portal.

Quando a cópia for concluída, o estado da encomenda será atualizado para **Completed (Concluído)** .

![Cópia de dados concluída](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Se a cópia for concluída com erros, consulte [solucionar problemas de erros de upload](data-box-disk-troubleshoot-upload.md).

Certifique-se de que os dados estão na(s) conta(s) de armazenamento antes de o eliminá-los da origem. Seus dados podem estar em:

- Suas contas de armazenamento do Azure. Quando copia os dados para o Data Box, consoante o tipo, os dados são carregados para um dos seguintes caminhos na sua conta de Armazenamento do Azure.

  - Para blobs de blocos e blobs de páginas: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Para Ficheiros do Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Em alternativa, pode aceder à sua conta de armazenamento do Azure no portal do Azure e navegar a partir daí.

- Seus grupos de recursos de disco gerenciado. Ao criar discos gerenciados, os VHDs são carregados como BLOBs de páginas e convertidos em discos gerenciados. Os discos gerenciados são anexados aos grupos de recursos especificados no momento da criação do pedido.

  - Se sua cópia para discos gerenciados no Azure tiver sido bem-sucedida, você poderá ir para os **detalhes do pedido** no portal do Azure e anotar o grupo de recursos especificado para discos gerenciados.

      ![Exibir detalhes do pedido](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Vá para o grupo de recursos observado e localize os discos gerenciados.

      ![Grupo de recursos para discos gerenciados](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Se você copiou um VHDX, ou um VHD dinâmico/diferencial, o VHDX/VHD será carregado para a conta de armazenamento de preparo como um blob de blocos. Acesse sua conta de **armazenamento** de preparo > BLOBs e, em seguida, selecione o contêiner-StandardSSD, StandardHDD ou PremiumSSD apropriado. O VHDX/VHDs deve aparecer como BLOBs de blocos em sua conta de armazenamento de preparo.

Para garantir que os dados foram carregados para o Azure, execute os seguintes passos:

1. Aceda à conta de armazenamento associada à sua encomenda de disco.
2. Aceda a **Serviço Blob > Procurar blobs**. É apresentada a lista de contentores. De forma correspondente à subpasta que criou nas pastas *BlockBlob* e *PageBlob*, os contentores com o mesmo nome são criados na sua conta de armazenamento.
    Se os nomes de pastas não estiverem em conformidade com as convenções de nomenclatura do Azure, o carregamento de dados para o Azure irá falhar.

4. Para se certificar de que todo o conjunto de dados foi carregado, utilize o Explorador de Armazenamento do Microsoft Azure. Anexe a conta de armazenamento correspondente à encomenda de aluguer de discos e, em seguida, observe a lista de contentores de blobs. Selecione um contentor, clique em **…Mais** e, em seguida, clique em **Estatísticas de pasta**. No painel **Atividades**, a estatística dessa pasta, incluindo o número de blobs e o tamanho total de blobs, é apresentado. O tamanho total de blobs em bytes deve corresponder ao tamanho do conjunto de dados.

    ![Estatísticas de pastas no Explorador de Armazenamento do Azure](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Eliminação de dados do Data Box Disk

Após a cópia ser concluída e se certificar de que os dados estão na conta de armazenamento do Azure, os discos são apagados de forma segura, segundo a norma NIST.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box Disk, como:

> [!div class="checklist"]
> * Enviar o Data Box Disk para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminação de dados do Data Box Disk


Avance para as próximas instruções para saber como gerir o Data Box Disk através do portal do Azure.

> [!div class="nextstepaction"]
> [Utilizar o portal do Azure para administrar o Azure Data Box Disk](./data-box-portal-ui-admin.md)

::: zone-end


::: zone target="chromeless"

# <a name="return-the-disk-and-verify-upload-to-azure"></a>Retornar o disco e verificar o carregamento no Azure

## <a name="ship-to-azure"></a>Enviar para o Azure

1. Depois que a validação de dados for concluída, desconecte os discos. Remova os cabos de ligação.
2. Embrulhe todos os discos e os cabos de ligação em plástico-bolha e coloque-os na caixa de envio. Os encargos poderão ser aplicados se os acessórios estiverem ausentes.
    - Reutilize o empacotamento da remessa inicial.  
    - É recomendável que você empacote os discos usando um encapsulamento bolha bem protegido.
    - Certifique-se de que o ajuste esteja ajustado para reduzir os movimentos na caixa.
3. As próximas etapas são determinadas por onde você está retornando o dispositivo.
    - [Agende uma retirada com UPS se retornar o dispositivo nos EUA e no Canadá](data-box-disk-deploy-picked-up.md#pick-up-in-us-canada).
    - [Agende uma retirada com a DHL para a Europa](data-box-disk-deploy-picked-up.md#pick-up-in-europe) visitando seu site e especificando o número de Bill aéreo.
    - [Agendar uma retirada para países na Austrália do Pacífico](data-box-disk-deploy-picked-up.md#pick-up-in-asia-pacific-region) , como Austrália, Japão, Coreia e Cingapura.
4. Depois que os discos são coletados pela sua operadora, o status do pedido no portal é atualizado e uma ID de rastreamento é exibida.

## <a name="verify-upload-to-azure"></a>Verificar upload no Azure

Depois que os dados são carregados no Azure, verifique se os dados estão nas contas de armazenamento antes de excluí-lo da origem. Seus dados podem estar em:

- Suas contas de armazenamento do Azure. Quando copia os dados para o Data Box, consoante o tipo, os dados são carregados para um dos seguintes caminhos na sua conta de Armazenamento do Azure.

    - **Para BLOBs de bloco e blobs de página**: https://< storage_account_name >. blob. Core. Windows.<containername>net//files/a.txt

    - **Para arquivos do Azure**: https://< storage_account_name >. File. Core. Windows. NET<sharename>//files/a.txt

    Em alternativa, pode aceder à sua conta de armazenamento do Azure no portal do Azure e navegar a partir daí.

- Seus grupos de recursos de disco gerenciado. Ao criar discos gerenciados, os VHDs são carregados como BLOBs de páginas e convertidos em discos gerenciados. Os discos gerenciados são anexados aos grupos de recursos especificados no momento da criação do pedido.

::: zone-end


