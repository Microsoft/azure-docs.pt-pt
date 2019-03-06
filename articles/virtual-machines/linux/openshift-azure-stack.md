---
title: Implementar o OpenShift no Azure Stack | Documentos da Microsoft
description: Implemente o OpenShift no Azure Stack.
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
ms.date: 10/23/2018
ms.author: haroldw
ms.openlocfilehash: 91b37753ae80596612eda9d3ccd34858691e35ad
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57446950"
---
# <a name="deploy-openshift-container-platform-or-okd-in-azure-stack"></a>Implementar o OpenShift Container Platform ou OKD no Azure Stack

OpenShift pode ser implementado no Azure Stack. Existem algumas diferenças fundamentais entre o Azure e o Azure Stack, de modo a implementação irá ser ligeiramente diferentes e funcionalidades também serão ligeiramente diferentes.

Atualmente, o fornecedor de Cloud do Azure não funciona no Azure Stack. Por esse motivo, não será possível utilizar o disco anexar para armazenamento persistente no Azure Stack. Em vez disso, pode configurar outras opções de armazenamento, como NFS, iSCSI, GlusterFS, etc. Como alternativa, pode ativar CNS e utilizar GlusterFS para armazenamento persistente. Se estiver ativada CNS, três nós adicionais serão implantados com o armazenamento adicional para utilização GlusterFS.

Pode utilizar um dos vários métodos para implementar o OpenShift Container Platform ou OKD no Azure Stack:

- Pode implementar manualmente os componentes de infraestrutura do Azure necessários e, em seguida, siga os [documentação do OpenShift Container Platform](https://docs.openshift.com/container-platform) ou [documentação OKD](https://docs.okd.io).
- Também pode utilizar existente [modelo do Resource Manager](https://github.com/Microsoft/openshift-container-platform/) que simplifica a implementação do cluster OpenShift Container Platform.
- Também pode utilizar existente [modelo do Resource Manager](https://github.com/Microsoft/openshift-origin) que simplifica a implementação do OKD cluster.

Se utilizar o modelo do Resource Manager, selecione o ramo adequado (3.x da versão azurestack). Os modelos para o Azure não funcionarão como as versões de API são diferentes entre o Azure e o Azure Stack. A referência da imagem RHEL está atualmente hard-coded como uma variável no ficheiro azuredeploy. JSON e tem de ser alterada para coincidir com a sua imagem.

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

Para todas as opções, é necessária uma subscrição de Red Hat. Durante a implantação, a instância de Red Hat Enterprise Linux está registada para a subscrição do Red Hat e anexada ao ID de agrupamento que contém a elegibilidade para o OpenShift Container Platform.
Certifique-se de que tem um válidas Red Hat subscrição Manager (RHSM) nome de utilizador, palavra-passe e ID do conjunto. Em alternativa, pode utilizar uma chave de ativação, o ID da organização e o ID do conjunto.  Pode verificar estas informações ao iniciar sessão para https://access.redhat.com.

## <a name="azure-stack-prerequisites"></a>Pré-requisitos do Azure Stack

Uma imagem RHEL (OpenShift Container Platform) ou a imagem do CentOS (OKD) tem de ser adicionada ao seu ambiente do Azure Stack para implementar um cluster do OpenShift. Contacte o administrador do Azure Stack para adicioná-las. Aqui pode encontrar instruções:

- https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image
- https://docs.microsoft.com/azure/azure-stack/azure-stack-marketplace-azure-items
- https://docs.microsoft.com/azure/azure-stack/azure-stack-redhat-create-upload-vhd

## <a name="deploy-by-using-the-openshift-container-platform-or-okd-resource-manager-template"></a>Implementar utilizando o modelo OpenShift Container Platform ou OKD Resource Manager

Para implementar com o modelo do Resource Manager, use um ficheiro de parâmetros para fornecer os parâmetros de entrada. Para personalizar ainda mais a implementação, bifurcar o repositório do GitHub e altere os itens adequados.

Algumas opções de personalização comuns incluem, mas não estão limitadas a:

- Tamanho de VM de bastion (variável no azuredeploy. JSON)
- Convenções de nomenclatura (variáveis no azuredeploy. JSON)
- Especificações de cluster do OpenShift, modificado através do ficheiro de anfitriões (deployOpenShift.sh)
- Referência da imagem RHEL (variável no azuredeploy. JSON)

Para obter os passos implementar com a CLI do Azure, siga a secção adequada a [OpenShift Container Platform](./openshift-container-platform.md) secção ou o [OKD](./openshift-okd.md) secção.

## <a name="next-steps"></a>Passos Seguintes

- [Tarefas de pós-implementação](./openshift-post-deployment.md)
- [Resolver problemas de implementação do OpenShift no Azure](./openshift-troubleshooting.md)