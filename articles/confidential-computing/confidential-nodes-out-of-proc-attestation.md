---
title: Suporte de atestado fora de proc com intel SGX quote helper DaemonSet on Azure
description: DaemonSet para gerar a cotação fora do processo de aplicação SGX. Este artigo explica como é fornecida a instalação de atestado fora de proc para cargas de trabalho confidenciais que correm dentro de um contentor.
ms.service: container-service
author: agowdamsft
ms.topic: overview
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: b79b3b40f3fbfe7d70550db3aaf7b365aa455e89
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94564162"
---
# <a name="platform-software-management-with-sgx-quote-helper-daemon-set"></a>Gestão de software de plataforma com conjunto de ajudante de cotação SGX

[As aplicações enclave que](confidential-computing-enclaves.md) executam o atestado remoto requer uma COTAÇÃO gerada. Esta CITAÇÃO fornece uma prova criptográfica da identidade e do estado da aplicação, bem como do ambiente que o enclave está a executar. A geração do QUOTE requer componentes de software fidedignos que fazem parte dos Componentes de Software de Plataforma da Intel (PSW).

## <a name="overview"></a>Descrição geral
 
A Intel suporta dois modos de atestado para executar a geração de citações:
- **in-proc** : acolhe os componentes de software fidedignos dentro do processo de aplicação do enclave

- **fora do proc** : hospeda os componentes de software fidedignos fora da aplicação do enclave.
 
Aplicações SGX construídas usando Open Enclave SDK por padrão uso no modo de atestado in-proc. As aplicações baseadas em SGX permitem o out-of-proc e exigiriam um alojamento extra e exporia os componentes necessários, como o Architectural Enclave Service Manager (AESM), externo à aplicação.

A utilização desta funcionalidade é **altamente recomendada** , uma vez que melhora o tempo de uptime para as suas aplicações do enclave durante as atualizações da Plataforma Intel ou atualizações do controlador DCAP.

## <a name="why-and-what-are-the-benefits-of-out-of-proc"></a>Porquê e quais são os benefícios de fora-de-proc?

-   Não são necessárias atualizações para os componentes de geração de cotações de PSW para cada aplicação contentorizada: Com fora de proc, os proprietários de contentores não precisam de gerir atualizações dentro do seu contentor. Em vez disso, os proprietários de contentores contam com a interface fornecida pelo fornecedor que invoca o serviço centralizado fora do contentor, que será atualizado e gerido pelo prestador.

-   Não há necessidade de se preocupar com falhas no atestado devido a componentes de PSW desatualizados: A geração de citações envolve os componentes SW confiáveis - Enclave de Citação (QE) & Certificado de Provisionamento Enclave (PCE), que fazem parte da base de computação fidedigna (TCB). Estes componentes SW devem estar atualizados para manter os requisitos de atestado. Uma vez que o fornecedor gere as atualizações destes componentes, os clientes nunca terão de lidar com falhas de atestado devido a componentes SW fidedignos desatualizados dentro do seu contentor.

-   Melhor utilização da memória EPC No modo de atestado in-proc, cada aplicação de enclave precisa de instantanear a cópia de QE e PCE para atestado remoto. Com fora-de-proc, não há necessidade de o recipiente acolher esses enclaves, e assim não consome a memória do enclave da quota do contentor.

-   Salvaguardas contra a aplicação do Kernel Quando o condutor da SGX for transmitido para o kernel de Linux, haverá aplicação para que um enclave tenha maior privilégio. Este privilégio permite ao enclave invocar o PCE, que quebrará a aplicação do enclave em modo in-proc. Por defeito, os enclaves não obtêm esta permissão. Conceder este privilégio a uma aplicação de enclave requer alterações ao processo de instalação da aplicação. Isto é manuseado facilmente para um modelo fora de proc, uma vez que o prestador do serviço que trata de pedidos fora de proc irá certificar-se de que o serviço está instalado com este privilégio.

-   Não há necessidade de verificar a retrocompatibilidade com a PSW & DCAP. As atualizações aos componentes de geração de cotação de PSW são validadas para retrocompatibilidade pelo fornecedor antes da atualização. Isto ajudará a lidar com as questões de compatibilidade antecipadamente e abordá-las-á antes de implementar atualizações para cargas de trabalho confidenciais.

## <a name="how-does-the-out-of-proc-attestation-mode-work-for-confidential-workloads-scenario"></a>Como funciona o modo de atestado fora de proc para cenário de cargas de trabalho confidenciais?

O design de alto nível segue o modelo onde o solicitador de cotação e geração de citações são executados separadamente, mas na mesma máquina física. A geração de citações será feita de forma centralizada e serve pedidos de CITAÇÕES de todas as entidades. A interface precisa de ser devidamente definida e detetável para qualquer entidade solicitar cotações.

![sgx citação aesmo ajudante](./media/confidential-nodes-out-of-proc-attestation/aesmmanager.png)

O modelo abstrato acima aplica-se ao cenário de carga de trabalho confidencial, tirando partido do serviço AESM já disponível. A AESM é contentorizada e implantada como um daemonSet através do cluster Kubernetes. Kubernetes garante uma única instância de um recipiente de serviço AESM, embrulhado num Pod, a ser implantado em cada nó de agente. O novo daemonset SGX Quote terá uma dependência do daemonset sgx-device-plugin, uma vez que o recipiente de serviço AESM solicitaria a memória EPC do plugin de dispositivo sgx para o lançamento de enclaves QE e PCE.

Cada recipiente tem de optar por utilizar a geração de citações fora de proc, definindo a variável ambiental **SGX_AESM_ADDR=1** durante a criação. O recipiente também deve incluir o pacote libsgx-quote-ex que é responsável por direcionar o pedido para a tomada de domínio Unix padrão

Uma aplicação ainda pode usar o atestado in-proc como antes, mas tanto in-proc como fora-de-proc não podem ser usados simultaneamente dentro de uma aplicação. A infraestrutura fora de proc está disponível por defeito e consome recursos.

## <a name="sample-implementation"></a>Implementação da amostra

O ficheiro estivador abaixo é uma amostra para uma aplicação baseada em Enclave Aberto. Desaprove a variável ambiente SGX_AESM_ADDR=1 no ficheiro do estivador ou por defini-la no ficheiro de implantação. Siga a amostra abaixo para obter detalhes de docker e de implementação yaml. 

  > [!Note] 
  > O **libsgx-quote-ex** da Intel precisa de ser embalado no recipiente de aplicação para atestado fora de proc para funcionar corretamente.
    
```yaml
# Refer to Intel_SGX_Installation_Guide_Linux for detail
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
# this sets the flag for out of proc attestation mode. alternatively you can set this flag on the deployment files
ENV SGX_AESM_ADDR=1 

CMD make run
```
Alternativamente, o modo de atestado fora de proc pode ser definido no ficheiro yaml de implantação, como mostrado abaixo

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

## <a name="next-steps"></a>Passos Seguintes
[Provisiona nos nós confidenciais (Série DCsv2) em AKS](./confidential-nodes-aks-get-started.md)

[Amostras de arranque rápido recipientes confidenciais](https://github.com/Azure-Samples/confidential-container-samples)

[Lista DCsv2 SKU](../virtual-machines/dcv2-series.md)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions