---
title: Guia do administrador do portal do Azure Data Box | Microsoft Docs
description: Descreve como utilizar o portal do Azure para administrar o Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 10/19/2018
ms.author: alkohli
ms.openlocfilehash: f2ad4dc3d74771d28ca91e41f2b0eb547d678908
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122999"
---
# <a name="use-the-azure-portal-to-administer-your-data-box"></a>Utilizar o portal do Azure para administrar o Data Box

Este artigo descreve alguns dos fluxos de trabalho e algumas das tarefas de gestão com um elevado grau de complexidade que podem ser executados no Data Box. Pode gerir o Data Box através do portal do Azure ou da IU da Web local. 

Este artigo aborda em especial as tarefas que pode efetuar com o portal do Azure. Utilize o portal do Azure para gerir encomendas, gerir o Data Box e controlar o estado da encomenda à medida que avança para a conclusão.


## <a name="cancel-an-order"></a>Cancelar uma encomenda

Poderá ter de cancelar uma encomenda por vários motivos depois de fazer a encomenda. Só pode cancelar a encomenda antes de ser processada. Assim que a encomenda for processada e o Data Box preparado, deixa de ser possível cancelar a encomenda. 

Execute os seguintes passos para cancelar uma encomenda.

1.  Aceda a **Descrição Geral > Cancelar**. 

    ![Cancelar encomenda 1](media/data-box-portal-admin/cancel-order1.png)

2.  Escreva um motivo para cancelar a encomenda.  

    ![Cancelar encomenda 2](media/data-box-portal-admin/cancel-order2.png)

3.  Assim que a encomenda é cancelada, o portal atualiza o estado da encomenda e apresenta-o como **Cancelado**. 

## <a name="clone-an-order"></a>Clonar uma encomenda

A clonagem é útil em determinadas situações. Por exemplo, um utilizador utilizou o Data Box para transferir alguns dados. À medida que são gerados mais dados, é necessário outro Data Box para transferir esses dados para o Azure. Neste caso, a mesma encomenda pode ser clonada.

Execute os seguintes passos para clonar uma encomenda.

1.  Aceda a **Descrição Geral > Clone**. 

    ![Clonar a encomenda 1](media/data-box-portal-admin/clone-order1.png)

2.  Todos os detalhes da encomenda permanecem iguais. O nome da encomenda é o nome da encomenda original anexada por *-Clone*. Selecione a caixa de verificação para confirmar que reviu as informações de privacidade. Clique em **Criar**.

O clone é criado em apenas alguns minutos e o portal é atualizado para mostrar a nova encomenda.


## <a name="delete-order"></a>Eliminar encomenda

Pode querer eliminar uma encomenda quando a encomenda for concluída. A encomenda contém as suas informações pessoais, como o nome, o endereço e as informações de contacto. Estas informações pessoais são eliminadas quando a encomenda é eliminada.

Só pode eliminar as encomendas que foram concluídas ou canceladas. Execute os seguintes passos para eliminar uma encomenda.

1. Vá para **Todos os recursos**. Procure a sua encomenda.

2. Clique na encomenda que pretende eliminar e aceda a **Descrição Geral**. Na barra de comandos, clique em **Eliminar**.

    ![Eliminar a encomenda do Data Box 1](media/data-box-portal-admin/delete-order1.png)

3. Introduza o nome da encomenda quando lhe for pedido para confirmar a eliminação da encomenda. Clique em **Eliminar**.

## <a name="download-shipping-label"></a>Transferir etiqueta de envio

Terá de descarregar a etiqueta de envio caso o visor E-ink do Data Box não esteja a funcionar e não apresente a etiqueta de envio para devolução. 

Execute os seguintes passos para transferir uma etiqueta de envio.

1.  Aceda a **Descrição Geral > Transferir etiqueta de envio**. Esta opção só fica disponível após o dispositivo ser enviado. 

    ![Transferir etiqueta de envio](media/data-box-portal-admin/download-shipping-label.png)

2.  Esta ação transfere a seguinte etiqueta de envio para devolução. Guarde a etiqueta e imprima-a. Dobre e insira a etiqueta na bolsa transparente do dispositivo. Certifique-se de que a etiqueta está visível. Remova quaisquer etiquetas de envios anteriores existentes no dispositivo.

    ![Etiqueta de envio de exemplo](media/data-box-portal-admin/example-shipping-label.png)

## <a name="edit-shipping-address"></a>Editar o endereço de envio

Poderá ter de editar o endereço de envio assim que a encomenda for feita. Isto só está disponível até o dispositivo ser expedido. Depois de expedir o dispositivo, esta opção deixa de estar disponível.

Execute os seguintes passos para editar a encomenda.

1. Aceda a **Detalhes da encomenda > Editar endereço de envio**.

    ![Editar o endereço de envio 1](media/data-box-portal-admin/edit-shipping-address1.png)

2. Edite e valide o endereço de envio e, em seguida, guarde as alterações.

    ![Editar o endereço de envio 2](media/data-box-portal-admin/edit-shipping-address2.png)

