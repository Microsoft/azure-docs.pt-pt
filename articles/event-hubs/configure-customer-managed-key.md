---
title: Configure sua própria chave para criptografar dados de hubs de eventos do Azure em repouso
description: Este artigo fornece informações sobre como configurar sua própria chave para criptografar dados REST dos hubs de eventos do Azure.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: spelluru
ms.openlocfilehash: 311f69ffa436eebb261fb8aa5ee72886ad9fe9d0
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035899"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Configurar chaves gerenciadas pelo cliente para criptografar dados de hubs de eventos do Azure em repouso usando o portal do Azure
Os hubs de eventos do Azure fornecem criptografia de dados em repouso com o Criptografia do Serviço de Armazenamento do Azure (Azure SSE). Os hubs de eventos dependem do armazenamento do Azure para armazenar os dados e, por padrão, todos os dados armazenados com o armazenamento do Azure são criptografados usando chaves gerenciadas pela Microsoft. 

## <a name="overview"></a>Descrição geral
Os hubs de eventos do Azure agora dão suporte à opção de criptografar dados em repouso com chaves gerenciadas pela Microsoft ou chaves gerenciadas pelo cliente (Bring Your Own Key – BYOK). Esse recurso permite que você crie, gire, desabilite e revogue o acesso às chaves gerenciadas pelo cliente que são usadas para criptografar dados de hubs de eventos do Azure em repouso.

Habilitar o recurso BYOK é um processo de instalação única em seu namespace.

> [!NOTE]
> O recurso BYOK tem suporte dos clusters de [locatário único dedicados aos hubs de eventos](event-hubs-dedicated-overview.md) . Ele não pode ser habilitado para namespaces de hubs de eventos padrão.

Você pode usar Azure Key Vault para gerenciar suas chaves e auditar o uso da chave. Você pode criar suas próprias chaves e armazená-las em um cofre de chaves ou pode usar as APIs de Azure Key Vault para gerar chaves. Para obter mais informações sobre Azure Key Vault, consulte [o que é Azure Key Vault?](../key-vault/key-vault-overview.md)

Este artigo mostra como configurar um cofre de chaves com chaves gerenciadas pelo cliente usando o portal do Azure. Para saber como criar um cofre de chaves usando o portal do Azure, consulte [] início rápido: Defina e recupere um segredo de Azure Key Vault usando o portal do Azure] (.. /key-vault/quick-create-portal.md).

> [!IMPORTANT]
> O uso de chaves gerenciadas pelo cliente com os hubs de eventos do Azure requer que o cofre de chaves tenha duas propriedades necessárias configuradas. São:  **Exclusão reversível** e **não limpar**. Essas propriedades são habilitadas por padrão quando você cria um novo cofre de chaves no portal do Azure. No entanto, se você precisar habilitar essas propriedades em um cofre de chaves existente, deverá usar o PowerShell ou CLI do Azure.

## <a name="enable-customer-managed-keys"></a>Habilitar chaves gerenciadas pelo cliente
Para habilitar as chaves gerenciadas pelo cliente no portal do Azure, siga estas etapas:

