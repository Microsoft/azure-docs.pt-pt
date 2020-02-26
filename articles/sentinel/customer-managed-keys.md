---
title: Instale chaves geridas pelo cliente em Azure Sentinel. Microsoft Docs
description: Saiba como configurar as chaves geridas pelo cliente (CMK) no Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: yelevin
ms.openlocfilehash: bc2fa02925e7f5c671085eb87ca0431d3fca7691
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587962"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>Configurar a chave gerida pelo cliente Azure Sentinel


Este artigo fornece informações e passos de fundo para configurar uma chave gerida pelo cliente (CMK) para o Azure Sentinel. A CMK permite que todos os dados guardados ou enviados ao Azure Sentinel sejam encriptados em todos os recursos de armazenamento relevantes com uma chave Azure Key Vault criada ou detida por si.

> [!NOTE]
> -   A capacidade da CMK Azure Sentinel é fornecida apenas a clientes **novos** e o acesso a esta capacidade é controlado pelo registo de funcionalidades Azure. Pode solicitar acesso contactando azuresentinelCMK@microsoft.com, e como a capacidade está disponível, os pedidos pendentes serão aprovados.
> -   A capacidade da CMK Azure Sentinel só está disponível nas regiões leste dos EUA, West US 2 e Sul-Centro dos EUA.
> -   A capacidade cmk só está disponível para os clientes que enviam 1TB por dia ou mais. Receberá informações sobre preços adicionais quando se candidatar à Microsoft para fornecer CMK na sua subscrição Azure. Saiba mais sobre o carregamento [de Log Analytics.](../azure-monitor/platform/customer-managed-keys.md#disclaimers)

## <a name="how-cmk-works"></a>Como funciona a CMK 

A solução Azure Sentinel utiliza vários recursos de armazenamento para recolha de registos e funcionalidades, estes incluem Log Analytics e outros recursos de armazenamento. Como parte da configuração do CMK Azure Sentinel, terá de configurar as definições cmk nos recursos de armazenamento relacionados. Os dados guardados em recursos de armazenamento que não o Log Analytics também serão encriptados.

> [!NOTE]
> Se ativar a CMK no Azure Sentinel, qualquer funcionalidade de Pré-visualização Pública que não suporte a CMK não estará ativada.

## <a name="enable-cmk"></a>Ativar cmK 

Para fornecer CMK, siga estes passos: 

1.  Crie um Cofre de Chave Azure e armazene a chave.

2.  Ative a CMK no seu espaço de trabalho Log Analytics.

3.  Registe-se na Cosmos DB.

4.  Adicione uma política de acesso à sua instância azure Key Vault.

5.  Ativar a CMK em Azure Sentinel.

6.  Ativar o Sentinela Azure.

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>PASSO 1: Criar um cofre de chaves Azure e armazenar a chave

1.  [Crie o recurso Azure Key Vault,](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-manage-portal?view=azs-1910)em seguida, gere ou importe uma chave a ser usada para encriptação de dados.
    > [!NOTE]
    >  O Cofre de Chaves Azure deve ser configurado como recuperável para proteger a sua chave e o acesso.

1.  [Ativar as opções de recuperação:](../key-vault/key-vault-best-practices.md#turn-on-recovery-options)

    -   Certifique-se de que o [Soft Delete](../key-vault/key-vault-ovw-soft-delete.md) está ligado.

    -   Ligue a [proteção purga](../key-vault/key-vault-ovw-soft-delete.md#purge-protection) para proteger contra a eliminação forçada do segredo/cofre mesmo após a eliminação suave.

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>PASSO 2: Ativar a CMK no seu espaço de trabalho Log Analytics

Siga as instruções na [configuração da chave gerida pelo cliente Do Azure Monitor](../azure-monitor/platform/customer-managed-keys.md) para criar um espaço de trabalho CMK que será utilizado como espaço de trabalho Azure Sentinel nos seguintes passos.

### <a name="step-3-register-for-cosmos-db"></a>PASSO 3: Registe-se na Cosmos DB

O Azure Sentinel trabalha com a Cosmos DB como recurso adicional de armazenamento. Certifique-se de se registar na Cosmos DB.

Siga a instrução cosmos DB para registar o fornecedor de recursos [Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) para a sua subscrição Azure.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>PASSO 4: Adicione uma política de acesso à sua instância do Cofre chave Azure

Certifique-se de adicionar acesso da Cosmos DB à sua instância azure Key Vault. Siga a instrução cosmos DB para adicionar uma política de [acesso ao seu avançado Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance) com o diretor da Azure Cosmos DB.

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>PASSO 5: Habilitar a CMK em Azure Sentinel

A capacidade da CMK Azure Sentinel só é fornecida aos novos clientes depois de ter recebido acesso diretamente do grupo de produtos Azure. Utilize os seus contactos na Microsoft para receber a aprovação da equipa Azure Sentinel para permitir a CMK na sua solução.

Depois de obter aprovação, será-lhe pedido que forneça as seguintes informações para ativar a funcionalidade CMK.

-  ID do espaço de trabalho no qual pretende ativar a CMK

-  URL do cofre chave: Copie o "Identificador chave" da chave até ao último corte para a frente:  
    

    ![identificador chave](./media/customer-managed-keys/key-identifier.png)

    A equipa Azure Sentinel irá ativar a funcionalidade CMK Azure Sentinel para o seu espaço de trabalho fornecido.

-  Verificação da equipa de produtos Azure Sentinel de que foi aprovado para utilizar esta funcionalidade. Deve ter isto antes de prosseguir.

### <a name="step-6-enable-azure-sentinel"></a>PASSO 6: Habilitar o Sentinela Azure


Vá ao portal Azure e ative o Azure Sentinel no espaço de trabalho em que configura a CMK. Para mais informações, consulte [O Onboarding Azure Sentinel](quickstart-onboard.md).

## <a name="key-encryption-key-revocation-or-deletion"></a>Chave de encriptação Revogação ou eliminação da chave de encriptação


No caso de um utilizador revogar a chave de encriptação, quer eliminando-a ou removendo o acesso ao Azure Sentinel, dentro de uma hora, o Azure Sentinel honrará a mudança e comportar-se-á como se os dados já não estivessem disponíveis. Neste ponto, qualquer operação realizada que utilize recursos de armazenamento persistentes, tais como ingestão de dados, alterações persistentes de configuração e criação de incidentes, será evitada. Os dados previamente armazenados não serão eliminados, mas permanecerão inacessíveis. Os dados inacessíveis regem-se pela política de retenção de dados e serão expurgados de acordo com essa política.

A única operação possível após a revogação ou eliminação da chave de encriptação é a eliminação da conta.

Se o acesso for restaurado após a revogação, o Azure Sentinel restabelecerá o acesso aos dados dentro de uma hora.

Para saber mais sobre como isto funciona no Monitor Azure, consulte a revogação da [CMK do Monitor Azure.](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-revocation)

## <a name="key-encryption-key-rotation"></a>Rotação da chave de encriptação


Rotação da chave de suporte Azure Sentinel e Log Analytics. Quando um utilizador executa a rotação da chave no Key Vault, o Azure Sentinel suporta a nova tecla dentro de uma hora.

No Key Vault, pode realizar a rotação da chave criando uma nova versão da chave:

![rotação chave](./media/customer-managed-keys/key-rotation.png)

Pode desativar a versão anterior da chave após 24 horas, ou depois dos registos de auditoria do Cofre chave Do Azure já não mostrarem qualquer atividade que utilize a versão anterior.

Se utilizar a mesma tecla no Azure Sentinel e no Log Analytics, é necessário realizar a rotação da chave, tem de atualizar explicitamente o recurso de cluster no Log Analytics com a nova versão chave do Cofre de Chaves Azure. Para mais informações, consulte a [rotação CMK do Monitor Azure](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-rotation).

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a configurar uma chave gerida pelo cliente no Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats.md)
- [Utilize livros](tutorial-monitor-your-data.md) de trabalho para monitorizar os seus dados.

