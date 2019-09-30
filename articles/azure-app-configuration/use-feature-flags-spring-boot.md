---
title: Tutorial para usar os sinalizadores de recurso em um aplicativo Spring boot – Azure App configuração | Microsoft Docs
description: Neste tutorial, você aprenderá a implementar sinalizadores de recursos em aplicativos Spring boot.
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlwa
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: java
ms.topic: tutorial
ms.date: 09/26/2019
ms.author: mametcal
ms.custom: mvc
ms.openlocfilehash: 8c66e2995462701f7ddaefc3a2623c02fee883ef
ms.sourcegitcommit: 6013bacd83a4ac8a464de34ab3d1c976077425c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71687172"
---
# <a name="tutorial-use-feature-flags-in-a-spring-boot-app"></a>Tutorial: Usar sinalizadores de recurso em um aplicativo Spring boot

As bibliotecas do Spring boot Core Feature Management oferecem suporte à implementação de sinalizadores de recursos em um aplicativo Spring boot. Essas bibliotecas permitem que você adicione sinalizadores de recurso declarativamente ao seu código.

As bibliotecas de gerenciamento de recursos também gerenciam os ciclos de vida do sinalizador de recursos nos bastidores. Por exemplo, as bibliotecas atualizam e armazenam em cache os Estados de sinalizador ou garantem que um estado de sinalizador seja imutável durante uma chamada de solicitação. Além disso, a biblioteca Spring boot oferece integrações, incluindo ações do controlador MVC, rotas e middleware.

O guia de [início rápido adicionar sinalizadores de recurso a um aplicativo Spring boot](./quickstart-feature-flag-spring-boot.md) mostra várias maneiras de adicionar sinalizadores de recurso em um aplicativo Spring boot. Este tutorial explica esses métodos mais detalhadamente.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Adicione sinalizadores de recurso em partes principais de seu aplicativo para controlar a disponibilidade do recurso.
> * Integre com a configuração de aplicativo quando estiver usando-a para gerenciar sinalizadores de recurso.

## <a name="set-up-feature-management"></a>Configurar o gerenciamento de recursos

O Gerenciador de recursos do Spring boot `FeatureManager` obtém sinalizadores de recurso do sistema de configuração nativa da estrutura. Como resultado, você pode definir os sinalizadores de recurso do aplicativo usando qualquer fonte de configuração com suporte do Spring boot, incluindo o arquivo *. yml* ou as variáveis de ambiente do bootstrap local. `FeatureManager` depende da injeção de dependência. Você pode registrar os serviços de gerenciamento de recursos usando convenções padrão:

```java
private FeatureManager featureManager;

public HelloController(FeatureManager featureManager) {
    this.featureManager = featureManager;
}
```

Recomendamos que você mantenha os sinalizadores de recurso fora do aplicativo e gerencie-os separadamente. Isso permite que você modifique os Estados de sinalizador a qualquer momento e faça com que essas alterações entrem em vigor no aplicativo imediatamente. A configuração de aplicativo fornece um local centralizado para organizar e controlar todos os sinalizadores de recursos por meio de uma interface do usuário do portal dedicada. A configuração de aplicativo também fornece os sinalizadores para seu aplicativo diretamente por meio de suas bibliotecas de cliente Spring boot.

A maneira mais fácil de conectar seu aplicativo Spring boot à configuração do aplicativo é por meio do provedor de configuração:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
    <version>1.1.0.M4</version>
</dependency>
```

## <a name="feature-flag-declaration"></a>Declaração de sinalizador de recurso

Cada sinalizador de recurso tem duas partes: um nome e uma lista de um ou mais filtros que são usados para avaliar se o estado de um recurso está *ativado* (ou seja, quando seu valor é `True`). Um filtro define um caso de uso para quando um recurso deve ser ativado.

Quando um sinalizador de recurso tem vários filtros, a lista de filtros é percorrida na ordem até que um dos filtros determine que o recurso deve ser habilitado. Nesse ponto, o sinalizador do recurso está *ativado*e os resultados restantes do filtro são ignorados. Se nenhum filtro indicar que o recurso deve ser habilitado, o sinalizador de recurso será *desativado*.

O Gerenciador de recursos dá suporte a *Application. yml* como uma fonte de configuração para sinalizadores de recurso. O exemplo a seguir mostra como configurar sinalizadores de recurso em um arquivo YAML:

```yml
feature-management:
  featureSet:
    features:
      FeatureA: true
      FeatureB: false
      FeatureC:
        EnabledFor:
          -
            name: Percentage
            parameters:
              value: 50
