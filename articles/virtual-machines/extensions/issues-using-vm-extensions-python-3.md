---
title: Problemas com extensões VM em sistemas de máquinas virtuais Linux Azure ativados por Python 3
description: Saiba mais sobre a utilização de extensões VM em sistemas Linux com 3 ativados python
services: virtual-machines
documentationcenter: ''
author: v-miegge
ms.author: jparrel
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-resource-manager
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/25/2020
ms.assetid: 3cd520fd-eaf7-4ef9-b4d3-4827057e5028
ms.openlocfilehash: d38b41bc47c01b01fcbb1b05238396dca6d8eb4e
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558649"
---
# <a name="issues-using-vm-extensions-in-python-3-enabled-linux-azure-virtual-machines-systems"></a>Problemas com extensões VM em sistemas de máquinas virtuais Linux Azure ativados por Python 3

> [!NOTE]
> A Microsoft encoraja os utilizadores a adotarem **python 3.x** nos seus sistemas, a menos que a sua carga de trabalho exija suporte **Python 2.x.** Exemplos deste requisito podem incluir scripts de administração antigas, ou extensões como **Azure Disk Encryption** e **Azure Monitor**.
>
> Antes de instalar o **Python 2.x** em produção, considere a questão do suporte a longo prazo do Python 2.x, particularmente a sua capacidade de receber atualizações de segurança. Como produtos, incluindo algumas das extensões mencionadas, atualização com suporte **Python 3.8,** você deve interromper a utilização de Python 2.x.

Algumas distribuições de Linux transitaram para Python 3.8 e removeram completamente o `/usr/bin/python` ponto de entrada para Python. Esta transição tem impacto na implantação automatizada de certas extensões de máquinas virtuais (VM) com estas duas condições:

- Extensões que ainda estão em transição para suporte Python 3.x
- Extensões que usam o ponto de entrada do legado `/usr/bin/python`

Os utilizadores de distribuição Linux que tenham transitado para **Python 3.x** devem garantir que o ponto de entrada do legado `/usr/bin/python` existe antes de tentar implementar essas extensões nos seus VMs. Caso contrário, a extensão pode falhar. 

- As distribuições de Linux endossadas que são afetadas incluem **ubuntu Server 20.04 LTS** e **Ubuntu Pro 20.04 LTS**.

- As extensões VM afetadas incluem **encriptação do disco Azure,** **Log Analytics,** **Acesso VM** (usado para reset de password) e **Diagnóstico de Hóspedes** (usado para contadores de desempenho adicionais).

As atualizações no local, tais como a atualização de **Ubuntu 18.04 LTS** para **Ubuntu 20.04 LTS,** devem manter o `/usr/bin/python` symlink e permanecer inalteradas.

## <a name="resolution"></a>Resolução

Considere estas recomendações gerais antes de implementar extensões nos cenários conhecidos descritos anteriormente no Resumo:

1. Antes de implementar a extensão, reponha o `/usr/bin/python` symlink utilizando o método fornecido pelo fornecedor de distribuição Linux.

   - Por exemplo, para **Python 2.7**, utilize: `sudo apt update && sudo apt install python-is-python2`

1. Esta recomendação destina-se a clientes Azure e não é suportada na Azure Stack:

   - Se já implementou um caso que exibe este problema, utilize a funcionalidade de comando Executar na lâmina VM para executar os comandos acima mencionados. A extensão do comando Run em si não é afetada pela transição para Python 3.8.

1. Se estiver a implementar uma nova instância e precisar de definir uma extensão no tempo de fornecimento, utilize dados do utilizador **em nuvem** para instalar os pacotes acima mencionados.

   Por exemplo, para Python 2.7:

   ```python
   # create cloud-init config
   cat > cloudinitConfig.json <<EOF
   #cloud-config
   package_update: true
    
   runcmd:
   - sudo apt update
   - sudo apt install python-is-python2 
   EOF

   # create VM
   az vm create \
       --resource-group <resourceGroupName> \
       --name <vmName> \
       --image <Ubuntu 20.04 Image URN> \
       --admin-username azadmin \
       --ssh-key-value "<sshPubKey>" \
       --custom-data ./cloudinitConfig.json
   ```

1. Se os administradores de política da sua organização determinarem que as extensões não devem ser implementadas em VMs, pode desativar o suporte de extensão no tempo de provisionamento:

   - API REST

     Para desativar e ativar extensões quando pode implementar um VM com esta propriedade:

     ```python
       "osProfile": {
         "allowExtensionOperations": false
       },
     ```

## <a name="next-steps"></a>Passos seguintes

Consulte [outras alterações do sistema base desde 18.04 LTS - Python 3 por padrão](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes#Python3_by_default) para obter informações adicionais.
