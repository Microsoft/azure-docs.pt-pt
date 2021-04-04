---
title: Gerir a Caixa de Dados Azure, caixa de dados Azure Pesada através do portal Azure | Microsoft Docs
description: Descreve como usar o portal Azure para administrar a sua Caixa de Dados Azure e Caixa de Dados Azure Heavy.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: 46a18cb2b6e1682427d5674be28b240f35b120fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97678643"
---
# <a name="use-the-azure-portal-to-administer-your-azure-data-box-and-azure-data-box-heavy"></a>Utilize o portal Azure para administrar a sua Caixa de Dados Azure e caixa de dados Azure Heavy

Este artigo aplica-se tanto à Caixa de Dados Azure como à Caixa de Dados Azure Heavy. Este artigo descreve alguns dos complexos fluxos de trabalho e tarefas de gestão que podem ser realizadas no dispositivo Azure Data Box. Pode gerir o dispositivo Data Box através do portal Azure ou através da UI web local.

Este artigo foca as tarefas que pode efetuar com o portal do Azure. Utilize o portal Azure para gerir encomendas, gerir o dispositivo Data Box e acompanhar o estado da encomenda à medida que esta prossegue.

## <a name="cancel-an-order"></a>Cancelar uma encomenda

Pode ter de cancelar uma encomenda por várias razões depois de a colocar.

Para encomendas de importação e exportação, só pode cancelar a encomenda antes de ser processada. Uma vez que o seu pedido é processado e o dispositivo Data Box é preparado, não pode cancelar a encomenda.

Execute os seguintes passos para cancelar uma encomenda.

1.  Aceda a **Descrição Geral > Cancelar**.

    ![Cancelar o comando no separador Visão Geral para uma encomenda](media/data-box-portal-admin/portal-admin-cancel-command.png)

2.  Escreva um motivo para cancelar a encomenda.  

    ![Cancelar caixa de diálogo de encomenda](media/data-box-portal-admin/portal-admin-cancel-order-dbox.png)

3.  Assim que a encomenda é cancelada, o portal atualiza o estado da encomenda e apresenta-o como **Cancelado**.

## <a name="clone-an-order"></a>Clonar uma encomenda

A clonagem é útil em determinadas situações. Por exemplo, utilizou a Data Box para transferir alguns dados. À medida que mais dados são gerados, precisa de utilizar outro dispositivo Data Box para transferir esses dados para o Azure. Neste caso, pode clonar a mesma ordem.

Execute os seguintes passos para clonar uma ordem de importação.

1.  Aceda a **Descrição Geral > Clone**. 

    ![Comando clone no separador Visão Geral para uma encomenda](media/data-box-portal-admin/portal-admin-clone-command.png)

2.  Todos os detalhes da encomenda permanecem iguais. O nome da encomenda é o nome da encomenda original anexada por *-Clone*. Selecione a caixa de verificação para confirmar que reviu as informações de privacidade. Clique em **Criar**.

O clone é criado em apenas alguns minutos e o portal é atualizado para mostrar a nova encomenda.


## <a name="delete-order"></a>Eliminar encomenda

Pode querer eliminar uma encomenda quando a encomenda for concluída. A encomenda contém as suas informações pessoais, como o nome, o endereço e as informações de contacto. Estas informações pessoais são eliminadas quando a encomenda é eliminada.

Só pode eliminar as encomendas que foram concluídas ou canceladas. Execute os seguintes passos para eliminar uma encomenda.

1. Vá para **Todos os recursos**. Procure a sua encomenda.

2. Clique na encomenda que pretende eliminar e aceda a **Descrição Geral**. Na barra de comandos, clique em **Eliminar**.

    ![Eliminar o comando no separador Visão Geral para uma encomenda](media/data-box-portal-admin/portal-admin-delete-command.png)

3. Introduza o nome da encomenda quando lhe for pedido para confirmar a eliminação da encomenda. Clique em **Eliminar**.

## <a name="download-shipping-label"></a>Transferir etiqueta de envio

Poderá ter de descarregar a etiqueta de envio se a exibição de tinta E da sua Caixa de Dados não estiver a funcionar e não apresentar a etiqueta de envio de devolução. Não há ecrã de tinta E na Caixa de Dados Pesada, por isso este fluxo de trabalho não se aplica à Data Box Heavy.

Execute os seguintes passos para transferir uma etiqueta de envio.

