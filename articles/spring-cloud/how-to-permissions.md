---
title: Como - Use permissões em Azure Spring Cloud
description: Este artigo mostra-lhe como criar papéis personalizados para permissões em Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0c0b3bd81e0f73a7879382e28516378bd722bc17
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878486"
---
# <a name="how-to-use-permissions-in-azure-spring-cloud"></a>Como usar permissões em Azure Spring Cloud
Este artigo mostra-lhe como criar papéis personalizados que delegam permissões aos recursos da Azure Spring Cloud. As funções personalizadas estendem [as funções incorporadas do Azure](../role-based-access-control/built-in-roles.md) com várias permissões de stock.

Implementaremos as seguintes funções personalizadas:

* **Papel de desenvolvedor:** 
    * Implementar
    * Teste
    * Reiniciar aplicativos
    * Pode aplicar e fazer alterações nas configurações de aplicações no repositório git
    * Pode obter o fluxo de log
* **Ops - Engenharia de Fiabilidade do Local:** 
    * Reiniciar aplicativos
    * Obtenha fluxos de registo
    * Não é possível fazer alterações em apps ou configurações
* **Papel de Azure Pipelines/Jenkins/Github Actions**:
    * Pode realizar criar, ler, atualizar, apagar operações
    * Pode criar e configurar tudo em Azure Spring Cloud e aplicativos dentro da instância de serviço: Azure Pipelines, Jenkins ou GitHub Actions, usando modelos Terraform ou ARM

## <a name="define-developer-role"></a>Definir papel de Desenvolvedor

O papel do desenvolvedor inclui permissões para reiniciar apps e ver os seus streams de registo, mas não pode fazer alterações nas apps, configuração.

### <a name="navigate-subscription-and-resource-group-access-control-iam"></a>Navegar no controlo de subscrição e grupo de recursos Access (IAM)

Siga estes passos para começar a definir um papel.

1. No portal Azure, abra o grupo de subscrição e recursos onde pretende que a função personalizada seja atribuível.
2. Controlo **de acesso aberto (IAM)**.
3. Clique em **+ Adicionar**.
4. Clique **em Adicionar o papel personalizado.**
5. Clique em **Seguinte**.

   ![Criar função personalizada](media/spring-cloud-permissions/create-custom-role.png)

6. Clique **em Adicionar permissões**.

   ![Adicionar permissões começam](media/spring-cloud-permissions/add-permissions.png)

### <a name="search-for-azure-spring-cloud-permissions"></a>Procure permissões em Azure Spring Cloud:
7. Na caixa de pesquisa, procure *Microsoft.app.*
Selecione *Microsoft Azure Spring Cloud*.

   ![Selecione Azure Spring Cloud](media/spring-cloud-permissions/spring-cloud-permissions.png)

8. Selecione as permissões para a função de desenvolvedor:

A partir de **Microsoft.AppPlatform/Spring,** selecione:
* Escrever : Criar ou atualizar exemplo de serviço Azure Spring Cloud
* Leia: Obtenha a exemplo do serviço Azure Spring Cloud
* Outros : List Azure Spring Cloud service instance test keys

A partir do **Microsoft.AppPlatform/Spring/apps**, selecione:
* Leia: Leia a aplicação Microsoft Azure Spring Cloud
* Outros : Obter Microsoft Azure Spring Cloud application application upload URL

A partir do **Microsoft.AppPlatform/Spring/apps/bindings**, selecione:
* Leia: Leia a ligação da aplicação Microsoft Azure Spring Cloud

A partir do **Microsoft.AppPlatform/Spring/apps/implementações,** selecione:
* Write : Write Microsoft Azure Spring Cloud application deployment
* Leia: Leia a implementação da aplicação Microsoft Azure Spring Cloud
* Outros : Inicie a implementação da aplicação Microsoft Azure Spring Cloud
* Outros : Pare a implementação da aplicação Microsoft Azure Spring Cloud
* Outros : Reinicie a implementação da aplicação microsoft Azure Spring Cloud
* Outros : Obtenha URL de ficheiro de registo de implementação de aplicação da Microsoft Azure Spring Cloud

A partir do **Microsoft.AppPlatform/Spring/apps/domínios,** selecione:
* Leia: Leia: Leia o domínio personalizado da aplicação Microsoft Azure Spring Cloud

A partir do **Microsoft.AppPlatform/Spring/certificates,** selecione:
* Leia: Leia o certificado microsoft Azure Spring Cloud

A partir do **Microsoft.AppPlatform/locations/operationResults/Spring,** selecione:
* Leia: Leia : Leia o resultado da operação

