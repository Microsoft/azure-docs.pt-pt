---
title: Suporte de atesta com o ajudante de citação da Intel SGX DaemonSet no Azure (pré-visualização)
description: Um DaemonSet para gerar a cotação fora do processo de aplicação intel SGX. Este artigo explica como é fornecida a instalação de atestado fora de processo para cargas de trabalho confidenciais que funcionam dentro de um contentor.
ms.service: container-service
ms.subservice: confidential-computing
author: agowdamsft
ms.topic: overview
ms.date: 2/12/2021
ms.author: amgowda
ms.openlocfilehash: 849fd7afa3f9365f31ee8e03d9f9cc2174d64304
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484409"
---
# <a name="platform-software-management-with-intel-sgx-quote-helper-daemonset-preview"></a>Gestão de software de plataforma com o ajudante de cotação DaemonSet da Intel SGX (pré-visualização)

[As aplicações do enclave](confidential-computing-enclaves.md) que realizam atestado remoto requerem uma cotação gerada. Esta citação fornece uma prova criptográfica da identidade e do estado da aplicação, bem como o ambiente que o enclave está a executar. A geração da cotação requer componentes de software fidedignos que fazem parte dos Componentes de Software da Plataforma Intel (PSW).

## <a name="overview"></a>Descrição geral
 
A Intel suporta dois modos de atestado para executar a geração de citações:

- *Em processo* acolhe os componentes de software fidedignos dentro do processo de aplicação do enclave.

- *Fora do processo* acolhe os componentes de software fidedignos fora da aplicação do enclave.
 
As aplicações Intel Software Guard Extension (Intel SGX) construídas utilizando o Open Enclave SDK utilizam o modo de atestação em processo, por padrão. As aplicações baseadas em Intel SGX permitem o modo de atestação fora de processo. Se quiser utilizar este modo, precisa de hospedagem extra e precisa de expor os componentes necessários, como o Architectural Enclave Service Manager (AESM), externo à aplicação.

Esta funcionalidade melhora o tempo de atualização das suas aplicações do enclave durante as atualizações da plataforma Intel ou atualizações do controlador DCAP. Por esta razão, recomendamos a sua utilização.

Para ativar esta funcionalidade num cluster Azure Kubernetes Services (AKS), adicione o `--enable-sgxquotehelper` comando ao CLI Azure quando estiver a ativar o addon de computação confidencial. 

```azurecli-interactive
# Create a new AKS cluster with system node pool with Confidential Computing addon enabled and SGX Quote Helper
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom --enable-sgxquotehelper
```

Para obter mais informações, consulte [Quickstart: Implemente um cluster AKS com nós de computação confidencial utilizando o Azure CLI](confidential-nodes-aks-get-started.md).

## <a name="benefits-of-the-out-of-process-mode"></a>Benefícios do modo fora do processo

A lista a seguir descreve alguns dos principais benefícios deste modo de atestado:

-   Não são necessárias atualizações para os componentes de geração de cotações de PSW para cada aplicação contentorizada. Os proprietários de contentores não precisam de gerir atualizações dentro do seu contentor. Em vez disso, os proprietários de contentores confiam na interface do fornecedor que invoca o serviço centralizado fora do contentor. O fornecedor atualiza e gere o recipiente.

-   Não precisas de te preocupar com falhas de atestado devido a componentes de PSW desatualizados. O fornecedor gere as atualizações destes componentes.

-   O modo fora do processo proporciona uma melhor utilização da memória EPC do que o modo em processo. No modo em curso, cada aplicação do enclave precisa de instantaneamente a cópia de QE e PCE para atestado remoto. Em modo fora do processo, não há necessidade de o contentor acolher esses enclaves, e, portanto, não consome memória do enclave da quota do contentor.

-   Quando você a montante do controlador Intel SGX para um kernel Linux, há uma aplicação para um enclave ter maior privilégio. Este privilégio permite ao enclave invocar o PCE, que quebrará a aplicação do enclave em modo de processo. Por defeito, os enclaves não obtêm esta permissão. Conceder este privilégio a uma aplicação de enclave requer alterações ao processo de instalação da aplicação. Em contrapartida, no modo fora de processo, o prestador do serviço que trata de pedidos fora do processo garante que o serviço é instalado com este privilégio.

-   Não é preciso verificar a retrocompatibilidade com PSW e DCAP. As atualizações aos componentes de geração de cotação de PSW são validadas para retrocompatibilidade pelo fornecedor antes da atualização. Isto ajuda-o a lidar com problemas de compatibilidade antes de implementar atualizações para cargas de trabalho confidenciais.

