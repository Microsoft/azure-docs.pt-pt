---
title: Configurar chaves geridas pelo cliente no Azure Sentinel. Microsoft Docs
description: Saiba como configurar as teclas geridas pelo cliente (CMK) em Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/19/2020
ms.author: yelevin
ms.openlocfilehash: 58936066abcbe4c3f9fcfad78bf914c74079aa95
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88141793"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>Configurar a chave gerida pelo cliente Azure Sentinel


Este artigo fornece informações de fundo e passos para configurar uma chave gerida pelo cliente (CMK) para a Azure Sentinel. A CMK permite que todos os dados guardados ou enviados para o Azure Sentinel sejam encriptados em todos os recursos de armazenamento relevantes com uma chave Azure Key Vault criada ou propriedade por si.

> [!NOTE]
> -   A capacidade Azure Sentinel CMK é fornecida apenas a clientes que são **novos** e o acesso a esta capacidade é controlado pelo registo de recursos Azure.Pode solicitar o acesso através de azuresentinelCMK@microsoft.com contacto, e como a capacidade está disponível, os pedidos pendentes serão aprovados.
> -   A capacidade Azure Sentinel CMK só está disponível em regiões dos EUA, West US 2 e South-Central.
> -   A capacidade CMK só está disponível para os clientes que enviam 1TB por dia ou mais. Receberá informações sobre preços adicionais quando se candidatar à Microsoft para provisão de CMK na sua subscrição Azure. Saiba mais sobre [os preços do Log Analytics.](../azure-monitor/platform/manage-cost-storage.md#log-analytics-dedicated-clusters)

## <a name="how-cmk-works"></a>Como funciona a CMK 

A solução Azure Sentinel utiliza vários recursos de armazenamento para recolha de registos e funcionalidades, incluindo Log Analytics e outros. Como parte da configuração Azure Sentinel CMK, você terá que configurar as definições cmk sobre os recursos de armazenamento relacionados também. Os dados guardados em recursos de armazenamento que não o Log Analytics também serão encriptados.

Saiba mais sobre [a CMK.](../azure-monitor/platform/customer-managed-keys.md#customer-managed-key-cmk-overview)

> [!NOTE]
> Se ativar a CMK no Azure Sentinel, qualquer função de visualização pública que não suporte CMK não será ativada.

## <a name="enable-cmk"></a>Ativar CMK 

Para provisão cmk, siga estes passos: 

1.  Crie um cofre de chaves Azure e uma chave de armazenamento.

2.  Ativar a CMK no seu espaço de trabalho Log Analytics.

3.  Registe-se na Cosmos DB.

4.  Adicione uma política de acesso à sua instância Azure Key Vault.

5.  Ativar a CMK em Azure Sentinel.

6.  Ativar o Azure Sentinel.

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>PASSO 1: Criar um cofre de chave azul e chave de armazenamento

1.  [Crie o recurso Azure Key Vault,](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-manage-portal?view=azs-1910)em seguida, gere ou importe uma chave a ser usada para encriptação de dados.
    > [!NOTE]
    >  O Cofre da Chave Azure deve ser configurado como recuperável para proteger a sua chave e o acesso.

1.  [Ligue as opções de recuperação:](../key-vault/general/best-practices.md#turn-on-recovery-options)

    -   Certifique-se de que [o Soft Delete](../key-vault/general/soft-delete-overview.md) está ligado.

    -   Ligue [a proteção de purga](../key-vault/general/soft-delete-overview.md#purge-protection) para evitar a eliminação forçada do segredo/cofre mesmo após a eliminação suave.

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>PASSO 2: Ativar a CMK no seu espaço de trabalho Log Analytics

Siga as instruções na [configuração da chave gerida pelo cliente do Azure Monitor](../azure-monitor/platform/customer-managed-keys.md) para criar um espaço de trabalho CMK que será utilizado como espaço de trabalho Azure Sentinel nos seguintes passos.

### <a name="step-3-register-for-cosmos-db"></a>PASSO 3: Registre-se para Cosmos DB

Azure Sentinel trabalha com a Cosmos DB como um recurso adicional de armazenamento. Certifique-se de se registar no Cosmos DB.

Siga as instruções da Cosmos DB para registar o fornecedor de recursos [DB da Azure Cosmos](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) para a sua assinatura Azure.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>PASSO 4: Adicione uma política de acesso à sua instância Azure Key Vault

Certifique-se de adicionar acesso da Cosmos DB à sua instância Azure Key Vault. Siga as instruções do Cosmos DB para [adicionar uma política de acesso à sua instância Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance) com o diretor da Azure Cosmos DB.

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>PASSO 5: Ativar a CMK em Azure Sentinel

A capacidade Azure Sentinel CMK só é fornecida a novos clientes depois de ter recebido acesso diretamente do grupo de produtos Azure. Utilize os seus contactos na Microsoft para receber a aprovação da equipa do Azure Sentinel para permitir a CMK na sua solução.

Após a aprovação, ser-lhe-á pedido que forneça as seguintes informações para ativar a funcionalidade CMK.

-  ID do espaço de trabalho no qual pretende ativar a CMK

-  URL do cofre de chaves: Copie o "Identificador chave" da chave até ao último corte dianteiro:  
    

    ![identificador chave](./media/customer-managed-keys/key-identifier.png)

    A equipa do Azure Sentinel irá ativar a funcionalidade Azure Sentinel CMK para o seu espaço de trabalho fornecido.

-  Verificação da equipa de produtos Azure Sentinel que foi aprovada para utilizar esta funcionalidade. Deve ter isto antes de prosseguir.

### <a name="step-6-enable-azure-sentinel"></a>PASSO 6: Ativar sentinela Azure


Vá ao portal Azure e ative o Azure Sentinel no espaço de trabalho em que configura a CMK. Para mais informações, consulte [a Azure Sentinel Onboarding](quickstart-onboard.md).

## <a name="key-encryption-key-revocation-or-deletion"></a>Revogação ou eliminação da chave de encriptação


No caso de um utilizador revogar a chave de encriptação, quer eliminando-a quer removendo-a para o Azure Sentinel, dentro de uma hora, o Azure Sentinel honrará a mudança e comportar-se-á como se os dados já não estivessem disponíveis. Neste momento, qualquer operação realizada que utilize recursos de armazenamento persistentes, tais como ingestão de dados, alterações persistentes de configuração e criação de incidentes, será impedida. Os dados previamente armazenados não serão eliminados, mas permanecerão inacessíveis. Os dados inacessíveis são regidos pela política de retenção de dados e serão purgados de acordo com essa política.

A única operação possível após a revogação ou eliminação da chave de encriptação é a eliminação da conta.

Se o acesso for restaurado após a revogação, o Azure Sentinel restabelecerá o acesso aos dados dentro de uma hora.

Para saber mais sobre como isto funciona no Azure Monitor, consulte a revogação da [CMK do Monitor Azure.](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-revocation)

## <a name="key-encryption-key-rotation"></a>Rotação da chave de encriptação


Azure Sentinel e Log Analytics suportam a rotação da chave. Quando um utilizador realiza a rotação da chave no Key Vault, o Azure Sentinel suporta a nova chave dentro de uma hora.

No Key Vault, pode executar a rotação da chave criando uma nova versão da chave:

![rotação chave](./media/customer-managed-keys/key-rotation.png)

Pode desativar a versão anterior da chave após 24 horas, ou depois dos registos de auditoria do Azure Key Vault já não mostrarem qualquer atividade que utilize a versão anterior.

Se utilizar a mesma tecla no Azure Sentinel e no Log Analytics, é necessário realizar uma rotação de teclas, tem de atualizar explicitamente o recurso de cluster em Log Analytics com a nova versão chave Azure Key Vault. Para obter mais informações, consulte [a rotação CMK do Monitor Azure](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-rotation).

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a configurar uma chave gerida pelo cliente no Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats.md)
- [Utilize livros para](tutorial-monitor-your-data.md) monitorizar os seus dados.