A partir do **Microsoft.AppPlatform/locations/operationStatus/operationId**, selecione:
* Leia: Ler o estado da operação

    [![Criar permissões ](media/spring-cloud-permissions/developer-permissions-box.png) de Desenvolvimento](media/spring-cloud-permissions/developer-permissions-box.png#lightbox)

9. Clique em **Adicionar**.

10. Reveja as permissões.

11. Clique em **Rever e criar**.

## <a name="define-devops-engineer-role"></a>Definir papel de engenheiro DevOps
Este procedimento define uma função com permissões para implementar, testar e reiniciar aplicações Azure Spring Cloud.

1. Repita o procedimento para navegar na subscrição, no grupo de recursos e no controlo de acesso ao acesso (IAM).
2. Selecione as permissões para a função de engenheiro DevOps:

A partir de **Microsoft.AppPlatform/Spring,** selecione:
* Escrever : Criar ou atualizar exemplo de serviço Azure Spring Cloud
* Excluir : Excluir a página do serviço Azure Spring Cloud
* Leia: Obtenha a exemplo do serviço Azure Spring Cloud
* Outros : Ativar o ponto final do teste de instância de serviço da Azure Spring Cloud
* Outros : Desativar o ponto final do teste de instância de serviço da Cloud Azure Spring
* Outros : List Azure Spring Cloud service instance test keys
* Outros : Regenerar a chave de teste de placa de serviço da Nuvem de primavera regenerada

A partir do **Microsoft.AppPlatform/Spring/apps**, selecione:
* Write : Write Microsoft Azure Spring Cloud application
* Excluir : Eliminar a aplicação Microsoft Azure Spring Cloud
* Leia: Leia a aplicação Microsoft Azure Spring Cloud
* Outros : Obter Microsoft Azure Spring Cloud application application upload URL
* Outros : Validar o domínio personalizado da aplicação Microsoft Azure Spring Cloud

A partir do **Microsoft.AppPlatform/Spring/apps/bindings**, selecione:
* Write : Write Microsoft Azure Spring Cloud application binding
* Excluir : Eliminar a aplicação da Microsoft Azure Spring Cloud
* Leia: Leia a ligação da aplicação Microsoft Azure Spring Cloud

A partir do **Microsoft.AppPlatform/Spring/apps/implementações,** selecione:
* Write : Write Microsoft Azure Spring Cloud application deployment
* Excluir : Eliminar a aplicação Azure Spring Cloud
* Leia: Leia a implementação da aplicação Microsoft Azure Spring Cloud
* Outros : Inicie a implementação da aplicação Microsoft Azure Spring Cloud
* Outros : Pare a implementação da aplicação Microsoft Azure Spring Cloud
* Outros : Reinicie a implementação da aplicação microsoft Azure Spring Cloud
* Outros : Obtenha URL de ficheiro de registo de implementação de aplicação da Microsoft Azure Spring Cloud

A partir do **Microsoft.AppPlatform/Spring/apps/deployments/skus,** selecione:
* Leia: Implementação de aplicação de lista disponível

A partir de **Microsoft.AppPlatform/locations,** selecione:
* Outros : Verificar disponibilidade de nome

De Microsoft.AppPlatform/locations/operationResults/Spring select: Read : Leia : Leia o resultado da operação

A partir do **Microsoft.AppPlatform/locations/operationStatus/operationId**, selecione:
* Leia: Ler o estado da operação

A partir de **Microsoft.AppPlatform/skus,** selecione:
* Leia : Lista de skus disponíveis

   [Permissões de ![ ](media/spring-cloud-permissions/dev-ops-permissions.png) dev/Op ](media/spring-cloud-permissions/dev-ops-permissions.png#lightbox)

3. Clique em **Adicionar**.

4. Reveja as permissões.

5. Clique em **Rever e criar**.

## <a name="define-ops---site-reliability-engineering-role"></a>Definir Ops - Função de Engenharia de Fiabilidade do Local
Este procedimento define uma função com permissões para implementar, testar e reiniciar aplicações Azure Spring Cloud.

1. Repita o procedimento para navegar na subscrição, no grupo de recursos e no controlo de acesso ao acesso (IAM).

2. Selecione as permissões para a função de Engenharia de Fiabilidade do Local:

A partir de **Microsoft.AppPlatform/Spring,** selecione:
* Leia: Obtenha a exemplo do serviço Azure Spring Cloud
* Outros : List Azure Spring Cloud service instance test keys

A partir do **Microsoft.AppPlatform/Spring/apps**, selecione:
* Leia: Leia a aplicação Microsoft Azure Spring Cloud

A partir do **Microsoft.AppPlatform/apps/implementações,** selecione:
* Leia: Leia a implementação da aplicação Microsoft Azure Spring Cloud
* Outros : Inicie a implementação da aplicação Microsoft Azure Spring Cloud
* Outros : Pare a implementação da aplicação Microsoft Azure Spring Cloud
* Outros : Reinicie a implementação da aplicação microsoft Azure Spring Cloud

A partir do **Microsoft.AppPlatform/locations/operationResults/Spring,** selecione:
* Leia: Leia : Leia o resultado da operação

A partir do **Microsoft.AppPlatform/locations/operationStatus/operationId**, selecione:
* Leia: Ler o estado da operação

   [![Permissões de Ops/SRE ](media/spring-cloud-permissions/ops-sre-permissions.png)](media/spring-cloud-permissions/ops-sre-permissions.png#lightbox)

3. Clique em **Adicionar**.

4. Reveja as permissões.

5. Clique em **Rever e criar**.

## <a name="define-azure-pipelinesprovisioning-role"></a>Definir Gasodutos/Provisionamento de Azure
Este papel de Jenkins/Github Actions pode criar e configurar tudo em Azure Spring Cloud e apps com uma instância de serviço. Esta função é para a libertação ou implementação do código.

1. Repita o procedimento para navegar na subscrição, no grupo de recursos e no controlo de acesso ao acesso (IAM).

2. Abra as opções **permissões.**

3. Selecione as permissões para a função Azure Pipelines/Provisioning:
  
A partir de **Microsoft.AppPlatform/Spring,** selecione:
* Escrever : Criar ou atualizar exemplo de serviço Azure Spring Cloud
* Excluir : Excluir a página do serviço Azure Spring Cloud
* Leia: Obtenha a exemplo do serviço Azure Spring Cloud
* Outros : Ativar o ponto final do teste de instância de serviço da Azure Spring Cloud
* Outros : Desativar o ponto final do teste de instância de serviço da Cloud Azure Spring
* Outros : List Azure Spring Cloud service instance test keys
* Outros : Regenerar a chave de teste de placa de serviço da Nuvem de primavera regenerada

A partir do **Microsoft.AppPlatform/Spring/apps**, selecione:
* Write : Write Microsoft Azure Spring Cloud application
* Excluir : Eliminar a aplicação Microsoft Azure Spring Cloud
* Leia: Leia a aplicação Microsoft Azure Spring Cloud
* Outros : Obter Microsoft Azure Spring Cloud application application upload URL
* Outros : Validar o domínio personalizado da aplicação Microsoft Azure Spring Cloud

A partir do **Microsoft.AppPlatform/Spring/apps/bindings**, selecione:
* Write : Write Microsoft Azure Spring Cloud application binding
* Excluir : Eliminar a aplicação da Microsoft Azure Spring Cloud
* Leia: Leia a ligação da aplicação Microsoft Azure Spring Cloud

A partir do **Microsoft.AppPlatform/Spring/apps/implementações,** selecione:
* Write : Write Microsoft Azure Spring Cloud application deployment
* Excluir : Eliminar a aplicação Azure Spring Cloud
* Leia: Leia a implementação da aplicação Microsoft Azure Spring Cloud
* Outros : Inicie a implementação da aplicação Microsoft Azure Spring Cloud
* Outros : Pare a implementação da aplicação Microsoft Azure Spring Cloud
* Outros : Reinicie a implementação da aplicação microsoft Azure Spring Cloud
* Outros : Obtenha URL de ficheiro de registo de implementação de aplicação da Microsoft Azure Spring Cloud

A partir de **Microsoft.AppPlatform/skus,** selecione:
* Leia : Lista de skus disponíveis

A partir de **Microsoft.AppPlatform/locations,** selecione:
* Outros : Verificar disponibilidade de nome

A partir do **Microsoft.AppPlatform/locations/operationResults/Spring,** selecione:
* Leia: Leia : Leia o resultado da operação

A partir do **Microsoft.AppPlatform/locations/operationStatus/operationId**, selecione:
* Leia: Ler o estado da operação

A partir de **Microsoft.AppPlatform/skus,** selecione:
* Leia : Lista de skus disponíveis

   [![Permissões de gasodutos ](media/spring-cloud-permissions/pipelines-permissions-box.png)](media/spring-cloud-permissions/pipelines-permissions-box.png#lightbox)  

4. Clique em **Adicionar**.

5. Reveja as permissões.

6. Clique em **Rever e criar**.


## <a name="see-also"></a>Ver também
* [Criar ou atualizar funções personalizadas do Azure com o portal do Azure](../role-based-access-control/custom-roles-portal.md)

Para obter mais informações sobre três métodos que definem permissões personalizadas consulte:
* [Clonar um papel](../role-based-access-control/custom-roles-portal.md#clone-a-role)
* [Começar do zero](../role-based-access-control/custom-roles-portal.md#start-from-scratch)
* [Comece pelo JSON](../role-based-access-control/custom-roles-portal.md#start-from-json)