---
title: Como solucionar problemas Azure Functions Runtime está inacessível.
description: Saiba como solucionar problemas de uma conta de armazenamento inválida.
services: functions
documentationcenter: ''
author: alexkarcher-msft
manager: cfowler
editor: ''
ms.service: azure-functions
ms.workload: na
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: d5959acc7719e2b02d529bca8261bc09d5b93634
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085333"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>Como solucionar problemas "o tempo de execução do Functions está inacessível"


## <a name="error-text"></a>Texto do erro
Este documento destina-se a solucionar o erro a seguir quando exibido no portal do functions.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

### <a name="summary"></a>Resumo
Esse problema ocorre quando o Azure Functions Runtime não pode iniciar. O motivo mais comum para esse erro ocorrer é o aplicativo de função que está perdendo o acesso à sua conta de armazenamento. [Leia mais sobre os requisitos da conta de armazenamento aqui](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)

### <a name="troubleshooting"></a>Resolução de problemas
Vamos examinar os quatro casos de erro mais comuns, como identificar e como resolver cada caso.

1. Conta de armazenamento excluída
1. Configurações do aplicativo da conta de armazenamento excluídas
1. Credenciais da conta de armazenamento inválidas
1. Conta de armazenamento inacessível
1. Cota de execução diária cheia

## <a name="storage-account-deleted"></a>Conta de armazenamento excluída

Cada aplicativo de funções requer uma conta de armazenamento para operar. Se essa conta for excluída, sua função não funcionará.

### <a name="how-to-find-your-storage-account"></a>Como localizar sua conta de armazenamento

Comece pesquisando o nome da conta de armazenamento nas configurações do aplicativo. `AzureWebJobsStorage` Ou`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` conterá o nome da sua conta de armazenamento empacotado em uma cadeia de conexão. Leia mais especificações na referência de [configuração do aplicativo aqui](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)

Pesquise sua conta de armazenamento na portal do Azure para ver se ela ainda existe. Se ele tiver sido excluído, será necessário recriar uma conta de armazenamento e substituir as cadeias de conexão de armazenamento. Seu código de função é perdido e você precisará reimplantá-lo novamente.

## <a name="storage-account-application-settings-deleted"></a>Configurações do aplicativo da conta de armazenamento excluídas

Na etapa anterior, se você não tiver uma cadeia de conexão da conta de armazenamento, elas provavelmente foram excluídas ou substituídas. A exclusão das configurações do aplicativo é mais comumente feita ao usar slots de implantação ou Azure Resource Manager scripts para definir as configurações do aplicativo.

### <a name="required-application-settings"></a>Configurações de aplicativo necessárias

* Requerido
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Necessário para funções de plano de consumo
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

[Leia sobre essas configurações de aplicativo aqui](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

### <a name="guidance"></a>Orientação

* Não marque "configuração de slot" para nenhuma dessas configurações. Quando você permuta os slots de implantação, a função será interrompida.
* Não modifique essas configurações como parte das implantações automatizadas.
* Essas configurações devem ser fornecidas e válidas no momento da criação. Uma implantação automatizada que não contém essas configurações resultará em um aplicativo não funcional, mesmo que as configurações sejam adicionadas após o fato.

## <a name="storage-account-credentials-invalid"></a>Credenciais da conta de armazenamento inválidas

As cadeias de conexão da conta de armazenamento acima devem ser atualizadas se você regenerar as chaves de armazenamento. [Leia mais sobre o gerenciamento de chaves de armazenamento aqui](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)

## <a name="storage-account-inaccessible"></a>Conta de armazenamento inacessível

Seu Aplicativo de funções deve ser capaz de acessar a conta de armazenamento. Os problemas comuns que bloqueiam o acesso de funções a uma conta de armazenamento são:

* Aplicativos de funções implantados em ambientes de serviço de aplicativo sem as regras de rede corretas para permitir o tráfego de e para a conta de armazenamento
* O firewall da conta de armazenamento está habilitado e não está configurado para permitir o tráfego de e para funções. [Leia mais sobre a configuração de firewall da conta de armazenamento aqui](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="daily-execution-quota-full"></a>Cota de execução diária cheia

Se você tiver uma cota de execução diária configurada, seu Aplicativo de funções será desabilitado temporariamente e muitos dos controles do portal ficarão indisponíveis. 

* Para verificar, verifique os recursos de plataforma aberta > Aplicativo de funções configurações no Portal. Você verá a seguinte mensagem se você estiver acima da cota
    * `The Function App has reached daily usage quota and has been stopped until the next 24 hours time frame.`
* Remova a cota e reinicie o aplicativo para resolver o problema.

## <a name="next-steps"></a>Próximos Passos

Agora que seu Aplicativo de funções está de volta e operacional, dê uma olhada em nossos guias de início rápido e referências do desenvolvedor para entrar em funcionamento novamente!

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
