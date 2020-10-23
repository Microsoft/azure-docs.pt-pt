---
title: Configure lockbox para caixa de dados Azure
description: Saiba como utilizar o Lockbox do Cliente com a Caixa de Dados Azure.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 07/10/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 178ad169c1b576458f38b440ca79f4bb9eb012f5
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92124916"
---
# <a name="use-customer-lockbox-for-azure-data-box-preview"></a>Utilize o lockbox do cliente para a caixa de dados Azure (pré-visualização)

A Azure Data Box é usada para transferir dados do cliente de e para a Azure. Existem casos em que o Microsoft Support poderá ter de aceder aos dados dos clientes durante um pedido de Suporte. Pode utilizar o Customer Lockbox como interface para rever e aprovar ou rejeitar estes pedidos de acesso a dados. 

Este artigo abrange a forma como os pedidos de bloqueio do cliente são iniciados e rastreados para a importação de Caixa de Dados, bem como as encomendas de exportação. O artigo aplica-se tanto aos dispositivos Azure Data Box como aos dispositivos Azure Data Box Heavy. 

## <a name="devops-workflow-for-data-access"></a>Fluxo de trabalho de DevOps para acesso a dados

A equipa de Operações de Suporte e Caixa de Dados da Microsoft normalmente não acede aos dados dos clientes. Tentam resolver problemas utilizando ferramentas padrão e telemetria. <!--The only scenarios where there is a need to access customer data is when there is an issue with the data that needs to be fixed. For example, if the data is copied to a wrong folder or is in an incorrect format and is likely to result in an upload or download failure, then Microsoft will try to access your data in the Azure datacenter.--> 

Se os problemas não puderem ser resolvidos e exigirem que o Microsoft Support investigue ou repare dados, solicitam acesso elevado através do portal Just in Time (JIT). O portal JIP valida o nível de permissão, fornece autenticação multi-factor e também inclui uma aprovação dos Approvers Internos da Microsoft. Por exemplo, o aprovador pode ser Gestor de DevOps. 

Depois de o seu pedido de acesso elevado for aprovado através do portal JIT, caso tenha ativado o Lockbox, a Microsoft também exigirá o seu consentimento explícito para aceder aos dados. O acesso é solicitado e rastreado através do serviço Lockbox do Cliente no portal. 

Se não tiver ativado o Lockbox, então o seu consentimento não é necessário para aceder aos dados.


## <a name="prerequisites-for-access-request"></a>Pré-requisitos para pedido de acesso

Antes de começar, confirme que:

1. Criou uma encomenda Azure Data Box de acordo com as instruções:
    1. [Tutorial: Encomende caixa de dados Azure](data-box-deploy-ordered.md) para encomendas de importação.
    1. [Tutorial: Encomende caixa de dados Azure](data-box-deploy-export-ordered.md) para encomendas de exportação.

2. Configurar o cofre do cliente para a Caixa de Dados. Este é um serviço de opt-in. 

    1. O Bloqueio do Cliente encontra-se atualmente em pré-visualização para o serviço Data Box. Para ativar o bloqueio do cliente para caixa de dados para a sua organização, inscreva-se no [Lockbox do Cliente para pré-visualização pública Azure](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Kwz02N6XVCoKNpxIpqE_hUNzlTUUNYVkozOVlFNVRSWDVHRkkwTFQyViQlQCN0PWcu).
    2. O Customer Lockbox está automaticamente disponível para todos os clientes que tenham um plano de suporte Azure com um nível mínimo de Desenvolvedor. <!--How do you enable Lockbox? change this for Azure Data Box, perhaps you need a different support plan When you have an eligible support plan, no action is required by you to enable Customer Lockbox. Customer Lockbox requests are initiated by a Microsoft engineer if this action is needed to progress a support ticket that is filed from somebody in your organization.-->

3. Um pedido de serviço ou um bilhete de apoio já está aberto para esta edição. Para obter informações sobre o bilhete de apoio, consulte [Arquivar um pedido de serviço para Caixa de Dados.](data-box-disk-contact-microsoft-support.md)


## <a name="track-approve-request-via-lockbox"></a>Track, aprovar pedido via Lockbox

Para acompanhar e aprovar um pedido de acesso aos dados dos clientes, siga estes passos:

1. A Microsoft deteta que existe um problema durante o upload ou download dos dados no Centro de Dados Azure. Por exemplo, a ordem Caixa de Dados é interrompida durante a fase **de Cópia de Dados.** 

    O engenheiro de suporte conecta-se à Data Box através da sessão de Suporte e tenta resolver o problema utilizando ferramentas padrão e telemetria. Se os discos data box estiverem bloqueados e as ações não estiverem acessíveis, o engenheiro de suporte cria um pedido de Lockbox. 
 
2. Quando o pedido é criado, normalmente a notificação vai para administrador de subscrição, mas também pode configurar um grupo para notificações. 

3. Pode ver o pedido de bloqueio no portal Azure para a sua aprovação. 

    ![Pedido no portal Azure](./media/data-box-customer-lockbox/3-lockbox-request-azure-portal.png)

    Para aprovar o pedido de bloqueio do portal, pode selecionar **Aprovar**.

    ![Aprovar pedido](./media/data-box-customer-lockbox/4-lockbox-request-details-azure-portal.png)


    Uma vez aprovado o pedido, os discos do dispositivo são desbloqueados e as ações estão acessíveis na sessão de suporte.

4. O engenheiro de suporte resolve o problema do upload e, em seguida, desativa a sessão de suporte.

Após a emissão ser resolvida, o trabalho de cópia de dados irá progredir para a conclusão.


## <a name="next-steps"></a>Passos seguintes

- [Sistema de Proteção de Dados do Cliente para o Microsoft Azure](../security/fundamentals/customer-lockbox-overview.md)

<!--- [Approve, audit support access requests to VMs using Customer Lockbox for Azure](https://azure.microsoft.com/blog/approve-audit-support-access-requests-to-vms-using-customer-lockbox-for-azure/)-->