1. Navegue até o cluster Hubs de Eventos Dedicados.
1. Selecione o namespace no qual você deseja habilitar BYOK.
1. Na página **configurações** do seu namespace de hubs de eventos, selecione **criptografia (versão prévia)** . 
1. Selecione a **criptografia de chave gerenciada pelo cliente em repouso** , conforme mostrado na imagem a seguir. 

    ![Habilitar chave gerenciada pelo cliente](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Configurar um cofre de chaves com chaves
Depois de habilitar as chaves gerenciadas pelo cliente, você precisa associar a chave gerenciada pelo cliente ao namespace de hubs de eventos do Azure. Os hubs de eventos oferecem suporte apenas a Azure Key Vault. Se você habilitar a opção **criptografia com chave gerenciada pelo cliente** na seção anterior, precisará ter a chave importada para Azure Key Vault. Além disso, as chaves devem ter **exclusão reversível** e **não limpar** configuradas para a chave. Essas configurações podem ser configuradas usando o [PowerShell](../key-vault/key-vault-soft-delete-powershell.md) ou a [CLI](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection).

1. Para criar um novo cofre de chaves, siga o guia de [início rápido](../key-vault/key-vault-overview.md)do Azure Key Vault. Para obter mais informações sobre como importar chaves existentes, consulte [sobre chaves, segredos e certificados](../key-vault/about-keys-secrets-and-certificates.md).
1. Para ativar a exclusão reversível e limpar a proteção ao criar um cofre, use o comando [AZ keyvault Create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) .

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Para adicionar a proteção de limpeza a um cofre existente (que já tem a exclusão reversível habilitada), use o comando [AZ keyvault Update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) .

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Crie chaves seguindo estas etapas:
    1. Para criar uma nova chave, selecione **gerar/importar** no menu **chaves** em **configurações**.
        
        ![Selecionar botão gerar/importar](./media/configure-customer-managed-key/select-generate-import.png)
    1. Defina **as opções** para **gerar** e dar um nome à chave.

        ![Criar uma chave](./media/configure-customer-managed-key/create-key.png) 
    1. Agora você pode selecionar essa chave para associar ao namespace de hubs de eventos para criptografia na lista suspensa. 

        ![Selecionar chave do Key Vault](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Preencha os detalhes da chave e clique em **selecionar**. Isso habilitará a criptografia de dados em repouso no namespace com uma chave gerenciada pelo cliente. 

        > [!NOTE]
        > Para visualização, você só pode selecionar uma única chave. 

## <a name="rotate-your-encryption-keys"></a>Girar suas chaves de criptografia
Você pode girar sua chave no cofre de chaves usando o mecanismo de rotação do Azure Key Vaults. Para obter mais informações, consulte [Configurar a rotação de chaves e a auditoria](../key-vault/key-vault-key-rotation-log-monitoring.md). As datas de ativação e expiração também podem ser definidas para automatizar a rotação de chaves. O serviço de hubs de eventos detectará novas versões de chave e começará a usá-las automaticamente.

## <a name="revoke-access-to-keys"></a>Revogar o acesso às chaves
Revogar o acesso às chaves de criptografia não limpará os dados dos hubs de eventos. No entanto, os dados não podem ser acessados no namespace de hubs de eventos. Você pode revogar a chave de criptografia por meio da política de acesso ou excluindo a chave. Saiba mais sobre as políticas de acesso e proteger o cofre de chaves de [acesso seguro a um cofre de chaves](../key-vault/key-vault-secure-your-key-vault.md).

Depois que a chave de criptografia for revogada, o serviço de hubs de eventos no namespace criptografado se tornará inoperável. Se o acesso à chave estiver habilitado ou a chave de exclusão for restaurada, o serviço de hubs de eventos escolherá a chave para que você possa acessar os dados do namespace de hubs de eventos criptografados.

> [!NOTE]
> Se você excluir uma chave de criptografia existente do cofre de chaves e substituí-la por uma nova chave no namespace de hubs de eventos, já que a chave de exclusão ainda é válida (já que ela é armazenada em cache) por até uma hora, seus dados antigos (que foram criptografados com a chave antiga) ainda podem estar acessíveis ao longo  com os novos dados, que agora são acessíveis apenas usando a nova chave. Esse comportamento é por design na versão de visualização do recurso. 

## <a name="set-up-diagnostic-logs"></a>Configurar os registos de diagnóstico 
A configuração de logs de diagnóstico para namespaces habilitados para BYOK fornece as informações necessárias sobre as operações quando um namespace é criptografado com chaves gerenciadas pelo cliente. Esses logs podem ser habilitados e transmitidos posteriormente para um hub de eventos ou analisados por meio do log Analytics ou transmitidos para o armazenamento para executar análises personalizadas. Para saber mais sobre os logs de diagnóstico, consulte [visão geral dos logs de diagnóstico do Azure](../azure-monitor/platform/diagnostic-logs-overview.md).

## <a name="enable-user-logs"></a>Habilitar logs do usuário
Siga estas etapas para habilitar logs para chaves gerenciadas pelo cliente.

1. Na portal do Azure, navegue até o namespace que tem BYOK habilitado.
1. Selecione **configurações de diagnóstico** em **monitoramento**.

    ![Selecionar configurações de diagnóstico](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Selecione **+ Adicionar configuração de diagnóstico**. 

    ![Selecione Adicionar configuração de diagnóstico](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Forneça um **nome** e selecione para onde deseja transmitir os logs.
1. Selecione **CustomerManagedKeyUserLogs** e **salvar**. Essa ação habilita os logs para BYOK no namespace.

    ![Selecione a opção de logs de usuário de chave gerenciada pelo cliente](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Esquema de registo 
Todos os registos são armazenados no formato de JavaScript Object Notation (JSON). Cada entrada tem campos de cadeia de caracteres que usam o formato descrito na tabela a seguir. 

| Name | Descrição |
| ---- | ----------- | 
| TaskName | Descrição da tarefa que falhou. |
| ActivityId | ID interna que é usada para acompanhamento. |
| category | Define a classificação da tarefa. Por exemplo, se a chave do cofre de chaves estiver sendo desabilitada, ela seria uma categoria de informações ou, se uma chave não puder ser desativada, ela poderá ficar com erro. |
| resourceId | ID do recurso de Azure Resource Manager |
| keyVault | Nome completo do cofre de chaves. |
| key | O nome da chave que é usado para criptografar o namespace de hubs de eventos. |
| version | A versão da chave que está sendo usada. |
| operation | A operação executada na chave em seu cofre de chaves. Por exemplo, desabilitar/habilitar a chave, encapsular ou desencapsular |
| code | O código associado à operação. Exemplo: Código de erro 404 significa que a chave não foi encontrada. |
| message | Qualquer mensagem de erro associada à operação |

Aqui está um exemplo do log para uma chave gerenciada pelo cliente:

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="troubleshoot"></a>Resolução de problemas
Como prática recomendada, sempre habilite logs como mostrado na seção anterior. Ele ajuda a controlar as atividades quando a criptografia BYOK está habilitada. Ele também ajuda a definir o escopo dos problemas.

Veja a seguir os códigos de erros comuns a serem procurados quando a criptografia BYOK estiver habilitada.

| Action | Código de erro | Estado resultante dos dados |
| ------ | ---------- | ----------------------- | 
| Remover a permissão de encapsulamento/desencapsulamento de um cofre de chaves | 403 |    Inacessível |
| Remover a associação de função do AAD de uma entidade de segurança do AAD que concedeu a permissão de encapsulamento/desencapsulamento | 403 |  Inacessível |
| Excluir uma chave de criptografia do cofre de chaves | 404 | Inacessível |
| Excluir o cofre de chaves | 404 | Inacessível (pressupõe que a exclusão reversível esteja habilitada, que é uma configuração necessária.) |
| Alterando o período de validade na chave de criptografia, de modo que já tenha expirado | 403 |   Inacessível  |
| Alterar o NBF (não antes) de tal chave de criptografia de chave não está ativa | 403 | Inacessível  |
| Selecionando a opção **permitir serviços MSFT** para o Firewall do cofre de chaves ou bloqueando o acesso à rede para o cofre de chaves que tem a chave de criptografia | 403 | Inacessível |
| Movendo o cofre de chaves para um locatário diferente | 404 | Inacessível |  
| Problema de rede intermitente ou interrupção de DNS/AAD/MSI |  | Acessível usando a chave de criptografia de dados em cache |

> [!IMPORTANT]
> Para habilitar o geo-DR em um namespace que está usando a criptografia BYOK, o namespace secundário para emparelhamento deve estar em um cluster dedicado e deve ter uma identidade gerenciada atribuída ao sistema habilitada nele. Para saber mais, confira [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).

> [!NOTE]
> Se os pontos de extremidade de serviço de rede virtual (VNet) estiverem configurados em Azure Key Vault para seu namespace de hubs de eventos, não haverá suporte para BYOK. 


## <a name="next-steps"></a>Passos Seguintes
Consulte os seguintes artigos:
- [Descrição geral dos Hubs de Eventos](event-hubs-about.md)
- [Visão geral de Key Vault](../key-vault/key-vault-overview.md)