1.  Aceda a **Descrição geral > Transferir etiqueta de envio**. Esta opção só fica disponível após o dispositivo ser enviado. 

    ![Transferir etiqueta de envio](media/data-box-portal-admin/portal-admin-download-shipping-label.png)

2.  Esta ação transfere a seguinte etiqueta de envio para devolução. Guarde a etiqueta e imprima-a. Dobre e insira a etiqueta na manga clara do aparelho. Certifique-se de que a etiqueta está visível. Remova quaisquer etiquetas de envios anteriores existentes no dispositivo.

    ![Examinar a etiqueta de envio](media/data-box-portal-admin/portal-admin-example-shipping-label.png)

## <a name="edit-shipping-address"></a>Editar o endereço de envio

Poderá ter de editar o endereço de envio assim que a encomenda for feita. Isto só está disponível até o dispositivo ser expedido. Depois de expedir o dispositivo, esta opção deixa de estar disponível.

Execute os seguintes passos para editar a encomenda.

1. Aceda a **Detalhes da encomenda > Editar endereço de envio**.

    ![Editar o comando de endereço de envio em detalhes da Ordem](media/data-box-portal-admin/portal-admin-edit-shipping-address-command.png)

2. Edite e valide o endereço de envio e, em seguida, guarde as alterações.

    ![Editar caixa de diálogo de endereço de envio](media/data-box-portal-admin/portal-admin-edit-shipping-address-dbox.png)

## <a name="edit-notification-details"></a>Editar detalhes de notificação

Pode ser necessário alterar os utilizadores que recebem os e-mails de estado da encomenda. Por exemplo, um utilizador tem de manter-se informado sobre quando o dispositivo é entregue ou recolhido. Outro utilizador poderá ter de ser informado quando a cópia de dados estiver completa para que possam verificar se os dados estão na conta de armazenamento Azure antes de os eliminar da fonte. Nestes casos, pode editar os detalhes de notificação.

Execute os seguintes passos para editar os detalhes de notificação.

1. Aceda a **Detalhes da encomenda > Editar detalhes de notificação**.

    ![Editar o comando de detalhes de notificação em detalhes da Ordem](media/data-box-portal-admin/portal-admin-edit-notification-details-command.png)

2. Pode agora editar os detalhes de notificação e, em seguida, guardar as alterações.
 
    ![Editar caixa de diálogo de detalhes de notificação](media/data-box-portal-admin/portal-admin-edit-notification-details-dbox.png)


## <a name="download-order-history"></a>Transferir histórico de encomendas

Assim que a encomenda do Data Box estiver concluída, os dados nos discos de dispositivo são apagados. Quando a limpeza do dispositivo estiver concluída, pode transferir o histórico de encomendas no portal do Azure.

Execute os seguintes passos para transferir o histórico de encomendas.

1. Na sua encomenda do Data Box, aceda a **Descrição geral**. Certifique-se de que a encomenda é concluída. Se a encomenda estiver concluída e a limpeza do dispositivo também, então aceda a **Detalhes da encomenda**. A opção **Transferir histórico de encomendas** está disponível.

    ![Transferir histórico de encomendas](media/data-box-portal-admin/portal-admin-download-order-history.png)

