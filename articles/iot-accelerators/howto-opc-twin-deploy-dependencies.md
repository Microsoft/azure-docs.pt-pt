---
title: Como implantar as dependências de nuvem gémea da OPC em Azure Microsoft Docs
description: Este artigo descreve como implementar as dependências do OPC Twin Azure necessárias para fazer o desenvolvimento local e depuração.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: ae0e18a2dfbce52e50b4a8b58d478a46dff12922
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91282107"
---
# <a name="deploying-dependencies-for-local-development"></a>Implantação de dependências para o desenvolvimento local

> [!IMPORTANT]
> Enquanto atualizamos este artigo, consulte [a Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

Este artigo explica como implementar apenas os Serviços da Plataforma Azure necessários para fazer o desenvolvimento local e depuração.   No final, terá um grupo de recursos implantado que contém tudo o que precisa para o desenvolvimento local e depuração.

## <a name="deploy-azure-platform-services"></a>Implementar serviços da plataforma Azure

1. Certifique-se de que tem extensões [PowerShell e AzureRM](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) instaladas.  Abra uma solicitação de comando ou terminal e corra:

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. Siga as instruções para atribuir um nome ao grupo de recursos para a sua implantação.  O script implementa apenas as dependências deste grupo de recursos na sua subscrição Azure, mas não os micro serviços.  O script também regista uma aplicação em Azure AD.  Isto é necessário para apoiar a autenticação baseada em AAUTH.  A implantação pode demorar vários minutos.

3. Uma vez que o script esteja concluído, pode selecionar para guardar o ficheiro .env.  O ficheiro ambiente .env é o ficheiro de configuração de todos os serviços e ferramentas que pretende executar na sua máquina de desenvolvimento.  

## <a name="troubleshooting-deployment-failures"></a>Falhas de implantação de resolução de problemas

### <a name="resource-group-name"></a>Nome do grupo de recursos

Certifique-se de que utiliza um nome de grupo de recursos curto e simples.  O nome é também utilizado para nomear recursos como tal, deve cumprir os requisitos de nomeação de recursos.  

### <a name="azure-active-directory-ad-registration"></a>Registo do Diretório Ativo Azure (AD)

O script de implementação tenta registar aplicações AZure AD em Azure AD.  Dependendo dos seus direitos ao inquilino Azure AD selecionado, isso pode falhar. Existem três opções:

1. Se escolheu um inquilino AZure AD de uma lista de inquilinos, reinicie o roteiro e escolha um diferente da lista.
2. Em alternativa, desloque um inquilino Azure AD privado, reinicie o script e selecione para usá-lo.
3. Continue sem autenticação.  Uma vez que está a gerir os seus micro serviços localmente, isso é aceitável, mas não imita ambientes de produção.  

## <a name="next-steps"></a>Passos seguintes

Agora que implementou com sucesso os serviços da OPC Twin para um projeto existente, eis o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Saiba como implementar módulos OPC Twin](howto-opc-twin-deploy-modules.md)
