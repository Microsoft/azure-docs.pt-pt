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
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: e975e1757b77b4aab52a59d1f0709ef9cadae94e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066871"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Encriptação de disco azure para Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Descrição geral

A encriptação do disco azure aproveita o BitLocker para fornecer encriptação completa do disco em máquinas virtuais Azure que executam o Windows.  Esta solução está integrada com o Azure Key Vault para gerir chaves e segredos de encriptação de disco na subscrição do cofre chave. 

## <a name="prerequisites"></a>Pré-requisitos

Para obter uma lista completa de pré-requisitos, consulte a [Encriptação do Disco Azure para VMs do Windows](../windows/disk-encryption-overview.md), especificamente as seguintes secções:

- [VMs e sistemas operativos suportados](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisitos de networking](../windows/disk-encryption-overview.md#networking-requirements)
- [Requisitos de Política de Grupo](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schema"></a>Extensão Schema

Existem duas versões de esquema de extensão para encriptação de disco azure (ADE):
- v2.2 - Um novo esquema recomendado que não utiliza propriedades de Diretório Ativo Azure (AAD).
- v1.1 - Um esquema mais antigo que requer propriedades de Diretório Ativo Azure (AAD). 

Para selecionar um esquema de `typeHandlerVersion` destino, a propriedade deve ser definida igual à versão do esquema que pretende utilizar.

### <a name="schema-v22-no-aad-recommended"></a>Schema v2.2: Sem AAD (recomendado)

O esquema v2.2 é recomendado para todos os novos VMs e não requer propriedades de Diretório Ativo Azure.

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

O esquema 1.1 requer `aadClientID` e `aadClientSecret` `AADClientCertificate` ou ou não é recomendado para novos VMs.

Utilizando: `aadClientSecret`

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

Utilizando: `AADClientCertificate`

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
| tipo | Encriptação AzureDisk | string |
| typeHandlerVersion | 2.2, 1.1 | string |
| (1.1 esquema) AADClientID | xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxxx | guia | 
| (1.1 esquema) AADClientSecret | palavra-passe | string |
| (1.1 esquema) Certificado aADClient | impressão de polegar | string |
| Operação de encriptação | EnableEncryption, EnableEncryptionFormatAll | string | 
| (opcional - rSA-OAEP padrão) Algoritmo de Encriptação de Chaves | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | string |
| KeyVaultURL | url | string |
| KeyvaultResourceid | url | string |
| (opcional) KeyEncryptionKeyURL | url | string |
| (opcional) KekVaultResourceId | url | string |
| (opcional) SequênciaVersão | uniqueidentifier | string |
| VolumeType | S, Dados, Todos | string |

## <a name="template-deployment"></a>Implementação de modelos

Para um exemplo de implantação do modelo com base no esquema v2.2, consulte o Modelo QuickStart [Azure 201-encrypt-running-windows-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).

Para um exemplo de implantação do modelo com base em esquema v1.1, consulte o Modelo QuickStart [Azure 201-encrypt-running-windows-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).

>[!NOTE]
> Também `VolumeType` se o parâmetro estiver definido para Todos, os discos de dados só serão encriptados se estiverem devidamente formatados. 

## <a name="troubleshoot-and-support"></a>Resolução de problemas e apoio

### <a name="troubleshoot"></a>Resolução de problemas

Para resolução de problemas, consulte o guia de resolução de problemas da [Encriptação do Disco Azure](../windows/disk-encryption-troubleshooting.md).

### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/community/). 

Em alternativa, pode apresentar um incidente de apoio ao Azure. Vá ao [suporte azure](https://azure.microsoft.com/support/options/) e selecione Suporte. Para obter informações sobre a utilização do Suporte Azure, leia as FAQ de suporte do [Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre extensões, consulte [extensões e funcionalidades da máquina Virtual para Windows](features-windows.md).
* Para obter mais informações sobre encriptação do disco Azure para Windows, consulte [as máquinas virtuais do Windows](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#windows-virtual-machines).
