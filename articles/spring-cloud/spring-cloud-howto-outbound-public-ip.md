---
title: Como identificar endereços IP públicos de saída em Azure Spring Cloud
description: Como visualizar os endereços IP públicos de saída estática para comunicar com recursos externos, tais como Base de Dados, Armazenamento, Cofre-Chave, etc.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: 04174b9cffb7e853dee235a4141ccda74a7847c6
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663775"
---
# <a name="how-to-identify-outbound-public-ip-addresses-in-azure-spring-cloud"></a>Como identificar endereços IP públicos de saída em Azure Spring Cloud

Esta página explica como visualizar endereços IP públicos de saída estática de aplicações Azure Spring Cloud.  Os IPs públicos são utilizados para comunicar com recursos externos, tais como bases de dados, armazenamento e cofres-chave.

## <a name="how-ip-addresses-work-in-azure-spring-cloud"></a>Como os endereços IP funcionam na Nuvem de primavera de Azure

Um serviço Azure Spring Cloud tem um ou mais endereços IP públicos de saída. O número de endereços IP públicos de saída pode variar de acordo com os níveis e outros fatores. 

Os endereços IP públicos de saída são geralmente constantes e permanecem os mesmos, mas existem exceções.

## <a name="when-outbound-ips-change"></a>Quando os IPs de saída mudam

Cada instância Azure Spring Cloud tem um número definido de endereços IP públicos de saída em qualquer momento. Qualquer ligação de saída das aplicações, como uma base de dados back-end, utiliza um dos endereços IP públicos de saída como o endereço IP de origem. O endereço IP é selecionado aleatoriamente no tempo de execução, pelo que o seu serviço back-end deve abrir a sua firewall para todos os endereços IP de saída.

O número de IPs públicos de saída muda quando executa uma das seguintes ações:

- Atualize a sua instância Azure Spring Cloud entre níveis.
- Crie um bilhete de apoio para mais IPs públicos de saída para necessidades empresariais.

## <a name="find-outbound-ips"></a>Encontre IPs de saída

Para encontrar os endereços IP públicos de saída atualmente utilizados pela sua instância de serviço no portal Azure, clique em **Rede** no painel de navegação à esquerda do seu caso. Estão listados no campo **de endereços IP de saída.**

Pode encontrar a mesma informação executando o seguinte comando na Cloud Shell

```Azure CLI
az spring-cloud show --resource-group <group_name> --name <service_name> --query properties.networkProfile.outboundIPs.publicIPs --output tsv
```

## <a name="next-steps"></a>Próximos passos
> [!div class="nextstepaction"]
* [Saiba mais sobre identidades geridas para recursos Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Saiba mais sobre o cofre chave em Azure Spring Cloud](spring-cloud-tutorial-managed-identities-key-vault.md)
