---
title: Criar um controlador de dados Azure Arc no portal Azure
description: Criar um controlador de dados Azure Arc no portal Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 04/07/2021
ms.topic: how-to
ms.openlocfilehash: 12d0997e677bcca423f32951e99a6202855104ad
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030871"
---
# <a name="create-an-azure-arc-data-controller-in-the-azure-portal"></a>Criar um controlador de dados Azure Arc no portal Azure

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="introduction"></a>Introdução

Pode utilizar o portal Azure para criar um controlador de dados Azure Arc.

Muitas das experiências de criação do Arco Azure começam no portal Azure, mesmo que o recurso a ser criado ou gerido esteja fora da infraestrutura Azure. O padrão de experiência do utilizador nestes casos, especialmente quando não há conectividade direta entre o Azure e o seu ambiente, é usar o portal Azure para gerar um script que pode ser descarregado e executado no seu ambiente para estabelecer uma ligação segura de volta ao Azure. Por exemplo, os servidores ativados pelo Azure Arc seguem este padrão para [criar servidores ativados pelo Arc](../servers/onboard-portal.md).

Quando utilizar o modo de ligação indireto dos serviços de dados ativados do Azure Arc, pode utilizar o portal Azure para gerar um portátil para si que pode ser descarregado e executado no Azure Data Studio contra o seu cluster Kubernetes. 

Quando utilizar o modo de ligação direta, pode forcrédão do controlador de dados diretamente a partir do portal Azure. Pode ler mais sobre [os modos de conectividade.](connectivity.md)

## <a name="use-the-azure-portal-to-create-an-azure-arc-data-controller"></a>Utilize o portal Azure para criar um controlador de dados Azure Arc

Siga os passos abaixo para criar um controlador de dados Azure Arc utilizando o portal Azure e o Azure Data Studio.

1. Primeiro, faça login no mercado do [portal Azure.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/home/searchQuery/azure%20arc%20data%20controller)  Os resultados da pesquisa no mercado serão filtrados para lhe mostrar o 'Controlador de dados Azure Arc'.
1. Se o primeiro passo não tiver introduzido os critérios de pesquisa. Introduza os resultados da pesquisa, clique em 'Controlador de dados Azure Arc'.
1. Selecione o azulejo do Controlador de Dados Azure do mercado.
1. Clique no botão **Criar.**
1. Selecione o modo de conectividade indireta. Saiba mais sobre [os modos e requisitos de conectividade.](./connectivity.md) 
1. Reveja os requisitos para criar um controlador de dados Azure Arc e instale qualquer software pré-requisito em falta, como o Azure Data Studio e o kubectl.
1. Clique no botão **Seguinte: Detalhes do controlador de dados.**
1. Escolha uma subscrição, grupo de recursos e localização Azure tal como faria para qualquer outro recurso que criaria no portal Azure. Neste caso, a localização Azure que seleciona será onde os metadados sobre o recurso serão armazenados.  O recurso em si será criado em qualquer infraestrutura que escolher. Não precisa de estar na infraestrutura de Azure.
1. Insira um nome para o seu controlador de dados.

1. Selecione um perfil de configuração de implementação.
1. Clique no botão **Open in Azure Studio.**
1. No próximo ecrã, verá um resumo das suas seleções e um caderno que é gerado.  Pode clicar no botão **de portátil de provisionamento** para descarregar o caderno.

   > [!IMPORTANT]
   > Na plataforma de contentores Azure Red Hat OpenShift ou Red Hat OpenShift, deve aplicar a restrição de contexto de segurança antes de criar o controlador de dados. Siga as instruções na [Aplicação de uma restrição de contexto de segurança para serviços de dados ativados pelo Azure Arc no OpenShift](how-to-apply-security-context-constraint.md).

1. Abra o caderno no Azure Data Studio e clique no botão **Executar Tudo** na parte superior.
1. Siga as instruções e instruções no caderno para completar a criação do controlador de dados.

## <a name="monitoring-the-creation-status"></a>Monitorização do estado de criação

A criação do controlador levará alguns minutos a ser concluída. Pode monitorizar o progresso noutra janela do terminal com os seguintes comandos:

> [!NOTE]
>  O exemplo comanda abaixo assumir que criou um controlador de dados e espaço de nome Kubernetes com o nome 'arc'.  Se utilizar um nome diferente de espaço de nome/controlador de dados, pode substituir 'arco' pelo seu nome.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Também pode verificar o estado de criação de qualquer cápsula em particular executando um comando como abaixo.  Isto é especialmente útil para resolver problemas.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Problemas de criação de resolução de problemas

Se encontrar algum problema com a criação, consulte o [guia de resolução de problemas.](troubleshoot-guide.md)