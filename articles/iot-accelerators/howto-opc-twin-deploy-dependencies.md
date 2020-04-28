---
title: Como implantar dependências de nuvens gémeas oPC em Azure Microsoft Docs
description: Este artigo descreve como implantar as dependências oPC Twin Azure necessárias para fazer desenvolvimento local e depuração.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 231d1efa02ec80e8ad56a8895d4262d774480111
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73824104"
---
# <a name="deploying-dependencies-for-local-development"></a>Implantação de dependências para o desenvolvimento local

Este artigo explica como implementar apenas os Serviços da Plataforma Azure necessários para fazer o desenvolvimento local e a depuração.   No final, terá um grupo de recursos implantado que contém tudo o que precisa para o desenvolvimento local e depuração.

## <a name="deploy-azure-platform-services"></a>Implementar serviços da plataforma Azure

1. Certifique-se de que tem extensões PowerShell e [AzureRM PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) instaladas.  Abra um pedido de comando ou terminal e corra:

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. Siga as instruções para atribuir um nome ao grupo de recursos para a sua implantação.  O script implanta apenas as dependências deste grupo de recursos na sua subscrição Azure, mas não os micro serviços.  O script também regista uma Aplicação no Diretório Ativo Azure.  Isto é necessário para apoiar a autenticação baseada na OAUTH.  O destacamento pode demorar vários minutos.

3. Uma vez que o script esteja concluído, pode selecionar para guardar o ficheiro .env.  O ficheiro ambiente .env é o ficheiro de configuração de todos os serviços e ferramentas que pretende executar na sua máquina de desenvolvimento.  

## <a name="troubleshooting-deployment-failures"></a>Falhas de implementação de problemas

### <a name="resource-group-name"></a>Nome do grupo de recursos

Certifique-se de que utiliza um nome de grupo de recursos curto e simples.  O nome também é usado para nomear recursos como tal deve cumprir os requisitos de nomeação de recursos.  

### <a name="azure-active-directory-aad-registration"></a>Registo de Diretório Ativo Azure (AAD)

O script de implementação tenta registar aplicações AAD no Diretório Ativo Azure.  Dependendo dos seus direitos para com o inquilino selecionado da AAD, isso pode falhar.   Existem três opções:

1. Se escolheu um inquilino da AAD de uma lista de inquilinos, reinicie o guião e escolha um diferente da lista.
2. Em alternativa, desloque um inquilino aAD privado, reinicie o script e selecione para usá-lo.
3. Continue sem Autenticação.  Uma vez que está a gerir os seus micro serviços localmente, isso é aceitável, mas não imita ambientes de produção.  

## <a name="next-steps"></a>Passos seguintes

Agora que implementou com sucesso os serviços da OPC Twin para um projeto existente, eis o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Saiba como implementar módulos OPC Twin](howto-opc-twin-deploy-modules.md)
