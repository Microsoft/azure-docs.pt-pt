---
title: Encriptação do disco Azure para Windows
description: Implementa encriptação de disco Azure para uma máquina virtual do Windows utilizando uma extensão de máquina virtual.
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
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: e975e1757b77b4aab52a59d1f0709ef9cadae94e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80066871"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Encriptação do disco Azure para Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Descrição geral

A Azure Disk Encryption aproveita o BitLocker para fornecer encriptação completa do disco em máquinas virtuais Azure que executam o Windows.  Esta solução está integrada com o Azure Key Vault para gerir chaves e segredos de encriptação de discos na subscrição do cofre principal. 

## <a name="prerequisites"></a>Pré-requisitos

Para obter uma lista completa de pré-requisitos, consulte [a encriptação do disco Azure para VMs do Windows,](../windows/disk-encryption-overview.md)especificamente as seguintes secções:

- [VMs e sistemas operativos suportados](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisitos de rede](../windows/disk-encryption-overview.md#networking-requirements)
- [Requisitos de Política de Grupo](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schema"></a>Esquema de extensão

Existem duas versões de esquema de extensão para encriptação do disco Azure (ADE):
- v2.2 - Um esquema recomendado mais recente que não utiliza propriedades do Azure Ative Directory (AAD).
- v1.1 - Um esquema mais antigo que requer propriedades do Azure Ative Directory (AAD). 

Para selecionar um esquema de destino, a `typeHandlerVersion` propriedade tem de ser definida igual à versão do esquema que pretende utilizar.

### <a name="schema-v22-no-aad-recommended"></a>Schema v2.2: Sem AAD (recomendado)

O esquema v2.2 é recomendado para todos os novos VMs e não requer propriedades do Azure Ative Directory.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryption",
        "typeHandlerVersion": "2.2",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KekVaultResourceId": "[keyVaultResourceID]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultResourceId": "[keyVaultResourceID]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v11-with-aad"></a>Schema v1.1: com AAD 

O esquema 1.1 requer `aadClientID` e ou não é recomendado para novos `aadClientSecret` `AADClientCertificate` VMs.

`aadClientSecret`Utilização:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```

`AADClientCertificate`Utilização:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>Valores patrimoniais

| Nome | Valor / Exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publicador | Microsoft.Azure.Security | string |
| tipo | AzureDiskEncryption | string |
| typeHandlerVersion | 2.2, 1.1 | string |
| (1.1 esquema) AADClientID | xxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxxxxx | guid | 
| (1.1 esquema) AADClientSecret | palavra-passe | string |
| (1.1 esquema) Certificado de AADClient | impressão digital | string |
| EncriptaçãoOperação | EnableEncryption, EnableEncryptionFormatAll | string | 
| (opcional - padrão RSA-OAEP) KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | string |
| KeyVaultURL | url | string |
| KeyVaultResourceId | url | string |
| (opcional) KeyEncryptionKeyURL | url | string |
| (opcional) KekVaultResourceId | url | string |
| (opcional) SequênciaVersão | uniqueidentifier | string |
| VolumeType | OS, Dados, Todos | string |

## <a name="template-deployment"></a>Implementação de modelos

Para um exemplo de implementação do modelo com base no esquema v2.2, consulte O Modelo Azure QuickStart [201-encrypt-running-windows-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).

Para um exemplo de implementação do modelo com base no esquema v1.1, consulte O Modelo Azure QuickStart [201-encriptação-running-windows-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).

>[!NOTE]
> Além disso, se o `VolumeType` parâmetro for definido para Todos, os discos de dados só serão encriptados se estiverem devidamente formatados. 

## <a name="troubleshoot-and-support"></a>Resolução de problemas e apoio

### <a name="troubleshoot"></a>Resolução de problemas

Para a resolução de problemas, consulte o [guia de resolução de problemas de encriptação do disco Azure](../windows/disk-encryption-troubleshooting.md).

### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/community/). 

Em alternativa, pode apresentar um incidente de suporte Azure. Vá ao [suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o [Microsoft Azure Support FAQ](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre extensões, consulte [extensões e funcionalidades da máquina Virtual para Windows](features-windows.md).
* Para obter mais informações sobre a encriptação do disco Azure para windows, consulte [as máquinas virtuais do Windows](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#windows-virtual-machines).
