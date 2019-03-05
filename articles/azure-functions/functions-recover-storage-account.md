---
title: Como resolver problemas de Runtime de funções do Azure está inacessível.
description: Saiba como resolver problemas de uma conta de armazenamento inválido.
services: functions
documentationcenter: ''
author: alexkarcher-msft
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: bddfccaf0749bbb5eaf206e5961c713efa5eea17
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57311904"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>Como resolver problemas de "o runtime das funções não está acessível"


## <a name="error-text"></a>Texto do erro
Este documento destina-se para resolver o erro seguinte, quando apresentado no portal de funções.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

### <a name="summary"></a>Resumo
Este problema ocorre quando não é possível iniciar o Runtime das funções do Azure. A razão mais comum para este erro ocorrer é a aplicação de função perder o acesso à sua conta de armazenamento. [Leia mais sobre os requisitos de conta de armazenamento aqui](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)

### <a name="troubleshooting"></a>Resolução de problemas
Vamos examinar os quatro casos de erro mais comuns, como identificar e como resolver cada caso.

1. Conta de armazenamento eliminada
1. Definições de aplicação de conta de armazenamento eliminadas
1. Credenciais da conta de armazenamento inválidas
1. Conta de armazenamento inacessível
1. Quota de execução diária completa

## <a name="storage-account-deleted"></a>Conta de armazenamento eliminada

Cada aplicação de funções requer uma conta de armazenamento para operar. Se essa conta for eliminada sua função não irá funcionar.

### <a name="how-to-find-your-storage-account"></a>Como encontrar a sua conta de armazenamento

Comece por procurar o nome da conta de armazenamento nas definições da aplicação. Seja `AzureWebJobsStorage` ou `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` irá conter o nome da sua conta de armazenamento colocada dentro de uma cadeia de ligação. Leia mais informações no [aqui referência de definição de aplicação](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)

Pesquisa para a sua conta de armazenamento no portal do Azure para ver se ela ainda existe. Se tiver sido eliminada, terá de recriar uma conta de armazenamento e substituir as cadeias de ligação de armazenamento. O código de função é perdido e terá do implementar novamente novamente.

## <a name="storage-account-application-settings-deleted"></a>Definições de aplicação de conta de armazenamento eliminadas

No passo anterior, se não tinha uma cadeia de ligação de conta de armazenamento eles eram provavelmente eliminado ou substituído. Eliminar as definições de aplicação é feito normalmente quando utilizar ranhuras de implementação ou de scripts do Azure Resource Manager para definir as configurações do aplicativo.

### <a name="required-application-settings"></a>Definições da aplicação necessária

* Necessário
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Necessário para as funções do plano de consumo
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#websitecontentazurefileconnectionstring)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#websitecontentshare)

[Saiba mais sobre estas definições de aplicação aqui](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

### <a name="guidance"></a>Orientação

* Não verifica "definição de ranhura" para qualquer uma destas definições. Quando trocar as ranhuras de implementação a função será interrompida.
* Não modifique estas definições como parte de Implantações automatizadas.
* Estas definições tem de ser fornecido e válido no momento da criação. Uma implementação automatizada que não contenha estas definições resultará num aplicativo não funcional, mesmo que as definições são adicionadas após o fato.

## <a name="storage-account-credentials-invalid"></a>Credenciais da conta de armazenamento inválidas

As cadeias de ligação de conta de armazenamento acima têm de ser atualizadas se voltar a gerar chaves de armazenamento. [Leia mais sobre armazenamento gestão de chaves aqui](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)

## <a name="storage-account-inaccessible"></a>Conta de armazenamento inacessível

A aplicação de função tem de ser capaz de aceder à conta de armazenamento. Problemas comuns que um acesso de funções a uma conta de armazenamento de blocos são:

* Aplicações de funções implementadas para ambientes de serviço de aplicações sem as regras de rede correto para permitir o tráfego de e para a conta de armazenamento
* A firewall de conta de armazenamento é ativada e não configurada para permitir o tráfego de e para as funções. [Saiba mais sobre a configuração de firewall de conta de armazenamento aqui](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="daily-execution-quota-full"></a>Quota de execução diária completa

Se tiver uma Quota de execução diária configurado, a aplicação de função será desativada temporariamente e muitos dos controles portais deixará de estar disponíveis. 

* Para verificar, verificação de abrir a recursos da plataforma > definições da aplicação de funções no portal. Verá a seguinte mensagem se excedeu a quota
    * `The Function App has reached daily usage quota and has been stopped until the next 24 hours time frame.`
* Remover a quota e reinicie a aplicação para resolver o problema.

## <a name="next-steps"></a>Próximos Passos

Agora que a aplicação de função está operacional e volte a examinar as referências de inícios rápidos e desenvolvedor para começar a trabalhar e executar novamente!

* [Criar a sua primeira Função do Azure](functions-create-first-azure-function.md)  
  Comece de imediato e crie a sua primeira função com o guia de introdução das Funções do Azure. 
* [Referência para programadores das Funções do Azure](functions-reference.md)  
  Fornece mais informações técnicas sobre o tempo de execução das Funções do Azure e uma referência para as funções de codificação e definição de acionadores e enlaces.
* [Testar as Funções do Azure](functions-test-a-function.md)  
  Descreve várias ferramentas e técnicas para testar as suas funções.
* [Como dimensionar as Funções do Azure](functions-scale.md)  
  Aborda os planos de serviço disponíveis com as Funções do Azure, incluindo o plano de alojamento de Consumo e como escolher o plano adequado. 
* [Saiba mais sobre o Azure App Service](../app-service/overview.md)  
  Funções do Azure melhora o Serviço de Aplicações do Azure para funcionalidades essenciais como implementações, variáveis de ambiente e diagnósticos. 
