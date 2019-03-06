---
title: Resolver problemas de implementação do OpenShift no Azure | Documentos da Microsoft
description: Resolver problemas de implementação do OpenShift no Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/02/2019
ms.author: haroldw
ms.openlocfilehash: f94189e8f39771e0c3d7856ccbb8a23ba81d94aa
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57447279"
---
# <a name="troubleshoot-openshift-deployment-in-azure"></a>Resolver problemas de implementação do OpenShift no Azure

Se o cluster do OpenShift não implantar com êxito, o portal do Azure irá fornecer a saída de erro. A saída pode ser difícil de ler o que torna difícil identificar o problema. Analise rapidamente esta saída para o código de saída, 3, 4 ou 5. O seguinte fornece informações sobre esses códigos de saída de três:

- Código de saída 3: O nome de utilizador de subscrição do Red Hat / ID de organização ou de palavra-passe / chave de ativação está incorreto
- Código de saída 4: O ID de agrupamento do Red Hat estão incorreto ou existem não elegibilidades disponíveis
- Código de saída 5: Não é possível aprovisionar o Volume do agrupamento dinâmico de Docker

Para todos os outros códigos de saída, ligar para anfitriões de através de ssh para ver os ficheiros de registo.

**Plataforma de contentores do OpenShift**

SSH para o anfitrião do ansible playbook. Para o modelo ou a oferta do Marketplace, utilize o anfitrião de bastião. A partir de bastion, pode SSH para todos os outros nós do cluster (master, infra-estrutura, CNS, computação). Terá de ser a raiz para ver os ficheiros de registo. Raiz está desativada para acesso SSH por predefinição, portanto, não use raiz para encaminhar o SSH para outros nós.

**OKD**

SSH para o anfitrião do ansible playbook. Para o modelo OKD (versão 3.9 e anterior), utilize o anfitrião de mestre de 0. Para o modelo OKD (versão 3.10 e posterior), utilize o anfitrião de bastião. A partir do anfitrião de playbook ansible, pode SSH para todos os outros nós do cluster (master, infra-estrutura, CNS, computação). Terá de ser a raiz (sudo su-) para ver os ficheiros de registo. Raiz está desativada para acesso SSH por predefinição, portanto, não use raiz para encaminhar o SSH para outros nós.

## <a name="log-files"></a>Ficheiros de registo

Os ficheiros de registo (stderr e stdout) para os scripts de preparação do anfitrião estão localizados no /var/lib/waagent/custom-script/download/0 em todos os anfitriões. Se Ocorreu um erro durante a preparação do anfitrião, veja estes ficheiros de registo para determinar o erro.

Se os scripts de preparação foi executado com êxito, em seguida, os ficheiros de registo no diretório /var/lib/waagent/custom-script/download/1 do anfitrião de playbook ansible precisará ser examinado. Se o erro ocorreu durante a instalação real do OpenShift, o ficheiro de stdout exibirá o erro. Utilize estas informações para contactar o suporte para obter mais assistência.

Exemplo de saída

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

1. Chave privada tem a frase de acesso
2. Segredo do Cofre de chaves com a chave privada não foi criado corretamente
3. As credenciais de principal de serviço foram introduzidas incorretamente
4. Principal de serviço não tem acesso de Contribuidor ao grupo de recursos

### <a name="private-key-has-a-passphrase"></a>Chave privada tem uma frase de acesso

Verá um erro que a permissão foi negada para SSH. SSH para o anfitrião de manual de comunicação do ansible para verificar a existência de uma frase de acesso a chave privada.

### <a name="key-vault-secret-with-private-key-wasnt-created-correctly"></a>Segredo do Cofre de chaves com a chave privada não foi criado corretamente

A chave privada é injetada no anfitrião de playbook ansible - ~/.ssh/id_rsa. Certifique-se que este ficheiro está correto. Teste ao abrir uma sessão SSH para um de nós do cluster do anfitrião de playbook ansible.

### <a name="service-principal-credentials-were-entered-incorrectly"></a>As credenciais de principal de serviço foram introduzidas incorretamente

Ao fornecer a entrada para o modelo ou a oferta do Marketplace, as informações incorretas foi fornecidas. Certifique-se de que utilizar o appId correto (ID de cliente) e a palavra-passe (clientSecret) para o principal de serviço. Certifique-se ao emitir o seguinte comando da cli do azure.

```bash
az login --service-principal -u <client id> -p <client secret> -t <tenant id>
```

### <a name="service-principal-doesnt-have-contributor-access-to-the-resource-group"></a>Principal de serviço não tem acesso de Contribuidor ao grupo de recursos

Se o fornecedor de cloud do Azure estiver ativado, o principal de serviço utilizado tem de ter acesso de Contribuidor ao grupo de recursos. Certifique-se ao emitir o seguinte comando da cli do azure.

```bash
az group update -g <openshift resource group> --set tags.sptest=test
```

## <a name="additional-tools"></a>Ferramentas adicionais

Para alguns erros, também pode utilizar os seguintes comandos para obter mais informações:

1. Estado de systemctl <service>
2. journalctl -xe