```

Por convenção, a seção `feature-management` deste documento YML é usada para configurações de sinalizador de recurso. O exemplo anterior mostra três sinalizadores de recurso com seus filtros definidos na propriedade `EnabledFor`:

* `FeatureA` está *on*.
* `FeatureB` está *desativado*.
* `FeatureC` especifica um filtro chamado `Percentage` com uma propriedade `Parameters`. `Percentage` é um filtro configurável. Neste exemplo, `Percentage` especifica uma probabilidade de 50% para que o sinalizador de `FeatureC` seja *ativado*.

## <a name="feature-flag-checks"></a>Verificações de sinalizador de recurso

O padrão básico do gerenciamento de recursos é verificar primeiro se um sinalizador de recurso está definido como *on*. Nesse caso, o Gerenciador de recursos executa as ações que o recurso contém. Por exemplo:

```java
private FeatureManager featureManager;
...
if (featureManager.isEnabled("FeatureA"))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Injeção de dependência

No Spring boot, você pode acessar o Gerenciador de recursos `FeatureManager` por meio da injeção de dependência:

```java
@Controller
@ConfigurationProperties("controller")
public class HomeController {
    private FeatureManager featureManager;

    public HomeController(FeatureManager featureManager) {
        this.featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>Ações do controlador

Em controladores MVC, você usa o atributo `@FeatureGate` para controlar se uma ação específica está habilitada. A ação `Index` a seguir requer que `FeatureA` esteja *ativada* para que possa ser executada:

```java
@GetMapping("/")
@FeatureGate(feature = "FeatureA")
public String index(Model model) {
    ...
}
```

Quando um controlador MVC ou uma ação é bloqueada porque o sinalizador de recurso de controle está *desativado*, uma interface `IDisabledFeaturesHandler` registrada é chamada. A interface padrão `IDisabledFeaturesHandler` retorna um código de status 404 para o cliente sem corpo de resposta.

## <a name="mvc-filters"></a>Filtros MVC

Você pode configurar filtros MVC para que eles sejam ativados com base no estado de um sinalizador de recurso. O código a seguir adiciona um filtro MVC chamado `FeatureFlagFilter`. Esse filtro será disparado no pipeline MVC somente se `FeatureA` estiver habilitado.

```java
@Component
public class FeatureFlagFilter implements Filter {

    @Autowired
    private FeatureManager featureManager;

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        if(!featureManager.isEnabled("FeatureA")) {
            chain.doFilter(request, response);
            return;
        }
        ...
        chain.doFilter(request, response);
    }
}
```

## <a name="routes"></a>Rotas

Você pode usar sinalizadores de recurso para redirecionar rotas. O código a seguir redirecionará um usuário do `FeatureA` está habilitado:

```java
@GetMapping("/redirect")
@FeatureGate(feature = "FeatureA", fallback = "/getOldFeature")
public String getNewFeature() {
    // Some New Code
}

@GetMapping("/getOldFeature")
public String getOldFeature() {
    // Some New Code
}
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a implementar sinalizadores de recurso em seu aplicativo Spring boot usando as bibliotecas `spring-cloud-azure-feature-management-web`. Para obter mais informações sobre o suporte ao gerenciamento de recursos no Spring boot e na configuração do aplicativo, consulte os seguintes recursos:

* [Código de exemplo do sinalizador de recursos do Spring boot](/azure/azure-app-configuration/quickstart-feature-flag-spring-boot)
* [Gerir sinalizadores de funcionalidades](./manage-feature-flags.md)
