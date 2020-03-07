---
title: Encriptação de disco azure para Linux
description: Implementa encriptação de disco Azure para Linux para uma máquina virtual utilizando uma extensão virtual da máquina.
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
ms.openlocfilehash: 4fa7f7d1419a8cd1006a632ba67587ab3434bf5a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78383421"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Encriptação de disco azure para Linux (Microsoft.Azure.Security.AzureDiskCryptonForLinux)

## <a name="overview"></a>Descrição geral

A encriptação do disco Azure aproveita o subsistema dm-crypt em Linux para fornecer encriptação completa do disco em [distribuições selecionadas do Azure Linux](https://aka.ms/adelinux).  Esta solução está integrada com o Azure Key Vault para gerir chaves e segredos de encriptação de disco.

## <a name="prerequisites"></a>Pré-requisitos

Para obter uma lista completa de pré-requisitos, consulte a [Encriptação do Disco Azure para VMs Linux,](../linux/disk-encryption-overview.md)especificamente as seguintes secções:

- [Encriptação de disco azure para VMs Linux](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisitos adicionais de VM](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Requisitos de networking](../linux/disk-encryption-overview.md#networking-requirements)

## <a name="extension-schemata"></a>Schemata de extensão

Existem dois esquemas para encriptação de disco azure: v1.1, um esquema mais recente e recomendado que não utiliza propriedades de Azure Ative Directory (AAD) e v0.1, um esquema mais antigo que requer propriedades AAD. Tem de utilizar a versão schema correspondente à extensão que está a utilizar: schema v1.1 para a versão de extensão AzureDiskEncryptionForLinux versão 1.1, schema v0.1 para a versão de extensão AzureDiskCryptonForLinux 0.1.
### <a name="schema-v11-no-aad-recommended"></a>Schema v1.1: Sem AAD (recomendado)

O esquema v1.1 é recomendado e não requer propriedades de Diretório Ativo Azure.

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


### <a name="schema-v01-with-aad"></a>Schema v0.1: com AAD 

O esquema 0.1 requer `aadClientID` e `aadClientSecret` ou `AADClientCertificate`.

Utilização do `aadClientSecret`:

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

Utilização do `AADClientCertificate`:

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
| publicador | Microsoft.Azure.Security | string |
| tipo | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 0.1, 1,1 | int |
| (esquema 0.1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (esquema 0.1) AADClientSecret | palavra-passe | string |
| (esquema 0.1) AADClientCertificate | thumbprint | string |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | Dicionário JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | string |
| KeyEncryptionKeyURL | url | string |
| (opcional) KeyVaultURL | url | string |
| Passphrase | palavra-passe | string | 
| SequenceVersion | uniqueidentifier | string |
| VolumeType | OS, Data, All | string |

## <a name="template-deployment"></a>Implementação de modelos

Para um exemplo de implementação do modelo, consulte [enable Encryption num VM Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

## <a name="azure-cli-deployment"></a>Implementação de CLI do Azure

As instruções podem ser encontradas na mais recente [documentação do Azure CLI.](/cli/azure/vm/encryption?view=azure-cli-latest) 

## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

Para resolução de problemas, consulte o guia de resolução de problemas da [Encriptação do Disco Azure](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/community/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o suporte do [Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre extensões VM, consulte [extensões e funcionalidades da máquina virtual para Linux](features-linux.md).