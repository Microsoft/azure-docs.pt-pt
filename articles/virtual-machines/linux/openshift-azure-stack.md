---
title: Implantar OpenShift no Azure Stack | Microsoft Docs
description: Implantar OpenShift no Azure Stack.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: haroldw
ms.openlocfilehash: f9f1072954e01f718fd3d9f03430b6ed6666bb62
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70082586"
---
# <a name="deploy-openshift-container-platform-or-okd-in-azure-stack"></a>Implantar a plataforma de contêiner OpenShift ou OKD no Azure Stack

O OpenShift pode ser implantado em Azure Stack. Há algumas diferenças importantes entre o Azure e o Azure Stack, portanto, a implantação será ligeiramente diferente e os recursos também serão ligeiramente diferentes.

Atualmente, o provedor de nuvem do Azure não funciona no Azure Stack. Por esse motivo, você não poderá usar a anexação de disco para armazenamento persistente no Azure Stack. Em vez disso, você pode configurar outras opções de armazenamento, como NFS, iSCSI, GlusterFS, etc. Como alternativa, você pode habilitar o CNS e usar o GlusterFS para armazenamento persistente. Se o CNS estiver habilitado, três nós adicionais serão implantados com armazenamento adicional para uso GlusterFS.

Você pode usar um dos vários métodos para implantar a plataforma de contêiner OpenShift ou OKD no Azure Stack:

- Você pode implantar manualmente os componentes necessários da infraestrutura do Azure e, em seguida, seguir a [documentação da plataforma de contêiner do OpenShift](https://docs.openshift.com/container-platform) ou a documentação do [OKD](https://docs.okd.io).
- Você também pode usar um modelo existente do [Resource Manager](https://github.com/Microsoft/openshift-container-platform/) que simplifica a implantação do cluster da plataforma de contêiner do OpenShift.
- Você também pode usar um modelo existente do [Resource Manager](https://github.com/Microsoft/openshift-origin) que simplifica a implantação do cluster OKD.

Se estiver usando o modelo do Resource Manager, selecione o Branch apropriado (azurestack-Release-3. x). Os modelos do Azure não funcionarão, pois as versões de API são diferentes entre o Azure e o Azure Stack. Atualmente, a referência de imagem RHEL está embutida em código como uma variável no arquivo azuredeploy. JSON e precisa ser alterada para corresponder à imagem.

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

Para todas as opções, é necessária uma assinatura do Red Hat. Durante a implantação, a instância de Red Hat Enterprise Linux é registrada na assinatura do Red Hat e anexada à ID do pool que contém os direitos para a plataforma de contêiner OpenShift.
Verifique se você tem um nome de usuário, senha e ID de pool válidos do RHSM (Red Hat Subscription Manager). Como alternativa, você pode usar uma chave de ativação, ID da organização e ID do pool.  Você pode verificar essas informações entrando no https://access.redhat.com.

## <a name="azure-stack-prerequisites"></a>Pré-requisitos de Azure Stack

Uma imagem RHEL (plataforma de contêiner OpenShift) ou imagem CentOS (OKD) precisa ser adicionada ao ambiente de Azure Stack para implantar um cluster OpenShift. Contate o administrador do Azure Stack para adicionar essas imagens. As instruções podem ser encontradas aqui:

- https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image
- https://docs.microsoft.com/azure/azure-stack/azure-stack-marketplace-azure-items
- https://docs.microsoft.com/azure/azure-stack/azure-stack-redhat-create-upload-vhd

## <a name="deploy-by-using-the-openshift-container-platform-or-okd-resource-manager-template"></a>Implantar usando a plataforma de contêiner OpenShift ou o modelo do Gerenciador de recursos do OKD

Para implantar usando o modelo do Resource Manager, você usa um arquivo de parâmetros para fornecer os parâmetros de entrada. Para personalizar ainda mais a implantação, crie bifurcação do repositório GitHub e altere os itens apropriados.

Algumas opções de personalização comuns incluem, mas não se limitam a:

- Tamanho da VM de bastiões (variável em azuredeploy. JSON)
- Convenções de nomenclatura (variáveis em azuredeploy. JSON)
- OpenShift específicas do cluster, modificadas por meio do arquivo de hosts (deployOpenShift.sh)
- Referência de imagem RHEL (variável em azuredeploy. JSON)

Para obter as etapas para implantar usando o CLI do Azure, siga a seção apropriada na seção [plataforma de contêiner OpenShift](./openshift-container-platform.md) ou na seção [OKD](./openshift-okd.md) .

## <a name="next-steps"></a>Passos Seguintes

- [Tarefas pós-implantação](./openshift-post-deployment.md)
- [Solucionar problemas de implantação do OpenShift no Azure](./openshift-troubleshooting.md)