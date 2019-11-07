---
title: Configurar um ambiente de preparo no Azure Spring Cloud | Microsoft Docs
description: Saiba como usar a implantação azul-verde com o Azure Spring Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 24ce4dee04e4daf3eaee4144f8dc56de5867bbca
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607215"
---
# <a name="how-to-set-up-a-staging-environment"></a>Como configurar um ambiente de teste

Este artigo mostrará como aproveitar uma implantação de preparo usando o padrão de implantação azul-verde no Azure Spring Cloud. Ele também mostrará como colocar essa implantação de preparo em produção sem alterar diretamente a implantação de produção.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já tenha implantado o aplicativo PiggyMetrics do nosso [tutorial sobre como iniciar um aplicativo](spring-cloud-quickstart-launch-app-portal.md). O PiggyMetrics inclui três aplicativos: "gateway", "Account-Service" e "auth-Service".  

Se você tiver um aplicativo diferente que gostaria de usar para este exemplo, precisará fazer uma alteração simples em uma parte voltada ao público do aplicativo.  Essa alteração diferencia a implantação de preparo da produção.

>[!TIP]
> O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo.  Ele tem ferramentas comuns do Azure pré-instalados, incluindo as versões mais recentes do git, JDK, Maven e o CLI do Azure. Se você estiver conectado à sua assinatura do Azure, inicie o [Azure cloud Shell](https://shell.azure.com) do Shell.Azure.com.  Você pode saber mais sobre Azure Cloud Shell [lendo nossa documentação](../cloud-shell/overview.md)

Para concluir este artigo:


## <a name="install-the-azure-cli-extension"></a>Instalar a extensão de CLI do Azure

Instale a extensão do Azure Spring Cloud para o CLI do Azure usando o comando a seguir

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>Exibir todas as implantações

Vá para sua instância de serviço no portal do Azure e selecione **Gerenciamento de implantação** para exibir todas as implantações. Você pode selecionar cada implantação para obter mais detalhes.

## <a name="create-a-staging-deployment"></a>Criar uma implantação de preparo

1. Em seu ambiente de desenvolvimento local, faça uma pequena modificação no aplicativo de gateway da métrica do transportado. Por exemplo, altere a cor em `gateway/src/main/resources/static/css/launch.css`. Isso permitirá que você diferencie facilmente as duas implantações. Execute o seguinte comando para compilar o pacote jar: 

    ```azurecli
    mvn clean package
    ```

1. Crie uma nova implantação com CLI do Azure, dando a ela o nome de implantação de preparo "verde".

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. Depois que a implantação for concluída com êxito, acesse a página do gateway no **painel do aplicativo** e veja todas as instâncias na guia **instâncias do aplicativo** à esquerda.
  
> [!NOTE]
> O status da descoberta é "OUT_OF_SERVICE" para que o tráfego não seja roteado para essa implantação antes da conclusão da verificação.

## <a name="verify-the-staging-deployment"></a>Verificar a implantação de preparo

1. retorne à página de **Gerenciamento de implantação** e selecione a nova implantação. O status da implantação deve mostrar **em execução**. O botão "atribuir/cancelar domínio" será desabilitado, pois ele é um ambiente de preparo.

1. Na página **visão geral** , você deve ver um **ponto de extremidade de teste**. Copie e cole-o em uma nova página do navegador e você deverá ver a página novas métricas do transportado.

>[!TIP]
> * Confirme se o ponto de extremidade de teste termina com "/" para garantir que o CSS seja carregado corretamente.  
> * Se seu navegador exigir que você insira credenciais de logon para exibir a página, use a [decodificação de URL](https://www.urldecoder.org/) para decodificar o ponto de extremidade de teste. A decodificação de URL retorna uma URL no formato "https://\<nome de usuário >:\<senha > @\<cluster-Name >. Test. azureapps. Io/gateway/Green".  Use isso para acessar seu ponto de extremidade.

>[!NOTE]    
> As configurações do servidor de configuração se aplicam ao ambiente de preparo, bem como à produção. Por exemplo, se você definir o caminho do contexto (`server.servlet.context-path`) para o gateway de aplicativo no servidor de configuração como *somepath*, o caminho para as alterações de implantação verdes: "https://\<nome de usuário >:\<senha > @\<nome do cluster >. test.azureapps.io/gateway/green/somepath/... "
 
 Se você visitar seu gateway de aplicativo voltado para o público neste ponto, verá a página antiga sem a nova alteração.
    
## <a name="set-the-green-as-production-deployment"></a>Definir o verde como implantação de produção

1. Depois de verificar sua alteração no ambiente de preparo, você pode enviá-la por push para produção. Retorne ao **Gerenciamento de implantação** e marque a caixa de seleção antes do aplicativo "gateway".
2. Selecione "definir implantação".
3. Selecione "verde" no menu "implantação de produção" e selecione **aplicar**
4. Vá para a página **visão geral** do aplicativo de gateway. Se você já tiver atribuído um domínio para seu aplicativo de gateway, a URL será exibida na página **visão geral** . Visite a URL e você verá a página de métricas transportado modificadas.

>[!NOTE]
> Depois que a implantação verde for definida como ambiente de produção, a implantação anterior se tornará a implantação de preparo.

## <a name="modify-the-staging-deployment"></a>Modificar a implantação de preparo

Se você não estiver satisfeito com sua alteração, poderá modificar o código do aplicativo, criar um novo pacote jar e carregá-lo em sua implantação verde usando o CLI do Azure.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-a-staging-deployment"></a>Excluir uma implantação de preparo

Exclua sua implantação de preparo da porta do Azure navegando até a página de implantação de preparo e selecionando o botão **excluir** .

Como alternativa, exclua sua implantação de preparo do CLI do Azure com o seguinte comando:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
