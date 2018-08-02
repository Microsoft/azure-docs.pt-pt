---
title: Azure Disk Encryption para Linux | Documentos da Microsoft
description: Implementa o Azure Disk Encryption para Linux para uma máquina virtual com uma extensão de máquina virtual.
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 36e8875e91e2f04dbb60bab3211f07b2053e78f5
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414777"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption para Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Descrição geral

O subsistema de dm-crypt no Linux para fornecer encriptação de disco completa em tira partido do Azure Disk Encryption [selecione distribuições de Linux do Azure](https://aka.ms/adelinux).  Esta solução está integrada no Azure Key Vault para gerir as chaves de encriptação de disco e segredos.

## <a name="prerequisites"></a>Pré-requisitos

Para obter uma lista completa dos pré-requisitos, consulte [pré-requisitos do Azure Disk Encryption](
../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="operating-system"></a>Sistema operativo

O Azure Disk Encryption é atualmente suportado em selecionadas Distribuições e versões.  Consulte a [FAQ de encriptação de disco do Azure](../../security/azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) para a lista de distribuições do Linux que são suportadas.

### <a name="internet-connectivity"></a>Conectividade Internet

O Azure Disk Encryption para o Linux requer conectividade à Internet para acesso do Active Directory, o Cofre de chaves, armazenamento e pontos finais de gestão do pacote.  Para obter mais informações, consulte [pré-requisitos do Azure Disk Encryption](../../security/azure-security-disk-encryption-prerequisites.md).

## <a name="extension-schema"></a>Esquema de extensão

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

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor / exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | data |
| publicador | Microsoft.Azure.Security | cadeia |
| tipo | AzureDiskEncryptionForLinux | cadeia |
| typeHandlerVersion | 0,1, 1.1 (VMSS) | Int |
| AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID | 
| AADClientSecret | palavra-passe | cadeia |
| AADClientCertificate | Thumbprint | cadeia |
| DiskFormatQuery | {"dev_path": "", "nome": "","file_system": ""} | Dicionário JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | cadeia | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | cadeia |
| KeyEncryptionKeyURL | url | cadeia |
| KeyVaultURL | url | cadeia |
| Frase de acesso | palavra-passe | cadeia | 
| SequenceVersion | uniqueidentifier | cadeia |
| VolumeType | Sistema operacional, dados, todos os | cadeia |

## <a name="template-deployment"></a>Implementação de modelos

Para obter um exemplo de implementação do modelo, consulte [ativar a encriptação numa VM do Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

## <a name="azure-cli-deployment"></a>Implementação de CLI do Azure

Pode encontrar instruções na versão mais recente [documentação da CLI do Azure](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

Para resolução de problemas, consulte a [guia de resolução de problemas do Azure Disk Encryption](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/community/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre as extensões de VM, consulte [extensões de Máquina Virtual e funcionalidades para Linux](features-linux.md).