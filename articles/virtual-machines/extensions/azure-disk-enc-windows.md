---
title: Azure Disk Encryption para Windows | Microsoft Docs
description: Implanta Azure Disk Encryption em uma máquina virtual do Windows usando uma extensão de máquina virtual.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 00891122015bb3e6adb500b6f6c30fa031161b92
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598005"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Azure Disk Encryption para Windows (Microsoft. Azure. Security. AzureDiskEncryption)

## <a name="overview"></a>Visão geral

O Azure Disk Encryption aproveita o BitLocker para fornecer criptografia de disco completa em máquinas virtuais do Azure que executam o Windows.  Essa solução é integrada com Azure Key Vault para gerenciar chaves de criptografia de disco e segredos em sua assinatura do cofre de chaves. 

## <a name="prerequisites"></a>Pré-requisitos

Para obter uma lista completa de pré-requisitos, consulte [Azure Disk Encryption para VMs do Linux](../linux/disk-encryption-overview.md), especificamente as seguintes seções:

- [Azure Disk Encryption para VMs Linux](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisitos de rede](../windows/disk-encryption-overview.md#networking-requirements)
- [Requisitos de Política de Grupo](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schemata"></a>Schemata de extensão

Há duas Schemata para Azure Disk Encryption: v 1.1, um esquema mais novo e recomendado que não usa as propriedades do AAD (Azure Active Directory) e v 0.1, um esquema mais antigo que requer Propriedades do AAD. Você deve usar a versão do esquema correspondente à extensão que está usando: esquema v 1.1 para a extensão AzureDiskEncryption versão 1,1, esquema v 0.1 para a extensão AzureDiskEncryption versão 0,1.

### <a name="schema-v11-no-aad-recommended"></a>Esquema v 1.1: sem AAD (recomendado)

O esquema v 1.1 é recomendado e não requer Azure Active Directory Propriedades.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="schema-v01-with-aad"></a>Esquema v 0,1: com o AAD 

O esquema 0,1 requer `aadClientID` e `aadClientSecret` ou `AADClientCertificate`.

Usando `aadClientSecret`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```

Usando `AADClientCertificate`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="property-values"></a>Valores de propriedade

| Nome | Valor/exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| Programa | Microsoft. Azure. Security | string |
| tipo | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 0,1, 1,1 | int |
| (esquema 0,1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Volume | 
| (esquema 0,1) AADClientSecret | palavra-passe | string |
| (esquema 0,1) AADClientCertificate | digitais | string |
| DiskFormatQuery | {"dev_path": "", "Name": "", "file_system": ""} | Dicionário JSON |
| EncryptionOperation | Enableencryption e, EnableEncryptionFormatAll | string | 
| KeyEncryptionAlgorithm | ' RSA-OAEP ', ' RSA-OAEP-256 ', ' RSA1_5 ' | string |
| keyEncryptionKeyURL | url | string |
| keyVaultURL | url | string |
| adicional Senha | palavra-passe | string | 
| sequenceVersion | uniqueidentifier | string |
| volumeType | SO, dados, todos | string |

## <a name="template-deployment"></a>Implementação de modelos
Para obter um exemplo de implantação de modelo, consulte [criar uma nova VM do Windows criptografada da imagem da Galeria](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

## <a name="azure-cli-deployment"></a>Implantação de CLI do Azure

As instruções podem ser encontradas na documentação mais recente do [CLI do Azure](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

Consulte o [Guia de solução de problemas Azure Disk Encryption](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, você poderá entrar em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow do MSDN](https://azure.microsoft.com/support/community/). Como alternativa, você pode arquivar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione obter suporte. Para obter informações sobre como usar o suporte do Azure, leia as [perguntas frequentes sobre suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre extensões, consulte [recursos e extensões de máquina virtual para Windows](features-windows.md).
