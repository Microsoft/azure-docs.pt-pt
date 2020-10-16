---
title: Criar um ambiente de encenação em Azure Spring Cloud Microsoft Docs
description: Saiba como usar a implementação azul-verde com a nuvem de primavera Azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 72cf5553bec5985ba0310b4a347b0d2c60da6924
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090714"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Crie um ambiente de encenação em Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java

Este artigo discute como configurar uma implementação de encenação usando o padrão de implantação azul-verde em Azure Spring Cloud. A implementação azul/verde é um padrão de Entrega Contínua de DevOps do Azure que se baseia em manter uma versão já existente ativa (azul) enquanto é implementada uma nova. Este artigo mostra-lhe como colocar essa encenação em produção sem alterar a implantação da produção diretamente.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que já implementou a aplicação PiggyMetrics do nosso tutorial sobre o lançamento de [uma aplicação Azure Spring Cloud](./spring-cloud-quickstart.md). A PiggyMetrics compreende três aplicações: "gateway", "serviço de conta" e "serviço auth".  

Se quiser utilizar uma aplicação diferente para este exemplo, tem de fazer uma simples alteração numa parte virada para o público da aplicação.  Esta alteração diferencia a sua colocação de produção.

>[!TIP]
> AZure Cloud Shell é uma concha interativa gratuita que pode usar para executar as instruções neste artigo.  Tem ferramentas Azure comuns e pré-instaladas, incluindo as versões mais recentes de Git, JDK, Maven e o Azure CLI. Se tiver assinado a sua subscrição Azure, inicie a sua [Azure Cloud Shell](https://shell.azure.com).  Para saber mais, consulte [a visão geral da Azure Cloud Shell.](../cloud-shell/overview.md)

Para configurar um ambiente de preparação em Azure Spring Cloud, siga as instruções nas secções seguintes.

## <a name="install-the-azure-cli-extension"></a>Instale a extensão Azure CLI

Instale a extensão Azure Spring Cloud para o Azure CLI utilizando o seguinte comando:

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>Ver todas as implementações

Vá à sua instância de serviço no portal Azure e selecione **gestão de Implementação** para visualizar todas as implementações. Para ver mais detalhes, pode selecionar cada implementação.

## <a name="create-a-staging-deployment"></a>Criar uma implementação de encenação

1. No seu ambiente de desenvolvimento local, faça uma pequena modificação na aplicação de gateway PiggyMetrics. Por exemplo, altere a cor no ficheiro *gateway/src/main/resources/static/css/launch.css.* Ao fazê-lo, permite-lhe diferenciar facilmente as duas implementações. Para construir o pacote de frascos, executar o seguinte comando: 

    ```console
    mvn clean package
    ```

1. No CLI Azure, crie uma nova implantação, e dê-lhe o nome de implementação de encenação "verde".

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. Após o final da implementação com sucesso, aceda à página de gateway a partir do Painel de **Aplicações**, e veja todas as suas instâncias no separador Instâncias de **Aplicações** à esquerda.
  
> [!NOTE]
> O estado de descoberta é *OUT_OF_SERVICE* para que o tráfego não seja encaminhado para esta implementação antes que a verificação esteja completa.

## <a name="verify-the-staging-deployment"></a>Verifique a implantação da encenação

1. Volte à página **de gestão de Implementação** e selecione a sua nova implementação. O estado de implantação deve mostrar *Running*. O botão **de domínio Atribuição/Não-Assinatura** deve parecer cinzento, porque o ambiente é um ambiente de preparação.

1. No **painel de visão** geral, deverá ver um **ponto final de teste**. Copie e cole-o numa nova janela do navegador, e a nova página PiggyMetrics deve ser exibida.

>[!TIP]
> * Confirme que o seu ponto final de teste termina com um corte (/) para garantir que o ficheiro CSS está carregado corretamente.  
> * Se o seu navegador exigir que introduza credenciais de login para visualizar a página, use [o descodificar o URL](https://www.urldecoder.org/) para descodificar o seu ponto final de teste. O descodifica URL devolve um URL no formulário "https:// \<username> : \<password> @ \<cluster-name> .test.azureapps.io/gateway/green".  Utilize este formulário para aceder ao seu ponto final.

>[!NOTE]    
> As definições do servidor Config aplicam-se tanto ao ambiente de preparação como à produção. Por exemplo, se definir o caminho de contexto () para o `server.servlet.context-path` seu gateway de aplicações no servidor config como *algum caminho,* o caminho para a sua implementação verde muda para "https:// \<username> : \<password> @ \<cluster-name> .test.azureapps.io/gateway/green/somepath/...".
 
 Se visitar o seu portal de aplicações virado para o público neste momento, deverá ver a página antiga sem a sua nova alteração.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>Definir a implantação verde como o ambiente de produção

1. Depois de verificar a sua mudança no seu ambiente de encenação, pode empurrá-la para a produção. Volte à **gestão da Implementação**e selecione a caixa de verificação da aplicação **gateway.**

2. Selecione **a implementação do conjunto**.
3. Na lista **de Implantação de Produção,** selecione **Verde**e, em seguida, selecione **Apply**.
4. Aceda à página **geral** da sua aplicação gateway. Se já atribuiu um domínio para a sua aplicação gateway, o URL aparecerá no **painel de visão** geral. Para ver a página PiggyMetrics modificada, selecione o URL e vá ao site.

>[!NOTE]
> Depois de definir a implantação verde como ambiente de produção, a implantação anterior torna-se a colocação de encenação.

## <a name="modify-the-staging-deployment"></a>Modificar a implantação da encenação

Se não estiver satisfeito com a sua alteração, pode modificar o seu código de aplicação, construir um novo pacote de frascos e carregá-lo para a sua versão verde utilizando o Azure CLI.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Eliminar a implementação da encenação

Para eliminar a sua colocação a partir da porta Azure, aceda à sua página de implementação de encenação e, em seguida, selecione o botão **Eliminar.**

Em alternativa, elimine a sua colocação do CLI Azure executando o seguinte comando:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Implemente a sua primeira aplicação Azure Spring Cloud](spring-cloud-quickstart.md)