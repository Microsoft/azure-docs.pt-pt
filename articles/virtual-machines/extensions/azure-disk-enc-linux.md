---
title: Azure Disk Encryption para Linux | Microsoft Docs
description: Implanta Azure Disk Encryption para Linux em uma máquina virtual usando uma extensão de máquina virtual.
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: ejarvi
ms.openlocfilehash: 6a81f105f9632a7ca7e2bf7188e358274020c78f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084773"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption para Linux (Microsoft. Azure. Security. AzureDiskEncryptionForLinux)

## <a name="overview"></a>Descrição geral

Azure Disk Encryption aproveita o subsistema DM-cript no Linux para fornecer criptografia de disco completa em [distribuições do Linux do Azure](https://aka.ms/adelinux).  Essa solução é integrada com Azure Key Vault para gerenciar chaves de criptografia de disco e segredos.

## <a name="prerequisites"></a>Pré-requisitos

Para obter uma lista completa de pré-requisitos, consulte [Azure Disk Encryption](
../../security/azure-security-disk-encryption-prerequisites.md)pré-requisitos.

### <a name="operating-system"></a>Sistema operativo

Atualmente, o Azure Disk Encryption tem suporte em distribuições e versões selecionadas.  Consulte os [Azure Disk Encryption sistemas operacionais com suporte: Linux](../../security/azure-security-disk-encryption-prerequisites.md#linux) para obter a lista de distribuições do Linux com suporte.

### <a name="internet-connectivity"></a>Conectividade Internet

O Azure Disk Encryption para Linux requer conectividade com a Internet para acesso a pontos de extremidade de gerenciamento de Active Directory, Key Vault, armazenamento e pacotes.  Para obter mais informações, consulte Prerequisites [Azure Disk Encryption](../../security/azure-security-disk-encryption-prerequisites.md).

## <a name="extension-schemata"></a>Schemata de extensão

Há duas Schemata para Azure Disk Encryption: v 1.1, um esquema mais novo e recomendado que não usa as propriedades do AAD (Azure Active Directory) e v 0.1, um esquema mais antigo que requer Propriedades do AAD. Você deve usar a versão do esquema correspondente à extensão que está usando: esquema v 1.1 para a extensão AzureDiskEncryptionForLinux versão 1,1, esquema v 0.1 para a extensão AzureDiskEncryptionForLinux versão 0,1.
### <a name="schema-v11-no-aad-recommended"></a>Esquema v 1.1: Sem AAD (recomendado)

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
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultURL": "[keyVaultURL]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        },
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="schema-v01-with-aad"></a>Esquema v 0,1: com o AAD 

O esquema 0,1 requer `aadClientID` o e `aadClientSecret` o `AADClientCertificate`ou o.

Usando `aadClientSecret`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
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
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="property-values"></a>Valores de propriedade

| Nome | Valor / exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | Cadeia de caracteres |
| type | AzureDiskEncryptionForLinux | Cadeia de caracteres |
| typeHandlerVersion | 0,1, 1,1 | int |
| (esquema 0,1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | volume | 
| (esquema 0,1) AADClientSecret | password | Cadeia de caracteres |
| (esquema 0,1) AADClientCertificate | thumbprint | Cadeia de caracteres |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | Dicionário JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | Cadeia de caracteres | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | Cadeia de caracteres |
| KeyEncryptionKeyURL | url | Cadeia de caracteres |
| adicional KeyVaultURL | url | Cadeia de caracteres |
| Passphrase | password | Cadeia de caracteres | 
| SequenceVersion | uniqueidentifier | Cadeia de caracteres |
| VolumeType | OS, Data, All | Cadeia de caracteres |

## <a name="template-deployment"></a>Implementação de modelos

Para obter um exemplo de implantação de modelo, consulte [habilitar a criptografia em uma VM Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

## <a name="azure-cli-deployment"></a>Implementação de CLI do Azure

As instruções podem ser encontradas na documentação mais recente do [CLI do Azure](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

Para solução de problemas, consulte o [Guia de solução de problemas Azure Disk Encryption](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/community/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre extensões de VM, consulte [recursos e extensões de máquina virtual para Linux](features-linux.md).