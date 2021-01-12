---
title: Resolver problemas de conectividade entre o Synapse Studio e o armazenamento
description: Resolver problemas de conectividade entre o Synapse Studio e o armazenamento
author: saveenr
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: d570b4a8df5d59cf8828985bee20852d6bc79b1e
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98117066"
---
# <a name="troubleshoot-connectivity-between-azure-synapse-analytics-synapse-studio-and-storage"></a>Resolver problemas de conectividade entre o Synapse Studio do Azure Synapse Analytics e o armazenamento

No Synapse Studio, pode explorar recursos de dados localizados no seu armazenamento ligado. Este guia irá ajudá-lo a resolver problemas de conectividade quando estiver a tentar aceder aos seus recursos de dados. 

## <a name="case-1-storage-account-lacks-proper-permissions"></a>Caso #1: Conta de armazenamento carece de permissões adequadas

Se a sua conta de armazenamento não tiver as permissões adequadas, não poderá expandir a estrutura de armazenamento através de "Datas" --> "Linked" no Synapse Studio. Veja a imagem do sintoma de emissão abaixo. 

A mensagem de erro detalhada pode variar, mas o significado geral da mensagem de erro é: "Este pedido não está autorizado a executar esta operação."

No nó de armazenamento ligado:  
![Problema de conectividade de armazenamento 1](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1.png)

No nó do recipiente de armazenamento:  
![Problema de conectividade de armazenamento 1a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1a.png)

**SOLUÇÃO**: Para atribuir a sua conta à função adequada, consulte [utilizar o portal Azure para atribuir uma função Azure para acesso a dados de bolhas e filas](../../storage/common/storage-auth-aad-rbac-portal.md)


## <a name="case-2-failed-to-send-the-request-to-storage-server"></a>Caso #2: Não enviou o pedido para o servidor de armazenamento

Ao selecionar a seta para expandir a estrutura de armazenamento em "Data" --> "Linked" no Synapse Studio, poderá ver a questão "REQUEST_SEND_ERROR" no painel esquerdo. Veja a imagem do sintoma de emissão abaixo:

No nó de armazenamento ligado:  
![Problema de conectividade de armazenamento 2](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2.png)

No nó do recipiente de armazenamento:  
![Problema de conectividade de armazenamento 2a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2a.png)

Pode haver várias razões possíveis por trás desta questão:

### <a name="the-storage-resource-is-behind-a-vnet-and-a-storage-private-endpoint-needs-to-configure"></a>O recurso de armazenamento está por trás de um vNet e um ponto final privado de armazenamento precisa configurar

**SOLUÇÃO**: Neste caso, é necessário configurar o ponto final privado de armazenamento para a sua conta de armazenamento. Para configurar o ponto final privado de armazenamento para vNet, consulte [utilizar o portal Azure para atribuir uma função Azure para acesso a dados de bolhas e filas](../security/how-to-connect-to-workspace-from-restricted-network.md).

Pode utilizar o comando "nslookup \<storage-account-name\> .dfs.core.windows.net" para verificar a conectividade após a configuração do ponto final privado de armazenamento. Deve devolver uma corda semelhante a: \<storage-account-name\> ".privatelink.dfs.core.windows.net".

### <a name="the-storage-resource-is-not-behind-a-vnet-but-the-blob-service-azure-ad-endpoint-is-not-accessible-due-to-firewall-configured"></a>O recurso de armazenamento não está por trás de um vNet, mas o ponto final do serviço Blob (Azure AD) não está acessível devido à firewall configurada

**SOLUÇÃO**: Neste caso, tem de abrir a sua conta de armazenamento no portal Azure. Na navegação à esquerda, desloque-se para baixo para **Suporte + resolução de problemas** e selecione **a verificação** de conectividade para verificar o estado de conectividade do **serviço Blob (Azure AD).** Se não estiver acessível, siga o guia promovido para verificar a configuração de **Firewalls e redes virtuais** na sua página de conta de armazenamento. Para obter mais informações sobre firewalls de armazenamento, consulte [firewalls de armazenamento Configure Azure e redes virtuais.](../../storage/common/storage-network-security.md)

### <a name="other-issues-to-check"></a>Outras questões a verificar 

* O recurso de armazenamento a que está a aceder é o Azure Data Lake Storage Gen2 e está por trás de uma firewall e vNet (com o ponto final privado de armazenamento configurado) ao mesmo tempo.
* O recurso do contentor a que está a aceder foi apagado ou não existe.
* Travessia-inquilino: o inquilino do espaço de trabalho que o utilizador costumava fazer login não é o mesmo com o inquilino da conta de armazenamento. 


## <a name="next-steps"></a>Passos seguintes
Se os passos anteriores não ajudarem a resolver o seu problema, [crie um bilhete de apoio](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).