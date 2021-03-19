---
title: Gerir e monitorizar aplicações com o Spring Boot Actuator
description: Aprenda a gerir e monitorizar a aplicação com o Actuador de Bota de primavera.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 93fd286aa76a0409a515abbf8c9dabd88a9a65c4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90904288"
---
# <a name="manage-and-monitor-app-with-azure-spring-boot-actuator"></a>Gerir e monitorizar aplicações com o Spring Boot Actuator

**Este artigo aplica-se a:** ✔️ Java

Depois de implementar um novo binário na sua aplicação, poderá querer verificar a funcionalidade e ver informações sobre a sua aplicação de execução. Este artigo explica como aceder à API a partir de um ponto final de teste fornecido pela Azure Spring Cloud e expor as funcionalidades prontas para a produção para a sua aplicação.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que tem uma aplicação Spring Boot 2.x que pode ser implantada e iniciada com sucesso no serviço Azure Spring Cloud.  Ver [Quickstart: Lançar uma aplicação Azure Spring Cloud existente utilizando o portal Azure](spring-cloud-quickstart.md)

## <a name="verify-app-through-test-endpoint"></a>Verificar aplicativo através do ponto final do teste
1. Vá ao **painel de aplicações** e clique na sua aplicação para introduzir a página geral da aplicação.

1. No **painel de visão** geral, deverá ver **o Ponto final de teste**.  Aceda a este ponto final a partir da linha de comando ou navegador e observe a resposta da API.

1. Note o **ponto final de teste** URI que será usado na secção seguinte.

>[!TIP]
> * Se a aplicação devolver uma página de primeira linha e referenciar outros ficheiros através de um caminho relativo, confirme que o seu ponto final de teste termina com um corte (/). Isto garantirá que o ficheiro CSS esteja carregado corretamente.
> * Se visualizar a sua API a partir de um brower e o seu navegador exigir que introduza credenciais de login para visualizar a página, utilize o [descodificar o URL](https://www.urldecoder.org/) para descodificar o seu ponto final de teste. O descodifica URL devolve um URL no formulário "https:// \<username> : \<password> @ \<cluster-name> .test.azureapps.io/ \<app-name> / \<deployment-name> ".  Utilize este formulário para aceder ao seu ponto final.

## <a name="add-actuator-dependency"></a>Adicionar dependência do actuador

Para adicionar o actuador a um projeto baseado em Maven, adicione a dependência 'Starter':

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependencies>
```

Compile o novo binário e coloque-o na sua aplicação.

## <a name="enable-production-ready-features"></a>Ativar funcionalidades prontas para a produção
Os pontos finais do actuador permitem monitorizar e interagir com a sua aplicação. Por predefinição, a aplicação Spring Boot expõe `health` e `info` pontos finais para mostrar informações arbitrárias de aplicações e informações de saúde.

Para observar a configuração e o ambiente configurável, precisamos de ativar `env` e `configgrops` pontos finais também.

1. Vá ao painel **de visualização geral** da aplicação, clique em **Configuração** no menu de definição, vá à página de configuração **de variáveis ambiente.**
1. Adicione as seguintes propriedades como no formulário "chave:valor". Este ambiente abrirá o ponto final do Actuador de primavera "env", "saúde", "informação".

   ```
   management.endpoints.web.exposure.include: env,health,info
   ```
1. Clique no botão **Guardar,** a sua aplicação reiniciará automaticamente e carregará as novas variáveis ambientais.

Agora pode voltar ao painel geral da aplicação e aguardar até que o Estado de Provisionamento mude para "Bem-sucedido".  Haverá mais de um caso em execução.

> [!Note] 
> Uma vez que exponha a aplicação ao público, estes pontos finais do actuador também estão expostos ao público. Você pode esconder todos os pontos finais, eliminando as variáveis `management.endpoints.web.exposure.include` ambientais, e definir `management.endpoints.web.exposure.exclude=*`

## <a name="view-the-actuator-endpoint-to-view-application-information"></a>Ver o ponto final do actuador para ver as informações da aplicação
1. Agora pode aceder ao url `"<test-endpoint>/actuator/"` para ver todos os pontos finais expostos pelo Actuador de Botas de Mola.
1. Aceda a url `"<test-endpoint>/actuator/env"` , pode ver perfis ativos utilizados pela app, e todas as variáveis ambientais carregadas.
1. Se quiser pesquisar um ambiente específico, pode aceder ao url  `"<test-endpoint>/actuator/env/{toMatch}"` para o visualizar.

Para ver todos os pontos finais incorporados, consulte [Exposing Endpoints](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html#production-ready-endpoints-exposing-endpoints)

## <a name="next-steps"></a>Passos seguintes

* [Compreenda as métricas para Azure Spring Cloud](spring-cloud-concept-metrics.md)
* [Compreender o estado das aplicações no Azure Spring Cloud](spring-cloud-concept-app-status.md)

