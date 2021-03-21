---
title: Como migrar do controlador de gateway de entrada de aplicação Azure para o add-on AGIC
description: Este artigo fornece instruções sobre como migrar da AGIC implantada através do Helm para a AGIC implantada como um addon AKS
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: e83834fd5f8ca95826118c952f7884a494c7abbb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102050839"
---
# <a name="migrate-from-agic-helm-to-agic-add-on"></a>Migrar do AGIC Helm para o addon AGIC 

Se já tem a AGIC implantada através do Helm mas quer migrar para a AGIC implantada como um complemento AKS, os seguintes passos ajudarão a guiá-lo através do processo de migração. 

## <a name="prerequisites"></a>Pré-requisitos 
Antes de iniciar o processo de migração, há algumas coisas a verificar. 
  - Está a utilizar funcionalidades com a AGIC Helm que não sejam [suportadas atualmente com add-on da AGIC?](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)
  - Está a utilizar mais de uma implantação do Leme AGIC por cluster AKS? 
  - Está a utilizar várias implementações do AGIC Helm para atingir um Gateway de aplicação? 

Se respondeu sim a qualquer uma das perguntas acima, o addon AGIC não apoiará o seu caso de uso ainda, pelo que o melhor será continuar a usar o AGIC Helm entretanto. Caso contrário, continue com o processo de migração abaixo durante o horário de trabalho. 

## <a name="find-the-application-gateway-resource-id-that-agic-helm-is-currently-targeting"></a>Encontre o ID de recurso de gateway de aplicação que a AGIC Helm está atualmente a visar 
Navegue para o Gateway de aplicações que a sua implementação AGIC Helm está a ser alvo. Copie e guarde o ID de recursos do Gateway de aplicação. Vai precisar da identificação do recurso num passo posterior. O ID de recursos pode ser encontrado no Portal, no separador Propriedades do seu Gateway de Aplicações ou através do Azure CLI. O exemplo a seguir guarda o ID de recurso Application Gateway para *appgwId* para um gateway chamado *myApplicationGateway* no grupo de recursos *myResourceGroup*.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id") 
```

## <a name="delete-agic-helm-from-your-aks-cluster"></a>Elimine o Leme AGIC do seu cluster AKS
Através do Azure CLI, elimine a sua implantação AGIC Helm do seu cluster. Terá de eliminar primeiro a implementação do Leme AGIC antes de poder ativar o addon AGIC AKS. Por favor, note que quaisquer alterações que ocorram dentro do seu cluster AKS entre o momento de eliminação da sua implementação AGIC Helm e o tempo que ativar o addon AGIC não serão refletidas no seu Gateway de Aplicação, pelo que este processo de migração deve ser feito fora do horário de trabalho para minimizar o impacto. O Application Gateway continuará a ter a última configuração aplicada pela AGIC para que as regras de encaminhamento existentes não sejam afetadas. 

## <a name="enable-agic-add-on-using-your-existing-application-gateway"></a>Ativar o addon AGIC utilizando o seu Gateway de aplicações existente 
Agora pode ativar o addon AGIC no seu cluster AKS para direcionar o seu Gateway de Aplicação existente através do Azure CLI ou portal. Executar o seguinte comando Azure CLI para ativar o addon AGIC no seu cluster AKS. O exemplo permite o addon num cluster chamado *myCluster,* num grupo de recursos chamado *myResourceGroup,* utilizando o *ID* de recurso de gateway de aplicação que poupámos acima no passo anterior. 


```azurecli-interactive
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

Em alternativa, pode navegar para o seu cluster AKS no Portal utilizando este [link](https://portal.azure.com/?feature.aksagic=true) e ativar o addon AGIC no separador de Rede do seu cluster. Selecione o gateway de aplicações existente no menu suspenso quando escolher qual o Gateway de Aplicação que o add-on deve ter como alvo. 

![Portal do Controlador de Entrada de Gateway de Aplicação](./media/tutorial-ingress-controller-add-on-existing/portal-ingress-controller-add-on.png)

## <a name="next-steps"></a>Passos Seguintes
- Resolução de problemas do controlador de entrada de aplicações Gateway [**Ingress**](ingress-controller-troubleshoot.md): Guia de resolução de problemas para a AGIC 
- [**Anotações do controlador de entrada**](ingress-controller-annotations.md)de entrada de aplicação : Lista de anotações no AGIC 