---
title: Guia de solução de problemas Azure Disk Encryption
description: Este artigo fornece dicas de solução de problemas para Microsoft Azure a criptografia de disco para VMs do Windows.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 0d4e76f4d02b0287770243bfddf995a19f90d232
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749443"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Guia de solução de problemas Azure Disk Encryption

Este guia destina-se a profissionais de ti, analistas de segurança de informações e administradores de nuvem cujas organizações usam Azure Disk Encryption. Este artigo é para ajudar na solução de problemas relacionados à criptografia de disco.

Antes de executar qualquer uma das etapas a seguir, primeiro verifique se as VMs que você está tentando criptografar estão entre os [tamanhos de VM e sistemas operacionais com suporte](disk-encryption-overview.md#supported-vms-and-operating-systems)e se você atendeu a todos os pré-requisitos:

- [Requisitos de rede](disk-encryption-overview.md#networking-requirements)
- [Requisitos de política de grupo](disk-encryption-overview.md#group-policy-requirements)
- [Requisitos de armazenamento de chave de criptografia](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Solução de problemas Azure Disk Encryption por trás de um firewall

Quando a conectividade é restrita por um firewall, requisito de proxy ou configurações de NSG (grupo de segurança de rede), a capacidade da extensão de executar as tarefas necessárias pode ser interrompida. Essa interrupção pode resultar em mensagens de status como "status da extensão não disponível na VM". Em cenários esperados, a criptografia não é concluída. As seções a seguir têm alguns problemas de firewall comuns que você pode investigar.

### <a name="network-security-groups"></a>Grupos de segurança de rede
Qualquer configuração de grupo de segurança de rede aplicada ainda deve permitir que o ponto de extremidade atenda aos [pré-requisitos](disk-encryption-overview.md#networking-requirements) de configuração de rede documentados para criptografia de disco.

### <a name="azure-key-vault-behind-a-firewall"></a>Azure Key Vault atrás de um firewall

Quando a criptografia está sendo habilitada com [as credenciais do Azure ad](disk-encryption-windows-aad.md#), a VM de destino deve permitir a conectividade com os pontos de extremidade Azure Active Directory e Key Vault pontos de extremidade. Os pontos de extremidade de autenticação Azure Active Directory atuais são mantidos nas seções 56 e 59 da documentação de [intervalos de endereços IP e URLs do Office 365](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) . Key Vault instruções são fornecidas na documentação sobre como [acessar Azure Key Vault atrás de um firewall](../../key-vault/key-vault-access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Serviço de metadados de instância do Azure 
A VM deve ser capaz de acessar o ponto de extremidade do [serviço de metadados de instância do Azure](../windows/instance-metadata-service.md) , que usa um endereço IP não roteável conhecido (`169.254.169.254`) que pode ser acessado somente de dentro da VM.  Não há suporte para as configurações de proxy que alteram o tráfego HTTP local para esse endereço (por exemplo, a adição de um cabeçalho X-Forwardd-for).

## <a name="troubleshooting-windows-server-2016-server-core"></a>Solução de problemas do Windows Server 2016 Server Core

No Windows Server 2016 Server Core, o componente BdeHdCfg não está disponível por padrão. Esse componente é exigido pelo Azure Disk Encryption. Ele é usado para dividir o volume do sistema do volume do so, que é feito apenas uma vez durante o tempo de vida da VM. Esses binários não são necessários durante operações de criptografia posteriores.

Para contornar esse problema, copie os quatro arquivos a seguir de uma VM do Data Center do Windows Server 2016 para o mesmo local no Server Core:

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

1. Introduza o seguinte comando:

   ```
   bdehdcfg.exe -target default
   ```

1. Esse comando cria uma partição de sistema de 550 MB. Reinicialize o sistema.

1. Use o DiskPart para verificar os volumes e continue.  

Por exemplo:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```

## <a name="troubleshooting-encryption-status"></a>Solucionando problemas de status de criptografia 

O portal pode exibir um disco como criptografado mesmo após ele ter sido descriptografado na VM.  Isso pode ocorrer quando comandos de baixo nível são usados para descriptografar diretamente o disco de dentro da VM, em vez de usar os comandos de gerenciamento de Azure Disk Encryption de nível superior.  Os comandos de nível superior não apenas descriptografam o disco de dentro da VM, mas fora da VM eles também atualizam configurações importantes de criptografia de nível de plataforma e configurações de extensão associadas à VM.  Se eles não forem mantidos em alinhamento, a plataforma não poderá relatar o status de criptografia nem provisionar a VM corretamente.   

Para desabilitar Azure Disk Encryption com o PowerShell, use [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) seguido de [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). A execução de Remove-AzVMDiskEncryptionExtension antes da criptografia ser desabilitada falhará.

Para desabilitar Azure Disk Encryption com a CLI, use [AZ VM Encryption Disable](/cli/azure/vm/encryption). 

## <a name="next-steps"></a>Passos seguintes

Neste documento, você aprendeu mais sobre alguns problemas comuns no Azure Disk Encryption e como solucionar esses problemas. Para obter mais informações sobre esse serviço e seus recursos, consulte os seguintes artigos:

- [Aplicar a criptografia de disco na central de segurança do Azure](../../security-center/security-center-apply-disk-encryption.md)
- [Criptografia de dados do Azure em repouso](../../security/fundamentals/encryption-atrest.md)
