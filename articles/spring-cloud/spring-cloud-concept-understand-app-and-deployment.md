---
title: Compreenda a aplicação e a implementação em Azure Spring Cloud
description: A distinção entre aplicação e implantação em Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 07/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 81e1925810f374da6f02bf6c3a013b00b5bb9a2c
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/17/2020
ms.locfileid: "88264057"
---
# <a name="understand-app-and-deployment-in-azure-spring-cloud"></a>Compreenda a aplicação e a implementação em Azure Spring Cloud

**App** e **Implementação** são os dois conceitos-chave no modelo de recursos da Azure Spring Cloud. Em Azure Spring Cloud, uma *App* é uma abstração de uma aplicação de negócios ou um microserviço.  Uma versão de código ou binário implementada à medida que a *App* funciona numa *Implementação*.

 ![Apps e Implementações](./media/spring-cloud-app-and-deployment/app-deployment-rev.png)

O nível padrão da Cloud Azure Spring permite que uma Aplicação tenha uma implementação de produção e uma colocação de encenação, para que possa fazer facilmente a implantação azul/verde.

## <a name="app"></a>Aplicação
As seguintes funcionalidades/propriedades são definidas ao nível da App.

| Enumeração | Definição |
|:--|:----------------|
| Públicos</br>Ponto final | O URL para aceder à app |
| Personalizado</br>Domínio | REGISTO CNAME que assegura o domínio personalizado |
| Serviço</br>Enlace | Propriedades de configuração de ligação definidas no function.jsno ficheiro e no atributo *ServiceBusTrigger* |
| Gerido</br>Identidade | Identidade gerida pelo Azure Ative Directory permite que a sua app aceda facilmente a outros recursos protegidos pela Azure, como o Azure Key Vault |
| Persistente</br>Armazenamento | Definição que permite que os dados persistam para além do reinício da aplicação |

## <a name="deployment"></a>Implementação

As seguintes funcionalidades/propriedades são definidas ao nível de Implantação e serão trocadas ao trocar a produção/paragem de produção/paragem.

| Enumeração | Definição |
|:--|:----------------|
| CPU | Número de vcores por instância de App |
| Memória | Definição que atribui memória para escalar ou escalar implementações |
| Instância</br>de palavras | O número de instâncias de aplicações, definidas manualmente ou automaticamente |
| Escala automática | Contagem de instâncias de escala automaticamente com base em regras e horários predefinidos |
| JVM</br>Opções | definição: JAVA_OPTS |
| Ambiente</br>Variáveis | Configurações que se aplicam a todo o ambiente Azure Spring Cloud |
| Runtime</br>Versão | Java 8/Java 11|

## <a name="restrictions"></a>Restrições

* **Uma Aplicação deve ter uma implantação de produção**: A eliminação de uma produção é bloqueada pela API. Deve ser trocado para encenação antes de apagar.
* **Uma Aplicação pode ter no máximo duas Implementações**: Criar mais de duas implementações é bloqueado pela API. Desloque o seu novo binário para a produção ou implantação de encenação existentes.
* **A gestão da implementação não está disponível no Basic Tier**: Use o nível padrão para a capacidade de implantação Blue-Green.

## <a name="see-also"></a>Consulte também
* [Crie um ambiente de encenação em Azure Spring Cloud](spring-cloud-howto-staging-environment.md)