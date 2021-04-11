---
title: Implementar | do controlador de dados Azure Arc Modo de ligação direta
description: Explica como implantar o controlador de dados no modo de ligação direta.
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 04/06/2021
ms.topic: overview
ms.openlocfilehash: 220618f167237d5937766eb5e28b9b6569cef76a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031497"
---
#  <a name="deploy-azure-arc-data-controller--direct-connect-mode"></a>Implementar | do controlador de dados Azure Arc Modo de ligação direta

Este artigo descreve como implementar o controlador de dados Azure Arc no modo de ligação direta durante a pré-visualização atual desta funcionalidade. 

Atualmente pode criar o controlador de dados Azure Arc a partir do portal Azure. Outras ferramentas para serviços de dados ativados pelo Azure Arc não suportam a criação do controlador de dados no modo de ligação direta. Para mais detalhes, consulte [questões conhecidas - Azure Arc ativou serviços de dados (Pré-visualização)](known-issues.md).

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="complete-prerequisites"></a>Pré-requisitos completos

Antes de começar, verifique se completou os pré-requisitos no [controlador de dados implementar - modo de ligação direta - pré-requisitos](deploy-data-controller-direct-mode-prerequisites.md).

A partir de um nível elevado, este artigo explica como completar estas tarefas:

1. Crie uma extensão de serviços de dados ativado pelo Arco Azure. 
1. Crie uma localização personalizada.
1. Implemente o controlador de dados a partir do portal.

## <a name="create-an-azure-arc-enabled-data-services-extension"></a>Criar uma extensão de serviços de dados ativada pelo Arco Azure

Utilize o CLI de extensão k8s para criar uma extensão de serviços de dados.

### <a name="set-environment-variables"></a>Definir variáveis de ambiente

Desaprova as seguintes variáveis ambientais que serão depois utilizadas no passo seguinte.

#### <a name="linux"></a>Linux

``` terminal
# where you want the connected cluster resource to be created in Azure 
export subscription=<Your subscription ID>
export resourceGroup=<Your resource group>
export resourceName=<name of your connected kubernetes cluster>
export location=<Azure location>
```

#### <a name="windows-powershell"></a>Windows PowerShell
``` PowerShell
# where you want the connected cluster resource to be created in Azure 
$ENV:subscription="<Your subscription ID>"
$ENV:resourceGroup="<Your resource group>"
$ENV:resourceName="<name of your connected kubernetes cluster>"
$ENV:location="<Azure location>"
```

### <a name="create-the-arc-data-services-extension"></a>Criar a extensão dos serviços de dados do Arco

#### <a name="linux"></a>Linux

```bash
az k8s-extension create -c ${resourceName} -g ${resourceGroup} --name ${ADSExtensionName} --cluster-type connectedClusters --extension-type microsoft.arcdataservices --version "1.0.015564" --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g ${resourceGroup} -c ${resourceName} --name ${ADSExtensionName} --cluster-type connectedclusters
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:ADSExtensionName="ads-extension"

az k8s-extension create -c "$ENV:resourceName" -g "$ENV:resourceGroup" --name "$ENV:ADSExtensionName" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --version "1.0.015564" --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --name "$ENV:ADSExtensionName" --cluster-type connectedclusters
```

> [!NOTE]
> A instalação de extensão de serviços de dados Arc pode demorar alguns minutos a terminar.

### <a name="verify-the-arc-data-services-extension-is-created"></a>Verifique se a extensão dos serviços de dados do Arco foi criada

Pode verificar se a extensão de serviços de dados ativada pelo Arco é criada a partir do portal ou ligando diretamente ao cluster Kubernetes ativado pelo Arco. 

#### <a name="azure-portal"></a>Portal do Azure
1. Faça login no portal Azure e navegue para o grupo de recursos onde está localizado o recurso de cluster ligado kubernetes.
1. Selecione o cluster de kubernetes ativado pelo Arco (Tipo = "Kubernetes - Arco Azure") onde a extensão foi implantada.
1. Na navegação no lado esquerdo, em **Definições,** selecione "Extensões (pré-visualização)".
1. Deve ver a extensão que foi criada anteriormente num estado "instalado".

:::image type="content" source="media/deploy-data-controller-direct-mode-prerequisites/dc-extensions-dashboard.png" alt-text="Painel de extensões":::

#### <a name="kubectl-cli"></a>Kubectl CLI

