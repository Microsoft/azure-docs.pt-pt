---
title: Solucionar problemas de implantação do OpenShift no Azure | Microsoft Docs
description: Solucionar problemas de implantação do OpenShift no Azure.
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
ms.date: 04/19/2019
ms.author: haroldw
ms.openlocfilehash: 31512bb264b5e998e5b6adc76d37c82c174933be
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70091709"
---
# <a name="troubleshoot-openshift-deployment-in-azure"></a>Solucionar problemas de implantação do OpenShift no Azure

Se o cluster OpenShift não for implantado com êxito, o portal do Azure fornecerá a saída de erro. Talvez seja difícil ler a saída, o que dificulta a identificação do problema. Examine rapidamente essa saída para o código de saída 3, 4 ou 5. O seguinte fornece informações sobre esses três códigos de saída:

- Código de saída 3: O nome de usuário/senha da sua assinatura do Red Hat ou a ID da organização/chave de ativação está incorreta
- Código de saída 4: A ID do pool Red Hat está incorreta ou não há direitos disponíveis
- Código de saída 5: Não é possível provisionar o volume do pool fino do Docker

Para todos os outros códigos de saída, conecte-se aos hosts via SSH para exibir os arquivos de log.

**Plataforma de contêiner OpenShift**

SSH para o host do manual do Ansible. Para o modelo ou a oferta do Marketplace, use o host de bastiões. Da bastiões, você pode SSH para todos os outros nós no cluster (Mestre, infraestrutura, CNS, computação). Você precisará ser raiz para exibir os arquivos de log. A raiz é desabilitada para acesso SSH por padrão, portanto, não use root para SSH para outros nós.

**OKD**

SSH para o host do manual do Ansible. Para o modelo OKD (versão 3,9 e anterior), use o host Master-0. Para o modelo OKD (versão 3,10 e posterior), use o host bastião. No host do manual do Ansible, você pode fazer SSH para todos os outros nós no cluster (Mestre, infraestrutura, CNS, computação). Você precisará ser root (sudo su-) para exibir os arquivos de log. A raiz é desabilitada para acesso SSH por padrão, portanto, não use root para SSH para outros nós.

## <a name="log-files"></a>Ficheiros de registo

Os arquivos de log (stderr e stdout) para os scripts de preparação do host `/var/lib/waagent/custom-script/download/0` estão localizados em em todos os hosts. Se ocorrer um erro durante a preparação do host, exiba esses arquivos de log para determinar o erro.

Se os scripts de preparação forem executados com êxito, os arquivos de log `/var/lib/waagent/custom-script/download/1` no diretório do host do guia estratégico Ansible precisarão ser examinados. Se o erro ocorreu durante a instalação real do OpenShift, o arquivo stdout exibirá o erro. Use essas informações para entrar em contato com o suporte para obter mais assistência.

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

1. A chave privada tem senha
2. O segredo do cofre de chaves com a chave privada não foi criado corretamente
3. As credenciais da entidade de serviço foram inseridas incorretamente
4. A entidade de serviço não tem acesso de colaborador ao grupo de recursos

### <a name="private-key-has-a-passphrase"></a>A chave privada tem uma frase secreta

Você verá um erro de que a permissão foi negada para SSH. SSH para o host do manual do Ansible para verificar uma frase secreta na chave privada.

### <a name="key-vault-secret-with-private-key-wasnt-created-correctly"></a>O segredo do cofre de chaves com a chave privada não foi criado corretamente

A chave privada é copiada no host do guia estratégico Ansible-~/.ssh/id_rsa. Confirme se este arquivo está correto. Teste abrindo uma sessão SSH para um dos nós de cluster do host do guia estratégico Ansible.

### <a name="service-principal-credentials-were-entered-incorrectly"></a>As credenciais da entidade de serviço foram inseridas incorretamente

Ao fornecer a entrada para a oferta de modelo ou Marketplace, as informações incorretas foram fornecidas. Certifique-se de usar a appId (clientId) e a senha (clientSecret) corretas para a entidade de serviço. Verifique emitindo o comando da CLI do Azure a seguir.

```bash
az login --service-principal -u <client id> -p <client secret> -t <tenant id>
```

### <a name="service-principal-doesnt-have-contributor-access-to-the-resource-group"></a>A entidade de serviço não tem acesso de colaborador ao grupo de recursos

Se o provedor de nuvem do Azure estiver habilitado, a entidade de serviço usada deve ter acesso de colaborador ao grupo de recursos. Verifique emitindo o comando da CLI do Azure a seguir.

```bash
az group update -g <openshift resource group> --set tags.sptest=test
```

## <a name="additional-tools"></a>Ferramentas adicionais

Para alguns erros, você também pode usar os seguintes comandos para obter mais informações:

1. > de \<serviço de status systemctl
2. journalctl-xe
