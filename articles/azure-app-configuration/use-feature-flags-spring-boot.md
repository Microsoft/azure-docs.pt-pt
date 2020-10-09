---
title: Tutorial para usar bandeiras de recurso numa aplicação De Boot de primavera - Configuração de Aplicação Azure / Microsoft Docs
description: Neste tutorial, você aprende a implementar bandeiras de recurso em aplicativos Spring Boot.
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: java
ms.topic: tutorial
ms.date: 09/26/2019
ms.author: mametcal
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 83c437cb613e3dad04dee17f0f67040532066c3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87326601"
---
# <a name="tutorial-use-feature-flags-in-a-spring-boot-app"></a>Tutorial: Use bandeiras de recurso numa aplicação de Boot de primavera

As bibliotecas de Gestão de Recursos Core de Mola fornecem suporte para implementar bandeiras de funcionalidades numa aplicação Boot Spring. Estas bibliotecas permitem-lhe adicionar declarativamente bandeiras de características ao seu código.

As bibliotecas de Gestão de Recursos também gerem ciclos de vida de bandeira de recurso nos bastidores. Por exemplo, as bibliotecas renovam e cache estados de bandeira, ou garantem que um estado de bandeira seja imutável durante uma chamada de pedido. Além disso, a biblioteca Spring Boot oferece integrações, incluindo ações de controlador MVC, rotas e middleware.

O [Add feature flags a uma aplicação Spring Boot Quickstart](./quickstart-feature-flag-spring-boot.md) mostra várias formas de adicionar bandeiras de recurso numa aplicação Boot de Mola. Este tutorial explica estes métodos com mais detalhes.

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Adicione bandeiras de funcionalidades em partes chave da sua aplicação para controlar a disponibilidade do recurso.
> * Integre-se com a Configuração da Aplicação quando estiver a usá-lo para gerir as bandeiras de funcionalidades.

## <a name="set-up-feature-management"></a>Configurar gestão de recursos

O gestor de funcionalidades Spring Boot `FeatureManager` recebe bandeiras de recurso do sistema de configuração nativa da estrutura. Como resultado, pode definir as bandeiras de funcionalidade da sua aplicação utilizando qualquer fonte de configuração que o Boot de mola suporte, incluindo o ficheiro *bootstrap.yml* local ou variáveis ambientais. `FeatureManager` depende da injeção de dependência. Pode registar os serviços de gestão de recursos utilizando convenções padrão:

```java
private FeatureManager featureManager;

public HelloController(FeatureManager featureManager) {
    this.featureManager = featureManager;
}
```

Recomendamos que mantenha as bandeiras de características fora da aplicação e as gere separadamente. Ao fazê-lo, permite-lhe modificar estados de bandeira a qualquer momento e fazer com que essas alterações produzam efeitos imediatamente na aplicação. A Configuração de Aplicações oferece um local centralizado para organizar e controlar todas as suas bandeiras de funcionalidades através de um portal dedicado UI. A Configuração da Aplicação também entrega as bandeiras à sua aplicação diretamente através das suas bibliotecas de clientes Spring Boot.

A forma mais fácil de ligar a sua aplicação Boot de Mola à Configuração de Aplicações é através do fornecedor de configuração:

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

## <a name="feature-flag-declaration"></a>Declaração de bandeira de característica

Cada bandeira de características tem duas partes: um nome e uma lista de um ou mais filtros que são usados para avaliar se o estado de uma característica está *ligado* (isto é, quando o seu valor `True` é). Um filtro define uma caixa de utilização para quando uma função deve ser ligada.

Quando uma bandeira de recurso tem vários filtros, a lista de filtros é percorrida até que um dos filtros determine que a função deve ser ativada. Nessa altura, a bandeira da funcionalidade está *acesa*, e os restantes resultados do filtro são ignorados. Se nenhum filtro indicar que a função deve ser ativada, a bandeira de características está *desligada*.

O gestor de funcionalidades suporta *a aplicação.yml* como fonte de configuração para bandeiras de funcionalidades. O exemplo a seguir mostra como configurar bandeiras de recurso num ficheiro YAML:

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

Por convenção, a `feature-management` secção deste documento YML é utilizada para configurações de bandeira de recurso. O exemplo anterior mostra três bandeiras de características com os seus filtros definidos na `EnabledFor` propriedade:

* `feature-a`está *ligado.*
* `feature-b`está *desligado.*
* `feature-c` especifica um filtro nomeado `Percentage` com uma `parameters` propriedade. `Percentage` é um filtro configurável. Neste exemplo, `Percentage` especifica uma probabilidade de 50% para a bandeira estar `feature-c` *em*.

## <a name="feature-flag-checks"></a>Verificações de bandeira de recurso

O padrão básico de gestão de recursos é verificar primeiro se uma bandeira de recurso está definida para *.* Em caso afirmativo, o gestor de funcionalidades executa então as ações que a funcionalidade contém. Por exemplo:

```java
private FeatureManager featureManager;
...
if (featureManager.isEnabledAsync("feature-a").block()) {
    // Run the following code
}
```

## <a name="dependency-injection"></a>Injeção de dependência

No Boot Spring, pode aceder ao gestor de `FeatureManager` funcionalidades através da injeção de dependência:

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

## <a name="controller-actions"></a>Ações de controlador

Nos controladores MVC, utiliza-se o `@FeatureGate` atributo para controlar se uma ação específica está ativada. A seguinte `Index` ação deve ser `feature-a` *iniciada* antes de poder ser executada:

```java
@GetMapping("/")
@FeatureGate(feature = "feature-a")
public String index(Model model) {
    ...
}
```

Quando um controlador ou ação MVC é bloqueado porque a bandeira da função de controlo está *desligada,* uma interface registada `IDisabledFeaturesHandler` é chamada. A `IDisabledFeaturesHandler` interface predefinida devolve um código de estado 404 ao cliente sem organismo de resposta.

## <a name="mvc-filters"></a>Filtros MVC

Pode configurar filtros MVC para que sejam ativados com base no estado de uma bandeira de recurso. O seguinte código adiciona um filtro MVC chamado `FeatureFlagFilter` . Este filtro só é acionado dentro do gasoduto MVC se `feature-a` estiver ativado.

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

Pode utilizar bandeiras de recurso para redirecionar rotas. O seguinte código irá redirecionar um utilizador `feature-a` de:

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

Neste tutorial, aprendeu a implementar bandeiras de funcionalidades na sua aplicação Boot De primavera utilizando as `spring-cloud-azure-feature-management-web` bibliotecas. Para obter mais informações sobre o suporte à gestão de funcionalidades na Configuração de Boot e App de Mola, consulte os seguintes recursos:

* [Código de amostra de bandeira de mola](/azure/azure-app-configuration/quickstart-feature-flag-spring-boot)
* [Gerir sinalizadores de funcionalidades](./manage-feature-flags.md)
