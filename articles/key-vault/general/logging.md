---
title: Azure Key Vault registação / Microsoft Docs
description: Saiba como monitorizar o acesso aos seus cofres chave, permitindo o registo do Cofre da Chave Azure, que guarda informações numa conta de armazenamento Azure que fornece.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 162e40555e11dff716b58eec4b1168728257693e
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131178"
---
# <a name="azure-key-vault-logging"></a>Registo do Azure Key Vault

Depois de criar um ou mais cofres chave, provavelmente vai querer monitorizar como e quando os seus cofres chave são acedidos, e por quem. Pode fazê-lo ativando o registo do Azure Key Vault, que guarda informações numa conta de armazenamento Azure que fornece. Para obter orientação passo a passo sobre a configuração deste problema, consulte [como ativar a marcação do Cofre de Chaves](howto-logging.md).

Pode aceder à sua informação de registo 10 minutos (no máximo) após a operação do cofre da chave. Na maioria dos casos, o processo será ainda mais rápido.  Cabe-lhe gerir os seus registos na sua conta de armazenamento:

* Utilize métodos padrão de controlo de acesso do Azure para proteger os seus registos, restringindo o seu acesso.
* Elimine os registos que já não pretende manter na sua conta de armazenamento.

Para obter informações gerais sobre o Key Vault, veja [o que é o Cofre da Chave Azure?](overview.md) Para obter informações sobre onde o Cofre-Chave está disponível, consulte [a página de preços](https://azure.microsoft.com/pricing/details/key-vault/). Para obter informações sobre a utilização [do Monitor Azure para o Cofre de Chaves](https://docs.microsoft.com/azure/azure-monitor/insights/key-vault-insights-overview).

## <a name="interpret-your-key-vault-logs"></a>Interpretar os registos do seu Cofre de Chaves

Quando ativa o registo, um novo contentor chamado **insights-logs-auditevent** é automaticamente criado para a sua conta de armazenamento especificada. Pode utilizar esta mesma conta de armazenamento para recolher registos para vários cofres chave.

Os blobs individuais são armazenadas como texto, formatados como um blob JSON. Vamos ver um registo de exemplo. 

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

A tabela que se segue lista os nomes e descrições do campo:

| Nome do campo | Descrição |
| --- | --- |
| **hora** |Data e hora na UTC. |
| **recursosId** |Identificação de recursos do Azure Resource Manager. Para os registos do Cofre de Chaves, este é sempre o ID de recurso key Vault. |
| **operaçãoName** |Nome da operação, conforme documentada na tabela seguinte. |
| **operaçãoVer** |Versão REST API solicitada pelo cliente. |
| **categoria** |Tipo de resultado. Para os registos key vault, `AuditEvent` é o valor único disponível. |
| **resultadoType** |Resultado do pedido da API REST. |
| **assinatura resultados** |Estado de HTTP. |
| **resultadoDescrição** |Descrição adicional sobre o resultado, quando disponível. |
| **duraçõesMs** |Tempo necessário para o processamento do pedido de API REST, em milissegundos. Não inclui a latência de rede, assim, o tempo que medir do lado do cliente poderá não corresponder a este período de tempo. |
| **callerIpAddress** |Endereço IP do cliente que fez o pedido. |
| **correlationId** |Um GUID opcional que o cliente pode passar para correlacionar os registos do lado do cliente com os registos do lado do serviço (Cofre de Chaves). |
| **identidade** |Identidade do símbolo que foi apresentado no pedido da API REST. Trata-se normalmente de um "utilizador", de um "principal de serviço", ou da combinação "user+appId", como no caso de um pedido que resulta de um cmdlet Azure PowerShell. |
| **propriedades** |Informação que varia com base na operação **(operaçãoName).** Na maioria dos casos, este campo contém informações do cliente (a cadeia de agente de utilizador passou pelo cliente), o pedido exato de API URI e o código de estado HTTP. Além disso, quando um objeto é devolvido como resultado de um pedido (por exemplo, **KeyCreate** ou **VaultGet),** também contém a chave URI (as `id` ), cofre URI, ou URI secreto. |

Os valores de campo **operationName** estão no formato *ObjectVerb.* Por exemplo:

* Todas as operações de cofre chave têm o `Vault<action>` formato, tais como `VaultGet` e `VaultCreate` .
* Todas as operações-chave têm o `Key<action>` formato, tais como `KeySign` e `KeyList` .
* Todas as operações secretas têm o `Secret<action>` formato, tais como `SecretGet` e `SecretListVersions` .

A tabela a seguir lista os valores do nome de **operação** e os comandos correspondentes da API REST:

### <a name="operation-names-table"></a>Tabela de nomes de operação

| operationName | Comando API REST |
| --- | --- |
| **Autenticação** |Autenticar via Azure Ative Directory endpoint |
| **VaultGet** |[Obter informações sobre um cofre de chaves](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Criar ou atualizar um cofre de chaves](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Eliminar um cofre de chaves](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Atualizar um cofre de chaves](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Lista todos os cofres de chaves num grupo de recursos](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Criar uma chave](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Obter informações sobre uma chave](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[Importar uma chave para um cofre](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[Apoiar uma chave](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[Eliminar uma chave](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[Restaurar uma chave](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[Assinar com uma chave](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[Verificar com uma chave](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[Moldar uma chave](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[Desenrolar uma chave](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[Encriptar com uma chave](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[Desencriptar com uma chave](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[Atualizar uma chave](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[Lista as chaves num cofre](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[Lista as versões de uma chave](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[Criar um segredo](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[Obter um segredo](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Atualizar um segredo](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Eliminar um segredo](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Lista os segredos num cofre](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Lista as versões de um segredo](https://msdn.microsoft.com/library/azure/dn986824.aspx) |
| **VaultAccessPolicyChangedEventGridNotification** | A política de acesso ao cofre mudou o evento publicado |
| **SecretNearExpiryEventGridNotification** |Evento secreto perto da expiração publicado |
| **SecretExpiredEventGridNotification** |Evento secreto expirado publicado |
| **KeyNearExpiryEventGridNotification** |Chave perto do evento de expiração publicado |
| **KeyExpiredEventGridNotification** |Evento expirado chave publicado |
| **CertificadoNearExpiryEventGridNotification** |Certificado perto do evento de validade publicado |
| **CertificadoExpiredEventGridNotification** |Certificado vencida evento publicado |

## <a name="use-azure-monitor-logs"></a>Utilizar os registos do Azure Monitor

Pode utilizar a solução Key Vault nos registos do Azure Monitor para rever os registos do Key `AuditEvent` Vault. Nos registos do Azure Monitor, utiliza consultas de registo para analisar dados e obter a informação de que necessita. 

Para obter mais informações, incluindo como configurar isto, consulte [o Azure Key Vault no Azure Monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Passos seguintes

- [Como permitir a exploração do Cofre de Chaves](howto-logging.md)
- Para um tutorial que utilize o Azure Key Vault numa aplicação web .NET, consulte [Use Azure Key Vault a partir de uma aplicação web](tutorial-net-create-vault-azure-web-app.md).
- Para as referências de programação, consulte o [Guia para programadores do Cofre de Chaves do Azure](developers-guide.md).
- Para obter uma lista de Azure PowerShell 1.0 cmdlets para Azure Key Vault, consulte [as cmdlets do Cofre da Chave Azure](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).