## <a name="edit-notification-details"></a>Editar detalhes de notificação

Poderá ter de alterar os utilizadores que pretende que recebam os e-mails de estado da encomenda. Por exemplo, um utilizador tem de manter-se informado sobre quando o dispositivo é entregue ou recolhido. Outro utilizador poderá ter de manter-se informado sobre quando a cópia de dados é concluída, para poder verificar que os dados estão na conta de armazenamento do Azure antes de os eliminar da origem. Nestes casos, pode editar os detalhes de notificação.

Execute os seguintes passos para editar os detalhes de notificação.

1. Aceda a **Detalhes da encomenda > Editar detalhes de notificação**.

    ![Editar detalhes de notificação 1](media/data-box-portal-admin/edit-notification-details1.png)

2. Pode agora editar os detalhes de notificação e, em seguida, guardar as alterações.
 
    ![Editar detalhes de notificação 2](media/data-box-portal-admin/edit-notification-details2.png)


## <a name="download-order-history"></a>Transferir histórico de encomendas

Assim que a encomenda do Data Box estiver concluída, os dados nos discos de dispositivo são apagados. Quando a limpeza do dispositivo estiver concluída, pode transferir o histórico de encomendas no portal do Azure.

Execute os seguintes passos para transferir o histórico de encomendas.

1. Na sua encomenda do Data Box, aceda a **Descrição geral**. Certifique-se de que a encomenda é concluída. Se a encomenda estiver concluída e a limpeza do dispositivo também, então aceda a **Detalhes da encomenda**. A opção **Transferir histórico de encomendas** está disponível.

    ![Transferir histórico de encomendas](media/data-box-portal-admin/download-order-history-1.png)

2. Clique em **Transferir histórico de encomendas**. No histórico transferido, verá um registo dos registos de controlo da operadora. Se deslocar para baixo para a parte inferior deste registo, pode ver as ligações para:
    
   - **Copiar registos** - tenha a lista de ficheiros que obtiveram erros durante a cópia de dados do Data Box para a sua conta de armazenamento do Azure.
   - **Registos de auditoria** - contêm informações sobre a ativação e partilha de acesso no Data Box quando este está fora do datacenter do Azure.
   - **Ficheiros BOM** - tenha a lista de ficheiros (também conhecida como manifesto de ficheiro), que pode transferir durante a **Preparação para envio** e tem os nomes de ficheiros, tamanhos dos ficheiros e as somas de verificação dos ficheiros.

       ```
       -------------------------------
       Microsoft Data Box Order Report
       -------------------------------
        
       Name                                               : eastusdryrun                                      
       StartTime(UTC)                                     : 9/6/2018 12:54:47 PM +00:00                       
       DeviceType                                         : ImolaPod                                          
        
       -------------------
       Data Box Activities
       -------------------
        
       Time(UTC)             | Activity                       | Status          | Description                                                                                                                                           
        
       9/6/2018 12:54:51 PM  | OrderCreated         | Completed  |                                                                                                                              
       9/11/2018 8:57:38 PM  | DevicePrepared       | Completed  |                                                                                                                                                       
       9/12/2018 7:28:15 PM  | ShippingToCustomer   | InProgress | Pickup Scan. Local Time : 9/12/2018 2:52:31 PM at Chantilly                                                                                           
       9/13/2018 2:33:04 AM  | ShippingToCustomer   | InProgress | Departure Scan. Local Time : 9/12/2018 9:00:00 PM at Chantilly                                                                                                                                                                                                                                                              
       9/13/2018 12:40:31 PM | ShippingToCustomer   | InProgress | Arrival Scan. Local Time : 9/13/2018 5:00:00 AM at Oakland                                                                                            
       9/13/2018 2:42:10 PM  | ShippingToCustomer   | InProgress | Departure Scan. Local Time : 9/13/2018 6:08:00 AM at Oakland                                                                                          
       9/13/2018 3:42:12 PM  | ShippingToCustomer   | InProgress | Destination Scan. Local Time : 9/13/2018 8:14:08 AM at Sunnyvale                                                                                      
       9/13/2018 4:43:05 PM  | ShippingToCustomer   | InProgress | Destination Scan. Local Time : 9/13/2018 8:56:54 AM at Sunnyvale                                                                                      
       9/13/2018 4:43:05 PM  | ShippingToCustomer   | InProgress | Out For Delivery Today. Local Time : 9/13/2018 9:11:21 AM at Sunnyvale                                                                                
       9/13/2018 5:43:07 PM  | ShippingToCustomer   | Completed  | Delivered. Local Time : 9/13/2018 9:44:17 AM at SUNNYVALE                                                                                             
       9/14/2018 11:48:35 PM | ShippingToDataCenter | InProgress | Pickup Scan. Local Time : 9/14/2018 3:55:37 PM at Sunnyvale                                                                                                                                                                                 
       9/15/2018 1:52:35 AM  | ShippingToDataCenter | InProgress | Arrival Scan. Local Time : 9/14/2018 6:31:00 PM at San Jose                                                                                           
       9/15/2018 2:52:39 AM  | ShippingToDataCenter | InProgress | Departure Scan. Local Time : 9/14/2018 7:17:00 PM at San Jose                                                                                                                                                                             
       9/17/2018 8:23:31 AM  | ShippingToDataCenter | InProgress | Destination Scan. Local Time : 9/17/2018 4:14:37 AM at Chantilly                                                                                      
       9/17/2018 12:24:42 PM | ShippingToDataCenter | InProgress | Loaded on Delivery Vehicle. Local Time : 9/17/2018 7:45:36 AM at Chantilly                                                                            
       9/17/2018 1:25:11 PM  | ShippingToDataCenter | InProgress | Out For Delivery Today. Local Time : 9/17/2018 8:27:11 AM at Chantilly                                                                                
       9/17/2018 2:25:51 PM  | ShippingToDataCenter | Completed | Delivered. Local Time : 9/17/2018 9:56:32 AM at STERLING                                                                                              
       9/18/2018 9:55:41 PM  | DeviceBoot           | Completed | Appliance booted up successfully                                                                                                                      
       9/18/2018 11:00:25 PM | DataCopy             | Started   |                                                                                                                                                       
       9/18/2018 11:01:33 PM | DataCopy             | Completed | Copy Completed.                                                                                                                                       
       9/18/2018 11:20:58 PM | SecureErase          | Started   |                                                                                                                                                       
       9/18/2018 11:28:46 PM | SecureErase          | Completed | Azure Data Box:BY506B4B616700 has been sanitized according to NIST 800 -88 Rev 1.                                                                     
        
       ----------------------
       Data Box Job Log Links
       ----------------------
        
       Account Name         : eastusdryrun                                         
       Copy Logs Path       : copylog/copylogd695869a2a294396b7b903296c208388.xml                                                                                                                                                     
       Audit Logs Path      : azuredatabox-chainofcustodylogs\3b4cf163-f1af-475c-a391-f8afea3fa327\by506b4b616700                                                                                                                     
       BOM Files Path       : azuredatabox-chainofcustodylogs\3b4cf163-f1af-475c-a391-f8afea3fa327\by506b4b616700
       ```
     Pode, em seguida, aceder à sua conta de armazenamento e ver os registos de cópia.

