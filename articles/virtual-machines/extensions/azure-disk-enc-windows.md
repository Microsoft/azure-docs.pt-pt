---
title: Azure Disk Encryption para Windows | Documentos da Microsoft
description: Implementa o Azure Disk Encryption para uma máquina de virtual do Windows com uma extensão de máquina virtual.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 46699fb1add42d23a11234d5cd05e4a9627a91fd
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56983477"
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

      "EncryptionOperation": "[encryptionOperation]",
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
| publicador | Microsoft.Azure.Security | string |
| tipo | AzureDiskEncryptionForWindows| string |
| typeHandlerVersion | 1.0, 1.1, 2.2 (VMSS) | int |
| (opcional) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID | 
| (opcional) AADClientSecret | palavra-passe | string |
| (opcional) AADClientCertificate | Thumbprint | string |
| EncryptionOperation | EnableEncryption | string | 
| KeyEncryptionAlgorithm | RSA-OAEP, RSA1_5 | string |
| KeyEncryptionKeyURL | url | string |
| KeyVaultResourceId | uri do recurso | string |
| KekVaultResourceId | uri do recurso | string |
| KeyVaultURL | url | string |
| SequenceVersion | uniqueidentifier | string |
| VolumeType | Sistema operacional, dados, todos os | string |

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
