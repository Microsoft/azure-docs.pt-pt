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
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: 22568c7c23771f143f6cd583114949c380d15e3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066925"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Encriptação de disco azure para Linux (Microsoft.Azure.Security.AzureDiskCryptonForLinux)

## <a name="overview"></a>Descrição geral

A encriptação do disco Azure aproveita o subsistema dm-crypt em Linux para fornecer encriptação completa do disco em [distribuições selecionadas do Azure Linux](https://aka.ms/adelinux).  Esta solução está integrada com o Azure Key Vault para gerir chaves e segredos de encriptação de disco.

## <a name="prerequisites"></a>Pré-requisitos

Para obter uma lista completa de pré-requisitos, consulte a [Encriptação do Disco Azure para VMs Linux,](../linux/disk-encryption-overview.md)especificamente as seguintes secções:

- [VMs e sistemas operativos suportados](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisitos adicionais de VM](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Requisitos de networking](../linux/disk-encryption-overview.md#networking-requirements)
- [Requisitos de armazenamento de chaves de encriptação](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>Extensão Schema

Existem duas versões de esquema de extensão para encriptação de disco azure (ADE):
- v1.1 - Um novo esquema recomendado que não utiliza propriedades de Diretório Ativo Azure (AAD).
- v0.1 - Um esquema mais antigo que requer propriedades de Diretório Ativo Azure (AAD). 

Para selecionar um esquema de `typeHandlerVersion` destino, a propriedade deve ser definida igual à versão do esquema que pretende utilizar.

### <a name="schema-v11-no-aad-recommended"></a>Schema v1.1: Sem AAD (recomendado)

O esquema v1.1 é recomendado e não requer propriedades de Azure Ative Directory (AAD).

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "1.1",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[KeyVaultResourceId]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[KekVaultResourceId",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v01-with-aad"></a>Schema v0.1: com AAD 

O esquema 0.1 requer `AADClientID` e `AADClientSecret` `AADClientCertificate`ou.

Utilizando: `AADClientSecret`

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
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
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
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
| tipo | AzureDiskCryptonForLinux | string |
| typeHandlerVersion | 1.1, 0.1 | int |
| (0.1 esquema) AADClientID | xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxxx | guia | 
| (0.1 esquema) AADClientSecret | palavra-passe | string |
| (0.1 esquema) Certificado aADClient | impressão de polegar | string |
| (opcional) (0.1 esquema) Frase-passe | palavra-passe | string |
| DiskFormatQuery | {"dev_path","nome":"""file_system"} | Dicionário JSON |
| Operação de encriptação | EnableEncryption, EnableEncryptionFormatAll | string | 
| (opcional - rSA-OAEP padrão) Algoritmo de Encriptação de Chaves | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | string |
| KeyVaultURL | url | string |
| KeyvaultResourceid | url | string |
| (opcional) KeyEncryptionKeyURL | url | string |
| (opcional) KekVaultResourceId | url | string |
| (opcional) SequênciaVersão | uniqueidentifier | string |
| VolumeType | S, Dados, Todos | string |

## <a name="template-deployment"></a>Implementação de modelos

Para um exemplo de implantação do modelo com base em esquema v1.1, consulte o Modelo De Arranque Rápido [Azure 201-encrypt-running-linux-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

Para um exemplo de implantação do modelo com base em esquema v0.1, consulte o Modelo De Arranque Rápido [Azure 201-encrypt-running-linux-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

>[!WARNING]
> - Se já utilizou anteriormente a Encriptação do Disco Azure com a AD Azure para encriptar um VM, tem de continuar a utilizar esta opção para encriptar o seu VM.
> - Ao encriptar os volumes de Sistema sO, o VM deve ser considerado indisponível. Recomendamos vivamente que evite os logins do SSH enquanto a encriptação está em andamento para evitar problemas que bloqueiem quaisquer ficheiros abertos que precisem de ser acedidos durante o processo de encriptação. Para verificar o progresso, utilize o [comando Get-AzVMDiskCryptOnStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell ou o comando CLI de [encriptação vm.](/cli/azure/vm/encryption#az-vm-encryption-show) Espera-se que este processo derea algumas horas para um volume de 30GB de OS, além de tempo adicional para encriptar volumes de dados. O tempo de encriptação do volume de dados será proporcional ao tamanho e quantidade dos volumes de dados, a menos que o formato de encriptação seja utilizado. 
> - A encriptação incapacitante nos VMs linux é suportada apenas para volumes de dados. Não é suportado em dados ou volumes de OS se o volume de S tiver sido encriptado. 

>[!NOTE]
> Também `VolumeType` se o parâmetro estiver definido para Todos, os discos de dados só serão encriptados se estiverem corretamente montados.

## <a name="troubleshoot-and-support"></a>Resolução de problemas e apoio

### <a name="troubleshoot"></a>Resolução de problemas

Para resolução de problemas, consulte o guia de resolução de problemas da [Encriptação do Disco Azure](../linux/disk-encryption-troubleshooting.md).

### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/community/). 

Em alternativa, pode apresentar um incidente de apoio ao Azure. Vá ao [suporte azure](https://azure.microsoft.com/support/options/) e selecione Suporte. Para obter informações sobre a utilização do Suporte Azure, leia as FAQ de suporte do [Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre extensões VM, consulte [extensões e funcionalidades da máquina virtual para Linux](features-linux.md).
* Para obter mais informações sobre a encriptação do disco Azure para o Linux, consulte [as máquinas virtuais Linux](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines).
