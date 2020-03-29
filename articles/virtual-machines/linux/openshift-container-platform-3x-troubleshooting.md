---
title: Implantação da Plataforma de Contentores OpenShift 3.11 em Azure
description: Implantação da Plataforma de Contentores OpenShift 3.11 em Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: bd83a1ca731d81edb76a3c1bc07113ce96adb9ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066595"
---
# <a name="troubleshoot-openshift-container-platform-311-deployment-in-azure"></a>Implantação da Plataforma de Contentores OpenShift 3.11 em Azure

Se o cluster OpenShift não for implantado com sucesso, o portal Azure fornecerá uma saída de erro. A saída pode ser difícil de ler, o que dificulta a identificação do problema. Scaneie rapidamente esta saída para obter os códigos de saída 3, 4 ou 5. O seguinte fornece informações sobre estes três códigos de saída:

- Código de saída 3: O nome do utilizador da subscrição do chapéu vermelho / Palavra-passe ou id da organização / Chave de ativação está incorreto
- Código de saída 4: O ID do seu Pool De Chapéu Vermelho está incorreto ou não existem direitos disponíveis
- Código de saída 5: Incapaz de fornecer Docker Thin Pool Volume

Para todos os outros códigos de saída, ligue-se ao(s) hospedeiro através do SSH para visualizar os ficheiros de registo.

**OpenShift Container Platform 3.11**

SSH para o anfitrião de playbook ansible. Para o modelo ou a oferta do Marketplace, use o anfitrião do bastião. Do bastião, pode SSH a todos os outros nós do cluster (mestre, infra, CNS, computação). Tens de estar na raiz para ver os ficheiros de registo. A raiz é desativada para acesso ssh por padrão, por isso não use raiz para SSH para outros nódosos.

**OKD**

SSH para o anfitrião de playbook ansible. Para o modelo OKD (versão 3.9 e anterior), utilize o anfitrião master-0. Para o modelo OKD (versão 3.10 e mais tarde), utilize o anfitrião do bastião. Desde o hospedeiro de playbook ansible, você pode SSH a todos os outros nós do cluster (mestre, infra, CNS, computação). Terá de ser raiz (sudo su -) para ver os ficheiros de registo. A raiz é desativada para acesso ssh por padrão, por isso não use raiz para SSH para outros nódosos.

## <a name="log-files"></a>Ficheiros de registo

Os ficheiros de registo (stderr e stdout) `/var/lib/waagent/custom-script/download/0` para os scripts de preparação do anfitrião estão localizados em todos os anfitriões. Se ocorreu um erro durante a preparação do hospedeiro, consulte estes ficheiros de registo para determinar o erro.

Se os scripts de preparação forem `/var/lib/waagent/custom-script/download/1` bem sucedidos, então os ficheiros de registo no diretório do hospedeiro de playbook ansible terão de ser examinados. Se o erro ocorrer durante a instalação real do OpenShift, o ficheiro stdout mostrará o erro. Utilize estas informações para contactar o Suporte para obter mais assistência.

Saída de exemplo

```json
TASK [openshift_storage_glusterfs : Load heketi topology] **********************
fatal: [mycluster-master-0]: FAILED! => {"changed": true, "cmd": ["oc", "--config=/tmp/openshift-glusterfs-ansible-IbhnUM/admin.kubeconfig", "rsh", "--namespace=glusterfs", "deploy-heketi-storage-1-d9xl5", "heketi-cli", "-s", "http://localhost:8080", "--user", "admin", "--secret", "VuoJURT0/96E42Vv8+XHfsFpSS8R20rH1OiMs3OqARQ=", "topology", "load", "--json=/tmp/openshift-glusterfs-ansible-IbhnUM/topology.json", "2>&1"], "delta": "0:00:21.477831", "end": "2018-05-20 02:49:11.912899", "failed": true, "failed_when_result": true, "rc": 0, "start": "2018-05-20 02:48:50.435068", "stderr": "", "stderr_lines": [], "stdout": "Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2\n\tAllowing file volumes on cluster.\n\tAllowing block volumes on cluster.\n\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4\n\t\tAdding device /dev/sdd ... OK\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131\n\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdd ... OK\n\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\t\tAdding device /dev/sdd ... OK", "stdout_lines": ["Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2", "\tAllowing file volumes on cluster.", "\tAllowing block volumes on cluster.", "\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4", "\t\tAdding device /dev/sdd ... OK", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131", "\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdd ... OK", "\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\t\tAdding device /dev/sdd ... OK"]}

PLAY RECAP *********************************************************************
mycluster-cns-0       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-1       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-2       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-infra-0     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-1     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-2     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-master-0    : ok=502  changed=198  unreachable=0    failed=1   
mycluster-master-1    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-master-2    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-node-0      : ok=143  changed=55   unreachable=0    failed=0   
mycluster-node-1      : ok=143  changed=55   unreachable=0    failed=0   
localhost                  : ok=13   changed=0    unreachable=0    failed=0   

INSTALLER STATUS ***************************************************************
Initialization             : Complete (0:00:39)
Health Check               : Complete (0:00:24)
etcd Install               : Complete (0:01:24)
Master Install             : Complete (0:14:59)
Master Additional Install  : Complete (0:01:10)
Node Install               : Complete (0:10:58)
GlusterFS Install          : In Progress (0:03:33)
    This phase can be restarted by running: playbooks/openshift-glusterfs/config.yml

Failure summary:

  1. Hosts:    mycluster-master-0
     Play:     Configure GlusterFS
     Task:     Load heketi topology
     Message:  Failed without returning a message.
```

Os erros mais comuns durante a instalação são:

1. Chave privada tem palavra-passe
2. O segredo do cofre com chave privada não foi criado corretamente
3. As credenciais principais do serviço foram inseridas incorretamente
4. Diretor de serviço não tem acesso contributivo ao grupo de recursos

### <a name="private-key-has-a-passphrase"></a>Private Key tem uma frase de passe

Verá um erro que a permissão foi negada por SSH. ssh ao anfitrião de livro ansível para verificar se há uma palavra-passe na chave privada.

### <a name="key-vault-secret-with-private-key-wasnt-created-correctly"></a>O segredo do cofre com chave privada não foi criado corretamente

A chave privada é copiada para o hospedeiro ansible playbook - ~/.ssh/id_rsa. Confirme que este ficheiro está correto. Teste abrindo uma sessão de SSH a um dos nós de cluster do hospedeiro de playbook ansible.

### <a name="service-principal-credentials-were-entered-incorrectly"></a>As credenciais principais do serviço foram inseridas incorretamente

Ao fornecer a entrada para o modelo ou oferta do Marketplace, as informações incorretas foram fornecidas. Certifique-se de que utiliza o appId correto (clientId) e a palavra-passe (clientSecret) para o diretor de serviço. Verifique emitindo o seguinte comando azure cli.

```azurecli
az login --service-principal -u <client id> -p <client secret> -t <tenant id>
```

### <a name="service-principal-doesnt-have-contributor-access-to-the-resource-group"></a>Diretor de serviço não tem acesso contributivo ao grupo de recursos

Se o fornecedor de nuvem Azure estiver ativado, então o principal de serviço utilizado deve ter acesso ao grupo de recursos. Verifique emitindo o seguinte comando azure cli.

```azurecli
az group update -g <openshift resource group> --set tags.sptest=test
```

## <a name="additional-tools"></a>Ferramentas adicionais

Para alguns erros, também pode utilizar os seguintes comandos para obter mais informações:

1. serviço de \<estado sistemactl>
2. journalctl -xe
