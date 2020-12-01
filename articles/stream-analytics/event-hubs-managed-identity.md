---
title: Utilize identidades geridas para aceder ao Event Hub a partir de um trabalho de Azure Stream Analytics (Preview)
description: Este artigo descreve como usar identidades geridas para autenticar o seu trabalho Azure Stream Analytics para a entrada e saída do Azure Event Hubs.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: c65833e5bf581c6326bf9362c7e5fc00a985d301
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355114"
---
# <a name="use-managed-identities-to-access-event-hubfrom-an-azure-stream-analytics-job-preview"></a>Utilize identidades geridas para aceder ao Event Hub a partir de um trabalho de Azure Stream Analytics (Preview)

O Azure Stream Analytics suporta a autenticação de identidade gerida tanto para a entrada e saída do Azure Event Hubs. Identidades geridas eliminam as limitações dos métodos de autenticação baseados no utilizador, como a necessidade de reautenenciar devido a alterações de senha ou expirações simbólicas do utilizador que ocorrem a cada 90 dias. Quando remove a necessidade de autenticação manual, as suas implementações stream Analytics podem ser totalmente automatizadas.  

Uma identidade gerida é uma aplicação gerida registada no Azure Ative Directory que representa um determinado trabalho de Stream Analytics. A aplicação gerida é usada para autenticar um recurso direcionado, incluindo Os Centros de Eventos que estão por trás de uma firewall ou rede virtual (VNet). Para obter mais informações sobre como contornar firewalls, consulte [Permitir o acesso aos espaços de nomes do Azure Event Hubs através de pontos finais privados.](../event-hubs/private-link-service.md#trusted-microsoft-services)

Este artigo mostra-lhe como ativar a Identidade Gerida para uma entrada ou saída de um stream analytics através do portal Azure.Antes de ativar a Identidade Gerida, tem primeiro de ter um trabalho stream Analytics e recurso Desatado do Centro de Eventos.

## <a name="create-a-managedidentity"></a>Criar uma identidade gerida  

Primeiro, cria-se uma identidade gerida para o seu trabalho Azure Stream Analytics.  

1. No portal Azure, abra o seu trabalho Azure Stream Analytics.  

1. A partir do menu de navegação à esquerda, **selecione Identidade Gerida** localizada em    *Configuração.* Em seguida, verifique a caixa ao lado **da Identidade Gerida atribuída ao Sistema** e   selecione **Save**.

   :::image type="content" source="media/event-hubs-managed-identity/system-assigned-managed-identity.png" alt-text="Identidade gerida atribuída ao sistema":::  

1. Um diretor de serviço para a identidade do trabalho stream Analytics é criado no Azure Ative Directory. O ciclo de vida da identidade recém-criada é gerido por Azure. Quando o trabalho stream Analytics é eliminado, a identidade associada (isto é, o principal de serviço) é automaticamente eliminada pela Azure.  

   Quando guarda a configuração, o ID do Objeto (OID) do principal de serviço está listado como iD principal como mostrado abaixo:  

   :::image type="content" source="media/event-hubs-managed-identity/principal-id.png" alt-text="Principal ID":::

   O diretor de serviço tem o mesmo nome que o trabalho da Stream Analytics. Por exemplo, se o nome do seu trabalho  `MyASAJob` for, o nome do chefe de serviço também é  `MyASAJob` .  

## <a name="grant-the-stream-analytics-job-permissionsto-access-the-event-hub"></a>Conceda ao Stream Analytics permissões de emprego para aceder ao Centro de Eventos

Para que o trabalho do Stream Analytics aceda ao seu Centro de Eventos utilizando identidade gerida, o responsável pelo serviço que criou deve ter permissões especiais para o Centro de Eventos.

1. Vá ao **Controlo de Acesso (IAM)** no seu Centro de Eventos.

1. Selecione **+ Adicionar** e **Adicionar função.**

1. Na página **de atribuição de funções Adicionar,** insira as seguintes opções:

   |Parâmetro|Valor|
   |---------|-----|
   |Função|Proprietário de dados Azure Event Hubs|
   |Atribuir acesso a|Utilizador, grupo ou principal de serviço|
   |Selecione|Insira o nome do seu trabalho stream Analytics|

   :::image type="content" source="media/event-hubs-managed-identity/add-role-assignment.png" alt-text="Adicionar atribuição de função":::

1. **Selecione Guardar** e esperar um minuto ou mais para que as alterações se propaguem.

Você também pode conceder este papel ao nível do Event Hub Namespace, que irá naturalmente propagar as permissões a todos os Centros de Eventos criados sob o mesmo. Ou seja, todos os Centros de Eventos sob um Espaço de Nome podem ser usados como um recurso de autenticação de identidade gerida no seu trabalho stream Analytics.

## <a name="create-anevent-hub-input-or-output"></a>Criar uma entrada ou saída do Event Hub  

Agora que a sua identidade gerida está configurada, está pronto para adicionar o recurso Event Hub como entrada ou saída para o seu trabalho stream Analytics.  

### <a name="add-the-event-hub-as-an-input"></a>Adicione o Centro de Eventos como uma entrada 

1. Vá ao seu trabalho stream Analytics e navegue para a página **Inputs** em **Job Topology**.

1. **Selecione Adicionar Entrada de Fluxo > Centro de Eventos**. Na janela das propriedades de entrada, procure e selecione o Seu Centro de Eventos e selecione **Identidade Gerida** a partir do menu suspenso do modo de *autenticação.*

1. Preencha o resto das propriedades e selecione **Guardar.**

### <a name="add-the-event-hub-as-an-output"></a>Adicione o Centro de Eventos como uma saída

1. Vá ao seu trabalho stream Analytics e navegue para a página **outputs** em **Job Topology**.

1. **Selecione Adicionar > Centro de Eventos**. Na janela das propriedades de saída, procure e selecione o Seu Centro de Eventos e selecione **Identidade Gerida** a partir do menu suspenso do modo de *autenticação.*

1. Preencha o resto das propriedades e selecione **Guardar.**

## <a name="next-steps"></a>Passos seguintes

* [Saída de Hubs de Eventos da Azure Stream Analytics](event-hubs-output.md)
* [Transmitir dados a partir dos Hubs de Eventos](stream-analytics-define-inputs.md#stream-data-from-event-hubs)