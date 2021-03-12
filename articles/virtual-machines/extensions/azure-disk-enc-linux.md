---
title: Encriptação do disco Azure para Linux
description: Implementa encriptação de disco Azure para Linux para uma máquina virtual usando uma extensão de máquina virtual.
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
author: ejarvi
ms.author: ejarvi
ms.date: 03/19/2020
ms.collection: linux
ms.openlocfilehash: 7c79391e3459804a4b5ce72c2230d17af3269641
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102566265"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Encriptação do disco Azure para Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Descrição Geral

A Azure Disk Encryption aproveita o subsistema dm-crypt em Linux para fornecer encriptação completa do disco nas [distribuições selecionadas do Azure Linux](../linux/disk-encryption-overview.md).  Esta solução está integrada com o Azure Key Vault para gerir chaves e segredos de encriptação de discos.

## <a name="prerequisites"></a>Pré-requisitos

Para obter uma lista completa de pré-requisitos, consulte [a encriptação do disco Azure para Os VMs Linux,](../linux/disk-encryption-overview.md)especificamente as seguintes secções:

- [VMs e sistemas operativos suportados](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisitos adicionais de VM](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Requisitos de rede](../linux/disk-encryption-overview.md#networking-requirements)
- [Requisitos de armazenamento de chaves de encriptação](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>Esquema de extensão

Existem duas versões de esquema de extensão para encriptação do disco Azure (ADE):
- v1.1 - Um esquema recomendado mais recente que não utiliza propriedades do Azure Ative Directory (AAD).
- v0.1 - Um esquema mais antigo que requer propriedades do Azure Ative Directory (AAD). 

Para selecionar um esquema de destino, a `typeHandlerVersion` propriedade tem de ser definida igual à versão do esquema que pretende utilizar.

### <a name="schema-v11-no-aad-recommended"></a>Schema v1.1: Sem AAD (recomendado)

O esquema v1.1 é recomendado e não requer propriedades do Azure Ative Directory (AAD).

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

O esquema 0.1 requer `AADClientID` e qualquer um ou `AADClientSecret` `AADClientCertificate` .

`AADClientSecret`Utilização:

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

`AADClientCertificate`Utilização:

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

| Name | Valor / Exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | data |
| publicador | Microsoft.Azure.Security | string |
| tipo | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 1.1, 0.1 | int |
| (0.1 esquema) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (0.1 esquema) AADClientSecret | palavra-passe | string |
| (0.1 esquema) Certificado de AADClient | impressão digital | string |
| (opcional) (0.1 esquema) Frase-passe | palavra-passe | string |
| DiskFormatQuery | {"dev_path":"""nome":"""file_system":""} | Dicionário JSON |
| EncriptaçãoOperação | EnableEncryption, EnableEncryptionFormatAll | string | 
| (opcional - padrão RSA-OAEP) KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | string |
| KeyVaultURL | url | string |
| KeyVaultResourceId | url | string |
| (opcional) KeyEncryptionKeyURL | url | string |
| (opcional) KekVaultResourceId | url | string |
| (opcional) SequênciaVersão | uniqueidentifier | string |
| VolumeType | OS, Dados, Todos | string |

## <a name="template-deployment"></a>Implementação de modelos

Para um exemplo de implementação do modelo com base no esquema v1.1, consulte o Modelo Azure Quickstart [201-encrypt-running-linux-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

Para um exemplo de implementação do modelo com base no esquema v0.1, consulte o Modelo Azure Quickstart [201-encrypt-running-linux-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

>[!WARNING]
> - Se já utilizou a Encriptação do Disco Azure com AZure AD para encriptar um VM, deve continuar a utilizar esta opção para encriptar o seu VM.
> - Ao encriptar os volumes linux OS, o VM deve ser considerado indisponível. Recomendamos vivamente evitar logins SSH enquanto a encriptação está em andamento para evitar problemas que bloqueiem quaisquer ficheiros abertos que precisem de ser acedidos durante o processo de encriptação. Para verificar o progresso, utilize o [comandante Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell ou o comando CLI [do programa de encriptação vm.](/cli/azure/vm/encryption#az-vm-encryption-show) Espera-se que este processo despente-se em algumas horas para um volume de SO de 30GB, mais tempo adicional para encriptar volumes de dados. O tempo de encriptação do volume de dados será proporcional ao tamanho e quantidade dos volumes de dados, a menos que o formato de encriptação seja utilizado. 
> - A encriptação incapacitante nos VMs Linux é suportada apenas para volumes de dados. Não é suportado em dados ou volumes de SO se o volume de SO tiver sido encriptado. 

>[!NOTE]
> Além disso, se o `VolumeType` parâmetro for definido para All, os discos de dados só serão encriptados se estiverem corretamente montados.

## <a name="troubleshoot-and-support"></a>Resolução de problemas e apoio

### <a name="troubleshoot"></a>Resolução de problemas

Para a resolução de problemas, consulte o [guia de resolução de problemas de encriptação do disco Azure](../linux/disk-encryption-troubleshooting.md).

### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, pode contactar os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/community/). 

Em alternativa, pode apresentar um incidente de suporte Azure. Vá ao [suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o [Microsoft Azure Support FAQ](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre extensões VM, consulte [extensões e funcionalidades de máquina virtual para Linux.](features-linux.md)
* Para obter mais informações sobre a encriptação do disco Azure para Linux, consulte [as máquinas virtuais Linux](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines).