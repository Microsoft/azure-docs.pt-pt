---
title: Registo de HSM gerido Azure
description: Utilize este tutorial para ajudá-lo a começar com o registo gerido do HSM.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: 7420ffbe5b365c635c1eac2620cfd54ceb649ebf
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211809"
---
# <a name="managed-hsm-logging"></a>Registo de HSM gerido 

Depois de criar um ou mais HSMs geridos, provavelmente irá querer monitorizar como e quando os seus HSMs são acedidos, e por quem. Pode fazê-lo ativando o registo, que guarda informações numa conta de armazenamento Azure que fornece. Um novo recipiente denominado **insights-logs-auditevent** é automaticamente criado para a sua conta de armazenamento especificada. Pode utilizar esta mesma conta de armazenamento para recolher registos para vários HSMs geridos.

Pode aceder à sua informação de registo 10 minutos (no máximo) após a operação gerida do HSM. Na maioria dos casos, o processo será ainda mais rápido.  Cabe-lhe gerir os registos na sua conta de armazenamento:

* Utilize métodos de controlo de acesso do Azure standard para proteger os registos ao restringir quem pode aceder a eles.
* Elimine registos que já não pretende manter na conta de armazenamento.

Utilize este tutorial para ajudá-lo a começar com o registo gerido do HSM. Irá criar uma conta de armazenamento, ativar o registo e interpretar as informações de registo recolhidas.  

> [!NOTE]
> Este tutorial não inclui instruções para como criar HSMs geridos ou teclas. Este artigo fornece instruções do Azure CLI para a atualização da gravação de diagnóstico.

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos deste artigo, tem de ter os seguintes itens:

* Uma subscrição ao Microsoft Azure. Se não tiver uma, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial).
* A versão Azure CLI 2.12.0 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).
* Um HSM gerido na sua subscrição. Consulte [Quickstart: Provisão e ativação de um HSM gerido utilizando o Azure CLI](quick-create-cli.md) para provisões e ativar um HSM gerido.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Ligar à sua subscrição do Azure

O primeiro passo para a configuração do registo de chaves é apontar o Azure CLI para o HSM gerido que pretende registar.

```azurecli-interactive
az login
```

Para obter mais informações sobre as opções de login através do CLI, dê uma olhada [no login com o Azure CLI](/cli/azure/authenticate-azure-cli)

Poderá ter de especificar a subscrição que utilizou para criar o seu HSM Gerido. Introduza o seguinte comando para ver as subscrições da sua conta:

## <a name="identify-the-managed-hsm-and-storage-account"></a>Identificar o HSM gerido e a conta de armazenamento

```azurecli-interactive
hsmresource=$(az keyvault show --hsm-name ContosoMHSM --query id -o tsv)
storageresource=$(az storage account show --name ContosoMHSMLogs --query id -o tsv)
```

## <a name="enable-logging"></a>Ativar registo

Para ativar o registo de HSM gerido, utilize as definições de diagnóstico do **monitor az criem** comando, juntamente com as variáveis que criamos para a nova conta de armazenamento e o HSM gerido. Também colocaremos a bandeira **ativada** para **$true** e definiremos a categoria para **AuditEvent** (a única categoria para registos geridos do HSM):

Esta saída confirma que a sessão de registo está agora ativada para o seu HSM Gerido, e irá guardar informações na sua conta de armazenamento.

Opcionalmente, pode definir uma política de retenção para os seus registos de modo a que os registos mais antigos sejam automaticamente eliminados. Por exemplo, defina a política de retenção, definindo a bandeira **-RetentionEnabled** **para $true**, e defina o parâmetro **-RetentionInDays** para **90** de modo a que os registos com mais de 90 dias sejam automaticamente eliminados.

```azurecli-interactive
az monitor diagnostic-settings create --name ContosoMHSM-Diagnostics --resource $hsmresource --logs '[{"category": "AuditEvent","enabled": true}]' --storage-account $storageresource
```

O que é registado:

* Todos os pedidos autenticados da API, incluindo pedidos falhados como resultado de permissões de acesso, erros do sistema ou maus pedidos.
* Operações no próprio HSM gerido, incluindo criação, eliminação e atualização de atributos como tags.
* Operações relacionadas com o Domínio de Segurança, tais como inicializar & descarregar, inicializar a recuperação, carregar
* Operações completas de backup hSM, restauro e restauro seletivo
* Operações em chaves, incluindo:
  * Criar, modificar ou apagar as chaves.
  * Assinar, verificar, encriptar, desencriptar, embrulhar e desembrulhar chaves, listar chaves.
  * Backup chave, restaurar, purgar
