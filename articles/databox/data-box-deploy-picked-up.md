---
title: Tutorial para enviar novamente o Azure Data Box | Documentos da Microsoft
description: Aprenda a enviar o Azure Data Box para a Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 7/08/2019
ms.author: alkohli
ms.openlocfilehash: db0f0ac3073687b7c1cd8ca60e459e4bb3aa03f4
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626361"
---
# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>Tutorial: Devolver o Azure Data Box e verifique se o carregamento de dados para o Azure

Este tutorial descreve como devolver o Azure Data Box e certificar-se de que os dados foram carregados para o Azure.

Neste tutorial, irá obter informações sobre tópicos, como:

> [!div class="checklist"]
> * Pré-requisitos
> * Preparar para enviar
> * Enviar o Data Box para a Microsoft
> * Verificar o carregamento de dados para o Azure
> * Eliminação de dados do Data Box

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se:

- Já tiver concluído a [Tutorial: Copiar dados para o Azure Data Box e certifique-se](data-box-deploy-copy-data.md). 
- Tarefas de cópia estejam concluídas. Preparação para envio não é possível executar se tarefas de cópia estão em curso.

## <a name="prepare-to-ship"></a>Preparar para enviar

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

## <a name="ship-data-box-back"></a>Enviar o Data Box de volta

Certifique-se de que a cópia de dados para o dispositivo foi concluída e **preparação para envio** execução for concluída com êxito. Com base na região onde são enviados do dispositivo, o procedimento é diferente.


### <a name="ship-in-us-canada-europe"></a>Envie em nós, Canadá, Europa

Siga os passos seguintes se retornando o dispositivo nos EUA, Canadá, ou na Europa.

1. Certifique-se de que o dispositivo está desligado e cabos são removidos. 
2. Coloque em segurança o cabo de alimentação enviado com o dispositivo atrás do mesmo.
3. Certifique-se de que a etiqueta de envio é apresentada no ecrã de tinta eletrónica e agende uma recolha com a sua operadora. Se a etiqueta está danificada ou perdida ou não é apresentada no ecrã do E-tinta, contacte o Support da Microsoft. Se o suporte sugere, em seguida, pode aceder à **descrição geral > Download etiqueta de envio** no portal do Azure. Transfira a etiqueta de envio e affix no dispositivo. 
4. Agende uma recolha com UPS se retornando o dispositivo. Para agendar uma recolha:

    - Chame no-BREAK local (número gratuito de ligação específicos por país/região).
    - Em sua chamada citar a remessa inversa número, como mostra a exibição de tinta E ou a etiqueta impressa de controle.
    - Se o número de controlo não é indicado, UPS exigirá que paga um custo adicional durante a recolha.

    Em vez da recolha de agendamento, também pode remover o desativar a caixa de dados no local de entrega mais próximo.
4. Após a sua transportadora recolher e analisar o Data Box, o estado de encomenda no portal será atualizado para **Picked up (Recolhido)** . É também mostrado um ID de controlo.

### <a name="ship-in-asia-pacific-region"></a>Envie na região da Ásia-Pacífico

#### <a name="ship-in-australia"></a>Envie na Austrália

Os datacenters do Azure na Austrália tem uma notificação de segurança adicional. Todas as remessas de entrada tem de ter uma notificação de avançada. Siga os passos seguintes para enviar na Austrália.


1. Manter a caixa original utilizada para enviar o dispositivo para devolução.
2. Certifique-se de que a cópia de dados para o dispositivo foi concluída e **preparação para envio execução** é efetuada com êxito.
3. Desligar o dispositivo e remova os cabos.
4. Spool e coloque em segurança o cabo de alimentação que foi fornecido com o dispositivo na minha o dispositivo.
5. Soluções de Quantium de e-mail para pedir uma recolha. Consulte o número de referência de serviço especificado no portal do Azure. Utilizar o modelo de e-mail seguintes:- *pedido de etiqueta de envio reversa com o código TAU*. Certifique-se incluir os seguintes detalhes do e-mail: 

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
6. Austrália de soluções de Quantium e-mail uma etiqueta de envio de retorna.
7. A etiqueta de retorna de impressão e affix-lo na caixa de envio.
8. Entregar o pacote ao courier.

