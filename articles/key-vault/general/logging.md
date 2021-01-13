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
ms.date: 12/18/2020
ms.author: mbaldwin
ms.openlocfilehash: 5fcb3226eebf39ab18fb3bb24f0521a0523748d4
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134182"
---
# <a name="azure-key-vault-logging"></a>Registo do Azure Key Vault

Depois de criar um ou mais cofres chave, provavelmente vai querer monitorizar como e quando os seus cofres chave são acedidos, e por quem. Pode fazê-lo ativando o registo do Azure Key Vault, que guarda informações numa conta de armazenamento Azure que fornece. Para obter orientação passo a passo sobre a configuração deste problema, consulte [como ativar a marcação do Cofre de Chaves](howto-logging.md).

Pode aceder à sua informação de registo 10 minutos (no máximo) após a operação do cofre da chave. Na maioria dos casos, o processo será ainda mais rápido.  Cabe-lhe gerir os registos na sua conta de armazenamento:

* Utilize métodos padrão de controlo de acesso Azure na sua conta de armazenamento para proteger os seus registos, restringindo quem pode aceder aos mesmos.
* Elimine registos que já não pretende manter na conta de armazenamento.

Para obter informações gerais sobre o Key Vault, veja [o que é o Cofre da Chave Azure?](overview.md) Para obter informações sobre onde o Cofre-Chave está disponível, consulte [a página de preços](https://azure.microsoft.com/pricing/details/key-vault/). Para obter informações sobre a utilização [do Monitor Azure para o Cofre de Chaves](../../azure-monitor/insights/key-vault-insights-overview.md).

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
| **tempo** |Data e hora na UTC. |
| **recursosId** |Identificação de recursos do Azure Resource Manager. Para os registos do Cofre de Chaves, este é sempre o ID de recurso key Vault. |
| **operationName** |Nome da operação, conforme documentada na tabela seguinte. |
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

# <a name="vault"></a>[Cofre](#tab/Vault)

| operationName | Comando API REST |
| --- | --- |
| **Autenticação** |Autenticar via Azure Ative Directory endpoint |
| **VaultGet** |[Obter informações sobre um cofre de chaves](/rest/api/keyvault/vaults) |
| **VaultPut** |[Criar ou atualizar um cofre de chaves](/rest/api/keyvault/vaults) |
| **VaultDelete** |[Eliminar um cofre de chaves](/rest/api/keyvault/vaults) |
| **VaultPatch** |[Atualizar um cofre de chaves](/rest/api/keyvault/vaults) |
| **VaultList** |[Lista todos os cofres de chaves num grupo de recursos](/rest/api/keyvault/vaults) |
| **Salto em abóbada** |[Purga apagada](/rest/api/keyvault/vaults/purgedeleted) |
| **Recuperação de cofres** |Recuperar cofre apagado|
| **VaultGetDeleted** |[Receba o cofre apagado](/rest/api/keyvault/vaults/getdeleted) |
| **VaultListDeleted** |[Lista de cofres eliminados](/rest/api/keyvault/vaults/listdeleted) |
| **VaultAccessPolicyChangedEventGridNotification** | A política de acesso ao cofre mudou o evento publicado |

# <a name="keys"></a>[Chaves](#tab/Keys)

| operationName | Comando API REST |
| --- | --- |
| **KeyCreate** |[Criar uma chave](/rest/api/keyvault/createkey) |
| **KeyGet** |[Obter informações sobre uma chave](/rest/api/keyvault/getkey) |
| **KeyImport** |[Importar uma chave para um cofre](/rest/api/keyvault/vaults) |
| **KeyDelete** |[Eliminar uma chave](/rest/api/keyvault/deletekey) |
| **KeySign** |[Assinar com uma chave](/rest/api/keyvault/sign) |
| **KeyVerify** |[Verificar com uma chave](/rest/api/keyvault/vaults) |
| **KeyWrap** |[Moldar uma chave](/rest/api/keyvault/wrapkey) |
| **KeyUnwrap** |[Desenrolar uma chave](/rest/api/keyvault/unwrapkey) |
| **KeyEncrypt** |[Encriptar com uma chave](/rest/api/keyvault/encrypt) |
| **KeyDecrypt** |[Desencriptar com uma chave](/rest/api/keyvault/decrypt) |
| **KeyUpdate** |[Atualizar uma chave](/rest/api/keyvault/updatekey) |
| **KeyList** |[Lista as chaves num cofre](/rest/api/keyvault/getkeys) |
| **KeyListVersions** |[Lista as versões de uma chave](/rest/api/keyvault/getkeyversions) |
| **KeyPurge** |[Purgue uma chave](/rest/api/keyvault/purgedeletedkey) |
| **KeyBackup** |[Backup uma chave](/rest/api/keyvault/backupkey) |
| **KeyRestore** |[Restaurar uma chave](/rest/api/keyvault/restorekey) |
| **Chaverecover** |[Recuperar uma chave](/rest/api/keyvault/recoverdeletedkey) |
| **KeyGetDeleted** |[Obtenha a chave eliminada](/rest/api/keyvault/getdeletedkey) |
| **KeyListDeleted** |[Listar as chaves apagadas num cofre](/rest/api/keyvault/getdeletedkeys) |
| **KeyNearExpiryEventGridNotification** |Chave perto do evento de expiração publicado |
| **KeyExpiredEventGridNotification** |Evento expirado chave publicado |

# <a name="secrets"></a>[Segredos](#tab/Secrets)

| operationName | Comando API REST |
| --- | --- |
| **SecretSet** |[Criar um segredo](/rest/api/keyvault/updatecertificate) |
| **SecretGet** |[Obter um segredo](/rest/api/keyvault/getsecret) |
| **SecretUpdate** |[Atualizar um segredo](/rest/api/keyvault/updatesecret) |
| **SecretDelete** |[Eliminar um segredo](/rest/api/keyvault/deletesecret) |
| **SecretList** |[Lista os segredos num cofre](/rest/api/keyvault/getsecrets) |
| **SecretListVersions** |[Lista as versões de um segredo](/rest/api/keyvault/getsecretversions) |
| **SecretPurge** |[Purgue um segredo](/rest/api/keyvault/purgedeletedsecret) |
| **SecretBackup** |[Backup um segredo](/rest/api/keyvault/backupsecret) |
| **SecretRestore** |[Restaurar um segredo](/rest/api/keyvault/restoresecret) |
| **SecretRecover** |[Recuperar um segredo](/rest/api/keyvault/recoverdeletedsecret) |
| **SecretGetDeleted** |[Obter segredo apagado](/rest/api/keyvault/getdeletedsecret) |
| **SecretListDeleted** |[Listar os segredos apagados num cofre](/rest/api/keyvault/getdeletedsecrets) |
| **SecretNearExpiryEventGridNotification** |Evento secreto perto da expiração publicado |
| **SecretExpiredEventGridNotification** |Evento secreto expirado publicado |

# <a name="certificates"></a>[Certificados](#tab/Cerificates)

| operationName | Comando API REST |
| --- | --- |
| **CertificadoR** |[Obtenha informações sobre um certificado](/rest/api/keyvault/getcertificate) |
| **CertificadoCreato** |[Criar um certificado](/rest/api/keyvault/createcertificate) |
| **CertificadoImport** |[Importe um certificado em um cofre](/rest/api/keyvault/importcertificate) |
| **CertificadoUpdate** |[Atualizar um certificado](/rest/api/keyvault/updatecertificate) |
| **Lista de Certificados** |[Listar os certificados num cofre](/rest/api/keyvault/getcertificates) |
| **CertificadosListversões** |[Listar as versões de um certificado](/rest/api/keyvault/getcertificateversions) |
| **CertificadoDelete** |[Apagar um certificado](/rest/api/keyvault/deletecertificate) |
| **CertificadoPurge** |[Purgue um certificado](/rest/api/keyvault/purgedeletedcertificate) |
| **CertificateBackup** |[Backup de um certificado](/rest/api/keyvault/backupcertificate) |
| **Loja de Certificados** |[Restaurar um certificado](/rest/api/keyvault/restorecertificate) |
| **CertificadoRecover** |[Recuperar um certificado](/rest/api/keyvault/recoverdeletedcertificate) |
| **CertificadoGetDeleted** |[Obter certificado eliminado](/rest/api/keyvault/getdeletedcertificate) |
| **CertificadoSDeletado** |[Listar os certificados eliminados num cofre](/rest/api/keyvault/getdeletedcertificates) |
| **CertificadoPolicyGet** |[Obtenha a política de certificados](/rest/api/keyvault/getcertificatepolicy) |
| **CertificadoPolicyUpdate** |[Atualizar a política de certificados](/rest/api/keyvault/updatecertificatepolicy) |
| **CertificadoPolicySet** |[Criar política de certificados](/rest/api/keyvault/createcertificate) |
| **CertificadoContactsGet** |[Obter contactos de certificado](/rest/api/keyvault/getcertificatecontacts) |
| **CertificadoContactsSet** |[Definir contactos de certificado](/rest/api/keyvault/setcertificatecontacts) |
| **CertificadoContactsDelete** |[Eliminar contactos de certificados](/rest/api/keyvault/deletecertificatecontacts) |
| **CertificadoIssuerGet** |[Obtenha o emitente de certificado](/rest/api/keyvault/getcertificateissuer) |
| **CertificadoIssuerSet** |[Emitente de certificado definido](/rest/api/keyvault/setcertificateissuer) |
| **CertificadoIssuerUpdate** |[Emitente de certificado de atualização](/rest/api/keyvault/updatecertificateissuer) |
| **CertificadoIssuerDelete** |[Eliminar emissor de certificados](/rest/api/keyvault/deletecertificateissuer) |
| **CertifiIssuersList** |[Listar os emitentes de certificados](/rest/api/keyvault/getcertificateissuers) |
| **CertificadoEnroll** |Inscreva um certificado |
| **CertificadoRenovo** |Renovar um certificado |
| **100 000's** |Recuperar certificado pendente |
| **Licenciatura em Penitência** |Enquanto se aguarda uma fusão de certificado |
| **Certificado GastoUpdate** |Enquanto se aguarda uma atualização de certificado |
| **Certificado PendenteDelete** |Apagar certificado pendente |
| **CertificadoNearExpiryEventGridNotification** |Certificado perto do evento de validade publicado |
| **CertificadoExpiredEventGridNotification** |Certificado vencida evento publicado |

---

## <a name="use-azure-monitor-logs"></a>Utilizar os registos do Azure Monitor

Pode utilizar a solução Key Vault nos registos do Azure Monitor para rever os registos do Key `AuditEvent` Vault. Nos registos do Azure Monitor, utiliza consultas de registo para analisar dados e obter a informação de que necessita. 

Para obter mais informações, incluindo como configurar isto, consulte [o Azure Key Vault no Azure Monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Passos seguintes

- [Como permitir a exploração do Cofre de Chaves](howto-logging.md)
- [Monitor azul](https://docs.microsoft.com/azure/azure-monitor/)
- Para um tutorial que utilize o Azure Key Vault numa aplicação web .NET, consulte [Use Azure Key Vault a partir de uma aplicação web](tutorial-net-create-vault-azure-web-app.md).
- Para as referências de programação, consulte o [Guia para programadores do Cofre de Chaves do Azure](developers-guide.md).
- Para obter uma lista de Azure PowerShell 1.0 cmdlets para Azure Key Vault, consulte [as cmdlets do Cofre da Chave Azure](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).
