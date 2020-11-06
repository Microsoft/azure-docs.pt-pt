---
title: Guia do administrador do portal do Azure Data Box Disk | Microsoft Docs
description: Saiba como gerir o Disco caixa de dados utilizando o portal Azure. Gerir encomendas, gerir discos e acompanhar o estado de uma ordem à medida que progride.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 01/09/2019
ms.author: alkohli
ms.openlocfilehash: 538a650c6063422f89c8ed3d1753981a293693b7
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94338174"
---
# <a name="use-azure-portal-to-administer-your-data-box-disk"></a>Utilizar o portal do Azure para administrar o Data Box Disk

Os tutoriais neste artigo aplicam-se ao Microsoft Azure Data Box Disk durante a Pré-visualização. Este artigo descreve alguns dos fluxos de trabalho complexos e tarefas de gestão que podem ser executadas no Data Box Disk. 

Pode gerir o Data Box Disk através do portal do Azure. Este artigo foca as tarefas que pode efetuar com o portal do Azure. Utilize o portal do Azure para gerir encomendas, gerir discos e controlar o estado da encomenda à medida que avança para a fase terminal.

## <a name="cancel-an-order"></a>Cancelar uma encomenda

Poderá ter de cancelar uma encomenda por vários motivos depois de fazer a encomenda. Só pode cancelar a encomenda antes de a preparação do disco ser iniciada. Assim que os discos estiverem preparados e a encomenda tiver sido processada, não é possível cancelar a encomenda. 

Execute os seguintes passos para cancelar uma encomenda.

1.  Aceda a **Descrição Geral > Cancelar**. 

    ![Cancelar o comando no separador Visão Geral para uma encomenda](media/data-box-portal-ui-admin/portal-ui-admin-cancel-command.png)

2.  Escreva um motivo para cancelar a encomenda.  

    ![Razão para cancelar uma encomenda](media/data-box-portal-ui-admin/portal-ui-admin-cancel-order-reason.png)

3.  Assim que a encomenda é cancelada, o portal atualiza o estado da encomenda e apresenta-o como **Cancelado**.

    ![Encomenda cancelada](media/data-box-portal-ui-admin/portal-ui-admin-canceled-order.png)

Não recebe uma notificação por e-mail quando a encomenda é cancelada.

## <a name="clone-an-order"></a>Clonar uma encomenda

A clonagem é útil em determinadas situações. Por exemplo, um utilizador utilizou o Data Box Disk para transferir alguns dados. À medida que são gerados mais dados, são necessários mais discos para transferir dados para o Azure. Neste caso, a mesma encomenda pode ser clonada.

Execute os seguintes passos para clonar uma encomenda.

1.  Aceda a **Descrição Geral > Clone**. 

    ![Comando clone no separador Visão Geral para uma encomenda](media/data-box-portal-ui-admin/portal-ui-admin-clone-command.png)

2.  Todos os detalhes da encomenda permanecem iguais. O nome da encomenda é o nome da encomenda original anexada por *-Clone*. Selecione a caixa de verificação para confirmar que reviu as informações de privacidade. Clique em **Criar**.    

O clone é criado em apenas alguns minutos e o portal é atualizado para mostrar a nova encomenda.

