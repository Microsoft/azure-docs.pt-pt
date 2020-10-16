---
title: Guia de resolução de problemas de encriptação de disco Azure
description: Este artigo fornece dicas de resolução de problemas para a Encriptação do Disco Azure do Microsoft Azure para VMs windows.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: troubleshooting
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 45c64261b60d88557e63cb8f9af922fd7aa71ad4
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977906"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Guia de resolução de problemas de encriptação de disco Azure

Este guia destina-se a profissionais de TI, analistas de segurança de informação e administradores de nuvem cujas organizações utilizam encriptação de disco Azure. Este artigo é para ajudar na resolução de problemas relacionados com a encriptação do disco.

Antes de tomar qualquer um dos passos abaixo, certifique-se primeiro de que os VMs que está a tentar encriptar estão entre os [tamanhos e sistemas operativos VM suportados](disk-encryption-overview.md#supported-vms-and-operating-systems), e que cumpriu todos os pré-requisitos:

- [Requisitos de rede](disk-encryption-overview.md#networking-requirements)
- [Requisitos de política de grupo](disk-encryption-overview.md#group-policy-requirements)
- [Requisitos de armazenamento de chaves de encriptação](disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Resolução de problemas Azure Disk Encriptação atrás de uma firewall

Quando a conectividade é restringida por uma definição de firewall, proxy ou grupo de segurança de rede (NSG), a capacidade da extensão para executar as tarefas necessárias pode ser interrompida. Esta perturbação pode resultar em mensagens de estado tais como "O estado de extensão não disponível no VM." Em cenários esperados, a encriptação não termina. As secções que se seguem têm alguns problemas comuns de firewall que você pode investigar.

### <a name="network-security-groups"></a>Grupos de segurança de rede
Quaisquer definições de grupo de segurança de rede que sejam aplicadas devem ainda permitir que o ponto final cumpra os [pré-requisitos](disk-encryption-overview.md#networking-requirements) de configuração de rede documentados para encriptação do disco.

### <a name="azure-key-vault-behind-a-firewall"></a>Cofre de chave Azure atrás de uma firewall

Quando a encriptação estiver a ser ativada com [credenciais AD AZure,](disk-encryption-windows-aad.md#)o VM alvo deve permitir a conectividade tanto para os pontos finais do Azure Ative Directory como para os pontos finais do Key Vault. Os pontos finais de autenticação do Azure Ative Directory são mantidos nas secções 56 e 59 da documentação dos [conjuntos de URLs e ip da Microsoft 365.](/microsoft-365/enterprise/urls-and-ip-address-ranges) As instruções do Cofre chave são fornecidas na documentação sobre como aceder ao [cofre da chave Azure atrás de uma firewall](../../key-vault/general/access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service 
O VM deve poder aceder ao ponto final do [serviço Azure Instance Metadata,](../windows/instance-metadata-service.md) que utiliza um endereço IP não-roteado bem conhecido `169.254.169.254` que só pode ser acedido a partir do VM.  As configurações proxy que alteram o tráfego HTTP local para este endereço (por exemplo, adicionar um cabeçalho X-Forwarded)não são suportadas.

## <a name="troubleshooting-windows-server-2016-server-core"></a>Resolução de problemas Do Núcleo do Servidor do Windows 2016

No Núcleo do Servidor 2016 do Windows Server, o componente bdehdcfg não está disponível por padrão. Este componente é requerido pela Encriptação do Disco Azure. É usado para dividir o volume do sistema do volume de SO, o que é feito apenas uma vez para o tempo de vida do VM. Estes binários não são necessários durante as operações de encriptação posteriores.

Para contornar este problema, copie os seguintes quatro ficheiros de um VM do Data Center 2016 do Windows Server para a mesma localização no Server Core:

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

1. Este comando cria uma divisória de sistema de 550-MB. Reinicie o sistema.

1. Utilize o DiskPart para verificar os volumes e, em seguida, proceda.  

Por exemplo:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```

## <a name="troubleshooting-encryption-status"></a>Estado de encriptação de resolução de problemas 

O portal pode apresentar um disco encriptado mesmo depois de ter sido desencriptado dentro do VM.  Isto pode ocorrer quando comandos de baixo nível são usados para desencriptar diretamente o disco a partir do VM, em vez de usar os comandos de gestão de encriptação de disco Azure de nível superior.  Os comandos de nível superior não só desencriptam o disco a partir do VM, mas também fora do VM, também atualizam importantes definições de encriptação de nível da plataforma e definições de extensão associadas ao VM.  Se estes não forem mantidos em alinhamento, a plataforma não será capaz de reportar o estado de encriptação ou a provisionação adequada do VM.

Para desativar a encriptação do disco Azure com PowerShell, utilize [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) seguido por [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Executar Remove-AzVMDiskEncryptionExtension antes de a encriptação ser desativada falhará.

Para desativar a encriptação do disco Azure com O CLI, utilize [desativação de encriptação az vm](/cli/azure/vm/encryption). 

## 



## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu mais sobre alguns problemas comuns na Encriptação do Disco Azure e como resolver esses problemas. Para obter mais informações sobre este serviço e as suas capacidades, consulte os seguintes artigos:

- [Aplicar encriptação de disco no Azure Security Center](../../security-center/asset-inventory.md)
- [Encriptação de dados inativa do Azure](../../security/fundamentals/encryption-atrest.md)