---
title: Tutorial para usar bandeiras de recurso numa aplicação spring boot - Configuração de aplicações Azure [ Configuração de aplicações de Azure ] Microsoft Docs
description: Neste tutorial, você aprende a implementar bandeiras de recurso em aplicativos Spring Boot.
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
ms.openlocfilehash: d519ac44d617f725aa9b3d3f11671122bd9477bc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "78944317"
---
# <a name="tutorial-use-feature-flags-in-a-spring-boot-app"></a>Tutorial: Use bandeiras de recurso numa aplicação spring boot

As bibliotecas de Gestão de Recursos de Base de Botas de primavera fornecem suporte para a implementação de bandeiras de recurso numa aplicação spring boot. Estas bibliotecas permitem-lhe adicionar declarativamente bandeiras de recurso ao seu código.

As bibliotecas de Gestão de Recursos também gerem ciclos de vida de bandeira nos bastidores. Por exemplo, as bibliotecas refrescam e cache bandeira, ou garantem que um estado de bandeira seja imutável durante uma chamada de pedido. Além disso, a biblioteca Spring Boot oferece integrações, incluindo ações de controlador MVC, rotas e middleware.

As [bandeiras de recurso Add para uma aplicação Spring Boot Quickstart](./quickstart-feature-flag-spring-boot.md) mostram várias formas de adicionar bandeiras de funcionalidade seletiva numa aplicação spring boot. Este tutorial explica estes métodos com mais detalhes.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Adicione bandeiras de características em partes chave da sua aplicação para controlar a disponibilidade da funcionalidade.
> * Integre com a Configuração de Aplicações quando estiver a usá-la para gerir as bandeiras de recurso.

## <a name="set-up-feature-management"></a>Configurar a gestão de recursos

O gestor de `FeatureManager` funcionalidades Spring Boot recebe bandeiras do sistema de configuração nativa do quadro. Como resultado, pode definir as bandeiras de características da sua aplicação utilizando qualquer fonte de configuração que a Spring Boot suporte, incluindo o ficheiro *de bootstrap.yml* local ou variáveis ambientais. `FeatureManager`depende da injeção de dependência. Pode registar os serviços de gestão de funcionalidades utilizando convenções padrão:

```java
private FeatureManager featureManager;

public HelloController(FeatureManager featureManager) {
    this.featureManager = featureManager;
}
```

Recomendamos que mantenha as bandeiras fora da aplicação e as gere separadamente. Ao fazê-lo, permite-lhe modificar os estados de bandeira a qualquer momento e fazer com que essas alterações entrem imediatamente em vigor na aplicação. A Configuração da Aplicação fornece um local centralizado para organizar e controlar todas as suas bandeiras através de um portal dedicado UI. A Configuração da Aplicação também entrega as bandeiras à sua aplicação diretamente através das bibliotecas de clientes spring boot.

A forma mais fácil de ligar a sua aplicação Spring Boot à Configuração de Aplicações é através do fornecedor de configuração:

### <a name="spring-cloud-11x"></a>Nuvem de primavera 1.1.x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.1.2</version>
</dependency>
```

### <a name="spring-cloud-12x"></a>Nuvem de primavera 1.2.x

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-feature-management-web</artifactId>
    <version>1.2.2</version>
</dependency>
```

## <a name="feature-flag-declaration"></a>Declaração de bandeira de recurso

Cada bandeira de recurso tem duas partes: um nome e uma lista de um ou *on* mais filtros que são `True`usados para avaliar se o estado de uma característica está ligado (isto é, quando o seu valor está). Um filtro define um caso de utilização para quando uma função deve ser ligada.

Quando uma bandeira de características tem vários filtros, a lista de filtros é atravessada para poder até que um dos filtros determine que a funcionalidade deve ser ativada. Nessa altura, a bandeira da característica está *asereda,* e os resultados restantes do filtro são ignorados. Se nenhum filtro indicar que a função deve ser ativada, a bandeira de características está *desligada*.

O gestor de funcionalidades suporta a *aplicação.yml* como fonte de configuração para as bandeiras de recurso. O exemplo que se segue mostra como configurar bandeiras de recurso num ficheiro YAML:

```yml
feature-management:
  feature-set:
    feature-a: true
    feature-b: false
    feature-c:
      enabled-for:
        -
          name: Percentage
          parameters:
            value: 50
```

Por convenção, a `feature-management` secção deste documento YML é utilizada para configurações de bandeira de características. O exemplo anterior mostra três bandeiras de `EnabledFor` características com os seus filtros definidos na propriedade:

* `feature-a`está *ligado.*
* `feature-b`está *desligado.*
* `feature-c`especifica um filtro `Percentage` nomeado `parameters` com uma propriedade. `Percentage`é um filtro configurável. Neste exemplo, `Percentage` especifica uma probabilidade de 50% para a `feature-c` bandeira estar em *.*

## <a name="feature-flag-checks"></a>Verificações de bandeiras de características

O padrão básico de gestão de recursos é verificar primeiro se uma bandeira de característicaestá definida *.* Em caso afirmativo, o gestor de funcionalidades executa as ações que a funcionalidade contém. Por exemplo:

```java
private FeatureManager featureManager;
...
if (featureManager.isEnabledAsync("feature-a").block()) {
    // Run the following code
}
```

## <a name="dependency-injection"></a>Injeção de dependência

No Spring Boot, pode aceder `FeatureManager` ao gestor de funcionalidades através de uma injeção de dependência:

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

Nos controladores MVC, `@FeatureGate` utiliza-se o atributo para controlar se uma ação específica está ativada. As `Index` seguintes `feature-a` ações têm de ser *tomadas* antes de poder ser executada:

```java
@GetMapping("/")
@FeatureGate(feature = "feature-a")
public String index(Model model) {
    ...
}
```

Quando um controlador ou ação mvc é bloqueado porque a `IDisabledFeaturesHandler` bandeira da característica de controlo está *desligada,* uma interface registada é chamada. A `IDisabledFeaturesHandler` interface predefinida devolve um código de estado 404 ao cliente sem corpo de resposta.

## <a name="mvc-filters"></a>Filtros MVC

Pode configurar filtros MVC para que sejam ativados com base no estado de uma bandeira de recurso. O seguinte código adiciona um `FeatureFlagFilter`filtro MVC chamado . Este filtro só é acionado dentro `feature-a` do gasoduto MVC se estiver ativado.

```java
@Component
public class FeatureFlagFilter implements Filter {

    @Autowired
    private FeatureManager featureManager;

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        if(!featureManager.isEnabledAsync("feature-a").block()) {
            chain.doFilter(request, response);
            return;
        }
        ...
        chain.doFilter(request, response);
    }
}
```

## <a name="routes"></a>Rotas

Pode utilizar bandeiras de recurso para redirecionar rotas. O seguinte código redirecionará `feature-a` um utilizador de:

```java
@GetMapping("/redirect")
@FeatureGate(feature = "feature-a", fallback = "/getOldFeature")
public String getNewFeature() {
    // Some New Code
}

@GetMapping("/getOldFeature")
public String getOldFeature() {
    // Some New Code
}
```

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a implementar bandeiras de recurso `spring-cloud-azure-feature-management-web` na sua aplicação Spring Boot utilizando as bibliotecas. Para obter mais informações sobre o suporte de gestão de funcionalidades na Configuração de Boot e App spring, consulte os seguintes recursos:

* [Código de amostra de porta-botas de mola](/azure/azure-app-configuration/quickstart-feature-flag-spring-boot)
* [Gerir sinalizadores de funcionalidades](./manage-feature-flags.md)