* Pedidos não autenticados que resultam numa resposta 401. Exemplos são pedidos que não têm um símbolo ao portador, que são mal formados ou expirados, ou que têm um token inválido.  

## <a name="access-your-logs"></a>Aceder aos seus registos

Os registos HSM geridos são armazenados no recipiente **insights-logs-auditevent** na conta de armazenamento que forneceu. Para ver os registos, tem de descarregar bolhas. Para obter informações sobre o Azure Storage, consulte [Criar, descarregar e listar bolhas com Azure CLI](../../storage/blobs/storage-quickstart-blobs-cli.md).

As bolhas individuais são armazenadas como texto, formatadas como JSON. Vamos ver um registo de exemplo. O exemplo abaixo mostra a entrada de registo quando um pedido de criação de uma cópia de segurança completa é enviado para o HSM gerido.

```json
[
  {
    "TenantId": "766eaf62-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "time": "2020-08-31T19:52:39.763Z",
    "resourceId": "/SUBSCRIPTIONS/A1BA9AAA-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/MANAGEDHSMS/CONTOSOMHSM",
    "operationName": "BackupCreate",
    "operationVersion": "7.0",
    "category": "AuditEvent",
    "resultType": "Success",
    "properties": {
        "PoolType": "M-HSM",
        "sku_Family": "B",
        "sku_Name": "Standard_B1"
    },
    "durationMs": 488,
    "callerIpAddress": "X.X.X.X",
    "identity": "{\"claim\":{\"appid\":\"04b07795-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"http_schemas_microsoft_com_identity\":{\"claims\":{\"objectidentifier\":\"b1c52bf0-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"}},\"http_schemas_xmlsoap_org_ws_2005_05_identity\":{\"claims\":{\"upn\":\"admin@contoso.com\"}}}}",
    "clientInfo": "azsdk-python-core/1.7.0 Python/3.8.2 (Linux-4.19.84-microsoft-standard-x86_64-with-glibc2.29) azsdk-python-azure-keyvault/7.2",
    "correlationId": "8806614c-ebc3-11ea-9e9b-00155db778ad",
    "subnetId": "(unknown)",
    "httpStatusCode": 202,
    "PoolName": "mhsmdemo",
    "requestUri": "https://ContosoMHSM.managedhsm.azure.net/backup",
    "resourceGroup": "ContosoResourceGroup",
    "resourceProvider": "MICROSOFT.KEYVAULT",
    "resource": "ContosoMHSM",
    "resourceType": "managedHSMs"
  }
]
```

A tabela que se segue lista os nomes e descrições do campo:

| Nome do campo | Descrição |
| --- | --- |
| **TenantId** | ID do inquilino do Azure Ative Directory de subscrição onde o HSM gerido é criado |
| **Hora** |Data e hora na UTC. |
| **recursosId** |Identificação de recursos do Azure Resource Manager. Para registos geridos de HSM, este é sempre o ID de recurso gerido do HSM. |
| **operationName** |Nome da operação, conforme documentada na tabela seguinte. |
| **operaçãoVer** |Versão REST API solicitada pelo cliente. |
| **categoria** |Tipo de resultado. Para registos geridos de HSM, **o AuditEvent** é o valor único disponível. |
| **resultadoType** |Resultado do pedido da API REST. |
| **propriedades** |Informação que varia com base na operação **(operaçãoName)**|
| **assinatura resultados** |Estado de HTTP. |
| **resultadoDescrição** |Descrição adicional sobre o resultado, quando disponível. |
| **duraçõesMs** |Tempo necessário para o processamento do pedido de API REST, em milissegundos. Não inclui a latência de rede, assim, o tempo que medir do lado do cliente poderá não corresponder a este período de tempo. |
| **callerIpAddress** |Endereço IP do cliente que fez o pedido. |
| **correlationId** |Um GUID opcional que o cliente pode passar para correlacionar registos do lado do cliente com registos do lado do serviço. |
| **identidade** |Identidade do símbolo que foi apresentado no pedido da API REST. Este é geralmente um "utilizador", um "principal de serviço". |
| **requestUri** | O RESTANTE API solicita URI |
| **clientInfo** | 

## <a name="use-azure-monitor-logs"></a>Utilizar os registos do Azure Monitor

Pode utilizar a solução Key Vault nos registos do Azure Monitor para rever os registos geridos do HSM **AuditEvent.** Nos registos do Azure Monitor, utiliza consultas de registo para analisar dados e obter a informação de que necessita. 

## <a name="next-steps"></a>Passos seguintes

- Conheça as [melhores práticas](best-practices.md) para provisão e utilize um HSM gerido
- Saiba [como fazer backup e restaurar](backup-restore.md) um HSM gerido
