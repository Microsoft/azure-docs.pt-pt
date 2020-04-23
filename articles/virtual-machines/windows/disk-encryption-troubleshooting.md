---
title: Guia de resolução de problemas de encriptação de disco azure
description: Este artigo fornece dicas de resolução de problemas para encriptação do disco Microsoft Azure para VMs do Windows.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 11c1e0bf10725173a2a341addf4c3f845bbb7fba
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085693"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Guia de resolução de problemas de encriptação de disco azure

Este guia é para profissionais de TI, analistas de segurança da informação e administradores de nuvem cujas organizações usam encriptação de disco azure. Este artigo é para ajudar na resolução de problemas relacionados com a encriptação do disco.

Antes de dar qualquer um dos passos abaixo, certifique-se primeiro de que os VMs que está a tentar encriptar estão entre os [tamanhos de VM suportados e sistemas operativos,](disk-encryption-overview.md#supported-vms-and-operating-systems)e que preencheu todos os pré-requisitos:

- [Requisitos de networking](disk-encryption-overview.md#networking-requirements)
- [Requisitos de política de grupo](disk-encryption-overview.md#group-policy-requirements)
- [Requisitos de armazenamento de chaves de encriptação](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Resolução de problemas da encriptação do disco azure atrás de uma firewall

Quando a conectividade é restringida por uma firewall, requisito de procuração ou configurações de grupo de segurança de rede (NSG), a capacidade da extensão para executar as tarefas necessárias pode ser interrompida. Esta perturbação pode resultar em mensagens de estado como "Estado de extensão não disponível no VM". Em cenários esperados, a encriptação não termina. As secções que se seguem têm alguns problemas comuns de firewall que pode investigar.

### <a name="network-security-groups"></a>Grupos de segurança de rede
Quaisquer definições do grupo de segurança da rede que são aplicadas devem ainda permitir que o ponto final cumpra os [pré-requisitos](disk-encryption-overview.md#networking-requirements) de configuração da rede documentadopara a encriptação do disco.

### <a name="azure-key-vault-behind-a-firewall"></a>Cofre chave azure atrás de uma firewall

Quando a encriptação está a ser ativada com [credenciais De AD Azure,](disk-encryption-windows-aad.md#)o VM alvo deve permitir a conectividade tanto aos pontos finais do Diretório Ativo Azure como aos pontos finais do Cofre chave. Os pontos finais de autenticação do Diretório Ativo Azure atuais são mantidos nas secções 56 e 59 do [Office 365 URLs e](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) documentação de endereços IP. As instruções do cofre chave são fornecidas na documentação sobre como aceder ao [Cofre chave Azure atrás de uma firewall](../../key-vault/general/access-behind-firewall.md).

### <a name="azure-instance-metadata-service"></a>Serviço de Metadados de Instância Azure 
O VM deve poder aceder ao ponto final do [serviço de metadados de instância](../windows/instance-metadata-service.md) `169.254.169.254`seletiva, que utiliza um endereço IP não resaída conhecido ( ) que só pode ser acedido a partir do VM.  As configurações proxy que alteram o tráfego local http para este endereço (por exemplo, adicionar um cabeçalho X-Forwarded-For) não são suportadas.

## <a name="troubleshooting-windows-server-2016-server-core"></a>Problemas de resolução do Núcleo do Servidor do Windows 2016

No Windows Server 2016 Server Core, o componente bdehdcfg não está disponível por padrão. Este componente é exigido pela Encriptação do Disco Azure. É usado para dividir o volume do sistema do volume de OS, que é feito apenas uma vez para o tempo de vida do VM. Estes binários não são necessários durante operações de encriptação posteriores.

Para resolver este problema, copie os seguintes quatro ficheiros de um VM do Windows Server 2016 para a mesma localização no Núcleo do Servidor:

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

1. Este comando cria uma partição do sistema de 550 MB. Reiniciar o sistema.

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

O portal pode apresentar um disco como encriptado mesmo depois de não ter sido encriptado dentro do VM.  Isto pode ocorrer quando comandos de baixo nível são usados para desencriptar diretamente o disco de dentro do VM, em vez de usar os comandos de gestão de encriptação de disco de nível superior.  Os comandos de nível superior não só desencriptam o disco a partir do VM, mas também fora do VM, também atualizam definições importantes de encriptação de nível de plataforma e definições de extensão associadas ao VM.  Se estes não forem mantidos em alinhamento, a plataforma não será capaz de reportar o estado de encriptação ou fornecer o VM corretamente.   

Para desativar a encriptação do disco Azure com powerShell, utilize a [Encriptação Desactivada-AzVMDisk,](/powershell/module/az.compute/disable-azvmdiskencryption) seguida de [Remover-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Executar Remover-AzVMDiskEncriptaçãoA Extensão antes da encriptação ser desativada falhará.

Para desativar a encriptação do disco Azure com CLI, utilize o [desativação de encriptação az vm](/cli/azure/vm/encryption). 

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu mais sobre alguns problemas comuns na Encriptação do Disco Azure e como resolver esses problemas. Para obter mais informações sobre este serviço e as suas capacidades, consulte os seguintes artigos:

- [Aplicar encriptação de disco no Centro de Segurança Azure](../../security-center/security-center-apply-disk-encryption.md)
- [Encriptação de dados azure em repouso](../../security/fundamentals/encryption-atrest.md)