![Registos nas contas de armazenamento](media/data-box-portal-admin/logs-in-storage-acct-2.png)

Também pode ver a cadeia de registos de custódia que incluem os registos de auditoria e os ficheiros BOM.

![Registos nas contas de armazenamento](media/data-box-portal-admin/logs-in-storage-acct-1.png)

## <a name="view-order-status"></a>Ver o estado da encomenda

Quando o estado do dispositivo no portal mudar, será notificado através de uma mensagem de e-mail.

|Estado da encomenda |Descrição |
|---------|---------|
|Encomendado     | Fez uma encomenda com êxito. <br>Se o dispositivo estiver disponível, a Microsoft identifica um dispositivo para envio e prepara-o. <br> Se o dispositivo não estiver disponível de imediato, a encomenda será processada quando o dispositivo ficar disponível. A encomenda pode demorar entre vários dias a alguns meses a ser processada. Se não for possível satisfazer a encomenda num prazo de 90 dias, a mesma será cancelada e o requerente será notificado.         |
|Processado     | O processamento da encomenda está concluído. De acordo com a sua encomenda, o dispositivo está preparado para envio no datacenter.         |
|Expedido     | A encomenda foi enviada. Utilize o ID de controlo apresentado na encomenda no portal para controlar o envio.        |
|Entregue     | A encomenda enviada foi entregue no endereço especificado na encomenda.        |
|Recolhido     |O envio de devolução foi recolhido e selecionado e analisado pela transportadora.         |
|Recebido     | O dispositivo é recebido e analisado no datacenter do Azure. <br> Após a inspeção, será iniciado o carregamento do dispositivo.      |
|Cópia de dados     | A cópia dos dados está em curso. Controle o progresso da cópia da sua encomenda no portal do Azure. <br> Aguarde até que a cópia de dados esteja concluída. |
|Concluído       |A encomenda foi concluída com êxito.<br> Verifique se os seus dados estão no Azure antes de eliminar os dados no local dos servidores.         |
|Concluído com erros| A cópia de dados foi concluída, mas ocorreram erros durante a cópia. <br> Reveja os registos de cópia com o caminho indicado no portal do Azure.   |
|Cancelado            |A encomenda foi cancelada. <br> Cancelou a encomenda ou foi encontrado um erro e o serviço cancelou a encomenda. Se não for possível satisfazer a encomenda num prazo de 90 dias, a mesma também será cancelada e o requerente será notificado.     |
|Limpeza | Os dados nos discos de dispositivo são apagados. A limpeza de dispositivo será considerada como concluída quando o histórico da encomenda ficar disponível para transferência no portal do Azure.|



## <a name="next-steps"></a>Passos Seguintes

- Saiba como [Resolver problemas do Data Box](data-box-faq.md).