2. Clique em **Transferir histórico de encomendas**. O histórico descarregado inclui um registo de registos de rastreamento de porta-aviões. Haverá dois conjuntos de registo correspondentes aos dois nós num dispositivo Data Box Heavy. Se deslocar para baixo para a parte inferior deste registo, pode ver as ligações para:
    
   - **Copy logs** - tenha a lista de ficheiros que errou durante a cópia de dados da Caixa de Dados para a sua conta de armazenamento Azure (ordem de importação) ou da sua conta de armazenamento para a Caixa de Dados (ordem de exportação).
   - **Registos de auditoria** - contenha informações sobre como aceder à Caixa de Dados e aceder a ações quando a Caixa de Dados estiver fora do datacenter Azure.
   - **Ficheiros BOM em ordem de importação** - tenha a lista de ficheiros (também conhecidos como manifesto de ficheiros) que pode descarregar durante a **Preparação para enviar** e tem nomes de ficheiros, tamanhos de ficheiros e os ficheiros.
   - **Verbose logs in export order** - tenha a lista de ficheiros com nomes de ficheiros, tamanhos de ficheiros e cálculo de checkum quando os dados foram copiados das contas de Armazenamento Azure para a Caixa de Dados.

   Aqui está uma amostra de um histórico de encomendas de uma ordem de importação.

    ```output
    -------------------------------
    Microsoft Data Box Order Report
    -------------------------------
    Name                                               : DataBoxTestOrder                              
    StartTime(UTC)                                     : 10/31/2018 8:49:23 AM +00:00                       
    DeviceType                                         : DataBox                                           
    -------------------
    Data Box Activities
    -------------------
    Time(UTC)                 | Activity                       | Status          | Description  
    
    10/31/2018 8:49:26 AM     | OrderCreated                   | Completed       |                                                   
    11/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |                                                   
    11/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 11/3/2018 1:36:43        PM at AMSTERDAM-NLD                                                                                
    11/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 11/4/2018        8:23:30 PM at AMSTERDAM-NLD                                                                        
    11/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time :          11/4/2018 11:43:34 PM at AMSTERDAM-NLD                                                               
    11/5/2018 1:38:20 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LEIPZIG-DEU. Local Time :        11/5/2018 1:38:20 AM at LEIPZIG-DEU                                                                
    11/5/2018 2:31:07 AM      | ShippingToCustomer             | InProgress      | Processed at LEIPZIG-DEU. Local Time : 11/5/2018          2:31:07 AM at LEIPZIG-DEU                                                                            
    11/5/2018 4:05:58 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LEIPZIG-DEU. Local Time :            11/5/2018 4:05:58 AM at LEIPZIG-DEU                                                                    
    11/5/2018 4:35:43 AM      | ShippingToCustomer             | InProgress      | Transferred through LUTON-GBR. Local Time :              11/5/2018 4:35:43 AM at LUTON-GBR                                                                         
    11/5/2018 4:52:15 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LUTON-GBR. Local Time :              11/5/2018 4:52:15 AM at LUTON-GBR                                                                        
    11/5/2018 5:47:58 AM      | ShippingToCustomer             | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 10/5/2018 5:47:58 AM at LONDON-HEATHROW-GBR                                                
    11/5/2018 6:27:37 AM      | ShippingToCustomer             | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            11/5/2018 6:27:37 AM at LONDON-HEATHROW-GBR                                                            
    11/5/2018 6:39:40 AM      | ShippingToCustomer             | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 11/5/2018 6:39:40 AM at LONDON-HEATHROW-GBR                                                    
    11/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in LAMBETH-GBR. Local        Time : 11/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
    11/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 11/5/2018            9:13:24 AM at LAMBETH-GBR                                                                               
    11/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 11/5/2018        12:03:04 PM at LAMBETH-GBR                                                                          
    1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25        PM at LAMBETH-GBR                                                                                       
    1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at LAMBETH-GBR. Local Time : 1/25/2019          8:03:55 PM at LAMBETH-GBR                                                                            
    1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in LAMBETH-GBR. Local Time :            1/25/2019 8:04:58 PM at LAMBETH-GBR                                                                    
    1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR.            Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
    1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time :            1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
    1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local          Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR                                                   
    1/26/2019 2:17:10 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility BRUSSELS-BEL. Local Time        : 1/26/2019 2:17:10 PM at BRUSSELS-BEL                                                              
    1/26/2019 2:31:57 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        2:31:57 PM at BRUSSELS-BEL                                                                          
    1/26/2019 3:37:53 PM      | ShippingToDataCenter           | InProgress      | Processed at BRUSSELS-BEL. Local Time : 1/26/2019        3:37:53 PM at BRUSSELS-BEL                                                                          
    1/27/2019 11:01:45 AM     | ShippingToDataCenter           | InProgress      | Departed Facility in BRUSSELS-BEL. Local Time :          1/27/2019 11:01:45 AM at BRUSSELS-BEL                                                                 
    1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD.            Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
    1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019            9:07:57 AM at AMSTERDAM-NLD                                                                             
    1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019            1:35:56 PM at AMSTERDAM-NLD                                                                            
    1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019        2:57:48 PM at AMSTERDAM-NLD                                                                         
    1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |                                              
    1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully                  
    1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.                               
    1/29/2019 4:55:00 PM      | DataCopy                       | Started         |                                                 
    2/2/2019 7:07:34 PM       | DataCopy                       | Completed       | Copy Completed.                                   
    2/4/2019 7:47:32 PM       | SecureErase                    | Started         |                                                  
    2/4/2019 8:01:10 PM       | SecureErase                    | Completed       | Azure Data Box:DEVICESERIALNO has been sanitized          according to NIST 800-88 Rev 1.                                                                       

    ------------------
    Data Box Log Links
    ------------------

    Account Name         : Gus                                                       
    Copy Logs Path       : databoxcopylog/DataBoxTestOrder_CHC533180024_CopyLog_73a81b2d613547a28ecb7b1612fe93ca.xml
    Audit Logs Path      : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024
    BOM Files Path       : azuredatabox-chainofcustodylogs\7fc6cac9-9cd6-4dd8-ae22-1ce479666282\chc533180024      
    ```

    Pode, em seguida, aceder à sua conta de armazenamento e ver os registos de cópia.

   ![Os registos de cópia para uma conta de armazenamento](media/data-box-portal-admin/portal-admin-storage-account-copy-logs.png)

   Também pode ver a cadeia de registos de custódia, que incluem os registos de auditoria e os ficheiros BOM.

   ![Registos de cadeia de custódia para uma conta de armazenamento](media/data-box-portal-admin/portal-admin-storage-account-chain-of-custody-logs.png)