1. Ligue-se ao seu cluster Kubernetes através de uma janela do Terminal.
1. Executar o comando abaixo e garantir que o (1) espaço de nome acima mencionado é criado e (2) a `bootstrapper` cápsula está em estado de 'execução' antes de seguir para o passo seguinte.

``` console
kubectl get pods -n <name of namespace used in the json template file above>
```

Por exemplo, o seguinte obtém as cápsulas do espaço de `arc` nome.

```console
#Example:
kubectl get pods -n arc
```

## <a name="create-a-custom-location-using-custom-location-cli-extension"></a>Crie uma localização personalizada usando a extensão CLI de localização personalizada

Uma localização personalizada é um recurso Azure que é equivalente a um espaço de nome em um cluster Kubernetes.  As localizações personalizadas são usadas como um alvo para implantar recursos de ou para Azure. Saiba mais sobre localizações personalizadas nas [localizações personalizadas em cima do Arco Azure habilitado documentação kubernetes.](../kubernetes/conceptual-custom-locations.md)

### <a name="set-environment-variables"></a>Definir variáveis de ambiente

#### <a name="linux"></a>Linux

```bash
export clName=mycustomlocation
export clNamespace=arc
export hostClusterId=$(az connectedk8s show -g ${resourceGroup} -n ${resourceName} --query id -o tsv)
export extensionId=$(az k8s-extension show -g ${resourceGroup} -c ${resourceName} --cluster-type connectedClusters --name ${ADSExtensionName} --query id -o tsv)

az customlocation create -g ${resourceGroup} -n ${clName} --namespace ${clNamespace} \
  --host-resource-id ${hostClusterId} \
  --cluster-extension-ids ${extensionId} --location eastus2euap
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:clName="mycustomlocation"
$ENV:clNamespace="arc"
$ENV:hostClusterId = az connectedk8s show -g "$ENV:resourceGroup" -n "$ENV:resourceName" --query id -o tsv
$ENV:extensionId = az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --cluster-type connectedClusters --name "$ENV:ADSExtensionName" --query id -o tsv

az customlocation create -g "$ENV:resourceGroup" -n "$ENV:clName" --namespace "$ENV:clNamespace" --host-resource-id "$ENV:hostClusterId" --cluster-extension-ids "$ENV:extensionId"
```

## <a name="validate--the-custom-location-is-created"></a>Validar a localização personalizada é criada

A partir do terminal, executar o comando abaixo para listar as localizações personalizadas, e validar que o **Estado de Provisionamento** mostra Ter sucesso:

```azurecli
az customlocation list -o table
```

## <a name="create-the-azure-arc-data-controller"></a>Criar o controlador de dados Azure Arc

Após a extensão e localização personalizada, dirija-se ao portal Azure para implantar o controlador de dados Azure Arc.

1. Inicie sessão no portal do Azure.
1. Procure por "Controlador de dados Azure Arc" no Mercado Azure e inicie o fluxo Create.
1. Na secção **Pré-Requisitos, certifique-se** de que o aglomerado de Kubernetes (modo direto) ativado pelo Arco Azure é selecionado e prossiga para o passo seguinte.
1. Na secção **de detalhes do controlador de dados,** escolha um grupo de subscrição e recursos.
1. Introduza um nome para o controlador de dados.
1. Escolha um perfil de configuração baseado no fornecedor de distribuição Kubernetes para o qual está a implementar.
1. Escolha a Localização Personalizada que criou no passo anterior.
1. Forneça detalhes para o login e senha do administrador do controlador de dados.
1. Forneça detalhes para clientid, TenantId e Client Secret para o Diretor de Serviço que seria usado para criar os objetos Azure. Consulte [as métricas do Upload](upload-metrics-and-logs-to-azure-monitor.md) para obter instruções detalhadas sobre a criação de uma conta Principal de Serviço e as funções que precisavam de ser concedidas para a conta.
1. Clique **em Seguida**, reveja a página de resumo para todos os detalhes e clique em **Criar**.

## <a name="monitor-the-creation"></a>Monitorizar a criação

Quando o estado de implantação do portal Azure mostrar que a implementação foi bem sucedida, pode verificar o estado da implantação do controlador de dados Do Arco no cluster da seguinte forma:

```console
kubectl get datacontrollers -n arc
```

## <a name="next-steps"></a>Passos seguintes

[Criar um grupo de servidores do PostgreSQL Hyperscale preparado para o Azure Arc](create-postgresql-hyperscale-server-group.md)

[Criar um exemplo gerido pelo Azure SQL em Azure Arc](create-sql-managed-instance.md)