[![Ordem clonada](media/data-box-portal-ui-admin/portal-ui-admin-cloned-order.png)](media/data-box-portal-ui-admin/portal-ui-admin-cloned-order.png#lightbox) 

## <a name="delete-order"></a>Eliminar encomenda

Pode querer eliminar uma encomenda quando a encomenda for concluída. A encomenda contém as suas informações pessoais, como o nome, o endereço e as informações de contacto. Estas informações pessoais são eliminadas quando a encomenda é eliminada.

Só pode eliminar as encomendas que foram concluídas ou canceladas. Execute os seguintes passos para eliminar uma encomenda.

1. Vá para **Todos os recursos**. Procure a sua encomenda.

    ![Pedidos de pesquisa](media/data-box-portal-ui-admin/portal-ui-admin-search-data-box-disk-orders.png)

2. Clique na encomenda que pretende eliminar e aceda a **Descrição Geral**. Na barra de comandos, clique em **Eliminar**.

    ![Apagar uma encomenda](media/data-box-portal-ui-admin/portal-ui-admin-delete-command.png)

3. Introduza o nome da encomenda quando lhe for pedido para confirmar a eliminação da encomenda. Clique em **Eliminar**.

     ![Confirmar a eliminação da ordem](media/data-box-portal-ui-admin/portal-ui-admin-confirm-deletion.png)


## <a name="download-shipping-label"></a>Transferir etiqueta de envio

Poderá ter de transferir a etiqueta de envio, se a etiqueta de envio para devolução enviada com os seus discos estiver no local incorreto ou se tiver perdido. 

Execute os seguintes passos para transferir uma etiqueta de envio.
1.  Aceda a **Descrição geral > Transferir etiqueta de envio**. Esta opção só está disponível após o disco ser enviado. 

    ![Transferir etiqueta de envio](media/data-box-portal-ui-admin/portal-ui-admin-download-shipping-label.png)

2.  Esta ação transfere a seguinte etiqueta de envio para devolução. Guarde a etiqueta, imprima-a e afixe-a ao pacote para devolução.

    ![Examinar a etiqueta de envio](media/data-box-portal-ui-admin/portal-ui-admin-example-shipping-label.png)

## <a name="edit-shipping-address"></a>Editar o endereço de envio

Poderá ter de editar o endereço de envio assim que a encomenda for feita. Isto só está disponível até o disco ser expedido. Assim que o disco for expedido, esta opção deixa de estar disponível.

Execute os seguintes passos para editar a encomenda.

1. Aceda a **Detalhes da encomenda > Editar endereço de envio**.

    ![Editar o comando de endereço de envio em detalhes da Ordem](media/data-box-portal-ui-admin/portal-ui-admin-edit-shipping-address-command.png)

2. Pode agora editar o endereço de envio e, em seguida, guardar as alterações.

    ![Editar caixa de diálogo de endereço de envio](media/data-box-portal-ui-admin/portal-ui-admin-edit-shipping-address-dbox.png)

## <a name="edit-notification-details"></a>Editar detalhes de notificação

Poderá ter de alterar os utilizadores que pretende que recebam os e-mails de estado da encomenda. Por exemplo, um utilizador tem de manter-se informado sobre quando o disco é entregue ou recolhido. Outro utilizador poderá ter de ser informado quando a cópia de dados estiver completa para que possam verificar se os dados estão na conta de armazenamento Azure antes de os eliminar da fonte. Nestes casos, pode editar os detalhes de notificação.

Execute os seguintes passos para editar os detalhes de notificação.

1. Aceda a **Detalhes da encomenda > Editar detalhes de notificação**.

    ![Editar o comando de detalhes de notificação em detalhes da Ordem](media/data-box-portal-ui-admin/portal-ui-admin-edit-notification-details-command.png)

2. Pode agora editar os detalhes de notificação e, em seguida, guardar as alterações.
 
    ![Editar caixa de diálogo de detalhes de notificação](media/data-box-portal-ui-admin/portal-ui-admin-edit-notification-details-dbox.png)

## <a name="view-order-status"></a>Ver o estado da encomenda

|Estado da encomenda |Description |
|---------|---------|
|Encomendado     | Fez uma encomenda com êxito. <br> Se os discos não estiverem disponíveis, receberá uma notificação. <br>Se os discos estiverem disponíveis, a Microsoft identifica um disco para envio e prepara o pacote do disco.        |
|Processado     | O processamento da encomenda está concluído. <br> Durante o processamento da encomenda, ocorrem as ações seguintes:<li>Os discos são encriptados com a encriptação AES-128 BitLocker. </li> <li>O Data Box Disk está bloqueado para impedir qualquer acesso não autorizado.</li><li>A chave de acesso que desbloqueia os discos é gerada durante este processo.</li>        |
|Expedido     | A encomenda foi enviada. Deverá receber a encomenda dentro de 1 a 2 dias.        |
|Entregue     | A encomenda foi entregue no endereço especificado na encomenda.        |
|Recolhido     |O envio para devolução foi recolhido. <br> Uma vez que o envio é recebido no Centro de Dados Azure, os dados serão automaticamente enviados para a Azure.         |
|Recebido     | Os discos foram recebidos no datacenter do Azure. A cópia dos dados começará em breve.        |
|Dados copiados     |A cópia dos dados está em curso.<br> Aguarde até que a cópia de dados esteja concluída.         |
|Concluído       |A encomenda foi concluída com êxito.<br> Verifique se os seus dados estão no Azure antes de eliminar os dados no local dos servidores.         |
|Concluído com erros| A cópia de dados foi concluída, mas foram recebidos erros. <br> Reveja os registos de erro para upload utilizando o caminho fornecido na **Visão Geral**. Para mais informações, aceda a [baixar registos de erro de upload](data-box-disk-troubleshoot-upload.md#download-logs).   |
|Cancelado            |A encomenda foi cancelada. <br> Cancelou a encomenda ou foi encontrado um erro e o serviço cancelou a encomenda.     |



## <a name="next-steps"></a>Passos seguintes

- Saiba como [Resolver problemas do Data Box Disk](data-box-disk-troubleshoot.md).
