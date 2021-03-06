---
title: Aceder a um plano de dados com Azure AD, RBAC
description: Como aceder ao plano de dados com o controlo de acesso baseado em funções do Azure Ative Directory.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 2909d40fbc7cb5b310ea17f17a6e683ce47638ce
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102220137"
---
# <a name="access-the-data-plane-with-azure-active-directory-and-role-based-access-control"></a>Aceda ao plano de dados com diretório ativo Azure e controlo de acesso baseado em funções

Este artigo explica como aceder ao servidor de configuração da Azure Spring Cloud e aos pontos finais do registo de serviços utilizando o controlo de acesso baseado em funções (RBAC) do Azure Spring Cloud.

## <a name="assign-role-to-aad-usergroup-msi-or-service-principal"></a>Atribuir função ao utilizador/grupo AAD, MSI ou principal de serviço

Para utilizar o AAD e o RBAC tem de atribuir a função *Azure Spring Cloud Data Reader* a um utilizador, grupo ou principal de serviço pelo seguinte procedimento:

1. Aceda à página geral do serviço da sua instância de serviço.

2. Clique em **Controlo de Acesso (IAM)** para abrir a lâmina de controlo de acesso.

3. Clique no botão **Adicionar** e **adicione atribuições de funções** (a autorização pode ser necessária para adicionar).

4. Encontre e selecione O leitor de dados da nuvem de mola de *Azure* sob **função**.
5. Atribua acesso a `User, group, or service principal` ou de acordo com o tipo de `User assigned managed identity` utilizador. Procure e selecione o utilizador.  
6. Clique `Save`

   ![atribuir função](media/access-data-plane-aad-rbac/assign-data-reader-role.png)

## <a name="access-data-plane"></a>Plano de dados de acesso

Depois de um utilizador AAD ser atribuído à função *Azure Spring Cloud Data Reader,* os clientes podem iniciar sessão no Azure CLI com o utilizador, o principal serviço ou a identidade gerida.  Consulte [Authenticate Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli) para obter um token de acesso.  Em seguida, use os seguintes comandos.

```azurecli
az login
az account get-access-token
```

Atualmente, o CLI suporta pontos finais predefinidos do servidor config e do registo de serviço. Para mais informações, consulte [os pontos finais prontos para a Produção.](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints) 

Os clientes também podem obter uma lista completa de pontos finais suportados do servidor config e registo de serviço, acedendo a pontos finais:
* *https://SERVICE_NAME.Root_Endpoint/eureka/actuator/*
* *https://SERVICE_NAME.Root_Endpoint/config/actuator/* 

O sinal de acesso no cabeçalho fornece autorização. Apenas o método "GET" é suportado.

Por exemplo, aceder a um ponto final como *https://SERVICE_NAME.Root_Endpoint/eureka/actuator/health* ver o estado de saúde da eureka.

Vários pontos finais de raiz são mostrados abaixo de acordo com diferentes tipos de nuvem.

| Cloud          | Ponto final de raiz              |
| -------------- | -------------------------- |
| Público         | svc.azuremicroservices.io  |
| Bolo-luar/China | svc.microservices.azure.cn |

Se a resposta for *401 Não Autorizada,* verifique se a função está atribuída com sucesso.  Levará vários minutos para que a função entre em vigor ou verifique se o sinal de acesso não expirou.

## <a name="next-steps"></a>Passos seguintes
* [Autenticado Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)
* [Pontos finais prontos para a produção](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)

## <a name="see-also"></a>Ver também
* [Criar funções e permissões](spring-cloud-howto-permissions.md)