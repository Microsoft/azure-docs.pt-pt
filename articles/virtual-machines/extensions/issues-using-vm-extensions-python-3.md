---
title: Problemas que utilizam extensões VM em sistemas de máquinas virtuais Linux Azure ativados por Python 3
description: Saiba utilizar extensões VM em sistemas Linux com 3 habilitações Python
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
ms.author: jparrel
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.assetid: 3cd520fd-eaf7-4ef9-b4d3-4827057e5028
ms.openlocfilehash: 944abc62f25473ea52836af7dc1fdcd1e16d9269
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82120785"
---
# <a name="issues-using-vm-extensions-in-python-3-enabled-linux-azure-virtual-machines-systems"></a>Problemas que utilizam extensões VM em sistemas de máquinas virtuais Linux Azure ativados por Python 3

> [!NOTE]
> A Microsoft encoraja os utilizadores a adotar **python 3.x** nos seus sistemas, a menos que a sua carga de trabalho exija suporte **Python 2.x.** Exemplos deste requisito podem incluir scripts de administração legado, ou extensões como encriptação de **disco azure** e **Monitor Azure**.
>
> Antes de instalar **python 2.x** em produção, considere a questão do suporte a longo prazo da Python 2.x, particularmente a sua capacidade de receber atualizações de segurança. Como produtos, incluindo algumas das extensões mencionadas, atualizar com suporte **Python 3.8,** você deve interromper a utilização de Python 2.x.

Algumas distribuições linux transitaram para Python 3.8 e removeram completamente o ponto de entrada legado `/usr/bin/python` para python. Esta transição afeta a implantação automatizada de determinadas extensões de máquinas virtuais (VM) com as seguintes condições:

- Extensões que ainda estão em transição para suporte Python 3.x
- Extensões que usam `/usr/bin/python` o ponto de entrada legado

Os utilizadores de distribuição Linux que tenham transitado para **Python 3.x** devem garantir que o ponto de entrada do legado `/usr/bin/python` existe antes de tentar implementar essas extensões para os seus VMs. Caso contrário, a extensão pode falhar. 

- As distribuições linux endossadas que são afetadas incluem **ubuntu Server 20.04 LTS** e **Ubuntu Pro 20.04 LTS**.

- As extensões VM afetadas incluem encriptação de **disco azure,** **Log Analytics,** **Acesso VM** (usado para reset de palavra-passe) e **Diagnóstico de Hóspedes** (usado para contadores de desempenho adicionais).

As atualizações em vigor, tais como a atualização de **Ubuntu 18.04 LTS** para `/usr/bin/python` **Ubuntu 20.04 LTS,** devem manter o elo e permanecer inalterados.

## <a name="resolution"></a>Resolução

Considere as seguintes recomendações gerais antes de implementar extensões nos cenários conhecidos descritos anteriormente no resumo:

1.  Antes de implementar a `/usr/bin/python` extensão, reponha o symlink utilizando o método fornecido pelo fornecedor de distribuição Linux.

    - Por exemplo, para **Python 2.7,** use:`sudo apt update && sudo apt install python-is-python2`

2.  Se já implementou uma instância que exiba este problema, utilize a funcionalidade de **comando Run** na **lâmina VM** para executar os comandos acima mencionados. A extensão do comando Run em si não é afetada pela transição para Python 3.8.

3.  Se estiver a implementar uma nova instância e precisar de definir uma extensão no tempo de fornecimento, utilize dados de utilizadores **inite na nuvem** para instalar os pacotes acima mencionados.

    Por exemplo, para Python 2.7:

    ```
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

4.  Se os administradores de política da sua organização determinarem que as extensões não devem ser implementadas em VMs, pode desativar o suporte de extensão no momento do fornecimento:

    - API REST

      Para desativar e ativar extensões quando pode implementar um VM com esta propriedade:

      ```
        "osProfile": {
          "allowExtensionOperations": false
        },
      ```

## <a name="next-steps"></a>Passos seguintes

Consulte [outras alterações do sistema base desde 18.04 LTS - Python 3 por defeito](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes#Python3_by_default) para obter informações adicionais.
