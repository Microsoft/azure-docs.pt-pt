---
title: Tutorial para enviar novamente o disco do Azure Data Box | Documentos da Microsoft
description: Utilize este tutorial para aprender enviar o Azure Data Box Disk para a Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 06/25/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 7e7a1f119a2f2b0e60645cb776b26c124910cacb
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448220"
---
# <a name="tutorial-return-azure-data-box-disk-and-verify-data-upload-to-azure"></a>Tutorial: Devolver o disco do Azure Data Box e verifique se o carregamento de dados para o Azure

Este é o último tutorial da série: Implemente o disco do Azure Data Box. Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Enviar o Data Box Disk para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminação de dados do Data Box Disk

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que concluiu o [Tutorial: Copiar dados para o disco do Azure Data Box e certifique-se](data-box-disk-deploy-copy-data.md).

## <a name="ship-data-box-disk-back"></a>Enviar o Data Box Disk de volta

1. Após a validação de dados ser concluída, desligue os discos. Remova os cabos de ligação.
2. Embrulhe todos os discos e os cabos de ligação em plástico-bolha e coloque-os na caixa de envio. Custos podem ser aplicadas se os acessórios estão em falta.
    - Reutilize o empacotamento de primeira remessa.  
    - Recomendamos que o pacote de discos com um wrap bubbled bem protegido.
    - Certifique-se a opção snug para reduzir qualquer movimentos na caixa.

Os passos seguintes são determinados por onde retornando o dispositivo.

### <a name="pick-up-in-us-canada"></a>Pegar nos EUA, Canadá

Siga os passos seguintes se retornando o dispositivo nos EUA ou Canadá.

