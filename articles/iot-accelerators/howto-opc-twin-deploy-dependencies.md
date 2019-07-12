---
title: Como implementar as dependências de cloud de OPC duplo no Azure | Documentos da Microsoft
description: Como implementar as dependências de OPC duplo Azure.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 95c2130727c0cef889771c181fec53557a2a4b0f
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603722"
---
# <a name="deploying-dependencies-for-local-development"></a>Implantação de dependências para o desenvolvimento local

Este artigo explica como implementar a necessidade de serviços da plataforma Azure para fazer o local de desenvolvimento e depuração.   No final, terá um grupo de recursos implementado que contém tudo o que precisa para desenvolvimento local e depuração.

## <a name="deploy-azure-platform-services"></a>Implementar os serviços de plataforma do Azure

1. Certifique-se de que tem o PowerShell e [do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-1.1.0) extensões instaladas.  Abra uma linha de comandos ou terminal e execute:

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. Siga as instruções para atribuir um nome para o grupo de recursos para a sua implementação.  O script implementa apenas as dependências para este grupo de recursos na sua subscrição do Azure, mas não os microsserviços.  O script também registra uma aplicação no Azure Active Directory.  Isto é necessário para suportar a autenticação com base em OAUTH.  Implementação pode demorar vários minutos.

3. Depois do script tiver concluído, pode selecionar para guardar o ficheiro. env.  O ficheiro de ambiente. env é o arquivo de configuração de todos os serviços e ferramentas que pretende executar no computador de desenvolvimento.  

## <a name="troubleshooting-deployment-failures"></a>Resolução de problemas de falhas de implementação

### <a name="resource-group-name"></a>Nome do grupo de recursos

Certifique-se de que use um nome de grupo de recursos de curto e simples.  O nome é utilizado, também, para recursos de nome como tal, que ele deve estar em conformidade com requisitos de nomes de recursos.  

### <a name="azure-active-directory-aad-registration"></a>Registo do Azure Active Directory (AAD)

O script de implementação tenta registar aplicações do AAD no Azure Active Directory.  Consoante os seus direitos para o inquilino do AAD selecionado, isso poderá falhar.   Existem 3 opções:

1. Se optou por um inquilino do AAD de uma lista de inquilinos, reinicie o script e escolher um diferente a partir da lista.
2. Em alternativa, implemente um inquilino do AAD privado, reinicie o script e selecione usá-lo.
3. Continue sem autenticação.  Uma vez que estiver a executar localmente os microsserviços, isso é aceitável, mas não simular ambientes de produção.  

## <a name="next-steps"></a>Passos Seguintes

Agora que implementou com êxito os serviços de duplo de OPC para um projeto existente, este é o passo seguinte sugerido:

> [!div class="nextstepaction"]
> [Saiba mais sobre como implementar módulos OPC duplo](howto-opc-twin-deploy-modules.md)
