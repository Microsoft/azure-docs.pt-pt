---
title: Resolução de resolução de resolução de porta-contentores openshift plataforma 3.11 implantação em Azure
description: Resolução de resolução de resolução Da Plataforma de Contentores OpenShift 3.11 em Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.custom: devx-track-ansible
ms.openlocfilehash: b6dfb4cb697e7d66c7db323df9786ff7ad45e1d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101672329"
---
# <a name="troubleshoot-openshift-container-platform-311-deployment-in-azure"></a>Resolução de resolução de resolução de porta-contentores openshift plataforma 3.11 implantação em Azure

Se o cluster OpenShift não se desdobrar com sucesso, o portal Azure fornecerá saída de erro. A saída pode ser difícil de ler o que dificulta a identificação do problema. Verifique rapidamente esta saída para o código de saída 3, 4 ou 5. O seguinte fornece informações sobre estes três códigos de saída:

- Código de saída 3: O nome de utilizador da subscrição do chapéu vermelho / palavra-passe ou iD da organização / Chave de ativação está incorreta
- Código de saída 4: O seu ID da Piscina do Chapéu Vermelho está incorreto ou não há direitos disponíveis
- Código de saída 5: Incapaz de providenciar o volume de piscinas estivadores

Para todos os outros códigos de saída, ligue-se ao(s) anfitrião(s) através do ssh para visualizar os ficheiros de registo.

**OpenShift Container Platform 3.11**

SSH para o anfitrião de um livro de jogadas ansível. Para o modelo ou a oferta do Marketplace, use o anfitrião do bastião. Desde o bastião, pode SSH a todos os outros nós do cluster (master, infra, CNS, compute). Terá de ser raiz para ver os ficheiros de registo. Root é desativado para acesso SSH por padrão, por isso não use raiz para SSH para outros nós.

**OKD**

SSH para o anfitrião de um livro de jogadas ansível. Para o modelo OKD (versão 3.9 e anterior), utilize o anfitrião master-0. Para o modelo OKD (versão 3.10 e posterior), utilize o anfitrião do bastião. Do anfitrião de playbook ansível, você pode SSH a todos os outros nós no cluster (master, infra, CNS, compute). Terá de ser raiz (sudo su -) para visualizar os ficheiros de registo. Root é desativado para acesso SSH por padrão, por isso não use raiz para SSH para outros nós.

## <a name="log-files"></a>Ficheiros de registo

Os ficheiros de registo (stderr e stdout) para os scripts de preparação do anfitrião estão localizados em `/var/lib/waagent/custom-script/download/0` todos os anfitriões. Se ocorrer um erro durante a preparação do anfitrião, consulte estes ficheiros de registo para determinar o erro.

Se os scripts de preparação funcionarem com sucesso, os ficheiros de registo no `/var/lib/waagent/custom-script/download/1` diretório do anfitrião de playbook ansível terão de ser examinados. Se o erro ocorreu durante a instalação real do OpenShift, o ficheiro de sestout mostrará o erro. Utilize estas informações para contactar o Support para mais assistência.

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
2. Segredo chave do cofre com chave privada não foi criado corretamente
3. As credenciais principais do serviço foram inseridas incorretamente
4. Diretor de serviço não tem acesso ao grupo de recursos

### <a name="private-key-has-a-passphrase"></a>Private Key tem uma palavra-passe

Verá um erro que foi negado permissão para o SSH. ssh para o anfitrião de livro de jogadas ansível para verificar se há uma palavra-passe na chave privada.

### <a name="key-vault-secret-with-private-key-wasnt-created-correctly"></a>Segredo chave do cofre com chave privada não foi criado corretamente

A chave privada é copiada para o anfitrião ansível de playbook - ~/.ssh/id_rsa. Confirme que este ficheiro está correto. Teste abrindo uma sessão de SSH a um dos nós do cluster do anfitrião de playbook ansível.

### <a name="service-principal-credentials-were-entered-incorrectly"></a>As credenciais principais do serviço foram inseridas incorretamente

Ao fornecer a entrada para a oferta do modelo ou do Marketplace, foram fornecidas informações incorretas. Certifique-se de que utiliza o appId correto (clientId) e a palavra-passe (clientSecret) para o principal serviço. Verifique através da emissão do seguinte comando azure cli.

```azurecli
az login --service-principal -u <client id> -p <client secret> -t <tenant id>
```

### <a name="service-principal-doesnt-have-contributor-access-to-the-resource-group"></a>Diretor de serviço não tem acesso ao grupo de recursos

Se o fornecedor de nuvem Azure estiver ativado, o principal serviço utilizado deve ter acesso ao grupo de recursos. Verifique através da emissão do seguinte comando azure cli.

```azurecli
az group update -g <openshift resource group> --set tags.sptest=test
```

## <a name="additional-tools"></a>Ferramentas adicionais

Para alguns erros, também pode utilizar os seguintes comandos para obter mais informações:

1. estado sistemactl \<service>
2. journalctl -xe
