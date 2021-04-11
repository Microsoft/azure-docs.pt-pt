---
title: Acesso Config Servidor e Registo de Serviço
titleSuffix: Azure Spring Cloud
description: Como aceder ao Config Server e ao Service Registry Endpoints com o controlo de acesso baseado em funções do Azure Ative Directory.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 23e24e562ea6fa10eee82c54c9ab2a701dd10351
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106170086"
---
# <a name="access-config-server-and-service-registry"></a>Acesso Config Servidor e Registo de Serviço

Este artigo explica como aceder ao Servidor de Molas Config server e ao Registo de Serviços de Nuvem de primavera geridos pela Azure Spring Cloud utilizando o controlo de acesso baseado em funções (RBAC) do Azure Ative Directory (Azure AD).

## <a name="assign-role-to-azure-ad-usergroup-msi-or-service-principal"></a>Atribuir função ao utilizador/grupo AD AZure, MSI ou principal de serviço

Para utilizar a Azure AD e o RBAC tem de atribuir a função *Azure Spring Cloud Data Reader* a um utilizador, grupo ou principal de serviço pelo seguinte procedimento:

1. Aceda à página geral do serviço da sua instância de serviço.

2. Clique em **Controlo de Acesso (IAM)** para abrir a lâmina de controlo de acesso.

3. Clique no botão **Adicionar** e **adicione atribuições de funções** (a autorização pode ser necessária para adicionar).

4. Encontre e selecione O leitor de dados da nuvem de mola de *Azure* sob **função**.
5. Atribua acesso a `User, group, or service principal` ou de acordo com o tipo de `User assigned managed identity` utilizador. Procure e selecione o utilizador.  
6. Clique `Save`

   ![atribuir função](media/access-data-plane-aad-rbac/assign-data-reader-role.png)

## <a name="access-config-server-and-service-registry-endpoints"></a>Acesso Config Servidor e Registo de Serviços Pontos finais

Após a atribuição da função Azure Spring Cloud Data Reader, os clientes podem aceder ao Servidor Config da Cloud Spring e aos pontos finais do Registo de Serviços de Nuvem de primavera. Utilizar os seguintes procedimentos:

1. Obter um sinal de acesso. Depois de um utilizador AZure AD ser atribuído à função Azure Spring Cloud Data Reader, os clientes podem utilizar os seguintes comandos para iniciar sessão no Azure CLI com o utilizador, o principal do serviço ou a identidade gerida para obter um token de acesso. Para mais informações, consulte [Authenticate Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli). 

    ```azurecli
    az login
    az account get-access-token
    ```
2. Componha o ponto final. Suportamos pontos finais predefinidos do Servidor Config da Nuvem de primavera e do Registo de Serviços de Nuvem de primavera geridos pela Azure Spring Cloud. Para mais informações, consulte [os pontos finais prontos para a Produção.](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints) Os clientes também podem obter uma lista completa de pontos finais suportados do Servidor Config da Nuvem de primavera e do Registo de Serviços de Nuvem de primavera geridos pela Azure Spring Cloud, acedendo a pontos finais:

    * *https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/*
    * *https://SERVICE_NAME.svc.azuremicroservices.io/config/actuator/* 

3. Aceda ao ponto final composto com o token de acesso. Coloque o símbolo de acesso num cabeçalho para fornecer autorização.  Apenas o método "GET" é suportado.

    Por exemplo, aceder a um ponto final como *https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/health* ver o estado de saúde da eureka.

    Se a resposta for *401 Não Autorizada,* verifique se a função está atribuída com sucesso.  Levará vários minutos para que a função entre em vigor ou verifique se o sinal de acesso não expirou.

## <a name="next-steps"></a>Passos seguintes
* [Autenticado Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)
* [Pontos finais prontos para a produção](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)

## <a name="see-also"></a>Ver também
* [Criar funções e permissões](how-to-permissions.md)
