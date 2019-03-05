---
title: 2 notas de versão de atualização do serviço de aplicações no Azure Stack | Documentos da Microsoft
description: Saiba mais sobre as novidades na atualização dois para o serviço de aplicações no Azure Stack, os problemas conhecidos e onde pode transferir a atualização.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: anwestg
ms.reviewer: sethm
ms.lastreviewed: 05/18/2018
ms.openlocfilehash: 8126397dfb3b02778413ab6ee696a8138c792c15
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57342569"
---
# <a name="app-service-on-azure-stack-update-2-release-notes"></a>Serviço de aplicações no notas de versão de atualização 2 do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Estas notas de versão descrevem as melhorias e correções no serviço de aplicações do Azure no Azure Stack atualização 2 e os problemas conhecidos. Problemas conhecidos são divididos em problemas diretamente relacionados com a implementação, o processo de atualização e a problemas com a compilação (após a instalação).

> [!IMPORTANT]
> Aplicar a atualização de 1804 seu sistema integrado do Azure Stack ou implementar o development kit do Azure Stack mais recentes antes de implementar 1.2 de serviço de aplicações do Azure.
>
>

## <a name="build-reference"></a>Criar a referência

O serviço de aplicações no número de compilação de atualização 2 do Azure Stack é **72.0.13698.10**

### <a name="prerequisites"></a>Pré-requisitos

> [!IMPORTANT]
> Novas implementações do serviço de aplicações do Azure no Azure Stack agora exigem uma [certificado de caráter universal de assunto de três](azure-stack-app-service-before-you-get-started.md#get-certificates) devido a melhorias na forma em que o SSO para Kudu agora é processada no serviço de aplicações do Azure. É o novo assunto  **\*. sso.appservice.\< região\>.\< DomainName\>.\< extensão\>**
>
>

Consulte a [documentação antes de começar a utilizar](azure-stack-app-service-before-you-get-started.md) antes de iniciar a implementação.

### <a name="new-features-and-fixes"></a>Novas funcionalidades e correções

Serviço de aplicações do Azure no Azure Stack Update 2 inclui as seguintes melhorias e correções:

- Atualiza para **inquilino de serviço de aplicações, o administrador, portais de funções e ferramentas de Kudu**. Consistente com a versão do SDK do Portal do Azure Stack.

- Atualizações de serviço básico para melhorar a fiabilidade e ativar mais fácil diagnóstico dos problemas comuns de mensagens de erro.

- **Atualizações para as seguintes arquiteturas de aplicações e ferramentas**:
  - Foi adicionado .NET Framework 4.7.1
  - Adicionado **node. js** versões:
    - NodeJS 6.12.3
    - NodeJS 8.9.4
    - NodeJS 8.10.0
    - NodeJS 8.11.1
  - Adicionado **NPM** versões:
    - 5.6.0
  - Atualizado o .net Core componentes para ser consistente com o serviço de aplicações do Azure na nuvem pública.
  - Kudu atualizado

- Ranhuras de troca automática da implementação de funcionalidade ativada - [configurando a troca automática](https://docs.microsoft.com/azure/app-service/deploy-staging-slots#configure-auto-swap)

- Teste de funcionalidade de produção ativada - [introdução ao teste na produção](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)

- Habilitado - os Proxies de funções do Azure [trabalhar com os Proxies de funções do Azure](https://docs.microsoft.com/azure/azure-functions/functions-proxies)

- Extensão de administração do serviço de aplicações suporta a experiência do Usuário adicionado para:
  - Rotação secreta
  - Rotação de certificados
  - Rotação de credenciais do sistema
  - Rotação de cadeia de ligação

### <a name="known-issues-post-installation"></a>Problemas conhecidos (após a instalação)

- Os trabalhos são não é possível alcançar o servidor de ficheiros quando o serviço de aplicações é implementado numa rede virtual existente e o servidor de ficheiros só está disponível na rede privada.

Se optar por implementar numa rede virtual existente e um endereço IP interno para se ligar ao seu servidor de ficheiros, tem de adicionar uma regra de segurança de saída, permitindo que o tráfego entre a sub-rede de trabalho e o servidor de ficheiros SMB. Para fazer isso, vá para o WorkersNsg no Portal de administração e adicionar uma regra de segurança de saída com as seguintes propriedades:
* Origem: Qualquer
* Intervalo de portas de origem: *
* Destino: Endereços IP
* Intervalo de endereços IP de destino: Intervalo de IPs para o servidor de ficheiros
* Intervalo de portas de destino: 445
* Protocolo: TCP
* Ação: Permitir
* Prioridade: 700
* Nome: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problemas conhecidos para os administradores de nuvem operacional de serviço de aplicações do Azure no Azure Stack

Consulte a documentação no [notas de versão do Azure Stack 1804](azure-stack-update-1804.md)

## <a name="next-steps"></a>Passos Seguintes

- Para uma descrição geral do serviço de aplicações do Azure, consulte [serviço de aplicações do Azure no Descrição geral do Azure Stack](azure-stack-app-service-overview.md).
- Para obter mais informações sobre como preparar a implementação de serviço de aplicações no Azure Stack, veja [antes de começar com o serviço de aplicações no Azure Stack](azure-stack-app-service-before-you-get-started.md).
