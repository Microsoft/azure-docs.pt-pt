---
title: Configurar um ambiente de preparo no Azure Spring Cloud | Microsoft Docs
description: Saiba como usar a implantação azul-verde com o Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: 4adeb5593f86bdf3f8a4ea5f844c31a8314e0f15
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276923"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Configurar um ambiente de preparo no Azure Spring Cloud

Este artigo discute como configurar uma implantação de preparo usando o padrão de implantação azul-verde no Azure Spring Cloud. Ele também mostra como colocar essa implantação de preparo em produção sem alterar diretamente a implantação de produção.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já tenha implantado o aplicativo PiggyMetrics do nosso [tutorial sobre como iniciar um aplicativo do Azure Spring Cloud](spring-cloud-quickstart-launch-app-portal.md). O PiggyMetrics inclui três aplicativos: "gateway", "Account-Service," e "auth-Service".  

Se você quiser usar um aplicativo diferente para este exemplo, precisará fazer uma alteração simples em uma parte voltada ao público do aplicativo.  Essa alteração diferencia a implantação de preparo da produção.

>[!TIP]
> Azure Cloud Shell é um shell interativo gratuito que você pode usar para executar as instruções neste artigo.  Ele tem ferramentas do Azure já instaladas, incluindo as versões mais recentes do git, do JDK, do Maven e do CLI do Azure. Se você estiver conectado à sua assinatura do Azure, inicie o [Azure cloud Shell](https://shell.azure.com).  Para saber mais, confira [visão geral do Azure cloud Shell](../cloud-shell/overview.md).

Para configurar um ambiente de preparo no Azure Spring Cloud, siga as instruções nas próximas seções.

## <a name="install-the-azure-cli-extension"></a>Instalar a extensão de CLI do Azure

Instale a extensão do Azure Spring Cloud para o CLI do Azure usando o seguinte comando:

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>Exibir todas as implantações

Vá para sua instância de serviço no portal do Azure e selecione **Gerenciamento de implantação** para exibir todas as implantações. Para exibir mais detalhes, você pode selecionar cada implantação.

## <a name="create-a-staging-deployment"></a>Criar uma implantação de preparo

1. Em seu ambiente de desenvolvimento local, faça uma pequena modificação no aplicativo de gateway PiggyMetrics. Por exemplo, altere a cor no arquivo *Gateway/src/main/resources/static/CSS/Launch. css* . Isso permite que você diferencie facilmente as duas implantações. Para compilar o pacote jar, execute o seguinte comando: 

    ```azurecli
    mvn clean package
    ```

1. No CLI do Azure, crie uma nova implantação e dê a ela o nome de implantação de preparo "verde".

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. Depois que a implantação for concluída com êxito, acesse a página do gateway no **painel do aplicativo**e exiba todas as suas instâncias na guia **instâncias do aplicativo** à esquerda.
  
> [!NOTE]
> O status da descoberta é *OUT_OF_SERVICE* para que o tráfego não seja roteado para essa implantação antes de a verificação ser concluída.

## <a name="verify-the-staging-deployment"></a>Verificar a implantação de preparo

1. Retorne à página **Gerenciamento de implantação** e selecione a nova implantação. O status da implantação deve mostrar *em execução*. O botão **atribuir/cancelar domínio** deve aparecer esmaecido, pois o ambiente é um ambiente de preparo.

1. No painel **visão geral** , você deve ver um **ponto de extremidade de teste**. Copie e cole-o em uma nova janela do navegador e a nova página PiggyMetrics deverá ser exibida.

>[!TIP]
> * Confirme se o ponto de extremidade de teste termina com uma barra (/) para garantir que o arquivo CSS seja carregado corretamente.  
> * Se seu navegador exigir que você insira credenciais de logon para exibir a página, use a [decodificação de URL](https://www.urldecoder.org/) para decodificar o ponto de extremidade de teste. A decodificação de URL retorna uma URL no formato "https://\<nome de usuário >:\<senha > @\<cluster-Name >. Test. azureapps. Io/gateway/Green".  Use este formulário para acessar seu ponto de extremidade.

>[!NOTE]    
> As configurações do servidor de configuração se aplicam tanto ao ambiente de preparo quanto à produção. Por exemplo, se você definir o caminho do contexto (`server.servlet.context-path`) para o gateway de aplicativo no servidor de configuração como *somepath*, o caminho para a implantação verde será alterado para "https://\<nome de usuário >:\<senha > @\<nome do cluster >. Test. azureapps. Io/gateway/Green/somepath/...".
 
 Se você visitar seu gateway de aplicativo voltado para o público neste ponto, verá a página antiga sem a nova alteração.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>Definir a implantação verde como o ambiente de produção

1. Depois de verificar sua alteração no ambiente de preparo, você pode enviá-la por push para produção. Retorne ao **Gerenciamento de implantação**e marque a caixa de seleção aplicativo de **Gateway** .

2. Selecione **definir implantação**.
3. Na lista **implantação de produção** , selecione **verde**e, em seguida, selecione **aplicar**.
4. Vá para a página **visão geral** do aplicativo de gateway. Se você já tiver atribuído um domínio para seu aplicativo de gateway, a URL aparecerá no painel **visão geral** . Para exibir a página PiggyMetrics modificada, selecione a URL e vá para o site.

>[!NOTE]
> Depois de definir a implantação verde como o ambiente de produção, a implantação anterior torna-se a implantação de preparo.

## <a name="modify-the-staging-deployment"></a>Modificar a implantação de preparo

Se você não estiver satisfeito com sua alteração, poderá modificar o código do aplicativo, criar um novo pacote jar e carregá-lo em sua implantação verde usando o CLI do Azure.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Excluir a implantação de preparo

Para excluir a implantação de preparo da porta do Azure, acesse a página de implantação de preparo e, em seguida, selecione o botão **excluir** .

Como alternativa, exclua a implantação de preparo do CLI do Azure executando o seguinte comando:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