## <a name="view-order-status"></a>Ver o estado da encomenda

Quando o estado do dispositivo muda no portal, é notificado através de um e-mail.

### <a name="statuses-for-import-order"></a>Estatutos da ordem de importação

Aqui estão os estatutos de uma ordem de importação.

|Estado da encomenda |Description |
|---------|---------|
|Encomendado     | Fez uma encomenda com êxito. <br>Se o dispositivo estiver disponível, a Microsoft identifica um dispositivo para envio e prepara-o. <br> Se o dispositivo não estiver disponível imediatamente, a encomenda será processada quando o dispositivo estiver disponível. A encomenda pode demorar entre vários dias a alguns meses a ser processada. Se a ordem não puder ser cumprida em 90 dias, a encomenda é cancelada e você é notificado.         |
|Processado     | O processamento da encomenda está concluído. De acordo com a sua encomenda, o dispositivo está preparado para envio no datacenter.         |
|Expedido     | A encomenda foi enviada. Utilize o ID de controlo apresentado na encomenda no portal para controlar o envio.        |
|Entregue     | A encomenda enviada foi entregue no endereço especificado na encomenda.        |
|Recolhido     |O envio de devolução foi recolhido e selecionado e analisado pela transportadora.         |
|Recebido     | O dispositivo é recebido e analisado no datacenter do Azure. <br> Após a inspeção, será iniciado o carregamento do dispositivo.      |
|Cópia de dados     | A cópia dos dados está em curso. Controle o progresso da cópia da sua encomenda no portal do Azure. <br> Aguarde até que a cópia de dados esteja concluída. |
|Concluído       |A encomenda foi concluída com êxito.<br> Verifique se os seus dados estão no Azure antes de eliminar os dados no local dos servidores.         |
|Concluído com erros| A cópia de dados foi concluída, mas ocorreram erros durante a cópia. <br> Reveja os registos de cópia com o caminho indicado no portal do Azure. Consulte [exemplos de registos de cópias quando o upload for concluído com erros](./data-box-logs.md#upload-completed-with-errors).   |
|Concluído com avisos| A cópia de dados foi completada, mas os seus dados foram modificados. Os dados tinham erros de blob ou de nome de ficheiro não críticos que foram corrigidos alterando os nomes de ficheiros ou blob. <br> Reveja os registos de cópia com o caminho indicado no portal do Azure. Tome nota das modificações dos seus dados. Consulte [exemplos de registos de cópias quando o upload estiver concluído com avisos](./data-box-logs.md#upload-completed-with-warnings).   |
|Cancelado            |A encomenda foi cancelada. <br> Ou cancelou a encomenda ou o serviço cancelou a encomenda depois de ocorrer um erro. Se a encomenda não puder ser cumprida em 90 dias, a encomenda também é cancelada e você é notificado.     |
|Limpeza | Os dados nos discos de dispositivo são apagados. A limpeza de dispositivo será considerada como concluída quando o histórico da encomenda ficar disponível para transferência no portal do Azure.|

### <a name="statuses-for-export-order"></a>Estatutos para a ordem de exportação

Aqui estão os estatutos para uma ordem de exportação.

|Estado da encomenda |Description |
|---------|---------|
|Encomendado     | Com sucesso, escruexou uma ordem de exportação. <br>Se o dispositivo estiver disponível, a Microsoft identifica um dispositivo para envio e prepara-o. <br> Se o dispositivo não estiver disponível de imediato, a encomenda será processada quando o dispositivo ficar disponível. A encomenda pode demorar entre vários dias a alguns meses a ser processada. Se não for possível satisfazer a encomenda num prazo de 90 dias, a mesma será cancelada e o requerente será notificado.         |
|Cancelado            |A encomenda foi cancelada. <br> Ou cancelou a encomenda (só pode cancelar antes de a encomenda ser processada) ou foi encontrado um erro e o serviço cancelou a encomenda. Se não for possível satisfazer a encomenda num prazo de 90 dias, a mesma também será cancelada e o requerente será notificado.     |
|Processado     | O processamento da encomenda está concluído. De acordo com a sua encomenda, o dispositivo está preparado para cópia de dados no datacenter. As ações do dispositivo são criadas.         |
|Cópia de dados em curso     | A cópia de dados das contas de Armazenamento Azure especificadas para o dispositivo está em andamento. Controle o progresso da cópia da sua encomenda no portal do Azure. <br> Aguarde até que a cópia de dados esteja concluída. |
|Cópia concluída     | A cópia de dados das contas de Armazenamento Azure especificadas para o dispositivo está completa. Um ficheiro de registo verboso (se a opção foi ativada na encomenda) e um registo de cópia são criados na sua conta de armazenamento. O registo verboso contém as informações em todos os ficheiros (nome, caminho, check-ução de computação) que são copiados para o dispositivo. O registo de cópias contém o resumo do processo de cópia, incluindo uma lista de ficheiros que não podiam ser copiados devido a quaisquer erros. <br> Os dados da conta de armazenamento permanecem como estão. |
|Cópia concluída com erros| A cópia de dados foi concluída, mas ocorreram erros durante a cópia. <br> Reveja os registos de cópias na conta de Armazenamento Azure utilizando o caminho fornecido no portal Azure. Consulte [exemplos de registos de cópias quando o download for concluído com erros](./data-box-logs.md#upload-completed-with-errors).   |
|Cópia completada com avisos| A cópia de dados da conta de Armazenamento Azure foi concluída, mas os dados tiveram erros não críticos. <br> Reveja os registos de cópia com o caminho indicado no portal do Azure. Tome nota dos erros não críticos. Consulte [exemplos de registos de cópias quando o download for concluído com avisos](./data-box-logs.md#upload-completed-with-warnings).   |
|Cópia falhou com erros| A cópia de dados da conta de Armazenamento Azure falhou e a encomenda é encerrada. Um dispositivo não será enviado. <br> Reveja os registos de cópias na conta de Armazenamento Azure utilizando o caminho fornecido no portal Azure. Consulte [exemplos de registos de cópias quando o download falhou com erros](./data-box-logs.md#upload-completed-with-errors).   |
|Expedido     |A encomenda foi enviada. Utilize o ID de controlo apresentado na encomenda no portal para controlar o envio.        |
|Entregue     |A encomenda enviada foi entregue no endereço especificado na encomenda.        |
|Recolhido     |O envio de devolução foi recolhido e selecionado e analisado pela transportadora.         |
|Recebido     | O dispositivo é recebido e analisado no datacenter do Azure. <br> O carregamento é inspecionado.      |
|Concluído           |A ordem está completa.     |
|Limpeza | Os dados nos discos de dispositivo são apagados. A limpeza de dispositivo será considerada como concluída quando o histórico da encomenda ficar disponível para transferência no portal do Azure.|

> [!NOTE]
> Se o trabalho de cópia para exportar dados das contas de Armazenamento Azure para a Caixa de Dados se completar com erros ou avisos, o dispositivo ainda envia. Só em caso de falha de cópia, a encomenda é terminada e o dispositivo não é enviado.


Se estiver a utilizar o envio auto-gerido, depois de a cópia estar completa e antes de receber o dispositivo, verá os seguintes estados (em vez dos mencionados na tabela anterior):

|Estado da encomenda |Description |
|---------|---------|
|Pronto para recolha no Centro de Dados Azure      |O dispositivo está pronto para ser recolhido no datacenter Azure.        |
|Recolhido    |Pegou o dispositivo.         |
|Pronto para receber no Centro de Dados Azure     |O dispositivo está pronto para ser recebido no datacenter Azure.        |
|Recebido     |O dispositivo foi recebido no datacenter Azure.      |





## <a name="next-steps"></a>Passos seguintes

- Saiba como resolver problemas com a [Caixa de Dados e a Caixa de Dados Problemas Problemas Pesados](data-box-troubleshoot.md).