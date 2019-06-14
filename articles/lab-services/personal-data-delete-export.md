---
title: Como eliminar e exportar os dados pessoais do Azure DevTest Labs | Documentos da Microsoft
description: Saiba como excluir e exportar os dados pessoais do serviço Azure DevLast Labs para suportar as suas obrigações sob Regulamento geral de proteção de dados (GDPR).
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: spelluru
ms.openlocfilehash: e681652c13e521bd33524e247db65088f47a794c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60394987"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Exportar ou eliminar os dados pessoais do Azure DevTest Labs
Este artigo fornece passos para excluir e exportar os dados pessoais do serviço Azure DevTest Labs. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>Os dados pessoais recolher o DevTest Labs?
DevTest Labs recolhe duas partes principais de dados pessoais do utilizador. Eles são: endereço de e-mail do utilizador e o ID de objeto do utilizador. Estas informações são fundamentais para o serviço fornecer funcionalidades de versão para administradores de laboratório e utilizadores de laboratório.

### <a name="user-email-address"></a>Endereço de e-mail do utilizador
DevTest Labs utiliza o endereço de e-mail do utilizador para enviar notificações de e-mail de encerramento automático para os utilizadores de laboratório. A mensagem de e-mail notifica os utilizadores da sua máquina a ser encerrado. Os utilizadores podem adiar ou ignorar o encerramento, se quiser fazê-lo. Configurar o endereço de e-mail no nível de laboratório ou ao nível da VM.

**Definir um e-mail no laboratório:**

![Definir um e-mail ao nível do laboratório](./media/personal-data-delete-export/lab-user-email.png)

**Definir um e-mail na VM:**

![Definir um e-mail ao nível da VM](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>ID de objeto do utilizador
DevTest Labs utiliza o ID de objeto de utilizador para mostrar as tendências de custo de mês ano-mês e o custo por informações de recursos para os administradores de laboratório. Ele permite que-los controlar os custos e gerir limiares para o laboratório. 

**Tendência de custo estimado para o mês de calendário atual:** 
![tendência de custo estimado para o mês de calendário atual](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Mês até à data custo estimado de por recurso:** 
![mês até à data custo estimado de por recurso](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Por que precisamos para estes dados pessoais?
O serviço de DevTest Labs utiliza os dados pessoais para fins operacionais. Estes dados são essenciais para o serviço fornecer funcionalidades-chave. Se definir uma política de retenção no endereço de e-mail de utilizador, os utilizadores de laboratório não recebem notificações de e-mail de encerramento automático atempada após o respetivo endereço de e-mail é eliminado do nosso sistema. Da mesma forma, o administrador de laboratório não é possível ver mês ano-mês as tendências de custos e custos por recurso para máquinas no seus laboratórios, se o objeto de utilizador que são eliminados IDs com base numa política de retenção. Por conseguinte, estes dados tem de ser mantidos, desde que o recurso do utilizador está ativo no laboratório.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Como posso ter o sistema para se esqueça de meus dados pessoais?
Como um utilizador de laboratório, se gostaria de ter estes dados pessoais eliminados, pode fazê-ao eliminar o recurso correspondente no laboratório. O serviço de laboratórios Dev/Test torna anónimas os dados pessoais eliminados 30 dias depois de ser eliminado pelo utilizador.

Por exemplo, se eliminar a sua VM, ou removido o seu endereço de e-mail, o serviço de laboratórios Dev/Test torna anónimas estes dados 30 dias após o recurso seja eliminado. A política de retenção de 30 dias após a eliminação para se certificar de que fornecemos uma projeção de custo de mês ano-mês precisos para o administrador de laboratório.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Como posso pedir uma exportação nos meus dados pessoais?
Como um utilizador de laboratório, pode solicitar uma exportação nos dados pessoais que armazena o serviço de DevTest Labs. Para pedir para uma exportação, navegue para o **os dados pessoais** opção a **descrição geral** página do seu laboratório. Selecione o **pedido de exportação** botão inicia a criação de um ficheiro do excel que pode ser baixado na conta de armazenamento do seu administrador de laboratório. Em seguida, pode contactar o seu administrador de laboratório para ver estes dados.

1. Selecione **os dados pessoais** no menu da esquerda. 

    ![Página de dados pessoais](./media/personal-data-delete-export/personal-data-page.png)
2. Selecione o **grupo de recursos** que contém o laboratório.

    ![Selecionar grupo de recursos](./media/personal-data-delete-export/select-resource-group.png)
3. Selecione o **conta de armazenamento** no grupo de recursos.
4. Sobre o **conta de armazenamento** página, selecione **Blobs**.

    ![Selecione o mosaico de Blobs](./media/personal-data-delete-export/select-blobs-tile.png)
5. Selecione o contentor com o nome **labresourceusage** na lista de contentores.

    ![Selecione o contentor de BLOBs](./media/personal-data-delete-export/select-blob-container.png)
6. Selecione o **pasta** o nome de seu laboratório. Encontrará **csv** ficheiros para **discos** e **máquinas virtuais** em seu laboratório nesta pasta. Pode transferir estes ficheiros csv, filtrar o conteúdo para o utilizador de laboratório pedir um acesso e partilhá-lo com os mesmos.

    ![Transferir o ficheiro CSV](./media/personal-data-delete-export/download-csv-file.png)

## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos: 

- [Definir políticas para um laboratório](devtest-lab-get-started-with-lab-policies.md)
- [Perguntas mais frequentes](devtest-lab-faq.md)
