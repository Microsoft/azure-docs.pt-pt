---
title: Implementar OpenShift em Azure Stack
description: Desdobre o OpenShift na Pilha Azure.
author: haroldwongms
manager: joraio
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: decc66d2d5abd16c084aa19443dbac54e87a9560
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101667230"
---
# <a name="deploy-openshift-container-platform-or-okd-in-azure-stack"></a>Implementar plataforma de recipientes openshift ou OKD em Azure Stack

O OpenShift pode ser implantado em Azure Stack. Existem algumas diferenças fundamentais entre Azure e Azure Stack, pelo que a implementação diferirá ligeiramente e as capacidades também diferirão ligeiramente.

Atualmente, o Azure Cloud Provider não funciona em Azure Stack. Por esta razão, não poderá utilizar o dissi de disco para armazenamento persistente em Azure Stack. Em vez disso, pode configurar outras opções de armazenamento, tais como NFS, iSCSI, GlusterFS, etc. Como alternativa, pode ativar o SNC e utilizar o GlusterFS para armazenamento persistente. Se o SNC estiver ativado, serão implantados três nós adicionais com armazenamento adicional para utilização de GlusterFS.

Pode utilizar um de vários métodos para implementar a Plataforma de Recipientes OpenShift ou OKD em Azure Stack:

- Pode implantar manualmente os componentes de infraestrutura Azure necessários e, em seguida, seguir a documentação da [Plataforma de Contentores OpenShift](https://docs.openshift.com/container-platform) ou [a documentação OKD](https://docs.okd.io).
- Também pode utilizar um modelo de [Gestor de Recursos](https://github.com/Microsoft/openshift-container-platform/) existente que simplifica a implantação do cluster da Plataforma de Contentores OpenShift.
- Também pode utilizar um modelo de [Gestor de Recursos](https://github.com/Microsoft/openshift-origin) existente que simplifica a implementação do cluster OKD.

Se utilizar o modelo de Gestor de Recursos, selecione o ramo adequado (azurestack-release-3.x). Os modelos para Azure não funcionarão, uma vez que as versões API são diferentes entre Azure e Azure Stack. A referência de imagem RHEL é atualmente codificada como uma variável no azuredeploy.jsem ficheiro e terá de ser alterada para corresponder à sua imagem.

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

Para todas as opções, é necessária uma subscrição do Red Hat. Durante a implementação, a instância Red Hat Enterprise Linux está registada na assinatura Red Hat e anexada ao Pool ID que contém os direitos da Plataforma de Contentores OpenShift.
Certifique-se de que tem um nome de utilizador válido do Red Hat Subscription Manager (RHSM), palavra-passe e ID do Pool. Em alternativa, pode utilizar uma Chave de Ativação, ID Org e Pool ID.  Pode verificar esta informação ao iniciar sessão em https://access.redhat.com .

## <a name="azure-stack-prerequisites"></a>Pré-requisitos da Azure Stack

Uma imagem RHEL (Plataforma de Recipientes Abertos) ou imagem CentOS (OKD) precisa de ser adicionada ao ambiente Azure Stack para implantar um cluster OpenShift. Contacte o administrador da Azure Stack para adicionar estas imagens. As instruções podem ser encontradas aqui:

- https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image
- https://docs.microsoft.com/azure/azure-stack/azure-stack-marketplace-azure-items
- https://docs.microsoft.com/azure/azure-stack/azure-stack-redhat-create-upload-vhd

## <a name="deploy-by-using-the-openshift-container-platform-or-okd-resource-manager-template"></a>Implemente utilizando a plataforma de recipientes openshift ou o modelo de gestor de recursos OKD

Para implementar utilizando o modelo de Gestor de Recursos, utilize um ficheiro de parâmetros para fornecer os parâmetros de entrada. Para personalizar ainda mais a implementação, forque o repo GitHub e altere os itens apropriados.

Algumas opções comuns de personalização incluem, mas não se limitam a:

- Tamanho VM de bastião (variável em azuredeploy.jsem)
- Convenções de nomeação (variáveis em azuredeploy.js)
- Especificidades do cluster OpenShift, modificadas através do ficheiro hostes (deployOpenShift.sh)
- Referência de imagem RHEL (variável em azuredeploy.jsem)

Para que os passos de implantação utilizem o CLI Azure, siga a secção apropriada na secção [Plataforma de Contentores Descodificada](./openshift-container-platform-3x.md) ou na secção [OKD.](./openshift-okd.md)

## <a name="next-steps"></a>Passos seguintes

- [Tarefas pós-implantação](./openshift-container-platform-3x-post-deployment.md)
- [Resolução de problemas OpenShift implantação em Azure](./openshift-container-platform-3x-troubleshooting.md)