## <a name="confidential-workloads"></a>Cargas de trabalho confidenciais

O solicitador de citação e a geração de citações funcionam separadamente, mas na mesma máquina física. A geração de citações é centralizada e serve pedidos de cotações de todas as entidades. Para que qualquer entidade solicite cotações, a interface tem de ser devidamente definida e detetável.

![Diagrama mostrando as relações entre o solicitador de citações, geração de citações e interface.](./media/confidential-nodes-out-of-proc-attestation/aesmmanager.png)

Este modelo abstrato aplica-se ao cenário de carga de trabalho confidencial, aproveitando o serviço AESM que já está disponível. O AESM é contentorizado e implantado como Um DaemonSet através do cluster Kubernetes. Kubernetes garante uma única instância de um recipiente de serviço AESM, embrulhado numa vagem, a ser implantado em cada nó de agente. A nova citação da Intel SGX, a DaemonSet, terá uma dependência do daemonSet de plugin sgx-device, porque o contentor de serviço AESM solicita a memória EPC do plugin de dispositivos SGX para o lançamento de enclaves QE e PCE.

Cada recipiente tem de optar por utilizar a geração de citações fora do processo, definindo a variável ambiental durante a `SGX_AESM_ADDR=1` criação. O recipiente também deve incluir a embalagem, libsgx-quote-ex, que é responsável por direcionar o pedido para a tomada de domínio Unix padrão.

Uma aplicação ainda pode usar o atestado em processo como antes, mas em processo e fora de processo não pode ser usado simultaneamente dentro de uma aplicação. A infraestrutura fora do processo está disponível por padrão e consome recursos.

## <a name="sample-implementation"></a>Implementação da amostra

O ficheiro Docker que se segue é uma amostra para uma aplicação baseada no Open Enclave. Defina a `SGX_AESM_ADDR=1` variável ambiente no ficheiro Docker ou definindo-a no ficheiro de implantação. A amostra que se segue fornece detalhes para o ficheiro e implantação do Docker. 

  > [!Note] 
  > Para que o atestado fora do processo funcione corretamente, o libsgx-quote-ex da Intel precisa de ser embalado no recipiente de aplicação.
    
```yaml
# Refer to Intel_SGX_Installation_Guide_Linux for details
FROM ubuntu:18.04 as sgx_base
RUN apt-get update && apt-get install -y \
    wget \
    gnupg

# Add the repository to sources, and add the key to the list of
# trusted keys used by the apt to authenticate packages
RUN echo "deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main" | tee /etc/apt/sources.list.d/intel-sgx.list \
    && wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | apt-key add -
# Add Microsoft repo for az-dcap-client
RUN echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/18.04/prod bionic main" | tee /etc/apt/sources.list.d/msprod.list \
    && wget -qO - https://packages.microsoft.com/keys/microsoft.asc | apt-key add -

FROM sgx_base as sgx_sample
RUN apt-get update && apt-get install -y \
    clang-7 \
    libssl-dev \
    gdb \
    libprotobuf10 \
    libsgx-dcap-ql \
    libsgx-quote-ex \
    az-dcap-client \
    open-enclave
WORKDIR /opt/openenclave/share/openenclave/samples/remote_attestation
RUN . /opt/openenclave/share/openenclave/openenclaverc \
    && make build
# This sets the flag for out-of-process attestation mode. Alternatively you can set this flag on the deployment files.
ENV SGX_AESM_ADDR=1 

CMD make run
```
Em alternativa, pode definir o modo de atestado fora de processo no ficheiro .yaml de implementação. Eis como:

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
spec:
  template:
    spec:
      containers:
      - name: sgxtest
        image: <registry>/<repository>:<version>
        env:
        - name: SGX_AESM_ADDR
          value: 1
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10
        volumeMounts:
        - name: var-run-aesmd
          mountPath: /var/run/aesmd
      restartPolicy: "Never"
      volumes:
      - name: var-run-aesmd
        hostPath:
          path: /var/run/aesmd
```

## <a name="next-steps"></a>Passos seguintes

[Quickstart: Implementar um cluster AKS com nódoas de computação confidencial utilizando o Azure CLI](./confidential-nodes-aks-get-started.md)

[Amostras de arranque rápido recipientes confidenciais](https://github.com/Azure-Samples/confidential-container-samples)

[DCsv2 SKUs](../virtual-machines/dcv2-series.md)
