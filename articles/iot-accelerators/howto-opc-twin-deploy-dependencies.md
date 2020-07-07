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
ms.openlocfilehash: 231d1efa02ec80e8ad56a8895d4262d774480111
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "73824104"
---
# <a name="deploying-dependencies-for-local-development"></a>Implantação de dependências para o desenvolvimento local

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

2. Siga as instruções para atribuir um nome ao grupo de recursos para a sua implantação.  O script implementa apenas as dependências deste grupo de recursos na sua subscrição Azure, mas não os micro serviços.  O script também regista uma Aplicação no Diretório Ativo Azure.  Isto é necessário para apoiar a autenticação baseada em AAUTH.  A implantação pode demorar vários minutos.

3. Uma vez que o script esteja concluído, pode selecionar para guardar o ficheiro .env.  O ficheiro ambiente .env é o ficheiro de configuração de todos os serviços e ferramentas que pretende executar na sua máquina de desenvolvimento.  

## <a name="troubleshooting-deployment-failures"></a>Falhas de implantação de resolução de problemas

### <a name="resource-group-name"></a>Nome do grupo de recursos

Certifique-se de que utiliza um nome de grupo de recursos curto e simples.  O nome é também utilizado para nomear recursos como tal, deve cumprir os requisitos de nomeação de recursos.  

### <a name="azure-active-directory-aad-registration"></a>Registo do Azure Ative Directory (AAD)

O script de implementação tenta registar aplicações AAD no Azure Ative Directory.  Dependendo dos seus direitos ao inquilino selecionado da AAD, isto pode falhar.   Existem três opções:

1. Se escolheu um inquilino da AAD de uma lista de inquilinos, reinicie o guião e escolha um diferente da lista.
2. Em alternativa, desloque um inquilino privado da AAD, reinicie o script e selecione para usá-lo.
3. Continue sem autenticação.  Uma vez que está a gerir os seus micro serviços localmente, isso é aceitável, mas não imita ambientes de produção.  

## <a name="next-steps"></a>Próximos passos

Agora que implementou com sucesso os serviços da OPC Twin para um projeto existente, eis o próximo passo sugerido:

> [!div class="nextstepaction"]
> [Saiba como implementar módulos OPC Twin](howto-opc-twin-deploy-modules.md)
