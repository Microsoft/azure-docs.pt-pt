---
title: Implementar OpenShift em Azure Stack
description: Implementar OpenShift em Azure Stack.
author: haroldwongms
manager: joraio
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 51abfd1cbb438d0987554040867625f7fb71630b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758232"
---
# <a name="deploy-openshift-container-platform-or-okd-in-azure-stack"></a>Implementar plataforma de contentores openshift ou OKD em Pilha Azure

OpenShift pode ser implantado em Azure Stack. Existem algumas diferenças fundamentais entre o Azure e o Azure Stack, pelo que a implantação será ligeiramente diferente e as capacidades também diferirão ligeiramente.

Atualmente, o Azure Cloud Provider não trabalha no Azure Stack. Por esta razão, não poderá utilizar o encaixe do disco para armazenamento persistente em Azure Stack. Em vez disso, pode configurar outras opções de armazenamento, tais como NFS, iSCSI, GlusterFS, etc. Como alternativa, pode ativar o SNC e utilizar o GlusterFS para armazenamento persistente. Se o CNS estiver ativado, serão implantados três nós adicionais com armazenamento adicional para a utilização do GlusterFS.

Pode utilizar um dos vários métodos para implementar plataforma de contentores OpenShift ou OKD em Azure Stack:

- Pode implantar manualmente os componentes de infraestrutura Azure necessários e, em seguida, seguir a documentação da Plataforma de [Contentores OpenShift](https://docs.openshift.com/container-platform) ou [documentação OKD](https://docs.okd.io).
- Também pode utilizar um modelo de Gestor de [Recursos](https://github.com/Microsoft/openshift-container-platform/) existente que simplifica a implementação do cluster openshift container platform.
- Também pode usar um modelo de Gestor de [Recursos](https://github.com/Microsoft/openshift-origin) existente que simplifica a implementação do cluster OKD.

Se utilizar o modelo de Gestor de Recursos, selecione o ramo adequado (lançamento azurestack-3.x). Os modelos para Azure não funcionarão, uma vez que as versões API são diferentes entre Azure e Azure Stack. A referência de imagem RHEL é atualmente codificada como uma variável no ficheiro azuredeploy.json e terá de ser alterada para corresponder à sua imagem.

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

Para todas as opções, é necessária uma subscrição do Chapéu Vermelho. Durante a implementação, a instância Red Hat Enterprise Linux está registada na subscrição do Red Hat e anexa da Id pool que contém os direitos para a Plataforma de Contentores OpenShift.
Certifique-se de que tem um nome de utilizador válido do Red Hat Subscription Manager (RHSM), palavra-passe e ID de Pool. Em alternativa, pode utilizar uma Chave de Ativação, Id org e ID de piscina.  Pode verificar esta informação https://access.redhat.cominserindo-se em .

## <a name="azure-stack-prerequisites"></a>Pré-requisitos da Pilha Azure

Uma imagem RHEL (Plataforma de Recipientes OpenShift) ou imagem CentOS (OKD) precisa de ser adicionada ao seu ambiente Azure Stack para implantar um cluster OpenShift. Contacte o administrador do Azure Stack para adicionar estas imagens. As instruções podem ser encontradas aqui:

- https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image
- https://docs.microsoft.com/azure/azure-stack/azure-stack-marketplace-azure-items
- https://docs.microsoft.com/azure/azure-stack/azure-stack-redhat-create-upload-vhd

## <a name="deploy-by-using-the-openshift-container-platform-or-okd-resource-manager-template"></a>Implementar utilizando a plataforma de contentores OpenShift ou o modelo okd resource manager

Para implementar utilizando o modelo de Gestor de Recursos, utilize um ficheiro de parâmetros para fornecer os parâmetros de entrada. Para personalizar ainda mais a implementação, bifurque o repo GitHub e altere os itens apropriados.

Algumas opções comuns de personalização incluem, mas não se limitam a:

- Tamanho Bastião VM (variável em azuredeploy.json)
- Convenções de nomeação (variáveis em azuredeploy.json)
- Especificidades do cluster OpenShift, modificados através de ficheiros anfitriões (deployOpenShift.sh)
- Referência de imagem RHEL (variável em azuredeploy.json)

Para que as etapas de supor utilizando o Azure CLI, siga a secção adequada na secção Plataforma de [Contentores OpenShift](./openshift-container-platform-3x.md) ou na secção [OKD.](./openshift-okd.md)

## <a name="next-steps"></a>Passos seguintes

- [Tarefas de pós-implantação](./openshift-container-platform-3x-post-deployment.md)
- [Implantação openShift de resolução de problemas em Azure](./openshift-container-platform-3x-troubleshooting.md)