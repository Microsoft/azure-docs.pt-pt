---
title: Encriptação de disco azure para Windows
description: Implementa encriptação de disco azure para uma máquina virtual do Windows utilizando uma extensão virtual da máquina.
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
ms.openlocfilehash: 8435663dcf92e2617ea2fe9218649e94243272d2
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79250650"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Encriptação de disco azure para Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Descrição geral

A encriptação do disco azure aproveita o BitLocker para fornecer encriptação completa do disco em máquinas virtuais Azure que executam o Windows.  Esta solução está integrada com o Azure Key Vault para gerir chaves e segredos de encriptação de disco na subscrição do cofre chave. 

## <a name="prerequisites"></a>Pré-requisitos

Para obter uma lista completa de pré-requisitos, consulte a [Encriptação do Disco Azure para VMs Linux,](../linux/disk-encryption-overview.md)especificamente as seguintes secções:

- [Encriptação de disco azure para VMs Linux](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisitos de networking](../windows/disk-encryption-overview.md#networking-requirements)
- [Requisitos de Política de Grupo](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schemata"></a>Schemata de extensão

Existem dois esquemas para a extensão Windows AzureDiskEncryption: v2.2, um esquema mais recente e recomendado que não utiliza propriedades de Azure Ative Directory (AAD) e v1.1, um esquema mais antigo que requer propriedades AAD. Tem de utilizar a versão schema correspondente à extensão que está a utilizar: schema v2.2 para a versão de extensão AzureDiskEncryption 2.2, schema v1.1 para a versão 1.1 da extensão AzureDiskEncryption.

### <a name="schema-v22-no-aad-recommended"></a>Schema v2.2: Sem AAD (recomendado)

O esquema v2.2 é recomendado para todos os novos VMs e não requer propriedades de Diretório Ativo Azure.

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


### <a name="schema-v11-with-aad"></a>Schema v1.1: com AAD 

O esquema 1.1 requer `aadClientID` e `aadClientSecret` ou `AADClientCertificate` e não é recomendado para novos VMs.

Utilização do `aadClientSecret`:

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

Utilização do `AADClientCertificate`:

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

| Nome | Valor / exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publicador | Microsoft.Azure.Security | Cadeia de caracteres |
| tipo | AzureDiskEncryptionForLinux | Cadeia de caracteres |
| typeHandlerVersion | 1.1, 2.2 | Cadeia de caracteres |
| (1.1 esquema) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (1.1 esquema) AADClientSecret | palavra-passe | Cadeia de caracteres |
| (1.1 esquema) Certificado aADClient | thumbprint | Cadeia de caracteres |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | Dicionário JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | Cadeia de caracteres | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | Cadeia de caracteres |
| KeyEncryptionKeyURL | URL | Cadeia de caracteres |
| KeyVaultURL | URL | Cadeia de caracteres |
| (opcional) Passphrase | palavra-passe | Cadeia de caracteres | 
| SequenceVersion | uniqueidentifier | Cadeia de caracteres |
| VolumeType | OS, Data, All | Cadeia de caracteres |

## <a name="template-deployment"></a>Implementação de modelos
Para um exemplo de implementação do modelo, consulte [Criar um novo Windows VM encriptado a partir da imagem da galeria](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

## <a name="azure-cli-deployment"></a>Implementação de CLI do Azure

As instruções podem ser encontradas na mais recente [documentação do Azure CLI.](/cli/azure/vm/encryption?view=azure-cli-latest) 

## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshoot"></a>Resolver Problemas

Consulte o guia de resolução de problemas da encriptação do [disco Azure](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/community/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o suporte do [Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre extensões, consulte [extensões e funcionalidades da máquina Virtual para Windows](features-windows.md).