1. Utilize a etiqueta de envio para devolução, na capa de plástico afixada na caixa. Se a etiqueta é dano ou perda:
    - Aceda a **Descrição Geral > Transferir etiqueta de envio**.

        ![Transferir etiqueta de envio](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Esta ação transfere uma etiqueta de envio para devolução, conforme mostrado abaixo.

        ![Etiqueta de envio de exemplo](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Affix a etiqueta no dispositivo.

2. Feche a caixa de envio e certifique-se de que a etiqueta para envio de devolução se encontra visível.
3. Agende uma recolha no-BREAK. Para agendar uma recolha:

    - Chame no-BREAK local (número gratuito de ligação específicos por país/região).
    - Em sua chamada citar a remessa inversa número, como mostra a sua etiqueta impressa de controle.
    - Se o número de controlo não é indicado, UPS exigirá que paga um custo adicional durante a recolha.
    - Em vez da recolha de agendamento, também pode remover o fora do disco de caixa de dados no local de entrega mais próximo.


### <a name="pick-up-in-europe"></a>Pegar na Europa

Siga os passos seguintes se retornando o dispositivo na Europa.

1. Utilize a etiqueta de envio para devolução, na capa de plástico afixada na caixa. Se a etiqueta é dano ou perda:
    - Aceda a **Descrição Geral > Transferir etiqueta de envio**.

        ![Transferir etiqueta de envio](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Esta ação transfere uma etiqueta de envio para devolução, conforme mostrado abaixo.

        ![Etiqueta de envio de exemplo](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Affix a etiqueta no dispositivo.

2. Feche a caixa de envio e certifique-se de que a etiqueta para envio de devolução se encontra visível.
3. Se estiver a devolver o dispositivo na Europa com a DHL, solicite uma recolha à DHL acedendo ao respetivo site e especificando o número de carta de porte aéreo.
4. Aceda ao Web site DHL Express país/região e escolha **reservar uma coleção de Courier > eReturn remessa**.

    ![Devolução de DHL](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
3. Especifique o número de carta de porte aéreo e clique em **Schedule Pickup (Agendar Recolha)** para marcar uma recolha.

      ![Agendar recolha](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

### <a name="pick-up-in-asia-pacific-region"></a>Pegar na região da Ásia-Pacífico

Esta região inclui instruções para recolha no Japão, Coreia, Austrália e Singapura.

#### <a name="pick-up-in-australia"></a>Pegar na Austrália

Os datacenters do Azure na Austrália tem uma notificação de segurança adicional. Todas as remessas de entrada tem de ter uma notificação de avançada. Siga os passos seguintes para recolha na Austrália.

1. E-mail `adbops@microsoft.com` a etiqueta de envio do pedido com o código TAU ou o ID exclusivo de entrada. Coloque o pedido de, pelo menos, 3 dias de antecedência da data de remessa planeada para obter a etiqueta no tempo.
2. Deve ser o assunto do e-mail - *pedido de etiqueta de envio reversa com o código TAU*. Certifique-se incluir os seguintes detalhes do e-mail: 

    - Nome do pedido
    - Endereço
    - Nome do contacto

#### <a name="pick-up-in-japan"></a>Escolher no Japão

1. Escreva nome e endereço obter informações sobre a nota consignment como suas informações de remetente de sua empresa.
2. Solução de Quantium utilizando o modelo de e-mail seguinte de e-mail.

    - Se a nota do Japão Post Chakubarai consignment não estava incluída ou está em falta, tenha em atenção que neste e-mail. Quantium soluções Japão irá pedir Japão Post para trazer a nota consignment após a recolha.
    - Se tiver vários pedidos, envie um e-mail para garantir a recolha individual.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box Disk｜Job Name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
        a. 08：00-13：00 
        b. 13：00-15：00 
        c. 15：00-17：00 
        d. 17：00-19：00 
    ```

3. Receba um e-mail de confirmação de soluções de Quantium depois de ter marcadas uma recolha. A confirmação de e-mail também inclui informações sobre a nota de consignment Chakubarai.

Se for necessário, pode contactar o suporte de solução de Quantium (japonês) as seguintes informações: 

- E-mail:Customerservice.JP@quantiumsolutions.com 
- Telefone: 03-5755-0150 

#### <a name="pick-up-in-korea"></a>Pegar na Coreia

1. Certifique-se incluir a nota consignment retorno.
2. Para pedir recolha quando nota consignment está presente:
    1. Chamar *Quantium Solutions International* linha direta no 070 8231 1418 durante o horário do office (10 AM para 5 PM, segunda-feira a sexta-feira). Para citar *recolha do Microsoft Azure* e o número de pedido de serviço para fazer com que uma coleção.  
    2. Se a linha direta estiver ocupada, envie um e-mail `microsoft@rocketparcel.com`, com o assunto do e-mail *recolha do Microsoft Azure* e o número de pedido de serviço como referência.
    3. Se não receber o courier para coleção, chamar *Quantium Solutions International* linha direta para disposições alternativas. 
    4. Receberá um e-mail de confirmação para a agenda de recolha.
3. Efetue este passo apenas se a nota consignment não está presente. A recolha do pedido:
    1. Chamar *Quantium Solutions International* linha direta no 070 8231 1418 durante o horário do office (10 AM para 5 PM, segunda-feira a sexta-feira). Para citar *recolha do Microsoft Azure* e o número de pedido de serviço para fazer com que uma coleção. Especifique que precisa de uma nova nota consignment para fazer com que uma coleção. Forneça o remetente (cliente), informações de destinatário (Centro de dados do Azure) e o número de referência (número de pedido de serviço). 
    2. Se a linha direta estiver ocupada, envie um e-mail `microsoft@rocketparcel.com`, com o assunto do e-mail *recolha do Microsoft Azure* e o número de pedido de serviço como referência.
    3. Se não receber o courier para coleção, chamar *Quantium Solutions International* linha direta para disposições alternativas. 
    4. Receberá uma confirmação verbal se a solicitação é feita por telefone.

### <a name="pick-up-in-singapore"></a>Recolher em Singapura

1. A etiqueta de impressão e anexe para o box. Se a etiqueta é dano ou perda:
    - Aceda a **Descrição Geral > Transferir etiqueta de envio**.

        ![Transferir etiqueta de envio](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        Esta ação transfere uma etiqueta de envio para devolução, conforme mostrado abaixo.

        ![Etiqueta de envio de exemplo](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - Affix a etiqueta no dispositivo. Certifique-se de que a etiqueta está visível.

2. A recolha do pedido:
    - Chamar **SingPost** linha direta no **6845 6485** durante o horário do office (9h para 5 pm, segunda-feira a sexta-feira).  
    - Para citar *recolha do Microsoft Azure* e o serviço pedido número (número de controlo sobre a etiqueta de envio) para fazer com que uma coleção. 
    - Receberá uma confirmação verbal para a agenda de recolha. 
    - Se não receber o courier para coleção, chamar **SingPost** na **6845 6485** para disposições alternativas. 
3. Passar para o courier. 


## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

Após a sua transportadora recolher os discos, o estado de encomenda no portal será atualizado para **Picked up (Recolhido)** . É também mostrado um ID de controlo.

![Discos recolhidos](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Quando a Microsoft receber e analisar o disco, o estado da tarefa será alterado para **Received (Recebido)** . 

![Discos recebidos](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Os dados são automaticamente copiados após os discos serem ligados a um servidor no datacenter do Azure. Consoante o tamanho dos dados, a operação de cópia poderá demorar algumas horas a dias para concluir. Pode monitorizar o progresso da tarefa de cópia no portal.

Quando a cópia for concluída, o estado da encomenda será atualizado para **Completed (Concluído)** .

![Cópia de dados concluída](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Se a cópia for concluída com erros, ver [resolver problemas de erros de carregamento](data-box-disk-troubleshoot-upload.md).

Certifique-se de que os dados estão na(s) conta(s) de armazenamento antes de eliminá-los da origem. Os dados podem estar:

- Sua conta de armazenamento do Azure (s). Quando copia os dados para o Data Box, consoante o tipo, os dados são carregados para um dos seguintes caminhos na sua conta de Armazenamento do Azure.

  - Para blobs de blocos e blobs de páginas: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Para Ficheiros do Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Em alternativa, pode aceder à sua conta de armazenamento do Azure no portal do Azure e navegar a partir daí.

- Os grupos de recursos de disco gerido. Ao criar discos geridos, os VHDs são carregados como blobs de páginas e, em seguida, convertidos em discos geridos. Os discos geridos são anexados aos grupos de recursos especificados no momento da criação de ordem.

  - Se a sua cópia para os managed disks no Azure foi concluída com êxito, pode ir para o **detalhes de pedidos** no portal do Azure e fazer uma observação do grupo de recursos especificada para discos geridos.

      ![Ver detalhes dos pedidos](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Vá para o grupo de recursos observado e localize os discos geridos.

      ![Grupo de recursos para discos geridos](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Se tiver copiado um VHDX ou um VHD de diferenciação/dinâmico, o VHD/VHDX é carregado para a conta de armazenamento de teste como um blob de blocos. Aceda a sua transição **conta de armazenamento > Blobs** e, em seguida, selecione o contentor - StandardSSD, StandardHDD ou PremiumSSD adequado. O VHD/VHDX deve aparecer como blobs de blocos na conta de armazenamento temporário.

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


