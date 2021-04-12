---
title: Ativar a segurança da camada de transporte de ponta a ponta
titleSuffix: Azure Spring Cloud
description: Como permitir a segurança da camada de transporte de ponta a ponta para uma aplicação.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/24/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 647cf6f0b1af6a5858bbf1147cc03ecc4637ed25
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227811"
---
# <a name="enable-end-to-end-tls-for-an-application"></a>Ativar tLS de ponta a ponta para uma aplicação

Este tópico mostra-lhe como permitir que sSL/TLS de ponta a ponta proteja o tráfego de um controlador de entrada para aplicações que suportem HTTPS. Depois de ativar o TLS de ponta a ponta e carregar um certificado do keyvault, todas as comunicações dentro da Azure Spring Cloud estão seguras com TLS.

   ![Gráfico de comunicações protegido por TLS.](media/enable-end-to-end-tls/secured-tls.png)

## <a name="prerequisites"></a>Pré-requisitos 

- Um exemplo de Azure Spring Cloud. Siga o nosso [quickstart na implantação através do CLI Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli) para começar.
- Se não estiver familiarizado com o TLS de ponta a ponta, consulte [a amostra de TLS de ponta a ponta.](https://github.com/Azure-Samples/spring-boot-secure-communications-using-end-to-end-tls-ssl)
- Para carregar de forma segura os certificados necessários em aplicações de Boot Spring, pode utilizar [o arranque de arranque da mola keyvault](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-certificates).


## <a name="enable-end-to-end-tls-on-an-existing-app"></a>Ativar o TLS de ponta a ponta numa aplicação existente

Utilize o comando `az spring-cloud app update --enable-end-to-end-tls` para ativar ou desativar o TLS de ponta a ponta para uma aplicação.

```azurecli
az spring-cloud app update --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
az spring-cloud app update --enable-end-to-end-tls false -n app_name -s service_name -g resource_group_name
```

## <a name="enable-end-to-end-tls-when-you-bind-custom-domain"></a>Ativar TLS de ponta a ponta quando ligar o domínio personalizado

Utilize o comando `az spring-cloud app custom-domain update --enable-end-to-end-tls` ou `az spring-cloud app custom-domain bind --enable-end-to-end-tls` para ativar ou desativar o TLS de ponta a ponta para uma aplicação.

```azurecli
az spring-cloud app custom-domain update --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
az spring-cloud app custom-domain bind --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
```

## <a name="verify-end-to-end-tls-status"></a>Verificar o estado do TLS de ponta a ponta

Utilize o comando `az spring-cloud app show` para verificar o valor de `enableEndToEndTls` .
```
az spring-cloud app show -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>Passos seguintes
* [Acesso Config Servidor e Registo de Serviço](how-to-access-data-plane-azure-ad-rbac.md)