Se for necessário, pode enviar um e-mail suporte de solução Quantium Azure@quantiumsolutions.com ou telefone.


Para consulta em relação a sua encomenda através do telefone:

- Envie um e-mail para recolha pela primeira vez.
- Indique o seu nome de ordem no telefone.

#### <a name="ship-in-japan"></a>Enviar no Japão 

1. Manter a caixa original utilizada para enviar o dispositivo para devolução.
2. Desligar o dispositivo e remova os cabos.
3. Spool e coloque em segurança o cabo de alimentação que foi fornecido com o dispositivo na minha o dispositivo.
4. Escreva nome e endereço obter informações sobre a nota consignment como suas informações de remetente de sua empresa.
5. Solução de Quantium utilizando o modelo de e-mail seguinte de e-mail.

    - Se a nota do Japão Post Chakubarai consignment não estava incluída ou está em falta, tenha em atenção que neste e-mail. Quantium soluções Japão irá pedir Japão Post para trazer a nota consignment após a recolha.
    - Se tiver vários pedidos, envie um e-mail para garantir a recolha individual.

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

3. Receba um e-mail de confirmação de soluções de Quantium depois de ter marcadas uma recolha. A confirmação de e-mail também inclui informações sobre a nota de consignment Chakubarai.

Se for necessário, pode contactar o suporte de solução de Quantium (japonês) as seguintes informações: 

- E-mail:Customerservice.JP@quantiumsolutions.com 
- Telefone: 03-5755-0150 


## <a name="verify-data-upload-to-azure"></a>Verificar o carregamento de dados para o Azure

Quando a Microsoft receber e analisar o dispositivo, o estado da encomenda será atualizado para **Received (Recebido)** . O dispositivo, em seguida, passa pela verificação física para verificar os danos ou sinais de adulteração.

Depois de a verificação estar concluída, o Data Box é ligado à rede no datacenter do Azure. A cópia de dados é iniciada automaticamente. Consoante o tamanho dos dados, a operação de cópia poderá demorar algumas horas a dias para concluir. Pode monitorizar o progresso da tarefa de cópia no portal.

Quando a cópia for concluída, o estado da encomenda será atualizado para **Completed (Concluído)** .

Certifique-se de que os seus dados são carregados para o Azure antes de eliminar a partir da origem. Os dados podem estar:

- Sua conta de armazenamento do Azure (s). Quando copia os dados para o Data Box, consoante o tipo, os dados são carregados para um dos seguintes caminhos na sua conta de Armazenamento do Azure.

  - Para blobs de blocos e blobs de páginas: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Para Ficheiros do Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Em alternativa, pode aceder à sua conta de armazenamento do Azure no portal do Azure e navegar a partir daí.

- Os grupos de recursos de disco gerido. Ao criar discos geridos, os VHDs são carregados como blobs de páginas e, em seguida, convertidos em discos geridos. Os discos geridos são anexados aos grupos de recursos especificados no momento da criação de ordem. 

    - Se a sua cópia para os managed disks no Azure foi concluída com êxito, pode ir para o **detalhes de pedidos** no portal do Azure e fazer uma observação dos grupos de recursos especificada para discos geridos.

        ![Identificar os grupos de recursos de disco gerido](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        Vá para o grupo de recursos observado e localize os discos geridos.

        ![Disco ligado a grupos de recursos gerido](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - Se tiver copiado um VHDX ou um VHD de diferenciação/dinâmico, o VHD/VHDX é carregado para a conta de armazenamento de teste como um blob de página, mas a conversão de VHD para falha de disco gerido. Aceda a sua transição **conta de armazenamento > Blobs** e, em seguida, selecione o contentor adequado - Standard SSD, Standard HDD ou Premium SSD. Os VHDs são carregados como blobs de páginas na sua conta de armazenamento de teste.

## <a name="erasure-of-data-from-data-box"></a>Eliminação de dados do Data Box
 
Após o carregamento para o Azure, o Data Box elimina os dados dos discos de acordo com as [diretrizes SP 800-88 Revision 1 da NIST](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

## <a name="next-steps"></a>Passos Seguintes

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


