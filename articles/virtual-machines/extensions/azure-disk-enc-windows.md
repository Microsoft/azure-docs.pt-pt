---
title: Azure Disk Encryption para Windows | Documentos da Microsoft
description: Implementa o Azure Disk Encryption para uma máquina de virtual do Windows com uma extensão de máquina virtual.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 9a3e135172f0744c053da816b3c77762dbe783c3
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706104"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Azure Disk Encryption para Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Descrição geral

O Azure Disk Encryption tira partido do BitLocker para fornecer encriptação de disco completa em máquinas virtuais do Azure a executar o Windows.  Esta solução está integrada no Azure Key Vault para gerir as chaves de encriptação de disco e segredos na sua subscrição do Cofre de chaves. 

## <a name="prerequisites"></a>Pré-requisitos

Para obter uma lista completa dos pré-requisitos, consulte [pré-requisitos do Azure Disk Encryption](
../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="operating-system"></a>Sistema operativo

Para obter uma lista das versões do Windows atualmente, consulte [pré-requisitos do Azure Disk Encryption](../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="internet-connectivity"></a>Conectividade Internet

O Azure Disk Encryption requer conectividade à Internet para acesso ao Active Directory, o Cofre de chaves, armazenamento e pontos finais de gestão do pacote.  Para saber mais sobre as definições de segurança de rede, consulte [pré-requisitos do Azure Disk Encryption](
../../security/azure-security-disk-encryption-prerequisites.md).

## <a name="extension-schemata"></a>Esquemas de extensão

Existem dois esquemas para a encriptação de disco do Azure: v1.1, um esquema mais recente, é recomendado que não utiliza as propriedades do Azure Active Directory (AAD) e o v0.1, um esquema mais antigo que necessita de propriedades do AAD. Tem de utilizar a versão do esquema correspondente para a extensão estiver a utilizar: v1.1 de esquema para a versão 1.1, v0.1 de esquema para a versão da extensão 0,1 de AzureDiskEncryption da extensão de AzureDiskEncryption.

### <a name="schema-v11-no-aad-recommended"></a>Schema v1.1: Não existem AAD (recomendado)

O esquema de v1.1 é recomendado e não necessita de propriedades do Azure Active Directory.

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


### <a name="schema-v01-with-aad"></a>Esquema v0.1: com o AAD 

Requer o esquema de 0,1 `aadClientID` e qualquer um dos `aadClientSecret` ou `AADClientCertificate`.

Using `aadClientSecret`:

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

Using `AADClientCertificate`:

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
| publisher | Microsoft.Azure.Security | Cadeia de caracteres |
| type | AzureDiskEncryptionForLinux | Cadeia de caracteres |
| typeHandlerVersion | 0.1, 1.1 | int |
| (esquema de 0,1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID | 
| (esquema de 0,1) AADClientSecret | password | cadeia |
| (esquema de 0,1) AADClientCertificate | thumbprint | Cadeia de caracteres |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | Dicionário JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | cadeia | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | Cadeia de caracteres |
| KeyEncryptionKeyURL | url | Cadeia de caracteres |
| KeyVaultURL | url | Cadeia de caracteres |
| (opcional) Passphrase | password | cadeia | 
| SequenceVersion | uniqueidentifier | cadeia |
| VolumeType | OS, Data, All | Cadeia de caracteres |

## <a name="template-deployment"></a>Implementação de modelos
Para obter um exemplo de implementação do modelo, consulte [ criar uma nova VM do Windows criptografado da imagem da galeria](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

## <a name="azure-cli-deployment"></a>Implementação de CLI do Azure

Pode encontrar instruções na versão mais recente [documentação da CLI do Azure](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

Consulte a [guia de resolução de problemas do Azure Disk Encryption](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/community/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre as extensões, consulte [extensões de Máquina Virtual e funcionalidades para o Windows](features-windows.md).
